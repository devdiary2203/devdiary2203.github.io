---
title: "Clickhouse"
layout: post
date: 2024-03-10 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- data engineer
- clickhouse
category: blog
author: devdiary
description: Nội dung như title
---

Overview kiến trúc, tính năng của clickhouse

Clickhouse là một column database, data được lưu trữ thành các cột, các query được thực hiện trên các mảng hay còn gọi là vectorized query execution giúp giảm chi phí query.

# Architecture choices
Tại sao Clickhouse lại lựa chọn các đặc điểm này trong kiến trúc
## Column-oriented storage
Source data thường chứa vài nghìn cột, trong khi report có thể chỉ sử dụng một vài cột. Yêu cầu hệ thống cần tránh đọc vào các cột không cần thiết làm tăng tài nguyên cần sử dụng trên disk
## Indexes
Cấu trúc dữ liệu trong bộ nhớ của clickhouse chỉ cho phép đọc các cột cần thiết và chỉ đọc trong phạm vi các hàng cần thiết
## Data compression
Lưu trữ các giá trị khác nhau trong cùng một cột với nhau trong thực tế thường giúp tỉ lệ nén dữ liệu tốt hơn do data thường có điểm giống nhau hoặc ít khác biệt. Clickhouse còn hỗ trợ các cách mã hóa đặc biệt để tăng khả năng nén dữ liệu.
##  Vectorized query execution
Clickhouse không chỉ lưu trữ data trong các cột mà còn xử lý data trong các cột giúp sử dụng cache trong CPU tốt hơn.
## Scalability
Clickhouse phân chia sử dụng tất cả các core có thể dùng bên trong CPU và disk để thực hiện query, không chỉ trên 1 server mà còn cả trường hợp nhiều core trên một cụm server.

# Tính năng nổi bật của clickhouse
## True column-oriented database 
Trong các db dạng column, không có dữ liệu nào được lưu kèm cùng với giá trị data được lưu trữ. Db phải hỗ trợ lưu trữ các giá trị với một mức độ dài cố định để tránh việc phải lưu thêm thông tin về độ dài của giá trị.
Clickhouse không phải dạng single database, nó cho phép tạo bảng, database runtime, load data và thực hiện query mà không cần restart hay thay đổi cấu hình server.

## Data compression
Data compression là một kĩ thuật được clickhouse sử dụng để tối ưu hiệu năng. Specialized codecs.

## Disk storage of data
Data được lưu trữ sắp xếp theo primary key để giúp cho việc query dữ liệu nhanh trong vài chục ms. ClickHouse được thiết kế để tối ưu disk, giảm chi phí lưu trữ so với việc sử dụng RAM.

## Parallel processing on multiple cores
Các query lớn được thựa hiện song song tận dụng tất cả tài nguyên sẵn có trên server.

## Distributed processing on multiple servers
Trong clickHouse, dữ liệu có thể nằm trên các shard khác nhau, mỗi shard có thể chứa cả các bản sao dữ liệu phục vụ cho fault tolerance. Tất cả các shard có thể chạy một query song song, trả kết quả về cho user.

## SQL support
ClickHouse hỗ trợ query theo cú pháp SQL, bao gồm cả group by, order by, from, join, in, window functions. Chưa hỗ trợ các correlated subqueries.

## Vector computation engine
Data không chỉ lưu trữ trên các cột mà còn được xử lý bằng các vector (nhóm các cột) để sử dụng CPU hiệu quả.

## Real-Time data inserts
ClickHouse hỗ trợ bảng với một khóa chính. Để query lấy dữ liệu trong phạm vi khóa chính, data được lưu tăng dần trong data storage, sử dụng merge tree. Data có thể thêm liên tục vào bảng mà không cần cơ chế lock.

## Primary Indexes
Data được sắp xếp theo khóa chính giúp cho việc extract data theo 1 key hoặc một khoảng key nhanh hơn.

## Secondary Indexes
Secondary indexes trong ClickHouse không trỏ đến các row cụ thể hay một khoảng row. Thay vào đó, secondary index cho phép database kiểm tra tất cả các row trong một khoảng dữ liệu không match với điều kiện của câu query để không cần đọc tất cả dữ liệu trong phần này. Do đó index này còn được gọi là skipping indexes.

## Suitable for Online Queries
Trong một số db OLAP, việc query chấp nhận thời gian với độ trễ từ vài s đế vài phút, do đó nhiều hệ thống sẽ dùng cách query thống kê kết quả làm report offline thay vì online. Ưu điểm của clickhouse là độ trễ thấp, query có thể được xử lý mà không có delay để lấy được kết quả truy vấn.

