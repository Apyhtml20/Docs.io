---
layout: post
title: Harness Engineering for AI Agents
date: 2026-08-22
categories:
  - AI Engineering
  - Agents
tags:
  - harness-engineering
  - agentic-ai
  - context-engineering
  - multi-agent
  - llmops
  - ETCLOVG
---

# Harness Engineering?

> How do we move from building an AI model application to building a reliable AI system?

When I started working on agentic AI systems, I initially thought that most of the work was about choosing a good LLM, writing a good system prompt, and connecting a few tools.

The more I worked on agents, the more I realized that this is only a small part of the problem.

An agent can have a very capable model and still fail because it has the wrong context, poor tool interfaces, no persistent state, weak validation, or too many permissions.

This is where **Harness Engineering** becomes interesting.

---

## From Prompt Engineering to Harness Engineering

I see three different levels of engineering around an LLM.

### Prompt Engineering

Prompt engineering focuses mainly on the instructions given to the model.

The goal is to improve a model interaction by changing:

- instructions
- examples
- roles
- output formats
- constraints

It is important, but it mainly focuses on the **input to the model**.

### Context Engineering

Context engineering goes one step further.

Instead of asking only:

> "What should I tell the model?"

the question becomes:

> "What information should the model have at this specific step?"

This includes:

- conversation history
- retrieved information
- memory
- tool results
- previous actions
- summaries
- intermediate state

The context becomes something that needs to be actively managed.

### Harness Engineering

Harness engineering expands the scope again.

Now the question is:

> "How should the whole system around the model operate?"

The harness controls things such as:

- where the agent executes
- which tools it can use
- what context it receives
- how its state is maintained
- how agents communicate
- how failures are handled
- how results are verified
- what actions are allowed
- how execution is monitored

---

## A Useful Mental Model

One idea that helped me understand the concept is:

> **The model provides intelligence, while the harness provides the environment in which that intelligence can actually work.**

This is useful because a raw LLM does not automatically have a workspace, memory, tools, permissions, execution capabilities, or feedback loops.

Those capabilities have to be designed around the model.

---

## The ETCLOVG Framework

The paper *Agent Harness Engineering: A Survey* proposes a seven-layer taxonomy called **ETCLOVG**:

- **E — Execution Environment & Sandbox**
- **T — Tool Interface & Protocol**
- **C — Context & Memory Management**
- **L — Lifecycle & Orchestration**
- **O — Observability & Operations**
- **V — Verification & Evaluation**
- **G — Governance & Security**

The first four layers form the structural core of the harness, while Observability, Verification, and Governance act as a control layer around it.

![The ETCLOVG taxonomy: seven layers of agent harness engineering]({{ '/assets/images/etclovg-overview.png' | relative_url }}){: .doc-diagram }

I find this classification useful because it gives a practical way to ask:

> "What is missing from my agent system?"

Instead of simply saying that an agent is unreliable, we can try to identify **which layer is responsible for the problem**.

---

## 1. Execution Environment & Sandbox

The first question is simple:

> **Where is the agent actually allowed to act?**

An agent that can execute code, modify files, install packages, or access the network needs an execution environment.

This can be:

- a container
- a sandbox
- a virtual machine
- a browser environment
- a restricted runtime
- a repository workspace

The survey highlights three important reasons for sandboxing:

1. **Security**
2. **Reproducibility**
3. **Liveness**

A sandbox provides a middle ground:

> The agent can act autonomously, but only inside a controlled boundary.

```text
Agent
  ↓
Workspace
  ↓
Sandbox
  ├── Files
  ├── Tools
  ├── Limited network
  └── Limited permissions
```

---

## 2. Tool Interface & Protocol

An agent becomes much more useful when it can interact with the outside world.

```text
LLM
 │
 ├── filesystem
 ├── terminal
 ├── database
 ├── browser
 ├── Git
 └── APIs
```

But simply giving an agent many tools is not necessarily a good design.

A better approach is often:

```text
User request
     ↓
Tool selection
     ↓
Relevant tools only
     ↓
Agent execution
```

> **More tools does not necessarily mean more agent capability.**

---

## 3. Context & Memory Management

A model can only reason over the information that is available to it.

```text
Short-term  →  Current task / active context
Mid-term    →  Session state
Long-term   →  Persistent memory
```

Instead of sending everything to the LLM, we want:

