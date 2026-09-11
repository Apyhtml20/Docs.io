---
layout: post
title: Why world models?
date: 2026-09-02
cover: /assets/images/covers/world-models.jpg
categories:
  - AI Engineering
tags:
  - world-models
  - reinforcement-learning
---
# Why World Models?

> From Vision to JEPA, Reinforcement Learning and Agents.

## 1. Why World Models over the Classical Ones?

Most AI models solve a specific problem:

  Component                Main question
  ------------------------ --------------------------------------------
  Vision model             What do I see?
  LLM                      What should I generate/reason about?
  RAG                      What information can I retrieve?
  Memory                   What happened before?
  Tool                     What can I do?
  Reinforcement Learning   Which behavior maximizes long-term reward?
  World Model              What could happen next?
  Planner                  Which action should I take?

The key idea of a World Model is:

``` text
Current State + Action
          ↓
   Future State
```

Instead of only reacting to the current observation, the agent can model
possible future states before acting.

------------------------------------------------------------------------

## 2. From Perception to World Modeling

A vision model can detect objects:

``` text
Image
  ↓
Vision Encoder
  ↓
Objects / Features
```

For example:

``` text
Image → YOLO → car, person, road
```

This answers:

> What is present?

A World Model adds temporal and dynamic information:

``` text
Observation(t)
      +
Action(t)
      ↓
World Model
      ↓
Predicted State(t+1)
```

The question becomes:

> What happens if I perform this action?

------------------------------------------------------------------------

## 3. Vision as the First Layer

World Models need a representation of the environment.

Several vision architectures provide useful building blocks.

### CNN

CNNs extract hierarchical visual features:

``` text
Pixels
 ↓
Edges
 ↓
Textures
 ↓
Shapes
 ↓
Objects
```

### ViT

Vision Transformer:

``` text
Image
 ↓
Patch Embedding
 ↓
Visual Tokens
 ↓
Transformer
 ↓
Visual Representation
```

### YOLO

Object detection:

``` text
Image
 ↓
YOLO
 ↓
Bounding Boxes + Classes
```

### DETR

Transformer-based object detection:

``` text
Image
 ↓
Backbone
 ↓
Transformer
 ↓
Object Queries
 ↓
Objects
```

### SAM

Segmentation:

``` text
Image
 ↓
SAM
 ↓
Segmentation Masks
```

### CLIP

Joint image-text representation:

``` text
Image → Image Embedding
              ↕
          Similarity
              ↕
Text  → Text Embedding
```

These models provide perception and representations that can feed a
World Model.

------------------------------------------------------------------------

## 4. Why Latent Space?

A World Model does not necessarily need to predict every pixel.

An image contains a large amount of information:

``` text
1920 × 1080 × 3 pixels
```

But for planning, the important information may be:

``` text
Objects
Positions
Motion
Relations
Context
Environment State
```

Therefore:

``` text
Observation
     ↓
Encoder
     ↓
Latent State
     ↓
World Model
```

The objective is to learn a compact representation that preserves
information useful for prediction and decision-making.

------------------------------------------------------------------------

## 5. The Four Core Ideas

A useful way to understand World Models together with Reinforcement
Learning is through four concepts:

1.  Compression / latent representation
2.  Reward over time
3.  Value
4.  Behavior learning

![The four core building blocks of a World Model: compression, reward over time, value, and behavior learning]({{ '/assets/images/why_world_models_images/four_core_ideas.png' | relative_url }}){: .doc-diagram }

------------------------------------------------------------------------

### 5.1 Compression --- Latent Space

Instead of modeling raw observations directly:

``` text
Pixels
 ↓
Encoder
 ↓
Latent Representation
```

The latent state should contain the information needed to predict
relevant future states.

A simplified dynamics model is:

<div class="math-block">
$$
z_{t+1} = f(z_t, a_t)
$$
</div>

where:

-   \\(z_t\\) = current latent state
-   \\(a_t\\) = action
-   \\(z_{t+1}\\) = predicted future latent state

------------------------------------------------------------------------

### 5.2 Reward Over Time

In Reinforcement Learning, an action can have consequences several steps
later.

The return is:

