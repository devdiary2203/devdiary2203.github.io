---
title: "Builder pattern"
layout: post
date: 2022-09-24 06:42
image: /assets/images/markdown.jpg
headerImage: false
tag:
- design pattern
- software engineer
category: blog
author: devdiary
description: Builder Pattern
---

# Introduction
Code của bạn bằng một cách nào đó cần phải có tính linh hoạt, dễ bảo trì sau này, có khả năng tái sử dụng. Đó là khi design pattern cần được sử dụng đến. Lợi ích của design pattern:
- Giảm thời gian đọc hiểu khi sử dụng code: Khi ta code khoa học, có tổ chức, người khác hoặc chính chúng ta nhìn vào có thể hiểu được đoạn code này làm gì, sử dụng thế nào trong chương trình.
- Giảm thời gian fix bug
- Khả năng kiếm thử code tốt hơn
- Dễ tái sử dụng code: dễ thấy nhất là một đoạn code connect sql ta chắc chắn sẽ hay sử dụng ở bất kì project nào nên đơn giản là chỉ cần copy paste nguyên đoạn code này sang project mới mà không cần phải tốn thời gian viết lại

# Builder pattern
Đây là một pattern khá phổ biến từ người mới biết đến code cho đến những senior lâu năm đều có thể gặp hoặc đang sử dụng. Pattern này rất linh hoạt khi cần tổ chức các đối tượng phức tạp, nhiều thuộc tính, mỗi thuộc tính lại có nhiều lựa chọn.

Ví dụ: một đối tượng ở đây là mèo. Một con mèo thì có thể được biểu diễn bởi nhiều thuộc tính:
- màu lông (xám, đen, vằn, trắng...)
- độ dài lông (lông ngắn, lông dài)
- nguồn gốc (mèo ta, mèo anh,...)

Một ví dụ khác với câu lệnh SQL. Ta có câu lệnh đơn giản như sau: ```SELECT ... FROM ... WHERE```

Sau mỗi mệnh đề select ta có thể truyền tên trường cụ thể mà ta muốn select hoặc select *, count, cast... Tương tự sau mệnh đề from có thể là tên bảng bất kì,...

Builder pattern sẽ giúp biểu diễn một con mèo phức tạp ở trên bằng code một cách logic, tạo các thuộc tính của mèo nhanh chóng và linh hoạt.

# SQL queries
Bây giờ ta sẽ áp dụng builder pattern vào thực tế bằng cách tạo câu lệnh sql. Như đã nói ở trên, trong một câu lệnh sql có nhiều thành phần giống nhau mà câu lệnh nào ta cũng gặp phải như các mệnh đề select, from, where, having... Việc viết những câu lệnh như vậy nhiều lần xảy ra vấn đề:
- Chán :) viết cái gì nhiều, lặp đi lặp lại thì cũng chán thôi
- Chán sẽ dẫn đến dễ nhầm lẫn, viết sai câu lệnh dẫn đến query sai kết quả, viết nhầm cú pháp. Bạn có thể fix lỗi nhưng đỡ tốn thời gian cho việc này để dành thời gian làm việc khác vẫn hơn
- Việc viết một câu lệnh dài không chỉ dễ gây nhầm lẫn mà còn có thể gây ra những lỗi liên quan đến bảo mật. Sử dụng builder pattern có thể dễ dàng kiểm tra từng phần trong câu lệnh. Đặc biệt phù hợp với những lập trình viên thường xuyên phải viết những câu lệnh sql dài, khó kiểm soát.

Mình sẽ ví dụ một class để tạo câu lệnh sql sử dụng builder pattern. Tất nhiên đối với các câu lệnh đơn giản như ```SELECT * FROM db.table``` thì viết tay chắc sẽ nhanh hơn, nhưng bạn thử dụng với những câu lệnh dài hơn xem.