```text
Everything
    ↓
Selection → Filtering → Compression
    ↓
Relevant context
    ↓
LLM
```

---

## 4. Lifecycle & Orchestration

For a real task, the execution looks like:

```text
User request → Planning → Context construction
→ Tool selection → Execution → Observation
→ Validation → Retry / Continue / Delegate
→ Final result
```

For multi-agent systems:

```text
              Orchestrator
              /     |      \
        Context    Tool   Validator
          Agent    Agent    Agent
              \     |      /
               Final Result
```

> If one agent can solve the problem reliably, adding more agents introduces latency, tokens, and more failure points.

---

## 5. Observability & Operations

Without traces, we may only see: *"Agent returned the wrong answer."*

With observability, we can inspect:

```text
Trace
 ├── LLM call
 ├── Context size
 ├── Tool selection
 ├── Tool latency
 ├── Tool result
 ├── Retry
 ├── Validation
 └── Final response
```

Minimum to monitor: **Latency · Token usage · Cost · Tool calls · Errors · Retries · Context size · Success rate**

---

## 6. Verification & Evaluation

> Producing an output is not necessarily the same as completing the task correctly.

```text
Agent writes code → Run tests → Agent sees result → Agent corrects code
```

is generally better than:

```text
Agent writes code → Everything finishes → Human discovers failure
```

The first approach creates a feedback loop.

---

## 7. Governance & Security

> **What is the agent allowed to do?**

Instead of:

```text
Agent → Database → Everything
```

we want:

```text
Agent → Policy → Permission Check → Allowed Query → Database
```

---

## Humans Steer, Agents Execute

> **Humans steer. Agents execute.**

The engineer increasingly works on environments, constraints, interfaces, feedback loops, evaluation, architecture, and policies — while the agent operates inside those boundaries.

---

## Documentation Is Part of the Harness

A good repository should expose its knowledge clearly:

```text
AGENTS.md → Architecture → Design decisions
→ Execution plans → Implementation → Tests
```

Instead of putting hundreds of instructions into a single prompt, the agent can discover the information it needs progressively.

---

# Harness Engineering in Aptico

This is where I find the concept particularly useful for **Aptico CLI**.

The ETCLOVG model gives a useful way to reason about its architecture:

```text
ETCLOVG
   ├── E → Execution environment
   ├── T → Tools and interfaces
   ├── C → Context engineering and memory
   ├── L → Agent orchestration
   ├── O → Observability and LLMOps
   ├── V → Validation and evaluation
   └── G → Governance and security
```

![The Aptico CLI harness architecture]({{ '/assets/images/aptico-harness-architecture.png' | relative_url }}){: .doc-diagram }

For every new capability in Aptico, I can ask:

```text
Does it affect execution?     → E
Does it introduce a new tool? → T
Does it require new context?  → C
Does it change orchestration? → L
Can we observe it?            → O
Can we verify it?             → V
Is it secure?                 → G
```

---

# What I Would Take Into a Real Project

1. **Start with boundaries, not agents** — define what the system can and cannot access before creating agents.
2. **Treat context as a resource** — build a Retrieve → Rank → Filter → Compress → Inject pipeline.
3. **Every important action should have feedback** — Action → Observation → Validation → Correction.
4. **Design tools for agents** — a human-friendly API is not automatically an agent-friendly tool.
5. **Make failures observable** — if you can't answer *what did it see, decide, call, return*, debugging becomes guesswork.
6. **Build governance early** — permissions should be part of the architecture from the beginning.
7. **Keep the harness modular** — swap model, tools, context strategy, memory, or verifier without rewriting.

---

# Final Thought

The main thing I take from Harness Engineering is that building an agent is not simply about connecting an LLM to a few tools.

The real engineering challenge is **building the environment around the model**.

```text
Don't ask only: "Which model should I use?"

Also ask:
  "Where will it execute?"
  "What will it see?"
  "What can it do?"
  "How will it remember?"
  "How will I know what happened?"
  "How will I verify the result?"
  "What happens when it fails?"
  "What is it allowed to do?"
```

---

## References

- Li, J. et al. *Agent Harness Engineering: A Survey*, 2026.
- OpenAI. *Harness engineering: leveraging Codex in an agent-first world*, 2026.
- LangChain. *The Anatomy of an Agent Harness*, 2026.
