---
title: "Enum in java"
layout: post
date: 2024-05-02 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
category: blog
author: devdiary
description: Nội dung như title
---

Enum là một kiểu dữ liệu đặc biệt trong java sử dụng để biểu diễn các hằng số cố định dùng trong chương trình. Một enum có thể chứa các field, method, constructor. Ví dụ của enum như các thứ trong tuần, năm trong tháng, hay các gói khuyến mãi, voucher.

## Khai báo enum
Ví dụ khai báo enum:
```java
public class EnumExample {
    enum WeekDay {
        MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;
    }
 
    public static void main(String[] args) {
        WeekDay d = WeekDay.MONDAY;
            System.out.println(d);
    }
}
```

hoặc khai báo bên ngoài một lớp:
```java
enum WeekDay {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;
}
 
public class EnumExample {
    public static void main(String[] args) {
        WeekDay d = WeekDay.MONDAY;
            System.out.println(d);
    }
}
```

## Để duyệt các phần từ trong một enum, ta sử dụng method values():
```java
        for (WeekDay d : WeekDay.values()) {
            System.out.println(d);
        }
```

## Khởi tạo giá trị cho mỗi hằng số
Enum có các đặc điểm:
- Constructor mặc định là private
- Các phần tử của enum là static final
- Có thể tạo giá trị cụ thể gán cho các hằng số

```java
public class EnumExample3 {
    enum WeekDay {
        // Khởi tạo các phần tử từ construnctor
        // Các phần tử này luôn là static final
        MONDAY(2), TUESDAY(3), WEDNESDAY(4), THURSDAY(5), FRIDAY(7), SATURDAY(7), SUNDAY(8);
 
        private int value;
 
        // constructor này chỉ dùng trong nội bộ Enum
        // Modifier của constructor enum luôn là private
        // Nếu không khai báo private, java cũng sẽ hiểu là private.
        WeekDay(int value) {
            this.value = value;
        }
 
        // Có thể viết một static method lấy ra WeekDay theo value.
        public static WeekDay getWeekDayByValue(int value) {
            for (WeekDay d : WeekDay.values()) {
                if (d.value == value) {
                    return d;
                }
            }
            return null;
        }
    }
 
    public static void main(String[] args) {
        for (WeekDay d : WeekDay.values()) {
            System.out.println(d + " = " + d.value);
        }
 
        // Sử dụng static method của enum
        WeekDay d = WeekDay.getWeekDayByValue(3);
        System.out.println("value 3 is " + d);
    }
}
```

## So sánh phần tử trong java enum
Enum hỗ trợ so sánh cả theo toán tử == hoặc equals()
```java

public class EnumExample {
    enum WeekDay {
        MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;
    }
 
    public static void main(String[] args) {
        WeekDay today = WeekDay.SUNDAY;
 
        // use equal() method
        if (today.equals(WeekDay.SUNDAY)) {
            System.out.println("Today is holiday");
        } else {
            System.out.println("Today is working day");
        }
 
        // use == operator
        if (today == WeekDay.SUNDAY) {
            System.out.println("Today is holiday");
        } else {
            System.out.println("Today is working day");
        }
    }
}
```

## Sử dụng enum kết hợp switch
```java
public class EnumExample {
    enum WeekDay {
        MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;
    }
 
    public static void main(String[] args) {
        WeekDay today = WeekDay.SUNDAY;
 
        switch (today) {
        case MONDAY:
            System.out.println("Today is working day");
            break;
        case SATURDAY:
        case SUNDAY:
            System.out.println("Today is holiday");
            break;
        default:
            System.out.println(today);
        }
    }
}
```

Ngoài ra, trong python, enum có thể được xây dựng qua thư viện enum như sau:
https://realpython.com/python-enum/
