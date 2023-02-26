---
title: "Why is kafka fast?"
layout: post
date: 2023-02-24 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- kafka
- message queue
category: blog
author: devdiary
description: Nội dung như title
---

Kafka là một nền tảng theo kiến trúc phân tán cho phép lưu trữ event, xử lý luồng dữ liệu. Một trong những ưu điểm của kafka là đạt được độ trễ thấp khi xử lý message. Để đạt được điều này, kafka sử dụng 2 thiết kế chính là Sequential I/O và Zero Copy Principle. Đây cũng là 2 thiết kế được sử dụng phổ biến trong nhiều nền tảng streaming.

# Sequential I/O
Kafka sử dụng phương pháp filesystem để quản lí việc lưu trữ. Đây là nguyên nhân kafka sử dụng sequential I/O thay cho random I/O. Thời gian tìm kiếm của sequential I/O nhanh hơn nhiều ở trên disk. Thiết kế này cho phép appending write only phù hợp với các ứng dụng dạng message queue. Các message được ghi tuần tự, đánh offset bắt đầu từ 0. Consumer có thể lưu trữ lại offset này để truy cập nhằm tiết kiệm thời gian.

Việc áp dụng sequential I/O giúp kafka cung cấp thời gian tìm kiếm message là O(1) ngay cả khi dữ liệu tăng lên hàng TB. Đối với mỗi partition, kafka ghi tuần tự từng message được publish bởi producer vào file log, file mới sẽ được tạo khi file cũ đầy.

![Sequential I/O](/assets/images/sequential-io.png)

# Zero Copy Principle
Kĩ thuật thứ 2 mà kafka áp dụng là zero copy principle. Đầu tiên ta sẽ tìm hiểu các kafka xử lý dữ liệu nếu không có ZCP.

![Kafka without zero copy principle](/assets/images/kafka-without-zero-copy.png)

- 1.1. Producer ghi dữ liệu vào ứng dụng
- 1.2. Ứng dụng ghi dữ liệu xuống ram
- 1.3. OS đồng bộ dữ liệu định kì xuống disk
- 2.1. OS load dữ liệu từ disk lên ram theo yêu cầu của ứng dụng
- 2.2. Ứng dụng copy dữ liệu từ ram
- 2.3. Ứng dụng gửi dữ liệu đến socket
- 2.4. Card mạng lấy dữ liệu từ socker để chuẩn bị gửi qua network cho consumer
- 2.5. Gửi dữ liệu cho consumer

Ở luồng xử lý trên ta thấy dữ liệu phải được copy 4 lần từ disk đến khi gửi cho consumer. Mỗi lần copy dữ liệu, CPU chuyển trạng thái, lưu trữ trạng thái của các thread để khôi phục hoặc tiếp tục thực thi những lần tiếp theo (context switch). Việc này làm tăng thời gian xử lý, chuyển dữ liệu trong luồng.

![Kafka with zero copy principle](/assets/images/kafka-with-zero-copy.png)

Khi áp dụng zero copy principle:
- 3.1. OS load dữ liệu từ disk lên ram theo yêu cầu ứng dụng
- 3.2. Card mạng copy trực tiếp dữ liệu từ ram để trả cho consumer
- 3.3. Gửi dữ liệu cho consumer

Như vậy thay vì 4 lần copy dữ liệu, bây giờ chỉ cần thực hiện 2 lần copy dữ liệu, ứng dụng không cần phải lấy dữ liệu từ ram rồi gửi vào socket. ZCP đã tiết kiện được bước copy dữ liệu giữa ứng dụng và kernel, thời gian xử lý theo như bytebytego cung cấp giảm xuống khoảng 65%.
