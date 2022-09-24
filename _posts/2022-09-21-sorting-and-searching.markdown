---
title: "Sorting and searching"
layout: post
date: 2022-09-20 06:42
image: /assets/images/markdown.jpg
headerImage: false
tag:
- algorithm
- software engineer
category: blog
author: devdiary
description: Sorting and Searching
---

## Introduction

Các thuật toán sắp xếp

# Selection sort
Thuật toán chia mảng đầu vào thành 2 phần:
- Mảng chứa các phần tử đã được sắp xếp gọi là sorted sublist
- Mảng chứa các phần tử chưa được sắp xếp trong mảng đầu vào gọi là unsorted sublist

Ban đầu, sorted sublist rỗng, unsorted sublist chứa toàn bộ mảng đầu vào. Thuật toán tìm phần tử lớn nhất (nhỏ nhất tùy theo yêu cầu bài toán) thêm vào mảng sorted hoặc đổi chỗ với phần từ tận cùng bên trái của mảng sorted, tùy theo cách làm. Sau đó lưu vị trí đánh dấu bắt đầu từ unsorted sublist tăng lên 1 đơn vị về bên phải (vì phần từ được sắp xếp ta thêm dần vào bên trái nhất). Độ phức tạp O(n^2) vì phải duyệt qua toàn bộ phần tử của mảng và duyệt qua mảng con để tìm phần tử lớn nhất hoặc nhỏ nhất, thuật toán này không áp dụng trong thực tế do độ phức tạp quá lớn.

# Bubble sort (sinking sort)
Thuật toán sắp xếp các cặp phần tử và đổi chỗ nếu 2 phần tử đặt sai chỗ. Việc so sánh lặp lại cho đến khi mảng được sắp xếp. Độ phức tạp tính toán cũng là O(n^2) do duyệt qua toàn bộ phần tử và duyệt qua các phần tử từ một phần tử mốc.

# Insertion sort
Thuật toán sắp xếp theo logic tự nhiên. Lặp lại qua một mảng, chọn vị trí đúng cho mỗi phần tử và chèn phần tử vào đúng vị trí. Độ phức tạp tính toán là O(n^2) trong trường hợp phải đảo ngược lại toàn bộ mảng. Best-case của thuật toán là omega(n) khi các phần tử đã nằm đúng vị trí.

*Space complexity của các thuật toán này là O(1) do thay đổi vị trí tại chỗ*

# Merge sort
Thuật toán sắp xếp divide and conquer bằng cách chia mảng thành 2 nửa, sắp xếp mỗi nửa và merge lại với nhau. Trường hợp atomic (base case) của divide and conquer lúc này là mỗi mảng con có kích thước bằng 1. Phần xử lý phức tạp nhất dễ thấy nằm ở việc merge 2 mảng con lại với nhau. Merge sort gọi đệ quy trên mỗi nửa mảng được chia. RightIndex và LeftIndex tạm gọi là chỉ số của đầu tiên và cuối cùng của nửa mảng phải và trái. Trường hợp atomic đạt được khi phần tử [rightIndex] <= phần tử [leftIndex] thì dừng lại. Sau đó, hàm merge được gọi để merge 2 nửa mảng với nhau.

*Độ phức tạp tính toán là O(nlogn)*

# Quick sort
Quick sort là thuật toán nhanh nhất so với các thuật toán sắp xếp khác trong trường hợp cơ bản. Merge sort hoạt động tốt hơn trên linked list. Các thuật toán sắp xếp không dựa trên so sánh hoạt động tốt hơn quick sort

Các bước thực hiện:
- input mảng n phần tử
- chọn phần tử pivot trong mảng để sắp xếp
- chia mảng thành 2 mảng con, ta đặt giả sử rằng tất cả phần tử trong một mảng con nhỏ hơn phần tử pivot và tất cả phần tử trong mảng con còn lại lớn hơn phần tử pivot
- các phần tử bằng phần tử pivot có thể nằm ở một trong 2 mảng con đã sắp xếp
- sắp xếp 2 mảng con đệ quy để dẫn đến 2 mảng đã được sắp xếp
- nối 2 mảng con đã sắp xếp và phần tử pivot để được kết quả cuối cùng

Các trường hợp chọn phần tử pivot:
- Nếu chọn pivot là phần tử nhỏ nhất, mảng con bên trái sẽ luôn trống, tất cả phần tử sẽ nằm bên phải. Thời gian chia mảng thành 2 mảng con là O(n) lần. Tổng thời gian tính toán là O(n^2), đây là thời gian tính toán trường hợp xấu nhất của quick sort vì khi chia mảng n lần theo pivotvà mỗi lần chia thì duyệt mảng n lần để swap các vị trí giữa 2 mảng với nhau.

Cách chọn pivot:
- Chọn ngẫu nhiên: pivot có thể chọn ngẫu nhiên pivot, các chọn này được chứng minh là có thể mang lại hiệu quả tốt
- Median of three: chọn 3 phần tử ngẫu nhiên của mảng để tìm giá trị trung bình của chúng, cách này đảm bảo không chọn phải phần tử nhỏ nhất hoặc lớn nhất của mảng.

Độ phức tạp tính toán xấu nhất là O(n^2), trung bình độ phức tạp tính toán là O(nlogn) với giả sử phân phối các phần tử đồng đều và chọn ngẫu nhiên pivot. Trường hợp tốt nhất thì độ phức tạp là O(logn).

Quick sort và merge sort:
- Quick sort merge dễ hơn, nhưng chia khó 
- Merge sort merge phức tạp hơn nhưng chia dễ hơn so với quick sort
