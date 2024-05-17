---
layout: post
title: "Multi Agent Systems : From Reinforcement Learning to Large Language Models"
subtitle: "A review of exiting literature conducted for my upcoming GSoC Project at Orthogonal Research and Education Lab"
date: 2024-05-16
background: '/img/posts/gsococtave.png'
tags: gsoc, llm, ai
---

# Understanding Multi-Agent Systems : The Need for Collaboration

Imagine a team of robots, each with its own job. They're like players on a soccer field, working together to score goals that none could achieve alone. In multi-agent systems (MAS), these autonomous agents must communicate and work in harmony to tackle tasks too big for any single one.

In such setups, coordination is key—it's the glue holding everything together. Agents have no playbook or past examples to rely on; they need to figure things out as they go along. Whether it’s conducting complex experiments or something as simple as making dinner, every agent constantly evaluates how best to contribute their skills.

They decide not only what their next move will be but also who in the group would be the best partner for each task at hand—like choosing whom you'd pass the ball during a crucial moment of a game! This ability to adapt and coordinate smoothly is vital in any world where multiple agents interact.

Multi-agent systems (MAS) refer to a collective of autonomous agents that interact and collaborate to achieve goals that are often beyond the capabilities of individual agents. In such systems, agents must coordinate their actions, negotiate roles, and dynamically adapt to new situations to work together efficiently. This coordination is crucial, especially in environments where there is no prior experience or established social norms to guide behavior. These fundamental challenges highlight the importance of flexible coordination in any multi-agent world.

# Existing Techniques on MultiAgent Collaboration


## Paper 1 : A probabilistic approach with Reinforcement Learning

<p align="center"><i>Too many cooks: Bayesian inference for coordinating multi-agent collaboration</i></p>

The first paper I read on understanding multi-agent systems relies almost exclusively on the most basic principle used throughout the domain of artificial intelligence : probability. In this paper, the authors simulate **theory-of-mind** (the ability of humans to infer the hidden mental states that drive others to act) using **Bayesian Delegation**, an algorithm developed by them that helps artificial agents infer the hiddens of others by *inverse planning*. 

#### The goal and achieving it with Multi-Agent MDPs
The test suite used in the paper as the goal, inspired by Overcooked, is to evaluate multi-agent collaboration in 2D grid-world kitchens where **agents (chefs) work together to cook a recipe efficiently within a limited number of time steps**. The problem is modeled as a multi-agent Markov decision process (MMDP) where each state is defined by the 1. locations, 2. statuses, and 3. types of all objects and agents, and the **transition function** (which describes the probability of moving from one state to another based on these combined actions) depends on the joint actions of all agents.

This MMDP is defined as `<n, S, A{1..n}, T, R, γ, θ>` , where `n` is the number of agents, `S` is the state space, `A{1..n}` where `i goes from 1 to n` is the joint action space where each agent chooses its actions individually, `T(s, a{1..n}, s')` is the transition function which describes the probability of going from state `s` to `s'`after all agents after completed their actions a{1..n}, `R(s, a{1..n})` is the reward function shared by all agents, γ is the discount factor, and `θ` is a partially ordered set of subtasks `θ{i}`.  


#### How is Co-ordination Achieved?

The authors first decompose the task into sub-tasks, easily achieved in the givn test suite as "steps in a recipe". These sub-tasks are the target of high-level co-ordination between agents. Then, the agents need to choose one of two approaches for each sub-task, namely :
1. Divide & Conquer
2. Collaborate

For alignment on the order and execution of these sub-tasks, as well as choice of strategy, the authors bring in **Bayesian Delegation** to achieve coordination. 

<p align="center"><img src="https://i.ibb.co/F3yRVwJ/bayesian-delegation-figure-5.jpg"/>Bayesian Delegation : Dynamics of the Belief State</p>

To do so the authors use **belief states**. Each of the agents has a belief about what tasks everyone is likely working on, based on what they've observed. This belief is like a guess, and it's updated as they see more actions from each other. The above figure shows how these beliefs change over time, and alignment across the ordering of sub-tasks as well as within each sub-task.

