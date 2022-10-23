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

Một vấn đề quan trọng đôi lúc cần chú ý đến khi sử dụng kafka đó là việc đảm bảo thứ tự của message khi lưu trữ và lấy dữ liệu. Mô tả lại một chút, producer kafka publish các message vào stream để consumer có thể subscribe và lấy dữ liệu. Kafka quản lí log cho mỗi topic trên các partition, tất cả message từ cùng một producer được gửi đến cùng một partition và sắp xếp theo đúng thứ tự. Các partition được cấu trúc theo kiểu commit log, giữ thứ tự và không thể thay đổi thứ tự. Mỗi message được thêm vào một partition được gắn offset tương ứng, các offset này là id duy nhất trên partition.

Khi lấy data trên cùng một partition thì việc lấy data theo thứ tự không có vấn đề gì. Tuy nhiên kafka không quản lí thứ tự của message trên nhiều partition. Đối với các ứng dụng yêu cầu quản lí thứ tự record thì cách giải quyết là tạo một topic với chỉ một partition duy nhất và một consumer duy nhất (kafka không đảm bảo thứ tự message trên cùng topic, message được quản lí theo thuật toán round robin).


