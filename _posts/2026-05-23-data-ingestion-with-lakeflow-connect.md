---
title: "Data ingestion with lakeflow connect"
layout: post
date: 2026-05-23 14:06
image: /assets/images/markdown.jpg
headerImage: false
tag:
- data engineer
- databricks
category: blog
author: source guru
description: Data ingestion with lakeflow connect
---

I want to show you my learning path in Databricks when i'm moving to work as Data engineer

In this part, I want to share about data ingestion with lakeflow connect in Databricks, include:
- cloud storage ingestion with lakeflow connect standards connectors
- enterprise data ingestion with lakeflow connect managed connectors
- ingestion alternatives

trước khi đọc bài này nên có kiến thức về:
- databricks workspace
- spark
- delta lake
- medallion architecture
- unity catalog data objects
- Các kiểu dữ liệu: parquet, csv, json, txt
- khả năng sử dụng databricks notebooks

data engineering in databricks
- databricks streamlines hỗ trợ ingest và quản lí data liền mạch (seamless) sử dụng lakeflow connect và các solution lưu trữ như delta lake, parquet, iceberg

bắt đầu với table format/data format mà databricks sử dụng để optimize storage layer
phía trên storage layer, databricks lakeflow xây dựng data governance layer dựa trên unity catalog

phân biệt rõ vai trò:
- storage layer giải quyết các vấn đề của data liên quan đến acid, snapshot, schema solution, metadata table
- governance layer giải quyết các vấn đề liên quan đến phân quyền, audit, lineage, masking, row-level security, cross-workspace governance

databricks xây dựng lakeflow, hỗ trợ xử lý end-to-end cho data. là một platform thống nhất hỗ trợ ingestion, transformation, orchestration. Lakeflow bao gồm các thành phần:
- lakeflow connect: một tập các connector để đơn giản hóa (simplify) data ingestion từ các nguồn khác nhau như application, database, cloud storage, message buses, local files
- lakeflơ declarative pipelines: framework hỗ trợ build batch/streaming data pipeliné using sql, python, designed để tăng tốc (accelerate) phát triển các etl flow
- lakeflơ jobs: tự động hóa workflơ để điều phối khối lượng data xử lý, kết hợp nhiều task (coordination) bên trong các workflow phức tạp, lập lịch, tối ưu và quản lí các task lặp lại trong workflơ

bắt đầu với lakeflơ connect, data ingestion được sắp xếp (streamlined) đơn giản, hiệu quả thông qua các connectors cho phép ingest data từ file, cloud storage, database, enterprise application, streaming source trực tiếp vào lakehouse

trước đây với mỗi source phải sử dụng (resort) một solution chắp vá (patchwork) để ingest data. với lakeflơ, các data pipeline ingest data được thống nhất bên trong databricks, hỗ trợ đầy đủ setup, maintain, điều phối, quan sát và governace data.

ưu điểm các connectors cung cấp sẵn mà lakeflơ hướng đến:
- managed and efficient solution giúp giảm cost, tăng tốc khai thác các giá trị của data
- cung cấp interface chủ động tương tác, quản lí data, ingestion từ các application enterprise
- unified observability và governance giữ quản lí toàn bộ pipeline, bảng trong một hệ thống đảm bảo an toàn, ổn định cho data

lakeflơ connect phân loại
lakeflơ cung cấp các connector để ingest data vào lakehouse từ các nguồn khác nhau như enterprise application, database, cloud storage, local file, message buses,... gồm 3 loại chính:
- manual file upload: user upload local file trực tiếp vào volume hoặc table trong databricks
- standard connector: các connector hỗ trợ data ingestion từ các source như cloud storage, kafka,... hỗ trợ nhiều mode như batch, incremental batch, streaming (ingestion method). 
- managed connector: các connector được databricks xây dựng sẵn cho từng hệ thống cụ thể như saas platform, database. các connector này có khả năng scale, cost-effective, high performance ingestion vào lakehouse, tận dụng hiệu quả các pattern incremental read/write 

đi sâu hơn more detail về ingestion method:
- batch: load data theo batch các row vào databricks, được lập lịch, tất cả data được ingest lại khi pipeline chạy (traditional batch)
```sql
--- sql statement
create table as (ctas);
```

```python
# python method
spark.read.load()
```

- incremental batch: chỉ data mới được ingest, các data cũ tự động bỏ qua. ưu điểm nhanh hơn, tốn ít resource hơn do xử lý ít data hơn batch
```sql
--- sql statement
copy into
```

```python
# python method: auto loader with a timed trigger
spark.readStream()
```

```sql
--- declarative pipeline
create or refresh streaming table
```

- streaming: load data liên tục theo từng row hoặc batch rows, query được ngay khi rơư được tạo. ngược lại micro batch xử lý từng batch nhỏ trong tần suất thấp trong vài giây hoặc vài phút. 2 method trong streaming này cân bằng giữ latency và resource sử dụng. streaming ingestion cho phép query data near realtime, lơw latency, data được load từ các source như kafka, kinesis, google pub/sub, pulsar
```python
# python method: auto loader with continuous trigger
spark.readStream()
```

