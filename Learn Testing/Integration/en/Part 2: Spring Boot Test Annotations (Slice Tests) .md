### **Part 2: Spring Boot Test Annotations (Slice Tests)**

Before we dive into the specific annotations, let's understand the core idea behind them.

#### **What is a "Test Slice"?**

Think of your Spring Boot application as a layered cake.

*   The bottom layer is the **Persistence Layer** (JPA, Repositories).
*   The middle layer is the **Service/Business Logic Layer** (`@Service` beans).
*   The top layer is the **Web/Presentation Layer** (`@RestController`, `@Controller`).

A **slice test** focuses on testing just *one* of these layers (or "slices") in isolation. It loads a very limited, targeted `ApplicationContext` containing only the beans necessary for that specific slice.

**Why use slice tests?**

1.  **Speed:** Loading a small, targeted context is significantly faster than loading the entire application with `@SpringBootTest`.
2.  **Focus:** It forces you to think about the specific responsibilities of the layer you are testing and to properly mock its dependencies. This leads to better-designed, more decoupled tests.
3.  **Simplicity:** The annotations auto-configure everything you need for that slice, so you don't have to do complex manual setup.

Now, let's explore the most common and useful slice test annotation.

---

### **Topic 2.1: `@WebMvcTest`**

#### **What part of the application context it loads?**

`@WebMvcTest` is designed to test the **web layer** of your application and nothing else. When you use it, Spring Boot will auto-configure only the components relevant to Spring MVC.

**What gets loaded:**
*   `@Controller`, `@RestController`, `@ControllerAdvice` beans.
*   `@JsonComponent`, `Converter`, `GenericConverter` beans.
*   `Filter`, `WebMvcConfigurer`, `HandlerMethodArgumentResolver` beans.
*   Key infrastructure beans like `ObjectMapper` for JSON processing.

**What is NOT loaded:**
*   `@Service`, `@Repository`, `@Component` or any other regular bean.
*   The full security configuration (though some security infrastructure is present).
*   The data source and the entire persistence layer.

#### **When and why to use it?**

Use `@WebMvcTest` when your primary goal is to verify the behavior of your Spring MVC controllers. Specifically:

*   **Request Mapping:** Is my controller listening on the correct URL and HTTP method?
*   **Request Deserialization:** Can my controller correctly parse incoming JSON/XML into a DTO?
*   **Validation:** Are my `@Valid` annotations and validation rules being triggered correctly for invalid input?
*   **Service Layer Interaction:** Does my controller call the correct method on its service dependency?
*   **Response Serialization:** Does my controller correctly serialize the response object into JSON?
*   **HTTP Status Codes:** Does my endpoint return the correct status codes (200 OK, 201 Created, 400 Bad Request, 404 Not Found)?
*   **Exception Handling:** Does my `@ControllerAdvice` correctly handle exceptions thrown by the controller and return a proper error response?

#### **What dependencies are automatically configured?**

The star of the show with `@WebMvcTest` is **`MockMvc`**. Spring Boot will automatically configure a `MockMvc` bean in your test context, which you can `@Autowired` and use to drive your tests. `MockMvc` allows you to perform requests against your controllers without a running server, making the tests fast and reliable.

#### **Common pitfalls and limitations.**

1.  **Forgetting to Mock Dependencies:** This is the #1 mistake. Since `@Service` and `@Repository` beans are not loaded, if your controller depends on a service, you **must** provide a mock for it using `@MockBean`. If you forget, your test will fail with a `NoSuchBeanDefinitionException` when Spring tries to inject the service into the controller.

2.  **Testing Business Logic:** `@WebMvcTest` is not for testing the business logic inside your service. Your test should mock the service, define its behavior (e.g., "when `productService.findById(1L)` is called, then return this product"), and verify that the controller behaves correctly based on that predefined behavior.

