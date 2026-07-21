---
title: "Week 12"
date: 2026-07-02
weight: 12
chapter: false
pre: " <b> 1.12. </b> "
---

**Timeline:** 2/7 → 12/7 (5 working days)

> IRMS was completed by a five-member team. The notes below focus on my individual contribution while collaborating with the team.

## Day 1 - 2/7: Finalizing IRMS project implementation

**Work completed:** I worked with the team to review the final version of the Incident Response Management System (IRMS) before completing the internship report. I checked the overall system workflow, including user authentication, incident management, evidence storage, notification processing, and monitoring. I also reviewed whether the implemented features matched the original requirements and architecture design.

I verified the connection between AWS services such as Cognito, API Gateway, Lambda, DynamoDB, S3, EventBridge, SNS, CloudWatch, and CloudFront. Any remaining issues found during testing were discussed with team members and adjusted before final documentation.

**Knowledge learned:** A final system review is necessary to ensure that implementation results match the planned architecture and project objectives.

**Result achieved:** The main flows, including authentication, Incident CRUD, Timeline, and Evidence Upload, became more stable for end-to-end testing.

**Difficulty and lesson learned:** CORS and JWT errors can look similar at first, so authentication errors, routing errors, and backend errors should be debugged separately.

## Day 2 - 4/7: Writing and organizing the internship report

**Work completed:** I started completing the internship report by summarizing the AWS training process, project development stages, technical implementation, testing process, and final results. I organized the report structure based on the actual internship timeline, including AWS service learning, serverless architecture research, IRMS development, and system evaluation.

I also reviewed screenshots, diagrams, and technical explanations to ensure that the report clearly described my personal contributions while working in a five-member team.

```bash
npm run build
aws s3 sync dist/ s3://<frontend-bucket> --delete
aws cloudfront create-invalidation --distribution-id <distribution-id> --paths "/*"
```

**Knowledge learned:** A technical report should not only describe what was implemented but also explain the learning process, challenges, solutions, and results achieved.

**Result achieved:** The frontend could run more reliably through CloudFront and call the main APIs.

**Difficulty and lesson learned:** Without cache invalidation, viewers may still see old UI or JavaScript even after the source has been updated.

## Day 3 - 7/7: Reviewing results and preparing presentation materials

**Work completed:** I summarized the main achievements of the internship project and prepared content for the final presentation. The presentation focused on AWS Serverless architecture, selected AWS services, system workflow, implemented features, testing results, and lessons learned.

I reviewed the role of each AWS service in IRMS, including Cognito for authentication, Lambda for backend processing, DynamoDB for data storage, S3 for evidence management, SNS/EventBridge for notification workflows, and CloudWatch for monitoring.

**Knowledge learned:** Presenting a technical project requires explaining both the architecture and the practical value of the implemented system.

**Result achieved:** Workshop content became easier to read, more synchronized between Vietnamese and English, and closer to the AWS work I contributed to.

**Difficulty and lesson learned:** Documentation copied from working notes often has broken formatting or missing context, so each page needs to be checked carefully.

## Day 4 - 10/7: Final report review and feedback adjustment

**Work completed:** I reviewed the completed internship report and adjusted the content based on feedback from teammates and supervisors. I checked the accuracy of AWS service descriptions, project timeline, architecture diagrams, and personal contribution descriptions.

I also reviewed formatting, references, images, and technical terms to ensure the report was clear and professional. Any unclear explanations were rewritten to better describe the implementation process and lessons learned.

**Knowledge learned:** Feedback is an important part of improving technical documentation and ensuring that the final report accurately represents the project.

**Result achieved:** The report became more consistent in timeline, architecture, personal contribution, and AWS terminology.

**Difficulty and lesson learned:** When editing bilingual files, both meaning and formatting must be checked, not only sentence-by-sentence translation.

## Day 5 - 12/7: Final presentation and internship reflection

**Work completed:** I participated in the final internship presentation and reported the results achieved during the internship period. I presented the AWS knowledge gained, the development process of the IRMS project, the implemented serverless architecture, and my personal contributions to the team.

After receiving feedback from lecturers and company representatives, I reflected on the overall internship experience, including technical skills, teamwork, problem-solving ability, and documentation practices. I also identified areas for further improvement in cloud computing and security.

**Knowledge learned:** The internship helped me understand how cloud technologies are applied in real projects and improved my ability to work with AWS services in a team environment.

**Result achieved:** The report reflected the actual timeline, my personal role in the team, and the completed project status up to July 21, 2026.

**Difficulty and lesson learned:** Near the end, fixing one section can easily affect another, so a checklist-based review is safer than relying only on visual inspection.
