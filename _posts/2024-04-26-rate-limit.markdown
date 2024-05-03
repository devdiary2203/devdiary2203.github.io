---
title: "Rate limit"
layout: post
date: 2024-04-26 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
category: blog
author: devdiary
description: Nội dung như title
---

Trong usecase này, rate limit được áp dụng để hạn chế truy cập đến api gateway theo từng user. Lí do áp dụng rate limit từ một vấn đề phát sinh trong quá trình triển khai có nhiều request không lấy được user id hoặc spam vào api không cần thiết như call quá nhiều lần, mặc dù data không được update liên tục (trường hợp của api lấy số liệu từ model).

Để triển khai rate limit trong java, ta sử dụng thư viện bucket4j:
```xml
<dependency>
    <groupId>com.bucket4j</groupId>
    <artifactId>bucket4j-core</artifactId>
    <version>8.1.0</version>
</dependency>
```

Đầu tiên ta sẽ tạo một enum khởi tạo cấu hình rate limit dùng trong api:
```java
public enum RateLimitingPlan {

    BY_ID_PACKAGE(10);                      // Package for device id corresponding to each customer

    private int bucketCapacity;

    RateLimitingPlan(int bucketCapacity) {
        this.bucketCapacity = bucketCapacity;
    }

    public Bandwidth getLimit() {
        return Bandwidth.classic(
                bucketCapacity, Refill.intervally(bucketCapacity, Duration.ofSeconds(5))
        );
    }

    public int getBucketCapacity() {
        return bucketCapacity;
    }

    public static RateLimitingPlan resolvePlanFromKey(String key) {
        return BY_ID_PACKAGE;
    }
}
```

Ở đây, mục đích sử dụng enum với BY_ID_PACKAGE(10) để giới hạn số lần truy cập tối đa 10 request trong vòng 5s đối với mỗi user. Giả sử, khi api phát triển thêm yêu cầu cung cấp các gói mở rộng với số lượng request tương ứng với từng mức giá tiền, ta có thể bổ sung thêm enum các package tương ứng. Method Refill.intervally sử dụng để cấu hình khi user truy cập API quá 10 request / 5s sẽ cần đợi hết 5s mới có thể reset lại limit. 

Tiếp theo ta khởi tạo service xử lý limit theo từng user id:
```java
public class RateLimitingPlanService implements IRateLimitingPlanService {

    private Cache<String, Bucket> cache;

    public RateLimitingPlanService() {
        Cache<String, Bucket> cache = Caffeine.newBuilder()
                .expireAfterWrite(1, TimeUnit.HOURS)
                .expireAfterAccess(1, TimeUnit.HOURS)
                .maximumSize(1000000)
                .softValues()
                .build();
        this.cache = cache;
    }

    @Override
    public Bucket resolveBucket(String deviceId) {
        String key = deviceId;
        return cache.get(key, this::newBucket);
    }

    private Bucket newBucket(String key) {
        RateLimitingPlan rateLimitingPlan = RateLimitingPlan.resolvePlanFromKey(key);
        return bucket(rateLimitingPlan.getLimit());
    }

    private Bucket bucket(Bandwidth limit) {
        return Bucket.builder().addLimit(limit).build();
    }

}
```

Ý tưởng ở đây là sử dụng cache để lưu trữ trạng thái của từng user. Tùy thuộc vào số lượng user, tốc độ xử lý của api có thể chọn database khác để xử lý đáp ứng yêu cầu lưu trữ.

```java
Bucket limiter = rateLimitingPlanService.resolveBucket(deviceId);
ConsumptionProbe probe = limiter.tryConsumeAndReturnRemaining(1);
if (probe.isConsumed()) {
    // do something               
} else {
    return ResponseEntity.status(HttpStatus.TOO_MANY_REQUESTS).body("Too many requests");
}
```

Apply rate limit vào api, ta sử dụng method *tryConsumeAndReturnRemaining*. Method này sẽ kiểm tra bucket với user này còn quota không và trả về trạng thái có cho phép truy cập vào api không. Nếu không sẽ trả về mã lỗi 429 (too many requests)
