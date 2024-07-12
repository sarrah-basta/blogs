---
layout: post
title: "Midpoint Milestone : Introducing the LLAMOSC Framework"
subtitle: "Stage 3 : Bringing it all together"
date: 2024-07-12
background: '/img/posts/gsocorel.png'
tags: gsoc, llm, ai
---

# LLAMOSC (LLM Powered Agent Based Model for Open Source Communities) : A Framework for Simulating Sustainable Open Source Communities

## Introduction

We're halfway through the project, and I’m thrilled to share the progress I've made in developing a framework for simulating and enhancing open-source communities using Large Language Models (LLMs). Over the past few weeks, I've focused on implementing and refining algorithms for decision-making in multi-agent systems, integrating these components into a modular framework, and ensuring the code is robust and installable. Here’s a peek into what I've been working on and where we're headed next.

## Recap of Previous Work

In the previous phase, the preliminary design phase of the project was completed, resulting in a comprehensive diagram outlining the project architecture and initial code to test the preliminary requirements of the models, serving as a blueprint for the development process and significant progress in the implementation had been made, particularly in creating an environment for our agents and automating the pull request lifecycle.

### Environment Creation

Creating an environment for the agents was a critical step in the project. This environment would enable the agents to interact with and navigate through code repositories, understand the context, and solve issues autonomously.

I focused on researching various approaches and tools to aid in environment creation, such as AutoCodeRover, which provided insights into code representation and manipulation. By implementing AutoCodeRover, I aimed to leverage its capabilities to enhance our agents’ ability to solve issues and navigate complex codebases autonomously.

### Automated Pull Request Lifecycle

The primary focus was to automate the entire pull request lifecycle, from issue identification and solution to the creation, submission, review, and merging of pull requests. This process involved the creation of two essential classes: ContributorAgent and MaintainerAgent. The ContributorAgent is responsible for generating and submitting pull requests, while the MaintainerAgent reviews and merges them.

## Crafting Decision-Making Algorithms 

### The Need of Decision Making Algorithms & Challenges with the Approach so far

**Handling Multiple Eligible Contributors**

Before starting the multi-agent decision-making process, I faced a significant challenge: If multiple eligible contributors are available at the same time, how do we choose who can contribute? Multiple contributions at the same time could lead to merge conflicts. After discussions with my mentor, we decided to implement the Benevolent Dictator and Meritocratic Models for task allocation.

To research these models, I referred to:

