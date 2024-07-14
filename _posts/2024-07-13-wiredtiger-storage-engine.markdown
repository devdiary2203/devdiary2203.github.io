---
title: "WiredTiger storage engine"
layout: post
date: 2024-07-13 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
- mongodb
category: blog
author: devdiary
description: Nội dung như title
---

Storage engine là một thành phần trong các database chịu trách nhiệm quản lí cách data được lưu trữ, sử dụng disk hay in-memory. MongoDB hỗ trợ nhiều loại storage engine tùy thuộc vào phiên bản sử dụng và mục đích sử dụng với hiệu năng khác nhau.

- WiredTiger Storage Engine (default): WiredTiger là storage engine mặc định được dùng khi cài đặt mongodb. WiredTiger cung cấp mô hình xử lý lưu trữ document-level đồng bộ, checkpoint, compression
- In-Memory Storage Engine: Đây là một loại storage engine cung cấp ở phiên bản mongodb enterprise. Thay vì lưu data ở disk, phiên bản này lưu data in-memory.

# WiredTiger
WiredTiger storage engine là storage engine mặc định bên trong MongoDB, được cấu hình bằng thuộc tính --storageEngine hoặc storage.engine. MongoDB có thể tự động chọn storage engine khi tạo data file trong cấu hình --dbpath hoặc storage.dbPath.

Các môi trường sử dụng WiredTiger gồm:
- MongoDB Atlas: mongo db triển khai trên cloud
- MongoDB Enterprise: Phiên bản enterprise do MongoDB quản lí
- MongoDB Community: Source available, free, user tự cài đặt quản lí

# Tính năng và hạn chế của storage engine WT
- WT không thể pin docs vào cache của WT
- WT không reserve a portion of cache cho việc đọc và một phần khác cho việc ghi
- Thao tác ghi dữ liệu nặng có để làm ảnh hưởng đến hiệu năng nhưng WT ưu tiên index caching trong nhiều trường hợp
- WT cấp phát cache cho tất cả các instance của tiến trình mongod, tiến trình này xử lý request, quản lí truy cập data và các thao tác background khác. WT không cấp phát cache riêng cho mỗi database hoặc collection.

# Transaction (Read and Write) Concurrency
Từ version 7.0, MongoDB sử dụng một thuật toán mặc định để chỉnh sửa động số lượng transaction tối đa đồng thời được xử lý của storage engine (read và write). Thuật toán quản lí số transaction đồng thời mục đích tối ưu khả năng xử, tránh bị quá tải của cụm. Số lượng transaction tối đa không được vượt quá 128 read ticket và 128 write ticket, có thể thay đổi tuy theo từng node trong cluster. Số lượng read ticket và write ticket lớn nhất bên trong một node luôn bằng nhau. Để cấu hình số lượng tối đa ticket transaction sử dụng các config:
- storageEngineConcurrentReadTransactions 
- storageEngineConcurrentWriteTransactions 

Để check lại số lượng transaction tối đa được cho phép, sử dụng lệnh serverStatus kiểm tra tham số wiredTiger.concurrentTransactions. Giá trị wiredTigerConcurrentTransactions thấp không phản ánh một cụm bị quá tải, để kiểm tra một cụm có bị quá tải không, kiểm tra số lượng ticket read và write nằm trong queue.

# Document Level Concurrency
WiredTiger sử dụng document-level để quản lí đồng bộ khi ghi dữ liệu ví dụ như khi nhiều client cùng sửa một document tạo cùng một thời điểm. WT sử dụng cơ chế các lock khác nhau trên mỗi phạm vi global, database, collection. Khi phát hiện ra tranh chấp giữa 2 thao tác, WT sẽ thử lại thao tác gây ra conflict (optimistic concurrency control). Một số thao tác xảy ra trên phạm vi global, liên quan đến nhiều database sử dụng instance-wide lock để kiểm soát. Một số thao tác khác như collMod vẫn yêu cần lock cả database.

# Snapshots and Checkpoints
WT sử dụng cơ chế multiversion concurrency control MVCC để quản lí version data. Khi bắt đầu một thao tác, WT cung cấp snapshot tại một thời điểm để thực hiện thao tác đó. Snapshot biểu diễn một view nhất quán về dữ liệu in-memory.

Khi ghi dữ liệu vào disk, WT ghi tất cả dữ liệu của một snapshot vào disk, ghi vào tất cả data files. Như vậy, ta đã có một checkpoint chứa một bản dữ liệu nhất quán được lưu trong các data files. Checkpoint đảm bảo data files là consistent và có thể dùng để khôi phục dữ liệu khi có sự cố sau này.

MongoDB cấu hình WT để tạo các checkpoint mỗi 60s một lần, một bản snapshot sẽ được ghi vào disk.

Trong suốt quá trình update checkpoint mới, checkpoint cũ vẫn sử dụng bình thường. Trường hợp cụm bị shutdown hay phát hiện ra lỗi khi ghi checkpoint mới xuống disk, ngay khi restart lại cụm, mongodb sẽ khôi phục dữ liệu từ checkpoint thành công gần nhất.

