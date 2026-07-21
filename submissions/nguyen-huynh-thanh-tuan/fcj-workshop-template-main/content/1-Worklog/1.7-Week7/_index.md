---
title: "Week 7"
date: 2026-05-28
weight: 7
chapter: false
pre: " <b> 1.7. </b> "
---

**Timeline:** 28/5 → 3/6 (5 working days)

## Day 1- 28/5: Amazon CloudWatch fundamentals

**Work completed:** I studied Amazon CloudWatch and learned how AWS monitors applications, infrastructure, and services. During the lab, I explored CloudWatch Metrics, Logs, Log Groups, Log Streams, Dashboards, and Alarms. I practiced viewing system metrics such as CPU utilization, network activity, and application logs to understand how CloudWatch supports monitoring and troubleshooting.


**Knowledge learned:** I understood that Amazon CloudWatch provides centralized monitoring capabilities, allowing users to collect logs, track performance metrics, and create alerts for abnormal system conditions.

**Result achieved:** I successfully viewed resource metrics, analyzed logs, and understood the basic process of creating CloudWatch Alarms.

**Difficulty and lesson learned:** Resource names, parameters, and outputs should be consistent from the beginning.

## Day 2 - 2/6: sam validate practice

**Work completed:** I studied AWS CloudTrail and learned how it records API activities and user actions within an AWS account. During the learning process, I reviewed event history, management events, resource changes, and how CloudTrail helps identify who performed specific actions on AWS resources.


**Knowledge learned:** I understood that CloudTrail plays an important role in security auditing, compliance monitoring, and investigating unexpected changes within AWS environments.

**Result achieved:** I learned how to review CloudTrail events and identify information such as the user, action performed, time, and affected resources.

**Difficulty and lesson learned:** YAML indentation is sensitive, so templates should be formatted carefully.

## Day 3 - 3/6: sam build practice

**Work completed:** I studied AWS Config and learned how it continuously monitors AWS resource configurations. During the exercises, I explored configuration history, compliance rules, and how AWS Config detects whether resources follow predefined security and operational standards. I also reviewed how Config can support governance and compliance management.


**Knowledge learned:** I understood that AWS Config helps track configuration changes and ensures cloud resources maintain the required security and compliance status.

**Result achieved:** I understood how to review resource configurations and identify changes that may affect system security or reliability.

**Difficulty and lesson learned:** Runtime versions and dependencies should be reviewed before deployment.

## Day 4 - 4/6: sam deploy practice

**Work completed:** I studied AWS Systems Manager and its role in managing and operating AWS resources efficiently. During the learning session, I explored features such as Session Manager, Run Command, inventory management, and operational monitoring. I learned how Systems Manager can provide secure access to EC2 instances without exposing SSH ports directly to the Internet.

**Knowledge learned:** I understood that Systems Manager simplifies server management by providing centralized control, automation, and secure operational access.

**Result achieved:**  I gained a better understanding of how AWS Systems Manager supports infrastructure management and improves operational security.

**Difficulty and lesson learned:** When deployment fails, CloudFormation events should be reviewed instead of only reading the final terminal error.

## Day 5 - 5/6: Deployment checklist

**Work completed:** I studied AWS X-Ray and learned how distributed tracing helps analyze application performance and identify errors in complex systems. During the review, I explored traces, segments, service maps, and how X-Ray works with services such as Lambda and API Gateway to track request flows.


**Knowledge learned:** I understood that AWS X-Ray helps developers locate performance bottlenecks and troubleshoot failures by visualizing the communication between different components of an application.

**Result achieved:**  I gained an overview of how monitoring, logging, tracing, and auditing services work together to improve system reliability.

**Difficulty and lesson learned:**  Effective troubleshooting requires combining information from multiple sources such as CloudWatch Logs, CloudTrail events, Config changes, and X-Ray traces instead of relying on a single monitoring tool.