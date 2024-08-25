---
layout: post
title: "GSoC '24 @ OREL | INCF : Project Report"
subtitle: "From Concepts to Completion"
date: 2024-08-24
background: '/img/posts/gsocorel.png'
tags: gsoc, llm, ai
---

# Open Source Community Sustainibility using LLMs
<br>

### Important Links for the project

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-0pky{border-color:inherit;text-align:left;vertical-align:top}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky"><b>Topic</b><br></th>
    <th class="tg-0pky"><b>Links</b></th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky">Code Changes</td>
    <td class="tg-0pky"><a href="https://github.com/OREL-group/GSoC/pulls?q=is%3Apr+author%3Asarrah-basta+is%3Aclosed"><span style="color:#905">Merged Pull Requests</span></a></td>
  </tr>
  <tr>
    <td class="tg-0pky">Documentation & Blogs</td>
    <td class="tg-0pky"><a href="https://medium.com/orel-group"><span style="color:#905"> Blogs published to Organization's Medium Page </span></a></td>
  </tr>
  <tr>
    <td class="tg-0pky">Code Repository</td>
    <td class="tg-0pky"><a href="https://github.com/OREL-group/GSoC/tree/main/Open%20Source%20Sustainibility%20using%20LLMs"><span style="color:#905">OREL Group's GSoC Repository</span></a></td>
  </tr>
</tbody>
</table>
<br>

--

##  A bird's eye overview


