### **Part 5: Full Context Integration Testing**

So far, we have tested our application in isolated "slices." Now it's time to put it all together. Full context integration testing involves loading your *entire* Spring Boot application—from the web layer down to the database—and testing it as a complete, running system. This is the highest level of automated testing you will typically write and gives you the greatest confidence that all the pieces work together.

#### **Topic 5.1: Using `@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)`**

This is the cornerstone annotation for end-to-end (E2E) testing. Let's break down why:

*   **`@SpringBootTest`**: As we learned, this loads the complete `ApplicationContext`, just as if you ran your application's `main` method. All your controllers, services, repositories, and other components are created and wired together.
*   **`webEnvironment = WebEnvironment.RANDOM_PORT`**: This is the crucial part for E2E testing. It tells Spring to start a real, embedded web server (like Tomcat) and have it listen for HTTP requests on a randomly selected, available network port.

By using this combination, your test effectively runs a miniature, self-contained version of your production application. You can then use a real HTTP client to interact with it, simulating how a frontend application or another microservice would.

#### **Topic 5.2: Testing REST Endpoints with `TestRestTemplate` and `RestAssured`**

Since we have a real server running, we can't use `MockMvc`. We need a real HTTP client. Spring Boot provides `TestRestTemplate`, and a popular third-party alternative is `RestAssured`.

**1. Using `TestRestTemplate`**

This is a convenient wrapper around Apache's `HttpClient` that is specifically designed for integration tests. Spring Boot will automatically configure a `TestRestTemplate` bean for you to inject when you use `RANDOM_PORT`.

```java
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
class ProductControllerE2ETest extends AbstractIntegrationTest { // Reusing our DB config

    // Spring injects the random port the server started on
    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private ProductRepository productRepository;

    @Test
    void whenProductExists_GetRequest_ReturnsProductDetails() {
        // Arrange: Save data directly to the real database
        Product savedProduct = productRepository.save(new Product(null, "E2E Test Product", 199.99));

        // Act: Make a REAL HTTP call to our running application
        String url = "http://localhost:" + port + "/products/" + savedProduct.getId();
        ResponseEntity<ProductDto> response = restTemplate.getForEntity(url, ProductDto.class);

        // Assert
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(response.getBody()).isNotNull();
        assertThat(response.getBody().getName()).isEqualTo("E2E Test Product");
    }
}
```

**2. Using `RestAssured`**

RestAssured is a library that provides a fluent, BDD-style (Given/When/Then) syntax for writing REST API tests. It's often preferred for its high readability and powerful built-in assertion capabilities.

**Setup:** Add the RestAssured dependency to your `pom.xml`.

```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <scope>test</scope>
</dependency>
```

**Example Test:**

```java
import io.restassured.RestAssured;
import io.restassured.http.ContentType;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import static io.restassured.RestAssured.given;
import static org.hamcrest.Matchers.equalTo;

@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
class ProductControllerRestAssuredTest extends AbstractIntegrationTest {

    @LocalServerPort
    private int port;

    @Autowired
    private ProductRepository productRepository;

    // Configure RestAssured once before any tests
    @BeforeEach
    void setUp() {
        RestAssured.port = port;
        productRepository.deleteAll(); // Clean state
    }

    @Test
    void whenProductExists_GetRequest_ReturnsProductDetails() {
        // Arrange
        Product savedProduct = productRepository.save(new Product(null, "RestAssured Product", 150.00));

        // Act & Assert (using RestAssured's BDD syntax)
        given()
            .contentType(ContentType.JSON)
        .when()
            .get("/products/{id}", savedProduct.getId())
        .then()
            .statusCode(200)
            .body("name", equalTo("RestAssured Product"))
            .body("price", equalTo(150.00f)); // Note: JSON numbers are often floats
    }
}
```

**Comparison:**
*   `TestRestTemplate` is straightforward and integrated into Spring. It feels like regular Java code.
*   `RestAssured` offers a more expressive, domain-specific language for API testing which many find more readable. Its built-in `body()` assertions are often more concise than using `jsonPath`.

---

#### **Topic 5.3 & 5.4: Test Profiles (`application-test.yml`), `@ActiveProfiles`, and `@DirtiesContext`**

For full context tests, you almost always need a separate configuration from your main `application.yml`.

**1. Create `src/test/resources/application-test.yml`**

This file will be loaded *only* when the "test" profile is active. It's the perfect place to override properties for your test environment.

