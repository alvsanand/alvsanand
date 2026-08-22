---
date: 2026-08-22
authors: [alvsanand]
categories:
  - General
  - Datahub.local
  - Learning
---

# Understanding the AI Agent Harness

Large Language Models (LLMs) like **Claude 5 Opus**, or **GPT-5.6**, or **DeepSeek V4** are exceptional at reasoning, but they are fundamentally stateless text generators. They cannot directly view your repository, modify files on disk, or run your unit tests. An LLM acts as a central processor without operating system access: it generates code, but it lacks the execution layer needed to verify or deploy that code.

To transform an LLM into a fully functional coding assistant like **OpenCode**, you need an **Agent Harness**.

The harness is the dedicated runtime environment wrapping around the model. While the model handles high-level reasoning and decision-making, the harness acts as the scaffolding—managing execution loops, parsing structured tool requests, validating terminal outputs, preserving context across steps, and enforcing safety guardrails.

---

## The Execution Engine: Inside the OpenCode Loop

When you issue a command like *"Refactor `auth.ts` to use JWT tokens and run `npm test`,"* the OpenCode harness takes over. It manages the task through a recursive, multi-step execution lifecycle designed to achieve verifiable results.

![OpenCode Loop](/img/ai_agent_harness.jpg){ width="80%" }

**Step 1: Context Packaging & Workspace Normalization**

Because LLMs retain zero state between API calls, the OpenCode harness constructs the model's memory on every cycle. Before making a request, it collects:

* **Workspace Tree:** The repository layout obtained via tools like `ls` or AST parsers.
* **Active Artifacts:** The target code files (`src/auth.ts`, `tests/auth.test.ts`).
* **Durable History:** Compressed logs of previous turns, preventing token context bloat while keeping recent actions fresh.

**Step 2: LLM Reasoning & Tool Contract Emission**

The model receives the context bundle, analyzes the refactoring goal, and plans its approach. Crucially, the model does not edit files directly. Instead, it emits a strict, machine-readable tool call in JSON format (e.g., calling `read_file` or `patch_file`).

**Step 3: Interception, Safety Checks, and Tool Dispatch**

The OpenCode harness captures the model's tool call request and validates it against system policies before execution:

* **Safety Interception:** The harness checks for dangerous shell commands (such as unapproved file deletions or remote branch pushes) and blocks them or prompts for user confirmation.
* **Local Execution:** Once validated, the harness executes the action locally using system interfaces—editing code diffs, reading directory contents, or running `npm test` in a sandboxed shell environment.

**Step 4: Observation Capture and Memory Logging**

After the tool runs, the harness captures the exact output (`stdout` and `stderr`), test outputs, or exit codes. It writes these results to a persistent `execution_stream.log` on disk and appends them as a fresh "Observation" to the context window for the next turn.

---

## How It's Coded: Inside OpenCode's Architecture

OpenCode implements its harness through a hybrid runtime architecture: a high-performance **Go-based CLI/TUI engine** paired with a **Node.js/TypeScript SDK (`@opencode-ai/sdk`)**, backed by local **SQLite** databases for durable session state.

### 1. Markdown-Driven Agent Declarations

OpenCode uses declarative Markdown files with YAML frontmatter to define agent personas, assigned LLM providers, permissions, and tool access:

```markdown
---
name: opencoder
description: Specialized developer agent for multi-file refactoring
mode: subagent
model: anthropic/claude-3-7-sonnet
permissions:
  bash:
    mode: ask
    deny: ["rm -rf *", "git push --force"]
  edit:
    deny: [".env", "node_modules/**", ".git/**"]
tools: [read, edit, write, patch, grep, glob, bash, diagnostics]
---

You are an expert developer. Always create a step-by-step plan before writing code.
Run tests after applying patches to verify correctness.

```

### 2. Safety Interceptors & Hooks (`@opencode-ai/plugin`)

Custom guardrails and workspace rules are implemented via OpenCode's TypeScript plugin SDK. The harness emits lifecycle events like `tool.execute.before` to validate or block execution before it reaches the operating system:

```typescript
import { createPlugin } from '@opencode-ai/plugin';

export default createPlugin({
  name: 'security-guardrails',
  hooks: {
    // Intercept tool calls before execution
    'tool.execute.before': async ({ tool, args, session }) => {
      if (tool === 'bash') {
        const cmd = args.command as string;

        // Block dangerous commands programmatically
        if (cmd.includes('rm -rf') || cmd.includes('DROP DATABASE')) {
          throw new Error('Harness Error: Command violates security policy.');
        }
      }
    },

    // Inject active LSP diagnostics after code edits
    'tool.execute.after': async ({ tool, result, session }) => {
      if (tool === 'patch' || tool === 'write') {
        const diagnostics = await session.tools.execute('diagnostics', {});
        return { ...result, lsp_status: diagnostics };
      }
    }
  }
});

```

### 3. Context Compaction & Execution Engine Loop

OpenCode's core loop monitors memory usage dynamically. When conversation context reaches 95% of the model's token limit, an auto-compaction routine summarizes historical turns while preserving task goals to prevent out-of-memory errors:

```typescript
import { createOpenCodeClient } from '@opencode-ai/sdk';

async function runAgentLoop(session, initialPrompt) {
  let prompt = initialPrompt;

  while (session.isActive) {
    // 1. Context Check & Auto-Compaction
    if (session.getTokenUsageRatio() >= 0.95) {
      const summary = await session.summarizeCurrentState();
      session.compactAndResetHistory(summary); // Retains goals, drops raw logs
    }

    // 2. Query LLM Provider (Anthropic, OpenAI, DeepSeek, etc.)
    const response = await session.llm.generate({
      prompt: prompt,
      tools: session.getRegisteredTools(), // Local tools + MCP tools
    });

    // 3. Exit loop if LLM returns final text response
    if (!response.hasToolCalls) {
      return response.text;
    }

    // 4. Dispatch Tool Calls & Intercept via Hooks
    for (const toolCall of response.toolCalls) {
      try {
        await session.triggerHook('tool.execute.before', toolCall);
        
        // Execute command locally or in sandbox
        const result = await session.executeTool(toolCall.name, toolCall.args);
        
        // Feed observation back into history
        session.appendObservation(toolCall.id, result);
      } catch (error) {
        session.appendObservation(toolCall.id, `Error: ${error.message}`);
      }
    }
  }
}

```

---

## Deep Dive: Key Subsystems of OpenCode's Harness

To maintain stability across complex, multi-turn coding sessions, OpenCode relies on several specialized internal harness subsystems beyond the basic loop:

* **Model Context Protocol (MCP) Router:** Automatically discovers external integrations over `stdio` or `SSE` transports, exposing third-party databases, cloud APIs, and specialized tools directly to the agent.
* **LSP Integration Engine:** Connects directly with local Language Servers to feed compiler errors, type warnings, and syntax diagnostics back to the LLM immediately following code modifications.
* **AST-Guided File Patching:** Performs line-level code edits using Abstract Syntax Tree validation, ensuring generated patches pass syntax checks before being committed to disk.

---

## Harness vs. Model vs. UI: A Clear Separation

Understanding modern AI architecture requires distinguishing between these three layers:

| Component                | Responsibility                                                                                              | Examples in OpenCode                                                      |
| ------------------------ | ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------- |
| **LLM (The Brain)**      | Language processing, logic, planning, and tool request generation.                                          | Claude 3.7 Sonnet, Claude 3.5 Opus, DeepSeek V3, GPT-4o                   |
| **Harness (The Engine)** | Execution loop control, context packaging, file system I/O, tool execution, safety checks, log persistence. | OpenCode Core Runtime (`@opencode-ai/sdk`, Plugins, SQLite Session Store) |
| **UI (The Interface)**   | Rendering diffs, displaying terminal outputs, receiving user prompts.                                       | VS Code Extension, Go-based Terminal TUI/CLI                              |

---

By surrounding the language model with an **Agent Harness**, frameworks like OpenCode transform raw LLM intelligence into an enterprise-grade developer tool—combining reasoning flexibility with strict, sandboxed, and verified code execution.