---
title: "Blog 1"
date: ""
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---

AWS DevOps & Developer Productivity Blog
# Tăng tốc phát triển với quyền truy cập an toàn vào Amazon Q Developer bằng PingIdentity


*by Sid Vantair on 19 JUN 2025 in Amazon Q, AWS IAM Identity Center, Generative AI, Technical How-to Permalink*
Các khách hàng đang sử dụng [Amazon Q Developer](https://aws.amazon.com/q/developer/), 
một công cụ hỗ trợ lập trình được tăng cường bởi **Generative AI**, 
thường cần xác thực thông qua các nhà cung cấp danh tính (Identity Provider – IdP) 
hiện có như **PingIdentity**. Bằng cách tận dụng 
[AWS IAM Identity Center](https://aws.amazon.com/iam/identity-center/), 
các tổ chức có thể cho phép nhà phát triển của họ truy cập **Amazon Q Developer** 
bằng chính thông tin đăng nhập **PingIdentity** sẵn có, giúp đơn giản hóa quy trình 
xác thực và loại bỏ nhu cầu đăng nhập riêng biệt.

**Amazon Q Developer** có thể trò chuyện về mã nguồn, cung cấp gợi ý hoàn thiện mã trực tiếp 
(*inline code completions*), và tạo mới mã nguồn. Nó cũng quét mã của bạn để tìm lỗ hổng bảo mật 
và thực hiện cải thiện mã, bao gồm cập nhật ngôn ngữ, gỡ lỗi (*debugging*) và tối ưu hóa.

**Amazon Q Developer** có hai cấp độ. **Free Tier** được cung cấp miễn phí cho mục đích cá nhân. 
**Pro Tier** là phiên bản trả phí, cung cấp các tính năng như kiểm soát truy cập ở cấp độ doanh 
nghiệp, bảng điều khiển phân tích (*analytics dashboard*), tùy chỉnh và giới hạn sử dụng cao hơn.

Các tổ chức kích hoạt **Pro Tier** của **Amazon Q Developer** cho nhà phát triển của mình 
thường xác thực thông qua **AWS IAM Identity Center**. Cách tiếp cận này phổ biến vì khả năng 
liên kết (*federate*) với các nhà cung cấp danh tính bên ngoài.

Trong bài viết này, chúng tôi sẽ hướng dẫn bạn cách thiết lập **PingIdentity** làm external IdP 
cho **IAM Identity Center** và cho phép các nhà phát triển truy cập **Amazon Q Developer** 
bằng thông tin đăng nhập **PingIdentity** hiện có của họ.
các nhà phát triển truy cập Amazon Q Developer bằng thông tin đăng nhập PingIdentity hiện có của họ.
 
<span style="font-weight: bold; font-size: 24px;">Các hoạt động</span>

![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-1.png)
**Hình 1 – Tổng quan giải pháp**

## The business challenge

## Quy trình xác thực diễn ra như sau:

1. Nhà phát triển khởi tạo yêu cầu truy cập **Amazon Q Developer**.

2. **IAM Identity Center** kiểm tra trạng thái xác thực.

3. Nếu chưa được xác thực, người dùng sẽ được chuyển hướng đến trang đăng nhập **PingIdentity**.

4. Nhà phát triển nhập thông tin đăng nhập **PingIdentity**.

5. **PingIdentity** xác thực thông tin và gửi phản hồi **SAML**.

6. **IAM Identity Center** xác minh phản hồi **SAML**.

7. Khi xác minh thành công, quyền truy cập **Amazon Q Developer** được cấp.

8. Nhà phát triển bắt đầu sử dụng **Amazon Q Developer**.

---

## Yêu cầu trước

- Tài khoản **AWS**
- Môi trường **PingIdentity** có người dùng và nhóm đã được thiết lập sẵn để truy cập **Amazon Q Developer**
- **IAM Identity Center**
- **Gói đăng ký Amazon Q Developer bản Pro Tier**

---

## Hướng dẫn thực hiện

Trong phần này, chúng ta sẽ thực hành cách tạo kết nối dựa trên SAML giữa 
**PingIdentity** và **IAM Identity Center**, giúp bạn có thể truy cập 
**Amazon Q Developer** dễ dàng bằng thông tin đăng nhập PingIdentity của mình.

**Lưu ý:** Bạn sẽ cần chuyển qua lại giữa cổng quản trị PingIdentity và IAM Identity Center trên trình duyệt.  
Nên mở hai tab trình duyệt riêng biệt cho từng bảng điều khiển để thao tác thuận tiện hơn.

---

## Bước 1: Kích hoạt AWS Single Sign-On trong PingIdentity

Bước này bao gồm việc bật ứng dụng AWS Single Sign-On trong PingIdentity.

1. Trong giao diện PingIdentity, đi đến **Applications tab > Application Catalog**.

2. Tìm trong danh mục với từ khóa **"AWS Single Sign-On"** và chọn dấu **+** để bắt đầu Quick Setup.

![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-2.png)
**Hình 2 – Danh mục Ứng dụng PingIdentity**
*Alt Text:* Ảnh chụp màn hình của giao diện PingIdentity Application Catalog. 
Từ khóa “aws” được nhập trong thanh tìm kiếm, hiển thị ba kết quả: Amazon Web Services – AWS, 
AWS *Gov-Cloud* và AWS Single Sign-On. Tùy chọn **"AWS Single Sign-On"** được viền bằng hộp màu đỏ 
và bao gồm nút dấu cộng để thêm ứng dụng.

---

### 3. Cung cấp Name, SSO Region và SSO **Tenant ID**, rồi chọn **Next**

- **Name** – Nhập tên phù hợp cho kết nối  
- **SSO Region** – Nhập vùng (region) thích hợp  
- **Tenant ID** – Là Identity Store ID  

Bạn có thể chạy lệnh CLI sau để lấy giá trị này. Đây là chuỗi gồm **10 ký tự chữ và số**, bắt đầu bằng tiền tố **“d-”**:


aws sso-admin list-instances --query 'Instances[0].IdentityStoreId'

Output: “d-XXXXXXXXXX”
###  4. Điều hướng đến PingOne Mappings và chọn Email Address từ danh sách thả xuống.
![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-3.png)
**Hình 3 – Bản đồ thuộc tính đăng nhập một lần AWS**

*Alt Text:* Ảnh chụp màn hình cấu hình **AWS Single Sign-On** trong **PingIdentity**. 
Màn hình hiển thị *Bước 2* của quá trình thiết lập, trong đó thuộc tính 
**SAML_SUBJECT** được ánh xạ với thuộc tính **PingOne “Email Address”**. 
Một hộp đỏ làm nổi bật phần ánh xạ trong **“PingOne Mappings”**.

### 5. Tìm kiếm và chọn nhóm mà bạn đã tạo trước đó để cấp quyền truy cập cho 
**Amazon Q Developer**, và chọn dấu “+” để thêm nhóm.

### 6. Chọn **Save**.
![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/12/devops-16054-image-4.png)
**Hình 4 – Chọn các Nhóm thư mục PingIdentity để truy cập Amazon Q Developer**
*Alt Text:* Ảnh chụp màn hình của **Bước 3** trong quy trình thiết lập 
**AWS Single Sign-On** trong **PingIdentity**. Màn hình hiển thị giao diện chọn nhóm, 
trong đó nhóm **“Amazon Q”** được liệt kê. Một biểu tượng dấu cộng (+) xuất hiện bên cạnh 
nhóm để thêm vào, và nút **“Save”** màu xanh lam được làm nổi bật ở góc dưới bên phải để xác nhận cấu hình.

---

## Bước 2: Kết nối PingIdentity với IAM Identity Center

Bước này bao gồm việc cấu hình **PingIdentity** với thông tin đăng nhập của 
**AWS IAM Identity Center** để hoàn tất quá trình thiết lập xác thực.

1. Trong **bảng điều khiển PingIdentity**, điều hướng đến  
   **Applications Tab > Applications** và chọn ứng dụng bạn đã tạo trước đó ở **Bước 1**.

2. Chọn **Enable Advanced Configuration** và nhấn **Enable**.
![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-5.png)
**Hình 5 – Bật cấu hình nâng cao cho ứng dụng Đăng nhập một lần AWS**
*Alt Text:* Ảnh chụp màn hình bảng điều khiển **PingIdentity Applications** 
hiển thị ứng dụng **AWS Single Sign-On** đã được chọn. Bảng tổng quan hiển thị các phần 
cấu hình chính bao gồm **giao thức (SAML)**, **thuộc tính được ánh xạ**, **chính sách đã chọn**, 
và **nhóm truy cập (Amazon Q)**. Tùy chọn **“Enable Advanced Configuration”** được làm nổi bật gần cuối bảng.

---

3. Cuộn xuống và chọn **Download Metadata**. Thao tác này sẽ lưu tệp *Metadata* vào máy tính của bạn; 
tệp này sẽ được sử dụng sau trong quá trình cấu hình.

4. Trong một tab trình duyệt khác, đăng nhập vào 
   [AWS IAM Identity Center console](https://console.aws.amazon.com/) của bạn và chọn  
   **Choose your identity source**.

5. Trong phần **Identity source**, chọn **Change identity source** từ menu thả xuống **Actions**.
![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-6.png)
**Hình 6 – Thay đổi nguồn danh tính trong Bảng điều khiển IAM Identity Center**

*Alt Text:* Ảnh chụp màn hình trang cài đặt của **IAM Identity Center**, tập trung vào tab  
“**Nguồn danh tính**”. Trang hiển thị các chi tiết như nguồn danh tính, phương thức xác thực,  
URL cổng truy cập AWS, URL nhà phát hành và ID kho danh tính. Một menu thả xuống có nhãn  
**“Hành động”** được mở rộng ở góc trên bên phải, hiển thị các tùy chọn  
**“Tùy chỉnh URL cổng truy cập AWS”** và **“Thay đổi nguồn danh tính”**, được đánh dấu bằng khung màu đỏ.

6. Trên trang tiếp theo, chọn **External identity provider** và nhấn **Next**.

7. Trong phần **Service provider metadata**, sao chép  
   **IAM Identity Center Assertion Consumer Service (ACS) URL**.

![docflow1](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-7.png)

---
**Hình 7 – Sao chép URL ACS của Trung tâm Nhận dạng IAM**

*Alt Text:* Ảnh chụp màn hình bước **“Configure external identity provider”** trong quá trình thiết lập  
AWS IAM Identity Center. Màn hình hiển thị thông tin *service provider metadata*, bao gồm  
**AWS access portal sign-in URL**, **IAM Identity Center Assertion Consumer Service (ACS) URL**  
(được đánh dấu bằng khung đỏ), và **IAM Identity Center issuer URL**.  
Một nút có nhãn **“Download metadata file”** được hiển thị ở góc trên bên phải.

8. Bây giờ, quay lại tab trình duyệt **PingIdentity**, điều hướng đến tab  
   **Configuration**, và chọn biểu tượng cây bút (*pencil icon*) để chỉnh sửa thông tin chi tiết.

9. Dán **ACS URL** mà bạn đã sao chép từ **IAM Identity Center console** và chọn **Save**.

![docflow8](https://d2908q01vomq)b2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/12/devops-16054-image-26.png

**Hình 8 – Cấu hình cài đặt SAML của AWS Single Sign-On trong bảng điều khiển PingIdentity**

*Alt Text:* Hai ảnh chụp màn hình hiển thị quá trình cấu hình và chỉnh sửa cài đặt **SAML** cho  
**AWS Single Sign-On** trong **PingIdentity**. Ảnh đầu tiên hiển thị giao diện cấu hình tĩnh,  
liệt kê **ACS URL**, **signing key** (*“PingOne SSO Certificate for Administrators environment”*),  
**signing method** (*“Response”*), và **signing algorithm**.  
Ảnh thứ hai hiển thị giao diện chỉnh sửa, trong đó ô nhập **ACS URL** được đánh dấu bằng khung đỏ,  
cùng với các menu thả xuống để chọn signing key, tùy chọn signing method (*Assertion, Response hoặc cả hai*),  
và thuật toán **RSA_SHA256**. Các màn hình này hướng dẫn người dùng thiết lập hợp SAML bảo mật với AWS SSO.
## Bước 3: Cấu hình PingIdentity làm nhà cung cấp danh tính (IdP) bên ngoài trong IAM Identity Center

Bước này bao gồm việc thiết lập **PingIdentity** làm **IdP bên ngoài** trong  
**IAM Identity Center** để kích hoạt truy cập liên kết (*federated access*).

1. Quay lại tab trình duyệt trước đó nơi bạn đã mở bảng điều khiển  
   **IAM Identity Center**.

2. Tải lên tệp **PingIdentity IdP SAML metadata** đã tải xuống từ **bước 3 của phần trước**,  
   rồi chọn **Next**.

![docflow9](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-10.png)
**Hình 9 – AWS IAM Identity Center metadata**

*Alt Text:* Ảnh chụp màn hình của giao diện cấu hình **AWS Identity Center** nơi người dùng  
tải lên tệp **IdP SAML metadata XML**. Tệp metadata được chọn thành công. Bên dưới là các trường  
trống để nhập thủ công **IdP sign-in URL**, **IdP issuer URL**, và **IdP certificate**.  
Nút **“Next”** được tô sáng màu cam ở góc dưới bên phải, biểu thị bước tiếp theo trong quá trình thiết lập.

3. Xem lại danh sách các thay đổi. Khi đã sẵn sàng, nhập **ACCEPT**, rồi chọn  
   **Change identity source**.

---

## Bước 4: Bật cấp phát (provisioning) và các phiên làm việc nhận thức danh tính  
*(identity-aware sessions)* trong IAM Identity Center

Bước này bao gồm việc **cấu hình cấp phát người dùng** và **bật các phiên làm việc nhận thức danh tính**  
trong **AWS IAM Identity Center** để hỗ trợ kiểm soát truy cập động.

1. Trong bảng điều khiển  
   [IAM Identity Center Console](https://console.aws.amazon.com/),  
   chọn **Settings** ở thanh điều hướng bên trái.

2. Trên trang **Settings**, tìm và **bật tính năng automatic provisioning**.  
   Hành động này sẽ **kích hoạt ngay lập tức** quá trình cấp phát tự động trong  
   **IAM Identity Center** và hiển thị thông tin cần thiết bao gồm:
- **SCIM endpoint**
- **Access token**

3. Trong hộp thoại **Inbound automatic provisioning**, sao chép từng giá trị của các tùy chọn trên.  
   Bạn sẽ cần **dán các giá trị này vào PingIdentity** khi cấu hình phần cấp phát sau này.

4. Chọn **Close**.

5. Tiếp theo, bật các tùy chọn **identity-aware sessions** và **automatic provisioning**.

![docflow10](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-11.png)

**Hình 10 – Cài đặt IAM Identity Center cho identity-aware sessions và automatic provisioning**

*Alt Text:* Hai tùy chọn được hiển thị để cấu hình thêm: **“Enable identity-aware sessions”**  
và **“Automatic provisioning.”** Cả hai đều có nút **“Enable”** ở phía bên phải, được đánh dấu màu đỏ.
## Bước 5: Cấu hình kết nối cấp phát (Provisioning Connections) trong PingIdentity

Bước này hướng dẫn cách thiết lập **kết nối cấp phát trong PingIdentity**  
để kích hoạt việc **quản lý người dùng và nhóm tự động**.

1. Trong bảng điều khiển **PingIdentity**, điều hướng đến  
   **Integrations > Provisioning**.

2. Chọn biểu tượng **dấu cộng (+)** → **New Connection**.

3. Trong mục **Connection Type**, chọn **Identity Store**.
![docflow11](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-12.png)

**Hình 11 – Cấu hình kết nối Provisioning trong PingIdentity**

*Alt Text:* Màn hình cấu hình **Provisioning** trong **PingIdentity**. Thanh bên trái tô sáng tab  
Provisioning, bảng chính hiển thị hộp thoại **Create a New Connection** với hai tùy chọn  
**Identity Store** và **Gateway**. Identity Store được chọn bằng nút **Select**, biểu tượng dấu cộng (+)  
ở trên cùng cho phép thêm kết nối mới.

4. Chọn **SCIM outbound** từ danh sách tùy chọn và nhấn **Next**.

5. Nhập tên cho kết nối và nhấn **Next**.

6. Dán **SCIM endpoint URL** vào trường **SCIM BASE URL**.

7. Trong phần **Authentication Method**, chọn **OAuth 2 Bearer Token**.

8. Dán **Access Token** vào trường **OAuth Access Token**.
9. Nhấn **Test Connection** để kiểm tra kết nối, sau đó chọn **Next**.

![docflow12](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-13.png)

**Hình 12 – Cấu hình chi tiết xác thực (Authentication Details)**

*Alt Text:* Giao diện PingIdentity hiển thị bước cấu hình xác thực với các trường thông tin  
về **SCIM Base URL**, **SCIM Version (2.0)**, **OAuth 2 Bearer Token**, và **OAuth Access Token**.  
Nút **Test Connection** và **Next** hiển thị phía dưới.

---

10. Điều hướng đến **User Filter Expression** và thay đổi giá trị thành:

userName Eq "%s".
11. Chọn **Save**. (Theo mặc định, kết nối được tạo ở trạng thái **Disabled**.)

![docflow13](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-14.png)

**Hình 13 – Chỉnh sửa User Filter Expression cho kết nối**

*Alt Text:* Bước cuối của trình hướng dẫn **Create a New Connection** trong PingIdentity hiển thị  
cấu hình **User Filter Expression**, **User Identifier**, và tùy chọn nhóm.  
Nút **Save** được tô sáng ở góc dưới bên phải.

---

12. Chọn kết nối bạn vừa tạo và **bật công tắc (toggle switch)** để kích hoạt kết nối.
![docflow14](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-15.png)

**Hình 14 – Kích hoạt kết nối**

*Alt Text:* Màn hình cấu hình PingIdentity hiển thị tích hợp với **IAM Identity Store**,  
có công tắc bật/tắt ở góc trên bên phải được đánh dấu cho thấy kết nối đang được bật.

---

## Bước 6: Cấu hình quy tắc cung cấp (provisioning rules) trong PingIdentity

Bước này bao gồm việc thiết lập các quy tắc cung cấp trong **PingIdentity**  
để xác định cách người dùng và nhóm được đồng bộ hóa.

1. Trong bảng điều khiển **PingIdentity**, điều hướng đến **Integrations > Provisioning**.
2. Chọn **biểu tượng dấu cộng** > **New Rule**.

3. Nhập **Tên** và **Mô tả** cho quy tắc.

4. Chọn **Create**.

5. Chọn **biểu tượng dấu cộng** để chọn **Kết nối (Connection)** bạn đã tạo ở bước trước.

6. Chọn **Save**.

![docflow15](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/12/devops-16054-image-27.png)

**Hình 15 – Thêm kết nối IAM Identity Center vào quy tắc**

*Alt Text:* Ảnh chụp màn hình hiển thị các bước cuối cùng trong việc kết nối  
IAM Identity Center với IAM Identity Store bằng PingIdentity.  
Hình đầu tiên hiển thị kết nối IAM Identity Store được liệt kê trong phần **Available Connections**  
với biểu tượng **dấu cộng (+)** để bắt đầu liên kết.  
Hình thứ hai hiển thị kết nối đã chọn từ **PingOne Directory (P1)** làm nguồn  
và **IAM Identity Store (SCIM)** làm đích, với tùy chọn **“Save”** để lưu cấu hình.
7. Nếu bạn muốn đồng bộ người dùng từ thư mục PingIdentity, hãy tạo **bộ lọc người dùng (User Filter)**.  
   Để thực hiện, điều hướng đến **User Filter** và chọn biểu tượng bút chì để chỉnh sửa.

8. Chọn bộ lọc phù hợp từ danh sách thả xuống tùy theo trường hợp sử dụng của bạn và chọn **Save**.  
   Ở đây, tôi đã chọn **Group Name** được chỉ định cho quyền truy cập **Amazon Q Developer**.

![docflow16](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-18.png)

**Hình 16 – Bộ lọc người dùng trong PingIdentity**

*Alt Text:* Ảnh chụp màn hình giao diện **“Edit User Filter”** trong IAM Identity Center.  
Bộ lọc người dùng được cấu hình để cung cấp người dùng thuộc các nhóm có tên chứa  
**“Amazon Q Developer”**. Logic điều kiện được đặt thành khớp nếu **“Any”** (bất kỳ) điều kiện nào đúng.

9. Nếu bạn muốn đồng bộ một nhóm từ thư mục PingIdentity, hãy tạo **group provisioning**.  
   Để làm vậy, điều hướng đến **Group Provisioning** và chọn biểu tượng bút chì để chỉnh sửa.

10. Chọn nhóm thích hợp được chỉ định cho quyền truy cập **Amazon Q Developer** và chọn **Save**.
![docflow17](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-19.png)

**Hình 17 – Cấu hình Group Provisioning trong PingIdentity**

*Alt Text:* Ảnh chụp màn hình giao diện **“Edit Group Provisioning”** trong  
IAM Identity Center. Nhóm **“Amazon Q Developer”** được chọn cho việc cung cấp ra ngoài.  
Nút **“Save”** được tô sáng ở góc dưới bên trái.

11. Điều hướng đến **Attribute Mapping** và chọn biểu tượng bút chì để chỉnh sửa cài đặt.

12. Xóa thuộc tính **Primary Phone** trong **PingOne Directory**.
13. Thêm một thuộc tính mới và chọn **Username** làm thuộc tính từ **PingOne Directory**  
    và **displayName** làm thuộc tính trong **IAM Identity Store**.

14. Chọn **Save**.

![docflow18](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/12/devops-16054-image-28.png)

**Hình 18 – Cấu hình ánh xạ thuộc tính (Attribute Mapping) trong PingIdentity**

*Alt Text:* Ảnh chụp màn hình giao diện **“Edit Attribute Mapping”** của IAM Identity Center  
trong PingIdentity. Màn hình hiển thị danh sách ánh xạ thuộc tính giữa **PingOne Directory**  
và **IAM Identity Store**, bao gồm các thuộc tính như *Family Name*, *Given Name*, *Username*,  
*Email Address*, và *Primary Phone*. Một ánh xạ mới được thêm vào với  
**Username → displayName**. Nút **Add** và nút **Save** được đánh dấu để hướng dẫn thao tác.
15. Chọn quy tắc bạn đã tạo và bật **công tắc (toggle)** để kích hoạt quy tắc.

Điều này sẽ tự động cung cấp người dùng/nhóm từ PingIdentity sang IAM Identity Center thông qua **SCIM**.

![docflow19](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/12/devops-16054-image-29.png)

**Hình 19 – Trạng thái đồng bộ người dùng và nhóm từ PingIdentity qua SCIM**

*Alt Text:* Bản tóm tắt đồng bộ IAM Identity Center hiển thị việc cung cấp người dùng và nhóm thành công.  
Hình đầu tiên tô sáng hai người dùng bị ảnh hưởng và đã đồng bộ thành công.  
Hình thứ hai tô sáng một nhóm đã được đồng bộ thành công.  
Trạng thái đồng bộ được đánh dấu là **“ACTIVE”**, xác nhận tích hợp giữa PingOne và AWS IAM Identity Center đã thành công.
## Bước 7: Cấp quyền truy cập cho Amazon Q Developer

Bước này bao gồm việc xác định và đăng ký (subscribe) các nhóm cần được cấp quyền sử dụng **Amazon Q Developer**.

1. Trong bảng điều khiển  
   [Amazon Q Developer console](https://console.aws.amazon.com/amazonq/),  
   tại mục **Subscriptions**, thêm các nhóm của **IAM Identity Center** cần quyền truy cập vào Amazon Q Developer.

2. Chọn **Subscribe** và tìm kiếm tên nhóm.

3. Chọn **Assign**.

![docflow20](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-24.png)

**Hình 20 – Trang đăng ký Amazon Q Developer**  
*Alt Text:* Ảnh chụp màn hình trang “Subscriptions” của Amazon Q Developer trong AWS Management Console.  
Tab “Groups” được chọn, hiển thị “Amazon Q Developer” với trạng thái đăng ký là **Subscribed**.  
Nhóm “Amazon Q Developer” được đánh dấu bằng khung đỏ.


---

## Thiết lập Amazon Q Developer với IAM Identity Center

Phần này hướng dẫn bạn cài đặt tiện ích mở rộng Amazon Q Developer và thiết lập xác thực thông qua IAM Identity Center.

1. Để cài đặt **extension Amazon Q Developer** trong môi trường phát triển tích hợp (IDE), hãy hoàn tất các bước được mô tả trong  
   [AWS documentation](https://docs.aws.amazon.com).

2. Sau khi tiện ích được cài đặt, chọn biểu tượng **Amazon Q** trong IDE của bạn.
3. Chọn một tùy chọn đăng nhập (**Sign-in option**).

4. Chọn **Use with Pro license** rồi chọn **Continue**.

5. Tiếp tục bằng cách nhấn **Continue**.

6. Nhập **Start URL** — bạn có thể lấy URL này (AWS access portal URL) từ  
   bảng điều khiển **IAM Identity Center Console**.

![docflow21](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-25.png)

**Hình 21 – URL cổng truy cập IAM Identity Center**

*Alt Text:* Ảnh chụp màn hình trang cài đặt của IAM Identity Center trong AWS Console,  
hiển thị cấu hình nguồn danh tính. Phần **“Identity source”** được thiết lập là  
**External identity provider** sử dụng xác thực SAML 2.0 và cung cấp SCIM.  
Khung đỏ làm nổi bật **AWS access portal URL** và **Identity Store ID**.  
Tab **“Settings”** được chọn từ bảng điều hướng bên trái.

7. Nhập **Region** nơi lưu trữ thư mục danh tính, rồi chọn **Continue**.

8. Chọn **Open** trên cửa sổ bật lên để chuyển hướng đến trình duyệt.

9. Trình duyệt sẽ dẫn bạn đến URL **PingOne**, nơi bạn nhập thông tin  
   đăng nhập **PingIdentity**, sau đó chọn **Sign On**.

10. Khi xác thực thành công, chọn **Allow access** trong cửa sổ bật lên  
    để hoàn tất đăng nhập.
![docflow22](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-26-3.gif)

**Hình 22 – Thiết lập tiện ích mở rộng Amazon Q Developer trong Visual Studio Code**

*Alt Text:* Một bản ghi màn hình trong Visual Studio Code cho thấy người dùng  
chọn biểu tượng Amazon Q ở thanh bên trái. Màn hình chuyển sang cửa sổ đăng nhập,  
hiển thị thông báo rằng người dùng phải xác thực bằng thông tin đăng nhập  
PingIdentity thông qua IAM Identity Center trước khi có thể truy cập các  
tính năng của Amazon Q Developer. Thông báo nhấn mạnh rằng việc xác thực là  
bắt buộc để tiếp tục.

---

## Kiểm tra cấu hình

Sau khi hoàn thành bước trước thành công, bạn có thể tận dụng các gợi ý mã  
từ Amazon Q Developer.
![docflow23](https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-27.gif)

**Hình 23 – Ví dụ về Amazon Q Developer**

*Alt Text:* Bản ghi màn hình của Visual Studio Code cho thấy Amazon Q Developer tạo ra  
một đoạn mã mẫu trực tiếp trong trình soạn thảo.

---

## Dọn dẹp

Để tránh phát sinh chi phí sau khi kiểm thử giải pháp này, hãy thực hiện các bước sau để  
xóa tất cả tài nguyên đã được cấp phát:

---

### 1. Xóa cấu hình ứng dụng PingIdentity

- Trong bảng điều khiển **PingIdentity**, điều hướng đến **Applications**.  
- Tìm và xóa **ứng dụng AWS Single Sign-On** đã được cấu hình cho **IAM Identity Center**.

---

### 2. Đặt lại cấu hình IAM Identity Center

- Trong bảng điều khiển **AWS IAM Identity Center**:

  - Điều hướng đến **Settings → Identity source**.
  - Thay đổi **nguồn danh tính** về **IAM Identity Center directory mặc định**  
    nếu bạn không còn sử dụng PingIdentity.

### 3. Thu hồi đăng ký và quyền truy cập

- Trong bảng điều khiển **Amazon Q Developer**:

  - Truy cập **Subscriptions** và xóa các nhóm được gán như  
    **Amazon Q Developer** hoặc **CodeWhisperer trial**.

  - Thao tác này sẽ **hủy kích hoạt quyền truy cập** và ngăn chặn các khoản phí phát sinh trong tương lai.

---

### 4. Gỡ tiện ích mở rộng Amazon Q Developer

- Nếu muốn, bạn có thể **gỡ cài đặt tiện ích mở rộng Amazon Q Developer** khỏi  
  Visual Studio Code để hoàn nguyên hoàn toàn môi trường phát triển.

---

## Kết luận

Trong bài viết này, chúng tôi đã trình bày cách sử dụng **thông tin xác thực PingIdentity**  
hiện có để truy cập **Amazon Q Developer** thông qua tích hợp với **IAM Identity Center**.

Chúng tôi đã cung cấp **hướng dẫn chi tiết từng bước** để cấu hình  
**PingIdentity làm nhà cung cấp danh tính bên ngoài (IdP)** cho IAM Identity Center.

Cuối cùng, chúng tôi đã mô tả cách kết nối **tiện ích mở rộng Amazon Q Developer**  
trong IDE của bạn với AWS bằng thông tin đăng nhập PingIdentity,  
giúp bạn truy cập Amazon Q Developer một cách liền mạch.

Nếu bạn có bất kỳ nhận xét hoặc câu hỏi nào, hãy chia sẻ trong phần bình luận bên dưới.

---

## Để tìm hiểu thêm về các dịch vụ AWS

- [Amazon Q Developer](https://aws.amazon.com/q/)
- [IAM Identity Center](https://aws.amazon.com/iam/identity-center/)
- [AWS Toolkit for Visual Studio Code](https://docs.aws.amazon.com/toolkit-for-vscode/)
## Về tác giả

<div style="display: flex; align-items: flex-start; gap: 16px;">

<img src="https://d2908q01vomqb2.cloudfront.net/7719a1c782a1ba91c031a682a0a2f8658209adbf/2025/06/11/devops-16054-image-28.jpg" alt="Sid Vantair" style="width:120px; border-radius:8px;">

<div>

Sid Vantair là một **Kiến trúc sư Giải pháp (Solutions Architect)** tại AWS, phụ trách các tài khoản chiến lược.  
Anh luôn tận tâm trong việc giải quyết các vấn đề kỹ thuật phức tạp nhằm giúp khách hàng vượt qua những trở ngại.

Ngoài công việc, anh trân trọng thời gian bên gia đình và khuyến khích tinh thần ham học hỏi ở các con của mình.

</div>

</div>
