---
title: "Java annotation"
layout: post
date: 2024-05-08 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
- java
category: blog
author: devdiary
description: Nội dung như title
---

Annotation là một dạng metadata trong java để chú thích các class, method, package, biến. Annotation được biên dịch thành bytecode và đọc thông tin bằng cách sử dụng reflection để truy vấn thông tin metadata tương ứng. Trong java, có nhiều loại annotation với các mục đích khác nhau như:
- Java core: @Deprecated, @Override, @SuppressWarnings
- Spring framework: @Service, @Repository, @Configuration
- Hibernate: @Table, @Id, @Column, @OneToMany

Kí hiệu của annotation bắt đầu bằng `@` để đánh dấu cho compiler biết đây là một annotation đang được sử dụng. Đi sau là tên của annotation như Override, Controller,...

# Tự tạo một annotation trong java
Để tự khởi tạo một annotation trong java, sử dụng từ khóa *@interface* để khai báo. Java cung cấp một số annotation có sẵn như @Retention, @Target, @Documented, @Inherited để phục vụ cho việc tự khởi tạo một annotation.

## @Retention
Annotation này dùng để chú thích mức độ tồn tại của annotation được tạo. Có 3 mức đánh dấu phạm vi của annotation:
- Source: Tồn tại ở mức source code, không được compiler nhận ra
- Class: Được compiler ghi nhận nhưng không ghi nhận được khi runtime
- Runtime: JVM nhận biết được ở runtime, đây là mức độ cao nhất

## @Target
Annotation này dùng để chú thích cho một annotation khác, để đánh dấu được sử dụng trong phạm vi nào:
- Type: Gắn vào khi khai báo class, interface, enum, annotation
- Field: Gắn khi khai báo một field, enum
- Method: Gắn vào khi khai báo method
- Parameter: Gắn vào parameter
- Constructor: Gắn vào constructor
- Local variable: Gắn vào biến local
- Annotation type: Gắn vào trên một annotation
- Package: Gắn vào khi khai báo package

## @Documented
Đánh dẫu annotation mới này nên được thêm vào tài liệu của java

## @Inherited
Annotation có thể được kế thừa từ super class. Khi truy vấn annotation vào lớp con, lớp con không có annotation thì lớp cha của lớp này sẽ được gọi khi sử dụng annotation.

Ví dụ khởi tạo một annotation tên là *ExcelColumn* để đánh dấu field nào cần xử lý ra một cột khi xuất ra file excel trong một class

Khởi tạo excel column class:
```java
@Documented
@Target(ElementType.FIELD)
@Inherited
@Retention(RetentionPolicy.RUNTIME)
public @interface ExcelColumn {
 
    int index();
 
    String name();
 
    String address() default "Default value";

    String phone();
 
}
```

Cột address được gán giá trị default nên có thể được bỏ qua khi sử dụng annotation

```java
public class Customer {
 
    @ExcelColumn(index = 1, title = "#", description = "Customer id")
    private long id;
 
    @ExcelColumn(index = 2, title = "Tên")
    private String name;
 
    @ExcelColumn(index = 3, title = "Địa chỉ")
    private int address;
 
    @ExcelColumn(index = 4, title = "Sdt")
    private Date phone;
 
}
```

