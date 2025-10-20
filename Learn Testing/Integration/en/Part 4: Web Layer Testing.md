### **Part 4: Web Layer Testing**

Now we move up the application stack to the presentation layer. This is where your application interacts with the outside world, typically over HTTP with JSON payloads. Testing this layer is critical for ensuring your API is reliable, user-friendly, and secure.

#### **Topic 4.1: How `@WebMvcTest` Isolates Only the Web Layer**

As we've discussed, `@WebMvcTest` is a slice test. Its primary job is to load a minimal Spring `ApplicationContext` containing only the beans required for processing web requests.

**Let's visualize the slice:**

| Full Application Stack | `@WebMvcTest` Slice |
| :--- | :--- |
| **Servlet Container (Tomcat)** | Mock Servlet Environment |
| **Spring Security Filters** | Security Test Slice |
| **`@RestController` / `@Controller`** | ✅ **Loaded** |
| **`@ControllerAdvice`** | ✅ **Loaded** |
| **`ObjectMapper` / Converters** | ✅ **Loaded** |
| **`@Service`** | ❌ **Not Loaded** (Must be mocked) |
| **`@Repository`** | ❌ **Not Loaded** |
| **`DataSource` / Database** | ❌ **Not Loaded** |

This isolation is the key to its speed and focus. You are not testing if your business logic is correct or if your database queries work—those are jobs for unit and `@DataJpaTest`s, respectively. Here, you are testing if your controller behaves correctly **given a specific behavior from its dependencies (the service layer)**.

---

#### **Topic 4.2: Using `MockMvc` for Request/Response Testing**

`@WebMvcTest` automatically configures a `MockMvc` bean for you. This is your main entry point for testing. `MockMvc` allows you to simulate HTTP requests and assert on the responses without the overhead of a real network call.

The standard testing flow with `MockMvc` follows a fluent API structure:

`mockMvc.perform(requestBuilder)`
`.andExpect(resultMatcher)`
`.andExpect(anotherResultMatcher)`
`.andDo(resultHandler);` // Optional, for logging

**1. Building the Request (`MockMvcRequestBuilders`)**

This class provides static methods for creating any kind of HTTP request.

*   `get("/products/{id}", 1L)`: A GET request with a path variable.
*   `post("/products")`: A POST request.
*   `put("/products/{id}", 1L)`: A PUT request.
*   `delete("/products/{id}", 1L)`: A DELETE request.

You can customize the request further:

*   `.contentType(MediaType.APPLICATION_JSON)`: Sets the `Content-Type` header.
*   `.content(jsonString)`: Sets the request body.
*   `.param("page", "0").param("size", "10")`: Adds request parameters (e.g., for pagination).
*   `.header("Authorization", "Bearer ...")`: Adds a custom header.

**2. Asserting the Response (`MockMvcResultMatchers`)**

This class provides static methods for making assertions on the `MvcResult`.

*   `status().isOk()` (200), `isCreated()` (201), `isBadRequest()` (400), `isNotFound()` (404).
*   `content().contentType(MediaType.APPLICATION_JSON)`.
*   `content().string("Expected body")`.
*   `header().string("Location", "http://localhost/products/1")`.
*   `jsonPath("$.name", is("My Product"))`: The most powerful tool for inspecting the JSON body. We'll cover this in depth.

---

#### **Topic 4.3: Mocking Service Dependencies with `@MockBean`**

This is the most critical concept for using `@WebMvcTest` effectively. Since the service layer is not loaded, you **must** tell Spring what to inject into your controller. `@MockBean` instructs Spring to create a Mockito mock of a specific class and add it to the test's `ApplicationContext`.

**The Workflow:**

1.  **Arrange:** Use Mockito's `given()` (or `when()`) to define how the mock should behave.
2.  **Act:** Use `mockMvc.perform()` to execute the request against the controller.
3.  **Assert:** Use `.andExpect()` to verify the controller produced the correct response based on the mock's behavior.
4.  **(Optional) Verify:** Use Mockito's `verify()` to ensure the controller called the service method as expected.

**Code Example:**

```java
@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean // Spring will create a mock of ProductService and inject it
    private ProductService productService;

    @Test
    void givenProductExists_whenGetProductById_thenReturns200() throws Exception {
        // 1. Arrange: Define the mock's behavior
        Product product = new Product(1L, "Test Book", 29.99);
        given(productService.findById(1L)).willReturn(product);

        // 2. Act
        mockMvc.perform(get("/products/1"))
        // 3. Assert
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name", is("Test Book")));

        // 4. (Optional) Verify
        verify(productService, times(1)).findById(1L);
    }
}
```

---

#### **Topic 4.4: Asserting JSON Structure**

You have several powerful options for asserting the JSON response body.

1.  **`jsonPath()` (Most Common)**
    *   Uses the Jayway JsonPath library to query specific fields in the JSON document.
    *   Syntax: `$` is the root. `.` accesses properties. `[]` accesses array elements.
    *   Excellent for checking specific values without being brittle.

    ```java
    .andExpect(jsonPath("$.id", is(1)))
    .andExpect(jsonPath("$.name", is("Gaming Keyboard")))
    .andExpect(jsonPath("$.tags[0]", is("electronics")))
    .andExpect(jsonPath("$.owner.name", is("Admin")))
    ```