Each sub-task has pre- and post- conditions. In the initial time-steps, only tasks whose preconditions are met are non-zero. Over time, as more and more tasks get completed and preconditions for other tasks are met, they too become nonzero.

At each step, agents select the **most likely task allocation** based on what they've observed so far, and **plan** their next actions accordingly. They use a mathematical formula to calculate the posterior probability using Bayes rule, considering both their own actions (<u><i>prior knowledge</i></u>) and what others might be doing (<u><i>likelihood of observed actions</i></u>). This helps them plan their next action.

Thus, Bayesian Delegation allows agents to coordinate without explicit communication, relying instead on a shared history and probabilistic inference. This approach not only helps in avoiding conflicts when working on separate tasks (<u><i>Divide and Conquer Strategy</i></u>) but also fosters efficient cooperation when tasks overlap (<u><i>Coordination Strategy</i></u>). 

The concept of belief states in Bayesian Delegation allows agents to probabilistically infer and coordinate task allocations, enhancing the efficiency and adaptability of future multi-agent systems in dynamic and collaborative environments.

## Paper 2 : Enter Large Language Models for their ability to code 

<p align="center"><i>Communicative agents for software development</i></p>

<p align="center"><img src="https://d3i71xaburhd42.cloudfront.net/4747e72c5bc706c50e76953188f0144df18992d0/4-Figure2-1.png" width="600" height="230"/> ChatDev</p>

In the realm of collaborative multi-agent systems, the ChatDev paper presents an innovative methodology leveraging Large Language Models (LLMs) to enhance software development processes. This approach addresses the inherent challenges of generating entire software systems using LLMs, such as code hallucinations, incomplete implementations, and missing dependencies, by establishing a virtual chat-powered software technology company.

#### Why does Software require a Multi-Agent System Architecture?
The core innovation of ChatDev lies in its structured, collaborative framework, which mitigates common pitfalls in LLM-generated software development. The virtual company comprises diverse agents representing various roles such as chief officers, professional programmers, test engineers, and art designers. This diverse team collaborates to develop complete software systems, environmental guidelines, and user manuals. By using LLMs as the core thinking component, ChatDev simulates the entire software development process **without the need for additional model training**.

#### Collaboration Techniques : Chat Chain and the Waterfall Model
ChatDev employs the <u><i>waterfall model</i></u> to divide the development process into four distinct phases: `designing`, `coding`, `testing`, and `documenting`. Each phase is further broken down into multiple atomic chats, focusing on specific tasks and involving **role-playing** between two distinct agents. This structured dialogue ensures clear communication and effective collaboration among agents.

1. **Designing:** Innovative ideas are generated, and technical design requirements are defined through collaborative brainstorming.
2. **Coding:** Source code is developed and reviewed, with thought instructions guiding agents to address unimplemented methods and avoid code hallucinations.
3. **Testing:** All components are integrated, and feedback from interpreters is used for debugging, promoting iterative improvement.
4. **Documenting:** Environment specifications and user manuals are generated, ensuring comprehensive project documentation.

#### Multi-Agent Collaboration via Natural Language Chat
Effective communication among agents is crucial for successful collaboration. The **chat chain** mechanism facilitates this by breaking down complex tasks into manageable atomic chats, where agents exchange <u><i>instructions</i></u> and collaborate until the task is completed. This approach provides transparency in the decision-making process and allows for debugging and intervention when necessary.

Thus, coordination and collaboration in ChatDev is achieved through role specialization and structured dialogues. Each agent is assigned specific **roles and responsibilities**, ensuring that they contribute effectively to the task at hand. The `memory stream` or shared history maintains a comprehensive record of previous dialogues, enabling agents to make informed decisions. Additionally, the `self-reflection` mechanism prompts agents to review and reflect on their decisions, ensuring accuracy and consistency in their outputs.

#### Why LLMs

Using LLMs in ChatDev offers several advantages:

1. **Contextual Understanding:** LLMs provide contextually appropriate dialogue and behavior generation, enhancing the realism and effectiveness of agent collaboration.
2. **Reduced Hallucinations:** By simulating the entire development process and employing thought instructions, ChatDev minimizes code hallucinations, leading to more reliable software outputs.
3. **Adaptability:** LLMs enable agents to dynamically adjust their behaviors based on past experiences and current states, ensuring that actions are contextually relevant and goal-oriented.

