---
title: "Why is kafka fast?"
layout: post
date: 2023-02-24 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
category: blog
author: devdiary
description: Nội dung như title
---

Kafka là một nền tảng theo kiến trúc phân tán cho phép lưu trữ event, xử lý luồng dữ liệu. Một trong những ưu điểm của kafka là đạt được độ trễ thấp khi xử lý message. Để đạt được điều này, kafka sử dụng 2 thiết kế chính là Sequential I/O và Zero Copy Principle. Đây cũng là 2 thiết kế được sử dụng phổ biến trong nhiều nền tảng streaming.

# Zero Copy Principle











