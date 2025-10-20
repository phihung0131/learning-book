### **Part 1: Foundations of Integration Testing**

#### **Topic 1.1: What is Integration Testing in Spring Boot, and how is it different from Unit Testing?**

#### **Theory: The Testing Pyramid**

Imagine a pyramid. The wide base is made of **Unit Tests**, the middle layer is **Integration Tests**, and the small peak is **End-to-End (E2E) Tests**.

*   **Unit Tests (Base):** These are fast, numerous, and isolated. They test a single "unit" of code—typically a single method within a class—in complete isolation from its dependencies. If a method in your `UserService` calculates a user's age, a unit test would call that specific method and assert its return value. All external dependencies, like a database repository or another service, are replaced with "test doubles" (mocks or stubs).

*   **Integration Tests (Middle):** These tests verify that different "units" or components of your application work together as intended. Instead of testing a single method in isolation, you test a complete flow. For example, you might test that when a controller receives an HTTP request, it correctly calls the service, which then successfully interacts with the database.

*   **E2E Tests (Peak):** These are the most complex and slowest tests. They test the entire application flow from the user interface (or an API client) all the way to the database and back, simulating a real user journey.

#### **Unit Testing vs. Integration Testing in Spring Boot**

Let's break down the key differences in the context of a Spring Boot application.

| Feature | Unit Testing | Integration Testing |
| :--- | :--- | :--- |
| **Scope** | A single class or method (the "unit"). | Multiple components interacting together (e.g., Controller → Service → Repository). |
| **Dependencies** | External dependencies are **mocked**. For example, a `UserRepository` inside a `UserService` is replaced with a mock object. | External dependencies are often **real**. You might use a real database (like PostgreSQL in a Docker container) or a real message queue. |
| **Speed** | **Very Fast.** Tests run in milliseconds because they don't involve I/O, network calls, or starting a server. | **Slower.** Tests can take seconds or even minutes because they load parts of the Spring context, start a database, or listen on a real port. |
| **Spring Context** | **No Spring `ApplicationContext` is loaded.** Tests are plain JUnit/Mockito tests. You manually instantiate the class under test. | **The Spring `ApplicationContext` is loaded.** Spring's dependency injection and other features are active, just like in the real application. |
| **Goal** | To verify the logic of a single unit is correct. "Does my `calculatePrice` method return the right value for these inputs?" | To verify that components collaborate correctly. "Does my REST endpoint save data to the database and return the correct HTTP status?" |
| **Feedback Loop**| **Immediate.** Developers run them constantly during development. | **Slightly delayed.** Often run before committing code or as part of a CI/CD pipeline. |

---

#### **Code Example: A Tale of Two Tests**

Let's imagine a simple `UserService` and its `UserRepository`.

**The Components:**

```java
// UserRepository.java - JPA Interface
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByUsername(String username);
}

// UserService.java - The class we want to test
@Service
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    public User findByUsername(String username) {
        return userRepository.findByUsername(username)
                .orElseThrow(() -> new UserNotFoundException("User not found"));
    }
}
```

**1. The Unit Test (`UserServiceTest.java`)**

Here, we only care about the `UserService`'s logic. We **do not** care about the database. We will use Mockito to mock the `UserRepository`.

```java
// No Spring annotations needed here!
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.Optional;

import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.Mockito.when;

@ExtendWith(MockitoExtension.class) // Enables Mockito
class UserServiceTest {

    @Mock // Creates a mock implementation of UserRepository
    private UserRepository userRepository;

    @InjectMocks // Creates an instance of UserService and injects the mocks into it
    private UserService userService;

    @Test
    void whenUserExists_findByUsername_returnsUser() {
        // 1. Arrange: Define the behavior of our mock
        User expectedUser = new User(1L, "testuser");
        when(userRepository.findByUsername("testuser")).thenReturn(Optional.of(expectedUser));

        // 2. Act: Call the method we are testing
        User actualUser = userService.findByUsername("testuser");

        // 3. Assert: Verify the result
        assertThat(actualUser.getUsername()).isEqualTo("testuser");
    }

    @Test
    void whenUserDoesNotExist_findByUsername_throwsException() {
        // 1. Arrange: Define the behavior of our mock
        when(userRepository.findByUsername("nonexistent")).thenReturn(Optional.empty());

        // 2. Act & 3. Assert: Check if the correct exception is thrown
        assertThrows(UserNotFoundException.class, () -> {
            userService.findByUsername("nonexistent");
        });
    }
}
```

