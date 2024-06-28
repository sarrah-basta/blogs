---
layout: post
title: "The Building Blocks : Bottom Up Approach"
subtitle: "Stage 2 : Beginning the Implementation : Environement and Agent Creation"
date: 2024-06-28
background: '/img/posts/gsocorel.png'
tags: gsoc, llm, ai
---

## Environment Creation : The Need and the Way

### Introduction

In the previous phase, the preliminary design phase of the project was completed, resulting in a comprehensive diagram outlining the project architecture and initial code to test the preliminary requirements of the models. This design serves as a blueprint for the development process. Since then, significant progress has been made, particularly in creating an environment for our agents and automating the pull request lifecycle.

In this blog, I will recap the work I did since then on creating an environment for our agents, focusing on the steps taken, resources utilized, and the challenges faced. This phase is crucial as it lays the foundation for allowing the agents to navigate codespaces and solve issues efficiently.

Creating an environment for the agents was a critical step in the project. This environment would enable the agents to interact with and navigate through code repositories, understand the context, and solve issues autonomously.

### What Are the Resources I Viewed and Why
Initially, I focused on researching various approaches and tools to aid in environment creation ie. allowing them to navigate codespaces and solve issues. During my research, I came across several noteworthy papers:

- **CodeR** ([Paper](https://arxiv.org/pdf/2406.01304)): This paper provided insights into variious agents and their roles in code representation and manipulation, enabling them to understand and interact with code.
- **SWE-Agent** ([Paper](https://arxiv.org/abs/2405.15793)): This paper explored the integration of agents in software engineering tasks, providing valuable context and methodologies.
- **AutoCodeRover** ([Paper](https://arxiv.org/abs/2404.05427)): Among the papers I reviewed, AutoCodeRover stood out as the most applicable to our project. Its approach to navigating and understanding code repositories aligned well with our needs.

After evaluating these resources, I decided that implementing AutoCodeRover with a toy repository would be the best fit for our project. I began working on this implementation, anticipating it would provide a solid framework for our agents.

### Overview of AutoCodeRover and Its Importance in the Project
AutoCodeRover is a system designed to help agents navigate code repositories efficiently. It employs advanced techniques to understand the structure and dependencies within a codebase, making it highly suitable for our project. By implementing AutoCodeRover, I aimed to leverage its capabilities to enhance our agents' ability to solve issues and navigate complex codebases autonomously.

This environment I created is based on the paper "AutoCodeRover: Autonomous Program Improvement" and it's open-source code implementation [on github](https://github.com/nus-apr/auto-code-rover) has been used as a dependency in my project .
- It combines LLMs with analysis and debugging capabilities to prioritize patch locations ultimately leading to a patch.
- AutoCodeRover works in two stages:
- - Context retrieval: The LLM is provided with code search APIs to navigate the codebase and collect relevant context.
- - Patch generation: The LLM tries to write a patch, based on retrieved context.
- AutoCodeRover has two unique features:
- - Code search APIs are Program Structure Aware. Instead of searching over files by plain string matching, AutoCodeRover searches for relevant code context (methods/classes) in the abstract syntax tree.
- - When a test suite is available, AutoCodeRover can take advantage of test cases to achieve an even higher repair rate, by performing statistical fault localization.

#### The Challenges I Faced in Using AutoCodeRover
Implementing AutoCodeRover was not without its challenges. Alhough it was compatible with Ollama and running locally hosted open-source LLMs like LLaMA3, it was only tested to work with GPT-4, and due to resource constraints, I had to adapt it to work with LLaMA3-7B, a significantly smaller model. This required substantial modifications to the prompts and underlying logic.

I spent considerable time debugging these changes, ensuring that the adapted prompts could still deliver effective results despite the limitations of the smaller model. This involved understanding the nuances of prompt engineering and making iterative adjustments. I documented the changes I made to the original repository code at [my-own-fork](https://github.com/sarrah-basta/auto-code-rover-for-llama37b).

#### Making Everything Reproducible
Ensuring that the setup and implementation were reproducible was a priority. I documented each step meticulously to facilitate easy replication by others.

1. **Instructions for Creating and Activating a Virtual Environment**
   - I provided detailed instructions for creating a virtual environment using `venv` to ensure that all dependencies were isolated and managed efficiently.

2. **Inclusion of `requirements.txt`**
   - I compiled a `requirements.txt` file listing all necessary dependencies. This ensured that anyone setting up the environment could install all required packages with a single command

3. **Detailed Setup Instructions for AutoCodeRover**
   - I included comprehensive setup instructions for AutoCodeRover, covering everything from cloning the repository to configuring it to work with LLaMA3-7B.
   - This included modifications made to accommodate the smaller model, ensuring that all changes were documented and explained.

## Pull Request Lifecycle: Automating Everything from Solving Issues to Merging PRs

In this phase, the primary focus was to automate the entire pull request lifecycle, from issue identification and solution to the creation, submission, review, and merging of pull requests. This process involved the creation of two essential classes: `ContributorAgent` and `MaintainerAgent`. Below, I detail the steps taken and the challenges faced during this process.

### ContributorAgent

The first step was to design and initialize the `ContributorAgent` class. This agent is responsible for generating and submitting pull requests. I implemented the necessary methods to allow the agent to identify issues within the codebase, propose solutions, and create pull requests.

#### Key Implementations

1. **ContributorAgent Creation**: 
   - I started by defining the `ContributorAgent` class. This class includes methods to scan the codebase for issues, generate solutions, and package these solutions into pull requests.
   - I leveraged AutoCodeRover as a base framework for this agent. AutoCodeRover provided a foundational structure for navigating the codebase and proposing changes.

2. **Docker Integration**: 
   - To ensure a consistent and isolated environment for the `ContributorAgent`, I integrated `docker-py` for Docker management. This allowed the agent to operate within a Docker container, which replicates the auto-code-rover project's environment.
   - I configured the Docker container to include all necessary dependencies and set up volume bindings to sync the codebase between the host and the container.

3. **Diff Extraction**: 
   - After the `ContributorAgent` made changes, it was crucial to extract the generated diff from the Docker container to the local system for review and integration.
   - I implemented methods to retrieve these diffs, ensuring that they accurately reflected the proposed changes and were ready for submission as pull requests.

4. **Pull Request Creation**: 
   - The next step was to implement the logic for creating acceptable pull requests. The `ContributorAgent` needed to not only propose changes but also follow the proper protocol for committing these changes and submitting them for review.
   - This involved writing methods to commit code changes to a local repository using a `.diff` file and open a `pull_request.md` with adequate description for the same stored in a local `pull_requests` folder.

#### Challenges Faced

1. **Docker Integration**: 
   - One of the significant challenges was managing Docker containers and ensuring the correct extraction of diffs. This was addressed by detailed volume binding and container management within the script.
   - Debugging the interaction between the host system and the Docker container required meticulous attention to detail, particularly in maintaining file permissions and synchronization.

2. **Using AutoCodeRover with Docker**: 
   - Integrating AutoCodeRover to work seamlessly within a Docker environment required several adjustments. I needed to ensure that the Docker container had all the dependencies that AutoCodeRover required.
   - Additionally, I adapted AutoCodeRover's functionality to be compatible with `docker-py`, which involved modifying some of its internal scripts to run within the container context.

### Maintainer Agent

The `MaintainerAgent` is responsible for reviewing submitted pull requests and deciding whether to accept or reject them based on their quality and relevance.

#### Key Implementations

1. **Review and Merge**: 
   - The `MaintainerAgent` was designed to review the pull request description generated by the `ContributorAgent`. It will later also check for metrics such as code quality, relevance, and adherence to project guidelines.
   - Upon approval, the `MaintainerAgent` merges the pull request into the main branch. This involves fetching the pull request, reviewing the diffs, and executing the merge.

2. **Git Integration**: 
   - I integrated Git commands within the `MaintainerAgent` to apply approved diffs to the local repository and commit the changes. This automation ensures that the approved changes are seamlessly incorporated into the project.

#### Challenges Faced

1. **Automated Review**: 
   - Creating a reliable process for automated review using large language models (LLMs) was critical. This involved querying models like Ollama to generate decisions on pull request approvals.
   - Ensuring the automated review process was accurate and aligned with project standards required fine-tuning the prompts and reviewing multiple iterations of the agent's feedback.


## The Main Contribution: `reviewed_pr.py`

The primary outcome of this phase is the `reviewed_pr.py` file, which automates the creation, submission, review, and merging of pull requests by simulating the interactions between `ContributorAgent` and `MaintainerAgent`. This script encapsulates the workflow and ensures that the entire process is automated, efficient, and reproducible.

### Summary of Steps Taken

1. **Design and Initialization**
   - Created and initialized the `ContributorAgent` and `MaintainerAgent` classes.
   - Defined the roles and responsibilities of each agent within the pull request lifecycle.

2. **Implementation**
   - Developed methods for creating and submitting pull requests within the `ContributorAgent`.
   - Integrated Docker to manage the project environment.
   - Implemented diff extraction and local system integration.
   - Developed review and merge functionality within the `MaintainerAgent`.

3. **Testing and Debugging**
   - Conducted extensive testing to ensure the agents performed their tasks correctly.
   - Debugged issues related to Docker integration and automated review processes.

4. **Documentation**
   - Documented the entire process to ensure reproducibility and ease of understanding for future contributors.

By automating the pull request lifecycle, I have streamlined the process of identifying and solving issues, creating and submitting pull requests, and reviewing and merging changes. This is a big step towards the upcoming phases of this project.

All of the code for this can be found at [the OREL-group GSOC repository](https://github.com/OREL-group/GSoC/tree/f62af05b7422321e1a9e044add668399b53343be/Open%20Source%20Sustainibility%20using%20LLMs).
