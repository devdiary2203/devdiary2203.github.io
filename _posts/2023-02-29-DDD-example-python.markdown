---
title: "DDD example python"
layout: post
date: 2024-02-01 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- domain driven design
- python
category: blog
author: devdiary
description: Nội dung như title
---

Bài viết sẽ review qua về domain driven design và clean architecture là 2 concept quan trọng khi lập trình

# DDD - Domain driven design
Đây là một cách tiếp cận code (concept, quy tắc) một ứng dụng dựa vào phát triển các model liên quan đến domain của bài toán cần giải quyết. 

# Clean architecture
Clean architecture là một SA pattern sử dụng để thiết kế chương trình dễ maintain, test, mở rộng khi ứng dụng phát triển thêm yêu cầu. Mỗi layer trong pattern này có trách nhiệm riêng nhưng thường được chia làm 3 phần chính:

- Domain layer: layer xử lý business logic, cụ thể:
+ Định nghĩa các business logic
+ Các phụ thuộc vào các layer khác liên quan
+ Thiết kế dễ hiểu và maintain

- Application layer: layer xử lý về UI, các logic bên trong của ứng dụng
+ Dễ test 
+ Xử lý các use cases, workflow

- Infrastructure layer: layer xử lý việc kết nối đến các database, các logic liên quan đến kết nối database
+ Xử lý chi tiết các yêu cầu về technical liên quan đến hệ thống
+ Thực hiện lưu trữ, giao tiếp với data

- Presentation: Ngoài ra còn có lớp presentation chứa các logic liên quan đến UI, API giao tiếp với bên ngoài phụ thuộc vào application layer

# Ưu điểm của DDD và Client architecture
- Dễ maintain
- Dễ test: Codebase được chia các layer rõ ràng, có thể viết test riêng cho từng phần, dễ xử lý khi có bug chỉ cần tập trung xử lý một phần logic cụ thể
- Dễ mở rộng: Các tính năng được phát triển thêm được bổ sung theo các model liên quan đến business đó, làm codebase không bị chồng chéo nhau mỗi khi update

# DDD concepts
DDD đặt domain model làm trung tâm của chương trình. Tất cả các thành phần khác trong chương trình được thiết kế để phục vụ cho các model. Concept của DDD bao gồm:
- Ubiquitos language: Đảm bảo mô tả domain cho tất người tham gia trong project đều hiểu được vấn đề đang giải quyết
- Bounded contexts: Tách biệt các phần của domain để các team join vào phát triển các phần trong project không bị ảnh hưởng lẫn nhau
- Entities: các object mô tả đối tượng trong thực tế của project
- Value objects: là các đối tượng chỉ dùng để lưu giá trị, không cần phân biệt với nhau trong chương trình. Ví dụ trong chương trình có các mức voucher giảm giá 10%, 20%,... thì bất kì loại voucher nào cũng có thể có các mức này, không cần phân biệt các loại voucher với nhau.
- Aggregates: các nhóm được tổ chức thành tập hợp các entity liên quan đến nhau, đảm bảo tổ chức các đơn vị trong codebase luôn nhất quán. Logic xử lý một nhóm phải đáp ứng được tất cả entity, để khi có thêm entity mới có thể dễ tích hợp tiếp.
- Repositories: chịu trách nhiệm lưu trữ và lấy, tổng hợp các entity từ database

# So sánh DDD và Client architecture
DDD là một kĩ thuật tập trung phát triển code dựa vào domain model, liên quan đến business, trong khi client architecture là một pattern hướng đến giảm sự phụ thuộc giữa các thành phần trong project. Ở đây chỉ tìm ra các điểm tương đồng và khác biệt trong khi áp dụng DDD và client architecture vào project

- Điểm chung:
Cả DDD và CA hướng đến hỗ trợ entity, thành phần nắm giữa business của project. 
- Khác:
+ DDD tập trung giải quyết domain logic, thông qua các thành phần như entities, value object, aggregate, repository, trong khi đó, CA hướng dẫn giải quyết toàn bộ hệ thống, đảm bảo giảm sự phục thuộc giữa các thành phần với nhau.
+ CA được áp dụng cho các tính năng lớn của ứng dụng, đóng gói các business logic, điều phối luồng data từ các thành phần entity, value object. DDD áp dụng để xử lý các yêu cầu business bên trong tầng applicaion. Một cái hướng đến giải quyết kiến trúc phần mềm, một cái là concept, các quy tắc tiếp cận giải quyết vấn đề. 

# Sự phụ thuộc các thành phần trong CA:
- Presentation layer phụ thuộc vào application layer
- Application layer phụ thuộc vào domain layer
- Infrastructure layer phụ thuộc vào domain layer
- Domain layer không phụ thuộc vào các layer khác

Giữa các layer có sự phụ thuộc 1 chiều với nhau, không có ngược lại (dependency injection), domain layer là trung tâm không phục thuộc vào các layer khác. Do đó khi ứng dụng thay đổi về logic, thêm tính năng mới có thể dễ dàng update, mở rộng.

# Ví dụ
Giả sử một api update thông tin voucher cho khách hàng, với mỗi voucher sẽ được giảm một số tiền nào đó. Ngoài ra, có thể cần lấy thông tin từ API bên thứ 3 như thông tin user, lịch sử mua hàng,... Project này có thể được cấu trúc như sau:
```
- `app/`
    - `__init__.py` (initializes the application)
    - `domain/` (contains entities, value objects, and interfaces)
        - `__init__.py`
        - `interfaces/` (directory containing all interface definitions)
            - `__init__.py`
            - `ivoucher_repository.py` (interface for VoucherRepository)
            - `ithird_party_service.py` (interface for ThirdPartyService)
        - `entities/`
            - `__init__.py`
            - `voucher.py` (contains the voucher entity class)
        - `value_objects/`
            - `__init__.py`
            - `money.py` (contains the Money value object class)
    - `application/` (contains use cases and workflows)
        - `__init__.py`
        - `voucher_service.py` (contains the VoucherService class)
    - `infrastructure/` (contains technical details like database and web server)
        - `__init__.py`
        - `repositories/`
            - `__init__.py`
            - `voucher_repository.py` (implementation of VoucherRepository interface)
        - `services/`
            - `__init__.py`
            - `third_party_service.py` (implementation of ThirdPartyService interface)
    - `main.py` (entry point for the FastAPI application)
```

```main.py
from fastapi import FastAPI

from app.application.voucher_service import VoucherService
from app.domain.entity.voucher import Voucher
from app.domain.model.money import Money
from app.infrastructure.repository.voucher_repository import VoucherRepository
from app.infrastructure.service.third_party_service import ThirdPartyService

import uvicorn

app = FastAPI()

# Dependency injection
voucher_repo = VoucherRepository()
third_party_service = ThirdPartyService(base_url="localhost")
voucher_service = VoucherService(voucher_repo, third_party_service)


@app.post("/voucher")
async def create_voucher(title: str, start_date: str, end_date: str, initial_bid: Money):
    voucher = Voucher(title, start_date, end_date, initial_bid)
    await voucher_service.create_voucher(voucher)
    # save voucher to the repository
    return {"message": "Voucher created successfully!", "voucher": voucher.__dict__}


# Start application using unicorn
if __name__ == "__main__":
    uvicorn.run("main:app", host="0.0.0.0", port=8000, reload=True)
```
