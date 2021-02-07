---
title: "Maven - Something you need"
layout: post
date: 2021-01-16 13:41
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- java
category: blog
author: source jacob jenkov
description: Maven universal
---

# Maven - A Build Tool for Java
Maven là một công cụ hữu ích để xây dựng một project viết bằng java. Maven giúp tự động hóa các giai đoạn khi phát triển phần mềm như tạo source code, tạo document, biên dịch, đóng gói và cài đặt các gói phần mềm liên quan.
Ưu điểm của việc sử dụng công cụ build project như maven sẽ giúp cho quá trình phát triển tiết kiệm được thời gian đồng thời giảm sai sót so với việc tự xây dựng và kiểm thử từng bước.

# Ý tưởng cốt lõi của Maven
Maven tập trung xây dựng project dựa vào file pom.xml. File pom chứa thông tin các tài nguyên dùng trong project như source code, test code, các package phụ thuộc,v.v. File pom thường được đặt trong thư mục gôc của project. Sơ đồ dưới đây mô tả các bước để build một project bằng công cụ maven:
![Maven Overview](../assets/images/maven-overview.png)
- Đọc cấu hình trong file pom.xml
- Down load các dependencies về thư mục project ở local
- Build process: thực hiện build project bằng các lệnh được định nghĩa sẵn của maven
## Project Object Model Files (pom.xml)
Bước đầu tiên khi build project, maven sẽ đọc file pom để thực hiện các lệnh được định nghĩa trong file này.
Một file pom.xml định nghĩa các tài nguyên được sử dụng của project bao gồm thư mục source code, test code, các package phụ thuộc,v.v
File pom cũng mô tả lại các bước để build project (nếu các thư mục code được tổ chức theo một cấu trúc được định nghĩa thì maven có thể tự động build mà không cần định nghĩa các bước cụ thể)

pom.xml
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                      http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.dev.diary</groupId>
    <artifactId>java-web-crawler</artifactId>
    <version>1.0.0</version>
</project>
```
File pom.xml trên chứa một số thông tin cơ bản của project. 
- modelVersion: version đang sử dụng của file pom, sẽ tương thích với version của maven. Trong ví dụ trên, file pom đang viết ở version 4.0.0 sẽ tương thích với maven 2 hoặc 3
- groupId: thông tin định danh duy nhất cho công ty hay project của bạn, thường ứng với trên thư mục gốc của project, như trong ví dụ thì đường dẫn thư mục gốc sẽ là ./com/dev/diary
- artifactId: tên của project mà bạn tạo, như tên project trong ví dụ là Java web crawler thì artifactId tương ứng là java-web-crawler. artifactId cũng được sử dụng để đặt tên cho thư mục con chứa code bên trong thư mục được tạo bởi groupId. Ví dụ thư mục chứa code ứng với file pom trên sẽ là src/main/java/com/dev/diary/java-web-crawler/..
- versionId: là version của project, ví dụ khi project được update theo các version 1.0.0, 1.0.1,...

# Running Maven
Maven sẽ thực hiện các lệnh từ terminal hay còn gọi là các phase.  Một số lệnh cơ bản của maven thường được sử dụng như:
```commandline
mvn install
```
Lệnh này sẽ build project và tạo file JAR lưu trong thư mục project. "mvn install" cũng sẽ thực hiện tất cả các phase cần thiết để có thể chạy lệnh install và tạo ra file JAR để deploy service của bạn.
```commandline
mvn clean install
```
Nếu trước đây bạn đã từng build project và tạo xong một file JAR thì khi muốn install lại từ code, bạn có thể thêm tham số clean vào. Tham số này sẽ xóa các file được tạo ra từ lần build trước và instal lại.
```commandline
mvn dependency:copy-dependencies
```
Bạn cũng có thể thực hiện build từng phần nhỏ trong mỗi phase bằng cách thêm dấu ":" vào giữa phase và bước con trong phase đó (hay còn gọi là goal). Trong lệnh ví dụ ở trên, ta đã thực hiện copy các dependencies trong phase dependency

# Maven Directory Structure
Maven cũng cung cấp cho chúng ta một cấu trúc thư mục chuẩn của trong một project. Như đã nói ở trên, nếu bạn tạo một project theo đúng cấu trúc này (các IDLE java đã hỗ trợ chúng ta việc này) thì bạn không cần phần cấu hình cụ thể cấu trúc thư mục bên trong file pom nữa mà maven sẽ dựa theo cấu trúc chuẩn này để build project.

Một project sử dụng maven sẽ có cấu trúc chuẩn như sau:
```commandline
- src
  - main
    - java
    - resources
    - webapp
  - test
    - java
    - resources

- target
```
- src: thư mục gốc chưa source code, test code
  - main: thư mục chưa source code, tài nguyên liên quan đến service
    - java: thư mục chứa source code
    - resources: thư mục chứa tài nguyên cần cho project
    - webapp: thự mục khi bạn build một website bằng java
  - test: thư mục chứa code dùng để test
    - java:
    - resources:
- target: thư mục được tạo ra bởi maven chứa các file được tạo ra sau khi build project như file JAR. Tham số clean trong lệnh mvn install khi thêm vào sẽ xóa thư mục này nếu nó đã được tạo trước khi build project