3.  **Full Security Rules:** By default, `@WebMvcTest` will also auto-configure Spring Security. This means if you have security rules, your `MockMvc` requests might fail with a 401 Unauthorized or 403 Forbidden error. You often need to explicitly disable security in the annotation (`@WebMvcTest(excludeAutoConfiguration = SecurityAutoConfiguration.class)`) or use annotations like `@WithMockUser` to simulate an authenticated user.

#### **Example test code and expected behavior.**

Let's test a `ProductController`.

**The Controller:**
```java
@RestController
@RequestMapping("/products")
public class ProductController {

    private final ProductService productService;

    public ProductController(ProductService productService) {
        this.productService = productService;
    }

    @GetMapping("/{id}")
    public ResponseEntity<Product> getProductById(@PathVariable Long id) {
        Product product = productService.findById(id);
        return ResponseEntity.ok(product);
    }
}
```
**The `@WebMvcTest`:**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.BDDMockito.given;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.hamcrest.CoreMatchers.is;

// Target only the ProductController for this test
@WebMvcTest(ProductController.class)
class ProductControllerWebMvcTest {

    @Autowired
    private MockMvc mockMvc; // The auto-configured entry point for our tests

    @MockBean // Creates a mock of ProductService and adds it to the context
    private ProductService productService;

    @Test
    void whenProductExists_getProductById_returnsProductJsonAnd200() throws Exception {
        // 1. Arrange: Define the mock's behavior
        Product product = new Product(1L, "Gaming Keyboard", 99.99);
        given(productService.findById(1L)).willReturn(product);

        // 2. Act & 3. Assert: Perform the request and verify the response
        mockMvc.perform(get("/products/{id}", 1L))
                .andExpect(status().isOk())
                .andExpect(content().contentType("application/json"))
                .andExpect(jsonPath("$.id", is(1)))
                .andExpect(jsonPath("$.name", is("Gaming Keyboard")))
                .andExpect(jsonPath("$.price", is(99.99)));
    }

