# Deployment

## Chạy spring boot như một ứng dụng độc lập (build in Tomcat)

Step 1. Build ứng dụng bằng lệnh `mvn install`

Nếu ứng dụng được build thành công, ta sẽ có file package (jar hoặc war) tương ứng nằm trong thư mục `target`

Chạy ứng dụng bằng lệnh `java -jar <đường dẫn tới file jar>`

## Chạy spring boot với Tomcat

Build ứng dụng để có file package jar hoặc war như trên.

Download tomcat từ https://tomcat.apache.org/

![Tom cat](Images/deploy-1.png)

Giải nén vào một thư mục thích hợp.

Mặc định, tomcat sẽ chạy ở port 8080 cho http, có thể thay đổi ở file `config/server.xml`

![Tom cat](Images/deploy-2.png)