---
title: "Week 8"
date: 2026-06-04
weight: 8
chapter: false
pre: " <b> 1.8. </b> "
---

**Timeline:** 4/6 → 10/6 (5 working days)
## Day 1 - 4/6: AWS WAF and web application protection
**Work completed:** I studied AWS WAF (Web Application Firewall) and learned how it protects web applications from common web-based attacks. During the learning session, I explored Web ACLs, managed rules, custom rules, IP filtering, and rate-based rules. I also reviewed how AWS WAF integrates with services such as CloudFront and Application Load Balancer to filter malicious HTTP requests before they reach the application.

**Knowledge learned:** I understood that AWS WAF provides an additional security layer at the application level by monitoring and controlling incoming web traffic based on predefined security rules.

**Result achieved:** I understood how a React app becomes static files for S3/CloudFront hosting.

**Difficulty and lesson learned:** A wrong base URL or build-time variable can make the deployed frontend call the wrong API.

## Day 2 - 5/6: AWS Shield and DDoS protection

**Work completed:** I studied AWS Shield and learned how AWS provides protection against Distributed Denial of Service (DDoS) attacks. I explored the differences between AWS Shield Standard and AWS Shield Advanced, as well as how AWS Shield works together with CloudFront, Route 53, and other edge services to improve application availability.


**Knowledge learned:** I understood that DDoS protection is an important part of cloud security, especially for applications exposed to the public Internet.


**Result achieved:** I knew what to check when a protected API returned 401 or 403.

**Difficulty and lesson learned:** Tokens should not be logged casually in the browser console.

## Day 3 - 8/6: Amazon GuardDuty and threat detection

**Work completed:** I studied Amazon GuardDuty and learned how it uses threat intelligence and machine learning to detect suspicious activities in AWS environments. During the learning process, I explored findings related to unusual API calls, compromised credentials, malicious network activities, and potential security risks.

**Knowledge learned:** I understood that GuardDuty provides continuous security monitoring and helps identify potential threats without requiring manual analysis of all system activities.

**Result achieved:** I had a CORS checklist for API Gateway and Lambda responses.

**Difficulty and lesson learned:** Real browser testing is required after deploying an API Gateway stage.

## Day 4 - 9/6: Amazon Inspector and vulnerability assessment


**Work completed:** I studied Amazon Inspector and learned how it automatically assesses vulnerabilities in AWS workloads. I explored how Inspector analyzes EC2 instances, container images, and application dependencies to identify security issues such as software vulnerabilities and configuration risks.

**Knowledge learned:** I understood that vulnerability assessment helps organizations discover security weaknesses before they can be exploited by attackers.

**Result achieved:** I gained knowledge about automated vulnerability scanning and how security teams can prioritize remediation based on risk levels.

**Difficulty and lesson learned:** Vulnerability scanning is only effective when combined with regular updates, patch management, and proper security practices.

**Result achieved:** I understood the common states for API-driven screens: loading, success, empty, error, and unauthorized.

**Difficulty and lesson learned:** If API logic is mixed into components, changing endpoints or auth headers becomes difficult.

## Day 5 - 10/6: AWS KMS, Secrets Manager and security review
**Work completed:** I studied AWS Key Management Service (KMS) and AWS Secrets Manager for managing encryption keys and sensitive information. During the exercises, I learned about customer managed keys, encryption and decryption processes, key policies, and secure storage of application secrets. I also reviewed how Secrets Manager helps applications retrieve credentials securely without storing sensitive information directly in source code.


**Knowledge learned:**  I understood that encryption and secret management are essential components of cloud security, helping protect sensitive data and prevent credential exposure.

**Result achieved:** I had an integration checklist to use when the team connected the IRMS frontend and backend.

**Difficulty and lesson learned:** Testing should follow real user flows, not only individual API calls.
