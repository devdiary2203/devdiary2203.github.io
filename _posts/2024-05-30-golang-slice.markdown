---
title: "Golang slice"
layout: post
date: 2024-05-30 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
- golang
category: blog
author: devdiary
description: Nội dung như title
---

Mảng trong golang là một cấu trúc dữ liệu có số phần tử cố định, không thể thay đổi được. Để thay đổi kích thước mảng, thêm phân tử vào mảng thì phải tạo mảng mới có nghĩa là cấp phát bộ nhớ mới cho một mảng khác để lưu thêm phần tử. Slice ra đời như là một cấu trúc dữ liệu để giải quyết vấn đề này. Slice có đặc điểm có thể thay đổ được kích thước, có khả năng mở rộng để thực hiện các thao tác như append, copy. 

Cấu trúc dữ liệu của slice gồm 3 phần:
- Pointer: Con trỏ trỏ đến một mảng nới chứa dữ liệu thật sự slice chứa, gọi là backing array, Con trỏ này sẽ trỏ đến phần tử trong mảng khi xử lý dữ liệu.
- Length: Số lượng phần tử của slice đang có
- Capacity: Số lượng phần tử tối đa của slice có thể đạt được

Bây giờ sẽ đi sâu vào việc tại sao slice lại sử dụng cấu trúc dữ liệu có 3 thành phần như trên.

## Nil

Con trỏ pointer trỏ đến địa chỉ mảng đang chứa dữ liệu của slice. Trong golang, khái niệm dùng để chỉ không có gì không phải null hay none mà là nil. Một slice không trỏ đến mảng nào thì có giá trị bằng nil, lúc này con trỏ pointer không có giá trị nào.

Để kiểm tra một mảng rỗng (đang trỏ đến một mảng rỗng) sử dụng hàm len để kiểm tra độ dài của một mảng.

## Copy slice
Một vấn đề khác khi một slice được copy từ một slice khác thì con trỏ của slice mới này sẽ trỏ đến cùng backing array của slice trước. Việc này giúp golang tiết kiệm bộ nhớ sử dụng của chương trình.

Do slice trỏ đến địa chỉ của một mảng khác chứa dữ liệu nên khi thay đổi giá trị một phần tử trong slice, giá trị của mảng gốc được trỏ đến cũng sẽ bị thay đổi.

## Capacity

Tại sao lại có field capacity trong cấu trúc của slice. Field này dùng để thông báo cho golang biết khi nào cần cấp phát thêm bộ nhớ nếu trong trường hợp dùng hàm append để bổ sung thêm phần tử vào slice mà capacity đã đầy. Việc này sử dụng mới mục đích quản lí và tiết kiệm bộ nhớ trong golang khi chạy chương trình.