My project was [Open Source Community Sustainability](https://summerofcode.withgoogle.com/programs/2024/projects/XMUiHWHO), and as the name suggests revolved around simulating open-source community interactions to encourage conditions leading to sustainable practice. I proposed to do this by creating a framework with an agent-based model powered by Large Language Models (LLMs), contnuing the work done in OREL for this project earlier [GSoC '22 and GSoC '23](https://github.com/OREL-group/GSoC), and focused on a collaborative
approach, integrating **system and agent-based modeling elements** and leveraging **large language models
(LLMs)** to simulate open-source communities more accurately, aiming for enhanced sustainability and
community engagement.<br>

## Need for the project
Need for the Project
As the demand for open-source software continues to grow, so do the challenges related to community management, collaboration, and sustainability. This project addresses these challenges by utilizing Large Language Models (LLMs) to create realistic simulations of open-source communities. According to Xi et al. (2023) and Shanahan et al. (2023), LLM-based agents possess advanced reasoning, decision-making, and adaptive learning capabilities, allowing them to act as intelligent entities within these simulations. By integrating these agents, the project aims to map the complexities of community engagement and collaboration, focusing on emergent properties and overall system behavior. This framework is essential for understanding how individuals contribute to community growth and success, ultimately offering insights into effective community management, collaboration strategies, and sustainability within the open-source ecosystem.

## The Final Framework : LLAMOSC

> *As it is often said, reusability is not just about code. It’s about creating solutions that endure.*

Meaning, instead of just a Python script to run a simulation for my project, I wanted to build a framework, to create a sustainable solution that can be leveraged across multiple projects.

And hence, I created LLAMOSC.

### LLAMOSC : LLM-Powered Agent-Based Model for Open Source Communities

**LLAMOSC** (LLM Agent-Based Modeling for Open Source Communities), a cutting-edge framework designed to simulate and sustain open-source communities using advanced Large Language Models (LLMs) and Agent-Based Modeling (ABM) techniques.

The name encapsulates the core components and objectives of the framework:

- **LLM**: Large Language Models, the backbone of our intelligent agents, providing them with advanced perception, reasoning, and decision-making capabilities.
- **ABM**: Agent-Based Modeling, the methodological approach we use to simulate the interactions and dynamics within open-source communities.
- **OSC**: Open Source Communities, the focus of our simulations, aiming to enhance sustainability, collaboration, and engagement within these communities.

> So, how exactly will LLAMOSC work?

The following is a diagram for the preliminary design I created for this project illustrating the planned structure and flow of the simulation framework for simulating open-source community sustainability using large language models (LLMs).

<p align="center"><img src="https://i.ibb.co/B49dr21/Design-Diagram-for-Open-Source-Community-Sustaibility-using-LLMs.jpg" alt="Design-Diagram-for-Open-Source-Community-Sustaibility-using-LLMs"/>Preliminary Design Diagram for Open Source Community Sustaibility using LLMs</p>

### Key Components and Features implemented so far

1. **Environment**:
    - **GitHub (CodeSpace Environment)**: A simulated code space (powered by [AutoCodeRover](https://github.com/nus-apr/auto-code-rover)) where agents contribute to open-source projects, mirroring real-world GitHub repositories.

2. **Environment Variables**:
    - **Issues**: Issues are categorized with varying difficulty from 1 to 5, resembling real-life variation from "Good First Issues" aka Earth-Shot Issues to "Expert Required Issues" aka Mars-Shot Issues, assigned based on the contributor agent's experience level.
    - **Code Quality** : Introduced a metric to track the average code quality of pull requests. Code quality is measured and updated at each time step, with its trend visualized to assess improvements or declines over time.

3. **Agents**:
    - **Coding Ability**: Skill level in writing and reviewing code, (powered by AutoCodeRover), impacting their effectiveness in solving issues and contributing to code quality.
    - **Role Description**: Personalization through role_description provides each agent with a unique backstory, affecting their initial motivation level and behavior during GitHub discussions.

4. **Agent Variables (Internal States)**:
    - **Experience Level**: Categorized from 1 to 5, reflecting real-life contributors ranging from Novice to Experienced. The experience level is tracked and plotted over time to show growth or changes for each contributor.
    - **Motivation Level**: Initial motivation is influenced by the role description and backstory provided via LLM-generated system messages. This level changes based on bid acceptance/rejection, success in assigned issues, and code quality reviews of generated pull requests.
    

### Framework Capabilities

LLAMOSC integrates multiple capabilities to automate and optimize open-source community activities:

1. **AutoCodeRover Integration**:
    - Combines LLMs with advanced code navigation and debugging capabilities.
    - Operates in two stages: Context retrieval and Patch generation.
    - Features Program Structure Aware code search APIs to navigate codebases and generate patches.

2. **Automated Pull Request Lifecycle**:
    - **ContributorAgent**: Automates issue identification, solution proposal, and pull request creation.
    - **MaintainerAgent**: Automates pull request review and merging based on predefined criteria.
    - Utilizes Docker for environment consistency and isolation.
    - Implements diff extraction and automated pull request submission (powered by AutoCodeRover).

3. **Multi-Agent Decision Making Algorithms**:
    - **Authoritarian Algorithm (Benevolent Dictator Model)**:
        - Central maintainer manages and allocates tasks.
        - Contributors are rated and assigned tasks based on maintainer’s discretion.
    - **Decentralized Algorithm (Meritocratic Model)**:
        - Distributed decision-making among contributors.
        - Contributors bid on tasks based on their suitability and experience.

4. **Metrics and Trends Visualization**:
    - Visualizes different metrics throughout the simulation at each time step.
    - Includes **tracking** of contributor experience levels, code quality trends, and motivation levels to provide comprehensive insights into the dynamics of the open-source community.

### Accomplishments of LLAMOSC so far

- **Preliminary Design and Implementation**:
    - Created a robust framework for simulating open-source environments.
    - Developed agents with varying skill levels and experience to reflect real-world dynamics.

- **Automated Pull Request Lifecycle**:
    - Streamlined the process of identifying, solving issues, creating, submitting, reviewing, and merging pull requests.
    - Implemented reliable and reproducible workflows for ContributorAgent and MaintainerAgent.

- **Multi-Agent Decision Making**:
    - Simulated governance models used in open-source projects.
    - Developed algorithms for both centralized (authoritarian) and decentralized (meritocratic) task allocation.

- **Graphical User Interface**
    - Intuitive graphical interface for input and visualization of metrics updates within the LLAMOSC framework.

    <p align="center"><img src="https://i.imgur.com/yhiQWzR.png" alt="GUI Simulation Input"/></p>

    <p align="center"><img src="https://i.imgur.com/n3l6ZJJ.png" alt="GUI Simulation Window"/>Graphhical user Interface Simulation Window</p>

### Future Improvements <br>

- **Add Collaboration Algorithm for Multiple Agents on a Single Issue**
  Enhancing the collaboration algorithm will enable multiple agents to work together on a single issue, improving task efficiency and accuracy. This improvement is needed to simulate complex teamwork dynamics and better reflect real-world open-source project workflows.  
  [Link to Issue](https://github.com/OREL-group/GSoC/issues/64)

- **Use IssueCreatorAgent to Allow Dynamic Creation of New Issues During Simulation Instead of Just at the Start**   
  Implementing IssueCreatorAgent will enable the dynamic creation of issues throughout the simulation, providing a more realistic and flexible environment. This change is necessary to simulate the evolving nature of open-source projects where new issues are frequently introduced.  
  [Link to Issue](https://github.com/OREL-group/GSoC/issues/63)

- **Add ConversationSpace (to Simulate Slack)**  
  Implementing ConversationSpace to simulate Slack will provide a more accurate representation of team communication and collaboration within the simulation. This addition is necessary for capturing the nuances of real-time, informal communication in open-source projects.  
  [Link to Issue](https://github.com/OREL-group/GSoC/issues/60)
  
- **Integrate RAG within ConversationSpace and GithubDiscussion**  
  Integrating Retrieval-Augmented Generation (RAG) will enhance the interaction capabilities within ConversationSpace and GitHub Discussions by improving information retrieval and context-aware responses. This integration is crucial for more accurate and relevant agent interactions in discussions.  
  [Link to Issue](https://github.com/OREL-group/GSoC/issues/62)

- **Add Engagement Metrics Based on ConversationSpace**  
  Introducing engagement metrics will allow for the measurement of interaction quality and participant involvement within ConversationSpace. This feature is important for analyzing communication patterns and optimizing collaboration strategies.  
  [Link to Issue](https://github.com/OREL-group/GSoC/issues/61)

## Acknowledgements

> After my exhilirating journey of `GSoC @ INCF` in the past few months, I am proud of a lot of things, but mostly of myself, for having been able to absorb even tiny bits from the vast knowledge of my mentors and peers, this priceless experience of each up, down, error and success in the lines of code written by me giving me much to look forward to in a future career in development, research and open-source.

I am grateful and extremely thankful to the entire community at Orthogonal Research Lab, with everyone from experienced researchers  to even fellow GSoC contributors never hesitant to contribute to every discussion, ranging from interdisciplinary research, to building framework and debugging errors. I have carried out most of the discussion throughout my project at the [Weekly meets uploaded on youtube](https://www.youtube.com/watch?v=D3Q9Eo22Ua0&list=PL4RJ4xCetB61jIwYV_nY_Iqg4lm8pxN5j&pp=iAQB) and am truly amazed at the inclusivity and collaborative spirit of everyone here, making my deep-dive into open-source and research an extremely cherished experience!
<br>

I hope to keep contributing to this project as well as Orthogonal Research Lab.

### References

- Shanahan, M., McDonell, K., & Reynolds, L. (2023, May 25). Role-Play with large language models. [arXiv.Org](https://arxiv.org/abs/2305.16367)
- Xi, Z., Chen, W., Guo, X., He, W., Ding, Y., Hong, B., Zhang, M., Wang, J., Jin, S., Zhou, E., Zheng, R., Fan,
X., Wang, X., Xiong, L., Zhou, Y., Wang, W., Jiang, C., Zou, Y., Liu, X., … Gui, T. (2023, September 14). 
The rise and potential of large language model based agents: A survey. [arXiv.Org](https://arxiv.org/abs/2309.07864)
- The CodeSpace Environment is based on a paper "AutoCodeRover: Autonomous Program Improvement" and it's open-source code implementation [on github](https://github.com/nus-apr/auto-code-rover) has been used as a dependency in my project.
- For [decentralized](https://python.langchain.com.cn/docs/use_cases/agent_simulations/multiagent_bidding) and [authoritarian](https://python.langchain.com.cn/docs/use_cases/agent_simulations/multiagent_authoritarian) algorithm approaches : [Langchain Use Cases - Agent Simulations](https://python.langchain.com.cn/docs/use_cases/agent_simulations/)
- For information about [benevelont-dictator](http://oss-watch.ac.uk/resources/benevolentdictatorgovernancemodel) and [meritocratic](http://oss-watch.ac.uk/resources/meritocraticgovernancemodel) open-source goverenance models : [http://oss-watch.ac.uk/resources/](http://oss-watch.ac.uk/resources/) 
