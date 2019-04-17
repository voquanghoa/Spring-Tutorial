# Security phần 1 - Chuẩn bị

## Tạo model.

Giả sử rằng, ta cần có một hệ thống dữ liệu người dùng, mỗi người dùng có thể mang các quyền hạn khác nhau bao gồm:
- `ROLE_ADMIN` --> Có thể quản lý dữ liệu (thêm, sửa, xóa)
- `ROLE_MEMBER` --> Có thể xem dữ liệu

Ta có model như sau

- Role

```java
import lombok.*;

import javax.persistence.*;
import java.util.ArrayList;
import java.util.HashSet;
import java.util.Set;

@Entity
@Data
@RequiredArgsConstructor
@NoArgsConstructor
public class Role {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id = 0;


    @Column(nullable = false)
    @NonNull
    private String name;


    @Column
    @NonNull
    private String description;
}
```

- User

```java

import com.fasterxml.jackson.annotation.JsonAlias;
import lombok.Data;
import lombok.NoArgsConstructor;
import lombok.NonNull;
import lombok.RequiredArgsConstructor;

import javax.persistence.*;
import java.util.Set;

@Entity
@Data
@NoArgsConstructor
@RequiredArgsConstructor
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;

    @Column(nullable = false)
    @NonNull
    private String username;

    @JsonAlias("first_name")
    @Column(nullable = false)
    @NonNull
    private String firstName;

    @JsonAlias("last_name")
    @Column(nullable = false)
    @NonNull
    private String lastName;

    @Column(nullable = false)
    @NonNull
    private String password;

    @ManyToMany(fetch = FetchType.EAGER)
    @JoinTable(
            name = "user_role",
            joinColumns = @JoinColumn(name = "user_id"),
            inverseJoinColumns = @JoinColumn(name = "role_id")
    )
    private Set<Role> roles;
}
```

Ngoài ra, ta cũng có repository tương ứng của 2 model này

```java
import com.voquanghoa.bookstore.models.Role;
import org.springframework.data.jpa.repository.JpaRepository;

public interface RoleRepository extends JpaRepository<Role, Integer> {

    Role findByName(String name);
}
```

```java
import com.voquanghoa.bookstore.models.User;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Service;

@Service
public interface UserRepository extends JpaRepository<User, Integer>{

    User findByUsername(String username);
}
```

Để khởi tạo 2 người dùng với hai role tương ứng lúc khởi chạy ứng dụng, ta tạo class `DataSeedingListener` trong package `configurations` như sau

```java

import com.voquanghoa.bookstore.models.Role;
import com.voquanghoa.bookstore.models.User;
import com.voquanghoa.bookstore.repositories.RoleRepository;
import com.voquanghoa.bookstore.repositories.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationListener;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.event.ContextRefreshedEvent;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.stereotype.Component;

import java.util.HashSet;

@Component
@Configuration
public class DataSeedingListener implements ApplicationListener<ContextRefreshedEvent> {

    @Autowired
    private UserRepository userRepository;

    @Autowired
    private RoleRepository roleRepository;

    private void addRoleIfMissing(String name, String description){
        if (roleRepository.findByName(name) == null) {
            roleRepository.save(new Role(name, description));
        }
    }

    private void addUserIfMissing(String username, String password, String... roles){
        if (userRepository.findByUsername(username) == null) {
            User user = new User(username, "First name", "Last name", new BCryptPasswordEncoder().encode(password));
            user.setRoles(new HashSet<>());

            for (String role: roles) {
                user.getRoles().add(roleRepository.findByName(role));
            }

            userRepository.save(user);
        }
    }

    @Override
    public void onApplicationEvent(ContextRefreshedEvent event) {

        addRoleIfMissing("ROLE_ADMIN", "Administrators");
        addRoleIfMissing("ROLE_MEMBER", "Members");

        addUserIfMissing("user", "456", "ROLE_MEMBER");
        addUserIfMissing("admin", "1234", "ROLE_MEMBER", "ROLE_ADMIN");
    }
}
```

Xem:

- [Phần 2](Security-2.md)
- [Phần 3](Security-3.md)
- [Phần 4](Security-4.md)

[Trang chủ](https://voquanghoa.github.io/Spring-Tutorial/)