Checkpoint mới được đánh dấu tạo thành công, có thể dùng được khi WT metadata table cập nhật tham chiếu đến checkpoint mới. Khi checkpoint mới này được sử dụng, WT sẽ giải phóng checkpoint cũ.

# Snapshot retention history
Từ version 5.0, cấu hình minSnapshotHistoryWindowInSeconds dùng để chỉ thời gian một bản snapshot được giữ trong bao lâu. Do đó khi tăng thời gian lưu một bản snapshot cũng đòi hỏi nhiều bộ nhớ hơn để lưu nhiều bản snapshot hơn trong một khoảng thời gian. MongoDB lưu các bản snapshot bên trong WiredTigerHS.wt file trong thư mục dbPath.

# Journal
WT sử dụng cơ chế write-ahead log hay journal để tổng hợp data với các checkpoint để đảm bảo dữ liệu được nhất quán, ổn định. WT WAH persist tất cả dữ liệu được chỉnh sửa giữa các checkpoint, nếu cụm bị shutdown hay có lỗi giữa các checkpoint, WAH hay journal được sử dụng để khôi phục tất cả dữ liệu sau checkpoint cuối cùng. Tần suất, các xử lý data của mongodb ghi từ journal vào disk: https://www.mongodb.com/docs/manual/core/journaling/#std-label-journal-process

WT journal nén dữ liệu sử dụng thư viện snappy với các tùy chọn thuật toán khác nhau, sử dụng cấu hình storage.wiredTiger.engineConfig.journalCompressor. Nếu một log file nhỏ hơn hoặc bằng 128 bytes (kích thước file size nhỏ nhất mà WT yêu cầu) thì file đó sẽ không được nén.

# Compression
Với WT, MongoDB hỗ trợ compression cho tất cả các collection và index. Compression tối ưu tài nguyên lưu trữ nhưng ngược lại tốn thêm tài nguyên CPU để xử lý. Mặc định, WT sử dụng block compression dùng thư viện snappy cho tất cả collection và prefix compression cho tất cả index.

Có 2 thuật toán được sử dụng để compress data:
- zlib
- zstd

Để lựa chọn thuật toán compresss hoặc không compress data, sử dụng config:
- storage.wiredTiger.collectionConfig.blockCompressor

Để disable prefix compression, sử dụng config:
- storage.wiredTiger.indexConfig.prefixCompression

Theo tài liệu của mongodb, cấu hình compress mặc định đáp ứng được cân bằng giữa hiệu năng lưu trữ và tài nguyên cần để xử lý compress.

# Memory use
Với WT, MongoDB sử dụng cả WT internal cache và filesystem cache. 

Internal cache của WT sẽ lớn hơn một dung lượng tính theo công thức:
- 50% của RAM - 1GB hoặc 256MB

Mặc định, một system với 4G Ram sẽ dành ra 1.5GB dùng làm internal cache của WT (0.5 * 4 - 1). Ngược lại với một hệ thống có 1.25G RAM, WT sẽ được cấp phát 256MB cho internal cache do (0.5 * 1.25 - 1 < 1G) nhỏ hơn 1G nên WT cache sẽ được cấp phát theo option thứ 2. 

Với một số instance, như trong trường hợp trên của mongodb , khi chạy một container, db có thể ràng buộc về memory để đảm bảo thấp hơn memory của server. Memory sẽ bị giới hạn, chỉ dùng được với lượng RAM tối đa đã được cấp. 

Mặc định, WT sử dụng snappy block compression cho tất cả collect và prefix compression cho tất cả index. Compression mặc định được cấu hình ở mức global, được áp dụng cho mỗi collection và index khi khởi tạo. Sự khác biệt giữa data ở internal cache so với on-disk format:
- Data in filesystem cache giống như on disk, bao gồm việc compress dữ liệu. Filesystem được sử dụng với mục đích giảm thiểu các tác vụ IO của OS
- Index được load vào internal cache có cách biểu diễn khác với on-disk nhưng vẫn có cách ưu điểm của index prefix compression để giảm thiểu lượng RAM sử dụng. Index prefix compression loại bỏ trùng lặp từ index field.
- Collection data trong internal cache không được compress, sử dụng biểu diễn khác với data on-disk. Block compression có thể tiết kiệm đáng kể ở on-disk, nhưng data phải không bị compress mới có thể được xử lý bởi server

Với filesystem cache, mongodb tự động sử dụng tất cả lượng mem có sẵn của WT cache hay cách process khác không sử dụng đến. 

Để chỉnh sửa kích thước internal cache của WT sử dụng config sau (lưu ý tránh tăng kích thước cache của WT trên mức quy định): **storage.wiredTiger.engineConfig.cacheSizeGB** hoặc **--wiredTigerCacheSizeGB**
