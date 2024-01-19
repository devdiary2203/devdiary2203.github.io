---
title: "Proxy pattern"
layout: post
date: 2024-01-19 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- spring
- transactions
category: blog
author: devdiary
description: Nội dung như title
---

# Định nghĩa

Proxy pattern là một design pattern thuộc họ structural, mục đích của pattern này là tạo một lớp trung gian giữa object truy cập đến và phần logic chính xử lý object này để thực hiện một vài yêu cầu khác.

# Solution
Ban đầu object gửi từ client trỏ thẳng đến service chịu trách nhiệm xử lý yêu cầu này. Proxy pattern tạo một class có cùng interface với service gốc. Object được gửi từ client sẽ trỏ đến proxy này để xử lý trước khi chuyển hướng đến service chính chịu trách nhiệm. Concept này tương tự như data transfer object (DTO không phải proxy pattern)

```python
package io.diary.designpatternsdemo.ProxyPattern;

public interface IRecommendService {
    
    void addInfo();
    
    void recommend();
    
}
```

Ví dụ ta có một interface `IRecommendService` khai báo 2 phương thức là addInfo và recommend. Các thuật toán gợi ý khác nhau sẽ implement lại phương thức này để xử lý theo mục đích của từng thuật toán như hot item, related item, sale item,v.v. Trước khi request gửi đến từ client được xử lý bởi một trong các service thuật toán đã implement lại interface trên, giả sử ta cần phải bổ sung thêm một số thông tin liên quan đến user như ngày sinh, giới tính, địa điểm. Nếu với mỗi class thuật toán ta lại thêm một đoạn code bổ sung các thông tin trên trong method recommend sẽ gây ra trùng lặp code. Để xử lý ta có thể tạo thêm một class khác ProxyRecommendManager để nhận request từ client, bổ sung các thông tin trong method addInfo trước khi gọi đến phương thức recommend của thuật toán phù hợp.

```python
package io.diary.designpatternsdemo.ProxyPattern;

public class ProxyRecommendManager implements IRecommendService {

    @Override
    public void addInfo() {
        
    }

    @Override
    public void recommend() {

    }
}
```

# Use case proxy
- Virtual proxy: Một object ít khi được sử dụng trong chương trình có thể sử dụng pattern này để chỉ khởi tạo khi được dùng đến thay vì luôn được khởi tạo khi thực thi một tính năng nào đó
- Protection proxy: Sử dụng khi muốn tạo một class trung gian hạn chế truy cập đến tài nguyên với mỗi client
- Remote proxy: Trường hợp service chạy ở remote server, client request qua network cần được phân quyền, quản lí tài nguyên khi sử dụng network
- Logging proxy: Log mỗi request đến service
- Caching proxy: Cache kết quả, quản lí cache cho service đặc biệt khi xử lý các tác vụ nặng, xử lý lâu có thể dùng pattern này để tối ưu hệ thống
- Smart reference proxy: Quản lý chung các logic bổ sung dữ liệu của object khi được refer đến

# Ưu/nhược điểm
- Ưu điểm: 
+ Điều hướng service mà client không biết 
+ Dễ quản lí các service được trỏ đến
+ Tuân thủ nguyên tắc open/closed của solid: Có khả năng mở rộng dễ dàng, không cần phải sửa đổi các method

- Nhược điểm:
+ Càng nhiều logic thì code có thể phức tạp hơn
+ Cần xử lý khi reponse từ các service chính có lỗi hay delay

# Ví dụ

`RecommendApplication.java`
```
public class RecommendApplication {

    public static void main(String[] args) {
        ProxyRecommendManager proxyRecommendManager = new ProxyRecommendManager();
        proxyRecommendManager.recommend();
    }

}
```

`ProxyRecommendManager.java`
```
public class ProxyRecommendManager implements IRecommendService {

    private HotItemRecommendService hotItemRecommendService;

    public ProxyRecommendManager() {
        this.hotItemRecommendService = new HotItemRecommendService();
    }

    @Override
    public void addInfo() {
        System.out.println("Add review item");
    }

    @Override
    public void recommend() {
        addInfo();
        if (isHotItemRequest()) {
            hotItemRecommendService.recommend();
        }
    }

    private boolean isHotItemRequest() {
        return true;
    }

}
```

`HotItemRecommendService.java`
```
public class HotItemRecommendService implements IRecommendService {

    @Override
    public void addInfo() {
    }

    @Override
    public void recommend() {
        System.out.println("Recommend hot item");
    }
}
```

`IRecommendService.java`
```
public interface IRecommendService {

    void addInfo();

    void recommend();

}
```


Khi chạy chương trình sẽ thu được kết quả:
```
Add review item
Recommend hot item

Process finished with exit code 0
```

Việc addInfo các thông tin review của item được thực hiện ở proxy cho bất kì service thuật toán nào thay vì phải cài đặt ở bên trong từng thuật toán để tránh trùng lặp code và dễ quản lí việc thêm thông tin mới sau này.
