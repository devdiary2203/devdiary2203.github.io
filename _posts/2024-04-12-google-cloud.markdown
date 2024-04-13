---
title: "Google Cloud"
layout: post
date: 2024-04-12 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- data engineer
- cloud
category: blog
author: devdiary
description: Nội dung như title
---

Đây là những tìm hiểu cơ bản về google cloud của mình

# Compute 
Trong kiến trúc của google cloud, tầng ở giữa bao gồm 2 thành phần là compute và storage. Khi data càng lớn, thì lượng tài nguyên tính toán cần sử dụng càng lớn. Google cung cấp một loạt các dịch vụ tính toán bao gồm:

## Compute engine
Compute engine là một hệ thống IaaS, cung cấp hạ tầng ảo dùng cho tính toán, lưu trữ, mạng tương tự như các datacenter vật lí. Cách sử dụng tương tự như các server vật lí. Compute engine cung cấp tài nguyên linh hoạt theo yêu cầu sử dụng. 

## Google kubernets engine
GKE chạy ứng dụng dưới dạng các container trong môi trường cloud, ngược lại với compute engine chạy các máy ảo trên các server cloud. Một container sẽ chạy service với các phụ thuộc (thư viện cần để chạy service) đóng gói lại bên trong container. 

## App engine
App engine được quản lí bởi PaaS. 

## Cloud functions
Cloud function chạy code để xử lý các event như upload file lên cloud, 

## Cloud run
Cloud run có toàn quyền quản lí tài nguyên tính toán, cho phép xử lý các yêu cầu, event drive stateless workload mà không bị ảnh hưởng bởi server. Tập trung vào phát triển code, tài nguyên được tự động scale up/down theo yêu cầu. Cloud run chỉ tính phí dựa vào tài nguyên sử dụng.

# Storage

# Big data and ml product categories
Phân loại các ứng dụng của google phát triển cho big data và ml hay data to ai worflow

## Ingestion and process
- Pub/sub
- Dataflow
- Dataproc
- Cloud data fusion

## Storage
- Cloud storage
- Cloud sql
- Spanner
- Bigtable
- Firestore

## Analytics
- BigQuery datawarehouse
- Looker
- Looker studio

## Machine learning
- Vertex AI
- Vertex AI Workbench
- AutoML
- Tensorflow