**Key Takeaways from the Unit Test:**
*   **No Spring involvement:** We don't see `@SpringBootTest` or any other Spring-specific annotation. This is a pure JUnit 5 and Mockito test.
*   **Isolation:** The test is completely independent of the database. We explicitly told our `userRepository` mock how to behave.
*   **Speed:** This test runs in a few milliseconds.

**2. The Integration Test (`UserServiceIntegrationTest.java`)**

Here, we want to verify that `UserService` and `UserRepository` work together correctly with a **real database**. We'll use `@DataJpaTest`, which is a specialized integration test slice for the persistence layer.

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;
import org.springframework.context.annotation.Import;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest // <-- This is the key annotation
@Import(UserService.class) // We need to tell Spring to load our UserService bean
class UserServiceIntegrationTest {

    @Autowired
    private TestEntityManager entityManager; // A helper for JPA tests

    @Autowired
    private UserService userService;

    @Autowired
    private UserRepository userRepository;

    @Test
    void whenUserExists_findByUsername_returnsUser() {
        // 1. Arrange: Save a real user to the test database
        User userToSave = new User(null, "realuser");
        entityManager.persist(userToSave);
        entityManager.flush(); // Persist data to the DB

        // 2. Act: Call the service, which will hit the real test database
        User foundUser = userService.findByUsername("realuser");

        // 3. Assert: Verify the result came from the database
        assertThat(foundUser.getUsername()).isEqualTo("realuser");
    }
}
```

**Key Takeaways from the Integration Test:**
*   **Spring is here:** `@DataJpaTest` is a powerful annotation that configures an in-memory database (like H2) by default, scans for your `@Entity` classes and JPA repositories, and configures other necessary beans for persistence testing.
*   **Real Dependencies:** We are not mocking the `UserRepository`. We are `@Autowired`ing the *real* repository bean that Spring created. The test verifies the collaboration between the service and the persistence layer.
*   **Slower Execution:** This test takes longer because Spring has to:
    *   Start up an `ApplicationContext`.
    *   Configure an in-memory database.
    *   Perform schema creation.
    *   Execute a real SQL `INSERT` and `SELECT` statement.
*   **Test Isolation:** By default, each test method annotated with `@DataJpaTest` runs within its own transaction, which is rolled back at the end of the test. This ensures that data from one test does not leak into another, providing excellent test isolation.

---

### **Interview-Level Questions**

1.  **Question:** "You have a service method with complex business logic that calls a repository. How would you test it, and why would you choose that approach?"
    *   **Answer:** "I would use two types of tests. First, I'd write **unit tests** for the service class, mocking the repository. This allows me to test all the complex business logic, including edge cases and error conditions, very quickly and in isolation. Second, I would write at least one **integration test** using `@DataJpaTest` or a similar approach to verify that the service correctly interacts with the repository and that my JPQL or native queries in the repository are syntactically correct and return the expected data. This two-pronged approach ensures both logical correctness and successful integration."

2.  **Question:** "When would you choose to write an integration test instead of a unit test?"
    *   **Answer:** "I'd choose an integration test when the primary goal is to verify the collaboration between components, not the internal logic of a single component. This is crucial for:
        *   **Database Interactions:** Ensuring JPA entities are mapped correctly and queries work as expected.
        *   **API Contracts:** Verifying that a REST controller correctly serializes and deserializes JSON payloads and interacts with the service layer.
        *   **External Systems:** Testing the interaction with external services, message queues, or caches.
            Unit tests are insufficient for these scenarios because mocking these interactions wouldn't prove that the actual integration works."

3.  **Question:** "What, in your opinion, is the biggest mistake developers make when it comes to unit vs. integration testing?"
    *   **Answer:** "A common mistake is writing slow, complex integration tests for logic that should have been covered by simple unit tests. For example, using `@SpringBootTest` to test a simple calculation inside a service. This unnecessarily slows down the build and feedback cycle. The goal should be to have many fast unit tests covering all logical branches and fewer, more focused integration tests to verify the critical connection points between application layers."

---

### **Practical Exercise**

1.  **Setup:** Create a new Spring Boot project with the `Web`, `JPA`, `H2 Database`, and `Lombok` starters.
2.  **Create an Entity:** Define a `Product` entity with `id` (Long), `name` (String), and `price` (double).
3.  **Create a Repository:** Create a `ProductRepository` interface that extends `JpaRepository<Product, Long>`.
4.  **Create a Service:** Create a `ProductService` with a method `applyDiscount(Long productId, double discountPercentage)` that finds a product by its ID, calculates the discounted price, updates the product, and returns it.
5.  **Your Task:**
    *   Write a **unit test** for `ProductService`. Mock the `ProductRepository`. Test the discounting logic specifically. What happens if the discount is 100%? What if it's negative (it should throw an exception)?
    *   Write an **integration test** for `ProductService`. Use `@DataJpaTest`. In your test, save a real product to the database, call the `applyDiscount` method, and then use the repository to fetch the product again and assert that its price was correctly updated in the database.

This exercise will solidify your understanding of the boundary between these two crucial testing types.

***

Next, we will dive deeper into how the Spring `ApplicationContext` is loaded in tests and the importance of test isolation.

***

#### **Topic 1.2: How the Spring Application Context Loads in Integration Tests**

#### **Theory: The Magic Behind the Scenes**

In a regular Spring Boot application, the `main` method triggers the creation of an `ApplicationContext`. This context is responsible for creating and wiring all your beans (`@Component`, `@Service`, `@Repository`, etc.).

In an integration test, you don't have a `main` method. So how does the context get created? This is handled by the **Spring TestContext Framework**.

When you run a test class annotated with something like `@SpringBootTest` or `@DataJpaTest`, JUnit 5 (via the `@ExtendWith(SpringExtension.class)` which is included automatically in Spring Boot's test annotations) hands control over to the Spring TestContext Framework.

This framework is responsible for:
1.  **Reading Test Configuration:** It inspects the annotations on your test class (`@SpringBootTest`, `@ActiveProfiles`, `@MockBean`, etc.) to understand what kind of context you need.
2.  **Loading the `ApplicationContext`:** It builds and starts an `ApplicationContext` based on that configuration.
3.  **Injecting Dependencies:** It injects beans from the loaded context into your test class fields using `@Autowired`.
4.  **Managing Transactions:** It can start and roll back transactions for each test method.
5.  **Caching the Context:** This is the most important feature for performance. The framework intelligently **caches** the `ApplicationContext` and reuses it across different test classes.

#### **The All-Important Context Cache**

Loading a Spring `ApplicationContext` is an expensive operation. It can involve classpath scanning, bean instantiation, and dependency injection, which can take several seconds. If Spring had to do this for every single test *class*, a suite of hundreds of tests would be painfully slow.

To solve this, the Spring TestContext Framework maintains a **static cache of contexts**.

**How it works:**
1.  When the first test class runs, the framework creates a unique **key** based on its configuration (the annotations, properties, profiles, etc.).
2.  It builds the `ApplicationContext` and stores it in the cache with this key.
3.  When the next test class runs, the framework generates a key for it as well.
4.  It checks the cache:
    *   If a context with an **identical key** already exists, it is **reused**. The startup cost is skipped entirely.
    *   If the key is different, a **new `ApplicationContext` is created** and added to the cache.

**What makes a context's configuration key unique?**
Any change to the following (and more) will result in a new context being created:

*   **The annotations used:** A test with `@WebMvcTest` has a different key than one with `@SpringBootTest`.
*   **The `classes` attribute:** `@SpringBootTest(classes = MyConfig.class)` is different from `@SpringBootTest(classes = AnotherConfig.class)`.
*   **Active profiles:** `@ActiveProfiles("test")` is different from `@ActiveProfiles("dev")`.
*   **Property overrides:** `@TestPropertySource(properties = "my.prop=value1")` is different from `@TestPropertySource(properties = "my.prop=value2")`.
*   **The set of `@MockBean` or `@SpyBean` definitions:** A test class mocking `ServiceA` will not share a context with a test class mocking `ServiceB`.

**Visualizing the Cache:**

```
Test Run Starts...

