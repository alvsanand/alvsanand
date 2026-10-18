---
date: 2026-10-18
authors: [alvsanand]
categories: [OSS]
---
# Sympozium: when one AI agent isn't enough

Two AI agents work on one task. The first writes down what it found; the second needs that finding to finish the job. Now a question appears that no prompt can settle: is the second agent allowed to read what the first one wrote? And what happens if the first agent dies halfway through and never finishes its half?

An agent here means a language model given instructions and tools, left to work through steps on its own. One agent with a careful prompt can do a lot. Two agents bring delivery problems, permission problems, memory problems and state problems — the same problems infrastructure people have been solving for other software for a decade.

Sympozium is a coordination layer for teams of AI agents, and it runs on Kubernetes, the system most cloud infrastructure already uses to run software. The project's own summary is blunt: agents don't need better prompts, they need shared situational awareness.

![Sympozium: when one AI agent isn't enough - svg wide](/docs/img/sympozium-when-one-ai-agent-isn-t-enough-svg_wide.svg){ width="80%" }

## Why anyone puts AI agents on Kubernetes

You tell Kubernetes what you want — five copies of this program, always running — and a controller keeps it true. A controller is a loop that compares what you asked for with what actually exists, and closes the gap. A container is one packaged program with its dependencies. A pod is the smallest thing Kubernetes schedules, usually one container plus helpers.

Agents fit that model better than they fit a chat window. They run for minutes or hours. They call tools. They need storage that survives a restart, an identity, a way to be stopped and audited. Kubernetes already does all of that, and most companies already run it.

What Kubernetes has no concept of is an agent, its team, its memory or its permissions. Sympozium adds those. Every policy is a CRD — a custom resource definition, which is how you teach Kubernetes a new kind of object, so your own configuration is stored, versioned and reviewed like any standard object. A team's rules become a file in git instead of a paragraph buried in a prompt.

## Openclaw runs on your laptop. A company can't.

You may have met Openclaw, or one of the personal agent tools around it: you install it, point it at your own machine and your own accounts, and it helps you get things done. That is the right design for one person on one machine with one set of credentials. It is not a design a company can hand to a whole team.

A company has to answer different questions. Which team may use which model, and with whose key? What exactly did this agent do at 14:03 last Tuesday? Can it reach the public internet? Can it read a database secret? Who pays for the tokens?

Sympozium answers those as platform features rather than conventions. A `SympoziumPolicy` object carries tool allow and deny lists enforced on every run, sandbox requirements, network egress rules, image-registry allowlists, and whether skills may touch Kubernetes Secrets at all. Each Agent uses its own model key; only its sub-agents share it. The key's owner is recorded, and a model gateway checks it on every call. A skill — a packaged capability the agent can call — runs in its own sidecar, a second container beside the agent in the same pod, with short-lived least-privilege permissions that are cleaned up when the task ends. Each AgentRun, one execution of one agent, records a trace ID and token usage with an estimated cost.

## What else is out there, and why this one

Sympozium decides what agents *do*. It deliberately does not decide where their compute runs or how tokens move. Model placement belongs to llmfit-dra, a Kubernetes driver that places models through the standard scheduler. Serving belongs to an engine such as vLLM, SGLang or llama.cpp. When an agent needs a model, Sympozium claims one the way an application claims a PersistentVolume — a slice of storage the cluster hands out on request — and never picks the host itself.

Google's AX and Agent Substrate are the nearest neighbours. The project publishes a comparison that names where they are ahead (idle-agent density, suspend/resume, control-plane scale) and how the two could fit together. I would read that before choosing.

The reason to pick this one, in a sentence: it does one job — holding a team of agents together, permissioned and remembered — and refuses the others.

## The features that matter

The Synthetic Membrane is selective permeability for agent teams. Trust groups say which agents may see each other's output. Visibility tags mark individual pieces of context. Field-level gating hides one field inside a larger record. The point is that a research agent's raw dump should not land in front of the customer-facing agent.

Memory comes in two layers. A pack-level SQLite pool gives cross-persona sharing with per-persona access control and time decay, so older memories fade. A persona is a named role — researcher, reviewer, support. Underneath, SQLite with full-text search on a PersistentVolume means memories outlive the pods that wrote them.

Handoffs are declared, not improvised: delegation, sequential pipelines, supervision and stimulus triggers between personas, drawn on an interactive canvas so you can see the path a task takes.

An Ensemble is a Helm-like bundle — Helm is the package manager for Kubernetes — for a whole agent team. Activate a pack and the controller creates the Agents, the cron-style Schedules and the memory they need.

Channels are how humans reach the team: Telegram, Slack, Discord or WhatsApp, each running as its own workload in the cluster.

## How it is built

![Sympozium: when one AI agent isn't enough - svg wide](/docs/img/sympozium-when-one-ai-agent-isn-t-enough-svg_wide.svg){ width="80%" }

Agents run in isolated workloads. The control plane — the part that watches the declared objects and makes reality match — reconciles them the way any Kubernetes controller does. Skill code does not run inside the agent's process; it runs next to it in a sidecar with short-lived permissions and is collected afterwards. For stronger isolation, an agent can run in a sandbox built on gVisor or Kata through kubernetes-sigs/agent-sandbox, with warm pools for fast starts. OpenTelemetry traces and metrics, Prometheus endpoints, per-run trace IDs and cost estimates are all part of the platform rather than something you bolt on.

You can drive it from a terminal UI by running `sympozium`, from a browser dashboard with `sympozium serve`, or skip both and use Helm and kubectl.

One warning comes from the project itself: Sympozium is under active development, the APIs will change, and things will break.

## Starting it

The next article walks through the first install: what you need on the machine, the smallest setup that works, and the trap most people hit with local development clusters. Until then, `brew tap sympozium-ai/sympozium && brew install sympozium` followed by `sympozium install` is the whole first move, and the positioning page at deploy.sympozium.ai/docs is where the boundary of what Sympozium will and won't do is drawn most honestly.

Source: https://raw.githubusercontent.com/sympozium-ai/sympozium/main/README.md
