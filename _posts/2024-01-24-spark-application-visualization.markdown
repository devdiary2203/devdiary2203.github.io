---
title: "Spark application visualization"
layout: post
date: 2024-01-24 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- data engineer
- apache spark
category: blog
author: devdiary
description: Nội dung như title
---

Apache Spark cung cấp công cụ để debug và tối ưu các spark job gọi là Spark UI. Công cụ này được dùng để theo dõi timeline thực hiện các event của một job spark, mô hình hóa các bước thực hiện và thống kê một spark streaming

# Timeline view
Spark hiển thị các event theo timeline thực hiện của một application submit. Timeline này được chia thành 3 loại:
- Across all jobs
- Within one job
- Within one stage

[Hình]

Có 4 excutor được tạo, application được chia thành 4 job chạy song song với nhau (executor từ 5 đến 8). Trong hình có 1 job bị fail. Sau khi thực hiện xong các job, tất cả các executor bị xóa (trạng thái removed). Khi click vào 1 executor ta sẽ thấy chi tiết thông tin từng job như sau:

[Hình]

Job chạy word count trên 3 file song song với nhau (3 stage). Sau khi cả 3 stage hoàn thành thì join kết quả lại với nhau ở bước cuối cùng. Click vào một stage trong hình:

[Hình]

Stage này được chia thành 20 partition chia để trên 4 máy (các ip tương ứng). Mỗi bar (thanh ngang) bên trong biểu diễn trạng thái của một task con bên trong stage. Từ timeline này, có thể xem được các thông tin sau:
- Các partition chia đều trên các node
- Phần chiếm nhiều thời gian nhất của task là xử lý tính toán dữ liệu (executor computing time)
- Nhìn vào biểu đồ, có 2 task thực thi cùng lúc, vì vậy ta có thể tăng số core của một node để tăng tốc xử lý song song nhiều task cùng lúc hơn.

Ngoài ra, spark còn có một tính năng gọi là dynamic allocation, tính năng này giúp scale số executor động tùy theo nhu cầu sử dụng tài nguyên của job và tài nguyên node được chia sẻ hiệu quả giữa các job.

# Execution DAG
Ở phần trước, các job spark được mô tả chi tiết về tài nguyên sử dụng, số excutor, tối ưu tài nguyên số core sử dụng. Ngoài ra, spark còn cung cấp mô tả chuỗi các action được thực hiện bên trong một job dưới dạng DAG như sau:

[]

Job này thực hiện bài toán word count theo từng bước:
- textFile: Đọc file text từ input
- flatMap: Chia mỗi dòng thành các từ và map thành cặp (key, số lượng key)
- reduceByKey: Tính tổng số lượng mỗi từ trong văn bản

Mỗi bước ở trên tương ứng với mỗi bước chạy trong code. Chấm xanh trong hình đánh dấu RDD được spark cache lại ở bước này giúp cho việc truy cập trong tương lại không cần phải đọc lại từ HDFS.

Với cách visualize quá trình thực thiện trong một job spark này, ta có theo dõi từng bước, việc tổ chức các bước thực hiện đã hợp lí, đặt cache dữ liệu hiệu quả và tối ưu lại quy trình thực hiện nếu job bị chậm
