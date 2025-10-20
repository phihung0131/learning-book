### **Phần 4: Kiểm thử Tầng Web**

Bây giờ chúng ta di chuyển lên tầng trình bày trong ngăn xếp ứng dụng. Đây là nơi ứng dụng của bạn tương tác với thế giới bên ngoài, thường là qua HTTP với các payload JSON. Việc kiểm thử tầng này là cực kỳ quan trọng để đảm bảo API của bạn đáng tin cậy, thân thiện với người dùng và an toàn.

#### **Chủ đề 4.1: Cách `@WebMvcTest` Cô lập Chỉ Tầng Web**

Như chúng ta đã thảo luận, `@WebMvcTest` là một kiểm thử lát cắt. Nhiệm vụ chính của nó là tải một `ApplicationContext` tối thiểu của Spring, chỉ chứa các bean cần thiết để xử lý các request web.

**Hãy hình dung lát cắt này:**

| Ngăn xếp Ứng dụng Đầy đủ | Lát cắt của `@WebMvcTest` |
| :--- | :--- |
| **Servlet Container (Tomcat)** | Môi trường Servlet Giả lập (Mock) |
| **Các Filter của Spring Security** | Lát cắt Kiểm thử Bảo mật |
| **`@RestController` / `@Controller`** | ✅ **Được tải** |
| **`@ControllerAdvice`** | ✅ **Được tải** |
| **`ObjectMapper` / Converters** | ✅ **Được tải** |
| **`@Service`** | ❌ **Không được tải** (Phải được mock) |
| **`@Repository`** | ❌ **Không được tải** |
| **`DataSource` / Cơ sở dữ liệu** | ❌ **Không được tải** |

Sự cô lập này là chìa khóa cho tốc độ và sự tập trung của nó. Bạn không kiểm thử xem logic nghiệp vụ của mình có đúng hay không, hay các truy vấn cơ sở dữ liệu của bạn có hoạt động không—đó là công việc tương ứng của các unit test và `@DataJpaTest`. Ở đây, bạn đang kiểm thử xem controller của bạn có hoạt động chính xác **với một hành vi cụ thể từ các phụ thuộc của nó (tầng service)** hay không.

---

#### **Chủ đề 4.2: Sử dụng `MockMvc` để Kiểm thử Request/Response**

`@WebMvcTest` tự động cấu hình một bean `MockMvc` cho bạn. Đây là điểm vào chính của bạn để kiểm thử. `MockMvc` cho phép bạn mô phỏng các request HTTP và khẳng định trên các response mà không có chi phí của một cuộc gọi mạng thực sự.

Luồng kiểm thử tiêu chuẩn với `MockMvc` tuân theo một cấu trúc API fluent:

`mockMvc.perform(requestBuilder)`
`.andExpect(resultMatcher)`
`.andExpect(anotherResultMatcher)`
`.andDo(resultHandler);` // Tùy chọn, để ghi log

**1. Xây dựng Request (`MockMvcRequestBuilders`)**

Lớp này cung cấp các phương thức tĩnh để tạo bất kỳ loại request HTTP nào.

*   `get("/products/{id}", 1L)`: Một request GET với một biến đường dẫn.
*   `post("/products")`: Một request POST.
*   `put("/products/{id}", 1L)`: Một request PUT.
*   `delete("/products/{id}", 1L)`: Một request DELETE.

Bạn có thể tùy chỉnh request thêm nữa:

*   `.contentType(MediaType.APPLICATION_JSON)`: Đặt header `Content-Type`.
*   `.content(jsonString)`: Đặt body của request.
*   `.param("page", "0").param("size", "10")`: Thêm các tham số request (ví dụ: cho phân trang).
*   `.header("Authorization", "Bearer ...")`: Thêm một header tùy chỉnh.

**2. Khẳng định Response (`MockMvcResultMatchers`)**

Lớp này cung cấp các phương thức tĩnh để thực hiện các khẳng định trên `MvcResult`.

*   `status().isOk()` (200), `isCreated()` (201), `isBadRequest()` (400), `isNotFound()` (404).
*   `content().contentType(MediaType.APPLICATION_JSON)`.
*   `content().string("Expected body")`.
*   `header().string("Location", "http://localhost/products/1")`.
*   `jsonPath("$.name", is("My Product"))`: Công cụ mạnh mẽ nhất để kiểm tra body JSON. Chúng ta sẽ tìm hiểu sâu về điều này.

---

#### **Chủ đề 4.3: Mock các Phụ thuộc Service với `@MockBean`**

