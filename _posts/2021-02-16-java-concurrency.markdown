---
title: "Java Concurrency"
layout: post
date: 2021-02-16 12:18
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- concurrency
- java
category: blog
author: letcode
description: Java concurrency 
---

## Summary

- Một vài cách để chạy concurrency trong java.

#### Outline
- [Bài toán](#bai-toan)
- [Solution](#solution)

---

## Bài toán
Một vài lời giải cho một bài toán trên letcode để thực hiện concurreny trong ngôn ngữ java

## Solution

### Volatile
Sử dụng từ khóa **volatile** đặt trước một biến trong java sẽ làm giảm rủi ro có thể xảy ra trong việc đồng bộ memory, bởi vì bất cứ sự kiện nào làm thay đổi giá trị của biến này, thì các sự kiện khác sẽ phải chờ. Cơ chế này giúp cho một thread đọc một biến volatile, giá trị nhận được sẽ luôn là giá trị mới nhất được cập nhật vào biến đó, tránh được hiệu ứng side-effect trong code.

Trong lập trình, một action atomic để chỉ một event ảnh hưởng đến tất cả event khác. Atomic action không thể dừng khi đang thực hiện, các action khác phải đợi cho đến khi atomic action hoàn thành hoặc sẽ không có gì được trả về nếu có lỗi.

- Biến atomic có thể được sử dụng để đọc ghi với hầu hết các kiểu dữ liệu nguyên thủy (trừ long và double).

- Một biến được gắn từ khóa volatile ở trước sẽ thực thi đọc ghi theo cơ chế atomic (bao gồm cả kiểu long và double).
```java
class Foo {
    private volatile int flag;

    public Foo() {
        flag = 1;
    }

    public void first(Runnable printFirst) throws InterruptedException {
        for (;;) {
            if (flag == 1) {
                printFirst.run();
                flag = 2;
                break;
            }
        }
    }

    public void second(Runnable printSecond) throws InterruptedException {
        for (;;) {
            if (flag == 2) {
                printSecond.run();
                flag = 3;
                break;
            }
        }
    }

    public void third(Runnable printThird) throws InterruptedException {
        for (;;) {
            if (flag == 3) {
                printThird.run();
                flag = 1;
                break;
            }
        }
    }

}
```

### Semaphore
Semaphore là cơ chế sử dụng để quản lí một tập các khóa để quản lí các event. Khi một thread thực hiện event và nắm giữa một khóa, nó sẽ **acquire()** cho đến khi event hoàn thành, chuyển sang trạng thái **release()**, lock sẽ được chuyển sang cho một thread khác để tiếp tục thực hiện. Khi số khóa được dùng hết, các thread sẽ phải chờ đến khi một thread đang chạy hoàn thành event.

Semaphore thường được sử dụng để giới hạn số thread có thể truy cập đồng thời đến tài nguyên.

```java
class Foo {
    Semaphore semaphore1 = new Semaphore(0);
    Semaphore semaphore2 = new Semaphore(0);

    public Foo() {

    }

    public void first(Runnable printFirst) throws InterruptedException {
        printFirst.run();
        semaphore1.release();
    }

    public void second(Runnable printSecond) throws InterruptedException {
        semaphore1.acquire();
        printSecond.run();
        semaphore2.release();
    }

    public void third(Runnable printThird) throws InterruptedException {
        semaphore2.acquire();
        printThird.run();
    }

}
```

### Synchronized
Java cung cấp 2 cơ chế đồng bộ cơ bản bao gồm synchronized methods và synchronized statements.

**synchronized statements** yêu cầu khai báo cụ thể đối tượng sử dụng bằng từ khóa **synchronized**. Cơ chế này thường sử dụng cho đồng bộ...

```java
class Foo {
    private String mutex = new String("");
    private int counter = 1;

    public Foo() {

    }

    public void first(Runnable printFirst) throws InterruptedException {
        boolean loop = true;
        while (loop) {
            syncchronized(mutex) {
                if (counter == 1) {
                    printFirst.run();
                    ++counter;
                    loop = false;
                }
            }
        }
    }

    public void second(Runnable printSecond) throws InterruptedException {
        boolean loop = true;
        while (loop) {
            synchronized(mutex) {
                if (counter == 2) {
                    printSecond.run();
                    ++counter;
                    loop = false;
                }
            }
        }
    }

    public void third(Runnable printThird) throws InterruptedException {
        boolean loop = false;
        while (loop) {
            synchronized(mutex) {
                if (counter == 3) {
                    printThird.run();
                    ++counter;
                    loop = false;
                }
            }
        }
    }

}
```

### CountDownLatch
**CountDownLatch** là một cơ chế đồng bộ cho phép một hay nhiều threads chờ cho đến khi một tập các event đang được một thread khác thực hiện hoàn thành. **CountDownLatch** khởi tạo một bộ đếm ngược. Phương thức **await** sẽ block khi bộ đếm hiện tại về đến 0.

```java
class Foo {
    private final CountDownLatch L1 = new CountDownLatch(1);
    private final CountDownLatch L2 = new CountDownLatch(1);

    public Foo() {

    }

    public void first(Runnable printFirst) throws InterruptedException {
        printFirst.run();
        L1.countDown();
    }

    public void second(Runnable printSecond) throws InterruptedException {
        L1.await();
        printSecond.run();
        L2.countDown();
    }

    public void third(Runnable printThird) throws InterruptedException {
        L2.await();
        printThird.run();
    }

}
```

### Phaser
Một cơ chế đồng bộ tái sử dụng tương tự CountDownLatch nhưng linh hoạt hơn với nhiều mục đích.
Reference [Design Pattern Guru](https://refactoring.guru/).
