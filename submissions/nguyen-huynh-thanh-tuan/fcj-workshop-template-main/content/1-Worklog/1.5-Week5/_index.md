---
title: "Week 5"
date: 2026-05-14
weight: 5
chapter: false
pre: " <b> 1.5. </b> "
---

**Timeline:** 14/5 → 20/5 (5 working days)

## Day 1 - 14/5: Elastic Load Balancer fundamentals

**Work completed:** I studied Amazon Elastic Load Balancer (ELB) and learned how it distributes incoming traffic across multiple EC2 instances. During the lab, I explored the different types of load balancers available on AWS, reviewed listeners, target groups, and health checks, and learned how ELB improves the availability and reliability of cloud applications.
.

**Knowledge learned:** I understood that Elastic Load Balancer automatically distributes incoming requests to healthy instances, helping applications remain available even when individual servers encounter failures.


**Result achieved:** I understood the basic operation of Elastic Load Balancer and its role in improving application availability and traffic distribution.

**Difficulty and lesson learned:** Load balancing depends on correctly configured target groups and health checks. Incorrect configurations may prevent healthy instances from receiving traffic.


## Day 2 - 15/5: Amazon EC2 Auto Scaling

**Work completed:** I studied Amazon EC2 Auto Scaling and learned how scaling policies automatically adjust the number of EC2 instances according to workload demands. During the exercises, I reviewed Auto Scaling Groups, launch templates, scaling policies, and health monitoring. I also explored how Auto Scaling works together with Elastic Load Balancer to maintain application performance during traffic fluctuations.

**Knowledge learned:** I understood that Auto Scaling helps optimize both system performance and infrastructure costs by automatically increasing or decreasing computing resources based on demand.


**Result achieved:** I gained a better understanding of how Auto Scaling maintains high availability while reducing unnecessary operational costs.


**Difficulty and lesson learned:**Scaling policies should be configured carefully because overly aggressive scaling may increase costs, while insufficient scaling may affect application performance.

## Day 3 - 18/5: Amazon Route 53

**Work completed:** I studied Amazon Route 53 and learned how DNS services direct user requests to AWS resources. During the learning session, I explored hosted zones, DNS record types, routing policies, and domain management. I also reviewed how Route 53 integrates with Elastic Load Balancer and other AWS services to improve service availability.

**Knowledge learned:** I understood that Route 53 plays an important role in directing traffic reliably and supporting highly available cloud architectures.


**Result achieved:** I understood the relationship between domain names, DNS records, and AWS resources when deploying cloud applications.

**Difficulty and lesson learned:** DNS updates require propagation time, so changes to domain configurations may not take effect immediately.


## Day 4 - 19/5: Amazon CloudFront

**Work completed:**  I studied Amazon CloudFront as a Content Delivery Network (CDN) service. During the exercises, I learned how CloudFront distributes content through edge locations, reduces latency, and improves the performance of web applications. I also explored cache behavior, origin configuration, HTTPS support, and cache invalidation.

**Knowledge learned:** I understood that CloudFront helps deliver content faster by caching static resources closer to end users while reducing the workload on the origin server.


**Result achieved:** I understood the role of CloudFront in improving application performance and providing a more efficient content delivery solution.

**Difficulty and lesson learned:**Cached content may not update immediately after deployment, so cache invalidation should be performed when necessary to ensure users receive the latest version.

## Day 5 - 20/5: High availability architecture review


**Work completed:** I reviewed the AWS services studied during the week, including Elastic Load Balancer, Auto Scaling, Route 53, and CloudFront. I summarized how these services work together to build highly available and scalable cloud architectures. I also documented the deployment workflow, common configuration considerations, and best practices for improving system reliability and performance.


**Knowledge learned:** I recognized that building highly available cloud systems requires multiple AWS services to work together rather than relying on a single component.


**Result achieved:** I consolidated my understanding of AWS high availability services and prepared detailed notes for future laboratory exercises and cloud architecture design.

**Difficulty and lesson learned:** High availability depends not only on deploying multiple services but also on configuring and integrating them correctly. Careful planning and regular testing are essential to ensure the architecture operates reliably.