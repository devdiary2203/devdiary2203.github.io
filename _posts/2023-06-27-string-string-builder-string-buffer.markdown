---
title: "String, String builder, String buffer"
layout: post
date: 2023-02-24 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- java
category: blog
author: devdiary
description: Nội dung như title
---

Cả 3 class này đều được dùng để xử lý dữ liệu text trong java:
- String là immutable
- String buffer và String builder là mutable
String builder và String buffer tương tự nhau chỉ khác biệt ở tình huống sử dụng liên quan đến đa luồng
- String buffer tối ưu cho xử lý văn bản đa luồng
- String builder tối ưu cho xử lý văn bản 1 luồng
Tốc độ xử lý: String builder > String buffer > String

# Phân biệt mutable object và immutable object
- Mutable object: khi khởi tạo 1 đối tượng tức là khởi tạo tham chiếu đến instance của một class thì trạng thái của đối tượng này có thể thay đổi được sau khi khởi tạo thành công. Việc thay đổi các thuộc tính này thực hiện thông qua các phương thức get và set
- Immutable object: khi khởi tạo 1 đối tượng thì trạng thái của đối tượng không thể thay đổi được sau khi khởi tạo thành công. Các thuộc tính của đối tượng chỉ có thể get mà không thể set

Ví dụ với class String, các thuộc tính như length chỉ có thể get mà không set để thay đổi

# String 
String vừa là class vừa là một primitive type
- Primitive type: Tạo chuỗi lưu trữ (literal string), được lưu lại trong một vùng nhớ string pool nằm trong heap, tốn ít bộ nhớ. 
Nguyên nhân tốn ít bộ nhớ do giá trị này được lưu trữ trong String pool. Khi lưu một string mới, chương trình sẽ kiểm tra xem trong string pool đã tồn tại chuỗi này chưa, nếu có rồi sẽ tái sử dụng thay vì tạo một vùng nhớ mới. 

- Đối tượng: String là một class vì vậy có thể được khởi tạo qua toán tử new. Object string mới được khởi tạo này nằm trong bộ nhớ heap nằm ngoài String pool nên sẽ tốn không gian lưu trữ hơn so với kiểu primitive type. 

# String builder và String buffer
- String builder không xử lý động bộ khi có nhiều luồng cùng thao tác đến đối tượng trong khi string buffer đã xử lý đồng bộ khi có nhiều luồng cùng thao tác do đó string builder nhanh hơn string buffer vì không phải mất thời gian xử lý đồng bộ. String buffer phù hợp với thao tác cần xử lý đa luồng.
- Nếu connect nhiều chuỗi sử dụng String primitive thì JVM sẽ chuyển sang sử dụng string builder.
- String builder và String buffer khi khởi tạo có thể khởi tạo capacity kích thước của array lưu trữ các string (mặc định capacity=16)
