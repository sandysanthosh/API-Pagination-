Pagination is a common requirement in RESTful services, especially when dealing with large datasets. In Spring Boot, pagination can be easily achieved using Spring Data JPA's `Pageable` and `Page` classes. Here’s how you can configure and implement pagination in a service and REST controller class as per standards:

### 1. Setup Dependencies

Ensure you have the necessary dependencies in your `pom.xml`:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>
```

### 2. Entity Class

Create an entity class, for example, `User`:

```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String email;

    // getters and setters
}
```

### 3. Repository Interface

Create a repository interface that extends `JpaRepository`:

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
}
```

### 4. Service Class

Create a service class that uses the repository to fetch paginated data:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.stereotype.Service;

@Service
public class UserService {

    @Autowired
    private UserRepository userRepository;

    public Page<User> getUsers(int page, int size) {
        Pageable pageable = PageRequest.of(page, size);
        return userRepository.findAll(pageable);
    }
}
```

### 5. REST Controller Class

Create a REST controller to handle HTTP requests and return paginated data:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class UserController {

    @Autowired
    private UserService userService;

    @GetMapping("/users")
    public Page<User> getUsers(
        @RequestParam(defaultValue = "0") int page,
        @RequestParam(defaultValue = "10") int size) {
        return userService.getUsers(page, size);
    }
}
```

### 6. Configuration for Default Page Size and Page Number (Optional)

You can configure default values for page size and page number in `application.properties`:

```properties
spring.data.web.pageable.default-page-size=20
spring.data.web.pageable.max-page-size=50
```

### Explanation

1. **Dependencies**: Ensure you have Spring Data JPA and Web dependencies.
2. **Entity Class**: Define the entity `User` with fields `id`, `name`, and `email`.
3. **Repository Interface**: `UserRepository` extends `JpaRepository`, providing CRUD operations and pagination support.
4. **Service Class**: `UserService` has a method `getUsers` that accepts `page` and `size` parameters, constructs a `Pageable` object, and retrieves paginated data from the repository.
5. **REST Controller**: `UserController` has an endpoint `/users` that accepts `page` and `size` parameters (defaulting to 0 and 10, respectively) and returns paginated user data.

### Example Request

To fetch the first page with 10 users:

```
GET /users?page=0&size=10
```

This will return a `Page<User>` object containing users along with pagination information such as total pages, total elements, etc.

By following these steps, you can effectively implement pagination in a Spring Boot application, adhering to standard practices.
