---
title: "Shel scripting basics"
layout: post
date: 2024-01-21 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- shell
category: blog
author: devdiary
description: Nội dung như title
---


# Scheduling cron jobs using crontab
## Phân biệt cron, crond và crontab
- Cron là một service dùng để chạy các job
- Crond là các crontab file
- Crontab chứa các job và dữ liệu lập lịch
- Crontab cho phép sửa các crontab file

# Describe the cron syntax
Để mở editor crontab, dùng lệnh sau:
```
crontab -e
```

Cú pháp để chạy một job cron như sau:
```
m h dom mon dow command
```
Trong đó:
- m: minute
- h: hour
- dom: day of month
- mon: month 
- dow: day of week
- command: lệnh cần chạy

Các giá trị m, h, dom, mon, dow được thay bằng số hoặc dấu * có nghĩa là bất kì thời gian nào.

Ví dụ: 30 15 * * 0 date >> sundays.txt

Script trên có nghĩa là thêm ngày hiện tại vào file sundays.txt tại thời điểm 15:30 mỗi chủ nhật.

# Apply and remove cron jobs
## Thêm một job chạy crontab
Mở crontab editor:
```
crontab -e
```

Ví dụ thêm cronjob ở ví dụ trên vào crontab file như sau:
```
#   m   h   dom     mon dow command
    30  15  *       *   0   date >> path/sundays.txt
```

Sau khi thêm chỉ cần lưu và thoát khỏi cron editor. Để liệt kê danh sách các cronjob:
```
crontab -l
```