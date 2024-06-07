---
layout: post
title: "Beginning the coding period"
subtitle: "Stage 1 : Requirements Gathering and Preliminary Design"
date: 2024-06-06
background: '/img/posts/gsococtave.png'
tags: gsoc, llm, ai
---

## Evaluating Frameworks and Models for LLM-Based Agent Systems

### Introduction

In my journey to design an effective framework for LLM-based agent systems, I evaluated various frameworks and models. Here’s a comprehensive look at my evaluation process, the inspirations behind my design, and the final choices I made.

### Inspirations for LLM-Assisted Agent-Based Modeling

I drew significant inspiration from several agent-based models. These models, although not directly usable out of the box, provided valuable insights into design and implementation strategies.

#### Humanoid Agents
- Built from scratch.
- Uses GPT-3.5 turbo with a 16k context length.
- Memory stored as local strings only.

#### Generative Agents
- Uses GPT-3.5.
- Addresses challenges in Memory & Retrieval, Observation-Reflection, and Planning Strategies.
- Implements a Memory Stream for comprehensive experience records.

#### SABM
- Built from scratch.
- Uses GPT-4 with a 128k context length.
- Memory stored in local variables.

### Out-of-the-Box Frameworks

For practical implementation, I considered frameworks that are readily usable and come with comprehensive documentation and support.

#### MetaGPT

| `Pros`                                              | `Cons`                                          |
|---------------------------------------------------|-----------------------------------------------|
| Built for specialized software engineering tasks | Lacks inbuilt vector stores                   |
| Can be generalized for other tasks               | Limited support for states                    |
| Pip installable package                          |                                               |
| Comprehensive documentation                      |                                               |
| Supports local LLMs using Ollama                |                                               |
| Features like shared global memory               |                                               |


#### Langchain

| `Pros`                                                         | `Cons` |
|--------------------------------------------------------------|------|
| Supports time-weighted vector store retriever based on Generative Agents |      |
| Supports local LLMs using Ollama, llama.cpp, and llamafile |      |
| Comprehensive documentation                                 |      |
| Many features implemented like shared global memory, agent API, RAG, Reflection, various planning strategies including ReAct, CoT |      |

<br>

### Local LLM Execution Engines

For running models locally, I evaluated the following execution engines:
#### Llamafile

| `Pros`                                              | `Cons`                                            |
|---------------------------------------------------|-------------------------------------------------|
| Supports running open-source LLMs locally        | Integration support is lesser compared to Ollama |
| Configurable model weights and data               |                                                 |

#### Ollama

| `Pros`                                                         | `Cons`                                      |
|--------------------------------------------------------------|-------------------------------------------|
| Supports running open-source LLMs like Llama 2 and Mistral locally | Needs a hosted environment to run        |
| Optimizes setup and configuration details, including easy local GPU usage |                                   |

<br>

### Issues to be addressed

During my evaluation, I noted the following issues that would have to be addressed:
- Llama3b and Mistral7b have an 8k context length, which might be insufficient for my needs.
- High API usage costs of GPT-3.5 and GPT-4 (with their 128k context lengths) exceed my project scope.
- This necessitates exploring open-source local execution alternatives.

### Decision: Langchain and Ollama

After thorough evaluation, I decided on Langchain as my framework and Ollama for local LLM execution.

- **Langchain**: Provides extensive support for memory management, local LLMs, and features like RAG, making it ideal for my project.
- **Ollama**: Bundles model weights, configuration, and data, optimizing GPU usage and working seamlessly with Langchain.

## Preliminary Design and Implementation

### Metrics for Open Source Community Sustainability

To sustain an open-source community, I studied the following factors based on the materials provided :

1. **Bug Tracker Activity**: Indicates user engagement.
2. **Commit Diversity**: Shows collaborative effort.
3. **Organizational Diversity**: Reflects long-term sustainability.
4. **Discussion Forums**: Essential for community interaction.
5. **News, Announcements, and Releases**: Signify active maintenance.

**Funding Methods**:
- **Crowdfunding**: Suitable for initial launches, not ongoing maintenance.
- **Bounties**: Good for specific tasks but not sustainable long-term.

### Framework Overview

I aim to build a framework around simulating an open-source environment, encompassing various elements such as GitHub (for code repositories), IRC (for communication), and incentivized mentorship programs to attract and retain contributors. The goal is to model the interactions and productivity of agents with varying levels of experience, knowledge, and engagement.

#### Preliminary Design Diagram 

The following design diagram illustrates the planned structure and flow of the simulation framework for simulating open-source community sustainability using large language models (LLMs).

