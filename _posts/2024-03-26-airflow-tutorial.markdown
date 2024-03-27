---
title: "Airflow tutorial"
layout: post
date: 2024-03-26 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- data engineer
- data science
category: blog
author: devdiary
description: Nội dung như title
---

Airflow là công cụ mã nguồn mở sử dụng để lập lịch, quản lí và giám sát quy trình xử lý dữ liệu. 

# Airflow glossary

## DAG
Direct acyclic graph (DAG) là một đồ thị có hướng, không chu trình, biểu diễn tất cả các bước xử lý dữ liệu. Mỗi luồng xử lý được khởi tạo bên trong một file DAG, bên trong file này, ta định nghĩa một quy trình. Quy trình này được hiểu là một đồ thị với các node (đỉnh) tương ứng một task chịu trách nhiệm thực hiện một nhiệm vụ riêng. Các task có chạy tuần tự hoặc song song theo một thứ tự được cấu hình sẵn.

### Task
*Task* là đơn vị cơ bản trong airflow. Các task được sắp xếp theo thứ tự thực hiện bên trong DAG, upstream/downstream dùng để chỉ các task trước và sau của một task. Có 3 loại task cơ bản bên trong airflow:
- *Operator*: định nghĩa sẵn các task theo một template, kết nối các task lại với nhau tạo thành một DAG
- *Sensor*: lớp con của operator có nhiệm vụ chờ một sự kiện bên ngoài xảy ra để thực hiện nhiệm vụ nào đó
- *Taskflow* (triển khai dạng decorator *@task*): Loại task này sử dụng với mục đích custom một hàm python thực hiện dưới dạng một task

*Task*, *Operator*, *Sensor* là các lớp con của một lớp gọi là *BaseOperator*, các khái niệm task và operator có thể dùng để thay thế cho nhau.

### Vòng đời task
Một task trong DAG có thể trải qua các trạng thái sau:

![Airflow task states](/assets/images/airflow-task-state.png)

- *none*: Task chưa được đưa vào queue để chờ chạy
- *scheduled*: Scheduler xác định xong các phụ thuộc của task yêu cầu, đủ điều kiện đưa task vào queue chạy
- *queued*: Task đã được executor đưa vào queue, chờ node worker sẵn sàng để bắt đầu chạy (ví dụ worker đang thực hiện task khác, chờ task này chạy xong sẽ đến task đang đợi trong queue)
- *running*: Task đang chạy trong một node worker hoặc trên executor
- *success*: Task chạy thành công, không phát sinh lỗi gì
- *restarting*: Task được chạy lại khi có yêu cầu từ bên ngoài
- *failed*: Task không thành công, phát sinh lỗi khi chạy
- *skipped*: Task dừng chạy do phân nhánh, conflict only lastest
- *upstream_failed*: Task phía trước của task (task upstream) chạy không thành công. Tùy từng phiên bản các thuật ngữ previous/upstream, next/downstream dùng thay thế cho nhau
- *up_for_retry*: Task failed, được cấu hình để chạy thử lại, đang chờ được scheduler kiểm tra đưa vào queue
- *up_for_rescheduled*: Task là một sensor, đang ở chế độ reschedule
- *deferred*: Task bị hoãn bởi một điều kiện (trigger)
- *removed*: Task đã bị xóa khỏi DAG trước khi chạy

Luồng cơ bản của một task sẽ đi từ các trạng thái: none -> scheduled -> queued -> running -> success

## Task


## Operator


## Sensor


