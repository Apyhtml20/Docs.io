---
categories:
- AI Engineering
- Agents
date: 2026-08-22
layout: post
tags:
- harness-engineering
- agentic-ai
- context-engineering
- multi-agent
- llmops
- ETCLOVG
title: Harness Engineering for AI Agents
---

# Harness Engineering for AI Agents

> How do we move from building an AI model application to building a
> reliable AI system?

When I started working on agentic AI systems, I initially thought that
most of the work was about choosing a good LLM, writing a good system
prompt, and connecting a few tools.

The more I worked on agents, the more I realized that this is only a
small part of the problem.

An agent can have a very capable model and still fail because it has the
wrong context, poor tool interfaces, no persistent state, weak
validation, or too many permissions.

This is where **Harness Engineering** becomes interesting.

## From Prompt Engineering to Harness Engineering

I see three different levels of engineering around an LLM.

### Prompt Engineering

Prompt engineering focuses mainly on the instructions given to the
model.

The goal is to improve a model interaction by changing:

-   instructions
-   examples
-   roles
-   output formats
-   constraints

It is important, but it mainly focuses on the **input to the model**.

### Context Engineering

Context engineering goes one step further.

Instead of asking only:

> "What should I tell the model?"

the question becomes:

> "What information should the model have at this specific step?"

This includes:

-   conversation history
-   retrieved information
-   memory
-   tool results
-   previous actions
-   summaries
-   intermediate state

The context becomes something that needs to be actively managed.

### Harness Engineering

Harness engineering expands the scope again.

Now the question is:

> "How should the whole system around the model operate?"

The harness controls things such as:

-   where the agent executes
-   which tools it can use
-   what context it receives
-   how its state is maintained
-   how agents communicate
-   how failures are handled
-   how results are verified
-   what actions are allowed
-   how execution is monitored

The survey describes this progression as an expansion from prompt
engineering to context engineering and finally to system-level harness
engineering.

------------------------------------------------------------------------

## A Useful Mental Model

One idea that helped me understand the concept is:

> **The model provides intelligence, while the harness provides the
> environment in which that intelligence can actually work.**

This is useful because a raw LLM does not automatically have a
workspace, memory, tools, permissions, execution capabilities, or
feedback loops.

Those capabilities have to be designed around the model.

------------------------------------------------------------------------

## The ETCLOVG Framework

The paper *Agent Harness Engineering: A Survey* proposes a seven-layer
taxonomy called **ETCLOVG**:

-   **E --- Execution Environment & Sandbox**
-   **T --- Tool Interface & Protocol**
-   **C --- Context & Memory Management**
-   **L --- Lifecycle & Orchestration**
-   **O --- Observability & Operations**
-   **V --- Verification & Evaluation**
-   **G --- Governance & Security**

The first four layers form the structural core of the harness, while
Observability, Verification, and Governance act as a control layer
around it.

![The ETCLOVG taxonomy: seven layers of agent harness engineering — Execution Environment & Sandbox, Tool Interface & Protocol, Context & Memory Management, Lifecycle & Orchestration, Observability & Operations, Verification & Evaluation, and Governance & Security](assets/images/etclovg-overview.png){: .doc-diagram }

I find this classification useful because it gives a practical way to
ask:

> "What is missing from my agent system?"

Instead of simply saying that an agent is unreliable, we can try to
identify **which layer is responsible for the problem**.

------------------------------------------------------------------------

## 1. Execution Environment & Sandbox

The first question is simple:

> **Where is the agent actually allowed to act?**

An agent that can execute code, modify files, install packages, or
access the network needs an execution environment.

This can be:

-   a container
-   a sandbox
-   a virtual machine
-   a browser environment
-   a restricted runtime
-   a repository workspace

The important point is that execution should not happen directly on an
uncontrolled environment.

The survey highlights three important reasons for sandboxing:

1.  **Security**
2.  **Reproducibility**
3.  **Liveness**

The last point is particularly interesting.

