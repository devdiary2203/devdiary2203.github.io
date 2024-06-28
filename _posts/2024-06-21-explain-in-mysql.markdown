---
title: "Explain in mysql"
layout: post
date: 2024-06-20 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
- mysql
category: blog
author: devdiary
description: Nội dung như title
---

Explain là câu lệnh sử dụng để theo dõi kế hoạch truy vấn của một câu lệnh sql với các mệnh đề như select, update, insert,... Việc sử dụng explain để theo dõi kế hoạch các bước thực hiện câu lệnh, tối ưu câu truy vấn, index.

Giả sử có một câu truy vấn:
```sql
EXPLAIN SELECT * FROM Author
```

Các giá trị trả về của câu lệnh trên:

+----+-------------+------------+--------+---------------+---------+---------+----------------+------+-------------+
| id | select_type | table      | type   | possible_keys | key     | key_len | ref            | rows | Extra       |
+----+-------------+------------+--------+---------------+---------+---------+----------------+------+-------------+
|  1 | PRIMARY     | <derived2> | ALL    | NULL          | NULL    | NULL    | NULL           |  237 |             |
|  1 | PRIMARY     | Author     | eq_ref | PRIMARY       | PRIMARY | 3       | A1.AuthorCode  |    1 |             |
|  2 | DERIVED     | Book       | ALL    | NULL          | NULL    | NULL    | NULL           | 4079 | Using where |
+----+-------------+------------+--------+---------------+---------+---------+----------------+------+-------------+
3 rows in set (0.00 sec)

Ý nghĩa mỗi trường trong kết quả trả về

## id
Id là số thứ tự cho mỗi câu select trong câu truy vấn. 

## select_type
- SIMPLE
- PRIMARY
- DERIVED
- SUBQUERY
- DEPENDENT SUBQUERY
- UNCACHEABLE SUBQUERY
- UNION
- DEPENDENT UNION
- UNCACHEABLE UNION

## table
Các bảng được thực hiện truy vấn 

## type
Trường này thể hiện cách MySQL join bảng, mục đích trường này để theo dõi cách join bảng, các index có thể bổ sung. Giá trị của trường type bao gồm:
- system: 0/1
- const: bảng chỉ có duy nhất một dòng được đánh chỉ mục mà khớp với điều kiện tìm kiếm

## possible_keys

## key

## key_len

## ref

## rows

## Extra





