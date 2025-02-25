---
title: "HBase balancer"
layout: post
date: 2025-01-29 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
- hbase
category: blog
author: devdiary
description: Nội dung như title
---

Trong database phân tán, load balancer được hiểu là một module đảm bảo tải được phân tán đều vào các node theo cấu hình tài nguyên được sử dụng đã config trước. Hình dưới minh họa load balancer, các regione (shard) trong các bảng có tải không đều trước khi cân bằng và tải đã được chia đều sau khi cân bằng (số request nhận được mỗi node region bằng nhau vì số region trong mỗi node là giống nhau). 


Tùy theo kiến trúc data, tính năng load balancer có thể khác nhau, ví dụ load balancer có thể là layer stateless đặt trước cluster để điều phối traffic đến các node; hoặc mỗi database server tự xử lý điều phối request.

Trong HBase hỗ trợ một số loại load balancer được cài đặt sẵn như FavoredStochasticBalancer, SimpleLoadBalancer, StochasticLoadBalancer, RSGroupBasedLoadBalancer,... Mỗi loại load balancer đều cài đặt dựa trên StochasticLoadBalancer và override các tính năng để đáp ứng yêu cầu riêng

# HBase background
Kiến trúc của HBase bao gồm một số khái niệm chính:
- Tables: HBase tổ chức data thành các bảng, mỗi bảng tập hợp các row theo các cặp key-value. Các row được sắp xếp theo thứ tự từ thấp nhất sẽ xuất hiện đầu tiên trong bảng, mỗi bảng được chia nhỏ thành một hoặc nhiều region.

- Region: Mỗi bảng được chia thành các tập nhỏ không giao nhau, gọi là các region. Các region là đơn vị cơ bản để truy vấn data.

- Region server: là server lưu thông tin các region, xử lý khi client gửi request thông qua lời gọi rpc, data được lưu trong hdfs trong các datanode

- Hmaster: master node trong hbase, điều phối hoạt động giữa region server thực hiện các thao tác như region assignment, balancing,...

Hmaster là thành phần trung tâm của hbase, có nhiệm vụ:
- Phân phối các region vào region server
- Theo dõi healthy của các regionserver
- Cân bằng tài nguyên sử dụng giữa các region server
- Clear dữ liệu tmp trong cụm

Region server chịu trách nhiệm xử lý các request từ client như put, get, update, delete,... Mỗi region server chứa một tập các region, các region có thể thay đổi động khi thêm region server vào cluster, xóa region server trong cluster,...

Load balancer: HBase hỗ trợ auto sharding, auto balanced horizontal theo key-value. Load balancer chịu trách nhiệm giữ cho cluster hoạt động ổn định và tối ưu tài nguyên sử dụng khi thay đổi dữ liệu.

- Auto sharding: Mỗi table được chia thành các region, mỗi region (shard) nếu tăng kích thước dữ liệu đến một ngưỡng sẽ được split thành 2 region và nếu 2 region cạnh nhau đủ điều kiện có thể merge lại thành một region. Nhiệm vụ của balancer là đảm bảo cân bằng tải giữa các node khi có sự thay đổi về region.

- Auto balanced: Khi scale theo chiều ngang bằng cách thêm hoặc xóa region server trong cluster, các region tự động di chuyển sang region server mới từ region server cũ.

# HBase balancer goals
Load balancer khi triển khai trong một db phân tán như hbase đảm bảo tài nguyên ở các node (ví dụ region server) được sử dụng hợp lí bằng cách phân phối đều tải trên các node. Lượng request đến các region server có thể điều chỉnh theo các yêu tố:
- Số request trên mỗi node so với các node khác 
- Số region trên mỗi node trên mỗi table
- Có bao nhiêu region trên mỗi rack của cluster
- Có bao nhiêu data trên mỗi node so với các node khác

Region assignment có trách nhiệm gán region vào các region server, xảy ra khi tạo/xóa bằng, chạy balance, split/merge region,... theo plan của balancer đã cung cấp


# Balancer architecture
Stochastic Load Balancer là kiểu balance được cài đặt sẵn trong hbase, được cho là có hiệu năng tốt, có khả năng mở rộng. Kiểu cấu hình này chạy mô phỏng trạng thái hiện tại của cluster, input và output mong muốn. Trong suốt quá trình mô phỏng:

Cost function: cost function của balancer để tính toán chi phí (giá trị nằm giữa 0 và 1), 0 là cluster đang ở trạng thái cân bằng tốt nhất, 1 là trạng thái mất câng bằng. 

Cost function trong Hbase được sử dụng để đánh giá mức độ cân bằng theo các tiêu chí khác nhau như:
- Locality: vị trí dữ liệu trên cùng một server
- Region spread across region server: phân bố các vùng dữ liệu trên các region server

Các cost function có sẵn trong hbase:
- Server locality cost function: xác định mức độ locality cả dữ liệu trên các server
- Rack locality cost function: xác định mức độ locality theo rack

Cost function có thể tùy chỉnh để điều chỉnh cân bằng dữ liệu theo nhu cầu.

Candidate generator: là một hàm cân bằng khi được gọi sẽ đưa ra một trong 2 action sau để tối ưu cân bằng trong cluster:
- Swap region: Đổi chỗ region r1 trên rs1 với region r2 trên rs2
- Move region: di chuyển region r1 từ rs1 sang rs2

Mục đích của candidate generator là cải thiện tính cân bằng trong cluster theo một tiêu chí nhất định như locality, load balancing. Hbase cung cấp một số candidate generator:
- Random candidate generator: chọn ngẫu nhiên các region để đổi chỗ hoặc di chuyển
- Load candidate generator: cân bằng dựa vào mức độ sử dụng tài nguyên
- Locality candidate generator: tối ưu dự vào vị trí dữ liệu trong cluster

Candidate generator có thể tùy chỉnh để điều chỉnh cân bằng data theo nhu cầu riêng.


# Balancer performance
https://blog.flipkart.tech/architecture-of-apache-hbase-balancer-337134a3895d