If every agent action requires human approval, the agent becomes slow
and difficult to use. But if everything is allowed, the security
boundary becomes meaningless.

A sandbox provides a middle ground:

> The agent can act autonomously, but only inside a controlled boundary.

### Practical observation

For a coding agent, I would not start by giving it access to the entire
machine.

I would start with:

``` text
┌───────┐
│ Agent │
└───────┘
    │
    ▼
┌───────────┐
│ Workspace │
└───────────┘
    │
    ▼
┌─────────┐
│ Sandbox │
└─────────┘
    │
    ├── Files
    ├── Tools
    ├── Limited network
    └── Limited permissions
```

This makes failures easier to reproduce and easier to investigate.

------------------------------------------------------------------------

## 2. Tool Interface & Protocol

An agent becomes much more useful when it can interact with the outside
world.

For example:

``` text
┌─────┐
│ LLM │
└─────┘
    │
    ├── filesystem
    ├── terminal
    ├── database
    ├── browser
    ├── Git
    └── APIs
```

But simply giving an agent many tools is not necessarily a good design.

The tools need to be:

-   clearly described
-   discoverable
-   predictable
-   correctly parameterized
-   permission-controlled
-   easy to validate

### A practical problem

Imagine giving an agent 50 tools at startup.

The model now has to understand 50 tool descriptions before doing the
actual task.

This increases context usage and can make tool selection more difficult.

A better approach is often:

``` text
┌──────────────┐
│ User request │
└──────────────┘
        │
        ▼
┌────────────────┐
│ Tool selection │
└────────────────┘
         │
         ▼
┌─────────────────────┐
│ Relevant tools only │
└─────────────────────┘
           │
           ▼
┌─────────────────┐
│ Agent execution │
└─────────────────┘
```

### Observation

**More tools does not necessarily mean more agent capability.**

Sometimes the better architecture is not to add another tool, but to
make the existing tools easier for the agent to understand and use
correctly.

------------------------------------------------------------------------

## 3. Context & Memory Management

This is probably one of the most important parts of an agent system.

A model can only reason over the information that is available to it.

But in a long-running task, the amount of information can become very
large.

We can think about context at different levels:

``` text
┌────────────┐
│ Short-term │
└────────────┘
       │
       ▼
┌───────────────────────────────┐
│ Current task / active context │
└───────────────────────────────┘


┌──────────┐
│ Mid-term │
└──────────┘
      │
      ▼
┌───────────────┐
│ Session state │
└───────────────┘


┌───────────┐
│ Long-term │
└───────────┘
      │
      ▼
┌───────────────────┐
│ Persistent memory │
└───────────────────┘
```

The survey distinguishes short-term active context, session-level
persistence, long-term memory, long-horizon techniques, and context
drift.

### The interesting part

A common mistake is to think:

> "The model has a large context window, so we can put everything inside
> it."

I don't think this is a good strategy.

A large context window does not mean that every piece of information is
equally useful.

For example, if an agent is fixing a Python bug, it probably does not
need:

-   the entire Git history
-   every previous conversation
-   every log ever generated
-   every file in the repository

It needs the **relevant context**.

So instead of:

``` text
Everything → LLM
```

we want:

``` text
┌────────────┐
│ Everything │
└────────────┘
       │
       ▼
┌───────────┐
│ Selection │
└───────────┘
      │
      ▼
┌───────────┐
│ Filtering │
└───────────┘
      │
      ▼
┌─────────────┐
│ Compression │
└─────────────┘
       │
       ▼
┌──────────────────┐
│ Relevant context │
└──────────────────┘
          │
          ▼
┌─────┐
│ LLM │
└─────┘
```

This is where Context Engineering becomes an essential part of Harness
Engineering.

------------------------------------------------------------------------

## 4. Lifecycle & Orchestration

An agent is rarely just:

``` text
User → LLM → Answer
```

For a real task, the execution can look more like:

``` text
┌──────────────┐
│ User request │
└──────────────┘
        │
        ▼
┌──────────┐
│ Planning │
└──────────┘
      │
      ▼
┌──────────────────────┐
│ Context construction │
└──────────────────────┘
            │
            ▼
┌────────────────┐
│ Tool selection │
└────────────────┘
         │
         ▼
┌───────────┐
│ Execution │
└───────────┘
      │
      ▼
┌─────────────┐
│ Observation │
└─────────────┘
       │
       ▼
┌────────────┐
│ Validation │
└────────────┘
       │
       ▼
┌─────────────────────────────┐
│ Retry / Continue / Delegate │
└─────────────────────────────┘
               │
               ▼
┌──────────────┐
│ Final result │
└──────────────┘
```

For multi-agent systems, this becomes even more interesting.

For example:

``` text
                          ┌──────────────┐
                          │ Orchestrator │
                          └──────┬───────┘
              ┌───────────────────┼───────────────────┐
              ▼                   ▼                   ▼
      ┌───────────────┐    ┌────────────┐    ┌──────────────────┐
      │ Context Agent │    │ Tool Agent │    │ Validator Agent  │
      └───────┬───────┘    └─────┬──────┘    └────────┬─────────┘
              │                  │                     │
              └──────────────────┼─────────────────────┘
                                 ▼
                          ┌──────────────┐
                          │ Final Result │
                          └──────────────┘
```

The Lifecycle layer is responsible for deciding how these steps are
connected.

### Practical observation

I would avoid creating multiple agents just because multi-agent systems
are popular.

If one agent can solve the problem reliably, adding three more agents
can simply introduce:

-   more latency
-   more tokens
-   more communication
-   more failure points
-   more debugging complexity

The important question is not:

> "How many agents can I use?"

but:

> "Where does delegation actually improve the system?"

------------------------------------------------------------------------

## 5. Observability & Operations

When an agent fails, the final answer is often not enough to understand
why.

For example:

``` text
┌──────┐
│ User │
└──────┘
    │
    ▼
┌───────┐
│ Agent │
└───────┘
    │
    ▼
┌───────────┐
│ Tool A  ✓ │
└───────────┘
      │
      ▼
┌───────────┐
│ Tool B  ✓ │
└───────────┘
      │
      ▼
┌───────────┐
│ Tool C  ✗ │
└───────────┘
      │
      ▼
┌───────┐
│ Retry │
└───────┘
    │
    ▼
┌──────────────┐
│ Wrong result │
└──────────────┘
```

Without traces, we may only see:

> Agent returned the wrong answer.

With observability, we can inspect:

``` text
┌───────┐
│ Trace │
└───────┘
    │
    ├── LLM call
    ├── Context size
    ├── Tool selection
    ├── Tool latency
    ├── Tool result
    ├── Retry
    ├── Validation
    └── Final response
```

This makes agent behavior much easier to debug.

### What I would monitor

For an agent system, I would at least track:

-   Latency
-   Token usage
-   Cost
-   Tool calls
-   Errors
-   Retries
-   Context size
-   Success rate
-   Validation results

This becomes especially important when agents can run for a long time.

------------------------------------------------------------------------

## 6. Verification & Evaluation

One of the biggest differences between a chatbot and an agent is that an
agent can **take actions**.

Therefore:

> Producing an output is not necessarily the same as completing the task
> correctly.

For example, suppose an agent is asked:

> "Fix this bug."

The agent modifies the code.

That does not mean the task is finished.

A better workflow is:

``` text
┌─────────────────┐
│ Understand task │
└─────────────────┘
         │
         ▼
┌────────────────┐
│ Execute change │
└────────────────┘
         │
         ▼
┌───────────┐
│ Run tests │
└───────────┘
      │
      ▼
┌────────────────┐
│ Inspect result │
└────────────────┘
         │
         ▼
┌─────────────────┐
│ Verify behavior │
└─────────────────┘
         │
         ▼
┌────────────────┐
│ Accept / Retry │
└────────────────┘
```

The survey organizes verification around benchmark grounding, readiness
validation, controlled execution and trace capture, judgement/failure
attribution, and regression feedback.