    @Test
    void whenProductDoesNotExist_getProductById_returns404() throws Exception {
        // 1. Arrange: Define mock behavior for a non-existent product
        given(productService.findById(99L)).willThrow(new ProductNotFoundException("Product not found"));

        // 2. Act & 3. Assert
        mockMvc.perform(get("/products/{id}", 99L))
                .andExpect(status().isNotFound());
    }
}
```

**Expected Behavior:**
*   The test loads a context with only `ProductController` and the MVC infrastructure.
*   The `productService` field in the real `ProductController` instance is injected with our Mockito mock.
*   The first test succeeds because we told the mock to return a product, and we assert that the controller correctly serializes this product to JSON and returns a 200 OK status.
*   The second test succeeds because we told the mock to throw an exception, and we assert that the controller (or a `@ControllerAdvice`) correctly translates this to a 404 Not Found status.

---

### **Interview-Level Questions**

1.  **Question:** "You've written a `@WebMvcTest` but it's failing because Spring can't find a bean for your `UserService`. What did you forget?"
    *   **Answer:** "I forgot to declare the `UserService` as a `@MockBean`. `@WebMvcTest` only loads the web layer components and does not load `@Service` beans. To satisfy the dependency injection for the controller under test, I must provide a mock implementation of the service using `@MockBean`."

2.  **Question:** "Why would you choose `@WebMvcTest` over `@SpringBootTest` for testing a controller that returns a simple JSON object?"
    *   **Answer:** "For performance and focus. `@WebMvcTest` is significantly faster because it avoids loading the entire application context, including the service and data layers. It forces a clean separation of concerns, where I can test the controller's responsibilities—like request mapping, data binding, and serialization—in complete isolation from the business logic, which should be tested at the unit level. Using `@SpringBootTest` for this would be unnecessarily slow and complex."

3.  **Question:** "How would you test a file upload endpoint using `@WebMvcTest`?"
    *   **Answer:** "`MockMvc` fully supports testing file uploads. You would use the `MockMvcRequestBuilders.multipart()` method. This allows you to construct a multipart request, add files to it using `file()`, and also add other request parameters. You can then perform this request and assert the response status and body just like any other endpoint."

---

### **Practical Exercise**

1.  **Objective:** Use `@WebMvcTest` to test validation rules.
2.  **Setup:**
    *   In your project, create a `ProductCreateDTO` class.
    *   Add fields `name` (String) and `price` (Double).
    *   Annotate `name` with `@NotBlank` (must not be null or empty).
    *   Annotate `price` with `@Positive` (must be greater than zero).
    *   Create a `POST /products` endpoint in your `ProductController` that accepts this DTO with the `@Valid` annotation.
3.  **Your Task:**
    *   Write a new test method in your `@WebMvcTest` class.
    *   **Test Case 1 (Invalid Name):** Use `MockMvc` to perform a POST request to `/products` with a JSON body where the `name` is an empty string (`""`). Assert that the HTTP status code is **400 Bad Request**.
    *   **Test Case 2 (Invalid Price):** Perform another POST request where the `price` is `-10`. Assert that the status is **400 Bad Request**.
    *   **Test Case 3 (Valid Input):** Perform a POST request with a valid name and price. You'll need to mock the service layer call (e.g., `productService.createProduct(...)`). Assert that the status is **201 Created**.

This exercise will teach you how to use `MockMvc` to test controller inputs and validation, a critical part of API testing.

***

Next, we will move down the stack to the persistence layer and explore `@DataJpaTest`.

***

#### **Topic 2.2: `@DataJpaTest`**

#### **What part of the application context it loads?**

`@DataJpaTest` is the slice test annotation for the **persistence layer**. It focuses exclusively on testing JPA components. It's designed to be the fastest and easiest way to verify that your repositories, entities, and queries work correctly.

**What gets loaded:**
*   `@Repository` beans (specifically, your interfaces extending `JpaRepository` or other Spring Data repository interfaces).
*   Your `@Entity` classes (it will scan for them).
*   Core JPA infrastructure beans like `EntityManagerFactory` and `TransactionManager`.
*   A `DataSource` (typically configured for an in-memory database).
*   A special bean called `TestEntityManager`, which is a helpful utility for persistence tests.

**What is NOT loaded:**
*   The web layer: `@Controller`, `@RestController`, `@ControllerAdvice`.
*   The service layer and other general components: `@Service`, `@Component`.
*   The full security configuration.

#### **When and why to use it?**

Use `@DataJpaTest` whenever you need to test anything related to your database persistence logic. It's the ideal tool for verifying:

*   **Repository CRUD Operations:** Do `save()`, `findById()`, `delete()` work as expected?
*   **Custom Queries:** Are your `@Query` annotations (using JPQL or native SQL) syntactically correct and do they return the expected results?
*   **Entity Mappings:** Are your `@OneToMany`, `@ManyToOne`, etc., relationships configured correctly? Do cascading operations work?
*   **Database Constraints:** Does saving an entity with a non-unique value for a unique field correctly throw a `DataIntegrityViolationException`?
*   **Optimistic Locking:** Does your `@Version` annotation prevent dirty reads?

By focusing only on the persistence layer, `@DataJpaTest` gives you a fast and reliable way to confirm that your data access layer is behaving exactly as you intend, without the overhead of the service or web layers.

#### **What dependencies are automatically configured?**

1.  **In-Memory Database:** By default, `@DataJpaTest` will try to configure and connect to an in-memory database like **H2, HSQLDB, or Derby** if they are on the classpath. It automatically generates the database schema based on your JPA entities. This is extremely convenient as it requires zero configuration.

2.  **`TestEntityManager`:** This is a fantastic utility provided by Spring Boot specifically for JPA tests. It's a wrapper around the standard `EntityManager` with methods that are simplified for a testing context. It's perfect for the "Arrange" phase of your test, where you need to set up the database state.
    *   `persist(entity)`: Adds a new entity to the persistence context.
    *   `flush()`: Synchronizes the persistence context with the database (i.e., executes the `INSERT`, `UPDATE`, `DELETE` SQL statements). This is crucial because Hibernate often batches changes, and `flush()` forces them to be sent to the DB so you can query them.
    *   `find(id)`: Retrieves an entity from the database.
    *   `persistAndFlush(entity)`: A handy shortcut for the two most common operations.

3.  **Transactional Rollback:** As discussed in the Foundations section, each test method in a `@DataJpaTest` class is wrapped in a transaction that is **rolled back** at the end. This provides perfect test isolation, ensuring that the database is clean before the next test method runs.

#### **Common pitfalls and limitations.**

1.  **In-Memory vs. Real Database Dialect:** An H2 database might behave slightly differently from your production database (e.g., PostgreSQL or MySQL). A native query that works in H2 might fail in PostgreSQL due to subtle differences in SQL syntax or function names. This is the biggest limitation of the default setup. (We will solve this later with Testcontainers).

2.  **Forgetting to `flush()`:** A very common mistake is to save an entity with `TestEntityManager.persist()` and then immediately try to find it with a repository query. The query might fail because the `INSERT` statement hasn't actually been executed against the database yet. You must call `entityManager.flush()` to force the changes to be written.

3.  **Testing Services:** Do not use `@DataJpaTest` to test your service layer logic. If your service has transactional logic (`@Transactional`), its behavior might differ from the test's transactional behavior. If you need to test a service's interaction with a repository, it's often better to write a separate integration test using `@SpringBootTest` or by simply importing the service into your `@DataJpaTest` with `@Import(MyService.class)`.

#### **Example test code and expected behavior.**

Let's test a `ProductRepository` that has a custom query.

**The Repository:**
```java
public interface ProductRepository extends JpaRepository<Product, Long> {
    // Custom JPQL query
    List<Product> findByPriceGreaterThan(double price);
}
```

**The `@DataJpaTest`:**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
class ProductRepositoryTest {

    @Autowired
    private TestEntityManager entityManager; // Helper for persisting data

    @Autowired
    private ProductRepository productRepository; // The repository we are testing

    @Test
    void whenSaved_findById_returnsProduct() {
        // 1. Arrange
        Product product = new Product(null, "Test Product", 10.0);
        entityManager.persistAndFlush(product); // Save to DB and force sync

        // 2. Act
        Product found = productRepository.findById(product.getId()).orElse(null);

        // 3. Assert
        assertThat(found).isNotNull();
        assertThat(found.getName()).isEqualTo("Test Product");
    }

    @Test
    void whenFindByPriceGreaterThan_returnsMatchingProducts() {
        // 1. Arrange
        entityManager.persistAndFlush(new Product(null, "Cheap Mouse", 15.0));
        entityManager.persistAndFlush(new Product(null, "Good Monitor", 250.0));
        entityManager.persistAndFlush(new Product(null, "Expensive CPU", 500.0));

        // 2. Act
        List<Product> expensiveProducts = productRepository.findByPriceGreaterThan(200.0);

        // 3. Assert
        assertThat(expensiveProducts).hasSize(2);
        assertThat(expensiveProducts).extracting(Product::getName)
                                     .containsExactlyInAnyOrder("Good Monitor", "Expensive CPU");
    }
}
```

