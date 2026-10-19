---
date: 2026-10-19
authors: [alvsanand]
categories: [OSS]
---
# Sympozium: When One Agent Isn't Enough, and Prompts Aren't the Problem

Give one language model a prompt and a couple of tools and it will do something useful. Split the same job across four agents — one researches, one drafts, one reviews, one files the result — and the failures stop looking like bad prompting. The researcher's findings never reach the reviewer. Two agents spend the same budget on the same search. Nobody can say afterwards which agent did what. Sympozium treats those as coordination failures, and fixes them the way platform teams already fix coordination failures: with Kubernetes.

It is MIT licensed, under active development (its README warns plainly that APIs will change and things will break), and comes from the creator of k8sgpt and llmfit. Its one-line thesis is also its design brief: agents don't need better prompts, they need shared situational awareness.

![Sympozium: When One Agent Isn't Enough, and Prompts Aren't the Problem - svg wide](/img/sympozium-when-one-agent-isn-t-enough-and-svg_wide.svg?v=f0f39a9){ width="80%" }

## Why agents end up on Kubernetes

An AI agent, stripped of the marketing, is a language model in a loop with tools it can call. That loop makes it a long-running process with the same needs as any other service. It needs credentials to reach a model, a filesystem, a network boundary, a way to be stopped, and a log of what it did. It needs restarting when it fails halfway through a job.

Kubernetes is the container orchestrator most platform teams already run. It gives you one API for declaring what should be running, plus controllers — small programs that watch that API and make reality match it. Sympozium plugs into that machinery with custom resource definitions, or CRDs: a way to add your own object types to the Kubernetes API so ordinary tooling understands them. In Sympozium an Agent, an Ensemble and a policy are all objects. An agent's permissions can be reviewed in a pull request like any other infrastructure change, instead of living in someone's terminal history.

## Openclaw, and where a personal agent stops

The brief for this article asks where Openclaw stops being enough. Nothing in the material I have describes Openclaw, so I can't compare the two projects, and I won't pretend otherwise. What I can do is name the requirements an organisation tends to discover after the demo works.

Sympozium's README assumes that list directly: every agent owning its own model key, tool access gated by policy on every single run, agents unable to read Kubernetes Secrets unless a policy says they may, network egress rules, an allowlist of container image registries, and a token-and-cost figure attached to each run. Those are the things a single shared API key and a chat window cannot give you, and they are what separates something a security team signs off on from something running on someone's laptop.

## Why Sympozium exists

The project's own framing is short: containers needed orchestration, agents need coordination. Sympozium is a coordination layer for multi-agent systems. It decides what agents do — who delegates to whom, what each one can see, what they remember together.

It deliberately does not decide where compute happens. That belongs to a separate Kubernetes DRA (dynamic resource allocation) driver, llmfit-dra, which places models through the stock scheduler. Nor does it decide how tokens move; that is the serving engine's job, whether vLLM, SGLang or llama.cpp. The boundary is concrete: when an agent needs a model, Sympozium claims one the way an application claims a PersistentVolume — it asks for a capability and never picks the host.

## The features that carry the weight

**Synthetic Membrane** is selective permeability for agent teams. Trust groups, visibility tags and field-level gating control what one agent can see of another's context. That is what turns "shared awareness" from a slogan into a rule you can enforce.

**Ensembles, workflows and shared memory.** An Ensemble is a Helm-like bundle for an agent team: activate the pack and the controller stamps out the Agents, Schedules and memory it declares. Workflows cover delegation, sequential pipelines, supervision and stimulus triggers between personas, drawn on an interactive canvas. Shared Workflow Memory is a pack-level SQLite pool with per-persona access control and time decay, so knowledge is shared, but not uniformly and not forever.

**One key per agent, and mediated model access.** Each Agent uses its own model key, shared only with its sub-agents. The key's owner is recorded on its Secret and checked by the controller, the admission webhook and, on every call, the model gateway. By default that gateway holds the provider keys, so hardware-isolated agents never see one; built-in routes cover OpenAI, Anthropic and DeepSeek, and approved local servers work keyless.

**Policy as an object.** A `SympoziumPolicy` CRD carries tool gating (allow and deny, enforced on every run), sandbox requirements, egress rules, registry allowlists, and whether skills may touch Secrets. A skill runs in its own sidecar — a helper container alongside the agent — with ephemeral least-privilege RBAC, the Kubernetes permission system, cleaned up when the skill finishes.

**Runs you can account for.** OpenTelemetry traces and metrics, Prometheus endpoints, a trace ID per run, and token usage with estimated cost on every AgentRun. Channels are their own Deployments backed by NATS JetStream, a messaging system, which is how Telegram, Slack, Discord and WhatsApp traffic reaches an agent without the agent owning the connection.

## How the pieces fit together

Installing the control plane gives you the CRDs, the controllers, built-in Ensembles, the Celln plane and ergoz. Where a run lands depends on the node. On nodes that expose `/dev/kvm` and keep a kernel image under `/boot`, a probe labels the node `celln.dev/kvm=true`, and the Celln fleet runs each agent in a microVM — a small virtual machine, hardware-isolated from its neighbours, booted by a virtual machine monitor from that kernel file.

One trap before you try it: Kind is a development environment only here. On a Linux host its nodes see `/dev/kvm` but ship without a kernel image, so a plain install waits for a node that never qualifies. Kind on macOS or Windows cannot run the fleet at all. Models are declared as CRDs too: GGUF or HuggingFace weights are downloaded, an inference server such as llama.cpp, vLLM or TGI is deployed, and an OpenAI-compatible endpoint appears for your personas.

## What to read next

The next article takes a single AgentRun end to end — what the controller creates, where the policy is actually checked, and what the microVM sees. If you want to follow along, the first command on macOS or Linux is `brew tap sympozium-ai/sympozium` then `brew install sympozium`, followed by `sympozium install` against a cluster.

Source: https://raw.githubusercontent.com/sympozium-ai/sympozium/main/README.md
