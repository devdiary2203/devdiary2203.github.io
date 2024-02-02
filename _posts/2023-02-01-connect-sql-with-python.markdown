---
title: "Connect SQL with python"
layout: post
date: 2024-02-01 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- data engineer
- python
category: blog
author: devdiary
description: Nội dung như title
---

Để connect sql với python có một số cách sau:
- Sử dụng query sql native, giao tiếp với database thông qua thư viện chuẩn của python và API database
- Sử dụng các thư viện hỗ trợ ORM
- Sử dụng SQL query builder để build query

Giả sử ta có query sau:
```
SELECT
 c.country
 ,c.year
 ,MAX(e.horse_power) AS max_horse_power
FROM public.cars c
JOIN public.engines e
 ON c.engine_name = e.name
WHERE c.country != 'USA'
GROUP BY
 c.country
 ,c.year
HAVING MAX(e.horse_power) > 200
ORDER BY max_horse_power DESC
```

Bỏ qua các trường, mục đích của câu query, trong query này, có nhiều các toán tử hay được sử dụng. Để query câu lệnh này đến database ta có thể dùng một số cách sau:

# Raw SQL
Cách phổ biến nhất khi mới bắt đầu giao tiếp với sql qua python là viết câu lệnh sql sau đó giao tiếp với sql thông qua thư viện hỗ trợ client sql thuần như *pymysql*.

```
import pymysql.cursors

# Connect to the database
connection = pymysql.connect(host='localhost',
                             user='user',
                             password='passwd',
                             database='db',
                             cursorclass=pymysql.cursors.DictCursor)

with connection:
    with connection.cursor() as cursor:
        # Create a new record
        sql = "INSERT INTO `users` (`email`, `password`) VALUES (%s, %s)"
        cursor.execute(sql, ('webmaster@python.org', 'very-secret'))

    # connection is not autocommit by default. So you must commit to save
    # your changes.
    connection.commit()

    with connection.cursor() as cursor:
        # Read a single record
        sql = "SELECT `id`, `password` FROM `users` WHERE `email`=%s"
        cursor.execute(sql, ('webmaster@python.org',))
        result = cursor.fetchone()
        print(result)
```

- Ưu điểm:
+ Dễ dùng, implement nhanh
- Nhược điểm:
+ Code khó mở rộng hơn khi ứng dụng cần nhiều câu query hơn, connect đến nhiều database hơn
+ Câu lệnh SQL cần viết chuẩn với cú pháp từng loại DB

# ORM
ORM (Object relational mapping) là một kĩ thuật giao tiếp với các database thông qua các class và object được tạo trong code mapping với các table trong database. Đây là cách phổ biến được dùng trong nhiều ứng dụng hiện nay. Trong python, thư viện phổ biến hỗ trợ ORM được sử dụng là SQLAlchemy. Ngoài ra trong một số framework như Django, FastAPI cũng hỗ trợ ORM
- Ưu điểm:
+ Dễ đọc, an toàn, không cần phải viết query SQL cụ thể mà giao tiếp qua class, object => do đó thích nghi với nhiều loại database
+ Phù hợp với các ứng dụng lớn, cần tính an toàn
- Nhược điểm:
+ Mất thời gian để tìm hiểu cách sử dụng hơn so với thư viện hỗ trợ query thuần như pymysql
+ Cài đặt phức tạp hơn, các hàm query bị hạn chế hơn do đó khi cần query phức tạp khó đáp ứng và cài đặt

```
from dotenv import load_dotenv
import os

from sqlalchemy import URL, create_engine, func, select
from sqlalchemy.orm import DeclarativeBase, Mapped, Session, mapped_column
from sqlalchemy.types import Integer, String

class Base(DeclarativeBase):
    pass

class Cars(Base):
    __tablename__ = "cars"
    
    manufacturer: Mapped[str] = mapped_column(String(64))
    model: Mapped[str] = mapped_column(String(64))
    country: Mapped[str] = mapped_column(String(64))
    engine_name: Mapped[str] = mapped_column(String(64), primary_key=True, nullable=False)
    year: Mapped[int] = mapped_column(Integer)
    
class Engines(Base):
    __tablename__ = "engines"
    
    name: Mapped[str] = mapped_column(String(64), primary_key=True, nullable=False)
    horse_power: Mapped[int] = mapped_column(Integer)


def main():
    
    load_dotenv()
    
    connection_string = URL.create(
        'postgresql',
        username=os.getenv('USERNAME'),
        password=os.getenv('PASSWORD'),
        host=os.getenv('HOST'),
        database=os.getenv('DB'),
        #connect_args={'sslmode':'require'}
        )
    
    engine = create_engine(connection_string)
    session = Session(engine)
    
    sql = (
        select(
            Cars.country,
            Cars.year,
            func.max(Engines.horse_power).label("max_horse_power"),
        )
        .join(Engines, Cars.engine_name == Engines.name)
        .where(Cars.country != 'USA')
        .group_by(Cars.country, Cars.year)
        .having(func.max(Engines.horse_power) > 200)
        .order_by(func.max(Engines.horse_power).label("max_horse_power").desc())
    )
    
    for i in session.execute(sql):
        print(i)


if __name__ == '__main__':
    main()

#('Germany', 2019, 612)
#('UK', 2019, 612)
#('Germany', 2021, 510)
#('Germany', 2023, 469)

```
Ở ví dụ trên, thay vì viết tên các table, database trong câu query thì tạo các class tương ứng với các table này (class *Cars*, *Engines*)

# SQL query builder
Ngoài cách viết query thuần và sử dụng thư viện hỗ trợ ORM, còn có thể dùng thư viện hỗ trợ build câu query kết hợp (sử dụng builder pattern) với thư viện như pymysql để truy vấn kết quả
- Ưu điểm: Dễ sử dụng do chỉ sử dụng một thư viện khác để hỗ trợ build câu query, chỉ cần truyền các giá trị tương ứng với từng toán tử, tạo câu query hoàn chỉnh. Hỗ trợ viết câu truy vấn an toàn. 
- Nhược điểm: Mỗi thư viện hỗ trợ builder có thể chỉ phù hợp với một loại DB.

Thư viện hỗ trợ builder câu query có thể sử dụng như *pypika*.
```
from pypika import Table, Query

customers = Table('customers')
q = Query.from_(customers).select(customers.id, customers.fname, customers.lname, customers.phone)
```