### A useful engineering principle

I think verification should happen as close as possible to the action
that needs verification.

For example:

``` text
┌───────────────────┐
│ Agent writes code │
└───────────────────┘
          │
          ▼
┌───────────┐
│ Run tests │
└───────────┘
      │
      ▼
┌───────────────────┐
│ Agent sees result │
└───────────────────┘
          │
          ▼
┌─────────────────────┐
│ Agent corrects code │
└─────────────────────┘
```

is generally better than:

``` text
┌───────────────────┐
│ Agent writes code │
└───────────────────┘
          │
          ▼
┌─────────────────────┐
│ Everything finishes │
└─────────────────────┘
           │
           ▼
┌─────────────────────────┐
│ Human discovers failure │
└─────────────────────────┘
```

The first approach creates a feedback loop.

------------------------------------------------------------------------

## 7. Governance & Security

The final layer asks:

> **What is the agent allowed to do?**

This becomes critical when an agent can:

-   execute shell commands
-   access files
-   modify repositories
-   access databases
-   call APIs
-   interact with cloud resources

Governance can include:

-   permissions
-   identity
-   policies
-   audit logs
-   security boundaries
-   approval mechanisms
-   lifecycle hooks
-   component hardening

### Example

Instead of:

``` text
Agent → Database → Everything
```

we want:

``` text
┌───────┐
│ Agent │
└───────┘
    │
    ▼
┌────────┐
│ Policy │
└────────┘
     │
     ▼
┌──────────────────┐
│ Permission Check │
└──────────────────┘
          │
          ▼
┌───────────────┐
│ Allowed Query │
└───────────────┘
        │
        ▼
┌──────────┐
│ Database │
└──────────┘
```

This is particularly important when the agent generates actions
dynamically.

------------------------------------------------------------------------

## Humans Steer, Agents Execute

One of the ideas I find particularly interesting is the shift in the
role of the engineer.

A useful way to summarize it is:

> **Humans steer. Agents execute.**

The engineer increasingly works on:

-   environments
-   constraints
-   interfaces
-   feedback loops
-   evaluation
-   architecture
-   policies

while the agent operates inside those boundaries.

This does not mean removing humans from engineering.

Instead, humans move to a higher level of abstraction.

------------------------------------------------------------------------

## Documentation Is Part of the Harness

Another observation that I find very useful for real projects is that
documentation is not only for humans.

If an agent cannot discover an important architectural decision, then
from the agent's point of view that decision is almost invisible.

A good repository should therefore expose its knowledge clearly:

``` text
┌───────────┐
│ AGENTS.md │
└───────────┘
      │
      ▼
┌──────────────┐
│ Architecture │
└──────────────┘
        │
        ▼
┌──────────────────┐
│ Design decisions │
└──────────────────┘
          │
          ▼
┌─────────────────┐
│ Execution plans │
└─────────────────┘
         │
         ▼
┌────────────────┐
│ Implementation │
└────────────────┘
         │
         ▼
┌───────┐
│ Tests │
└───────┘
```

Instead of putting hundreds of instructions into a single prompt, the
agent can discover the information it needs progressively.

This is especially useful for large repositories where the entire
codebase cannot be placed into the context window.

------------------------------------------------------------------------

# Harness Engineering in Aptico

This is where I find the concept particularly useful for **Aptico CLI**.

Aptico is designed as a multi-agent AI harness for terminal-based
workflows.

The ETCLOVG model gives me a useful way to reason about its
architecture:

``` text
┌─────────┐
│ ETCLOVG │
└─────────┘
     │
     ├── E → Execution environment
     ├── T → Tools and interfaces
     ├── C → Context engineering and memory
     ├── L → Agent orchestration
     ├── O → Observability and LLMOps
     ├── V → Validation and evaluation
     └── G → Governance and security
```

![The Aptico CLI harness architecture: Observability & Operations monitors the four core layers (Execution & Sandbox, Tool Interface & Protocol, Context & Memory Management, Lifecycle & Orchestration), which feed into Verification & Evaluation and finally Governance & Security](assets/images/aptico-harness-architecture.png){: .doc-diagram }

