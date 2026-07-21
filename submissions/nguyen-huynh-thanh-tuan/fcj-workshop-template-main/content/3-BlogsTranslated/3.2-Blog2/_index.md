---
title: "Amazon RDS + AI + MCP"
date: 2026-07-10
weight: 2
chapter: false
pre: " <b> 3.2. </b> "
---

## 3.2 Amazon RDS + AI + MCP: When Reading Logs Is No Longer a Nightmare

While learning AWS, I often read AWS Blog articles to see how AWS solves real technical problems instead of only studying each service in isolation.

One article that caught my attention explained how AWS combines **Amazon CloudWatch**, **Amazon Bedrock**, **Kiro CLI**, and **MCP (Model Context Protocol)** to support Amazon RDS log analysis through natural language. What interested me was not only the AI technology, but also how the components were connected into a reasonable operational workflow.

### 3.2.1 How It Works

In a traditional workflow, an operator often needs to perform several manual steps:

- Open CloudWatch
- Find the correct Log Group
- Write a CloudWatch Logs Insights query
- Run multiple commands to filter data
- Read and summarize thousands of log lines

With the approach described in the article, AI can support most of these steps. The flow can be summarized as:

```text
Kiro CLI -> MCP Server -> CloudWatch -> RDS Logs -> AI summarizes the result -> Natural language answer
```

This reduces a lot of manual work when checking system health or investigating database-related incidents.

![RDS log analysis architecture with Kiro, MCP, and CloudWatch](/Internship/images/3-BlogsTranslated/blog-diagram-03.jpeg)

### 3.2.2 What I Learned

From my perspective, the main value of the article is not simply that "AI reads logs". The important part is how access control is handled when AI is introduced into cloud infrastructure.

AI does not directly access AWS resources. Instead, the **MCP Server** acts as an intermediary. AI can only call the tools that the MCP Server allows, CloudWatch remains the source for retrieving logs, and access permissions are still managed through IAM.

This makes AI integration with cloud infrastructure safer and more controlled. AI does not become a component with unrestricted access inside the system; it works within a defined set of tools and permissions.

### 3.2.3 Why This Approach Matters

In systems with dozens or hundreds of databases, the amount of daily log data can be very large. In many situations, operators do not lack data. What they lack is the ability to quickly find the right information.

Being able to ask questions in natural language can significantly reduce investigation time:

- Which database has the most connection errors today?
- Are there any queries running longer than 10 seconds in the last 24 hours?
- Did any unusual errors appear when CPU usage increased?

Instead of manually writing queries, filtering logs, and summarizing results, the operator can receive a preliminary analysis and then verify the details against the original CloudWatch data.

### 3.2.4 Personal Takeaways

I see this as a clear example of how AI is changing system operations. In the past, AI was often discussed in the context of chatbots or content generation. Now, AI is gradually becoming an operations assistant that helps cloud engineers with repetitive tasks such as reading logs, analyzing alerts, and summarizing data.

From this article, I learned that the future skill set of a Cloud Engineer may go beyond simply knowing how to use AWS services. Engineers also need to know how to combine AI with cloud services in a way that is safe, controlled, and useful for improving operational efficiency.

**Reference:** [Amazon RDS log analysis using natural language queries with Kiro and MCP](https://aws.amazon.com/vi/blogs/database/amazon-rds-log-analysis-natural-language-queries-with-kiro-and-mcp/)
