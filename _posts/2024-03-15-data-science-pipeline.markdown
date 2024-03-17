---
title: "Data science pipeline"
layout: post
date: 2024-03-15 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- data science
- mlops
category: blog
author: devdiary
description: Nội dung như title
---

# Data science pipeline
Data science pipeline có thể được hiểu là luồng liên kết nhiều thành phần khác nhau, data được vận chuyển qua từng bước để tạo thành một quy trình xử lý từ đầu đến cuối. Một pipeline cơ bản có thể được tổ chức gồm các bước sau:

[Ảnh]

- Data retrieval and ingestion: Data là thành phần quan trọng nhất của các project. Bước đầu tiên trong pipeline sẽ thu thập dữ liệu từ các nguồn khác nhau để đưa vào project. Dữ liệu cần được nhận dạng từ các nguồn, kiểu dữ liệu là gì, thông tin cần lấy, lưu trữ tập trung dữ liệu thu thập được vào một kho tập trung để gửi đến bước tiếp theo trong pipeline.
- Data preparation: Để mô hình đạt được độ chính xác, dữ liệu cần phải được xử lý. Bước chuẩn bị dữ liệu bao gồm việc áp dụng các kĩ thuật như classification, cleaning, transformation, feature selection và feature engineering.
- Model training: Sau khi dữ liệu đã được thu thập và xử lý để chọn lọc ra các thông tin quan trọng nhất, các model có khả năng giải quyết bài toán sẽ được training với dữ liệu này.
- Model evaluation and tuning: Khi model được training xong, cần được đánh giá độ chính xác bằng các độ đo và tinh chỉnh model (theo dữ liệu, hyperparameters, chọn model phù hợp)
- Model development: Sau khi đánh giá, tinh chỉnh hoàn tất, model tốt nhất được chọn. Lúc này model cần được deploy hay còn gọi là serving, đây là bước tích hợp model vào sản phẩm thật để đưa ra các dự đoán.
- Monitoring: Đây là bước cuối cùng đảm bảo model sau khi đưa vào vận hành đáp ứng được chất lượng yêu cầu. Các vấn đề cần được kiểm tra, giám sát liên tục như độ chính xác của model (có thể thay đổi theo thời gian do dữ liệu thay đổi), chất lượng dữ liệu, tài nguyên xử dụng (ram, cpu, gpu,...)

# Tại sao cần xây dựng data pipeline
Mỗi bước trong pipeline ở trên đều chịu trách nhiệm riêng của mình. Việc xây dựng pipeline giúp tự động hóa quy trình triển khai model từ lúc có dữ liệu thô đến khi ra được model thành phẩm đưa vào dự đoán bài toán thực tế. Pipeline này giúp giảm thời gian triển khai các bước lặp đi lặp lại: Nhận dữ liệu -> xử lý -> training -> serving -> nhận dữ liệu mới ->... DS có thể tập trung vào cải thiện từng step. Trường hợp cần cải thiện data, model, monitor, ta chỉ cần tập trung cải thiện ở step liên quan thay vì phải quan tâm đến toàn bộ pipeline

# Kedro
Kedro là một opensource framework hỗ trợ xây dựng pipeline cho DS, giúp xây dựng pipeline, tạo sẵn cấu trúc cơ bản cho một project DS giảm thời gian phát triển, có khả tái sử dụng code, dễ maintain. Một số đặc điểm của kedro:
- Reproducibility: Kedro tạo ra các pipeline có khả năng triển khai và tái sử dụng trên các nền tảng khác như như windows, mac, linux do có thể cài đặt bằng các install package python.
- Modularity: Chia code thành các phần nhỏ, mỗi phần có trách nhiệm riêng giúp dễ đọc và dễ maintain.
- Maintainability: Kedro tạo ra một template chung cho các project giúp lập trình việc dễ tiếp cận với bất kì project nào và không cần phải viết lại các thành phần cơ bản của project nhiều lần.
- Versioning: Kedro cung cấp tính năng tracking dữ liệu, cấu hình các phiên bản cho pipeline để dễ quản lí, đánh giá.
- Documentation: Code được hỗ trợ bổ sung documentation tương ứng dễ đọc dễ hiểu.
- Seamless packagling: Các project tạo bảng kedro có khả năng tích hợp với các công cụ khác để triển khai như airflow, docker.

