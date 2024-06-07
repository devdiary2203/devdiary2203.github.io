---
title: "Primary key vs unique key"
layout: post
date: 2024-06-06 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
- database
- sql
category: blog
author: devdiary
description: Nội dung như title
---

Cả primary key và unique key đều được dùng để định dinh một row là duy nhất trong cơ sở dữ liệu quan hệ. Trường hợp ta có một bảng User (user_id, name, address, phone).
- Trường user_id thường được dùng làm primary key, mỗi user có một id làm định danh duy nhất
- Giả sử mỗi user chỉ được dùng một số điện thoại duy nhất gắn liền với user_id hoặc không có số điện thoại (null) thì trường phone cũng có thể được chọn là unique key.

Một trong những điểm chính để phân biệt primary key và unique key đó là unique key đảm bảo không có giá trị trùng lặp được chèn vào bảng trong khi primary key duy trì tính toàn vẹn khi tham chiếu

Phân biệt sự khác nhau giữa primary key và unique key trong database:
- Unique key có thể null, primary key không thể null
- Unique key được biểu diễn bằng ràng buộc duy nhất trong khi primary key được tạo sử dụng ràng buộc khóa chính và đó là ràng buộc duy nhất
- Primary key có thể gồm một hoặc nhiều unique key trong table
- Một bảng chỉ có 1 primary key nhưng có thể có nhiều unique key
- Nhiều engine tự động tạo một clustered index với trường primary key, khi đó chỉ có một clustered index trên một table, không tạo được trên trường unique key tại cùng bảng đó nữa.
- Primary key có thể được dùng làm khóa ngoại trên một bảng kh