2.  **`content().json()` (Strict Structure Matching)**
    *   Asserts that the response body matches a given JSON structure. It's flexible about whitespace and field order but strict about which fields are present.
    *   This is fantastic for preventing API contract regressions. You can store the expected JSON in a file in `src/test/resources`.

    ```java
    // expected-product.json
    // { "id": 1, "name": "Gaming Keyboard", "price": 99.99 }

    @Test
    void testWithJsonFile() throws Exception {
        // ... arrange mock ...
        String expectedJson = new String(Files.readAllBytes(Paths.get("src/test/resources/expected-product.json")));

        mockMvc.perform(get("/products/1"))
            .andExpect(status().isOk())
            .andExpect(content().json(expectedJson));
    }
    ```

3.  **Manual Deserialization with `ObjectMapper`**
    *   For very complex assertions, you can get the response as a string, deserialize it back into a DTO, and use a rich assertion library like AssertJ.

    ```java
    MvcResult result = mockMvc.perform(get("/products/1"))
            .andExpect(status().isOk())
            .andReturn();

    String contentAsString = result.getResponse().getContentAsString();
    ProductDto actualProduct = objectMapper.readValue(contentAsString, ProductDto.class);

    assertThat(actualProduct.getName()).isEqualTo("Gaming Keyboard");
    assertThat(actualProduct.getPrice()).isCloseTo(99.99, within(0.01));
    ```

---

#### **Topic 4.5: Testing Validation, Exception Handling, and Controller Advice**

`@WebMvcTest` is the perfect place to test these cross-cutting concerns.

**Testing Validation Rules (`@Valid`)**

```java
@Test
void whenCreateProductWithInvalidData_thenReturns400() throws Exception {
    // Arrange: Create a DTO with a blank name
    ProductCreateDto dto = new ProductCreateDto("", -10.0);
    String dtoString = objectMapper.writeValueAsString(dto);

    // Act & Assert
    mockMvc.perform(post("/products")
            .contentType(MediaType.APPLICATION_JSON)
            .content(dtoString))
            .andExpect(status().isBadRequest());
}
```
This test implicitly verifies that your `@NotBlank` and `@Positive` annotations on the DTO are working and that Spring's validation mechanism is correctly triggered, returning a 400 Bad Request.

**Testing Exception Handling (`@ControllerAdvice`)**

```java
@Test
void whenGetProductThatDoesNotExist_thenReturns404() throws Exception {
    // Arrange: Tell the mock to throw the exception that your service would throw.
    given(productService.findById(99L))
        .willThrow(new ProductNotFoundException("Product with id 99 not found"));

    // Act & Assert
    mockMvc.perform(get("/products/99"))
        .andExpect(status().isNotFound());
}
```
This test proves that when your controller's dependency throws a `ProductNotFoundException`, your `@ControllerAdvice` correctly intercepts it and translates it into an HTTP 404 Not Found response.

---

#### **Topic 4.6: Handling Edge Cases**

**Multipart File Uploads**

Use `MockMvcRequestBuilders.multipart()` to simulate a file upload.

```java
@Test
void whenUploadImage_thenReturns200() throws Exception {
    MockMultipartFile imageFile = new MockMultipartFile(
        "file",               // The name of the request part
        "image.jpg",          // The original filename
        "image/jpeg",         // The content type
        "image-bytes".getBytes()
    );

    mockMvc.perform(multipart("/products/1/image").file(imageFile))
        .andExpect(status().isOk());
}
```

**Authentication and Authorization**

Spring Security provides test annotations that make this easy. `@WithMockUser` is the most common.

```java
@Test
@WithMockUser(username = "admin", roles = {"ADMIN"})
void whenDeleteProductAsAdmin_thenReturns204() throws Exception {
    // The request will be performed as if an authenticated user "admin"
    // with the role "ADMIN" is making the call.
    mockMvc.perform(delete("/products/1"))
        .andExpect(status().isNoContent());
}

@Test
@WithMockUser(username = "user", roles = {"USER"}) // User with insufficient privileges
void whenDeleteProductAsUser_thenReturns403() throws Exception {
    mockMvc.perform(delete("/products/1"))
        .andExpect(status().isForbidden());
}
```

---

### **Topic 4.7: Differences between `MockMvc`, `WebTestClient`, and `RestAssured`**

| Tool | Type | Key Characteristic | Best For... |
| :--- | :--- | :--- | :--- |
| **MockMvc** | Spring Test Framework | **In-Memory / Mock Server.** No real HTTP call. | Fast, isolated slice testing of controllers (`@WebMvcTest`). Testing validation and exception handling. |
| **WebTestClient** | Spring Test Framework | **Reactive & Flexible.** Can bind to `MockMvc` OR a real server. | Testing reactive (WebFlux) applications. A modern alternative to both `MockMvc` and `TestRestTemplate`. |
| **RestAssured** | 3rd Party Library | **Real HTTP Client.** BDD-style (`given/when/then`). | End-to-end REST API tests (`@SpringBootTest(webEnvironment=RANDOM_PORT)`). Readability and powerful JSON/XML validation. |

*   **Choose `MockMvc`** when you want fast, focused feedback on your controller logic in isolation.
*   **Choose `RestAssured` or `TestRestTemplate`** when you need to test the full application stack, including the servlet container, filters, and network stack, in an end-to-end test.

***

Next, we will put everything together and learn about **Full Context Integration Testing**, where we load the entire application and test it from end to end.