TestClassA.java (@SpringBootTest, @ActiveProfiles("test"))
  -> No context found in cache for this configuration.
  -> CREATING NEW CONTEXT (takes 5 seconds)...
  -> Storing context in cache with key [SpringBootTest, profiles={'test'}, ...].
  -> Running tests in TestClassA...

TestClassB.java (@SpringBootTest, @ActiveProfiles("test"))
  -> Context found in cache for key [SpringBootTest, profiles={'test'}, ...].
  -> REUSING CACHED CONTEXT (takes 5 milliseconds).
  -> Running tests in TestClassB...

TestClassC.java (@SpringBootTest, @ActiveProfiles("other"))
  -> No context found in cache for this configuration.
  -> CREATING NEW CONTEXT (takes 5 seconds)...
  -> Storing context in cache with key [SpringBootTest, profiles={'other'}, ...].
  -> Running tests in TestClassC...

TestClassD.java (@DataJpaTest)
  -> No context found in cache for this configuration.
  -> CREATING NEW CONTEXT (takes 2 seconds)... (Faster because it's a slice)
  -> Storing context in cache with key [DataJpaTest, ...].
  -> Running tests in TestClassD...
```

---

#### **Topic 1.3: Test Isolation, Database State, and Rollback**

Even if the `ApplicationContext` is reused, the tests themselves must be **isolated** from each other. A test should not be affected by the state left behind by a previously run test.

This is especially critical for database state. If `TestA` creates a user named "john", `TestB` should not fail because it also tried to create a user named "john", resulting in a unique constraint violation.

Spring solves this with **transactional test support**.

**How it works:**

By default, test methods that use Spring's test annotations related to databases (like `@DataJpaTest` and even `@SpringBootTest`) are transactional.

*   **`@Transactional` on Test Methods:** Spring effectively wraps each test method in a transaction.
*   **Automatic Rollback:** At the end of the test method execution (whether it passes or fails), this transaction is **automatically rolled back**.

This means any database operations performed during the test (inserts, updates, deletes) are completely undone. The database state is reset to what it was before the test started, ensuring a clean slate for the next test.

**Example:**

```java
@DataJpaTest
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    void testA_saveUser() {
        userRepository.save(new User(null, "userA"));
        // At this point, "userA" is in the database within this transaction
        assertThat(userRepository.count()).isEqualTo(1);
    } // <-- Transaction for testA is ROLLED BACK here. "userA" is gone.

    @Test
    void testB_verifyNoUsers() {
        // This test starts with a clean slate because testA was rolled back.
        assertThat(userRepository.count()).isEqualTo(0);
    }
}
```

**Mocking Dependencies for Isolation**

Another key aspect of isolation is controlling dependencies. Sometimes you need to test how your component behaves when a dependency fails or returns specific data. This is where mocking comes in.

*   `@MockBean`: This annotation, provided by Spring Boot, lets you add a Mockito mock to the `ApplicationContext`. It will replace any existing bean of the same type with the mock.

This is a powerful tool for isolation:
*   You can prevent a test from making a real network call to an external service.
*   You can force a dependency to throw an exception to test your error handling.
*   You can provide specific data from a dependency without having to set it up in the database.

**Important Note:** Using `@MockBean` will change the context's configuration key, which will likely prevent the context from being reused. Use it when necessary, but be aware of the performance implication.

---

### **Interview-Level Questions**

1.  **Question:** "What is the primary performance optimization Spring uses when running integration tests, and how does it work?"
    *   **Answer:** "The primary optimization is the **`ApplicationContext` cache**. The Spring TestContext Framework creates a unique key based on the test's configuration (annotations, active profiles, properties, etc.). If a subsequent test class has an identical configuration, Spring reuses the already started context from the cache, which dramatically reduces test execution time by avoiding the expensive context startup process."

2.  **Question:** "You have two test classes. `TestA` runs successfully. When `TestB` runs, it fails with a database unique key violation. What is the most likely cause and how would you fix it?"
    *   **Answer:** "The most likely cause is a lack of test isolation. `TestA` probably committed data to the database that was not cleaned up before `TestB` started. The standard solution in Spring is to ensure transactional test management is enabled. Most Spring Boot test slices, like `@DataJpaTest`, do this by default. For a full `@SpringBootTest`, you can add `@Transactional` to the test class or individual methods. This ensures that any database changes made within a test are rolled back at its conclusion, providing a clean state for the next test."

3.  **Question:** "I have 10 test classes that all use `@SpringBootTest` and run very slowly. What is the first thing you would check to improve their performance?"
    *   **Answer:** "The first thing I would check is why the application context isn't being cached and reused. I would look for small differences in the configuration of each test class. Do they use different `@ActiveProfiles`? Do they declare different `@TestPropertySource` properties? Do they use `@MockBean` to mock different services? Each of these variations will cause Spring to create a new, expensive application context. The goal is to harmonize these configurations as much as possible across tests so the context can be reused, significantly speeding up the test suite."

---

### **Practical Exercise**

1.  **Objective:** See context caching and transactional rollbacks in action.
2.  **Setup:** Use the same project from the previous exercise (`Product` entity, repository, service).
3.  **Task:**
    *   Create a test class `ProductRepoTestA` annotated with `@DataJpaTest`.
    *   In this class, write a single test method `testSaveProduct()` that saves a new `Product` and asserts that the repository count is 1.
    *   Create a second test class `ProductRepoTestB` that is **identical** to the first (you can copy-paste it).
    *   Add logging to your test methods (e.g., `System.out.println("Running test in TestA")`).
    *   Run *all* tests in your project.
4.  **Observe:**
    *   **Context Loading:** You should see in the logs that the Spring context is loaded only **once**.
    *   **Test Isolation:** Both tests will pass, even though they both check for a count of 1. This demonstrates that the data saved in `TestA` was rolled back before `TestB` started.

This simple exercise will prove the two most fundamental concepts we've just discussed: context caching for speed and transactional rollback for isolation.

***

Next, we will take a deep dive into the most powerful and comprehensive test annotation: `@SpringBootTest`.

***

#### **Topic 1.4: Understanding `@SpringBootTest` and its parameters**

`@SpringBootTest` is the "heavyweight champion" of Spring Boot testing annotations. Its primary purpose is to load a full Spring `ApplicationContext` for an integration test. When you need to test the integration of multiple layers of your application together, this is often the tool you'll reach for.

#### **Theory: The Full Application Slice**

Unlike the "slice" test annotations we'll cover later (like `@WebMvcTest` or `@DataJpaTest`), `@SpringBootTest` doesn't make assumptions about what you want to test. By default, it loads **everything**.

It does this by searching for your main application class (the one annotated with `@SpringBootApplication`) and using its configuration to create an identical `ApplicationContext` for your test. This means all your services, repositories, components, and configurations will be loaded, just as they would be if you ran the application for real.

**When to use `@SpringBootTest`:**

*   When you need to test the interaction between multiple layers that are not covered by a specific slice test (e.g., testing a flow from a service layer to a caching layer and then to the database).
*   When you want to run an end-to-end test against a real running server.
*   When you are testing something that depends on the application's lifecycle, like `@Scheduled` tasks.
*   When you need to load a configuration that is not included in a slice test by default.

**When to AVOID `@SpringBootTest`:**

*   **For Unit Tests:** Never use `@SpringBootTest` to test the internal logic of a single method. This is the most common and costly mistake developers make. It's like using a sledgehammer to crack a nut.
*   **For Simple Web Layer Tests:** If you only need to test your controllers, request/response serialization, and validation, `@WebMvcTest` is far more efficient.
*   **For Simple JPA/Database Tests:** If you only need to test your repositories and JPA queries, `@DataJpaTest` is much faster.

---

#### **Key Parameters of `@SpringBootTest`**

The behavior of `@SpringBootTest` can be fine-tuned with its attributes. The most important one is `webEnvironment`.

##### **`webEnvironment`**

This attribute controls whether a real web server (like Tomcat or Netty) is started for your test. It has four possible values:

1.  **`WebEnvironment.MOCK` (The Default)**
    *   **What it does:** Loads a full Spring `ApplicationContext` but **does not** start a real HTTP server. Instead, it provides a "mock" servlet environment.
    *   **How to test:** You can inject and use `MockMvc` to fire requests at your controllers without any network overhead.
    *   **When to use it:** This is the most common setting. Use it when you want to test your controllers and business logic together in a full context, but you don't need to test against a real running server on a specific port. It's faster than starting a real server.

2.  **`WebEnvironment.RANDOM_PORT`**
    *   **What it does:** Loads a full `ApplicationContext` and starts a **real embedded web server** on a random, available port.
    *   **How to test:** You can use `TestRestTemplate` or `WebTestClient` to make actual HTTP calls to `http://localhost:<random_port>`. The port can be injected into your test.
    *   **When to use it:** For end-to-end tests where you want to verify the entire HTTP stack, from the client request to the database and back. This is the most comprehensive type of integration test.

