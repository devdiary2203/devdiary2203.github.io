---
title: "The clean architecture"
layout: post
date: 2021-09-02 03:50
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
category: blog
author: summary
description: 
---

![Example](/assets/img/CleanArchitecture.jpeg)

Có nhiều kiến trúc code khác nhau, tuy nhiên, chúng đều hướng tới cùng mục đích là tách project thành các tầng. Mỗi tầng giải quyết các yêu cầu khác nhau, phụ thuộc vào business của bài toán.

# Một số yêu cầu kiến trúc của hệ thống:

- Không bị ràng buộc vào một thư viện, framework
- Có thể kiểm tra được các thành phần UI, database, web server,...
- Không phụ thuộc vào UI: yêu cầu thay đổi UI bằng giao diện UI, không kéo theo việc thay đổi hệ thống bên dưới
- Không phụ thuộc vào database: chuyển đổi linh hoạt giữa các database khác nhau. Business không bị ràng buộc bởi database
- Không phục thuộc vào các tác nhân bên ngoài

# Nguyên tắc phụ thuộc
Hình ở đầu bài mô tả các khu vực khác nhau của một phần mềm. Càng tiến ra ngoài vòng tròn, level phần mềm càng cao. 2 vòng tròn bên ngoài là cơ chế, 2 vòng trong là policies của phần mềm.

Kiến trúc trong hình hoạt động theo nguyên tắc phụ thuộc - Dependency rule. Mã nguồn chỉ thực thi hướng vào trong. Không có đoạn code nào ở vòng tròn bên trong cần biết các thông tin ở vòng tròn bên ngoài. Đặc biệt, khi khai báo tên các thực thể: class, biến... ở vòng tròn bên ngoài thì không được đề cập đến code của vòng trong. 

Định dạng dữ liệu ở vòng ngoài thì không được sử dụng ở bên trong. Khi định dạng được khởi tạo ở vòng ngoài sử dụng ở các entity ở bên trong có thể gây bị ảnh hưởng khi bị thay đổi.

# Entities
Các entity mô tả business của project. Một entity có thể là một object với các method bên trong hoặc một tập các cấu trúc dữ liệu. Entity biểu diễn các quy tắc chung, cở bản nhất của một project, không bị thay đổi khi thay đổi các thành phần code trong ứng dụng.

# Use cases
Vòng tròn này chứa các use cases của hệ thống, điều phối i/o của dữ liệu từ các entity và hoạt động theo business cử project. Các use case thường có gắng không ảnh hưởng đến các đối tượng khác như database, UI.
Use case bị ảnh hướng theo application của business.

# Interface adapters
Layer này sẽ chịu trách nhiệm chuyển đổi và truy xuất dữ liệu đến database, web, các cấu trúc dữ liệu bên trong: use cases, entities. Kiến trúc MVC sẽ nằm toàn bộ ở layer này. 

# Frameworks and Drivers
Layer này chứa các thành phần giao tiếp cụ thể như web, database, chúng phụ thuộc vào các framework, database được dựng sẵn để sử dụng.

Layer bên trong chứa các policies chung nhất cần nắm được để xây dựng ứng dụng. Các vòng tròn này áp dụng tuân thủ theo Dependency Rule. Ngoài ra, kiến trúc trong hình đầu bài có thể thay đổi tùy theo yêu cầu cụ thể.

# Crossing Boundaries
Theo dependency rule thì use case không thể gọi đến presenter. 
Ví dụ trong trường hợp đặc biệt, use case cần gọi đến presenter. Use case sẽ sử dụng một interface use case output để gọi đến presenter.
Cách giải quyết này áp dụng dependency inversion principle. Trong java, các interface và inheritance được sắp xếp phù hợp sao cho các đoạn code phụ thuộc chống lại luồng điều khiển

# Ví dụ cấu trúc project
Một project có thể chia thành các tầng khác nhau biểu diễn hướng phụ thuộc như domain, application, infrastructure, webUI.
## Domain
Domain là phần core của ứng dụng, không tham chiếu đến phần nào khác trong project. Bên trong nó chứa các entity và list todo cụ thể.
## Application
Layer này tham chiếu đến domain. Application chứa các định nghĩa liên quan đến query database, xử lý các hoạt động truy xuất đến domain và entity. 
## Infrastructure
Layer này chứa các interface và implementation truy xuất dữ liệu đến database. Layer application tham chiếu đến infrastructure thông qua các interface.
## WebUI
Layer này chưa controller chuyển handler từ http request đến application để xử lý và trả về kết quả. Controller được tách ra để tránh liên quan đến các layer khác.