---
title: "AWS Cloud Mastery Series #2 - DevOps trên AWS"
date: 2025-11-17
weight: 4
chapter: false
pre: " <b> 4.4. </b> "
---

### AWS Cloud Mastery Series #2 - DevOps trên AWS

**- Ngày:** 17 tháng 11, 2025 (Cả ngày)
**- Địa điểm:** Bitexco Financial Tower, Quận 1, TP. Hồ Chí Minh


---

#### Tổng quan Sự kiện

Sự kiện chuyên sâu kéo dài cả ngày này tập trung vào việc áp dụng Văn hóa DevOps, các công cụ CI/CD (Tích hợp liên tục/Triển khai liên tục) của AWS, và các công nghệ hiện đại hóa như Infrastructure as Code (IaC) và Containerization.

**Mục tiêu chính:**
* **Phát triển Tư duy DevOps:** Hiểu rõ văn hóa, nguyên tắc và các chỉ số hiệu suất chính (DORA, MTTR, deployment frequency).
* **Xây dựng CI/CD:** Nắm vững cách sử dụng bộ dịch vụ AWS Code (CodeCommit, CodeBuild, CodeDeploy, CodePipeline) để tự động hóa quy trình phát hành.
* **Triển khai IaC:** Thực hành triển khai và quản lý cơ sở hạ tầng bằng **AWS CloudFormation** và **AWS CDK**.
* **Hiện đại hóa Ứng dụng:** Tìm hiểu về containerization, lưu trữ (ECR), và quản lý orchestration (ECS/EKS).
* **Cải thiện Observability:** Thiết lập hệ thống giám sát toàn diện bằng **CloudWatch** và **AWS X-Ray** để truy vết phân tán.

---

#### Bài học và Kiến thức cốt lõi (Key Takeaways)

* **Văn hóa là Nền tảng:** DevOps là sự kết hợp giữa văn hóa, nguyên tắc và công cụ; các chỉ số DORA là thước đo quan trọng cho sự thành công của đội ngũ.
* **Tự động hóa Triệt để:** Toàn bộ quá trình từ mã nguồn (**CodeCommit**) đến triển khai (**CodePipeline/CodeDeploy**) phải được tự động hóa, ưu tiên các chiến lược triển khai an toàn như Blue/Green và Canary.
* **IaC là Bắt buộc:** Quản lý cơ sở hạ tầng dưới dạng mã (IaC) giúp tăng khả năng lặp lại, giảm thiểu lỗi thủ công, và dễ dàng phát hiện độ lệch (drift detection) trong CloudFormation.
* **Container cho Microservices:** Sử dụng Docker cùng với các dịch vụ quản lý container của AWS (ECS, EKS, App Runner) là mô hình chuẩn để triển khai kiến trúc microservices.
* **Tracing Phân tán:** **AWS X-Ray** rất cần thiết để hiểu rõ hiệu suất và điểm nghẽn trong các hệ thống phân tán, bổ sung cho các metrics và logs truyền thống của **CloudWatch**.

---

#### Ứng dụng vào Công việc (Application to Work)

1.  **Đo lường DORA:** Bắt đầu đo lường các chỉ số DORA (Tần suất triển khai, Thời gian quay vòng thay đổi, Thời gian phục hồi sự cố, Tỷ lệ lỗi thay đổi) cho các dự án hiện tại.
2.  **Chuyển đổi Pipeline:** Lựa chọn một quy trình triển khai thủ công hoặc bán tự động và chuyển đổi hoàn toàn sang **AWS CodePipeline** với các bước Build (CodeBuild) và Deployment (CodeDeploy) tự động.
3.  **Thí điểm CDK:** Bắt đầu thí điểm sử dụng **AWS CDK** để định nghĩa và triển khai một dịch vụ nhỏ, tận dụng các ngôn ngữ lập trình quen thuộc (ví dụ: Python/TypeScript).
4.  **Tích hợp X-Ray:** Áp dụng **AWS X-Ray** cho các microservices mới được phát triển để thu thập dữ liệu truy vết (tracing data) và phân tích hiệu suất giữa các thành phần.

---