3.  **`WebEnvironment.DEFINED_PORT`**
    *   **What it does:** The same as `RANDOM_PORT`, but it uses the port defined in your `application.properties` (e.g., `server.port=8080`).
    *   **When to use it:** Rarely. This can lead to port conflicts if your main application or another test is already running on that port. `RANDOM_PORT` is almost always a safer choice.

4.  **`WebEnvironment.NONE`**
    *   **What it does:** Loads a full `ApplicationContext` but explicitly does not provide any web environment (mock or real).
    *   **When to use it:** When your application is not a web application (e.g., a command-line utility or a message-driven microservice) and you don't want the overhead of setting up even a mock web context.

##### **Other Useful Parameters**

*   **`classes`**: This allows you to specify which configuration classes to load instead of letting Spring find your main `@SpringBootApplication`. This is a powerful way to limit the scope of your test and speed it up. For example, `@SpringBootTest(classes = {UserService.class, UserRepository.class, TestDatabaseConfig.class})` creates a custom, mini-application context just for this test.

*   **`properties`**: A convenient way to override properties from your `application.properties` file directly in the annotation.
    *   **Example:** `@SpringBootTest(properties = "spring.datasource.url=jdbc:h2:mem:testdb")`

---

#### **Code Example: `MOCK` vs. `RANDOM_PORT`**

