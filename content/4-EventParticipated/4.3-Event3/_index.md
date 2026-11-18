
---
title: "Event 3 "
date: 2026-11-14
weight: 3
chapter: false
pre: " <b> 4.3. </b> "
---

# First Cloud AI Journey – Weekly Meet Up

- **Date:** Saturday, November 14, 2026
- **Location:** 26th Floor, Bitexco Financial Tower, Ho Chi Minh City, Vietnam
- **Organizer:** First Cloud AI Journey (FCAJ)
- **Presenters:** Members of the First Cloud AI Journey (FCAJ) team
- **Role:** In-Person Attendee

### Event Objectives

First Cloud AI Journey – Weekly Meet Up was a knowledge-sharing and networking event for individuals interested in Amazon Web Services (AWS), Cloud Computing, and Artificial Intelligence (AI).

The event focused on building and deploying AI systems in enterprise environments, covering topics such as cost optimization, AWS resource management, and the development of AI Agent systems capable of collaborating and handling multiple tasks.

Through presentations and practical insights shared by members of the FCAJ community, attendees had the opportunity to learn more about applying AWS and AI technologies to real-world projects.

### Key Topics and Knowledge Gained

#### 1. Differences Between AI in Academic Projects and Enterprise Environments

In academic environments, AI models are often developed and tested using available datasets with a relatively small number of users.

However, when deployed in enterprise environments, AI systems must support larger workloads, maintain stable performance, and ensure data security.

Several AWS services were introduced during this session:

- **AWS Lambda:** Runs code without requiring developers to manage servers.
- **Auto Scaling:** Automatically adjusts computing resources based on workload demands.
- **Load Balancer:** Distributes incoming requests across multiple servers to prevent overload.
- **AWS KMS:** Helps manage encryption keys to protect sensitive data.
- **AWS IAM:** Manages identities and access permissions for AWS resources.
- **Amazon Bedrock Guardrails:** Helps control AI-generated content and improve safety when using AI applications.

These services help AI systems become more scalable, reliable, and suitable for real-world business requirements.

**What I learned:**

This session helped me understand that building an AI model for a classroom project is different from deploying an AI system in a production environment.

Enterprise AI systems require not only accurate results but also scalability, security, reliability, and effective resource management.

#### 2. Using AI Wisely to Optimize Costs

One of the most interesting topics was how to choose appropriate AI models for different tasks instead of using a large AI model for every request.

The FCAJ team illustrated this concept using a restaurant analogy:

- **Head Chef:** A large AI model acts as the coordinator, assigning tasks to other specialized models.
- **Main Course, Dessert, and Cashier:** Smaller AI models handle specific tasks at lower costs.
- **Caching:** Stores the results of frequently repeated requests to avoid unnecessary processing.

By combining larger models with smaller specialized models, AI systems can reduce operational costs while maintaining efficient performance.

**What I learned:**

I realized that using the most powerful AI model is not always the best solution.

Selecting models based on task complexity and reusing previously generated results can improve response times, reduce costs, and make better use of computing resources.

#### 3. Building Multi-Agent Systems with Strands Agents

This session introduced how to combine **Strands Agents** and **FastAPI** to develop a Multi-Agent system in which each AI Agent is responsible for a specific task.

Several example Agents were presented:

- **qa_agent:** Uses FAISS to retrieve information from documents and answer questions related to policies.
- **ticket_agent:** Handles service requests and includes a human approval mechanism for important actions.
- **research_agent:** Uses Tavily to search for and summarize information from the Internet.

Instead of requiring a single AI Agent to perform every task, the system distributes requests among specialized Agents.

This approach makes workflows easier to organize, monitor, and expand when necessary.

**What I learned:**

I gained a better understanding of how Multi-Agent systems work and how different AI Agents can collaborate to complete more complex tasks.

I also learned that dividing responsibilities among Agents can improve system organization and make individual tasks easier to manage.

#### 4. Managing Security and Memory with Amazon Bedrock AgentCore

Another important topic was managing identities, access permissions, and memory when deploying AI Agent systems.

The following components were introduced:

- **AgentCore Identity:** Supports identity authentication and access management when AI Agents interact with external services.
- **AgentCore Gateway:** Provides a way to connect and manage tools and APIs that AI Agents can use.
- **OAuth2 and Amazon Cognito:** Support secure authentication and authorization processes.
- **Workload Access Token:** Used during authentication and authorization for system workloads.
- **AgentCore Memory:** Helps manage information within interaction sessions and retain useful information for future use.

**What I learned:**

This session showed me that developing AI Agents involves much more than generating answers to user questions.

A reliable AI Agent system must also consider security, user authentication, data management, access control, and memory management.

These aspects are especially important when AI Agents are allowed to interact with external services or perform actions on behalf of users.

### Learning Outcomes and Key Takeaways

The meetup provided valuable technical knowledge and practical insights into Cloud Computing and AI development.

**The main lessons I gained included:**

- Understanding the differences between experimental AI models developed for academic purposes and production AI systems deployed in enterprise environments.
- Learning how to optimize AI costs by combining large models, smaller specialized models, and caching mechanisms.
- Understanding how Multi-Agent systems distribute tasks among specialized Agents and manage their activities.
- Gaining knowledge about authentication, authorization, security, and memory management in AI Agent systems.
- Learning how different AWS services can work together to create scalable and reliable AI applications.
- Having the opportunity to exchange ideas and learn from FCAJ community members and other attendees, while exploring AWS and AI solutions that could be applied to the architecture of my internship project.

Overall, the event helped me develop a broader understanding of the technical and operational considerations involved in building AI systems for real-world applications.

### Event Participation Photos

The following photos were taken during **First Cloud AI Journey – Weekly Meet Up** on November 14, 2026, in Ho Chi Minh City.

![Use AI Wisely presentation slide at First Cloud AI Journey](/my_aws_workshop/images/4-EventParticipated/event-3/anhslide.jpg)

*Figure 1. The "Use AI Wisely" presentation slide illustrating how a large AI model can coordinate smaller specialized models, combined with caching repeated requests to optimize costs and processing efficiency.*

![Personal photo at First Cloud AI Journey](/my_aws_workshop/images/4-EventParticipated/event-3/anhcanhan.jpg)

*Figure 2. My personal photo at First Cloud AI Journey – Weekly Meet Up on November 14, 2026.*
