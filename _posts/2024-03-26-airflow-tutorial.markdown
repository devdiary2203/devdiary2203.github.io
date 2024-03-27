---
title: "Airflow tutorial"
layout: post
date: 2024-03-15 22:03
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

# Airflow glosarry

## DAG
Direct acyclic graph (DAG) là một đồ thị có hướng, không chu trình, biểu diễn tất cả các bước xử lý dữ liệu. Mỗi luồng xử lý được khởi tạo bên trong một file DAG, bên trong file này, ta định nghĩa một quy trình. Quy trình này được hiểu là một đồ thị với các node (đỉnh) tương ứng một task chịu trách nhiệm thực hiện một nhiệm vụ riêng. Các task có chạy tuần tự hoặc song song theo một thứ tự được cấu hình sẵn.

### Vòng đời task
Một task trong DAG có thể trải qua các trạng thái sau:

![Airflow task states](/assets/images/airflow-task-state.png)

- none: Task chưa được đưa vào hàng đợi để chờ chạy
- scheduled: Task được lập lịch xong chờ chạy
- queued:
- running: 
- success:
- restarting: 
- failed: 
- skipped:
- upstream_failed:
- up_for_retry:
- up_for_rescheduled:
- deferred:
- removed:

## Task


## Operator


## Sensor