I do not see ETCLOVG as something that must be implemented literally.

I see it more as an **architectural checklist**.

For every new capability in Aptico, I can ask:

-   Does it affect execution?
-   Does it introduce a new tool?
-   Does it require new context?
-   Does it change orchestration?
-   Can we observe it?
-   Can we verify it?
-   Is it secure?

This makes architectural decisions easier to reason about.

------------------------------------------------------------------------

# What I Would Take Into a Real Project

## 1. Start with boundaries, not agents

Before creating five specialized agents, define:

-   what the system can access
-   what it cannot access
-   what tools exist
-   where execution happens
-   how state is stored

A clear environment can be more valuable than another prompt.

## 2. Treat context as a resource

Do not send everything to the LLM.

Build a context pipeline:

``` text
┌──────────┐
│ Retrieve │
└──────────┘
      │
      ▼
┌──────┐
│ Rank │
└──────┘
    │
    ▼
┌────────┐
│ Filter │
└────────┘
     │
     ▼
┌──────────┐
│ Compress │
└──────────┘
      │
      ▼
┌────────┐
│ Inject │
└────────┘
```

This can reduce cost and make the agent's behavior more predictable.

## 3. Every important action should have feedback

If the agent modifies something, give it a way to check the result.

``` text
┌────────┐
│ Action │
└────────┘
     │
     ▼
┌─────────────┐
│ Observation │
└─────────────┘
       │
       ▼
┌────────────┐
│ Validation │
└────────────┘
       │
       ▼
┌────────────┐
│ Correction │
└────────────┘
```

This is much more powerful than simply increasing the model size.

## 4. Design tools for agents

A human-friendly API is not automatically an agent-friendly tool.

Tool descriptions, parameters, errors, return values, and permissions
all influence how reliably the model can use the tool.

## 5. Make failures observable

If an agent fails, I want to answer:

-   What did it see?
-   What did it decide?
-   Which tool did it call?
-   What did the tool return?
-   How long did it take?
-   Did it retry?
-   Why did validation fail?

If these questions cannot be answered, debugging becomes guesswork.

## 6. Build governance early

Security should not be added after the agent becomes autonomous.

Permissions should be part of the architecture from the beginning.

``` text
┌────────────┐
│ Capability │
└────────────┘
       │
       ▼
┌────────────┐
│ Permission │
└────────────┘
       │
       ▼
┌────────┐
│ Policy │
└────────┘
     │
     ▼
┌───────────┐
│ Execution │
└───────────┘
      │
      ▼
┌───────┐
│ Audit │
└───────┘
```

## 7. Keep the harness modular

A good harness should allow us to change:

-   Model
-   Tools
-   Context strategy
-   Memory
-   Orchestrator
-   Verifier
-   Policies
-   Observability

without rewriting the whole system.

This becomes especially important as models evolve.

------------------------------------------------------------------------

# Final Thought

The main thing I take from Harness Engineering is that building an agent
is not simply about connecting an LLM to a few tools.

The real engineering challenge is building the environment around the
model.

Reliable agents need execution environments, tools, context and memory,
lifecycle management, observability, verification, and governance.

For me, the useful mindset is therefore:

Don't ask only:

> "Which model should I use?"

Also ask:

-   "Where will it execute?"
-   "What will it see?"
-   "What can it do?"
-   "How will it remember?"
-   "How will I know what happened?"
-   "How will I verify the result?"
-   "What happens when it fails?"
-   "What is it allowed to do?"

That is where I see **Harness Engineering** becoming an important part
of modern AI engineering.

------------------------------------------------------------------------

## References

-   Li, J. et al. *Agent Harness Engineering: A Survey*, 2026.
-   OpenAI. *Harness engineering: leveraging Codex in an agent-first
    world*, 2026.
-   LangChain. *The Anatomy of an Agent Harness*, 2026.