## Support for Approximated Calculations
Clickhouse tìm cách cân bằng giữa độ chính xác và hiệu năng query:
- Các hàm aggregate tính xấp xỉ kết quả các giá trị khác nhau, trung bình
- Query trên một phần của dữ liệu và lấy kết quả xấp xỉ trả về, lượng data được lấy từ disk ít hơn so với bình thường.
- Chạy query aggregate trên một tập giới hạn các key ngẫu nhiên thay vì toàn bộ key. Đối với một số phân phối của key tương ứng với dữ liệu đẩy vào sẽ cung cấp kết quả chính xác với cách làm này.

## Adaptive Join Algorithm
- Clickhouse xử lý thao tác join theo phương pháp gọi là adaptive join. Cách này ưu tiên thuật toán hash-join, trường hợp bị lỗi mới quay lại sử dụng merge-join nếu có nhiều hơn 1 bảng to.

## Data Replication and Data Integrity Support
Clickhouse sử dụng sao lưu bất đồng bộ trên nhiều server. Sau khi data được ghi vào bất kì replica nào, tất cả các replica còn lại sẽ nhận được bản copy của data. Hệ thống chịu trách nhiệm đảm bảo dữ liệu giống nhau trên các replica, Việc khôi phục data khi có lỗi được xử lý tự động hoặc bán tự động.

## Role-Based Access Control
- Clickhouse hỗ trợ các thao tác quản lí người dùng như tạo user, cấp quyền theo cú pháp SQL.

## Nhược điểm của clickhouse
- Không có full-fledge transaction
- Việc sửa và xóa dữ liệu đã tạo tốn nhiều thời gian và tài nguyên. Các yêu cầu update, delete data có hỗ trợ thực hiện theo batch
- Không tối ưu cho việc query các record cụ thể do spare index.

# Một số thuật ngữ
## Atomicity
Tính atomic đảm bảo cho các transaction hoạt động thành công hoặc không và không bị ảnh hưởng lẫn nhau. Tính năng này đặc biệt quan trọng trong các hoạt động giao diện chuyển tiền trong ngân hàng.

## Cluster
Tập các node trong hệ thống triển khai cụm clickhouse

## CMEK
Customer-managed encryption keys (CMEK) cho phép user sử dụng kms riêng để mã hóa dữ liệu trong disk clickhouse đảm bảo an toàn dữ liệu.

## Dictionary
Tính năng lưu mapping các cặp key-value trong một số kiểu dữ liệu, tăng hiệu quả khi truy vấn và join các bảng với nhau.

## Parts
Các file dữ liệu trong disk dùng để chia các phần của dữ liệu.

## Replica
Các bản sao dữ liệu trên clickhouse database, dùng kết hợp với replcatedMergeTree engine để đồng bộ dữ liệu trên các node.

## Shard
Là một tập con của dữ liệu, data trong clickhouse luôn có ít nhất 1 shard. Nếu data không chia trên nhiều server, chỉ có 1 share được tạo. Sharding data trên nhiều node giúp chia tải khi đọc dữ liệu tốt hơn.

# Các thao tác cơ bản
## Tạo bảng
Tạo database lưu trữ các bảng:
```sql
CREATE DATABASE IF NOT EXISTS helloworld
```

Tạo bảng trong database:
```sql
CREATE TABLE helloworld.my_first_table
(
    user_id UInt32,
    message String,
    timestamp DateTime,
    metric Float32
)
ENGINE = MergeTree()
PRIMARY KEY (user_id, timestamp)
```

Engine của table (merge tree) sẽ chịu trách nhiệm quản lí:
- Vị trí, cách data được lưu trữ
- Các loại truy vấn được hỗ trợ
- Data có được replicate không
Có nhiều loại engine khác trong clickhouse, merge tree là một engine cơ bản thường được sử dụng.

**Primary key**
- PK trong clickhouse không phải là unique cho mỗi row trong table
- PK quyết định cách data được sắp xếp trong disk. Mỗi 8192 rows hay 10MB dữ liệu thì một entry mới được tạo trong primary key index file. Cách làm này tạo các sparse index có kích thước khớp với bộ nhớ, phù hợp với các truy vấn select chỉ cần lấy từng khoảng nhỏ dữ liệu.
- PK có thể định nghĩa khi khởi tạo bảng dùng từ khóa *PRIMARY KEY*, trường hợp không được chỉ định trước thì các trường trong mệnh đề ORDER BY được sử dụng làm PK. Nếu có cả PK và ORDER BY thì PK phải là một tập con của các trường trong mệnh đề ORDER BY.