Đây là khái niệm quan trọng nhất để sử dụng `@WebMvcTest` một cách hiệu quả. Vì tầng service không được tải, bạn **phải** nói cho Spring biết phải tiêm gì vào controller của bạn. `@MockBean` hướng dẫn Spring tạo một mock Mockito của một lớp cụ thể và thêm nó vào `ApplicationContext` của bài kiểm thử.

**Quy trình làm việc:**

1.  **Arrange (Sắp xếp):** Sử dụng `given()` (hoặc `when()`) của Mockito để định nghĩa cách mock nên hoạt động.
2.  **Act (Hành động):** Sử dụng `mockMvc.perform()` để thực thi request đến controller.
3.  **Assert (Khẳng định):** Sử dụng `.andExpect()` để xác minh controller đã tạo ra response chính xác dựa trên hành vi của mock.
4.  **(Tùy chọn) Verify (Xác minh):** Sử dụng `verify()` của Mockito để đảm bảo controller đã gọi phương thức service như mong đợi.

**Ví dụ Code:**

```java
@WebMvcTest(ProductController.class)
class ProductControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean // Spring sẽ tạo một mock của ProductService và tiêm nó vào
    private ProductService productService;

    @Test
    void givenProductExists_whenGetProductById_thenReturns200() throws Exception {
        // 1. Arrange: Định nghĩa hành vi của mock
        Product product = new Product(1L, "Test Book", 29.99);
        given(productService.findById(1L)).willReturn(product);

        // 2. Act
        mockMvc.perform(get("/products/1"))
        // 3. Assert
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.name", is("Test Book")));

        // 4. (Tùy chọn) Verify
        verify(productService, times(1)).findById(1L);
    }
}
```

---

#### **Chủ đề 4.4: Khẳng định Cấu trúc JSON**

Bạn có một số tùy chọn mạnh mẽ để khẳng định body response JSON.

1.  **`jsonPath()` (Phổ biến nhất)**
    *   Sử dụng thư viện Jayway JsonPath để truy vấn các trường cụ thể trong tài liệu JSON.
    *   Cú pháp: `$` là gốc. `.` truy cập các thuộc tính. `[]` truy cập các phần tử mảng.
    *   Tuyệt vời để kiểm tra các giá trị cụ thể mà không bị cứng nhắc.

    ```java
    .andExpect(jsonPath("$.id", is(1)))
    .andExpect(jsonPath("$.name", is("Gaming Keyboard")))
    .andExpect(jsonPath("$.tags[0]", is("electronics")))
    .andExpect(jsonPath("$.owner.name", is("Admin")))
    ```

2.  **`content().json()` (Đối sánh Cấu trúc Nghiêm ngặt)**
    *   Khẳng định rằng body response khớp với một cấu trúc JSON đã cho. Nó linh hoạt về khoảng trắng và thứ tự các trường nhưng nghiêm ngặt về các trường có mặt.
    *   Điều này rất tuyệt vời để ngăn chặn sự thụt lùi của hợp đồng API. Bạn có thể lưu trữ JSON mong đợi trong một tệp tại `src/test/resources`.

    ```java
    // expected-product.json
    // { "id": 1, "name": "Gaming Keyboard", "price": 99.99 }

    @Test
    void testWithJsonFile() throws Exception {
        // ... sắp xếp mock ...
        String expectedJson = new String(Files.readAllBytes(Paths.get("src/test/resources/expected-product.json")));

        mockMvc.perform(get("/products/1"))
            .andExpect(status().isOk())
            .andExpect(content().json(expectedJson));
    }
    ```

3.  **Giải tuần tự hóa Thủ công với `ObjectMapper`**
    *   Đối với các khẳng định rất phức tạp, bạn có thể lấy response dưới dạng một chuỗi, giải tuần tự hóa nó trở lại thành một DTO, và sử dụng một thư viện khẳng định phong phú như AssertJ.

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

#### **Chủ đề 4.5: Kiểm thử Validation, Xử lý Exception, và Controller Advice**

`@WebMvcTest` là nơi hoàn hảo để kiểm thử các mối quan tâm xuyên suốt (cross-cutting concerns) này.

**Kiểm thử các Quy tắc Xác thực (`@Valid`)**

```java
@Test
void whenCreateProductWithInvalidData_thenReturns400() throws Exception {
    // Arrange: Tạo một DTO với tên trống
    ProductCreateDto dto = new ProductCreateDto("", -10.0);
    String dtoString = objectMapper.writeValueAsString(dto);

    // Act & Assert
    mockMvc.perform(post("/products")
            .contentType(MediaType.APPLICATION_JSON)
            .content(dtoString))
            .andExpect(status().isBadRequest());
}
```
Bài kiểm thử này ngầm xác minh rằng các annotation `@NotBlank` và `@Positive` của bạn trên DTO đang hoạt động và cơ chế xác thực của Spring được kích hoạt chính xác, trả về 400 Bad Request.

