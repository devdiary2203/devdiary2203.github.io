---
title: "Accuracy, Precision, Recall"
layout: post
date: 2023-03-01 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- machine learning
category: blog
author: devdiary
description: Nội dung như title
---

Ta khởi đầu với mô hình phân loại logistic regression dự đoán ra xác suất là khả năng xảy ra của input. Ví dụ trong bài toán recommendation system, mô hình dự đoán ra khả năng click vào một bài post. Xác suất này nếu cao hoặc thấp hẳn thì không có vấn đề gì phải bàn, nhưng nếu xác suất dự đoán ở khoảng giữa [0, 1] thì việc quyết định sẽ gặp khó khăn. Đó là lí do ta cần chọn ra một giá trị gọi là decision threshold hay classification threshold (ngưỡng) để đưa ra quyết định dự đoán dễ dàng hơn. Giá trị này được chọn tùy thuộc vào chất lượng mô hình hoặc yêu cầu bài toán.

# True, False, Positive, Negative
Bây giờ ta cần định nghĩa các khái niệm, độ đo để đánh giá một mô hình. Chắc ai cũng biết câu chuyện cậu bé chăn cừu nói dối dân làng là sói đến, sau nhiều lần như thế thì dân làng không còn tin lời cậu bé nữa.

Giả sử ta coi như lời thông báo của cậu bé là dự đoán, sẽ có 2 khả năng:
- "Có sói": positive class (thông báo tốt)
- "Không có sói": negative class (thông báo không tốt)

Positive, negative ở đây do ta tự quy định để dễ hình dung.

![Mô hình true-false positive](/assets/images/true-false-positive.png)

Ở đây ta cần hiểu được 2 khái niệm:
- True positive (TP): khi cậu bé thông báo cho dân làng chính xác có sói đến
- False positive (FP): khi cậu bé thông báo sai cho dân làng (trêu dân làng) là có sói đến, trong khi không có con sói nào
- False negative (FN): khi cậu bé thông báo sai cho dân làng là không có sói, trong khi sói đã đến và ăn hết cừu
- True negative (TN): khi cậu bé thông báo đúng cho dân làng là không có sói và thực tế là không có sói.

2 trường hợp TP và TN có thể hiểu là mô hình dự báo đúng. Trong khi đó:
- FP: dự báo sai lớp đúng
- FN: dự báo sai lớp sai

# Accuracy
Accuracy là độ đo để đánh giá mô hình phân loại. 

$Accuracy = \dfrac{TP + TN}{TP + TN + FP + FN}$

Nhìn công thức có thể thấy acc càng cao thì mô hình càng có khả năng dự đoán đúng. Vậy tại sao không lấy số kết quả dự đoán chính xác chia cho tổng số dự đoán luôn mà còn phải phân tích TP, TN, FP, FN. Việc phân tích TP, TN sẽ cho thấy khả năng dự đoán của mô hình tốt/xấu theo chiều hướng nào (dự đoán user click tốt hay dự đoán user không click tốt hơn). Từ việc phân tích này, ta có thể thấy một vấn đề với độ đo accuracy đó là khi dữ liệu bị mất cân bằng (số lượng mẫu các lớp lệch nhau) thì kết quả accuracy không phản ảnh hết được chất lượng của mô hình.

# Precision
Precision là một độ đo khác để đánh giá mô hình cùng với accuracy. Ý nghĩa của nó đánh giá khả năng mô hình dự đoán đúng được các mẫu ở lớp positive. 

Như đối với bài toán recommendation dự đoán khả năng user click vào một bài post, ta có thể quy định:
- Positive: user click vào bài viết
- Negative: user không click vào bài viết

Precision sẽ đánh giá khả năng mô hình dự đoán đúng user có click vào bài post.

$ Precision = \dfrac{TP}{TP + FP} $

# Recall
Recall là một độ đo khác để đánh giá mô hình cùng với accuracy. Ý nghĩa của nó đánh giá xem mô hình dự đoán bị sai lớp positive nhiều không. Trong bài toán user click, ta có thể hiểu là đánh giá những user bị dự đoán là không click nhưng thực tế là họ sẽ click vào bài.

$ Recall = \dfrac{TP}{TP + FN} $

Dễ thấy 2 giá trị *Precision* và *Recall* càng cao thì mô hình có khả năng dự đoán càng tốt. Giá trị 2 độ đo này sẽ thay đổi khi thay đổi *decision threshold* ở trên. *F1-score* cũng là một độ đo để đánh giá chất lượng mô hình kết hợp cả *precision* và *recall*.

Vậy làm cách nào để chọn được ngưỡng *decision threshold* này?
# ROC curve
Đường cong ROC là một đồ thị đánh giá chất lượng của mô hình ở tất cả các ngưỡng phân loại. ROC đánh giá dựa vào 2 giá trị:
- True positive rate (TPR) - Recall:
$ TPR = \dfrac{TP}{TP + FN} $
- False positive rate (FPR): Đánh giá khả năng dự đoán sai của mô hình (dự đoán user click trong khi user sẽ không click)
$ FPR = \dfrac{FP}{FP + TN} $