+ declarative pipeline trigger mode continuous: sử dụng decorator @dlt hoặc config **continuous=true** trong config resource workflơư của databricks hoặc setup pipeline mode=continuous

delta lake review
- delta lake là giao thức open-source hỗ trợ read/write file vào cloud storage, cung cấp cơ chế quản lí ổn định và có khả năng scale data cho lakehouse, trao quyền (empower)  cho phép user ingest data từ các source khác nhau và quản lí hiệu quả theo các layer bronze (raw), silver (cleaned), gold (curated), full acid transaction, time travel, schema enforcement, hỗ trợ cả batch và streaming workload.

mục tiêu của delta lake là ingest file từ external source như cloud object storage vào delta lake (các bảng delta table). delta table là một open table format hỗ trợ lakehouse architecture

Bên trong delta lake, bao gồm các delta table. cơ bản, các delta table lưu data bên trong một thư mục, data được lưu thành các file parquet, khi data thay đổi chúng được add vào delta log (transaction log kèm theo metadata) dưới dạng các file json. log này giúp lưu trữ transaction trong data và table version. transaction log cung cấp các thông tin về delta table. Khi insert, delete hoặc update data vào bảng, delta chỉ cần add transaction (log file), bảng sẽ được update và managed. tôi cho là bạn có thể từng quen thuộc với các khái niệm như write ahead log. chúng ta sẽ tìm hiểu sau cách delta lake xử lý các thao tác này đạt hiệu năng cao như thế nào

delta lake có một số tính năng chính:
- acid transactions: atomicity, consitency, isolation and durability cho các thao tác, nhiều user có thể cùng đọc ghi không bị conflict
- data manipulation language: delta lake hỗ trợ các thao tác insert, update, delete, merge,... để quản lí data
- time travel: user có thể query, revert về version cũ, dễ audit và khôi phục (facilitating auditing and recovery)
- schema evolution and enforcement: đảm bảo toàn vẹn data (data integrity) trong khi cho phép cập nhật schema cho phép thay đổi cấu trúc bảng
- unified batch, streaming processing, optimization, performance, scalability

# medallion architecture (multu hop)
Khi ingest data vào delta lake theo batch hay streaming hay cả hai, data liên tục tăng và cải thiện tiến bộ hơn (progressively) khi đi qua các layer trong kiến trúc: bronze, silver, gold.
- bronze layer: lưu trữ data raw, data được ingest vào layer này, chưa được xử lý, giữ nguyên cấu trúc ban đầu
- silver layer: data được làm sạch, transform, enrich, output là data được tinh chế (refined) có thể đáp ứng việc analysis
- gold layer chứa data được tuyển chọn (curated), tổng hợp chất lượng cao, tối ưu cho report, phân tích chuyên sâu, BI

# data ingestion from cloud storage
hãy đi sâu hơn vào cách convert file raw vào delta tables trong databricks.
data ingestion luôn là một bước quan trọng trong kiến trúc lakehouse, cho phép tận dụng khả năng lưu trữ lớn trên cloud storage, hỗ trợ nhiều loại format như csv, json, parquet, đảm bảo và hỗ trợ các tính năng như acid, time travel, schema enforement. 3 method chính để ingest file từ cloud storage vào delta table thông qua standard connectors
- create table as (ctas)
- copy into
- auto loader

## create table as (ctas)
tạo bảng delta table trỏ đến file ở cloud object storage, hàm read_files() đọc file theo location được config và xử lý về dạng bảng
- hỗ trợ nhiều dạng file: json, csv, xml, text, binary, parquet, avro, orc
- tự động detect file format và infer schema thống nhất (unified schema) cho tất cả các file
- có thể config file format trước 
- có thể sử dụng streaming table để ingest incremental vào delta lake sử dụng auto loader ngay sau đây (shortly)

```sql
create table new_table as
select *
from read_files(
    path_to_file, 
    format='',
    <other format specific>
)
```

## copy into
method này tạo một bảng mới sau đó copy data vào (incremental). data được copy từ cloud storage vào delta table. cách này thực hiện bulk load từ các file ở source vào bảng delta theo path location được config. 

```sql
create table new_table;

copy into new_table
from dir_path
fileformat=<file_type>
format_options()
copy_options()

```

- from: path đến location lưu file
- format option: config file format, quyết định các source file được xử lý, parse và interpreted
- copy options: config cách data được copy như schema evolution (merge schema) và idempotency (force) đối với các file đã ingest rồi thì sẽ skip không ingest lại nữa (incremental)

cách copy into phù hợp khi cloud storage liên tục có file mới được add, incremental batch

## auto loader
hãy delve into vào method thứ 3, auto loader dùng để ingest dữ liệu incremental khi có file mới (batch hoặc streaming) khi file được update vào storage. hỗ trợ cả python hoặc sql (thông qua declarative pipeline) với khả năng xử lý file lớn. auto loader được build dựa vào spark structured streaming. để đi sâu vào hơn, tao và chúng mày sẽ cần tìm hiểu về stream processing trong spark và build data pipeline trong databricks.

