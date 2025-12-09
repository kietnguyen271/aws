---
title: "AWS Cloud Mastery Series #2 - DevOps on AWS"
date: 2025-11-17
weight: 4
chapter: false
pre: " <b> 4.4. </b> "
---

### AWS Cloud Mastery Series #2 - DevOps on AWS

**- Date:** November 17, 2025 (Full Day)
**- Location:** Bitexco Financial Tower, District 1, Ho Chi Minh City


---

#### Event Overview

This full-day deep-dive session focused on implementing DevOps culture, AWS Continuous Integration/Continuous Delivery (CI/CD) tools, and modernizing technologies like Infrastructure as Code (IaC) and Containerization.

**Key Objectives:**
* **Develop DevOps Mindset:** Grasp the culture, principles, and key performance metrics (DORA, MTTR, deployment frequency).
* **Build CI/CD Pipelines:** Master the use of AWS Code services (CodeCommit, CodeBuild, CodeDeploy, CodePipeline) for automating the release process.
* **Implement IaC:** Practice deploying and managing infrastructure using **AWS CloudFormation** and **AWS CDK**.
* **Application Modernization:** Learn about containerization, image storage (ECR), and orchestration management (ECS/EKS).
* **Improve Observability:** Set up comprehensive monitoring using **CloudWatch** and **AWS X-Ray** for distributed tracing.

---

#### Key Takeaways & Learnings

* **Culture is Foundational:** DevOps is a combination of culture, principles, and tools; DORA metrics are crucial for measuring team performance.
* **End-to-End Automation:** The entire process from source code (**CodeCommit**) to deployment (**CodePipeline/CodeDeploy**) must be automated, prioritizing safe deployment strategies like Blue/Green and Canary.
* **IaC is Essential:** Managing infrastructure as code increases repeatability, minimizes manual errors, and allows for easy drift detection in CloudFormation.
* **Containers for Microservices:** Utilizing Docker with AWS container management services (ECS, EKS, App Runner) is the standard model for deploying microservices architecture.
* **Distributed Tracing:** **AWS X-Ray** is vital for understanding the performance and bottlenecks in distributed systems, complementing traditional metrics and logs from **CloudWatch**.

---

#### Application to Work

1.  **Measure DORA:** Begin measuring DORA metrics (Deployment Frequency, Lead Time for Changes, Mean Time to Recovery, Change Failure Rate) for current projects.
2.  **Pipeline Transformation:** Select one manual or semi-automated deployment process and fully transition it to an automated **AWS CodePipeline** with automated Build (CodeBuild) and Deployment (CodeDeploy) steps.
3.  **Pilot CDK:** Start piloting the use of **AWS CDK** to define and deploy a small service, leveraging familiar programming languages (e.g., Python/TypeScript).
4.  **Integrate X-Ray:** Apply **AWS X-Ray** to newly developed microservices to gather tracing data and analyze inter-component performance.

---