Như ví dụ trên, data được sắp xếp theo user_id và timestamp.

## Insert 
```sql
INSERT INTO helloworld.my_first_table (user_id, message, timestamp, metric) VALUES
    (101, 'Hello, ClickHouse!',                                 now(),       -1.0    ),
    (102, 'Insert a lot of rows per batch',                     yesterday(), 1.41421 ),
    (102, 'Sort your data based on your commonly-used queries', today(),     2.718   ),
    (101, 'Granules are the smallest chunks of data read',      now() + 5,   3.14159 )
```
Mỗi lần chạy câu lệnh insert sẽ tạo ra một **part** mới trong storage (ClickHouse cũng hỗ trợ UDFs để customer các thao tác khi xử lý data).

### Insert theo batch
ClickHouse tối ưu việc insert theo batchm, mỗi batch insert từ vài nghìn đến vài triệu row. Ngoài ra, clickhouse hỗ trợ việc insert async, tổ chức thành từng batch nhỏ sử dụng http client.

Ngoài ra có thể insert thông qua việc tích hợp clickhouse với các database client khác.

## Select

```sql
SELECT *
FROM helloworld.my_first_table
ORDER BY timestamp
```

Ngoài ra, clickhouse còn hỗ trợ format kết quả select theo nhiều format khác nhau, ví dụ:
```sql
SELECT *
FROM helloworld.my_first_table
ORDER BY timestamp
FORMAT TabSeparated
```

Tham khảo thêm: https://clickhouse.com/docs/en/interfaces/formats

## Update/delete
ClickHouse là database sinh ra phục vụ mục đích tổng hợp, phân tích tối ưu cho các thao tác insert, select. Các thao tác liên quan đến update, delete được phân loại *mutations* để xử lý thông qua câu lệnh ALTER TABLE hoặc sử dụng lightweight deletes, nguyên nhân do khóa chính trong clickhouse không phải chỉ ở trên 1 row duy nhất như các database OLTP do đó việc update/delete tốn nhiều thời gian và tài nguyên hơn. 
```sql
ALTER TABLE website.clicks
UPDATE visitor_id = getDict('visitors', 'new_visitor_id', visitor_id)
WHERE visit_date < '2022-01-01'
```

Lightweight delete:
```sql
DELETE FROM hits WHERE Title LIKE '%hello%';
```
Tính năng lightweight delete có sẵn trên tất cả engine họ MergeTree. Tính năng này là bất đồng bộ, để thực hiện xóa với điều kiện data đã bị xóa trên 1 replica hay đợi data được xóa trên tất cả replica ta sử dụng tham số *mutations_sync* Để bật tính năng này trong clickhouse cần thêm cấu hình sau:
```sql
SET allow_experimental_lightweight_delete = true;
```

Để xử lý yêu cầu update data liên tục, clickhouse cung cấp một tính năng khác gọi là deduplication. Clickhouse không kiểm tra trùng lặp row trước khi insert dữ liệu. Do đó, việc kiểm tra xử lý trùng lặp được thực hiện cuối cùng sau khi insert dữ liệu, điều này gây ra một số vấn đề:
- Dữ liệu có thể bị duplicate ở bất kì thời điểm nào
- Việc xử lý xóa các bản ghi trùng lặp được thực hiện khi merge các part lại
- Câu truy vấn chấp nhận việc dữ liệu có khả năng bị trùng lặp

Các table engine được sử dụng trong clickhouse đển xử lý việc trùng lặp dữ liệu:
- ReplacingMergeTree: các row bị duplicate có cùng sorting key khi merge sẽ bị xóa, dữ liệu trả về sẽ là row gần nhất được insert
- CollapsingMergeTree và VersionedCollapsingMergeTree cùng sử dụng cách loại bỏ row cũ đang có và chèn row mới vào nhưng phức tạp hơn ReplacingMergeTree. 2 engine này có ưu điểm khi thực hiện các thao tác aggregate và query đơn giản hơn vì không cần quan tâm đến việc data được merge hay chưa. Thường được sử dụng khi table có yêu cầu update data thường xuyên.

## Join
ClickHouse hỗ trợ join sử dụng cú pháp tương tự SQL:
```sql
SELECT
   *
FROM imdb.roles
  JOIN imdb.actors_dictionary
  ON imdb.roles.actor_id = imdb.actors_dictionary.id
```