<p align="center"><img src="https://i.ibb.co/B49dr21/Design-Diagram-for-Open-Source-Community-Sustaibility-using-LLMs.jpg" alt="Design-Diagram-for-Open-Source-Community-Sustaibility-using-LLMs"/>Preliminary Design Diagram for Open Source Community Sustaibility using LLMs</p>


#### Key Components and Variables

**1. Environment:**
- **GitHub (CodeSpace Environment):** A virtual code space where agents contribute to open-source projects, mirroring real-world GitHub repositories.
- **IRC (Internet Relay Chat):** A communication platform where agents interact, share information, and seek help. Each agent’s IRC status is modeled using flags to ensure communication between active members.
- **Incentivized Mentorship Programs:** Designed to attract new contributors and provide guidance to novices, enhancing their growth and retention within the community.

**2. Environment Variables:**
- **Issues:** Classified from "Experienced" to "Good-first", issues are assigned based on the agent’s experience level. 
- **Code Productivity:** Measured by the number of commits, pull requests, issue resolutions, and overall code quality.
- **Community Metrics:** Includes stars, forks, contributions, and engagement metrics to track community health and participation.
- **Resource Availability:** Represents computational resources, funding, and mentorship availability.

**3. Agents:**
- **Domain Knowledge:** Understanding of open-source guidelines and project-specific knowledge.
- **Coding Ability:** Skill level in writing and reviewing code.
- **Memory and Learning:** The agent’s ability to remember past interactions and learn from them.

**4. Agent Variables (Internal States):**
- **Experience Level:** Classified as Novice, Contributor, or Maintainer.
- **Knowledge:** The agent's understanding of the project and coding standards.
- **Engagement Level:** The degree of active participation in the community.
- **Motivation:** The drive to contribute and engage.
- **Commitment:** The willingness to stay involved over time.

#### Personalization (Subtask)
- **Backstory:** Initializes agent variables to provide a subjective starting point.
- **IRC Chat History:** Shared information from past interactions to simulate ongoing conversations and relationships.
- **Conversation:** Represents real-time interactions within the IRC.

#### Agent-Environment Communication

The interaction between agents and the environment follows a structured flow:
1. **Issue Assignment:** Issues are assigned based on the agent’s experience level.
2. **Code Writing and Review:** Agents write and review code, iterating until the code meets the project’s standards.
3. **Acceptance by Maintainer:** Maintainers review and accept the code contributions.
4. **State Updates:** Agent states (e.g., experience level, knowledge) are updated based on their activities and outcomes.
5. **Metrics Updates:** Code productivity and community metrics are updated accordingly.

#### Agent-Agent Communication (Subtask)

Agents also communicate with each other through IRC:
1. **IRC Engagement:** Agents engage in conversations, seek help, and collaborate.
2. **Conversation Updates:** New interactions are logged and influence agent states.
3. **State and Metrics Updates:** The communication impacts community metrics, resource availability, and agent states.

#### End Condition

The simulation runs until one of the following conditions is met:
- **All Issues Resolved:** All assigned issues have been successfully addressed.
- **Fixed Number of Iterations:** The simulation reaches a predefined number of iterations.

#### Sustainability Metrics

Throughout the simulation, I hope to continuously track sustainability metrics to gauge the health of the open-source community and visualize them using graphs. These metrics include:
- **Community Growth:** Measured by the number of new contributors and their retention rates.
- **Productivity Trends:** The overall productivity of the community, reflected in the number of contributions and their quality.
- **Engagement Levels:** The degree of active participation and collaboration within the community.

## Initial Steps: Fine-Tuning Models

After the design, I began developing code to fine-tune models using prompt tuning, adhering to the preliminary requirements.

**Requirements**:
1. Code Ability
2. Code Review
3. Context Understanding
4. Memory Management

**Testing Code Ability**:
- Creating a "Contributor" Agent to perform actions like summarizing GitHub issues, planning, writing code, and tests.

**Testing Conversation Ability**:
- Utilizing RAG for understanding context.

## Conclusion : Summary of the Work done so far 

The design of this simulation framework was a significant step towards understanding the dynamics of open-source communities. By modeling various aspects such as agent communication, issue resolution, and productivity metrics, I aimed to identify strategies that can enhance community sustainability. Future iterations of this project will refine the agent behaviors and introduce more sophisticated metrics to provide deeper insights. 

Also, by evaluating various frameworks and models, I was able to arrive at the the decision to use Langchain and Ollama. I have started implementing initial tests to evaluate agents' code ability and conversation ability, focusing on context understanding and memory management.
