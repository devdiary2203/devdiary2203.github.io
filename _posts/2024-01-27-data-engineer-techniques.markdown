---
title: "Data engineer techniques"
layout: post
date: 2024-01-27 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- data engineer
category: blog
author: devdiary
description: Nội dung như title
---

# Data extraction techniques


# Data transformation techniques
- Name data transformation techniques
- Scheme-on-read vs schema-on-write
- Lost in transformation
## Data transformation
- Một số kĩ thuật để thực hiện data transformation:
+ Data typing: convert dữ liệu sang kiểu phù hợp như string, integer, object,...
+ Data structuring: tái cấu trúc, format lại data từ cấu trúc này sang cấu trúc khác phù hợp với yêu cầu như json, xml, csv được convert để ghi vào database
+ Anonymizing, encrypting: dữ liệu cần được mã hóa để đảm bảo tính bảo mật cho thông tin
+ Cleaning: Xóa các bản ghi bị trùng lặp, xử lý dữ liệu bị miss
+ Normalizing: Chuẩn hóa dữ liệu về dạng thông dụng, dễ đọc, dễ xử lý với nhiều ứng dụng khác nhau sử dụng
+ Filtering, sorting, aggregatin, binning
+ Joining data sources: Join các nguồn data với nhau để tổng hợp thông tin

## Scheme-on-read vs Scheme-on-write
- Scheme-on-write là một cách tiếp cận phù hợp cho ETL pipeline. Data ghi vào phải được chuyển đổi phù hợp với cấu trúc của database. Ý tưởng là data ghi vào phải nhất quán về cấu trúc khi lưu trữ. Đặc điểm của cách tiếp cận này:
+ Đảm bảo tính nhất quán dữ liệu, hiệu quả ở tốc độ đọc
+ Tính linh hoạt sẽ bị hạn chế do cấu trúc cố định
- Schema-on-read là một cách tiếp cận mới khác, phù hợp cho ELT pipeline. Cấu trúc data tùy thuộc vào yêu cầu khi load dữ liệu từ raw data storage:
+ Cách làm này có ưu điểm về tính linh hoạt khi xử lý dữ liệu, cấu trúc dữ liệu transform đáp ứng theo yêu cầu riêng của từng ứng dụng

## Lost in transformation
Trong quá trình transformation, việc dữ liệu bị mất hoàn toàn có thể xảy ra:
- ETL: dữ liệu gốc có thể bị mất mà không thể khôi phục lại do việc xử lý đọc từ raw data ghi vào database
- ELT: dữ liệu gốc không thể bị mất do cách xử lý load từ dữ liệu gốc để transform sang cấu trúc phù hợp với từng yêu cầu

Một số trường hợp xảy ra mất dữ liệu:
- Compression: Mất mát dữ liệu khi thực hiện nén dữ liệu, đổi kiểu dữ liệu gây ra lỗi hoặc sai 
- Filtering: Lọc dữ liệu bị mất dữ liệu theo một số điều kiện
- Aggregating: Tổng hợp dữ liệu theo ngày, tháng, năm từ dữ liệu gốc
- Edge computing: Dữ liệu dạng stream liên tục khi xảy ra event, không được lưu trữ vào một kho chứa raw data

# Data loading techniques
- Các kĩ thuật data loading
- So sánh batch loading và stream loading
- Kĩ thuật push and pull data
- Parallel loading

## Các kĩ thuật data loading
Data loading được chia theo các trường hợp sử dụng:
- Full loading:
+ Xử lý toàn bộ lịch sử data từ một data warehouse
- Incremental
+ Sau khi xử lý full loading với lịch sử data, data tiếp tục được append vào để xử lý
+ Tùy vào số lượng, tốc độ tăng trưởng của data để chọn cách xử lý theo batch hay stream
- Scheduled
+ Các task load data dạng định kì như tính toán thống kê theo ngày, chạy cron
- On-demand
+ Các task load data theo nhu cầu sử dụng như đánh giá kích thước data, detect event, request tài nguyên như video, music streaming
- Batch and stream
+ Batch và stream là 2 cách tiếp cận để xử lý data đầu vào trong khi batch xử lý data theo từng đoạn thì stream xử lý data realtime. Ngoài ra, còn có micro-batch nghĩa là data được chia nhỏ để xử lý nhanh hơn, thường sử dụng với các dữ liệu mới gần đây
- Push and pull
+ Push and pull là 2 cách xử lý data dựa theo mô hình client-server. Pull được sử dụng khi muốn truy cập lấy tài nguyên từ client như các trang rss, email. Push là việc lấy dữ liệu từ server như message, notification
- Parallel and serial
+ Data được chia thành các luồng khác nhau để xử lý khi load 



