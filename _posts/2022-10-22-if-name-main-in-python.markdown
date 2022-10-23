---
title: "If name main in python"
layout: post
date: 2022-10-22 22:03
image: /assets/images/me.jpeg
headerImage: false
tag:
- software engineer
- python
category: blog
author: devdiary
description: if __name__ == "__main__" in python
---

# Cái nhìn thoáng qua
Mô tả một cách đơn giản thì khi có một file chứa đoạn lệnh này ```if __name__ == "__main__":```:
- Khi bạn chạy file đó thì đoạn code bên trong ```if __name__ == "__main__":``` sẽ được thực hiện
- Khi bạn import file đó bên trong các file khác thì đoạn code bên trong ```if __name__ == "__main__":``` sẽ không được thực hiện

Ví dụ ta có một folder **test** gồm 2 file **add_number.py** và **main.py**:
- test
    + add.py
    + main.py

Nội dung file như sau:

```python 
# add.py
def add(a, b):
    print(f"{a} + {b} = {a + b}")


if __name__ == "__main__":
    print(3, 4)
```

```python
# main.py
from test import add_number

if __name__ == "__main__":
    add_number.add(1, 2)
```

Nếu bạn chạy file **add.py** kết quả nhận được sẽ là ```3 + 4 = 7```. Tuy nhiên nếu bạn chạy file **main.py** thì kết quả in ra màn hình sẽ là *1 + 2 = 3* và đoạn lệnh ```print(3, 4)``` đặt bên trong khối ```if __name__ == "__main__":``` của hàm **add.py** sẽ không được thực hiện. Do ta import file **add_number.py** vào file **main.py** để sử dụng hàm ```add(a, b)``` nên đoạn lệnh ```print(3, 4)``` không được thực hiện.

Mục đích của việc này cho phép bạn có thể chạy các đoạn code không liên quan đến các hàm bạn đã viết mà không bị ảnh hưởng khi import các hàm này để sử dụng trong các file khác.

# Tìm hiểu kĩ hơn chút
```if``` là lệnh đều kiện, vì vậy đoạn lệnh ```if __name__ == "__main__":``` sẽ kiểm tra:
- Nếu biến ```__name__ == "__main__"``` là đúng thì thực hiện tiếp đoạn code thụt vào bên dưới
- Nếu biến ```__name__ == "__main__"``` là sai thì python sẽ không thực hiện đoạn code thụt vào bên dưới 

Vậy khi nào ```__name__``` bằng ```__main__```?

Ta sửa file **main.py** và file **add.py** như sau:

```python 
# add.py
def add(a, b):
    print(f"__name__ in add_two_number file is {__name__}")
    print(f"{a} + {b} = {a + b}")


if __name__ == "__main__":
    print(3, 4)
```

```python 
# main.py
from test import add_number

if __name__ == "__main__":
    print(f"__name__ in main file is {__name__}")
    add_number.add(1, 2)
```

Chạy thử lại file main.py kết quả nhận được sẽ là:
```shell
__name__ in main file is __main__
__name__ in add_two_number file is test.add_two_number
1 + 2 = 3
```

Trong python, top-code level là khái niệm chỉ môi trường của module đầu tiên được chạy để khởi động chương trình và import các module cần thiết vào để sử dụng. Ở ví dụ trên, ```main.py``` được chạy đầu tiên nên nó được hiểu là top-code level của chương trình. Biến ```__name__``` trong python gọi là biến global, mỗi file code ```.py``` đều có một biến ```__name__```. 

Trong môi trường top-code level, biến ```__name__``` được đặt giá trị bằng  ```""__main__""``` và có kiểu ```str```. Nếu bạn không set câu lệnh ```if __name__ == "__main__":```, thì biến ```__name__``` cũng được mặc định set giá trị bằng ```""__main__""```. Còn ở trong các module được import vào file **main.py** để sử dụng như **add_number.py** thì biến ```__name__``` được đặt giá trị là tên của module.

Ý tương về ```__name__ == "__main__"``` trong python cũng tương tự như trong nhiều ngôn ngữ khác như Java, C,... tuy nhiên không chặt chẽ như các ngôn ngữ này. Câu lệnh ```if __name__ == "__main__":``` có thể hữu ích khi chạy python sử dụng dòng lệnh (ngoài ra python còn cung cấp các cách chạy khác mình sẽ đề cập đến sau này) hoặc muốn test một vài chức năng trong module của bạn trước khi import nó để sử dụng trong các module khác (tránh side-effects - các tác dụng phụ có thể xảy ra). Tuy nhiên khi chạy những chương trình đơn giản trên 1 file duy nhất như dùng notebooks để visualize dữ liệu thì bạn có thể không cần quá quan tâm đến điều này.


