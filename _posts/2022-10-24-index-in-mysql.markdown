---
title: "Index in MySQL"
layout: post
date: 2022-10-26 22:03
image: /assets/images/me.jpeg
headerImage: false
tag:
- software engineer
- database
category: blog
author: devdiary
description: High performance SQL - Part 3
---

Index hay key trong MySQL là các cấu trúc được storage engine sử dụng để tăng tốc độ tìm kiếm các bản ghi.

# Các loại Index
Có nhiều loại index được thiết kế cho các mục đích khác nhau, index được cài đặt ở tầng storage engine trong kiến trúc của database do đó, mỗi loại database có cách sử dụng và hỗ trợ các loại index khác nhau. Không phải engine nào cũng hỗ trợ tất cả các loại index. Ngay cả khi, các engine hỗ trợ cùng một loại index thì cách cài đặt bên dưới cũng khác nhau với mỗi loại engine.

## B-Tree indexes
B-Tree index là một trong những loại index phổ biến, sử dụng cấu trúc dữ liệu dạng cây. Hầu hết các storage engine trong MySQL đều hỗ trợ B-Tree, ngoại trừ Archive engine. B-Tree biểu diễn mỗi node lá chứa liên kết đến một nhóm các node được chia theo từng khoảng (range). Như đã nói mỗi engine có cách cài đặt và sử dụng khác nhau, điều này dẫn đến sự khác biệt về hiệu năng của mỗi loại engine (InnoDB, MyISAM,...). Ý tưởng chung của B-Tree các tất cả các giá trị được lưu trữ theo thứ tự, mỗi node lá có cùng khoảng cách đến node gốc. B-Tree index tăng tốc độ truy vấn dữ liệu bởi vì storage engine không cần phải scan toàn bộ bảng dữ liệu. Engine bắt đầu tìm từ node gốc, chữa các con trỏ để truy cập đến node lá. Mỗi con trỏ dùng để truy vấn đến một node lá, tại mỗi node lá, chứa một khoảng các giá trị lưu trữ. Cuối cùng, engine lấy giá trị cần tìm trong node hoặc trả về không tìm được dữ liệu.

![Minh họa B-Tree](/assets/images/indexmysql_1.png)

Trong ví dụ trên, node lá chứa các con trỏ truy cập thẳng đến dữ liệu. Tuy nhiên cây cũng có thể chia làm nhiều level và ở các level giữa, các con trỏ sẽ trỏ đến các node ở level thấp hơn, phụ thuộc vào độ lớn của mỗi bảng dữ liệu.

B-Tree lưu index của các cột theo thứ tự do đó phù hợp cho việc tìm kiếm dữ liệu theo khoảng. Ví dụ một cây index trên một trường text lưu theo thứ tự giảm dần.

```sql
CREATE TABLE People (
 last_name varchar(50) not null,
 first_name varchar(50) not null,
 dob date not null,
 gender enum('m', 'f')not null,
 key(last_name, first_name, dob)
);
```

Bảng vừa tạo có index trên các trường ```last_name```, ```first_name```, ```dob```. Bây giờ ta tìm kiếm các user có tên bắt đầu bằng chữ **K**

![Tìm kiếm username bắt đầu bằng 'K'](/assets/images/indexmysql_2.png)






