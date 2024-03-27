---
title: "Airflow tutorial"
layout: post
date: 2024-03-26 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- data engineer
- data science
category: blog
author: devdiary
description: Nội dung như title
---

Airflow là công cụ mã nguồn mở sử dụng để lập lịch, quản lí và giám sát quy trình xử lý dữ liệu. 

# Airflow glossary

## DAG
Direct acyclic graph (DAG) là một đồ thị có hướng, không chu trình, biểu diễn tất cả các bước xử lý dữ liệu. Mỗi luồng xử lý được khởi tạo bên trong một file DAG, bên trong file này, ta định nghĩa một quy trình. Quy trình này được hiểu là một đồ thị với các node (đỉnh) tương ứng một task chịu trách nhiệm thực hiện một nhiệm vụ riêng. Các task có chạy tuần tự hoặc song song theo một thứ tự được cấu hình sẵn.

### Task
*Task* là đơn vị cơ bản trong airflow dùng để định nghĩa một công việc cần thực hiện bên trong DAG. Các task được sắp xếp theo thứ tự thực hiện bên trong DAG, upstream/downstream dùng để chỉ các task trước và sau của một task. Có 3 loại task cơ bản bên trong airflow:
- *Operator*: định nghĩa sẵn các task theo một template, kết nối các task lại với nhau tạo thành một DAG
- *Sensor*: lớp con của operator có nhiệm vụ chờ một sự kiện bên ngoài xảy ra để thực hiện nhiệm vụ nào đó
- *Taskflow* (triển khai dạng decorator *@task*): Loại task này sử dụng với mục đích custom một hàm python thực hiện dưới dạng một task

*Task*, *Operator*, *Sensor* là các lớp con của một lớp gọi là *BaseOperator*, các khái niệm task và operator có thể dùng để thay thế cho nhau.

Để khai báo mối quan hệ giữa các task bên trong một file DAG, có 2 cách để cấu hình:
- Sử dụng dấu *bitshift* (*>>* hoặc *<<*):
```python
task_1 >> task_2 >> [task_3, task_4] 
```

- Sử dụng method set_upstream và set_downstream:
```python
task_1.set_downstream(task_2)
task_2.set_upstream(task_3)
```

### Control flow
Mặc định, các task sẽ được chạy khi các task phụ thuộc nó chạy thành công. Tuy nhiên, airflow cung cấp thêm các method để điều khiển quá trình task đang chạy:

#### Branching
Branching là tính năng phân nhánh các task, quy trình không phải đi theo một luồng cố định mà tùy điều kiện sẽ thực hiện các nhánh task khác nhau bên trong một DAG. Branching sử dụng decorator *@task.branch* đánh dấu trong file DAG để thông báo cho airflow.

```python
@task.branch(task_id="branch_task")
def branch_func(ti=None):
    xcom_value = int(ti.xcom_pull(task_ids="start_task"))
    if xcom_value >= 5:
        return "continue_task"
    elif xcom_value >= 3:
        return "stop_task"
    else:
        return None


start_op = BashOperator(
    task_id="start_task",
    bash_command="echo 5",
    do_xcom_push=True,
    dag=dag,
)

branch_op = branch_func()

continue_op = EmptyOperator(task_id="continue_task", dag=dag)
stop_op = EmptyOperator(task_id="stop_task", dag=dag)

start_op >> branch_op >> [continue_op, stop_op]
```

#### Trigger rules
Trigger rule cài đặt trước các điều kiện để một task trong DAG được thực hiện. 

#### Setup and Teardown
Setup and teardown là tính năng cài đặt và loại bỏ quan hệ giữa các task

#### Latest only
Latest only là một trường hợp đặc biệt của branching. Giả sử trong một DAG có 3 task được cấu hình như sau:
```python
task_1 >> task_2 >> task_3
```
Trường hợp khi chạy lại DAG của những ngày trước, không phải ngày hiện tại, bạn muốn chỉ thực hiện *task_1* mà không cần chạy *task_2*, *task_3* thì có thể sử dụng tính năng này.

#### Depends On Past
Depends on past là điều kiện thực hiện một task dựa vào điều kiện của task này ở lần chạy trước.


### Vòng đời task
Một task trong DAG có thể trải qua các trạng thái sau:

![Airflow task states](/assets/images/airflow-task-state.png)

- *none*: Task chưa được đưa vào queue để chờ chạy
- *scheduled*: Scheduler xác định xong các phụ thuộc của task yêu cầu, đủ điều kiện đưa task vào queue chạy
- *queued*: Task đã được executor đưa vào queue, chờ node worker sẵn sàng để bắt đầu chạy (ví dụ worker đang thực hiện task khác, chờ task này chạy xong sẽ đến task đang đợi trong queue)
- *running*: Task đang chạy trong một node worker hoặc trên executor
- *success*: Task chạy thành công, không phát sinh lỗi gì
- *restarting*: Task được chạy lại khi có yêu cầu từ bên ngoài
- *failed*: Task không thành công, phát sinh lỗi khi chạy
- *skipped*: Task dừng chạy do phân nhánh, conflict only lastest
- *upstream_failed*: Task phía trước của task (task upstream) chạy không thành công. Tùy từng phiên bản các thuật ngữ previous/upstream, next/downstream dùng thay thế cho nhau
- *up_for_retry*: Task failed, được cấu hình để chạy thử lại, đang chờ được scheduler kiểm tra đưa vào queue
- *up_for_rescheduled*: Task là một sensor, đang ở chế độ reschedule
- *deferred*: Task bị hoãn bởi một điều kiện (trigger)
- *removed*: Task đã bị xóa khỏi DAG trước khi chạy

Luồng cơ bản của một task sẽ đi từ các trạng thái: none -> scheduled -> queued -> running -> success

## Operator
Operator là một template khai báo sẵn cấu hình của một task, operator được khai báo bên trong file DAG. Airflow cung cấp nhiều loại operator tích hợp sẵn để thực hiện các task với yêu cầu khác nhau và giao tiếp với một số hệ thống khác như HDFS, MySQL, AWS. Một số loại operator thông dụng:

### BashOperator


### PythonOperator


### EmailOperator


### Decorator @task

## Sensor
Sensor là một loại operator đặc biệt, thực hiện một task khi có một sự kiện nào đó xảy ra. Sự kiện này dựa vào một thời gian cụ thể, một file được tạo hoặc một event từ bên ngoài. Có 2 trạng thái của sensor:
- poke (mặc định): sensor được khởi tạo từ đầu và luôn ở trong worker khi dag đang chạy
- reshedule: mỗi lần gọi, sensor mới được khởi tạo trong work, khi không được gọi sensor sẽ ở trạng thái sleep
