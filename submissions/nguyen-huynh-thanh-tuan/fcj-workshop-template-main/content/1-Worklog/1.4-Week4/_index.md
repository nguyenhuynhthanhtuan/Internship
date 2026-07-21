---
title: "Week 4"
date: 2026-05-07
weight: 4
chapter: false
pre: " <b> 1.4. </b> "
---

**Timeline:** 07/5 → 13/5 (5 working days)

## Day 1 - 07/5 : Amazon RDS fundamentals

**Work completed:** I studied Amazon Relational Database Service (RDS) and its role as a managed relational database service on AWS. During the lab, I explored different database engines supported by Amazon RDS, configured a database instance, reviewed storage options, and learned the basic process of connecting applications to a database using an endpoint.


**Knowledge learned:** I understood that Amazon RDS simplifies database administration by automating tasks such as software patching, backups, and maintenance while allowing users to focus on application development.


**Result achieved:** I successfully deployed an Amazon RDS instance and understood the basic workflow of creating and managing a managed relational database.


**Difficulty and lesson learned:** Database configuration requires careful consideration of storage, security, and network settings to ensure both performance and secure access.

## Day 2 - 8/5: Amazon Aurora

**Work completed:** I studied Amazon Aurora and compared it with standard Amazon RDS database engines. During the learning session, I explored Aurora's architecture, high availability, automatic failover, and performance optimization features. I also reviewed common scenarios where Aurora is preferred for enterprise applications.


**Knowledge learned:** I learned that Amazon Aurora is designed to provide higher performance, better scalability, and improved availability compared to traditional relational database engines while remaining compatible with MySQL and PostgreSQL.

**Result achieved:** I understood the key differences between Amazon Aurora and standard Amazon RDS services and identified situations where Aurora would be the more suitable choice.


**Difficulty and lesson learned:** Although Aurora offers many advanced features, selecting it should depend on application requirements rather than using it by default.


## Day 3 - 11/5: Amazon DynamoDB

**Work completed:** I studied Amazon DynamoDB as a fully managed NoSQL database service. During the lab, I explored tables, partition keys, sort keys, and data organization. I also learned how DynamoDB supports applications that require low latency and high scalability while reducing database administration tasks.

**Knowledge learned:** I understood that DynamoDB is suitable for applications with large numbers of requests and flexible data structures where high performance and automatic scaling are required.


**Result achieved:** I gained a better understanding of NoSQL database concepts and the differences between DynamoDB and traditional relational databases.


**Difficulty and lesson learned:** Designing an appropriate partition key is important because it directly affects database performance and scalability.


## Day 4 - 12/5: Amazon ElastiCache and database performance

**Work completed:** I studied Amazon ElastiCache and learned how caching improves application performance by reducing the number of requests sent directly to the database. I reviewed the basic concepts of Redis and Memcached, their common use cases, and how caching can reduce response time while improving system scalability.


**Knowledge learned:** I understood that caching is an effective technique for improving application performance, reducing database workload, and supporting high-traffic systems.
.

**Result achieved:** I understood the role of Amazon ElastiCache in cloud architecture and recognized when caching should be applied to improve overall system performance.
.

**Difficulty and lesson learned:**Cached data may become outdated if it is not synchronized properly, so cache management strategies should be carefully planned.


## Day 5 - 13/5: Database management and weekly review

**Work completed:**I reviewed the database services studied during the week, including Amazon RDS, Amazon Aurora, Amazon DynamoDB, and Amazon ElastiCache. I summarized their key characteristics, management methods, backup strategies, monitoring capabilities, and common use cases. I also reviewed basic administrative tasks such as configuring database settings, monitoring performance, and planning backup and recovery procedures.


**Knowledge learned:** I recognized that each AWS database service is designed for different application requirements, and selecting the appropriate solution depends on data structure, scalability, performance, and availability requirements.

**Result achieved:** I consolidated my understanding of AWS database services and prepared detailed notes to support future laboratory exercises and project implementation.

**Difficulty and lesson learned:** Comparing relational databases, NoSQL databases, and caching services helped me understand their individual roles more clearly. I realized that choosing the right database architecture is essential for building efficient and scalable cloud applications.