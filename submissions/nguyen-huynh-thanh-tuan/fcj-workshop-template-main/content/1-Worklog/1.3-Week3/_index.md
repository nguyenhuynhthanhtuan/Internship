---
title: "Week 3"
date: 2026-04-30
weight: 3
chapter: false
pre: " <b> 1.3. </b> "
---

**Timeline:** 30/4 → 6/5 (5 working days)

## Day 1 - 30/4: Amazon S3 fundamentals

**Work completed:** I studied Amazon S3 and its object storage model. During the lab, I practiced creating S3 buckets, uploading and managing objects, configuring bucket settings, and reviewing bucket permissions. I also explored the differences between object storage and traditional file storage, as well as common use cases of Amazon S3 for storing application data, static website content, and backup files.

**Knowledge learned:** I understood that Amazon S3 is a highly durable object storage service designed to store and retrieve data efficiently while providing high availability and scalability.

**Result achieved:** I successfully created and managed S3 buckets and understood the basic workflow of storing and accessing objects in Amazon S3.


**Difficulty and lesson learned:**Bucket permissions must be configured carefully because incorrect settings may unintentionally expose data to the public.


## Day 2 - 1/5: Amazon EFS and shared file storage


**Work completed:** I studied Amazon Elastic File System (EFS) and learned how it provides shared file storage for multiple EC2 instances. During the learning session, I compared Amazon EFS with Amazon EBS and Amazon S3 to understand their different storage purposes, performance characteristics, and usage scenarios.


**Knowledge learned:** I learned that Amazon EFS is suitable for workloads requiring a shared file system, while Amazon EBS provides block storage and Amazon S3 is designed for object storage.


**Result achieved:** I understood the differences between the three AWS storage services and identified appropriate use cases for each solution.

**Difficulty and lesson learned:** Although all three services are used for data storage, selecting the correct storage service depends on the application's architecture and access requirements.

## Day 3 - 4/5: Amazon S3 Storage Classes and Lifecycle Policy

**Work completed:** I explored the different Amazon S3 Storage Classes, including Standard, Standard-IA, One Zone-IA, Intelligent-Tiering, Glacier Instant Retrieval, Glacier Flexible Retrieval, and Glacier Deep Archive. I also practiced configuring Lifecycle Policies to automatically transition objects between storage classes or delete objects after a specified period.


**Knowledge learned:** I understood that selecting the appropriate storage class and configuring Lifecycle Policies can significantly reduce storage costs while maintaining data availability based on business requirements.


**Result achieved:** I successfully configured Lifecycle Policies and understood how automated storage management helps optimize long-term cloud storage costs.


**Difficulty and lesson learned:** Storage optimization should be planned carefully because selecting an inappropriate storage class may increase retrieval costs or reduce data accessibility.

## Day 4 - 5/5: AWS Backup and data recovery

**Work completed:** I studied AWS Backup and learned how centralized backup management works across AWS services. During the exercises, I reviewed backup plans, backup vaults, retention periods, and recovery points. I also practiced creating backup jobs and explored the basic process of restoring protected resources.

**Knowledge learned:** I understood that regular backups are an essential part of disaster recovery planning and help improve data protection and business continuity.


**Result achieved:** I gained a better understanding of backup management and the importance of planning backup schedules and retention policies.

**Difficulty and lesson learned:**Creating backups alone is not sufficient; recovery procedures should also be tested regularly to ensure data can be restored successfully when needed.

## Day 5 - 6/5: Weekly storage services review

**Work completed:** I reviewed all storage-related services studied during the week, including Amazon S3, Amazon EFS, Storage Classes, Lifecycle Policy, Versioning, and AWS Backup. I summarized the key characteristics, advantages, and common use cases of each service. I also documented the configuration steps and best practices for managing cloud storage and data protection.


**Knowledge learned:** I recognized that AWS provides multiple storage solutions for different business needs, and choosing the appropriate service depends on performance, availability, durability, and cost requirements.


**Result achieved:** I consolidated my understanding of AWS storage services and prepared a complete set of notes to support future laboratory exercises and project development.


**Difficulty and lesson learned:** Reviewing and comparing the storage services after completing the labs helped me understand their differences more clearly and improved my ability to select the appropriate storage solution for different scenarios.
