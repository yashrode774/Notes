# Spring boot notes.

## Annotations
- @SpringBootApplication - Marks the main class of a Spring Boot application.
  - @Configuration - Declares this class contains Spring configuration. (No need to create bin for this class just create beans for @Bean)
  - @EnableAutoConfiguration - Automatically creates beans based on dependencies in spring IOC.
  - @ComponentScan - Scans the package for components like @Service, @Repository, etc
    we can provide the package name to ComponentScan where spring will look for components.

    ### ComponentScan with excludeFilters

    @ComponentScan(
    basePackages = "com.example",
    excludeFilters = @ComponentScan.Filter(type = FilterType.REGEX, pattern = "com\\.example\\.demo\\..*")
    )

## @Component - Creates bin

- @Service
- @Repository
- @Controller
- @RestController
- @Autowired

## Difference between @Controller and @RestController -

@Controller- typically returns view name(use @ResponseBody on method to return JSON/XML)
@RestController(@Controller + @ResponseBody)- All methods return data (JSON/XML/etc), not views.

## @Autowired

Injects dependencies automatically (constructor, field, setter).

## @Configuration

- Marks a class as source of Bean definitions.
- Declares a bean that will be managed by Spring.

## @Configuration

public class AppConfig {
@Bean
public MyService myService() {
return new MyService();
}
}

## @RequestMapping

- Maps web requests to handler methods.
- (Can be used at class or method level.)

Shorthands:
@GetMapping, @PostMapping, @PutMapping, @DeleteMapping, @PatchMapping

## @PathVariable

```
@GetMapping("/users/{id}") 
public User getUser(@PathVariable int id) { ... }
```

## @RequestParam

```
@GetMapping("/search")
public String search(@RequestParam String keyword) { ... }
```

## @RequestBody

```
@PostMapping("/users") 
public String addUser(@RequestBody User user) { ... }
```

## @Transactional

- In above example if updateUser has two db operations and if one of them fails
  then transaction revert back the first operation also.
  `@Transactional public void updateUser(User user) { ... }`

  ## Propagation
  - Defines **how a transaction is propagated** from the **calling method** to the **called method**.
  - The **default propagation** type is `REQUIRED`.

- **`REQUIRED`**  
  If the calling method has an active transaction, the called method will **participate in it**.  
  If not, a **new transaction** will be started.

- **`REQUIRES_NEW`**  
  Always starts a **new transaction**, even if the calling method already has one.  
  The caller's transaction is **suspended**.  
  _Useful for tasks like sending notifications._

- **`MANDATORY`**  
  The calling method **must already have an active transaction**.  
  If not, an **exception is thrown**.

- **`NESTED`**  
  If a transaction exists, the method executes within a **nested transaction** using a **savepoint**.  
  If an exception occurs, changes made within this method are **rolled back to the savepoint**, but the outer transaction can continue.  
  If no transaction exists, it behaves like `REQUIRED`.

- **`SUPPORTS`**  
  The method will **join the transaction if one exists**; otherwise, it will **run non-transactionally**.

- **`NOT_SUPPORTED`**  
  The method will **not run in a transaction**, even if the caller has one.  
  The existing transaction is **suspended**.  
  _Typically used for third-party API calls._

- **`NEVER`**  
  The method must **not run within a transaction**.  
  If a transaction exists, an **exception is thrown**.

## Sample code:
  ```java
  @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void calledMethod(User user) {
        // Logic to send notification
        // Throws exception if sending fails
    }
  ```

## SQL Isolation Levels (with Examples)

Default isolation level in mysql is REPEATABLE READ and it is different for different db.
### 1. READ UNCOMMITTED
- 🔓 No locks → allows **dirty reads**
- ❌ Allows: **Dirty Reads**, **Non-Repeatable Reads**, **Phantom Reads**
- 🧠 *Example:*  
  T1 updates a balance,  
  T2 reads it before T1 commits → may read incorrect value.

---

### 2. READ COMMITTED
- ✅ Only reads **committed** data → prevents dirty reads  
- ❌ Still allows: **Non-Repeatable Reads**, **Phantom Reads**
- 🧠 *Example:*  
  T1 reads a row.  
  T2 updates and commits the row.  
  T1 re-reads → sees different value.

---

### 3. REPEATABLE READ
- ✅ Prevents **Dirty Reads**, **Non-Repeatable Reads**  
- ❌ Still allows: **Phantom Reads**
- 🧠 *Example:*  
  T1 reads rows with salary > 5000.  
  T2 inserts a new row with salary 6000.  
  T1 re-runs the same query → sees new row.

---

### 4. SERIALIZABLE
- ✅ Prevents **Dirty Reads**, **Non-Repeatable Reads**, **Phantom Reads**
- 🧠 *Example:*  
  Transactions behave as if they run **one after another**, not in parallel.