<div class="math-block">
$$
G_t = r_t + \gamma r_{t+1} + \gamma^2 r_{t+2} + \dots
$$
</div>

where:

-   \\(r_t\\) = reward at time \\(t\\)
-   \\(\gamma\\) = discount factor

Therefore, an action should not only be evaluated by its immediate
reward.

``` text
Action
 ↓
Immediate reward
 ↓
Future states
 ↓
Future rewards
```

This is one reason temporal modeling is important.

------------------------------------------------------------------------

### 5.3 Value

The value function estimates the expected future return from a state:

<div class="math-block">
$$
V(s) = \mathbb{E}\left[ \sum_{k=0}^{\infty} \gamma^k r_{t+k} \;\middle|\; s_t = s \right]
$$
</div>

Simplified:

``` text
State
 ↓
Value
 ↓
Expected future quality
```

This is useful for comparing possible trajectories.

------------------------------------------------------------------------

### 5.4 Learning Behavior

A common RL architecture separates the Actor and Critic:

``` text
              State
             /     \
            ↓       ↓
         Actor     Critic
           ↓         ↓
        Action      Value
```

-   **Actor**: chooses an action.
-   **Critic**: evaluates the state/action.

This is close to the broader cognitive architecture proposed by Yann LeCun, where a World Model sits alongside Perception, an Actor and a Critic:

![LeCun's cognitive architecture: Perception, World Model, Actor and Critic working together]({{ '/assets/images/why_world_models_images/lecun_cognitive_architecture.png' | relative_url }}){: .doc-diagram }

This can be combined with a World Model:

``` text
Current State
      ↓
World Model
      ↓
Possible Futures
      ↓
Critic / Value
      ↓
Planner / Actor
      ↓
Action
```

------------------------------------------------------------------------

## 6. Model-Free vs Model-Based Reinforcement Learning

### Model-Free RL

The agent learns a direct mapping:

``` text
State
 ↓
Policy
 ↓
Action
```

The agent mainly learns:

> In this situation, which action works?

### Model-Based RL

The agent also learns a model of the environment:

``` text
State + Action
       ↓
  World Model
       ↓
 Future State
       ↓
   Planning
       ↓
     Action
```

The agent can therefore evaluate actions using predicted outcomes.

------------------------------------------------------------------------

## 7. World Models and Simulation

The main advantage of a learned world model is the possibility of
simulation.

Suppose the agent has three possible actions:

``` text
Current State
     │
     ├── Action A → Future A
     │
     ├── Action B → Future B
     │
     └── Action C → Future C
```

The agent can evaluate the predicted futures before executing an action.

This creates:

``` text
Observe
   ↓
Represent
   ↓
Predict
   ↓
Simulate
   ↓
Evaluate
   ↓
Plan
   ↓
Act
```

------------------------------------------------------------------------

## 8. World Models + Planning

The World Model and the Planner have different roles.

### World Model

> What could happen?

### Planner

> Which sequence of actions should I choose?

``` text
             Current State
                   ↓
             World Model
                   ↓
          Predicted Futures
            /      |      \
           A       B       C
           ↓       ↓       ↓
        Future   Future   Future
            \      |      /
                 Planner
                    ↓
                Best Action
```

------------------------------------------------------------------------

## 9. World Models + Reinforcement Learning

The complete loop becomes:

``` text
Environment
     ↓
Observation
     ↓
World Model
     ↓
Predicted Future States
     ↓
Value / Reward
     ↓
Planning
     ↓
Action
     ↓
Environment
```

This is the basis of many **model-based RL** approaches.

------------------------------------------------------------------------

## 10. World Models

One important early work is **World Models** by Ha and Schmidhuber
(2018).

The idea was to learn a compressed spatial-temporal representation of an
environment and use this learned model to train a controller in an
imagined environment.

Conceptually:

``` text
Real Environment
      ↓
Collect Experience
      ↓
Learn World Model
      ↓
Dream / Simulate
      ↓
Train Controller
      ↓
Real Environment
```

Reference:

-   Ha & Schmidhuber, *World Models*, 2018
-   https://arxiv.org/abs/1803.10122

------------------------------------------------------------------------

## 11. Dreamer

Dreamer is another important family of model-based RL methods.

The central idea is to learn an environment model and improve the policy
using imagined trajectories.

``` text
Real Experience
      ↓
World Model
      ↓
Latent Dynamics
      ↓
Imagined Trajectories
      ↓
Actor-Critic
      ↓
Improved Policy
```

DreamerV3 extended this approach across a broad set of tasks.

Reference:

-   Hafner et al., *Mastering Diverse Domains through World Models*,
    2023
-   https://arxiv.org/abs/2301.04104

------------------------------------------------------------------------

## 12. MuZero

MuZero is important because it does not need to reconstruct the complete
environment.

It learns the information needed for planning:

``` text
Observation
    ↓
Representation
    ↓
Dynamics
    ↓
Reward / Value
    ↓
Planning
```

MuZero combines learned representations and dynamics with search.

It was demonstrated on environments including Atari and board games such
as Go, chess and shogi.

Reference:

-   Schrittwieser et al., *Mastering Atari, Go, Chess and Shogi by
    Planning with a Learned Model*, Nature, 2020
-   https://www.nature.com/articles/s41586-020-03051-4

------------------------------------------------------------------------

## 13. JEPA

JEPA stands for:

> **Joint Embedding Predictive Architecture**

The main idea is to predict representations rather than reconstruct
every pixel.

Simplified:

``` text
Context
   ↓
Encoder
   ↓
Context Embedding
   ↓
Predictor
   ↓
Predicted Target Embedding

Target
   ↓
Target Encoder
   ↓
Target Embedding
```

The objective is to make the predicted representation close to the
target representation.

This is different from pixel-level reconstruction.

![Yann LeCun's shift in focus from LLMs toward JEPA and World Models]({{ '/assets/images/why_world_models_images/lecun_shift_llm_to_jepa.png' | relative_url }}){: .doc-diagram }

------------------------------------------------------------------------

## 14. I-JEPA

I-JEPA applies this principle to images.

Instead of predicting the exact pixels of a missing region, the model
predicts the representation of that region.

``` text
Image
 ↓
Context Region
 ↓
Encoder
 ↓
Predictor
 ↓
Target Representation
```

The important idea is:

> Learn useful semantic representations without requiring pixel-level
> reconstruction.

Reference:

-   Assran et al., *Self-Supervised Learning from Images with a
    Joint-Embedding Predictive Architecture*, CVPR 2023
-   https://arxiv.org/abs/2301.08243

------------------------------------------------------------------------

## 15. V-JEPA

V-JEPA extends the JEPA idea to video.

A video contains both spatial and temporal information:

``` text
Frame(t-2)
Frame(t-1)
Frame(t)
Frame(t+1)
Frame(t+2)
```

Therefore:

``` text
Video
 ↓
Spatio-Temporal Representation
 ↓
Predict Missing / Future Representation
```

This is relevant to World Models because an environment is not static.

The model must represent:

``` text
What is here?
+
How does it change?
```

------------------------------------------------------------------------

## 16. V-JEPA 2

V-JEPA 2 extends the predictive representation idea toward world
modeling and physical reasoning.

A simplified view is:

``` text
Video
 ↓
Vision Encoder
 ↓
World Representation
 ↓
Predictor
 ↓
Future Representation
```

With action-conditioned learning:

``` text
Current State + Action
          ↓
       Predictor
          ↓
    Future State
```

This creates a connection between:

``` text
Self-Supervised Vision
        +
JEPA
        +
World Models
        +
Actions
        +
Planning
        +
Robotics
```

Reference:

-   Meta AI, *V-JEPA 2*
-   https://ai.meta.com/blog/v-jepa-2-world-model-benchmarks/

------------------------------------------------------------------------

## 17. World Model vs LLM

A simplified distinction:

### LLM

``` text
Tokens
 ↓
Transformer
 ↓
Next-token prediction
```

### World Model

``` text
State + Action
      ↓
Future State
```

They can work together:

``` text
              Agent
                │
        ┌───────┴───────┐
        ↓               ↓
       LLM          World Model
        ↓               ↓
 Reasoning        Prediction
        │               │
        └───────┬───────┘
                ↓
             Planning
                ↓
              Action
```

A World Model is therefore not a replacement for an LLM.

It provides a model of an environment and its dynamics.

------------------------------------------------------------------------

## 18. World Model vs RAG

RAG and World Models solve different problems.

### RAG

``` text
Question
 ↓
Retriever
 ↓
Documents
 ↓
LLM
 ↓
Answer
```

Main question:

> What information can I retrieve?

### World Model

``` text
Current State
      +
Action
      ↓
Future State
```

Main question:

> What could happen?

Therefore:

``` text
RAG       → Knowledge
Memory    → History
LLM       → Reasoning
World Model → Dynamics
Planner   → Decision
Tools     → Action
```

------------------------------------------------------------------------

## 19. World Model + Agents

A simple agent loop is:

``` text
Observe
   ↓
Think
   ↓
Act
```

A more advanced agent can use:

``` text
Observe
   ↓
Understand
   ↓
World Model
   ↓
Predict
   ↓
Plan
   ↓
Validate
   ↓
Act
   ↓
Observe Again
```

This is particularly relevant to agentic systems.

------------------------------------------------------------------------

## 20. Connection with Context Engineering

Context Engineering manages what the agent knows at a given moment:

``` text
System Instructions
+
User Request
+
Memory
+
RAG
+
Tool Results
+
Current State
```

A World Model adds another capability:

``` text
Current Context
      ↓
World State
      ↓
Dynamics
      ↓
Future Prediction
```

So:

``` text
Context Engineering
→ What information is available?

World Model
→ How can the environment evolve?
```

------------------------------------------------------------------------

## 21. Gymnasium

To experiment with these ideas, **Gymnasium** is particularly useful.

It provides standardized Reinforcement Learning environments and an API
for agent-environment interaction.

The basic loop is:

``` text
Observation
    ↓
Agent
    ↓
Action
    ↓
Environment
    ↓
Reward + New Observation
```

Basic example:

``` python
import gymnasium as gym

env = gym.make("CartPole-v1")

observation, info = env.reset()

for _ in range(1000):
    action = env.action_space.sample()

    observation, reward, terminated, truncated, info = env.step(action)

    if terminated or truncated:
        observation, info = env.reset()

env.close()
```

Reference:

-   https://gymnasium.farama.org/

------------------------------------------------------------------------

## 22. Why Gymnasium is useful for World Model projects

Gymnasium provides controlled environments where we can collect
transitions:

``` text
(s_t, a_t, r_t, s_t+1)
```

This is exactly the type of data required to learn environment dynamics.

For example:

``` text
State + Action
      ↓
World Model
      ↓
Predicted Next State
```

Then compare:

``` text
Predicted State
       vs
Real State
```

This gives a measurable prediction error.

------------------------------------------------------------------------

## 23. A Practical World Model Project Roadmap

### Project 1 --- Model-Free RL

Start with:

``` text
Gymnasium
   ↓
Observation
   ↓
Policy
   ↓
Action
```

Examples:

-   CartPole
-   MountainCar
-   LunarLander

------------------------------------------------------------------------

### Project 2 --- Learn Environment Dynamics

Collect:

``` text
(s_t, a_t, r_t, s_t+1)
```

Train:

``` text
f(s_t, a_t) → s_t+1
```

Then measure:

``` text
Prediction Error
```

------------------------------------------------------------------------

### Project 3 --- Model-Based RL

Use the learned model:

``` text
State
 ↓
World Model
 ↓
Simulate actions
 ↓
Evaluate futures
 ↓
Choose action
```

------------------------------------------------------------------------

### Project 4 --- Latent World Model

Instead of predicting raw states:

``` text
Observation
 ↓
Encoder
 ↓
Latent State
 ↓
Dynamics Model
 ↓
Future Latent State
```

This is closer to modern latent world model approaches.

------------------------------------------------------------------------

### Project 5 --- Vision World Model

Use visual observations:

``` text
Image / Video
      ↓
Vision Encoder
      ↓
Latent State
      ↓
World Model
      ↓
Future Latent State
```

Possible encoders:

``` text
ViT
CLIP
CNN
Video Transformer
JEPA-style encoder
```

------------------------------------------------------------------------

### Project 6 --- Agent + World Model

Final architecture:

``` text
Environment
     ↓
Perception
     ↓
World Model
     ↓
Simulation
     ↓
Value
     ↓
Planner
     ↓
Agent
     ↓
Tools / Actions
     ↓
Environment
```

------------------------------------------------------------------------

## 24. A Complete Architecture

``` text
                         ENVIRONMENT
                              │
                              ▼
                         PERCEPTION
                              │
                  ┌───────────┴───────────┐
                  │                       │
                 Vision                  Text
                  │                       │
                  └───────────┬───────────┘
                              ▼
                        WORLD STATE
                              │
                              ▼
                        WORLD MODEL
                              │
                    ┌─────────┴─────────┐
                    ↓                   ↓
                Prediction          Simulation
                    │                   │
                    └─────────┬─────────┘
                              ↓
                           VALUE
                              ↓
                           PLANNER
                              ↓
                            AGENT
                              │
                       ┌──────┴──────┐
                       ↓             ↓
                     LLM           Tools
                       │             │
                       └──────┬──────┘
                              ↓
                          VALIDATION
                              ↓
                            ACTION
                              ↓
                         ENVIRONMENT
                              │
                              └────→ New Observation
```

------------------------------------------------------------------------

## 25. Main Challenges

World Models are not perfect.

### Prediction Error

``` text
Predicted Future
       ≠
Real Future
```

### Error Accumulation

``` text
Prediction t1
     ↓
Prediction t2
     ↓
Prediction t3
     ↓
Increasing uncertainty
```

### Distribution Shift

A model trained in one environment may fail in another.

### Representation

The model must learn what information is actually useful for prediction
and planning.

### Computation

Long-horizon simulation can be expensive.

------------------------------------------------------------------------

## 26. The Core Idea

The progression can be summarized as:

``` text
CNN
 ↓
What do I see?

Transformer
 ↓
How are the elements related?

Vision Models
 ↓
What is in the environment?

LLM
 ↓
How can I reason about information?

RAG
 ↓
What information can I retrieve?

Agent
 ↓
What can I do?

Reinforcement Learning
 ↓
Which behavior maximizes future reward?

World Model
 ↓
What could happen if I act?
```

And the complete loop becomes:

``` text
OBSERVE
   ↓
REPRESENT
   ↓
PREDICT
   ↓
SIMULATE
   ↓
EVALUATE
   ↓
PLAN
   ↓
ACT
   ↓
OBSERVE AGAIN
```

------------------------------------------------------------------------

## 27. Final Takeaway

A World Model is not simply another neural network architecture.

It is a way to model the dynamics of an environment so that an agent
can:

-   represent its current state;
-   predict possible future states;
-   evaluate consequences;
-   simulate actions;
-   plan;
-   act;
-   learn from feedback.

The main connection between the topics is:

``` text
Computer Vision
      ↓
Representation Learning
      ↓
JEPA
      ↓
World Model
      ↓
Prediction
      ↓
Reinforcement Learning
      ↓
Value
      ↓
Planning
      ↓
Agents
      ↓
Action
```

The essential transition is:

``` text
Perception
    ↓
Prediction
    ↓
Simulation
    ↓
Planning
    ↓
Action
```

That is the main reason to study **World Models**.

------------------------------------------------------------------------

## References

-   Ha, D. & Schmidhuber, J. --- *World Models* (2018)\
    https://arxiv.org/abs/1803.10122

-   Assran et al. --- *Self-Supervised Learning from Images with a
    Joint-Embedding Predictive Architecture* (I-JEPA, CVPR 2023)\
    https://arxiv.org/abs/2301.08243

-   Schrittwieser et al. --- *Mastering Atari, Go, Chess and Shogi by
    Planning with a Learned Model* (MuZero, Nature 2020)\
    https://www.nature.com/articles/s41586-020-03051-4

-   Hafner et al. --- *Mastering Diverse Domains through World Models*
    (DreamerV3, 2023)\
    https://arxiv.org/abs/2301.04104

-   Meta AI --- *V-JEPA 2*\
    https://ai.meta.com/blog/v-jepa-2-world-model-benchmarks/

-   Gymnasium --- Reinforcement Learning environments\
    https://gymnasium.farama.org/