```
class QueryBuilder:

    def __init__(self):
        """
        Khởi tạo một class QueryBuilder gồm các thuộc tính là các mệnh đề:
        - select_clause
        - from_clause
        - where_clause
        - groupby_clause

        Khi sử dụng, bạn có thể bổ sung thêm các thuộc tính là các mệnh đề khác mà bạn cần trong câu truy vấn
        """
        self.select_clause = ""
        self.from_clause = ""
        self.where_clause = ""
        self.groupby_clause = ""
        # self.having_clause = ""
        # self.join_clause = ""
        # self.update_clause = ""
        # self.insert_clause = ""
        # ...

    def select_builder(self, clause_value: str):
        """
        Method này truyền vào đoạn truy vấn select
        :param clause_value: đoạn truy vấn select mà bạn muốn
                clause_value: str để ràng buộc giá trị truyền vào của method này là kiểu string
        :return: self
        """
        self.select_clause = clause_value
        return self

    def from_builder(self, clause_value: str):
        self.from_clause = clause_value
        return self

    def where_builder(self, clause_value: str):
        self.where_clause = clause_value
        return self

    def groupby_builder(self, clause_value: str):
        self.groupby_clause = clause_value
        return self

    def build_select(self):
        query = None

        # Kiểm tra xem mệnh đề select, from có trống không, nếu có thì trả về lỗi cho chương trình
        if self.select_clause == "" or self.select_clause is None:
            return query

        if self.from_clause == "" or self.from_clause is None:
            return query

        # Ta có thể các đoạn lệnh để kiếm tra điều kiện của từng mệnh đề trong các method clause_builder

        if self.where_clause != "" and self.where_clause is not None:
            where_clause = f"WHERE {self.where_clause}"
        else:
            where_clause = ""

        if self.groupby_clause != "" and self.groupby_clause is not None:
            groupby_clause = f"GROUPBY {self.groupby_clause}"
        else:
            groupby_clause = ""

        # Ghép các mệnh đề thành câu truy vấn hoàn chỉnh
        query = f"SELECT {self.select_clause} " \
                f"FROM {self.from_clause} " \
                f"{where_clause} " \
                f"{groupby_clause};"
        return query

```

Ở đây mình viết một class để build câu query giả sử có các mệnh đề *select, from, where, groupby*. Bạn có thể bổ sung thêm các mệnh đề khác theo nhu cầu. Method ```build_select()``` dùng để build câu lệnh *select* hoàn chỉnh.

Bây giờ ta thử build một câu truy vấn đơn giản từ class vừa viết như sau:

```
query_builder = QueryBuilder()
query_builder.select_builder("catName, catColor")\
    .from_builder("Cats")\
    .where_builder("catAge < 10")\
    .groupby_builder("catColor")

query = query_builder.build_select()
print("SQL query: " + query)
```

Câu truy vấn in ra như sau:
```SQL query: SELECT catName, catColor FROM Cats WHERE catAge < 10 GROUPBY catColor;```

Có một kĩ thuật nhỏ là mỗi method build mệnh đề *select, from, where, groupby* mình đều trả ```self``` nghĩa là trả về chính đối tượng **QueryBuilder** mà ta đang xây dựng. Cách này dùng khi bạn muốn build câu lệnh sử dụng pipeline, tương tự cách sử dụng các hàm trong nhiều thư viện như scikit-learn, spark.

```
query_builder.select_builder("catName, catColor")\
    .from_builder("Cats")\
    .where_builder("catAge < 10")\
    .groupby_builder("catColor")
```

Trong trường bạn câu lệnh của bạn không có mệnh đề *where* hay *groupby* thì bạn chỉ cần bỏ qua hoặc truyền vào giá trị *None* hoặc string rỗng ```""``` thì method *builder_select* sẽ bỏ qua mệnh đề này. Ngoài ra bạn có thể tự viết thêm các method để build những câu query khác như ```build_update()```, ```build_insert()```, ```build_delete()```

Bây giờ, khi sử dụng builder pattern để tạo ra một câu truy vấn sql sẽ chỉ cần viết ra một file riêng và import vào bất kì notebooks nào khác mà bạn muốn sử dụng. Code sẽ trở nên dễ đọc, trực quan hơn, và dễ debug khi câu lệnh sql có lỗi cú pháp (syntax).

Thanks!
