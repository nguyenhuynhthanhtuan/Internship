---
title: "Week 2"
date: 2026-04-23
weight: 2
chapter: false
pre: " <b> 1.2. </b> "
---

**Timeline:** 23/4 → 29/4 (5 working days)

## Day 1 - 23/4: Amazon EC2 fundamentals

**Work completed:** I began the hands-on AWS laboratories by learning Amazon EC2 and its core components. I explored different EC2 instance types, Amazon Machine Images (AMIs), instance lifecycle, and pricing options. During the lab, I launched my first EC2 instance, configured basic settings, and became familiar with the AWS Management Console for managing cloud servers.


**Knowledge learned:** I understood that Amazon EC2 provides scalable virtual servers on AWS and that selecting the appropriate instance type depends on workload requirements, performance, and cost considerations.

**Result achieved:** I successfully deployed an EC2 instance and understood the basic process of creating, managing, and terminating cloud servers.

**Difficulty and lesson learned:** There were many configuration options when launching an instance, so I learned to carefully review each setting before deployment to avoid unnecessary configuration mistakes.


## Day 2 - 24/4: Amazon EBS and EC2 connectivity

**Work completed:** I studied Amazon Elastic Block Store (EBS) and learned how storage volumes are attached to EC2 instances. I practiced creating and attaching EBS volumes, reviewed volume types, and understood the relationship between EC2 and persistent storage. I also learned how to connect to Linux instances using SSH and Windows instances using Remote Desktop Protocol (RDP).


**Knowledge learned:** I learned that EBS provides persistent block storage independent of the EC2 instance lifecycle, making it suitable for operating systems, databases, and application data.

**Result achieved:** I understood how EC2 instances interact with EBS volumes and successfully connected to both Linux and Windows virtual machines.

**Difficulty and lesson learned:** Connecting to virtual machines requires the correct key pair, security group configuration, and network settings. Missing any of these components may prevent successful access.

## Day 3 - 27/4: IAM User and IAM Role management

**Work completed:** I practiced creating IAM Users, assigning permissions through IAM Groups and Policies, and creating IAM Roles for AWS services. During the exercises, I compared the use cases of IAM Users and IAM Roles and reviewed the principle of least privilege when assigning permissions.

**Knowledge learned:** I understood that IAM is the foundation of AWS identity and access management, and that permissions should always be granted based on actual operational requirements.

**Result achieved:** I successfully created IAM Users and IAM Roles and gained a clearer understanding of how AWS services securely access resources through IAM Roles.

**Difficulty and lesson learned:** Permission management can become complicated if policies are assigned incorrectly. I learned to grant only the minimum permissions required and expand access only when necessary.

## Day 4 - 28/4: Security Group and Virtual Private Cloud (VPC)

**Work completed:** I learned the fundamentals of Amazon VPC, including subnets, route tables, Internet Gateway, and Security Groups. I created a basic VPC environment, configured inbound and outbound rules for EC2 instances, and reviewed how Security Groups protect cloud resources by controlling network traffic.

**Knowledge learned:** I understood that Security Groups operate as virtual firewalls for EC2 instances, while Amazon VPC provides isolated networking environments for deploying cloud infrastructure securely.

**Result achieved:** I successfully configured a VPC environment and understood how networking and security settings work together to control access to AWS resources.


**Difficulty and lesson learned:** Incorrect network or security configurations can prevent instances from being accessed even when they are running. I learned to verify routing, subnet placement, and Security Group rules step by step when troubleshooting.


## Day 5 - 29/4: Weekly practice review

**Work completed:** I reviewed all laboratory exercises completed during the week, including Amazon EC2, Amazon EBS, IAM User, IAM Role, Security Group, Key Pair, and Amazon VPC. I summarized the deployment workflow, connection procedures, and security configurations used throughout the labs. I also documented common configuration errors and the corresponding solutions for future reference.

**Knowledge learned:** I recognized that deploying cloud infrastructure requires not only creating resources but also configuring identity management, networking, storage, and security correctly to ensure a stable environment.

**Result achieved:** I consolidated my understanding of the core AWS infrastructure services and prepared a set of notes that will support more advanced laboratories in the following weeks.

**Difficulty and lesson learned:** Reviewing each lab immediately after completion helped reinforce the concepts and made it easier to identify mistakes. I realized that maintaining clear documentation is essential when working with multiple AWS services.

