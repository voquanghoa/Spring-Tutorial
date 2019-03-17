## 1. Khởi tạo project

Vào trang https://start.spring.io/, điền cấu hình tương tự như dưới đây và bấm `Generate Project`

![Init project](Images/spring-init.png)

Download file zip về máy, giải nén vào thư mục an toàn, ví dụ ở đây mình giải nén vào thư mục ~/code/Train/Tuto/bookstore

![Init project](Images/spring-2.png)

Mở command line tại thư mục giải nén và chạy lệnh `mvn install`

![Init project](Images/spring-3.png)

Nếu mọi thứ sẵn sàng, kết quả sẽ là 

![Init project](Images/spring-4.png)

Sau đó chạy lệnh `mvn spring-boot:run`

![Init project](Images/spring-5.png)

Chú ý thông báo `Tomcat started on port(s): 8080 (http) with context path ''`, mở trình duyệt và nhập http://localhost:8080/ sẽ nhận được 

![Init project](Images/spring-6.png)

Như vậy là ok. Tắt command line.

## 2. Mở dự án với IntelliJ IDEA

Mở IntelliJ IDEA và chọn Open

![IntelliJ IDEA](Images/spring-7.png)

Chọn đường dẫn tới dự án

![IntelliJ IDEA](Images/spring-8.png)

Sau khi dự án được mở, các tiến trình IDE khởi chạy hoàn tất, mở file Application và chọn `Run <File>.main()` từ menu chuột phải

![IntelliJ IDEA](Images/spring-10.png)

Nếu mọi thứ suôn sẽ, web service cũng được khởi chạy tương đương với lệnh `mvn spring-boot:run`

![IntelliJ IDEA](Images/spring-9.png)

Như vậy là mọi thứ đã sẵn sàng để tiếp tục những bài tiếp theo.