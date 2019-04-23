# Deployment Heroku

## Cấu hình dự án

### Quy định file jar

Mở file `pom.xml`

![Heroku](Images/deploy-6.png)

Thêm dòng `<finalName>bookstore</finalName>` để chỉ định tên package. Khi đó, nếu ta chạy lệnh `mvn clean install` thì ta sẽ nhận được file `bookstore.jar` trong thư mục `target`.

### Tạo profile

Tạo profile heroku với file `application-heroku.properties` với nội dung:

```
spring.jpa.hibernate.ddl-auto=create
spring.datasource.driverClassName=org.postgresql.Driver
spring.datasource.url: ${JDBC_DATABASE_URL:}
spring.datasource.maxActive=10
spring.datasource.maxIdle=5
spring.datasource.minIdle=2
spring.datasource.initialSize=5
spring.datasource.removeAbandoned=true
server.port=${PORT:5000}
jwt-key=eyJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJCb29rU3RvcmUifQ.xW8fVhGB66Ie3vL8hZoAOuntDXokWaxv3Nkl5V3V_ao
```

![Heroku](Images/deploy-8.png)

Tạo file `Procfile` với nội dung 

```
heroku ps:scale web=1
web: java -Dspring.profiles.active=heroku -jar target/bookstore.jar
```
Mục đích chính của file này là khai báo ứng dụng sẽ chạy với file `target/bookstore.jar` và dùng profile `heroku` đã tạo mới ở trên.

![Heroku](Images/deploy-9.png)

### Cấu hình dữ liệu

Ta sẽ sử dụng hệ database `postgress` trên heroku với một số quy định về tên bảng để tránh trùng từ khóa. Cụ thể, với model `User` ta cần đổi tên bảng về `users`. Để làm điều đó, ta thêm 

```java
@Table(name = "users")
```

Ta cũng cần add dependency là thư viện postgresql vào file `pom.xml`

```xml
<dependency>
  <groupId>org.postgresql</groupId>
  <artifactId>postgresql</artifactId>
</dependency>
```

![Heroku](Images/deploy-10.png)


## Heroku

Truy cập trang https://heroku.com đăng ký một tài khoản miễn phí với một username và password.

Tải tại và cài đặt Heroku CLI https://toolbelt.heroku.com

Trên command line, ta chạy lệnh `heroku login`, đăng nhập tài khoản với username/password.

### Tạo dự án heroku

Mở command line, trên thư mục dự án, ta chạy lệnh `heroku create <tên app>`

![Heroku](Images/heroku-1.png)

Ở đây, <tên app> là một tên tùy chọn, bao gồm các chữ số, không được phép trùng với một app khác, mỗi tài khoản được phép tạo tối đa 5 app. Nếu thành công, ta sẽ được một trang web mới với url như hình. Có thể bỏ qua tham số <tên app>, heroku sẽ tạo một tên ngẫu nhiên, có thể không ok lắm.

Tiếp theo, ta cần cài đặt posgres database trên server bằng lệnh `heroku addons:create heroku-postgresql`

Để deploy trang web lên, ta chạy lệnh `git push heroku master`. (Phân biệt với `git push origin master` hay `git push`)

Quá trình này diễn ra khá lâu để hoàn tất (nếu không có lỗi). Sau đó, ta có thể mở trình duyệt bằng cách gõ đường dẫn ở trên hoặc dùng lệnh `heroku open` để xem kết quả.

### Xử lý sự cố

Việc deploy lên heroku có thể gặp nhiều một số tình huống lỗi, kể cả chạy ok ở local nhưng sẽ không chạy hoặc chạy sai trên heroku. Vì vậy, ta phải check vấn đề là gì và tìm cách xử lý như sau:

- Xem log

Xem log là cách dễ nhất để biết vấn đề đang bị là gì, ta có thể xem log bằng lệnh `heroku logs --tail`. Lệnh này sẽ lấy log liên tục từ server và hiển thị ra màn hình, để kết thúc, ta bấm Ctrl + C.

- Check database

Ta có thể dùng lệnh để xem cấu trúc database, thực hiện vài lệnh để xem, sử database bằng cách chạy lệnh `heroku pg:psql`

![Heroku](Images/heroku-2.png)

Để kết thúc, gõ `\q`

- Dùng trang chủ heroku

Đăng nhập trên trang chủ https://dashboard.heroku.com/apps để xem tình trạng app.

# Xem thêm

- https://devcenter.heroku.com/articles/heroku-postgresql#using-the-cli
- https://devcenter.heroku.com/articles/deploying-spring-boot-apps-to-heroku

[Trang chủ](https://voquanghoa.g