**Expected Behavior:**
*   Spring Boot detects H2 on the classpath and starts a new, empty in-memory database for this test class.
*   It scans for our `Product` entity and creates the corresponding `PRODUCT` table.
*   For each test method:
    *   A new transaction begins.
    *   The `entityManager` saves data directly to the database.
    *   The `productRepository` is called, which executes real SQL against the in-memory database.
    *   The assertions pass.
    *   The transaction is rolled back, wiping all the data that was inserted.
*   The next test method starts with a completely empty database.

---

### **Interview-Level Questions**

1.  **Question:** "What is `TestEntityManager` and why is it useful?"
    *   **Answer:** "`TestEntityManager` is a utility class provided by Spring Boot for JPA tests. It's a wrapper around the standard `EntityManager` but with methods simplified for testing. It's primarily used in the 'arrange' phase of a test to set up the database state by persisting entities. Its `flush()` method is critical for ensuring that any changes in the persistence context are written to the database before the 'act' phase of the test begins."

2.  **Question:** "What are the pros and cons of using the default H2 in-memory database with `@DataJpaTest`?"
    *   **Answer:** "The main **pro** is speed and convenience. It requires zero configuration and runs very fast, making it excellent for quick feedback during development. The main **con** is that H2 is not a perfect substitute for a production database like PostgreSQL or Oracle. There can be subtle differences in SQL dialects, function support, and constraint handling, which means a test passing on H2 might still fail in production. This risk can be mitigated by using Testcontainers."

