---
title: "Blogs"
date: ""
weight: 3
chapter: false
pre: " <b> 3. </b> "
---


###  [Blog 1 – Truy cập Amazon Q Developer an toàn bằng PingIdentity](3.1-Blog1/)
Blog này giải thích cách các tổ chức có thể tích hợp **PingIdentity** với **AWS IAM Identity Center** để cung cấp cơ chế xác thực SAML an toàn cho **Amazon Q Developer**.  
Bài viết hướng dẫn chi tiết cách cấu hình PingIdentity làm nhà cung cấp danh tính (IdP), kích hoạt AWS SSO, trao đổi metadata, thiết lập SCIM provisioning để đồng bộ người dùng và nhóm, và gán quyền truy cập Amazon Q Developer Pro.  
Ngoài ra, blog cũng mô tả quy trình đăng nhập trong IDE bằng thông tin đăng nhập PingIdentity, giúp lập trình viên truy cập Amazon Q Developer một cách liền mạch mà không cần tài khoản AWS riêng biệt.

###  [Blog 2 – Xây dựng Trợ lý Ảo Ngành Năng lượng với Amazon Bedrock, Amazon Connect và Amazon Lex](3.2-Blog2/)
Blog này giới thiệu cách xây dựng **Energy Virtual Assistant** hỗ trợ khách hàng giao tiếp bằng ngôn ngữ tự nhiên thông qua **Amazon Connect**, **Amazon Lex** và **Amazon Bedrock Agents**.  
Giải pháp cho phép tự động hóa các tác vụ phổ biến trong ngành tiện ích — như báo cáo mất điện, tra cứu hóa đơn, cập nhật thông tin tài khoản và phân tích mức tiêu thụ điện — bằng cách tích hợp Lex, Bedrock Agent, DynamoDB, Timestream và các hệ thống backend mô phỏng.  
Bài viết cung cấp hướng dẫn từng bước triển khai CloudFormation, cấu hình intent trong Lex, liên kết số điện thoại Amazon Connect, và kiểm thử toàn bộ luồng hội thoại.  
Giải pháp minh hoạ cách generative AI có thể hiện đại hóa trung tâm hỗ trợ khách hàng bằng khả năng tự động hóa thông minh và phản hồi theo ngữ cảnh.

###  [Blog 3 – Xây dựng pipeline streaming serverless bảo mật với Amazon MSK Serverless và Amazon EMR Serverless](3.3-Blog3/)
Blog này trình bày cách xây dựng pipeline **streaming serverless** hoàn chỉnh, sử dụng **Amazon MSK Serverless** để thu nhận dữ liệu Kafka và **Amazon EMR Serverless** để xử lý dữ liệu bằng Spark Structured Streaming theo thời gian gần thực.  
Bài viết giải thích cách **IAM authentication** thay thế cơ chế quản lý chứng chỉ TLS truyền thống, cho phép Spark kết nối bảo mật với MSK mà không cần keystore hay secret.  
Nội dung bao gồm triển khai CloudFormation, tạo Kafka topic, tạo dữ liệu streaming từ EC2, cấu hình Spark với IAM authentication, ghi dữ liệu xử lý vào Amazon S3, tạo bảng Glue Data Catalog, và truy vấn kết quả bằng **Amazon Athena**.  
Giải pháp chứng minh cách doanh nghiệp có thể đơn giản hóa kiến trúc streaming, tăng cường bảo mật và mở rộng quy mô mà không cần quản lý máy chủ hay cluster.

