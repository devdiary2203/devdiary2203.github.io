---
title: "Data order issues in Kafka"
layout: post
date: 2022-09-30 21:03
image: /assets/images/me.jpeg
headerImage: false
tag:
- software engineer
- apache kafka
category: blog
author: devdiary
description: Data order issues in Kafka
---

Kiến trúc event driven là một trong những cách thiết kế giúp cho các service giao tiếp với nhau trong kiến trúc microservice. Một ứng dựng message based dùng các ứng dụng message queue để giao tiếp giữa các service. Kafka là một công cụ phổ biến để đáp ứng vấn đề xây dựng này. Tuy nhiên có một số vấn đề khi sử dụng kafka để giao tiếp giữa các ứng dụng cần xử lý như ordering message, duplicate message.

# Ordering message
Nhiều ứng dụng yêu cầu đảm bảo chính xác thứ tự message theo từng event như create -> modify -> cancel. Các topic trong kafka được tạo nếu chỉ có 1 consumer đọc 1 topic với 1 partition thì vấn đề về thứ tự không cần phải quan tâm đến. Tuy nhiên việc chỉ có 1 consumer đọc data sẽ không hợp lí khi có nhiều yêu cầu cần xử lý cùng một lúc. Giải pháp của kafka đưa ra là một topic chia thành nhiều phân vùng (partition) để có thế xử lý song song bởi nhiều consumer. Trong một phân vùng, kafka đảm bảo thứ tự của tất cả message. 

Lúc này sẽ có trường hợp các event của cùng một đối tượng lại nằm trên các phân vùng khác nhau. Ta sẽ cần xử lý từ producer để đảm bảo các event của cùng một đối tượng nằm cùng trên một phân vùng. 

Kafka đề xuất cách để gắn partition cho từng message như gắn key cụ thể cho từng message. Kafka phân tán các key theo hashkey của message. Ví dụ: hashcode(key) % N với N là số lượng partition của topic. Đối với các event của cùng một đối tượng muốn đảm bảo các event này vào cùng một partition thì các event này ta sẽ gắn cùng key với nhau. Tất cả các event này sẽ gửi vào cùng partition và consume theo đúng thứ tự gửi. 

Một cách khác để đảm bảo thứ tự của message là gửi message vào partition cụ thể. Ví dụ có 3 partition, message là có dữ liệu tên user thì ta có thể chia bảng chữ cái làm 3 phần, gửi message của những user có tên bắt đầu mỗi phần vào partition cụ thể.

# Duplicate messages

Một vấn đề xảy ra khi consumer message như service đọc message ra nhưng chưa xử lý kịp thì bị crash. Khi đó broker có thể gửi đi cùng một message vài lần cho đến khi service xử lý thành công. Khi đó service có thể xử lý cùng một message vài lần, vấn đề này gọi là duplicate message làm cho kết quả có thể bị sai. Để xử lý vấn đề này, kafka sử dụng một con trỏ là offset cho mỗi consumer để đánh dấu, các message phía trước offset chưa được xử lý, còn các message phía sau offset đã được xử lý thành công và không gửi lại nữa. Khi cài đặt, kafka có một tham số để cấu hình các xử lý offset là ```enable.auto.commit```. 
- Nếu giá trị này = *true*, kafka sẽ tự động dịch chuyển offset mỗi lần gửi message cho consumer thành công mà không quan tâm đến việc service đã xử lý xong hay chưa. Giá trị này đảm bảo mỗi message chỉ được gửi 1 lần cho mỗi consumer nhưng sẽ có nhược điểm là nếu phía service chưa xử lý xong thì sẽ bị miss dữ liệu.
- Nếu giá trị này = *false*, kafka sau khi gửi message cho consumer, broker sẽ đợi tín hiệu ack từ consumer thông báo message đã được xử lý thành công thì mới cập nhật offset. Cách làm này có thể đảm bảo dữ liệu không bị miss khi xử lý nhưng sẽ nảy sinh một vấn đề khác. Giả sử service đọc data theo batch gồm 100 message. Khi mới xử lý xong 50 message thì service bị crash, ack chưa được gửi cho broker, do đó, khi service restart lại sẽ phải xử lý trùng 50 message đã xử lý trước đó. 

Ta vẫn có thể xử lý lại đối với các event không gây ra hiệu ứng phụ. Tuy nhiên, đối với các hệ thống thanh toán, nếu event thanh toán trừ tiền bị lặp lại nhiều lần có thể gây hậu quả nghiêm trọng. Để xử lý vấn đề nói trên, ta có thể sử dụng thêm một cơ sở dữ liệu quan hệ như MySQL để lưu lại các message đã được xử lý. Giả sử một message được xử lý rồi sẽ insert vào bảng này. Đối với các message bị lặp lại, ta kiếm tra message đã được xử lý rồi thì sẽ không xử lý lại nữa.

![Xử lý duplicate message](/assets/images/tracking-message.png)