một vài điểm chú ý với auto loader:
- auto loader incremental và xử lý hiệu quả file mới với cả luồng batch và streaming khi data được thêm vào storage 
- tự động detect và ingest new file từ cloud storage vào delta lake tables, xử lý đơn giản với cả luồng batch và streaming mà không cần config nhiều
- auto loader hỗ trợ đa dạng cả python và sql thông qua declarative pipeline
- xử lý lượng file lớn, có khả năng scale
- build base spark structured streaming

### python auto loader
```python
spark.readStream
    .format(cloud files)
    .options(cloud file format, json)
    .option(cloud file schema location, checkpoint path)
    .load(volumne/catalog/schema/files)
writeStream
    .option(checkpoint location, checkpoint path)
    .trigger(processingTime=5s)
    .toTable(catalog.database.table)
```

data được ghi vào volume quản lí bởi unity catalog và ghi vào location được config catalog.

### auto loader by declarative pipelines
```sql
create or refresh streaming table catalog.schema.table 
schedule every 1 hour
as
select *
from stream read_files(
    path, 
    format
)
```

databricks recommend sử dụng streaming table để ingest data thay cho copy into. streaming table được quản lí bởi unity catalog hỗ trợ cả streaming và incremental. Khi tạo bảng streaming, pipeline tự động được tạo cho bảng này, streaming table được sử dụng để load data incremental từ kafka hoặc cloud storage

để tạo streaming table từ file trong volume, sử dụng auto loader, databricks recommend sử dụng cho hầu hết các trường hợp. bên dưới streaming tables tạo qua sql sử dụng serverless compute (xem thêm aws/dlt trong databricks)

khả năng xử lý của auto loader lớn hơn so với copy into (hàng triệu file + backfill so với hàng nghìn file). trong khi create table + spark read phù hợp với data nhỏ, đơn giản xử lý

**notes: managed và external tables in databricks**
- Managed table:
+ databricks quản lý cả data và metadata
+ data được lưu trong databricks managed storage
+ drop table sẽ xóa luôn data
+ recommend khi tạo mới table 
- external table:
+ databricks chỉ quản lí metadata
+ drop table không xóa luôn data
+ hỗ trợ nhiều loại format, bao gồm cả delta lake
+ recommend khi cần share data giữa các platform hoặc trỏ vào một nguồn data đã có sẵn bên ngoài

## Metadata columns
khi ingest data từ cloud storage, metadata columns khi source file name, modification time được append vào các _metadata column để lưu context cho mỗi row trong table được tạo trong lakehouse phục vụ cho các mục đích audit, lineage, debug

ví dụ khi ingest data từ các file như csv, txt, json vào data lake, một số cột metadata được thêm kèm với mỗi row để lưu các thông tin:
- last modification time của row 
- source file name

các cột metadata sẽ bị ẩn đi khi query thông thường. để hiển thị thông tin metadata column, cần bổ sung thêm property trong query khi select, một số tên cột metadata trong databricks:
- _metadata.file_modification_time
- _metadata.file_name

**Refer**: https://docs.databricks.com/aws/en/ingestion/file-metadata-column

## Rescued data column
khi ingest data, databricks hỗ trợ lưu thông tin data bị lỗi do mismatched hoặc unparseable field trong table thay vì drop đi.

trong quá trình ingest data, khi input data không match với schema của table, các hàm như *read_files()*, *spark.read* hoặc *auto loader* hỗ trợ rescue data, đảm bảo các cột không match về dữ liệu được lưu lại.

ví dụ: cloud storage location chứa các file data, trong đó có cột user và cost. khi ingest cột cost dạng bigint nhưng giá trị chứa exception, như "100$" dạng string. giá trị này sẽ không được ingest vào cột cost mà được lưu lại vào cột *_rescued_data* dưới dạng json để capture lại thông tin không khớp. cột cost sẽ để trống do đó không ảnh hưởng khi pipeline tiếp tục xử lý.

## ingesting semi-structured data 
ingest data dạng semi structured như json giúp xử lý parsing và transformation phức tạp hiệu quả hơn, ngoài ra còn hỗ trợ các phân tích nâng cao

có nhiều cách truy cập giá trị các field bên trong một json string

- string data type: select dạng `json_column:name` => dạng này đơn giản, raw, hiệu năng thấp hơn so với các method khác

- struct data type: defined schema với cột chứa json có cấu trúc nhất quán (consistent), query struct hiệu quả hơn query raw json `select schema_of_json()`

- variant data type: variant vừa được public từ 2025, có thể lưu cột với bất kì kiểu data nào, kể cả json, lí tưởng cho semi-structured, flexible, cải thiện hiệu năng, lưu trữ ở dạng nhị phân `select parse_json()` (https://www.databricks.com/blog/introducing-open-variant-data-type-delta-lake-and-apache-spark)