```yaml
# src/test/resources/application-test.yml

server:
  port: 0 # Use a random port, equivalent to RANDOM_PORT

# Example: disable a feature you don't want in tests
feature-flags:
  send-emails: false

# Configure logging specifically for tests
logging:
  level:
    com.yourapp: DEBUG
    org.springframework.transaction: TRACE

# Using Flyway? Let it manage the schema.
spring:
  jpa:
    hibernate:
      ddl-auto: validate # Let Flyway create, Hibernate just validates
  flyway:
    enabled: true

  # We don't need to put datasource URL/user/pass here because
  # our Testcontainers setup will override them dynamically.
```

**2. Activate the Profile with `@ActiveProfiles("test")`**

Add this annotation to your test class to tell Spring which profile to activate.

```java
@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)
@ActiveProfiles("test") // <-- Tells Spring to load application-test.yml
class ProductControllerE2ETest extends AbstractIntegrationTest {
    // ...
}
```

**3. Resetting State with `@DirtiesContext`**

The Spring TestContext Framework is smart and caches the `ApplicationContext` between test classes to speed things up. However, sometimes a test might do something that irrevocably changes the context, such as modifying a singleton bean's state or changing a property.

`@DirtiesContext` tells Spring that the application context has been "dirtied" and must be closed and recreated before the next test class runs.

*   `@DirtiesContext(classMode = ClassMode.AFTER_CLASS)`: Recreate the context after all tests in this class have run (most common).
*   `@DirtiesContext(methodMode = MethodMode.AFTER_METHOD)`: Recreate the context after *each method* in this class. **Use with extreme caution! This will make your tests incredibly slow.**

**When to use it:**
*   A test modifies a singleton bean's state in a way that can't be easily reset.
*   A test dynamically changes application properties.
*   You are testing something that relies on application startup logic (e.g., a `@PostConstruct` method) and need a fresh start for the next test class.

**Rule of Thumb:** Avoid `@DirtiesContext` if you can. It's a performance killer and often indicates that your tests are not properly isolated. Prefer cleaning your database state in a `@BeforeEach` or `@AfterEach` method.

---

#### **Topic 5.5: Initializing Data**

For E2E tests, you need a reliable way to get your database into a known state.

1.  **Flyway/Liquibase (Best Practice):** As covered in the JPA section, this is the most robust method. Your tests run against a schema that is identical to production. You can also use Flyway's "repeatable" migrations to populate common reference data (e.g., a list of countries or user roles).

2.  **`data.sql`:** If you place a `data.sql` file in `src/test/resources`, Spring Boot will execute it after the schema has been created. This is a simple way to insert seed data.
    *   **Pros:** Very simple.
    *   **Cons:** Can become hard to manage. It runs for *all* tests, which may not be what you want. Programmatic setup is often more flexible.

3.  **Programmatic Setup (Most Flexible):** Using a `@BeforeEach` method to call your repositories and set up the exact state needed for your tests.
    *   **Pros:**
        *   Each test can set up only the data it needs.
        *   It's pure Java, so it's typesafe and easy to refactor.
        *   You can use object factories or Test Data Builders to create complex object graphs easily.
    *   **Cons:** Can be slightly more verbose for simple data.

**Recommendation:** Use Flyway to manage your schema. Use a programmatic `@BeforeEach` method to manage the data specific to each test method, ensuring you `deleteAll()` first to maintain isolation.

---

#### **Topic 5.6: Handling Issues and Best Practices for Performance**

*   **Port Conflicts:** Always use `WebEnvironment.RANDOM_PORT` or `server.port=0` in your test profile to avoid them.
*   **Startup Delays & Async Operations:** If your application does work in the background, your test might finish before the work is done. Use a library like **Awaitility** to poll for a result within a timeout (e.g., `await().atMost(5, SECONDS).until(() -> repository.count() == 1);`).
*   **Context Caching is Key:** The biggest performance gain comes from reusing the `ApplicationContext`.
    *   **Harmonize your test configurations.** Try to use the same `@ActiveProfiles` and avoid `@MockBean` in E2E tests if possible.
    *   **Use the singleton container pattern** for Testcontainers.
    *   **Avoid `@DirtiesContext`** unless absolutely necessary.

***

Next, we will formalize our understanding of **Test Profiles and Configuration**, looking at property overrides and managing environment-specific settings in more detail.

