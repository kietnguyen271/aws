---
title: "Proposal"
date: ""
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# Đề Xuất Hệ Thống Hỗ Trợ Cai Thuốc Lá

## 1. Tóm tắt Điều hành

Bản đề xuất này mô tả thiết kế và triển khai một **Nền tảng Hỗ trợ Cai Thuốc Lá dựa trên đám mây**, nhằm giúp người dùng bỏ thuốc thông qua việc theo dõi dữ liệu, phân tích hành vi, huấn luyện bằng AI và tương tác cộng đồng.  
Hệ thống tích hợp một hạ tầng backend hiện đại, có khả năng mở rộng, được triển khai trên **AWS Cloud**, đảm bảo tính sẵn sàng cao, bảo mật và trải nghiệm người dùng liền mạch.

Mục tiêu là cung cấp một hành trình thông minh và cá nhân hóa để người dùng theo dõi, lập kế hoạch và đạt được mục tiêu cai thuốc—đồng thời mang đến cho quản trị viên và huấn luyện viên các công cụ để hỗ trợ và hướng dẫn họ.

---

## 2. Mục tiêu Hệ thống

- Giúp người dùng xây dựng và theo dõi các kế hoạch cai thuốc được cá nhân hóa.  
- Theo dõi hành vi hút thuốc và tiến trình sức khỏe theo thời gian thực.  
- Cung cấp huấn luyện bằng AI, nhắc nhở và thông điệp động viên.  
- Cho phép tương tác và khích lệ giữa các thành viên trong cộng đồng.  
- Cung cấp hạ tầng điện toán đám mây an toàn và có khả năng mở rộng.

---

## 3. Các Tính năng Chính

### Tính năng cho Người dùng

- **Đăng ký & Gói thành viên:** Người dùng có thể đăng ký, chọn các gói dịch vụ và thanh toán cho các tính năng cao cấp.  
- **Theo dõi tình trạng hút thuốc:** Ghi lại số lượng thuốc lá sử dụng mỗi ngày, chi phí và tần suất.  
- **Kế hoạch cai thuốc cá nhân:** Tạo và điều chỉnh kế hoạch cai dựa trên thói quen và mục tiêu của người dùng.  
- **Theo dõi tiến trình:** Hiển thị các thống kê như số ngày không hút thuốc, tiền tiết kiệm và cải thiện sức khỏe.  
- **Thông báo động viên:** Gửi tự động nhắc nhở và thông điệp khích lệ theo định kỳ.  
- **Thành tựu & Huy hiệu:** Mở khóa các cột mốc như “7 ngày không hút thuốc” hoặc “Tiết kiệm 100K”.  
- **Tương tác cộng đồng:** Chia sẻ thành tích, lời khuyên và động viên trong mạng lưới hỗ trợ.  
- **Tác tử AI Coaching:** Hướng dẫn cá nhân hóa dựa trên công nghệ máy học.  
- **Tích hợp thiết bị sức khỏe:** Thu thập dữ liệu từ các thiết bị đeo thông minh hoặc IoT để theo dõi tiến trình.

### Tính năng cho Quản trị viên & Nhà vận hành

- **Dashboard & Báo cáo:** Giám sát chỉ số người dùng, mức độ tương tác và phân tích tác động sức khỏe.  
- **Cổng huấn luyện viên:** Huấn luyện viên có thể tương tác với người dùng qua chat hoặc video.  
- **Quản lý phản hồi & đánh giá:** Theo dõi và phản hồi mức độ hài lòng của người dùng.  
- **Quản lý thanh toán & gói dịch vụ:** Quản lý các gói phí và đăng ký của người dùng.

---

## 4. Kiến trúc Hệ thống (AWS Cloud)

Hệ thống tận dụng các dịch vụ do AWS quản lý để đảm bảo khả năng mở rộng và bảo mật, như được minh họa trong sơ đồ kiến trúc.

### Lớp Frontend

- **Amazon S3** lưu trữ website tĩnh (frontend React hoặc Angular).  
- **Amazon CloudFront** phân phối nội dung toàn cầu và xử lý mã hóa SSL/TLS.

### Xác thực & Phân quyền

- **Amazon Cognito** quản lý đăng ký, đăng nhập và liên kết danh tính, đảm bảo truy cập an toàn cho cả người dùng và huấn luyện viên.

### Lớp Ứng dụng

- **AWS Lambda** xử lý các tác vụ serverless như thanh toán hoặc các hoạt động API nhẹ.  
- **Network Load Balancer (NLB)** phân phối các yêu cầu đến các instance EC2 backend.  
- **EC2 (Private Subnet)** chạy các microservice cốt lõi:
  - **User Service**  
  - **Cessation Service**  
  - **Social Media Service**

### Lớp Dữ liệu

- **PostgreSQL Databases** cho dữ liệu người dùng và cai thuốc (trên EC2 hoặc RDS).  
- **MongoDB** cho tính năng xã hội và dữ liệu phi cấu trúc.  
- **S3 Bucket (Backup)** lưu trữ bản sao lưu cơ sở dữ liệu được mã hóa định kỳ.

### DevOps Pipeline

- **GitLab CI/CD Pipeline** tự động triển khai lên **Amazon ECR** và EC2.  
- **VPC Endpoint** đảm bảo kết nối an toàn với các dịch vụ AWS mà không cần ra Internet.  
- **EC2 Instance Connect Endpoint** cho phép truy cập quản trị có kiểm soát.

{{< figure src="/images/2-Proposal/P.jpg" title="Hình 1 – Kiến trúc điện toán đám mây AWS cho Nền tảng Hỗ trợ Cai Thuốc Lá" >}}

---

## 5. Bảo mật và Tuân thủ

- **Mã hóa dữ liệu:** Tất cả dữ liệu nhạy cảm được mã hóa khi truyền (TLS) và khi lưu trữ (AES-256).  
- **IAM Policies:** Kiểm soát truy cập chi tiết cho từng vai trò hệ thống.  
- **Private Subnets:** Backend và cơ sở dữ liệu biệt lập khỏi Internet công cộng.  
- **VPC Link & Endpoints:** Đảm bảo giao tiếp nội bộ an toàn giữa các dịch vụ.  
- **Chiến lược sao lưu:** Sao lưu tự động hàng ngày lên S3 với versioning và lifecycle policy.

---

## 6. Khả năng mở rộng và Hiệu năng

- **Auto Scaling:** EC2 và Lambda tự động mở rộng khi nhu cầu tăng.  
- **CDN Caching:** CloudFront lưu cache nội dung giúp tăng tốc phân phối toàn cầu.  
- **Load Balancing:** NLB phân phối lưu lượng và đảm bảo chịu lỗi.  
- **Microservices tách biệt:** Cho phép mở rộng độc lập từng dịch vụ.

---

## 7. Phát triển trong tương lai

- Tích hợp với **ứng dụng di động** Android và iOS.  
- **Dự đoán nguy cơ tái hút** sử dụng AI nâng cao dựa trên hành vi người dùng.  
- Chat và tư vấn video thời gian thực.  
- Tích hợp **cổng thanh toán bên thứ ba**.  
- Các thử thách gamification và hệ thống phần thưởng.

---

## 8. Kết quả kỳ vọng

- Tăng tỷ lệ bỏ thuốc thành công.  
- Tăng động lực và mức độ tương tác của người dùng.  
- Hệ thống có khả năng mở rộng để hỗ trợ lượng người dùng lớn.  
- Nền tảng an toàn, tuân thủ và dễ bảo trì.

---
[📄 Download Proposal (.docx)](/aws/Proposal.docx)
