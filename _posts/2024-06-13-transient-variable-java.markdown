---
title: "Transient Variable Java"
layout: post
date: 2024-06-13 22:03
image: /assets/images/markdown.jpg
headerImage: false
tag:
- software engineer
- backend engineer
- java
category: blog
author: devdiary
description: Nội dung như title
---

# Biến transient trong java
Biến transient trong java là một biến mà giá trị của nó sẽ không bị serialized bởi JVM và được gán giá trị mặc định của kiểu trong quá trình deseriablization. Từ khóa transient có thể sử dụng để ngăn các object được serialized (các thuộc tính được gán transient và static). 

Một biến transient (không được persist) được sử dụng chủ yếu với mục đích bảo mật như chứa giá trị password, token sẽ không bị lưu hay di chuyển bất kì đâu trong chương trình. Một ví dụ khác có thể không cần serialized và gán thuộc tính đó bằng transient là trường hợp một biến được tính bằng giá trị các biến khác (chỉ lưu giá trị tạm thời). Class hình chữ nhật có 3 thuộc tính là dài, rộng, chu vi thì chu vi có thể tính được từ thuộc tính dài và rộng, do đó không cần thiết phải serialization thuộc tính chu vi.

![Serialization and Deserialization](serialization-de.png)

# Ví dụ biến transient trong java

Tạo một class Book lưu thông tin của sách như title, tác giả, id sách như sau:
```java
class Book implements Serializable{
    private int id;
    private String title;
    private String author;
    private transient int authorId = 1; //transient variable not serialized

    public Book(int id, String title, String author, int authorId) {
        this.id = id;
        this.title = title;
        this.author = author;
        this.authorId = authorId;
    }

    @Override
    public String toString() {
        return "Book{" + "ID=" + id + ", title=" + title + ", author=" + author + ", authorId=" + authorId + '}';
    }
 
}

```

Khi thực hiện serialization object, JVM sẽ kiểm tra thuộc tính authorId sẽ không được serialized và persisted, giá trị này sẽ được khởi tạo bằng giá trị mặc của kiểu dữ liệu (0 với kiểu dữ liệu int). Cách đánh dấu một biến là transient sẽ giảm khả năng bị lộ thông tin của một trường khi truyền dữ liệu như trong trường hợp class Book, id của tác giả có thể giảm bị lộ khi serialized. Giá trị của authorId khi đọc ra hoặc ghi vào file sẽ = 0 (giá trị mặc định của kiểu int)

```java
public class TransientTest {

 
    public static void main(String args[]) {
 
       Book narnia = new Book(1024, "Narnia", "unknown", 12312312312312);
       System.out.println("Before Serialization: " + narnia);
     
        try {
            FileOutputStream fos = new FileOutputStream("narnia.ser");
            ObjectOutputStream oos = new ObjectOutputStream(fos);
            oos.writeObject(narnia);

            System.out.println("Book is successfully Serialized ");

            FileInputStream fis = new FileInputStream("narnia.ser");
            ObjectInputStream ois = new ObjectInputStream(fis);
            Book oldNarnia = (Book) ois.readObject();
         
            System.out.println("Book successfully created from Serialized data");
            System.out.println("Book after seriazliation : " + oldNarnia);
         
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}
```
