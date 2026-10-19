---
date: 2026-10-19
authors: [alvsanand]
categories: [OSS]
---
# Sympozium: Coordination for Agent Teams, Not Better Prompts

Five agents with five carefully tuned prompts will still duplicate each other's work, drop context at every handoff, and leave you unable to say which one read the production Secret. Sympozium's README puts its thesis in one line: agents don't need better prompts, they need shared situational awareness. The project is a Kubernetes-native coordination layer for multi-agent AI systems — selective permeability, structured handoffs, shared memory — where agents run in isolated workloads and every policy is a custom resource. It comes from the creator of k8sgpt and llmfit.

![Sympozium: Coordination for Agent Teams, Not Better Prompts - svg wide](/img/sympozium-coordination-for-agent-teams-not-better-prompts-svg_wide.svg){ width="80%" }

## Why these agents end up on Kubernetes

Strip an agent run down and what remains is a workload. It needs a model endpoint, a tool set, a filesystem, an identity, a timeout and an audit record. You already operate the component that supplies all six, and you already carry the on-call rotation for it.

The pull gets stronger with the second agent. A periodic agent is a scheduled job. A tool-using agent needs a service account narrow enough for those tools. A chat-facing assistant needs a long-lived Deployment and a route to a model. Each is a solved problem on Kubernetes and a greenfield build anywhere else.

## Why Sympozium

The README's framing is the part worth keeping: containers needed orchestration, agents need coordination.

The division of labour is deliberate. Sympozium decides what agents do. It does not decide where compute happens; that belongs to llmfit-dra, a Kubernetes DRA driver that places models by physics through the stock scheduler. It does not decide how tokens move either; that is the serving engine's job — vLLM, SGLang or llama.cpp. When an agent needs a model it claims one the way an application claims a PersistentVolume: it says what it needs and leaves placement alone.

If you have watched a platform team try to bolt scheduling policy onto an agent framework, that boundary stops the fight before it starts.

## What it does best

**Synthetic Membrane** is the coordination primitive. Trust groups, visibility tags and field-level gating decide what agents in a team may share, which is a different question from what they can reach over the network. The project publishes a paper on it.

**Ensembles** are Helm-like bundles for agent teams. Activate a pack and the controller stamps out the Agents, Schedules and memory that belong to it, so a persona topology becomes a reviewed artifact rather than a sequence of `kubectl apply`.

**Shared workflow memory** is a pack-level SQLite pool with per-persona access control and time decay. A researcher persona's findings outlive the pod that produced them, and the write path stays gated.

**Skill sidecars** carry the tool isolation story. Each skill runs in its own sidecar with ephemeral least-privilege RBAC and is garbage-collected on completion, so a skill's permissions exist for the length of a run and no longer.

**SympoziumPolicy** is where governance bites. One CRD covers tool gating with allow/deny lists enforced on every run, sandbox requirements, network egress rules, image-registry allowlists, and whether skills may touch Secrets.

**One key per Agent** closes the credential blast radius. Every Agent uses its own model key, and only its sub-agents share it. The key's owner is recorded on its Secret and checked by the controller, the admission webhook and, on every call, the model gateway. Skills cannot read Secrets or start pods that do unless a policy allows it. By default a gateway holds the provider keys, so hardware-isolated agents never see one.

**Cost and traces** arrive per run: OpenTelemetry traces and Prometheus endpoints, a trace ID per run, and token usage with an estimated cost on every `AgentRun`.

## Architecture

The control plane is the familiar trio: CRDs, controllers and an admission webhook. Helm split it into `sympozium-crds` and `sympozium` so the CRDs get their own upgrade lifecycle; the chart needs cert-manager for webhook TLS.

Below that are two execution substrates. Agent runs execute as pods, with skills as sidecars and kernel-level isolation available through `kubernetes-sigs/agent-sandbox` using gVisor or Kata on warm pools. The Celln fleet executes on microVMs instead, and the README's framing is that the harness stays and the task cell goes: the cell is discarded after returning its result.

The model gateway holds provider keys by default, with built-in routes for OpenAI, Anthropic and DeepSeek and keyless access to approved local servers. Channel integrations — Telegram, Slack, Discord, WhatsApp — are each a dedicated Deployment backed by NATS JetStream. Persistent memory is SQLite with FTS5 on a PersistentVolume.

## Where to start

I would install the control plane before designing a persona topology, because the first thing worth seeing is what a single `AgentRun` costs and traces like.

One caveat from the README: the project is under active development, the APIs will change, and things will break.

The follow-up walks through that first run, from install to a finished `AgentRun` record.

Source: https://raw.githubusercontent.com/sympozium-ai/sympozium/main/README.md
