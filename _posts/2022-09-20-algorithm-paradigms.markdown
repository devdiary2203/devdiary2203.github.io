---
title: "Algorithm paradigms"
layout: post
date: 2022-09-20 06:42
image: /assets/images/markdown.jpg
headerImage: false
tag:
- algorithm
- software engineer
category: blog
author: devdiary
description: Algorithm paradigms
---

## Summary

Giới thiệu về các dạng thuật toán

### Brute force

Thuật toán vét cạn
Thuật toán tìm kiếm bằng cách thử tất cả các lựa chọn có thể xảy ra.
Ví dụ có một mảng các số nguyên, chúng ta muốn tìm giá trị nhỏ nhất, lớn nhất trong mảng, brute force sẽ duyệt qua tất cả các phần tử để tìm lời giải.
Ưu điểm của thuật toán này là đảm bảo luôn tìm ra được lời giải, tuy nhiên cách này là lâu nhất vì vậy khi sử dụng thuật toán này cần tôi ưu lời giải sao cho hiệu quả nhất. Linear search tìm kiếm giá trị bằng kiểm tra lần lượt từng phần tử trong mảng cho đến khi tìm được phần tử bằng giá trị cho trước, có độ phức tạp O(n).

### Greedy algorithms

Greedy là mô hình thuật toán tìm bằng các xây dựng từng phần lời giải. Lời giải tiếp theo được chọn sao cho lời giải đó là rõ ràng và có lợi ích tốt nhất tại thời điểm đó. Các lời giải này là tối ưu cục bộ, do đó thuật toán này được tạo ra với hi vọng sẽ tìm được lời giải tối ưu toàn cục dựa vào lời giải tối ưu cục bộ. Thuật toán tham lam phải thỏa mãn các thuộc tính:

- Greedy choice property: một lời giải toàn cục có thể chọn được từ một điểm tối ưu cục bộ.
- Optimal substructure: một lời giải tối ưu cho vấn đề chứa một lời giải tối ưu cho các vấn đề con.

Greedy algorithms thực hiện đệ quy từng phần để giải các vấn đề con.

Ưu điểm: giải quyết từng vấn đề con, dễ hiểu, logic

Nhược điểm: It is entirely possible that the most optimal short-term solutions may lead to the worse posible long-term outcome

### Divide and Conquer

Mô hình thuật toán này chia vấn đề thành các bài toán con cho đến khi các bài toán con là duy nhất và giống nhau, không thể chia thêm được nữa

Bắt đầu giải quyết các bài toán nhỏ này và tổng hợp lời giải với nhau.

Cách tiếp cận này sử dụng đệ quy do đó có thể chậm.

Thuật toán này gồm 3 bước:

- Devide
- Conquer
- Merge

### Dynamic programming

Dynamic programming giải quyết các vấn đề bằng các tổng hợp các lời giải con giống như divide conquer.

"Those who cannot remember the past are condemned to repeat it"

#### Characteristics

- Overlapping subproblems: các bài toán nhỏ của bài toán lớn không độc lập, 2 vấn đề con cùng chia sẻ giải quyết một vấn đề
- Optimal substructure property: Lời giải tối ưu toàn cục của vấn đề có thể xây dựng từ một lời giải của vấn đề con.

#### Dynamic programming patterns

- Top down: hướng giải quyết top down hay memoization tương tự với đệ quy, tuy nhiên cải tiến hơn ở chỗ top down sẽ tìm kiếm lời giải trong một bảng tra cứu trước khi tìm lời giải cho chính nó
- Bottom up: hướng giải quyết tabulation hay bottom up ngược lại so với top-down và tránh sử dụng đệ quy. Cách giải quyết này sẽ điền kết quả vào một bảng tìm kiếm và tính toán lời giải dựa vào kết quả trong bảng