3.  **Question:** "My `@DataJpaTest` is failing with a `LazyInitializationException`. What's happening and how could I fix it?"
    *   **Answer:** "`LazyInitializationException` occurs when you try to access a lazily-loaded collection or relationship on an entity after the Hibernate session it was loaded in has been closed. In a `@DataJpaTest`, the session is tied to the `@Transactional` test method. The exception likely happens if you return an entity from a repository call and then try to access a lazy field *outside* of that transactional scope or after the session is flushed and cleared. To fix this, you can either change the fetch type to `EAGER` (not always recommended), use a `JOIN FETCH` in your query to load the data explicitly, or perform the assertion that touches the lazy field within the same test method, ensuring the transaction is still active."

***

Next, we will look at two more specialized slice test annotations: `@JsonTest` and `@RestClientTest`.

***

#### **Topic 2.3: `@JsonTest`**

#### **What part of the application context it loads?**

`@JsonTest` is a highly specialized and lightweight slice test annotation. Its sole purpose is to test the **JSON serialization and deserialization** of your objects. It is perfect for verifying that your DTOs or Entities are converted to and from JSON exactly as you expect.

**What gets loaded:**
*   An `ObjectMapper` (Jackson) or `Gson` bean, depending on your configuration. This is the core engine for JSON processing.
*   Any `@JsonComponent` beans you have defined. These are custom serializers and deserializers that `@JsonTest` will pick up and register with the `ObjectMapper`.
*   JSON testing utilities like `JacksonTester`, `GsonTester`, or `JsonbTester`.

**What is NOT loaded:**
*   Pretty much everything else. No controllers, no services, no repositories, no database. This makes `@JsonTest` incredibly fast.

#### **When and why to use it?**

You should use `@JsonTest` when you need to verify the exact structure of your JSON. This is particularly useful when:

*   You have custom serialization logic (e.g., formatting dates in a specific way like `yyyy-MM-dd`).
*   You are using Jackson annotations like `@JsonFormat`, `@JsonProperty`, `@JsonIgnore`, or `@JsonView`.
*   You want to ensure your API contract is stable and that changes to a DTO don't accidentally break the JSON format expected by clients.
*   You have written a custom `@JsonComponent` (e.g., a custom serializer for a complex object) and want to unit test it in isolation.

It allows you to test the JSON mapping layer without the overhead of the web layer (`@WebMvcTest`) or the full application (`@SpringBootTest`).

#### **What dependencies are automatically configured?**

Spring Boot auto-configures a tester object based on your JSON mapping library. The most common is **`JacksonTester<T>`**. This is a wrapper that provides convenient assertion methods for testing JSON.

*   `jacksonTester.write(object)`: Serializes an object to JSON.
*   `jacksonTester.parse(jsonString)`: Deserializes a JSON string into an object.
*   It integrates seamlessly with assertion libraries like AssertJ, allowing you to check specific JSON paths.

#### **Common pitfalls and limitations.**

1.  **Only Testing One Way:** A common mistake is to only test serialization (object to JSON) but forget to test deserialization (JSON to object). A field might serialize correctly but fail to deserialize if it's read-only or lacks a proper setter. Always test both directions.
2.  **Testing the Wrong Thing:** Do not use `@JsonTest` to test controller logic. It knows nothing about HTTP. Its only job is to test the conversion of a single object.
3.  **Assuming Default `ObjectMapper`:** If your application has a highly customized `ObjectMapper` bean (e.g., with many custom modules registered), `@JsonTest` might not pick up that exact configuration unless it's configured via standard properties or `@JsonComponent`s.