Let's assume we have a `ProductController` that uses our `ProductService`.

**1. Testing with `webEnvironment = MOCK`**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;

// Default is MOCK, but we can be explicit.
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc // Enables and configures MockMvc
class ProductControllerMockEnvTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ProductRepository productRepository;

    @Test
    void whenProductExists_thenGetProductReturns200() throws Exception {
        // Arrange: Save a product directly to the repo
        productRepository.save(new Product(1L, "Test Book", 29.99));

        // Act & Assert
        mockMvc.perform(get("/products/1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.name").value("Test Book"));
    }
}
```

**2. Testing with `webEnvironment = RANDOM_PORT`**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.test.web.server.LocalServerPort; // Injected port
import org.springframework.http.ResponseEntity;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class ProductControllerRandomPortTest {

    @LocalServerPort // Injects the random port used by the server
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private ProductRepository productRepository;

    @Test
    void whenProductExists_thenGetProductReturns200() {
        // Arrange: Save a product
        productRepository.save(new Product(1L, "Test Book", 29.99));

        // Act: Make a real HTTP call
        String url = "http://localhost:" + port + "/products/1";
        ResponseEntity<Product> response = restTemplate.getForEntity(url, Product.class);

        // Assert
        assertThat(response.getStatusCode().is2xxSuccessful()).isTrue();
        assertThat(response.getBody().getName()).isEqualTo("Test Book");
    }
}
```

