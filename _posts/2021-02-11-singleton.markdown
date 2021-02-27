---
title: "Singleton"
layout: post
date: 2021-02-11 22:26
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- design pattern
category: blog
author: source guru
description: Singleton - All you need 
---

## Summary

Giới thiệu tổng quan về design pattern.

#### Outline
- [Mục đích](#muc-dich)
- [Solution](#solution)
- [Code...](#code)
- [Ứng dụng](#ung-dung)

---

## Mục đích
Singleton là một design pattern thuộc **Creational pattern**, được tạo ra nhằm đảm bảo mỗi class chỉ có một thể hiện (instance) và chương trình có thể truy cập đến thể hiện này từ bất kì đâu.

- Mỗi class chỉ có một thể hiện. Khi lập trình, bạn cần kiểm soát các instance được tạo ra từ một class khi sử dụng. Phổ biến nhất có lẽ là khi bạn muốn viết một class để truy vấn đến cơ sở dữ liệu, mỗi khi thực hiện truy vấn bạn cần tạo một instance từ class đó. Lúc này, chương trình sẽ cần tạo và trả về một instance mới thay vì một instance đã được tạo trước đó.

- Cung cấp khả năng truy cập từ bất kì đâu của chương trình đến instance đó. Cách giải quyết này thuận lợi khi muốn truy cập đến các thông tin của chương trình, tuy nhiên cách làm này cũng có những rủi ro. Việc instance được phép truy cập từ bất kì đâu trong chương trình, code có thể được overwrite và chương trình bị crash. Để giải quyết vấn đề này, singleton cũng cung cấp một cơ chế để đảm bảo instance không thể bị overwrite từ code khác.

Trong trường hợp không muốn tạo nhiều instance từ một class, đặc biệt khi phần còn lại của chương trình phụ thuộc vào class này, singleton có thể được cài đặt trong chính class đó.

## Solution
Có thể cài đặt singleton theo hai bước như sau:
- Set phương thức khởi tạo là private, để tránh việc khởi tạo instance mới không mong muốn từ bên bên ngoài.
- Tạo một phương thức static để khởi tạo private object và gán object này vào một thuộc tính static. Khi phương thức static này được gọi, object đã gán trước đó sẽ được trả về. Bằng cách này, bất kì khi nào phương thức được gọi, một đối tượng giống nhau sẽ được trả về.

**Fun fact**
Một đất nước có duy nhất một chính phủ, mỗi người dân đều có thể nói rằng đó là chính phủ của họ ở bất kì đâu trên thế giới.

## Code

Sơ đồ mô tả singleton pattern

Singleton class khai báo một phương thức static getInstance để trả về cùng một thể hiện khi class gọi đến phương thức này.
Phương thức khởi tạo nên được ẩn đi trong code, thay vào đó, phương thức getInstance được dùng như các duy nhất để gọi một singleton object.


Reference [Design Pattern Guru](https://refactoring.guru/).
