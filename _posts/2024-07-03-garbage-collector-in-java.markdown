---
title: "Garbage collector in java"
layout: post
date: 2024-06-27 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
- garbage collector
category: blog
author: devdiary
description: Nội dung như title
---

Garbage collector là một công cụ hay phương pháp dùng để giải phóng bộ nhớ khi không còn sử dụng, quản lí tài nguyên hiệu quả. Khi tạo một object, object này sẽ chiếm bộ nhớ RAM, khi object này không còn được sử dụng đến trong chương trình, một phần bộ nhớ trong RAM sẽ bị chiếm dụng, bộ nhớ của RAM liên tục bị chiếm dụng mà không giải phóng cho đến khi RAM không còn đủ dung lượng chương trình sẽ bị dừng.

GC là một thành phần quan trọng để sử dụng quản lí, phát hiện các vấn đề liên quan đến bộ nhớ (memory leak) và tối ưu bộ nhớ hiệu quả.

# Java sử dụng memory

![alt text](https://cdn.getmidnight.com/a8240cb8235e9c493a0c30607586166c/2024/05/Pasted-image-20240506135258.png)

Memory trong java được chia thành 3 vùng:

- Stack: Khi một method được khởi tạo, data dược lưu vào trong stack. Bao gồm data được khai báo các kiểu dữ liệu nguyên thủy như int, long, boolean,... Các object của method không được lưu trực tiếp trong stack, thay vào đó, các object này được lưu trogn heap, stack lưu con trỏ để trỏ đến vùng nhớ chứa dữ liệu trong heap. MỖi thread có stack riêng, hoạt động theo cấu trúc first in last out. Vì stack chứ các biến được tạo khi khởi tạo method do đó java sẽ biết được khi nào method đã chạy xong để thu hồi bộ nhớ

- Heap: Khi tạo một object trong java, chúng được lưu trong vùng nhớ heap. Tạo một list các item lưu ở vùng nhớ heap, list chỉ lưu các con trỏ trỏ đến dữ liệu thật. Heap được quản lí bởi GC

- Metaspace: Metaspace là các biến chứa giá trị vĩnh viễn được lưu như static, class information. Vùng nhớ này không bị quản lí bởi GC.

Ví dụ đoạn code sau:

```java
public void memoryTest() {
	int variable1 = 1;
	TestClass variable2 = TestClass.builder().number(2).build();
	TestClass variable3 = TestClass.builder().number(3).build();
	transformVariables(variable1, variable2, variable3);
	log.info("var1: {}, var2: {}, var3: {}", variable1, variable2.getNumber(), variable3.getNumber());
}

private void transformVariables(int var1, TestClass var2, TestClass var3) {
	var1 = 4;
	var2.setNumber(5);
	var3 = TestClass.builder().number(6).build();
}
```

Kết quả nhận được sẽ là:

```shell
var1: 1, var2: 5, var3: 3
```

- var1: Object var1 được lưu trong stack của method memoryTest (lưu giá trị gốc kiểu primitive type) do đó khi truyền vào method transformVariables nó không gửi giá trị tham chiếu mà gửi giá trị là 1. Do đó khi thay đổi giá trị của biến var1 trong method transformVariables (tạo vùng nhớ mới) sẽ không ảnh hưởng đến giá trị của biến.

- var2: Giá trị của biến var2 khi in ra bị thay đổi. Khi truyền biến var2 vào method transformVariables, var2 là object do đó java sẽ truyền giá trị con trỏ (reference type) của biến trỏ đến giá trị trong heap. Do đó khi thay đổi giá trị của class TestClass bằng set, giá trị đang được lưu bên ngoài sẽ bị thay đổi.

- var3: var3 cũng được truyền vào method giống như biến var2, tuy nhiên bên trong method transformVaribales, biến var3 được gán lại cho một object khác mới được khởi tạo, do đó khi kêt thúc method transformVaribales, object mới tạo náy sẽ bị xóa, còn object gốc bên ngoài không bị thay đổi vì. Do đó giá trị biến var3 khi in ra vẫn giữ nguyên bằng 3.

Minh họa ví dụ vừa rồi:
![alt text](https://cdn.getmidnight.com/a8240cb8235e9c493a0c30607586166c/2024/05/Pasted-image-20240506141550.png)

# Cấu trúc bộ nhớ trong java
![alt text](https://cdn.getmidnight.com/a8240cb8235e9c493a0c30607586166c/2024/05/Pasted-image-20240506141809.png)

Java chia bộ nhớ thành các phân vùng để quản lí hiệu quả vòng đời của các object, gồm 3 vùng:
- Young generation:

- Old generation:

- Metaspace:
