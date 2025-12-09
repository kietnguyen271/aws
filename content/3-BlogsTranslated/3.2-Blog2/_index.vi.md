---
title: "Blog 2"
date: ""
weight: 1
chapter: false
pre: " <b> 3.2. </b> "
---
[**AWS for Industries**](https://aws.amazon.com/industries/)

# Trợ lý Ảo Năng Lượng: Chuyển đổi dịch vụ khách hàng ngành tiện ích với AWS Generative AI

*by Darren Roback and Jeremy Cianella on 10 JUL 2025 in  
[Amazon Bedrock](https://aws.amazon.com/bedrock/), [Amazon Bedrock Guardrails](https://aws.amazon.com/bedrock/guardrails/),  
[Amazon CloudWatch](https://aws.amazon.com/cloudwatch/), [Amazon Connect](https://aws.amazon.com/connect/),  
[Amazon DynamoDB](https://aws.amazon.com/dynamodb/), [Amazon Lex](https://aws.amazon.com/lex/),  
[Amazon Timestream](https://aws.amazon.com/timestream/), [AWS Lambda](https://aws.amazon.com/lambda/),  
Energy (Oil & Gas), Industries – Permalink*

Trong kỷ nguyên chuyển đổi số ngày càng mạnh mẽ, các nhà cung cấp dịch vụ điện (electric utility providers) đang phải đối mặt với những thách thức chưa từng có trong việc đáp ứng kỳ vọng của khách hàng về dịch vụ nhanh chóng, hiệu quả và dễ tiếp cận.

Các utility contact centers đặc biệt gặp khó khăn trong việc cung cấp dịch vụ khách hàng hiệu quả. Hệ thống **Interactive Voice Response (IVR)** truyền thống có thể xử lý tốt các yêu cầu trực tiếp, rõ ràng thông qua thao tác phím bấm hoặc các lệnh thoại cơ bản — chẳng hạn như báo mất điện hoặc kiểm tra số dư tài khoản. Tuy nhiên, những lệnh phức tạp, mơ hồ và nhiều lượt (multi-turn) thường phải chuyển sang nhân viên thật, tạo ra nút thắt trong quá trình phục vụ.

Giới hạn này trở nên nghiêm trọng hơn trong các sự kiện thời tiết khắc nghiệt hoặc khi mất điện trên diện rộng, khi lượng cuộc gọi có thể tăng vọt chỉ trong vài phút — thường khiến ngay cả những trung tâm được bố trí đầy đủ nhân sự cũng bị quá tải. Thách thức càng gia tăng bởi nhu cầu tích hợp với nhiều hệ thống backend khác nhau như **Customer Information Systems (CIS)**, **Billing Information System (BIS)**, **Meter Data Management Systems (MDMS)**, **Outage Management Systems (OMS)** và nhiều knowledge bases để xử lý các yêu cầu của khách hàng. Tất cả những điều này dẫn đến thời gian xử lý lâu hơn và làm tăng khả năng gây thất vọng cho khách hàng.

Các utility contact centers hiện đại cần xử lý hiệu quả những gì chúng tôi gọi là **“ABCDs”** trong dịch vụ khách hàng ngành tiện ích. Mỗi chức năng cốt lõi này đòi hỏi phải truy cập vào các hệ thống khác nhau và kiến thức chuyên ngành cụ thể:

- **Account management**: Mở, đóng, truy xuất và cập nhật thông tin tài khoản khách hàng; thường cần tương tác với CIS.
- **Billing**: Truy xuất số dư tài khoản, lịch sử hóa đơn và xử lý thanh toán — thường cần tương tác với BIS.

- **Consumption**: Phân tích và giải thích các mô hình tiêu thụ năng lượng từ dữ liệu smart meter — thường cần tương tác với MDMS.

- **Dispatch**: Tạo yêu cầu dịch vụ, báo cáo sự cố/mất điện, kiểm tra trạng thái yêu cầu và lên lịch bảo trì — thường cần tương tác với OMS.

Để quản lý hiệu quả các chức năng này, các customer service representatives cần được đào tạo chuyên sâu và có kinh nghiệm. Họ phải hiểu cấu trúc giá điện phức tạp, phân giải dữ liệu tiêu thụ năng lượng, giải thích cách tính hóa đơn, và điều hướng các chính sách và quy trình phức tạp. Kiến thức chuyên sâu này mất nhiều tháng để hình thành, dẫn đến thời gian onboarding dài cho nhân viên mới và khó duy trì chất lượng dịch vụ nhất quán trên mọi tương tác với khách hàng.

Những hạn chế của hệ thống IVR truyền thống và sự phức tạp trong vận hành ngành tiện ích đã mở ra cơ hội cho các giải pháp tiên tiến dựa trên AI. Generative AI — với khả năng hiểu ngôn ngữ tự nhiên, xử lý truy vấn phức tạp, và tạo ra phản hồi giống con người — mang đến hướng tiếp cận đầy hứa hẹn cho những thách thức này.

Các **Large Language Models (LLMs)** cho phép chúng ta xây dựng virtual assistants có khả năng hiểu ý định của khách hàng chính xác hơn, xử lý phạm vi yêu cầu rộng hơn mà không cần sự can thiệp của con người, đồng thời đưa ra các phản hồi tinh tế và nhận thức ngữ cảnh tốt hơn.

Để giải quyết những thách thức này, chúng tôi đã phát triển **Energy Virtual Assistant** — một cách tiếp cận tiên tiến cho dịch vụ khách hàng, kết hợp Generative AI với agentic capabilities, hỗ trợ ra quyết định thông minh thông qua chain-of-thought reasoning và tích hợp với các hệ thống năng lượng cũng như tận dụng dữ liệu hiện có của doanh nghiệp tiện ích. Giải pháp này kết hợp sức mạnh của **LLMs** với kiến thức chuyên ngành và khả năng tích hợp hệ thống các thiết bị trong dịch vụ khách hàng ngành năng lượng.

Trong bài viết này, chúng tôi sẽ thảo luận về lợi ích của Generative AI trong dịch vụ khách hàng, trình bày kiến trúc của giải pháp, cung cấp hướng dẫn triển khai từng bước, và chia sẻ các bước tiếp theo để triển khai giải pháp này trong contact center của các nhà cung cấp tiện ích.
## Lợi ích của Generative AI trong dịch vụ khách hàng

Generative AI đại diện cho một bước nhảy vọt trong công nghệ dịch vụ khách hàng,  
mang đến một bộ năng lực toàn diện giúp định hình lại cách các tổ chức tương tác với  
khách hàng của họ.

### Cá nhân hóa ở quy mô lớn

Không giống các hệ thống truyền thống, generative AI có thể tạo ra các phản hồi  
không chỉ chính xác mà còn được cá nhân hóa theo phong cách giao tiếp, sở thích và  
lịch sử tương tác của từng khách hàng. Các tiếp cận này biến dịch vụ khách hàng  
thông thường thành một trải nghiệm tương tác tinh tế và mang tính cá nhân cao,  
khiến khách hàng cảm thấy được thấu hiểu thực sự.

### Trí tuệ cảm xúc và phân tích cảm xúc

Nhờ vào khả năng sentiment analysis nâng cao, generative AI có thể phát hiện  
những sắc thái tinh tế trong giao tiếp của khách hàng, từ đó điều chỉnh giọng điệu  
và cách tiếp cận phù hợp với trạng thái cảm xúc của họ. Dù khách hàng thể hiện sự tức giận,  
bối rối hay hài lòng, hệ thống đều có thể đưa ra phản hồi đồng cảm và phù hợp với  
ngữ cảnh, phản ánh mức độ thấu hiểu gần như con người.

### Năng lực đa ngôn ngữ và hiểu biết văn hóa

Generative AI phá vỡ rào cản ngôn ngữ bằng khả năng hỗ trợ đa ngôn ngữ mạnh mẽ  
với độ trôi chảy gần như người bản xứ ở nhiều ngôn ngữ khác nhau.  
Hơn cả việc dịch thuật, khả năng này còn đảm bảo giao tiếp phù hợp về mặt văn hóa,  
hiểu được những sắc thái ngữ cảnh và ngôn ngữ mà các công cụ dịch truyền thống thường bỏ qua.  
Điều này tạo nên trải nghiệm dịch vụ khách hàng mang tính toàn cầu nhưng vẫn gần gũi  
và được cá nhân hóa theo từng khu vực.

### Tích hợp dữ liệu thống nhất

Generative AI hoạt động như một bộ điều phối dữ liệu tinh vi, tích hợp liền mạch  
thông tin từ các silo dữ liệu trong tổ chức. Việc tạo ra một cái nhìn toàn diện về  
### Hiệu quả vận hành và tối ưu chi phí

Generative AI còn có thể thúc đẩy hiệu quả chi phí đáng kể bằng cách giảm thời gian  
xử lý trung bình (handle time), tăng tỷ lệ giải quyết ngay trong lần liên hệ đầu tiên  
(first-contact resolution rate), tự động hóa toàn diện các yêu cầu thường xuyên và  
loại bỏ chi phí làm thêm giờ nhờ khả năng sẵn sàng 24/7.

### Khả năng mở rộng vượt trội

Các nền tảng dịch vụ khách hàng trong ngành tiện ích thường gặp khó khăn với biến  
động về khối lượng tương tác, đặc biệt trong các tình huống khẩn cấp như mất điện  
diện rộng, bão lũ hoặc cháy rừng.  
Các contact center sử dụng generative AI có thể nhanh chóng mở rộng quy mô khi nhu cầu tăng  
đột biến, duy trì chất lượng dịch vụ ổn định dù phải xử lý mười hay mười nghìn tương tác cùng lúc.  
Tính linh hoạt này đảm bảo khả năng hỗ trợ khách hàng đáng tin cậy trong mọi tình huống.

### Khả năng hoạt động liên tục

Điều quan trọng nhất là generative AI cho phép cung cấp dịch vụ khách hàng liên tục  
24/7 mà không cần chi phí cao cho mô hình nhân sự truyền thống.  
Khách hàng có thể truy cập hỗ trợ bất kỳ lúc nào, từ đó cải thiện đáng kể khả năng  
tiếp cận dịch vụ và mức độ hài lòng tổng thể.

## Tổng quan giải pháp

Energy Virtual Assistant được thiết kế để hỗ trợ các tình huống sử dụng phổ biến  
trong dịch vụ khách hàng ngành tiện ích, chẳng hạn như báo cáo sự cố mất điện,  
kiểm tra dữ liệu phân tích mức tiêu thụ, cập nhật thông tin tài khoản và xử lý  
thanh toán hóa đơn.

Khách hàng trải nghiệm với Energy Virtual Assistant thông qua nhiều kênh  
(voice hoặc chat), được hỗ trợ bởi Generative AI để cung cấp phản hồi nhanh chóng,  
chính xác, thông minh và mang tính cá nhân hóa.
Energy Virtual Assistant được xây dựng bằng cách kết hợp nhiều dịch vụ của Amazon Web Services (AWS), bao gồm:

- **[Amazon Connect](https://aws.amazon.com/connect/)**: Là giải pháp contact center trên nền tảng đám mây, cho phép doanh nghiệp cung cấp dịch vụ khách hàng ở bất kỳ quy mô nào. Nó hỗ trợ giao tiếp đa kênh (omni-channel communication), định tuyến theo kỹ năng (skills-based routing), và các tính năng được hỗ trợ bởi AI như conversational IVR. Amazon Connect đóng vai trò là điểm đầu vào cho khách hàng khi họ tương tác với Energy Virtual Assistant qua kênh thoại.

- **[Amazon Lex](https://aws.amazon.com/lex/)**: Là dịch vụ AI được quản lý toàn phần, cho phép xây dựng giao diện hội thoại (chatbot) bằng giọng nói hoặc văn bản. Nó sử dụng công nghệ nền tảng của Amazon Alexa. Amazon Lex đóng vai trò là lớp giao diện hội thoại cho Energy Virtual Assistant, giúp hiểu và xử lý ngôn ngữ tự nhiên (natural language understanding).

- **[Amazon Bedrock](https://aws.amazon.com/bedrock/)**: Là dịch vụ được quản lý toàn phần cung cấp quyền truy cập vào các foundation models (FMs) hiệu năng cao thông qua một unified API. Amazon Bedrock mang đến năng lực Generative AI cho Energy Virtual Assistant.

- **[Agents for Amazon Bedrock](https://aws.amazon.com/bedrock/agents/)**: Là tính năng cho phép người dùng tạo các AI agents có khả năng tự động phân tích và thực thi các tác vụ nghiệp vụ phức tạp bằng cách điều phối FMs, knowledge bases và APIs. Energy Virtual Assistant là một Bedrock agent sử dụng chain-of-thought reasoning và tích hợp với các hệ thống năng lượng để xử lý yêu cầu khách hàng.

- **[AWS Lambda](https://aws.amazon.com/lambda/)**: Là dịch vụ serverless compute thực thi mã nguồn khi có sự kiện xảy ra. Lambda đóng vai trò quan trọng trong Agents for Amazon Bedrock bằng cách cung cấp lớp xử lý (compute layer) cho các API integration tới các hệ thống năng lượng backend.

- **[Amazon DynamoDB](https://aws.amazon.com/dynamodb/)**: Là dịch vụ NoSQL database được quản lý toàn phần, không cần máy chủ (serverless), cung cấp hiệu năng nhanh, ổn định và khả năng mở rộng linh hoạt. Trong giải pháp này, DynamoDB được sử dụng để mô phỏng các hệ thống năng lượng như Account, Billing và Outage Management.
- **[Amazon Timestream](https://aws.amazon.com/timestream/)**: Là dịch vụ cơ sở dữ liệu time series được quản lý toàn phần, được thiết kế để thu thập, lưu trữ và phân tích dữ liệu chuỗi thời gian. Trong trường hợp này, Timestream được dùng để lưu trữ dữ liệu tiêu thụ năng lượng, thường được gọi là MDMS.

- **[Guardrails for Amazon Bedrock](https://docs.aws.amazon.com/bedrock/latest/userguide/guardrails.html)**: Là tính năng giúp triển khai các biện pháp kiểm soát an toàn như lọc nội dung, chặn chủ đề, lọc từ khóa, và ẩn thông tin nhạy cảm nhằm bảo vệ tương tác giữa khách hàng và foundation model (FM).

Trước khi đi vào phần kiến trúc giải pháp, hãy xem cách giải pháp này thay đổi các tình huống dịch vụ khách hàng trong ngành tiện ích.  
Giả sử một khách hàng gọi điện phản ánh về hóa đơn cao bất thường. Trước đây, nhân viên phải kiểm tra nhiều hệ thống khác nhau: lịch sử sử dụng điện, biểu đồ đo, biểu giá điện, thậm chí là dữ liệu thời tiết. Với giải pháp của chúng tôi, virtual assistant có thể tự động thu thập và phân tích toàn bộ thông tin đó, đồng thời cung cấp lời giải thích và khuyến nghị phù hợp theo ngữ cảnh cho khách hàng — hoàn toàn không cần sự can thiệp của con người.

## **Kiến trúc giải pháp**

Kiến trúc giải pháp dưới đây mô tả tổng quan về các thành phần được sử dụng trong hệ thống này, và để minh họa, chúng ta sẽ cùng xem xét từng phần.

![solution-architecture](https://d2908q01vomqb2.cloudfront.net/c5b76da3e608d34edb07244cd9b875ee86906328/2025/07/10/Figure-1-Solution-architecture.png)

**Hình 1: Solution architecture**

1. **Khách hàng sử dụng điện (energy customers)** tương tác với nhà cung cấp tiện ích (utility provider) thông qua nhiều loại yêu cầu phổ biến qua kênh thoại (voice channel). Mặc dù giải pháp này được thiết kế để phục vụ khách hàng sử dụng thoại, nó có thể mở rộng để hỗ trợ các kênh khác trong tương lai.
2. Khách hàng gọi vào Amazon Connect và ngay lập tức được đưa vào một luồng tương tác (contact flow) trong Amazon Connect.

3. Một bot Amazon Lex được nhúng vào luồng Amazon Connect và đóng vai trò là giao diện speech-to-text cho khách hàng khi họ tương tác với Energy Virtual Assistant.

4. Energy Virtual Assistant được hỗ trợ bởi một Amazon Bedrock agent, có nhiệm vụ tiếp nhận, xử lý và phân loại các yêu cầu từ khách hàng.

5. Amazon Bedrock agent lấy lịch sử hội thoại của khách hàng, các hành động khả dụng và yêu cầu hiện tại, sau đó gửi đến một foundation model (FM) để xử lý.

6. Foundation model (FM) sử dụng chain-of-thought reasoning để xác định các bước thích hợp nhằm đáp ứng yêu cầu của khách hàng.

7. Các action groups đại diện cho các hành động và tích hợp mà agent có thể gọi để xử lý yêu cầu của khách hàng.  
Các hành động được cấu hình trong giải pháp này bao gồm:

   - **Account Action Group**: Thực hiện các hành động như mở tài khoản mới, truy xuất và cập nhật thông tin tài khoản, hoặc đóng tài khoản.

   - **Billing Action Group**: Thực hiện các hành động như truy xuất số dư tài khoản, lịch sử thanh toán, và xử lý các khoản thanh toán của khách hàng.

   - **Ticketing Action Group**: Thực hiện các hành động như tạo ticket dịch vụ, kiểm tra trạng thái ticket, truy xuất tất cả ticket của một tài khoản cụ thể, báo cáo sự cố mất điện và lập lịch bảo trì.

   - **Consumption Action Group**: Dùng để truy xuất thông tin tiêu thụ năng lượng, cung cấp tính chi phí dựa trên mức tiêu thụ, và giải thích các mô hình sử dụng năng lượng.

   - **Date/Time Action Group**: Dùng để xác định ngày giờ hiện tại và hỗ trợ các yêu cầu có phạm vi thời gian (range-based requests) mà FM cần để phục vụ khách hàng.

8. Khi các hành động cần thiết đã được thực hiện, một Amazon Bedrock FM sẽ được sử dụng để xử lý và chuẩn bị phản hồi cuối cùng gửi lại cho khách hàng.

## **Luồng cuộc gọi Amazon Connect**
Luồng cuộc gọi dưới đây mô tả các bước được thực hiện khi khách hàng tương tác với Amazon Connect qua kênh thoại. Để minh họa rõ hơn, chúng ta sẽ cùng xem xét chi tiết từng bước.

![Amazon Connect Call Flow](https://d2908q01vomqb2.cloudfront.net/c5b76da3e608d34edb07244cd9b875ee86906328/2025/07/10/Figure-2-Amazon-Connect-call-flow.jpg)

**Hình 2: Amazon Connect call flow**

1. Khi khách hàng bắt đầu vào luồng Amazon Connect, khối **Enable Logging** được sử dụng để bật ghi nhật ký (flow logs), các log này được lưu trữ trong **Amazon CloudWatch**. Flow logs rất hữu ích để khắc phục sự cố và theo dõi các bước cụ thể được thực hiện trong một cuộc gọi của khách hàng.

2. Khối **Set Language** được sử dụng để thiết lập ngôn ngữ và giọng nói cho chức năng text-to-speech (TTS) trong contact flow.

3. Khối **Main Menu** được sử dụng để phát lời chào ban đầu cho người gọi và cũng là nơi bot **Amazon Lex** (với các intents đã được cấu hình) được kích hoạt để tương tác với khách hàng.

---

Các intents cụ thể được sử dụng trong luồng này bao gồm:

- **EnergyVirtualAgentIntent**: đại diện cho Amazon Bedrock Energy Virtual Agent, được dùng để xử lý tất cả các yêu cầu của khách hàng như báo cáo sự cố mất điện, kiểm tra dữ liệu tiêu thụ, cập nhật thông tin tài khoản và xử lý thanh toán hóa đơn. Intent này được cấu hình với các utterances khác nhau để hiểu và phân loại các yêu cầu khác nhau mà khách hàng có thể nói với Amazon Bedrock agent.

- **SpeakToAgentIntent**: dùng để chuyển cuộc gọi trực tiếp của khách hàng đến nhân viên thật trong contact center. Intent này được cấu hình với các utterances tương ứng với các cụm từ khách hàng sử dụng khi họ muốn nói chuyện với nhân viên thật.

- **GoodbyeIntent**: được dùng để kết thúc tương tác của khách hàng với contact center. Intent này được cấu hình với các utterances kết thúc cuộc gọi.
4. Khối **Set Queue** được sử dụng để xác định hàng đợi (queue) mà khách hàng sẽ được đưa vào nếu họ muốn nói chuyện với nhân viên thật.

5. Khối **Transfer to Queue** được dùng để định tuyến khách hàng đến hàng đợi của nhân viên thật.

6. Khối **Queue at Capacity** được sử dụng để thông báo cho khách hàng biết rằng hàng đợi đã đầy, thông qua một thông báo phát bằng text-to-speech.

7. Khối **Goodbye Prompt** được sử dụng để gửi lời chào tạm biệt đến khách hàng thông qua thông báo text-to-speech.

8. Khối **Error Prompt** được sử dụng để thông báo cho khách hàng biết rằng có sự cố xảy ra trong luồng, cũng thông qua một thông báo text-to-speech.

---

## Điều kiện tiên quyết

Trước khi bắt đầu, hãy đảm bảo rằng trong môi trường của bạn đã có các thành phần sau:

- [Created an Amazon Connect instance](https://docs.aws.amazon.com/connect/latest/adminguide/amazon-connect-instances.html)  
  được sử dụng để hỗ trợ khách hàng tương tác với Energy Virtual Assistant thông qua kênh thoại đến (inbound voice channel).

- [Claimed an Amazon Connect phone number](https://docs.aws.amazon.com/connect/latest/adminguide/contact-center-phone-number.html)  
  được liên kết với luồng (flow) được tạo trong quá trình triển khai.

- [An Amazon Connect BasicQueue](https://docs.aws.amazon.com/connect/latest/adminguide/queues.html)  
  được tạo mặc định cùng với Amazon Connect.

- [Enabled access to Claude 3.5 Haiku](https://aws.amazon.com/bedrock/)  
  trong Amazon Bedrock, đóng vai trò là FM (foundation model) cho Amazon Bedrock agent.

  - Chúng tôi sử dụng hồ sơ suy luận Claude 3.5 (Claude 3.5 inference profile) trong giải pháp này, điều đó có nghĩa là bạn cần bật quyền truy cập mô hình tại các **AWS Regions** us-east-1, us-east-2 và us-west-2.

- [Permissions to deploy the AWS CloudFormation template](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-iam.html)
## Triển khai giải pháp

Quá trình triển khai giải pháp này bao gồm các bước sau:

1. **Tạo CloudFormation stack**, triển khai toàn bộ các thành phần cơ bản.

2. **Cấu hình EnergyVirtualAgent Lex Bot intent**, vì tại thời điểm viết bài này, Amazon Lex chưa hỗ trợ việc tạo intent thông qua CloudFormation hoặc API.

3. **Liên kết Amazon Connect phone number với Amazon Connect flow.**

Trong quá trình triển khai, hệ thống sẽ tự động tạo dữ liệu khách hàng giả lập trong các hệ thống **Account**, **Billing** và **Consumption**. Cụ thể, các bộ dữ liệu (datasets) được tạo bao gồm:

- **AccountData**: năm khách hàng giả định, bao gồm các trường dữ liệu như  
  **AccountNumber**, CustomerName, EmailAddress, MeterNumber, PhoneNumber, PremiseNumber, RateCode và ServiceAddress.

- **BillingData**: mười hai tháng dữ liệu thanh toán cho mỗi khách hàng, bao gồm  
  BillingDate, BillingStatus, **DueDate**, **EnergyCharge**, **KWhConsumption**,  
  ServiceCharge và TotalAmount.

- **IntervalData**: ba tháng dữ liệu tiêu thụ theo chu kỳ 15 phút cho mỗi đồng hồ đo, bao gồm  
  L1–L2–L3 Voltage và Current, **InstantaneousPower** và Frequency.

---

## Tạo CloudFormation stack

Trước khi bắt đầu, hãy clone  
**AWS Samples Energy Virtual Assistant GitHub repository**  
về máy cục bộ của bạn, vì chúng ta sẽ sử dụng tệp YAML CloudFormation cho phần đầu tiên của quá trình triển khai.

Thực hiện các bước sau:

1. Truy cập **AWS CloudFormation console** và chọn  
   **Create stack > With new resources (standard)**.
2. Trong phần **Prerequisite – Prepare template**, chọn **Choose an existing template** và sau đó chọn **Upload a template file**.

3. Nhấn **Choose file**, sau đó từ repository vừa clone, điều hướng đến thư mục  
   `\deployment` và tải lên tệp **cloudformation_template.yaml**.

4. Chọn **Next** để chuyển sang màn hình **Specify stack details**.

5. Trong trường **Stack name**, nhập **EnergyVirtualAssistant**.

6. Trong trường **ConnectInstanceArn**, nhập **Amazon Resource Name (ARN)** của Amazon Connect instance của bạn.

7. Trong trường **ConnectQueueArn**, nhập **ARN** của hàng đợi **Amazon Connect BasicQueue**.

8. Chọn **Next** để chuyển sang màn hình **Configure stack options**.

9. Chọn hộp kiểm xác nhận rằng **AWS CloudFormation** có thể tạo **IAM resources** với tên tùy chỉnh.

10. Chọn **Next** để tiếp tục đến màn hình **Review and create**.

11. Xem lại tất cả cấu hình, sau đó chọn **Submit** để tạo **CloudFormation stack**.

Sau khi hoàn tất nhập thông tin, khởi chạy stack và chờ cho đến khi trạng thái chuyển sang **CREATE_COMPLETE** trước khi tiếp tục các bước kế tiếp.

---

## Cấu hình Lex Bot

Khi quá trình triển khai **CloudFormation stack** hoàn tất, bạn có thể tiếp tục cấu hình **Amazon Bedrock agent intent** trong **Amazon Lex bot**.

Trong bước này, chúng ta sẽ sử dụng các giá trị **EnergyVirtualAgentId** và  
**EnergyVirtualAgentAliasId** được tạo ra từ kết quả (outputs) của **CloudFormation stack**.
Thực hiện các bước sau:

1. Truy cập **Amazon Lex console** và chọn **EnergyVirtualAgentLexBot** đã được tạo sẵn cho bạn.

2. Trong phần **Draft version** và **English (US)**, chọn **Intents**.

3. Chọn menu **Add intent** và chọn **Use built-in intent**.

4. Chọn **AMAZON.BedrockAgentIntent – GenAI feature** cho loại built-in intent.

5. Ở trường **Intent name**, nhập **EnergyVirtualAgentIntent** và chọn **Add**.

6. Ở trường **Description**, nhập:  
   **Intent to handle customer energy requests.**

7. Ở trường **Bedrock Agent ID**, nhập giá trị **EnergyVirtualAgentId** lấy từ phần output của CloudFormation.

8. Ở trường **Agent Alias ID**, nhập giá trị **EnergyVirtualAgentAliasId** lấy từ phần output của CloudFormation.

9. Truy cập thư mục `\deployment` trong repository đã clone và mở tệp  
   **agent_intent_utterances.md**.

10. Trong phần **Sample utterances**, chọn **Plain text** và dán toàn bộ nội dung utterances từ tệp vừa mở.

11. Chọn **Save intent** ở cuối màn hình.

12. Chọn **Build** ở góc trên bên phải màn hình để áp dụng các thay đổi.

13. Truy cập trang **Bot versions** và chọn **Create version**.

14. Chọn **Create** để tạo một phiên bản bot mới và chờ cho đến khi quá trình hoàn tất.

15. Trong phần **Deployment**, chọn **Aliases** và chọn **PROD alias**.

16. Chọn **Associate version with alias** và chọn phiên bản bot mới vừa tạo.

17. Chọn **Associate** để hoàn tất quá trình liên kết.

---

## Liên kết số điện thoại

Bây giờ bạn đã sẵn sàng để liên kết **Amazon Connect phone number** (đã được claim trước đó) với **contact flow** được tạo trong quá trình triển khai CloudFormation stack.

Thực hiện các bước sau:

1. Đăng nhập vào **Amazon Connect instance** của bạn và truy cập trang **Phone numbers**.

2. Chọn số điện thoại của bạn trong danh sách.

3. Trong phần **Contact flow/IVR**, nhập **EnergyVirtualAgentFlow** vào ô tìm kiếm.

4. Chọn **EnergyVirtualAgentFlow** và chọn **Save** để liên kết số điện thoại với luồng này.

---

## Kiểm thử giải pháp

Trong repository này, thư mục **test** bao gồm nhiều kịch bản kiểm thử (*test scenarios*) mà bạn có thể sử dụng để tương tác và kiểm thử **Energy Virtual Assistant**.

Để trải nghiệm và tương tác với Energy Virtual Assistant, hãy **gọi đến số điện thoại Amazon Connect** mà bạn đã liên kết với contact flow, sau đó đặt các câu hỏi cho **Amazon Bedrock agent**, sử dụng thông tin tài khoản giả lập đã được tạo sẵn trong quá trình triển khai.

---

## Tùy chỉnh giải pháp

Người đọc được khuyến khích **tùy chỉnh giải pháp** này sao cho phù hợp với nhu cầu thực tế của mình, sau khi đã **clone repository** về máy cục bộ.
Các bảng **DynamoDB** và **Timestream** trong backend — được sử dụng như bản mô phỏng cho các hệ thống CRM, Billing, Outage, và MDMS — có thể sẽ khác trong môi trường thực tế.

Trước tiên, bạn nên xác định **loại yêu cầu khách hàng (customer requests)** mà bạn muốn hệ thống xử lý, sau đó tùy chỉnh các hệ thống backend cần thiết để hỗ trợ các tương tác đó.

Giải pháp này được thiết kế chủ yếu nhằm minh hoạ **“art-of-the-possible”** (tiềm năng ứng dụng thực tế) của trợ lý **AI sinh sinh (generative AI–enabled assistants)**, đồng thời đóng vai trò như một **công cụ tăng tốc (acceleration vehicle)** cho các hoạt động proof-of-concept (POC).

---

## Dọn dẹp tài nguyên

Để **xóa giải pháp** và **tránh phát sinh chi phí thêm** cho các tài nguyên AWS được sử dụng, hãy thực hiện các bước sau:

1. Đăng nhập vào **Amazon Connect instance** của bạn và truy cập trang **Phone numbers**.

2. Chọn số điện thoại của bạn trong danh sách.

3. Trong phần **Contact flow/IVR**, nhập **EnergyVirtualAgentFlow** vào ô tìm kiếm.

4. **Bỏ chọn (deselect)** luồng **EnergyVirtualAgentFlow** và chọn **Save** để xóa liên kết số điện thoại khỏi luồng.

5. Truy cập **CloudFormation console** trong tài khoản AWS của bạn.

6. Tìm stack **EnergyVirtualAssistant** và chọn **Delete**.

Hành động này sẽ **xóa toàn bộ tài nguyên** được triển khai bởi CloudFormation stack, ngoại trừ các **CloudWatch log groups** được tạo cho **Lambda functions** và **Amazon Connect instance**.  
Bạn có thể **giữ lại** hoặc **xóa chúng** tùy theo nhu cầu.
## Tóm tắt

Trong giải pháp này, chúng tôi đã minh hoạ cách xây dựng **agentic solutions** trên  
**Amazon Bedrock** và cách kết hợp **nhiều dịch vụ AWS** để tự động hóa quy trình chăm sóc khách hàng.

Việc ứng dụng **generative AI** trong dịch vụ khách hàng mang lại nhiều lợi ích vượt trội so với **IVR truyền thống**, chẳng hạn như:

- **Thoát khỏi các luồng IVR cứng nhắc** dựa trên quy tắc.
- **Cung cấp dịch vụ khách hàng liên tục 24/7.**
- **Tự động mở rộng quy mô** trong những giai đoạn có lưu lượng cao.

Chúng tôi khuyến khích bạn **triển khai, kiểm thử và tùy chỉnh** giải pháp này trong môi trường của mình để nhanh chóng nhận thấy giá trị thực tế của việc sử dụng **generative AI** nhằm **nâng cao trải nghiệm và hiệu quả dịch vụ khách hàng**.

---

## Darren Roback

<img src="https://d2908q01vomqb2.cloudfront.net/c5b76da3e608d34edb07244cd9b875ee86906328/2025/07/10/Darren-Roback.png" alt="Darren Roback" width="160" style="border-radius: 8px;">

Darren là Sr. Solutions Architect tại Amazon Web Services (AWS), làm việc tại St. Louis, Missouri.  
Anh có hơn 20 năm kinh nghiệm trong lĩnh vực công nghệ thông tin (IT) và đặc biệt đam mê các lĩnh vực **Data Analytics**, **Generative AI**, **Internet of Things (IoT)**, cũng như **Security and Compliance**.

Tại AWS, Darren hợp tác cùng các khách hàng trong ngành năng lượng và tiện ích (energy and utility) để giúp đưa ra quyết các thách thức kinh doanh bằng công nghệ của AWS.  
Ngoài công việc, anh yêu thích chế tác gỗ (woodworking) và dành thời gian cho gia đình.

---

## Jeremy Cianella

<img src="https://d2908q01vomqb2.cloudfront.net/c5b76da3e608d34edb07244cd9b875ee86906328/2025/07/10/Jeremy-Cianella.png" alt="Jeremy Cianella" width="160" style="border-radius: 8px;">

Jeremy Cianella là Sr. Solutions Architect làm việc tại Miami, Florida, phụ trách hỗ trợ các khách hàng trong lĩnh vực tiện ích (utility) và năng lượng tái tạo (renewables).

Anh có hơn 15 năm kinh nghiệm làm việc trong vận hành hệ thống tiện ích, kinh tế doanh nghiệp (enterprise architecture) và công nghệ đám mây (cloud technologies), giúp khách hàng thực hiện các sáng kiến chuyển đổi số (digital transformation).
