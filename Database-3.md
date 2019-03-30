# Database phần 3 - Dữ liệu quan hệ

## Quan hệ Nhiều - Một

Giả sử ta có lớp `Book` và lớp `Author` và ta có mối quan hệ `Nhiều book` thuộc về `Một Author`, khi đó ta thiết lập mối quan hệ `@ManyToOne` từ lớp `Book` tới lớp `Author`.

Lớp `Book`
```java
import lombok.Data;

import javax.persistence.*;

@Data
@Entity
public class Book {

    @Id
    @GeneratedValue(strategy= GenerationType.AUTO)
    private int id;

    private String name;

    @ManyToOne
    private Author author;

    private int year;
}
```

Lớp `Author`

```java
import lombok.Data;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
@Data
public class Author {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;

    private String name;
    private String email;
    private String website;
    private String favoriteQuote;
}
```

Thử nghiệm

```java
System.out.println("Hello World");
Author author = new Author();
author.setEmail("hoa@gmail.com");
author.setName("Hoa");
author = authorRepository.save(author);

Book book = new Book();
book.setAuthor(author);
book.setName("Harry");
book.setYear(1990);

int savedId = bookRepository.save(book).getId();

Book savedBook = bookRepository.findById(savedId).get();

System.out.println(savedBook);
```


Kết quả:

1. Spring sẽ tự động tạo cột `author_id` từ bảng `book` và mỗi khi lưu trường author, Spring sẽ gán giá trị tương ứng

![Author id](Images/sql-14.png)

2. Khi lấy lên giá trị book, spring sẽ tự động gán giá trị author tương ứng.

![Author id](Images/sql-15.png)

