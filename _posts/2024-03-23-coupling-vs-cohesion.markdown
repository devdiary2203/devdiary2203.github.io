---
title: "Coupling vs cohesion"
layout: post
date: 2024-03-23 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- oop
category: blog
author: devdiary
description: Nội dung như title
---

# Cohesion
Cohesion là khái niệm dùng để thể hiện sự liên kết các phần tử bên trong một module, mô tả mối quan hệ giữa các phương thức trong class. Cohesion tập trung vào việc hiểu một module/class được thiết kế như thế nào. Khả năng kết hợp của class càng cao thì thiết kế OOP càng tốt.

Nếu một class bên trong một module chỉ thực hiện một nhiệm vụ có mục đích rõ ràng và không có nhiệm vụ nào khác đi kèm thì class có tính gắn kết cao. Ngược lại, class nếu đang cố gắng đóng gói nhiều hơn một mục đích hoặc có mục đích không rõ ràng bên trong thì module có độ liên kết thấp. Ưu điểm của việc một module hay class có độ liên kết cao:
- Giảm sự phức tạp module, dễ maintain, tái sử dụng code, dễ đọc hiểu
- Hoạt động ổn định

Một đặc điểm của sự liên kết cao (high cohesion) đó là các thành phần sẽ có sự tương quan thấp (loose coupling) và ngược lại.

Trong các nguyên tắc solid, có một nguyên tắc đó là *Single responsibility principle*, nguyên tắc này hướng đến sự liên kết cao giữa các thành phần, mỗi class chỉ thực hiện một nhiệm vụ duy nhất. Để tăng tính cohesion bên trong một module:
- Các chức năng được nhúng bên trong class, truy cập thông qua method
- Các method chỉ xử lý một số nhỏ activity liên quan, tránh các thao tác dư thừa không liên quan

Ví dụ cohesion:
Để thực hiện các tính năng liên quan đến email như kiểm tra email hợp lệ, gửi email, hiển thị email, địa chỉ email, nếu ta gộp tất cả tính năng này lại bên trong một class như sau thì có thể xem là low cohesion:
```java
public class A() {
    checkEmail();

    validateEmail();

    sendEmail();

    printLetter();

    printAddress();
}
```

Ta có thể chia mỗi method con bên tron class A thành một class riêng như sau:
```java
public class A() {
    checkEmail();
}
```

```java
public class B() {
    validateEmail();
}
```

```java
public class C() {
    sendEmail();
}
```

```java
public class D() {
    printLetter();
}
```


```java
public class E() {
    printAddress();
}
```

## Cohesion type
### Coincidental cohension
Các thành phần trong module được gom lại với nhau tùy ý, ví dụ điển hình là module utility trong code khi các class thực hiện một số chức năng phổ biến trong project được nhóm lại cùng nhau.

### Logical cohesion
Các thành phần trong module được gom lại với nhau dựa vào logic cùng thực hiện một mục đích chung. Ví dụ các thao tác liên quan đến các thao tác input từ mouse, keyboard của user vào một module.

### Temporal cohesion
Các thành phần trong module được gom lại với nhau vì thời điểm các thành phần này được gọi đến trong project như gửi noti cảnh báo lỗi, xử lý exception.

### Procedural cohesion
Các thành phần này được gắn kết vì luôn thực hiện theo một trình tự nhất định như khi gửi request đến cần đọc api key, check authentication.

### Communication cohesion
Sự gắn kết thể hiện khi các thành phần cùng đọc đến một loại dữ liệu ví dụ các thao tác cùng đợc vào thông tin cá nhân của một user

### Sequential cohesion
Các thành phần cùng nhận input từ một module khác và xử lý data. Ví dụ đọc data từ kafka sau đó xử lý tiếp theo yêu cầu thì trong module này có thể thành các class như *consumerA.java*, *consumerB.java* mỗi class chịu trách nhiệm đọc từ một topic và xử lý message.

### Functional cohesion
Các thành phần có sự gắn kết về mục tiêu cuối cùng của task cần đạt được 

### Perfect cohesion
Thành phần không thể tối ưu thêm về mặt chức năng bên trong nữa.

# Coupling
Nếu cohesion thể hiện mức độ liên quan đến nhau giữa các thành phần trong một module, thì coupling thể hiện sự phụ thuộc lẫn nhau giữa các module. Sự phụ thuộc giữa 2 class được thể hiện khi:
- Một thuộc tính trong class A được tham chiếu từ class B
- A gọi đến các method từ một object tạo bởi class B
- A có một method tham chiếu đến B thông qua tham số truyền vào
- A được extend hoặc implement từ B

Low coupling thể hiện một quan hệ trong đó một module tương tác với một module khác thông qua interface, không cần quan tâm đến việc khai một method bên trong module trong các module khác như thế nào. 

## Các thuộc tính của coupling
- Degree: Số lượng connection giữa các module với nhau. Để tối ưu, giữa các module nên có ít connection. Nếu module cần connect đến các module khác thì có thông qua một vài param hay interface để hạn chế số lượng connection.
- Ease: Connect giữa các module cần thể hiện rõ ràng, khi thực hiện kết nối không cần phải tìm hiểu kĩ bên trong module đó hoạt động như thế nào.
- Flexibility: Các module có thể connect với nhau linh hoạt, có thể maintain thay thế một vài yêu cầu kết nối khác sau này.

## Tightly coupling
Tightly couling thể hiện sự phụ thuộc chặt chẽ (nhiều connection) giữa các module với nhau.
- Sự thay đổi một module sẽ dẫn đến sự thay đổi các module khác
- Việc update, maintain sẽ tốn nhiều thời gian hơn
- Giảm khả năng tái sử dụng hoặc test 
=> Để khắc phục các vấn đề này cần giảm thiểu sự phụ thuộc giữa các module, mỗi module được giới hạn rõ ràng các chức năng cần thực hiện.

## Coupling in OOP
### Subclass coupling
Mô tả mối quan hệ giữa lớp con và lớp cha. Lớp con có connection đến lớp cha nhưng lớp cha không connect đến lớp con
### Temporal coupling

### Dynamic coupling

### Semantic coupling

### Logical coupling

