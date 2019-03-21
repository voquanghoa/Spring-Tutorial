# Database phần 2 - Tùy biến, Query

## 1. Tùy cột trong bảng dữ liệu

Giả sử ta có model như sau

```java
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

Thì tương ứng, ta có được bảng dữ liệu như sau
![MySQL](Images/sql-12.png)

Ở đây
- @Id : xác định khóa chính của bảng
- @GeneratedValue: Cho biết cột dữ liệu này sẽ được tự tạo giá trị lúc được insert

Ngoài ra, còn có nhiều annotitaion khác có thể thay đổi thuộc tính của các cột, bao gồm

Validation dữ liệu
- @Null/@NotNull: Bắt buộc dữ liệu phải null hoặc khác null thì mới được insert/update
- @Past/@Future: Sử dụng với cột thời gian, quy định dữ liệu phải là một giá trị ở quá khứ hoặc tương lai
- @Min/@Max: Sử dụng với cột số, quy định dữ liệu phải lớn hơn hoặc nhỏ hơn một giá trị nhất định
- @Size: Quy định chiều dài tối thiểu/tối đa của chuỗi
- @Pattern: Sử dụng cho cột String quy định dữ liệu phải phù hợp với pattern

Bổ sung thông tin cột
- @Lob: Cho biết chuỗi có thể có kích thước rất lớn
- @Column: Định nghĩa thông tin cột như tên, sự duy nhất
- @OrderColumn: Cho biết thứ tự các cột trong bảng

Khai báo các quan hệ (Sẽ nghiên cứu kỹ ở phần sau)
- @PrimaryKeyJoinColumn: Chỉ định khóa ngoại
- @PrimaryKeyJoinColumns: Chỉ định tập khóa ngoại
- @ManyToMany: Cho biết quan hệ nhiều - nhiều
- @ManyToOne: Cho biết quan hệ nhiều - một
- @OneToMany: Cho biết quan hệ một - nhiều
- @OneToOne: Cho biết quan hệ một-một
- ...

## 2. Tùy biến các câu lệnh query

Ở phần trước, ta đã biết cách khai báo một repository như sau

```java
public interface AuthorRepository extends CrudRepository<Author, Integer> {
}
```

Những hàm mà một `authorRepository` cung cấp:
- save: lưu (bao gồm cả insert/update) một author
- saveAll: lưu (insert/update) cùng lúc nhiều author
- findById: tìm kiếm một author theo id
- existsById: kiểm tra một id có tồn tại
- findAll: lấy tất cả các author
- findAllById: tìm nhiều author theo danh sách id
- count: lấy lượng author
- deleteById: xóa author bởi id
- delete: xóa author
- deleteAll: xóa author theo danh sách hoặc toàn bộ author

Ngoài ra, ta có thể khai báo thêm các method với tên theo quy tắc và danh sách tham số phù hợp, framework sẽ đi implement giùm. Ví dụ

```java
public interface AuthorRepository extends CrudRepository<Author, Integer> {
    List<Author> findByNameContaining(String name);
}
```

Tên hàm `findByNameContaining` với `findBy` là tìm author theo điều kiện và `NameContaining` tức là tên chứa chuỗi name ở trong param.

Kết quả
![MySQL](Images/sql-13.png)

### Query bằng tên hàm


Khi đặt tên một hàm trong repository bắt đầu với `find`, `query`, `read`, `get`, `count` và tiếp theo bởi `by` và danh sách từ khóa điều kiện

|Keyword|Sample|JPQL Snippet|
|--- |--- |--- |
|And|findByLastnameAndFirstname|...where x.lastname = ?1 and x.firstname = ?2|
|Or|findByLastnameOrFirstname|...where x.lastname = ?1 or x.firstname = ?2|
|Is, Equals|findByFirstnameEquals|...where x.firstname = ?1|
|Between|findByStartDateBetween|...where x.startDate between ?1 and ?|
|LessThan|findByAgeLessThan|...where x.age < ?1|
|LessThanEqual|findByAgeLessThanEqual|...where x.age <= ?1|
|GreaterThan|findByAgeGreaterThan|...where x.age > ?1|
|GreaterThanEqual|findByAgeGreaterThanEqual|...where x.age >= ?1|
|After|findByStartDateAfter|...where x.startDate > ?1|
|Before|findByStartDateBefore|...where x.startDate < ?1|
|IsNull|findByAgeIsNull|...where x.age is null|
|IsNotNull, NotNull|findByAge(Is)NotNull|...where x.age not null|
|Like|findByFirstnameLike|...where x.firstname like ?1|
|NotLike|findByFirstnameNotLike|...where x.firstname not like ?1|
|StartingWith|findByFirstnameStartingWith|...where x.firstname like ?1 (parameter bound with appended %)|
|EndingWith|findByFirstnameEndingWith|...where x.firstname like ?1 (parameter bound with prepended %)|
|Containing|findByFirstnameContaining|...where x.firstname like ?1 (parameter bound wrapped in %)|
|OrderBy|findByAgeOrderByLastnameDesc|...where x.age = ?1 order by x.lastname desc|
|Not|findByLastnameNot|...where x.lastname <> ?1|
|In|findByAgeIn(Collection ages)|...where x.age in ?1|
|NotIn|findByAgeNotIn(Collection ages)|...where x.age not in ?1|
|True|findByActiveTrue()|...where x.active = true|
|False|findByActiveFalse()|...where x.active = false|
|IgnoreCase|findByFirstnameIgnoreCase|...where UPPER(x.firstame) = UPPER(?1)|


Ví dụ

```java
import com.voquanghoa.bookstore.models.Author;
import org.springframework.data.repository.CrudRepository;