#### **Example test code and expected behavior.**

Let's imagine a `ProductDto` where we want to control the date format.

**The DTO:**
```java
public class ProductDto {
    private String name;
    private double price;

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd'T'HH:mm:ssZ")
    private Date creationDate;

    // Constructors, getters, setters...
}
```

**The `@JsonTest`:**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.json.JsonTest;
import org.springframework.boot.test.json.JacksonTester;
import org.springframework.boot.test.json.JsonContent;

import java.util.Date;
import java.text.SimpleDateFormat;

import static org.assertj.core.api.Assertions.assertThat;

@JsonTest
class ProductDtoJsonTest {

    @Autowired
    private JacksonTester<ProductDto> json; // The auto-configured tester

    @Test
    void testSerialization() throws Exception {
        // 1. Arrange
        String expectedDate = "2025-10-26T10:00:00+0000";
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        Date date = sdf.parse(expectedDate);
        ProductDto dto = new ProductDto("My Product", 123.45, date);

        // 2. Act
        JsonContent<ProductDto> result = json.write(dto);

        // 3. Assert
        assertThat(result).extractingJsonPathStringValue("$.name").isEqualTo("My Product");
        assertThat(result).extractingJsonPathNumberValue("$.price").isEqualTo(123.45);
        assertThat(result).extractingJsonPathStringValue("$.creationDate").isEqualTo(expectedDate);
    }

    @Test
    void testDeserialization() throws Exception {
        // 1. Arrange
        String jsonContent = "{\"name\":\"My Product\", \"price\":123.45, \"creationDate\":\"2025-10-26T10:00:00+0000\"}";

        // 2. Act
        ProductDto result = json.parse(jsonContent).getObject();

        // 3. Assert
        assertThat(result.getName()).isEqualTo("My Product");
        assertThat(result.getPrice()).isEqualTo(123.45);

        // Verify the date was parsed correctly
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        assertThat(sdf.format(result.getCreationDate())).isEqualTo("2025-10-26T10:00:00+0000");
    }
}
```
---

### **Topic 2.4: `@RestClientTest`**

#### **What part of the application context it loads?**

`@RestClientTest` is the slice test for the **client side** of a REST communication. It's used for testing code that *calls* an external REST service, typically using `RestTemplate` or the newer `RestClient`.

**What gets loaded:**
*   An `ObjectMapper` for handling JSON.
*   Support for `RestTemplateBuilder` and `RestClient.Builder`.
*   A special mock server (`MockRestServiceServer`) that intercepts outgoing requests.
*   Your `@RestClient`-annotated interfaces (if using the declarative client).

**What is NOT loaded:**
*   Your web layer (`@RestController`, etc.). You are not testing *your* server, you are testing your *client*.
*   The service and repository layers.

#### **When and why to use it?**

Use `@RestClientTest` when you have a service or component in your application that needs to communicate with a third-party REST API. This allows you to:

*   Test your REST client in complete isolation without making actual network calls.
*   Verify that your client sends requests to the correct URL with the correct HTTP method and headers.
*   Ensure that the request body is correctly serialized to JSON.
*   Simulate various server responses (200 OK, 404 Not Found, 500 Internal Server Error) to test your client's handling of both success and failure scenarios.
*   Verify that your client correctly deserializes the JSON response from the external service.

#### **What dependencies are automatically configured?**

The key component is **`MockRestServiceServer`**. This bean is automatically created and associated with your `RestTemplate`. When your client code tries to make an HTTP call, this mock server intercepts it.

You can then use its fluent API to:
1.  **Set Expectations:** Define what kind of request you expect your client to make (`expect(requestTo("/api/users/1"))`).
2.  **Define a Response:** Specify what response the mock server should return when it sees that request (`andRespond(withSuccess(jsonResponse, MediaType.APPLICATION_JSON))`).

#### **Common pitfalls and limitations.**

1.  **Targeting the Wrong Component:** You must specify which client you are testing in the annotation, for example, `@RestClientTest(MyApiClient.class)`. If you don't, Spring might not be able to wire the mock server correctly.
2.  **Testing Your Own Controllers:** A frequent point of confusion. `@RestClientTest` is **not** for testing your own `@RestController`s. It's for testing the code that *calls other services*. Use `@WebMvcTest` or `@SpringBootTest` for your own controllers.
3.  **Using `WebClient`:** `@RestClientTest` is designed for the synchronous `RestTemplate`/`RestClient`. For testing the reactive `WebClient`, you should use a different tool, such as `OkHttp`'s `MockWebServer`.

#### **Example test code and expected behavior.**

Let's test a client that fetches user data from an external API.

**The Client:**
```java
@Service
public class UserApiClient {
    private final RestTemplate restTemplate;

