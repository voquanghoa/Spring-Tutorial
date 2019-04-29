# Document API với swagger

Khi api lớn lên với nhiều router khác nhau, ta cần phải có document để người sử dụng dễ nắm bắt. Phổ biến nhất, ta dùng Swagger để tự động tạo document.

Đầu tiên, ta thêm dependency tới swagger vào file `pom.xml` 

```xml
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.9.2</version>
</dependency>

<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.9.2</version>
</dependency>
```

Tiếp theo, ta tạo class `SwaggerConfig` vào package configurations với nội dung

```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class SwaggerConfig {
    @Bean
    public Docket api() {
        return new Docket(DocumentationType.SWAGGER_2)
                .select()
                .apis(RequestHandlerSelectors.any())
                .paths(PathSelectors.any())
                .build();
    }
}
```

Chạy ứng dụng, ta sẽ nhận được

![http://localhost:8080/v2/api-docs](http://localhost:8080/v2/api-docs)

![Swagger](Images/Swagger-1.png)

[http://localhost:8080/swagger-ui.html](http://localhost:8080/swagger-ui.html)

![Swagger](Images/Swagger-2.png)

[Trang chủ](https://voquanghoa.github.io/Spring-Tutorial/)