---

### **Interview-Level Questions**

1.  **Question:** "What is the difference between `@SpringBootTest(webEnvironment = WebEnvironment.MOCK)` and `@WebMvcTest`? They both use `MockMvc`."
    *   **Answer:** "The key difference is the scope of the `ApplicationContext`. `@WebMvcTest` loads only the web layer: controllers, JSON serializers, filters, and other web-related beans. It explicitly does *not* load the service or repository layers; you must mock them using `@MockBean`. In contrast, `@SpringBootTest(webEnvironment = MOCK)` loads the *entire* application context, including services, repositories, and the database configuration. You use `MockMvc` to interact with this fully wired application, but without the overhead of a real HTTP server."

2.  **Question:** "You are writing a test for a `@Scheduled` method that runs every hour. How would you test it, and which annotation would you use?"
    *   **Answer:** "A scheduled task is not part of the web or data layer, so a slice test isn't appropriate. I would use `@SpringBootTest` to load the full application context, which ensures the scheduling configuration (`@EnableScheduling`) is active and my `@Scheduled` bean is created. I would then mock the `Clock` or use a library like Awaitility to wait for the task to execute and verify its side effects, such as a database update or a call to a mocked service."

3.  **Question:** "When is it a good idea to use the `classes` attribute in `@SpringBootTest`?"
    *   **Answer:** "Using the `classes` attribute is an excellent optimization strategy. If you know you're only testing the integration between a specific service (`OrderService`) and its dependencies (`ProductService`, `OrderRepository`), you can specify `classes = {OrderService.class, ProductService.class, OrderRepository.class}`. This prevents Spring from scanning the entire classpath and loading unrelated components like web controllers or scheduled tasks, resulting in a much faster and more focused integration test while still using a real Spring context."

---

### **Practical Exercise**

1.  **Objective:** Write a full integration test using `@SpringBootTest`.
2.  **Setup:** Use the same `Product` project. Create a `ProductController` with a GET endpoint `/products/{id}` that fetches a product using the `ProductService`.
3.  **Task:**
    *   Create a new test class `ProductControllerIntegrationTest`.
    *   Annotate it with `@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)`.
    *   Inject `TestRestTemplate` and `ProductRepository`.
    *   Write a test that:
        1.  Saves a `Product` directly using the repository to set up the database state.
        2.  Constructs the URL using the injected random port.
        3.  Uses `restTemplate` to make a GET request to your endpoint.
        4.  Asserts that the HTTP status code is 200 OK and that the body of the response contains the correct product details.

This exercise will give you hands-on experience with the most comprehensive form of integration testing in Spring Boot.

***

Next, we will begin our deep dive into the various **Slice Test Annotations**, starting with `@WebMvcTest`.

