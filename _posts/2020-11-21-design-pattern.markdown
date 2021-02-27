---
title: "Design Pattern"
layout: post
date: 2020-11-21 14:06
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- design pattern
category: blog
author: source guru
description: Induction design pattern
---

## Summary

Giới thiệu tổng quan về design pattern.

#### Outline
- [Khái niệm](#khai-niem)
- [Ví dụ](#vi-du)
- [Các thành phần của pattern](#thanh-phan)
- [Phân loại pattern](#phan-loai)

---

## Khái niệm

Nếu bạn đang gặp rắc rối hoặc cần tìm hướng giải quyết cho việc thiết kế ứng dụng của mình thì design pattern có thể là thứ mà bạn đang tìm kiếm. Nói cách khác, design pattern là những thiết kế phổ biến cho những vấn đề trong thiết kế phần mềm. Chúng được coi như những template của việc thiết kế phần mềm và bạn có thể tùy chỉnh lại để phù hợp với yêu cầu của riêng sản phẩm khi bắt tay vào code.

Pattern không phải là những thuật toán, thư viện được dựng sẵn. Pattern được thiết kế ở level cao hơn để định hướng thiết kế cho các ứng dụng. Một pattern được áp dụng vào nhưng project khác nhau thì code của chúng cũng khác nhau. Khi áp dụng pattern, bạn biết được kết quả đầu ra mình mong muốn và các chức năng cần có nhưng việc xây dựng các chức năng, sử dụng thuật toán, công nghệ nào phụ thuộc hoàn toàn vào bạn :)

---

## Ví dụ

Giả sử, bạn đang phát triển một ứng dụng trò chơi giả lập làm một con vịt, trong đó, người chơi có thể chọn một loài vịt bất kì để thử các hành động khác nhau của một con vịt như kêu 'quack, quack', hay vỗ cánh. Bạn nhanh chóng thiết kế cho ứng dụng của mình gồm các lớp mô tả các hành động của con vịt

{% highlight raw %}
![Funnny duck application][/assets/image/duck-pattern.png]
<figcaption class="caption">Funny duck application</figcaption>
{% endhighlight %}

---

## Các thành phần của pattern

Hầu hết các pattern được định nghĩa theo khuôn mẫu nhất định để ta có thể tái sử dụng trong nhiều yêu cầu khác nhau, thông thường chúng gồm các phần:

- **Intent** mô tả tóm tắt vấn đề và lời giải mà pattern hướng đến

- **Motivation** giải thích chi tiết vấn đề và lời giải

- **Structure** mô tả cấu trúc của pattern và mối liên hệ giữa các phần trong cấu trúc với nhau

- **Code** minh họa cho pattern

---

## Phân loại pattern

Có nhiều design pattern được sử dụng phổ biến tùy theo từng mục đích. Ta có thể phân loại dựa vào mức độ phức tạp của pattern, độ chi tiết trong thiết kế, khả năng mở rộng cho toàn bộ hệ thống.

Dựa vào độ phức tạp và chi tiết của thiết kế, ta có thể chia các pattern thành 2 loại:

- **Idioms** để chỉ các pattern đơn giản được thiết kế ở level thấp, dùng cho một ngôn ngữ duy nhất.

- **Architectural patterns** để chỉ các pattern được thiết kế ở level cao hơn dùng bao quát cho toàn bộ ứng dụng và có thể áp dụng cho nhiều ngôn ngữ khác nhau.

Cách phân loại ở trên có vẻ khá chung, gây khó khăn cho những người mới bắt đầu khi cần tìm một giải pháp khi viết một ứng dụng. Một cách phân loại pattern khác dựa vào mục đích sử dụng của pattern sẽ giúp cho dev có thể nắm được cách dùng và chọn ra pattern phù hợp với nhu cầu. Theo cách này, các pattern được phân vào 3 loại:

- **Creational patterns** cung cấp cơ chế khởi tạo object nhắm tăng tính linh hoạt và khả năng tái sử dụng code.

- **Structural patterns** giải thích các để kết hợp các object và class thành một cấu trúc lớn hơn, mà vẫn giữa được tính linh hoạt và hiệu quả của cấu trúc ứng dụng.

- **Behavioral patterns** các pattern này hướng đến sự hiệu quả trong việc giao tiếp và nhiệm vụ mỗi object thực hiện.

## Tổng kết

Trong bài viết này mình đã tóm tắt qua một số khái niệm liên quan đến design pattern. Nếu bạn từng gặp rắc rối khi ứng dụng có thêm những yêu cầu mới từ khách hàng, hay việc chưa có kinh nghiệm dẫn đến đống code ngày càng trở nên lộn xộn và khó maintain thì design pattern có thể là thứ bạn đang cần. Các bài viết sau mình sẽ viết chi tiết về từng loại pattern và một số pattern tiêu biểu cho từng loại.

HN, 21/11/2020, miss you !

Reference [Design Pattern Guru](https://refactoring.guru/).
