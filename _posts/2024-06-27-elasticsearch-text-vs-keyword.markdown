---
title: "Elasticsearch text vs keyword"
layout: post
date: 2024-06-27 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
- elasticsearch
category: blog
author: devdiary
description: Nội dung như title
---

Phân biệt kiểu dữ liệu Text và Keyword của một field trong Elasticsearch.

# Khác biệt
Elasticsearch sẽ analyze một field kiểu text trước khi lưu vào inverted index trong khi keyword không được analyze (character filter, tokenizer, token filter) mà lưu vào inverted index luôn.

# Cách dùng
Khi index một document vào elasticsearch chứa string chưa được định nghĩa field trước đây, elasticsearch sẽ tạo một mapping động với cả 2 kiểu là text và keyword. Tuy nhiên, nên định nghĩa trước mapping kiểu dữ liệu của một field để tiết kiệm không gian lưu trữ và tăng tốc độ ghi. Ví dụ định nghĩa một field với một trong hay kiểu:

Keyword mapping
```shell
curl --request PUT \
  --url http://localhost:9200/text-vs-keyword/_mapping \
  --header 'content-type: application/json' \
  --data '{
 "properties": {
  "keyword_field": {
   "type": "keyword"
  }
 }
}'
```

Text mapping
```shell
curl --request PUT \
  --url http://localhost:9200/text-vs-keyword/_mapping \
  --header 'content-type: application/json' \
  --data '{
 "properties": {
  "text_field": {
   "type": "text"
  }
 }
}'
```

Multi fields
```shell
curl --request PUT \
  --url http://localhost:9200/text-vs-keyword/_mapping \
  --header 'content-type: application/json' \
  --data '{
 "properties": {
  "text_and_keyword_mapping": {
   "type": "text",
   "fields": {
    "keyword_type": {
     "type":"keyword"
    }
   }
  }
 }
}'
```

# Cách hoạt động
Hai kiểu dữ liệu được index khác nhau trong inverted index. Ví dụ ta có một document như sau:
```shell
curl --request POST \
  --url http://localhost:9200/text-vs-keyword/_doc/example \
  --header 'content-type: application/json' \
  --data '{
 "keyword_field":"The quick brown fox jumps over the lazy dog",
 "text_field":"The quick brown fox jumps over the lazy dog"
}'
```

Document
```json
{
    "_index": "text-vs-keyword",
    "_type": "_doc",
    "_id": "example",
    "_score": 1.0,
    "_source": {
        "keyword_field": "The quick brown fox jumps over the lazy dog",
        "text_field": "The quick brown fox jumps over the lazy dog"
    }
}
```

## Keyword
Elasticsearch sẽ không analyze keyword, string được index sẽ giữ nguyên giá trị

![Keyword data type](/assets/images/keyword-index.png)

## Text
String được index kiểu text sẽ đi qua bước analyze trước khi lưu vào inverted index. String sẽ được split và lower để index. Kiểm tra string được analyze như thế nào khi lưu vào elasticsearch:
```shell
curl --request POST \
  --url http://localhost:9200/text-vs-keyword/_analyze?pretty \
  --header 'content-type: application/json' \
  --data '{
  "analyzer": "standard",
  "text": "The quick brown fox jumps over the lazy dog"
}'
```

![Text data type](/assets/images/text-index.png)

## Query với text và keyword
Có 2 loại query trường dùng cho string:
- Match query
- Term query

Match Query sẽ analyze string thành các term trước khi tìm kiểm còn term query thì không analyze. Query elasticsearch sẽ tiếp tục match các term bên trong inverted index để tìm kiếm. Do đó việc có analyze sẽ ảnh hưởng đến việc tìm kiếm theo term nào, kết quả tìm kiếm sẽ khác nhau. 

# Một số use case


# Khi nào sử dụng text hay keyword
- Keyword:
    + Cần query chính xác string được lưu vào
    + Query được xử lý tương tự như các database khác
    + Sử dụng được wildcard query

- Text:
    + Triển khai tính năng autocomplete
    + Triển khai tính năng search system