By leveraging the advanced capabilities of LLMs, ChatDev demonstrates a powerful methodology for simulating and enhancing collaborative multi-agent systems in software development. This approach not only improves the realism and effectiveness of agent interactions but also opens new possibilities for research and application in environments requiring human-like cooperation.

### Paper 3: LLM Based Agents to Simulate Human Behaviors
<p align="center"><i>Humanoid Agents - Platform for Simulating Human-like Generative Agents</i></p>


<p align="center"><img src="https://github.com/HumanoidAgents/HumanoidAgents/raw/main/img/front_page.png" width="600" height="400" /> Humanoid Agents</p>

The paper on Humanoid Agents introduces a groundbreaking approach to collaborative multi-agent systems by utilizing Large Language Models (LLMs) to simulate human-like generative agents. This methodology enhances our understanding and implementation of complex, collaborative behaviors among agents, closely mimicking **real-world human interactions**. The core innovation lies in the use of LLMs, to generate realistic agent behaviors and interactions, enabling nuanced and adaptive collaboration.

#### The starting point : Agent Initialization & Metrics
Here, each agent is endowed with detailed attributes, including a name, age, daily routine, personality traits, and an initial emotional state. For instance, an agent might be initialized as `John Lin, a friendly pharmacy shopkeeper`, with <u><b>basic needs</u></b> such as `fullness` and `social interaction` set to mid-levels. This setup ensures that agents start with <u>realistic, relatable profiles</u> that influence their subsequent behaviors and decisions.

#### Planning : The use of LLMs with Internal States
Utilizing the ability of Large Language Models to understand, process and generate natural language fully in the realm of **Activity Planning**, the platform adopts a sophisticated approach where agents' daily plans are meticulously crafted and broken down into smaller intervals. This allows agents to adapt their activities based on changes in <u>their internal states</u>, such as `emotions` and `basic needs`. For example, if an agent becomes very hungry while planning to eat much later, it might opt for a snack to maintain its functionality, demonstrating flexibility and real-time decision-making.

#### Dialogue, Discourse and its effect on Multi-Agent Collaboration
**Dialogue Generation** plays a pivotal role in fostering collaboration among agents. Agents determine whether to engage in conversations based on a variety of factors, including their `personality traits`, `current activities`, and the `closeness of their relationships` with other agents. 

These interactions are not merely superficial; they significantly impact agents' `emotional states` and `social bonds`. A positive conversation can lead to stronger relationships, enhancing <b><u>cooperative efforts in future tasks.</u></b>

The use of LLMs provides significant advantages in creating these generative agents. LLMs allow for <u>rich, contextually appropriate dialogue</u> and <u>behavior generation</u>, making the agents appear more lifelike and capable of handling complex social interactions. This leads to more authentic and effective collaboration within the multi-agent system. Agents can dynamically adjust their behaviors based on past experiences and current states, ensuring that their actions are both **contextually relevant** and **goal-oriented**.

By focusing on collaborative behaviors and utilizing the advanced capabilities of LLMs, this paper demonstrates a powerful methodology for simulating and studying human-like interactions in multi-agent systems. The approach not only enhances the realism and effectiveness of agent collaboration but also opens new avenues for research and application in environments where human-like cooperation is essential.

## References 

1. Too many cooks: Bayesian inference for coordinating multi-agent collaboration. Topics in Cognitive Science, 13, 414–432. [https://doi.org/10.1111/tops.12525](https://doi.org/10.1111/tops.12525) 

2. Communicative agents for software development. arXiv.Org. [https://arxiv.org/abs/2307.07924](https://arxiv.org/abs/2307.07924) 


3. Humanoid agents: Platform for simulating human-like generative agents. Proceedings of the 2023 Conference on Empirical Methods in Natural Language Processing: System Demonstrations. [http://dx.doi.org/10.18653/v1/2023.emnlp-demo.15](http://dx.doi.org/10.18653/v1/2023.emnlp-demo.15)