- [Langchain Use Cases: Agent Simulations](https://python.langchain.com.cn/docs/use_cases/agent_simulations/) for [decentralized](https://python.langchain.com.cn/docs/use_cases/agent_simulations/multiagent_bidding) and [authoritarian](https://python.langchain.com.cn/docs/use_cases/agent_simulations/multiagent_authoritarian) algorithm approaches. While they are not about simulating an open-source community, but rather about a debate between LLM-powered agents, they gave a good starting to understand and build my own code off of.
- [OSS Watch Resources](http://oss-watch.ac.uk/resources/) for information about [benevolent-dictator](http://oss-watch.ac.uk/resources/benevolentdictatorgovernancemodel) and [meritocratic](http://oss-watch.ac.uk/resources/meritocraticgovernancemodel) open-source governance models.

**Determining Bid/Rating Criteria**

Another challenge was deciding the basis for maintainers' ratings or contributors' bids. After a discussion with my mentor, we decided to simulate a GitHub discussion, similar to what happens in the comments of a GitHub issue. This approach introduced inter-agent communication, making the simulation more realistic.

### The Solution

To address the concerns above, I focused my attention on creating two decision-making algorithms inspired by governance models commonly seen in open-source projects: the Benevolent Dictator Model and the Meritocratic Model. These algorithms simulate how issues are assigned to contributors within an open-source project using LLMs.

#### Benevolent Dictator Model (Authoritarian Algorithm)

The Benevolent Dictator Model hinges on a central maintainer who allocates tasks to contributors. Here’s a rundown of how it works:

1. **Eligibility Check**: Filtering contributors based on their availability and experience.
2. **Discussion Simulation**: Using LLMs to simulate a GitHub-style discussion among eligible contributors.
3. **Rating**: The maintainer rates each contributor based on their discussion comments and experience.
4. **Selection**: The highest-rated contributor is chosen.
5. **Task Allocation**: The task is assigned to the selected contributor, and task completion is simulated.

#### Meritocratic Model (Decentralized Algorithm)

The Meritocratic Model offers a more democratic approach, where contributors gain influence through their contributions. Here’s how this model operates:

1. **Eligibility Check**: Filtering contributors based on their availability and experience.
2. **Discussion Simulation**: Using LLMs to simulate a GitHub-style discussion among eligible contributors.
3. **Bidding**: Contributors bid on their suitability for the issue.
4. **Selection**: The highest bidder is selected.
5. **Task Allocation**: The task is assigned to the selected contributor, and task completion is simulated.

#### Key Differences and Common Features

The main difference between these models lies in the decision-making process: the Benevolent Dictator Model is centralized, while the Meritocratic Model is decentralized. Both models feature proportional bidding/rating to encourage contributors to attempt tasks that match their potential, adding a layer of realism to the simulation.

## Bringing It All Together
Once completed, I focused on integrating the components I had developed into a cohesive and modular framework, now known as LLAMOSC (LLM-Powered Agent-Based Model for Open Source Communities). This framework aims to simulate and enhance the sustainability of open-source communities through advanced LLMs.

### Key Components and Features integrated in the framework so far

1. **Environment**:
    - **GitHub Simulation**: A simulated code space where agents contribute to open-source projects.
    - **Issue Categorization**: Issues are categorized by difficulty, mirroring real-world variations.

2. **Agents**:
    - **Coding Ability**: Agents have varying skill levels in writing and reviewing code.
    - **Experience Level**: Agents range from novice to experienced contributors.

3. **Framework Capabilities**:
    - **AutoCodeRover Integration**: Combining LLMs with code navigation and debugging capabilities.
    - **Automated Pull Request Lifecycle**: Automating issue identification, solution proposal, and pull request creation and review.
    - **Multi-Agent Decision Making**: Implementing both authoritarian and decentralized task allocation algorithms.

### Achievements in the project so far

- **Framework Design and Implementation**: Developed a robust framework to simulate open-source environments.
- **Automated Processes**: Streamlined the entire pull request lifecycle, ensuring efficient and reliable workflows for ContributorAgent and MaintainerAgent.
- **Governance Model Simulations**: Created and tested algorithms for centralized and decentralized decision-making in task allocation.

## Looking back at the proposal

Comparing to the initial proposal, I've successfully implemented core components of the framework, closely aligning with my planned design. The key areas achieved include environment creation, agent implementation, and decision-making algorithms.

<p align="center"><img src="https://i.ibb.co/B49dr21/Design-Diagram-for-Open-Source-Community-Sustaibility-using-LLMs.jpg" alt="Design-Diagram-for-Open-Source-Community-Sustaibility-using-LLMs"/>Preliminary Design Diagram for Open Source Community Sustaibility using LLMs</p>

#### A feature by feature rundown


| `Feature`                         | `Proposed`                                                   | `Achieved`                                                                 |
|---------------------------------|-------------------------------------------------------------|--------------------------------------------------------------------------|
| **Framework Design**                | Develop a modular framework for simulating open-source communities. | Designed and implemented the LLAMOSC framework with modular components, completed automated pull request lifecycle and multi-agent decision making.  |
| **Agent Implementation**            | Develop agents with varying skill levels and experience.   | Implemented ContributorAgent and MaintainerAgent with diverse capabilities. |
| **Environment Creation**            | Simulate a realistic open-source project environment.      | Developed a GitHub-like simulation space with categorized issues.        |
| **Environment**                 | GitHub (CodeSpace Environment), IRC, Incentivized Mentorship | GitHub (AutoCodeRover-powered CodeSpace Environment)                     |
| **Environment Variables**       | Issues (Experienced to Good-first), Code Productivity, Community Metrics, Resource Availability | Issues (1 to 5 difficulty levels), Code Productivity                    |
| **Agents**                      | Domain Knowledge, Coding Ability, Memory and Learning        | Domain Knowledge (Via Prompts), Coding Ability(Powered by AutoCodeRover), Experience Level (1 to 5)          |
| **Agent Variables (Internal States)** | Experience Level, Knowledge, Engagement Level, Motivation, Commitment | Experience Level (Levels 1 to 5) |
| **Personalization (Subtask)**   | Backstory, IRC Chat History, Conversation                    | -                                                                        |
| **Agent-Agent Communication (Subtask)**   | IRC Simulation                   | -                                                                        |

Moving forward, I plan to refine the existing agents with metrics and state updates, enhance the framework’s capabilities, and integrate additional features such as mentorship programs and community metrics tracking. The goal is to create a comprehensive and sustainable simulation model that mirrors real-world open-source community dynamics, ultimately contributing to the growth and health of these communities.

My immediate plan of action consists of :

1. **Integrate Metrics into LLAMOSC Framework:**
   - Incorporate environment variables such as issues (Experienced to Good-first), code productivity metrics (commits, pull requests, resolutions), and agent variables (Experience Level, Knowledge, Engagement Level, Motivation) into the LLAMOSC framework. Develop state change formulas to dynamically update these metrics.

2. **Develop User Interface:**
   - Create a user interface to facilitate agent creation and visualize metric changes throughout simulations. Utilize graphical representations (e.g., graphs, charts) to display the evolving dynamics of issues, productivity, and agent variables during simulation runs.

These steps will enhance the functionality and usability of LLAMOSC, supporting deeper analysis and interaction with simulated open-source community dynamics.
Stay tuned for further updates as I continue to develop and refine LLAMOSC in the second half of the project!

All of the code for this can be found at [the OREL-group GSOC repository](https://github.com/OREL-group/GSoC/tree/350fa3fbc6f3cacf8e019e13f5d1dda8a7f82307/Open%20Source%20Sustainibility%20using%20LLMs).

## References 
- For [decentralized](https://python.langchain.com.cn/docs/use_cases/agent_simulations/multiagent_bidding) and [authoritarian](https://python.langchain.com.cn/docs/use_cases/agent_simulations/multiagent_authoritarian) algorithm approaches : [Langchain Use Cases : Agent Simulations](https://python.langchain.com.cn/docs/use_cases/agent_simulations/)
- For information about [benevelont-dictator](http://oss-watch.ac.uk/resources/benevolentdictatorgovernancemodel) and [meritocratic](http://oss-watch.ac.uk/resources/meritocraticgovernancemodel) open-source goverenance models : [http://oss-watch.ac.uk/resources/](http://oss-watch.ac.uk/resources/) 
- This environment is based on a paper "AutoCodeRover: Autonomous Program Improvement" and it's open-source code implementation [on github](https://github.com/nus-apr/auto-code-rover) has been used as a dependency in my project .
