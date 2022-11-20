---
title: "Lead scoring with python"
layout: post
date: 2022-11-17 22:03
image: /assets/images/me.jpeg
headerImage: false
tag:
- data science
category: blog
author: devdiary
description: Lead score with python
---

# Khách hàng tiềm năng là gì?
Khách hàng tiềm năng (lead - potential customer) đóng vai trò quan trọng trong nhiều mô hình kinh doanh hiện nay. Các sản phẩm hướng tới đối tượng user thông qua định danh hay đăng kí tài khoản thì việc phát hiện và chuyển đổi tập khách hàng tiềm năng thành các khách hàng sử dụng sản phẩm, đóng góp doanh thu cho doanh nghiệp là vô cùng quan trọng. Nhóm khách hàng này được hiểu là những user quan tâm đến việc thanh toán, sử dụng sản phẩm, dịch vụ của bạn. Một tập khách hàng tiềm năng cơ bản có thể được biễu diễn bằng một trong các thông tin sau:
- Demographic (độ tuổi, giới tính,...)
- Thông tin cá nhân
- Nguồn tiếp cận: mạng xã hội, quảng cáo, dịch vụ của bên thứ ba
- Log: thời gian sử dụng website, số lượng click, view,...

# Quy trình quản lí khách hàng tiềm năng
![Tổng quan quy trình quản lí khách hàng tiềm năng](/assets/images/lead-scoring-system.png)

Một quy trình quản lí khách hàng tiềm năng đơn giản có thể được chia thành 3 giai đoạn:
- Lead generation
- Lead qualification
- Conversion

## Lead generation
Đây là bước đầu tiên của quy trình với mục đích tìm kiếm các khách hàng tiềm năng quan tâm đến sản phẩm của doanh nghiệp. Tập khách hàng này như đã nói ở trên được xây dựng bằng cách lấy dữ liệu từ dịch vụ của bên thứ ba cung cấp hay chạy các chiến dịch quảng cáo, chức năng giới thiệu user, hành động của user trên sản phẩm (website, app).

## Lead Qualification
Việc xây dựng tập khách hàng tiềm năng ở trên có thể coi là việc thu thập dữ liệu thô. Dữ liệu này có thể chưa chính xác vì ảnh hưởng của nhiều yếu tố ngoại cảnh. Nếu tiếp thị đến toàn bộ tập khách hàng ở trên mà chưa qua bước đánh giá lại chất lượng dữ liệu sẽ có thể tốn nhiều thời gian và hiệu quả đạt được không cao do dữ liệu thô quá lớn và bị nhiễu trong quá trình thu thập. Bước tiếp theo cần làm là phân tích, đánh giá xem liệu những user nào có khả năng thực sự sẽ mang lại lợi nhuận cho sản phẩm (click quảng cáo trên website, thanh toán sản phẩm,...). Những user được lọc ra sau bước này sẽ là những user tiềm năng nhất.

## Lead conversion
Đây là bước cuối cùng trong quy trình quyết định thành công của toàn bộ chiến dịch bán hàng. Sau khi tìm ra được các khách hàng tiềm năng nhất, nói một cách đơn giản, các hoạt động tiếp thị sẽ tìm cách thuyết phục khách hàng bỏ tiền ra cho sản phẩm của doanh nghiệp. Thành công của toàn bộ dự án sẽ được quyết định ở bước này.

Cảm ơn bạn đã đọc hết đoạn chém gi vừa rồi của một người không biết gì về marketing.
.
.
.
.
.
.
.
.
.
.
.
# Lead scoring là gì?
Bây giờ mình chém gió tiếp...

Tình huống phổ biến có thể bạn đã gặp trong thực tế là không hiểu sao mình nhận được một cuộc gọi từ công ty nào đó và nói rằng bạn có quan tâm đến sản phẩm của họ. Việc này rất phiền phức vì bạn còn không biết đó là công ty nào hoặc chưa có nhu cầu sử dụng sản phẩm. Có rất nhiều khách hàng tiềm năng, số lượng có thể lên đến cả triệu người. Ở góc nhìn của doanh nghiệp, việc đầu tư thời gian và tiền bạc để tiếp thị đến từng người trong số cả triệu người sẽ gây tốn kém và thiếu hiệu quả.

Bạn mới mở một quán cafe mèo và lập fanpage của quán của mình để quảng cáo trên mạng xã hội X. Team vận hành quảng cáo của mxh X sẽ xây dựng bộ dữ liệu gồm những user quan tâm đến cafe gồm những thuộc tính như độ tuổi, giới tích, chủ đề quan tâm để gợi ý fanpage của bạn. Tuy nhiên với những người chỉ thích uống cafe mà ghét mèo thì việc gợi ý này có vẻ hơi phản tác dụng. Bằng trực giá, những user có độ tuổi trẻ, quan tâm đến các chủ đề về thú cưng có vẻ là các khách hàng tiềm năng sẽ tìm đến quán cafe khi xem được quảng cáo, ngược lại những user già hơn có thể sẽ ít quan tâm. Đây là lúc ta cần xây dựng một hệ thống để đánh giá điểm cho từng user - Lead scoring system, để biết liệu rằng đây có phải là một khách hàng tiềm năng cho quán cafe của bạn hay không. 

Hệ thống đánh giá điểm người dùng - Lead scoring system sử dụng để đánh giá điểm user trên dữ liệu và có thể kết hợp với hệ thống CRM (customer relationship management) nhằm theo dõi từng khách hàng tiềm năng và sự thay đổi trạng thái của khách hàng đó. Tất nhiên với lượng dữ liệu lớn lên đến cả triệu người như hiện nay ở nhiều doanh nghiệp, cần có một phương pháp hiệu quả để tính điểm cho user. Một trong những cách phổ biến được sử dụng hiện nay đó là áp dụng các thuật toán machine learning vào dự đoán.
![Mô hình dự đoán khách hàng tiềm năng](/assets/images/predict-lead-scoring.png)

# Ví dụ

