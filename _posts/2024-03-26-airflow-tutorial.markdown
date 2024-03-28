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
Airflow sẽ chờ tất cả các task phía trước hoàn thành để tiếp tục chạy task hiện tại. Tuy nhiên, airflow cung cấp thêm trigger dùng để điều khiển luồng chạy của một task trong DAG thông qua tham số trigger_rule nhận một trong các giá trị:
- all_success: tất cả các task trước thành công mới thực hiện task hiện tại
- all_failed: tất cả các task trước fail hoặc task liền trước fail 
- all_done: tất cả các task trước done
- all_skipped: tất cả các task trước ở trạng thái skipped
- one_failed: ít nhất một task upstream fail (không cần đợi check tất cả các task trước task hiện tại, chỉ cần có 1 task fail)
- one_success: ít nhất một task upstream thành công (không cần đợi check tất cả các task)
- one_done: ít nhất một task fail hoặc success
- none_failed: tất cả các task upstream chạy thành công hoặc skipped (không ở trạng thái failed hoặc upstream_failed)
- none_failed_min_one_success: tất cả upstream task không fail hoặc upstream_failed và có ít nhất một task success
- none_skipped: không có upstream task nào ở trạng thái skipped, nghĩa là các task upstream có thể trạng thái failed, successed hoặc upstream_failed
- always: không có ràng buộc gì, task gắn trigger này luôn được chạy

#### Setup and Teardown
Setup and teardown là tính năng cung cấp tài nguyên để chạy một task, sau đó thu hồi khi task chạy xong. Một số đặc điểm của tính năng này:
- Nếu task bị xóa, thì config setup and teardown cũng bị xóa
- Teardown task sẽ không được hiển thị trên tool để bỏ qua việc theo dõi trạng thái của task này
- Teardown task sẽ chạy nếu setup task thành công, kể cả task đó fail
- Teardown task bị bỏ qua nếu các ràng buộc điều kiện của task này conflict với nhóm các task chung đang chạy

Để cấu hình task setups and teardown, ta sử dụng method *as_setup* và *as_teardown*. Giả sử ta có 3 task create_cluster, run_query, delete_cluster, chạy theo thứ tự tạo cluster cấp tài nguyên, chạy query lấy kết quả và xóa cluster vừa tạo khi chạy xong. DAG được cấu hình như sau:
```python
create_cluster.as_setup() >> run_query >> delete_cluster.as_teardown()
```
hoặc
```python
create_cluster >> run_query >> delete_cluster.as_teardown(setups=create_cluster)
```

- Nếu xóa *run_query* và chạy lại, *create_cluster* và *delete_cluster* sẽ bị xóa
- Nếu *run_query* fail, *delete_cluster* vẫn chạy
- DAG chạy thành công chỉ phụ thuộc vào task *run_query* có chạy thành công hay không

Trường hợp có nhiều task cần chạy trước khi teardown, dùng thêm context manager trong python như sau:
```python
with delete_cluster().as_teardown(setups=create_cluster()):
    [RunQueryOne(), RunQueryTwo()] >> DoSomeOtherStuff()
    WorkOne() >> [do_this_stuff(), do_other_stuff()]
```

Ngoài ra có thể chạy nhiều cặp ràng buộc setup and teardown trong DAG:
```python
with TaskGroup("setup") as tg_s:
    create_cluster = create_cluster()
    create_bucket = create_bucket()
run_query = run_query()
with TaskGroup("teardown") as tg_t:
    delete_cluster = delete_cluster().as_teardown(setups=create_cluster)
    delete_bucket = delete_bucket().as_teardown(setups=create_bucket)
tg_s >> run_query >> tg_t
```

#### Latest only
Latest only là một trường hợp đặc biệt của branching. Giả sử trong một DAG có 3 task được cấu hình như sau:
```python
task_1 >> task_2 >> task_3
```
Trường hợp khi chạy lại DAG của những ngày trước, không phải ngày hiện tại, bạn muốn chỉ thực hiện *task_1* mà không cần chạy *task_2*, *task_3* thì có thể sử dụng tính năng này.

```python
import datetime

import pendulum

from airflow.models.dag import DAG
from airflow.operators.empty import EmptyOperator
from airflow.operators.latest_only import LatestOnlyOperator
from airflow.utils.trigger_rule import TriggerRule

with DAG(
    dag_id="latest_only_with_trigger",
    schedule=datetime.timedelta(hours=4),
    start_date=pendulum.datetime(2021, 1, 1, tz="UTC"),
    catchup=False,
    tags=["example3"],
) as dag:
    latest_only = LatestOnlyOperator(task_id="latest_only")
    task1 = EmptyOperator(task_id="task1")
    task2 = EmptyOperator(task_id="task2")
    task3 = EmptyOperator(task_id="task3")
    task4 = EmptyOperator(task_id="task4", trigger_rule=TriggerRule.ALL_DONE)

    latest_only >> task1 >> [task3, task4]
    task2 >> [task3, task4]

```

Trong DAG trên:
- task1 là downstream của task latest_only do đó sẽ bị skipped trong tất cả các lần chạy, ngoại trừ lần chạy gần nhất
- task2 độc lập với task latest_only, nên sẽ chạy khi được lập lịch bình thường
- task3 là downstream của task1 và task2. Do trigger rule mặc định là all_success, do đó nếu task1 fail thì task3 cũng sẽ không được thực hiện
- task4 cũng là task downstream của task1 và task2, tuy nhiên, trigger của task này là ALL_DONE, do đó task4 vẫn chạy sau khi tất cả các task trước hoàn thành

#### Depends On Past
Depends on past là điều kiện thực hiện một task dựa vào điều kiện của task này ở lần chạy trước thành công hay không. Trước hợp task được lập lịch chạy và dừng độc lập mỗi lần thì tính năng này không khả dụng.


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
Operator là một template khai báo sẵn cấu hình của một task, operator được khai báo bên trong file DAG. Airflow cung cấp nhiều loại operator tích hợp sẵn để thực hiện các task với yêu cầu khác nhau và giao tiếp với một số hệ thống khác như HDFS, MySQL, AWS. Một số loại operator thông dụng như *bash operator*, *python operator*, *email operator*, *decorator @task*

### BashOperator
- Tạo task thực hiện một script bash

### PythonOperator
- Tạo task sử dụng hàm python với các tham số bên trong

### EmailOperator
- Tạo task gửi email

### Decorator @task
- Sử dụng *@task* decorator để tự tạo một operator bằng các viết hàm python và decorator

## Sensor
Sensor là một loại operator đặc biệt, thực hiện một task khi có một sự kiện nào đó xảy ra. Sự kiện này dựa vào một thời gian cụ thể, một file được tạo hoặc một event từ bên ngoài. Có 2 trạng thái của sensor:
- poke (mặc định): sensor được khởi tạo từ đầu và luôn ở trong worker khi dag đang chạy
- reshedule: mỗi lần gọi, sensor mới được khởi tạo trong work, khi không được gọi sensor sẽ ở trạng thái sleep