```java
@Transactional(isolation = Isolation.READ_COMMITTED)
public void someBusinessMethod() {
    // your logic here
}
```
--------------------------------------

## Scope of beans
1. Singleton
   - Only one shared instance per Spring container
   - Used for stateless services, repositories, etc.
2. Prototype
   - Spring creates a new instance every time you request the bean.
   - Good for stateful beans (example ShoppingCart)
3. Request
   - One bean instance per HTTP request.
   - Created at the start of request, destroyed when request completes.
4. Session
   - One bean instance per user HTTP session.
   - when you want to store user-specific data
5. Application
   - One bean instance per ServletContext (application-wide).
   - Similar to singleton but scoped to a web application.

### How to use
@Scope("prototype")
By default, scope is Singleton.

-------------------------

# **JPA**

Java Persistence API (JPA), which is the standard specification for mapping Java objects to relational databases.

### Three types of repositories

1. CrudRepository + ListCrudRepository
    ||
    \/
2. PagingAndSortingRepository + ListPagingAndSortingRepository
    ||
    \/
3. JpaCrudRepository

### Custom Query method

For the simple operations like findByName, findByAge we just need to add the method declaration
in `JpaRepository` like `List<Applicant> findByPhone(String number);` and we are all se to use findByPhone method.

### Fetch Types in JPA (and Spring Data JPA)

- Lazy and Eager
  FetchType.LAZY	Related entities are loaded on demand, when accessed
  FetchType.EAGER	Related entities are loaded immediately, with the parent entity

```
    @OneToOne(fetch = FetchType.EAGER) // Eager loading
    private Address address;

    @OneToMany(fetch = FetchType.LAZY) // Lazy loading
    private List<Order> orders;
```

## cascading
cascading operations propagates changes made to entities across relationships.
CascadeType.ALL
CascadeType.PERSIST
CascadeType.MERGE
CascadeType.REMOVE
CascadeType.REFRESH
CascadeType.DETACH

## @JoinColumn

@JoinColumn defines which column in the current table will store the foreign key.
`@JoinColumn(name = "column_name", referencedColumnName = "target_column")`
referencedColumnName is optional, if not given it uses the primary key of the referenced table as referencedColumnName.

```java
@Entity
public class Employee {
@Id
@GeneratedValue
private Long id;@ManyToOne
@JoinColumn(name="department_id")
private Department department;// other fields and methods
}
```

## JPQL and Native Queries

- `@Query("SELECT u FROM User u WHERE u.email = :email")`
- Use native SQL with nativeQuery = true

## @Query

In Spring Data JPA, the @Query annotation is used to define custom queries.
The nativeQuery attribute controls whether the query is a **native SQL query** or a JPQL
(Java Persistence Query Language) query.

### nativeQuery = false

`@Query(nativeQuery = true, value = "SELECT * FROM Student ORDER BY age")  List<Student> findAllStudentsOrderByAge();`

### nativeQuery = false

`@Query("SELECT s FROM Student s ORDER BY s.age") List<Student> findAllStudentsOrderByAge();`

## Pagination and Sorting

`Page<User> findAll(Pageable pageable); pageable = PageRequest.of(page, size, Sort.by("name"))` // paging + sorting

sorting:
`userRepository.findAll(Sort.by("name").descending());`

---

# ✅ Testing in Spring Boot

Testing helps verify that your code works as expected. Spring Boot uses **JUnit 5** by default along with various assertion methods.

---

## 🧪 Writing Tests

Use `@Test` to mark a method as a test case.

### 🔹 Common Assertions

```java
assertEquals(expected, actual);
assertTrue(condition);
assertFalse(condition);
assertNull(object);
assertNotNull(object);
assertThrows(Exception.class, () -> someCode());
assertAll("desc", () -> { ... }, () -> { ... });
```

---

## 🚀 Integration Testing
Let's write a test for 
```java
@Service
public class UserService {

    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User create(String name) {
        return userRepository.save(new User(name));
    }
}
```

1. Use `@SpringBootTest` to load the full Spring context.

```java
// Integration Test - Loads Spring context
@SpringBootTest
class UserServiceIntegrationTest {

    @Autowired
    private UserService userService;

    @MockBean
    private UserRepository userRepository;

    @Test
    void testCreateUser() {
        User dummyUser = new User("Yash");
        when(userRepository.save(any(User.class))).thenReturn(dummyUser);

        User result = userService.create("Yash");

        assertEquals("Yash", result.getName());
        verify(userRepository).save(any(User.class));
    }
}
```