**Kiểm thử Xử lý Exception (`@ControllerAdvice`)**

```java
@Test
void whenGetProductThatDoesNotExist_thenReturns404() throws Exception {
    // Arrange: Bảo mock ném ra exception mà service của bạn sẽ ném.
    given(productService.findById(99L))
        .willThrow(new ProductNotFoundException("Product with id 99 not found"));

    // Act & Assert
    mockMvc.perform(get("/products/99"))
        .andExpect(status().isNotFound());
}
```
Bài kiểm thử này chứng minh rằng khi phụ thuộc của controller của bạn ném ra một `ProductNotFoundException`, `@ControllerAdvice` của bạn sẽ chặn nó một cách chính xác và chuyển nó thành một response HTTP 404 Not Found.

---

#### **Chủ đề 4.6: Xử lý các Trường hợp Biên (Edge Cases)**

**Tải lên Tệp Multipart**

Sử dụng `MockMvcRequestBuilders.multipart()` để mô phỏng việc tải lên một tệp.

```java
@Test
void whenUploadImage_thenReturns200() throws Exception {
    MockMultipartFile imageFile = new MockMultipartFile(
        "file",               // Tên của phần request
        "image.jpg",          // Tên tệp gốc
        "image/jpeg",         // Kiểu nội dung
        "image-bytes".getBytes()
    );

    mockMvc.perform(multipart("/products/1/image").file(imageFile))
        .andExpect(status().isOk());
}
```

**Xác thực và Ủy quyền**

Spring Security cung cấp các annotation kiểm thử giúp việc này trở nên dễ dàng. `@WithMockUser` là phổ biến nhất.

```java
@Test
@WithMockUser(username = "admin", roles = {"ADMIN"})
void whenDeleteProductAsAdmin_thenReturns204() throws Exception {
    // Request sẽ được thực hiện như thể một người dùng đã xác thực "admin"
    // với vai trò "ADMIN" đang thực hiện cuộc gọi.
    mockMvc.perform(delete("/products/1"))
        .andExpect(status().isNoContent());
}

@Test
@WithMockUser(username = "user", roles = {"USER"}) // Người dùng không có đủ đặc quyền
void whenDeleteProductAsUser_thenReturns403() throws Exception {
    mockMvc.perform(delete("/products/1"))
        .andExpect(status().isForbidden());
}
```

---

### **Chủ đề 4.7: Sự khác biệt giữa `MockMvc`, `WebTestClient`, và `RestAssured`**

| Công cụ | Loại | Đặc điểm chính | Tốt nhất cho... |
| :--- | :--- | :--- | :--- |
| **MockMvc** | Spring Test Framework | **Trong bộ nhớ / Máy chủ Mock.** Không có cuộc gọi HTTP thực sự. | Kiểm thử lát cắt nhanh, cô lập các controller (`@WebMvcTest`). Kiểm thử validation và xử lý exception. |
| **WebTestClient** | Spring Test Framework | **Phản ứng (Reactive) & Linh hoạt.** Có thể liên kết với `MockMvc` HOẶC một máy chủ thực. | Kiểm thử các ứng dụng phản ứng (WebFlux). Một sự thay thế hiện đại cho cả `MockMvc` và `TestRestTemplate`. |
| **RestAssured** | Thư viện bên thứ 3 | **Client HTTP thực.** Phong cách BDD (`given/when/then`). | Các bài kiểm thử API REST đầu cuối (`@SpringBootTest(webEnvironment=RANDOM_PORT)`). Khả năng đọc và xác thực JSON/XML mạnh mẽ. |

*   **Chọn `MockMvc`** khi bạn muốn có phản hồi nhanh, tập trung vào logic controller của bạn một cách cô lập.
*   **Chọn `RestAssured` hoặc `TestRestTemplate`** khi bạn cần kiểm thử toàn bộ ngăn xếp ứng dụng, bao gồm cả servlet container, các filter, và ngăn xếp mạng, trong một bài kiểm thử đầu cuối.

***

Tiếp theo, chúng ta sẽ kết hợp mọi thứ lại với nhau và tìm hiểu về **Kiểm thử Tích hợp Toàn bộ Context**, nơi chúng ta tải toàn bộ ứng dụng và kiểm thử nó từ đầu đến cuối.