import java.util.List;

public interface AuthorRepository extends CrudRepository<Author, Integer> {
    List<Author> findByNameContaining(String name);
    List<Author> findByNameOrEmail(String name, String email);
    List<Author> findByNameContainingOrEmailContaining(String name, String email);
}
```

### Sử dụng Query

Ta viết lệnh sql để thực hiện việc query

```java
import com.voquanghoa.bookstore.models.Author;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.CrudRepository;

import java.util.List;

public interface AuthorRepository extends CrudRepository<Author, Integer> {
    @Query("select u from Author u where email = ?1")
    List<Author> findByEmailAddress(String emailAddress);
}
```

Ở đây `?1` là giá trị sẽ được thay thế bởi tham số `emailAddress`, hoặc một cách tường minh hơn, giá trị tham số hàm có thể được điền bằng cách dùng tên

```java
public interface AuthorRepository extends CrudRepository<Author, Integer> {
    @Query("select u from Author u where email = :emailAddress")
    List<Author> findByEmailAddress(String emailAddress);
}
```

### Cập nhật dữ liệu

Ta có thể cập nhật dữ liệu với thao tác UPDATE/DELETE bằng query với Annotation @Modifing như sau:

```java
import com.voquanghoa.bookstore.models.Author;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.CrudRepository;
import org.springframework.transaction.annotation.Transactional;

public interface AuthorRepository extends CrudRepository<Author, Integer> {

    @Transactional
    @Modifying
    @Query("delete Author where email = :emailAddress")
    void deleteByEmail(String emailAddress);

    @Transactional
    @Modifying
    @Query(value = "update Author set email = :newEmail  where id = :id")
    void updateEmailById(int id, String newEmail);
}
```

1. Thêm thuộc tính `price` và thêm vào Repository phương thức tìm kiếm sách có giá trong phạm vi `min` đến `max`
2. Thay đổi router `/find` để nhận vào một query `q` có thể tìm kiếm book có `name` query `q`
3. Thay đổi router GET "" để nhận vào các optional parametter String `q`, String `order`, String orderType="ASC", thay đổi response dựa theo params.