2. Unit Test using @ExtendWith(MockitoExtension.class), @InjectMocks and @Mock
```
// Unit Test - Tests UserService in isolation using Mockito
@ExtendWith(MockitoExtension.class)
class UserServiceUnitTest {

    @Mock
    private UserRepository userRepository;

    @InjectMocks
    private UserService userService;

    @Test
    void testCreateUser() {
        User dummyUser = new User("Yash");
        when(userRepository.save(any(User.class))).thenReturn(dummyUser);

        User result = userService.create("Yash");

        assertEquals("Yash", result.getName());
        verify(userRepository).save(any(User.class));
    }
}
```
---

## 🔁 Parameterized Tests

Use `@ParameterizedTest` to test multiple inputs.

```java
@ParameterizedTest
@CsvSource({
  "1,2,3",
  "2,2,4",
  "2,2,5"
})
void testAdding(int a, int b, int expected) {
    assertEquals(expected, a + b);
}
```

You can also use `@CsvFileSource` for external CSV input.

---

## 🔄 Test Lifecycle

Use these for setup/cleanup tasks:

```java
@BeforeEach   // runs before every test
@AfterEach    // runs after every test
@BeforeAll    // runs once before all tests (must be static)
@AfterAll     // runs once after all tests (must be static)
```

Example:

```java
@BeforeAll
static void setUp() {
    // Open file
}

@AfterAll
static void tearDown() {
    // Close file
}
```

---

## 📈 Code Coverage

- Shows how much of your code is tested (%).
- Use **IntelliJ plugin: Code Coverage for Java** or tools like **JaCoCo**.


# 📝 Logging

Logging is the recording of application events (e.g., errors, info, debug messages) to trace runtime behavior.

- Uses **SLF4J** (Simple Logging Facade for Java)
- By default, SLF4J uses **Logback** under the hood

---

## ✅ How to Use

- Add `@Slf4j` (from Lombok)
- Without Lombok: `Logger log = LoggerFactory.getLogger(ClassName.class);`
- Log example:  
  ```java
  log.info("📝 Your message here!");

### Logging levels

  trace < debug < info < warn < error
For info you will see all messages at that level and above(right)(inclusive).

## ⚙️ Logging Configuration (application.properties)

```properties
# Log file name
logging.file.name=app.log

# Root logging level
logging.level.root=INFO

# Set logging level for a specific package
logging.level.com.example=DEBUG
```

### Distributed tracing

OpenTelemetry -> jaeger
https://refactorfirst.com/distributed-tracing-with-opentelemetry-jaeger-in-spring-boot

---

# 📊 Monitoring

Monitoring involves collecting **metrics**, **health checks**, and **performance data** from the application.

---

## 🚀 Spring Boot Actuator

Spring Boot provides built-in monitoring endpoints via the **Actuator** module.

### 🔹 Common Endpoints

| Endpoint              | Description                                 |
|-----------------------|---------------------------------------------|
| `/actuator/health`    | App health status                           |
| `/actuator/beans`     | All registered beans                        |
| `/actuator/mappings`  | All request mappings                        |
| `/actuator/metrics`   | App & system metrics (CPU, memory, etc.)    |
| `/actuator/env`       | Current environment properties              |
| `/actuator/httptrace` | Recent HTTP requests (last 100 by default)  |

---

## ⚙️ Configuration (application.properties)

```properties
# Expose specific or all actuator endpoints
management.endpoints.web.exposure.include=*

# Show detailed health info
management.endpoint.health.show-details=always
alth.show-details=always
```

## Can we override or replace the Embedded tomcat server in Spring Boot
- Yes, using exclusions you can and use different server (e.g. Jetty)
```properties
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```


# 🌐 Global Exception Handling in Spring Boot

In Spring Boot, **Global Exception Handling** allows you to manage exceptions from a single place, rather than handling them in each controller.

---

## ✅ Steps to Implement

1. **Create a class annotated with `@ControllerAdvice`.** (@ControllerAdvice makes the class global across all controllers.)
2. **Extend** `ResponseEntityExceptionHandler` to inherit Spring’s default exception handling.
3. Inside this class, define methods annotated with `@ExceptionHandler` to handle specific exceptions globally. (@ExceptionHandler is used to specify which exception a method should handle.)

---

## 📘 Example

```java
@ControllerAdvice
public class GlobalExceptionHandler extends ResponseEntityExceptionHandler {

    @ExceptionHandler(NullPointerException.class)
    public ResponseEntity<String> handleNullPointerException(NullPointerException ex) {
        return new ResponseEntity<>("Null value found!", HttpStatus.BAD_REQUEST);
    }

    @ExceptionHandler(CustomException.class)
    public ResponseEntity<String> handleCustomException(CustomException ex) {
        return new ResponseEntity<>(ex.getMessage(), HttpStatus.INTERNAL_SERVER_ERROR);
    }

}