## Kedro catalog
Kedro cung cấp một khái niệm gọi là catalog, đây là nơi lưu trữ thông tin của tất cả các nguồn dữ liệu được sử dụng trong project. Catalog được lưu trữ dưới dạng file *catalog.yaml* trong thư mục config với các trường biểu diễn loại thông tin, kiểu dữ liệu, đường dẫn đọc dữ liệu:
```yaml
companies:
  type: pandas.CSVDataset
  filepath: data/01_raw/companies.csv
```
Ví dụ trên lưu thông tin của dữ liệu companies với các tham số để project có thể đọc dữ liệu này bao gồm:
- type: kiểu dữ liệu là csv. Kedro hỗ trợ config các kiểu dữ liệu xử lý bằng thư viện pandas hoặc matplotlib.
- filepath: đường dẫn đến folder chứa dữ liệu
- Ngoài ra còn một số các tham số khác như versioned, load_args, save_args, file_format,... tùy theo yêu cầu, xem chi tiết tại [Link-https://docs.kedro.org/en/stable/data/data_catalog_yaml_examples.html]

## Setup
Kedro cung cấp cách cài đặt dưới dạng cài thư viện của python. Trước khi cài đặt kedro, ta cần đảm bảo cài sẵn git, python trên máy để tích hợp được kedro.
### Cài đặt môi trường ảo
Đầu tiên ta cần cài môi trường ảo python dùng cho project của mình. Có thể sử dụng anaconda, pipvenv,... Ở đây ta sử dụng anaconda để tạo một môi trường ảo (trong windows cần mở của số *Anaconda Powershell Prompt*)

```shell
conda create --name kedro python==3.10
```

Init môi trường vừa tạo:
```shell
conda activate kedro
```
Trên trang chính thức của kedro, các phiên bản python đang hỗ trợ >3.8. Sau khi tạo môi trường xong, ta cần cài thư viện kedro:
```shell
pip install kedro
```

Ngoài ra một số phiên bản có thể cần cài đặt thêm thư viện kedro-viz (trường hợp khi cài kedro chưa có thư viện kedro-viz đi kèm). Đây là thư viện dùng để mô phỏng cấu trúc các thành phần bên trong project được tạo.
```shell
pip install kedro-viz
```

### Khởi tạo project
Để khởi tạo một project kedro ta sử dụng lệnh *kedro new*. 
```shell
kedro new --name=first_project --tools=lint,docs --example=n
```

Trong câu lệnh trên còn có các option khác kedro cung cấp để customize thêm cho project:
- **--name**: Tên của project, sau khi chạy, kedro sẽ tạo ra một thư mục tên là first_project 
- **--tools**: Kedro cung cấp thêm các công cụ hỗ trợ sau
    + lint: công cụ format code đúng chuẩn
    + test: công cụ hỗ trợ viết test code
    + log: ghi log trong project thay thế cho lệnh print
    + docs: cài đặt thêm documentation cho code, giúp code dễ đọc mục đích của các hàm, dễ maintain
    + data folders: bổ sung 1 folder để quản lí data
    + pyspark: cấu hình thêm thư viện để chạy project sử dụng pyspark
    + kedro viz: tool dùng để mô tả cấu trúc code
- **--example**: Bổ sung ví dụ code, dữ liệu khi mới bắt đầu với kedro.

Sau khi chạy lệnh trên, một thư mục tên là first_project được tạo với cấu trúc bên trong thư mục như sau:
- conf: Chứa các file config cho project như user, password, tham số model input.
- data: Thư mục chứa input, output data. Bên trong thư mục này được chia thành 9 thư mục con-layer (có thể thay đổi theo phiên bản kedro) để phân loại các loại data theo mục đích bao gồm:
    + 01_raw
    + 02_intemidiate
    + 03_primary
    + 04_feature
    + 05_model_input
    + 06_models
    + 07_model_output
    + 08_reporting
    + 09_tracking
- docs: Chứa các file liên quan đến project documentation
- logs: Chứa các file ghi log của project khi chạy
- notebooks: Chứa các file notebooks được sử dụng trong project như đánh giá, EDA dữ liệu
- src: Chứa source code của project bao gồm các hàm data processing, model training, pipeline

# First project
Để áp dụng các tính năng mà kedro cung cấp ở trên và hiểu được ưu điểm của việc sử dụng kedro trong các project data science, ta sẽ thử làm một project phát hiện bất thường trong dữ liệu sử dụng kedro framework.

## Bước 1: Cài đặt môi trường và khởi tạo project
Ta vẫn sử dụng conda để tạo một môi trường ảo tên là *kedro-anomaly-detection* dành riêng cho project này, sử dụng phiên bản python 3.10 và active môi trường này:
```shell
conda create --name kedro-anomaly-detection python==3.10
```

```shell
conda activate kedro-anomaly-detection
```

Cài đặt kedro:
```shell
conda activate kedro-anomaly-detection
```

Khởi tạo một project dùng kedro, apply công cụ lint và docs để hỗ trợ chỉnh sửa code đúng chuẩn:
```shell
kedro new --name=anomaly_detection --tools=lint,docs --example=y
```

Sau khi chạy lệnh trên, kedro sẽ khởi tạo một project với thư mục tên *anomaly_detection*, bên trong chứa các thư mục con:
```shell
README.md		conf			data			docs			notebooks		pyproject.toml		requirements.txt	src
```
[Ảnh]

Kedro đã xây dựng sẵn một số thư viện cần thiết để bắt đầu project trong file *requirements.txt*, ta sẽ cài đặt các thư viện này vào trước khi bắt đầu xây dựng pipeline:
```shell
pip install -r requirements.txt
```

## Bước 2: Data setup







