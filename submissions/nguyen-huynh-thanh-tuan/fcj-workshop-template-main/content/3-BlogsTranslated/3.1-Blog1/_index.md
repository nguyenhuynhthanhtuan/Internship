---
title: "Shift-left Security with Amazon Bedrock"
date: 2026-07-10
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

## 3.1 Shift-left Security with Amazon Bedrock: Automating Threat Modeling with Generative AI

In software development and cloud architecture, **Shift-left Security** is an important DevSecOps practice. Instead of finding vulnerabilities only after deployment, this approach brings security activities into the design phase. One core technique that supports this practice is **Threat Modeling**.

Traditional Threat Modeling often requires several meetings, security specialists, and manual analysis that can take from a few days to a full week. This makes it difficult to apply at scale, especially in modern development environments where teams deploy quickly.

To address this challenge, AWS introduced a reference architecture called **Threat Designer**, combining **Generative AI** and **Amazon Bedrock** to automate the process of building threat models.

![Shift-left Security and Security Debt](/Internship/images/3-BlogsTranslated/blog-diagram-01.jpeg)

### 3.1.1 Solution

Threat Designer is a web application that automates Threat Modeling by using **Foundation Models (FM)** on Amazon Bedrock.

Instead of requiring security engineers to manually analyze architecture diagrams and compare them with security frameworks, the system uses **Multimodal AI** to understand several inputs at the same time:

- System architecture diagrams
- Solution descriptions
- Deployment assumptions

From these inputs, it builds a **Threat Catalog** that includes threats, attack scenarios, and corresponding mitigations.

![Threat Designer architecture on AWS](/Internship/images/3-BlogsTranslated/blog-diagram-02.jpeg)

Unlike traditional Threat Modeling tools that rely mainly on fixed rules or templates, Foundation Models can reason with context, understand relationships between system components, and identify attack scenarios that may not have been predefined. The whole solution is built on a serverless architecture, which helps reduce operational cost, scale automatically, and minimize infrastructure management.

### 3.1.2 How It Works

The Threat Designer architecture combines several AWS services:

1. **Request intake:** The user accesses a web application deployed on AWS Amplify and signs in through Amazon Cognito. The user then uploads the architecture diagram, solution description, and required assumptions. These files are stored securely in Amazon S3.
2. **Processing trigger:** The frontend sends the request to Amazon API Gateway, which invokes an AWS Lambda backend through proxy integration. Lambda receives the request, stores the initial information, and starts the asynchronous agent workflow.
3. **AI agent analysis:** The agent runs on AWS Lambda and is orchestrated by LangGraph. This allows the Threat Modeling process to be split into smaller steps such as image processing, asset identification, data flow analysis, and threat enumeration.
4. **Agentic workflow:** After each analysis round, the agent evaluates the quality of its own output through gap analysis. The system supports manual mode, where the user defines the number of iterations, and auto mode, where the agent detects missing areas and continues improving the Threat Catalog.

At each step, the agent uses a Foundation Model on Amazon Bedrock to reason from the architecture image and the solution description. During analysis, AI can refer to security frameworks such as **MITRE ATT&CK** and **OWASP** to make the Threat Catalog more complete.

The execution state is stored in **Amazon DynamoDB**, including the agent execution state and the Threat Catalog. This allows the agent to work asynchronously and continue processing even when the analysis takes several minutes.

### 3.1.3 Personal Takeaways

After reading **Accelerate Threat Modeling with Generative AI** on the AWS Machine Learning Blog, I found that the most valuable part of Threat Designer is not only the use of a strong AI model, but also the way AWS designed an **Agentic Workflow** for a task that normally depends heavily on security expertise.

Before reading this article, I considered Threat Modeling difficult to automate because AI must understand system architecture, data flows, trust boundaries, and each application's deployment context. With the multimodal capability of Foundation Models on Amazon Bedrock and the self-evaluation workflow through LangGraph, AI does not only answer questions. It can participate in analysis, review its own output, and improve the result across multiple iterations.

This is also a clear example of **Agentic AI** being applied in cybersecurity. Instead of producing a single answer, AI can check for missing areas, identify gaps, and continue improving the output before returning it to the user.

From my perspective, Generative AI will not replace security engineers in the near future, but it can become a very useful support tool. Time-consuming work such as Threat Modeling, document analysis, and architecture review can be prepared by AI first, allowing specialists to focus more on evaluation and security decisions.

### 3.1.4 Conclusion

Threat Designer is not intended to replace cybersecurity specialists. It acts as a supporting tool for Threat Modeling, helping accelerate analysis and standardize assessment results. By combining AWS serverless architecture with Amazon Bedrock and Agentic Workflow, the process of building a threat model can be shortened from several days to around 5-15 minutes, depending on system complexity.

This is a noteworthy approach for DevSecOps teams, especially as Generative AI becomes more deeply integrated into software development and cybersecurity workflows.

**Reference:** [Accelerate Threat Modeling with Generative AI](https://aws.amazon.com/blogs/machine-learning/accelerate-threat-modeling-with-generative-ai/)