    public UserApiClient(RestTemplateBuilder builder) {
        this.restTemplate = builder.rootUri("https://api.example.com").build();
    }

    public UserData getUserById(String id) {
        return restTemplate.getForObject("/users/{id}", UserData.class, id);
    }
}

// A DTO to hold the response data
public class UserData { ... }
```

**The `@RestClientTest`:**
```java
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.client.RestClientTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.client.MockRestServiceServer;

import static org.assertj.core.api.Assertions.assertThat;
import static org.springframework.test.web.client.match.MockRestRequestMatchers.requestTo;
import static org.springframework.test.web.client.response.MockRestResponseCreators.withSuccess;

// We specify that we are testing the UserApiClient
@RestClientTest(UserApiClient.class)
class UserApiClientTest {

    @Autowired
    private UserApiClient userApiClient; // The actual client bean we're testing

    @Autowired
    private MockRestServiceServer server; // The mock server that will intercept calls

    @Autowired
    private ObjectMapper objectMapper; // Helper to create JSON strings

    @Test
    void whenGetUserById_callsCorrectUrlAndReturnsUser() throws Exception {
        // 1. Arrange: Prepare the expected response from the mock server
        UserData expectedUser = new UserData("1", "John Doe");
        String expectedJsonResponse = objectMapper.writeValueAsString(expectedUser);

        // Set up the mock server's expectation
        this.server.expect(requestTo("https://api.example.com/users/1"))
                .andRespond(withSuccess(expectedJsonResponse, MediaType.APPLICATION_JSON));

        // 2. Act: Call the method on our client
        UserData actualUser = userApiClient.getUserById("1");

        // 3. Assert: Verify the result and that the mock server was called
        this.server.verify(); // Fails the test if the expected request was never made
        assertThat(actualUser.getName()).isEqualTo("John Doe");
    }
}
```
---

### **Interview-Level Questions**

1.  **Question:** "You have a DTO with a `@JsonIgnore` property. Which test slice annotation is the best and fastest for verifying that this property is indeed excluded from the JSON output?"
    *   **Answer:** "`@JsonTest` is the perfect tool for this. It provides the fastest feedback because it only loads the JSON infrastructure (`ObjectMapper`) and a `JacksonTester`. I can write a simple test to serialize the DTO and use `assertThat(json).doesNotHaveJsonPath("$.ignoredField")` to verify the property is missing, confirming the `@JsonIgnore` annotation is working."

2.  **Question:** "What is the role of `MockRestServiceServer` in a `@RestClientTest`?"
    *   **Answer:** "`MockRestServiceServer` acts as a fake server that intercepts outgoing HTTP requests made by the `RestTemplate` under test. Instead of making a real network call, the request is sent to this mock server. We can then use its API to set expectations on the request (e.g., verifying the URL, method, and headers) and to provide a stubbed response. This allows us to test our REST client's behavior in isolation and simulate various API responses, including errors, without any external dependencies."

***

This concludes our tour of the main slice test annotations. Next, we will dive deep into **Database Testing with JPA**, moving beyond the in-memory H2 database to more robust solutions like Testcontainers.