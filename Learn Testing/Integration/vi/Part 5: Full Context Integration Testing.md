### **Phần 5: Kiểm thử Tích hợp Toàn bộ Context**

Cho đến nay, chúng ta đã kiểm thử ứng dụng của mình trong các "lát cắt" cô lập. Bây giờ là lúc để kết hợp tất cả lại với nhau. Kiểm thử tích hợp toàn bộ context bao gồm việc tải *toàn bộ* ứng dụng Spring Boot của bạn—từ tầng web xuống đến cơ sở dữ liệu—và kiểm thử nó như một hệ thống hoàn chỉnh, đang chạy. Đây là cấp độ kiểm thử tự động cao nhất mà bạn thường sẽ viết và mang lại cho bạn sự tự tin lớn nhất rằng tất cả các mảnh ghép hoạt động cùng nhau.

#### **Chủ đề 5.1: Sử dụng `@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)`**

Đây là annotation nền tảng cho kiểm thử đầu cuối (E2E). Hãy phân tích lý do tại sao:

*   **`@SpringBootTest`**: Như chúng ta đã học, nó tải `ApplicationContext` hoàn chỉnh, giống như khi bạn chạy phương thức `main` của ứng dụng. Tất cả các controller, service, repository và các thành phần khác của bạn đều được tạo và kết nối với nhau.
*   **`webEnvironment = WebEnvironment.RANDOM_PORT`**: Đây là phần quan trọng cho kiểm thử E2E. Nó bảo Spring khởi động một máy chủ web nhúng thực sự (như Tomcat) và để nó lắng nghe các request HTTP trên một cổng mạng ngẫu nhiên, khả dụng.

Bằng cách sử dụng sự kết hợp này, bài kiểm thử của bạn thực sự chạy một phiên bản thu nhỏ, khép kín của ứng dụng production. Sau đó, bạn có thể sử dụng một client HTTP thực để tương tác với nó, mô phỏng cách một ứng dụng frontend hoặc một microservice khác sẽ làm.

#### **Chủ đề 5.2: Kiểm thử các Endpoint REST với `TestRestTemplate` và `RestAssured`**

Vì chúng ta có một máy chủ thực đang chạy, chúng ta không thể sử dụng `MockMvc`. Chúng ta cần một client HTTP thực. Spring Boot cung cấp `TestRestTemplate`, và một lựa chọn phổ biến của bên thứ ba là `RestAssured`.

**1. Sử dụng `TestRestTemplate`**

Đây là một trình bao bọc (wrapper) tiện lợi quanh `HttpClient` của Apache, được thiết kế đặc biệt cho các bài kiểm thử tích hợp. Spring Boot sẽ tự động cấu hình một bean `TestRestTemplate` để bạn tiêm vào khi bạn sử dụng `RANDOM_PORT`.

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class ProductControllerE2ETest extends AbstractIntegrationTest { // Tái sử dụng cấu hình DB của chúng ta

    // Spring tiêm vào cổng ngẫu nhiên mà máy chủ đã khởi động
    @LocalServerPort
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private ProductRepository productRepository;

    @Test
    void whenProductExists_GetRequest_ReturnsProductDetails() {
        // Arrange: Lưu dữ liệu trực tiếp vào cơ sở dữ liệu thật
        Product savedProduct = productRepository.save(new Product(null, "E2E Test Product", 199.99));

        // Act: Thực hiện một cuộc gọi HTTP THỰC SỰ đến ứng dụng đang chạy của chúng ta
        String url = "http://localhost:" + port + "/products/" + savedProduct.getId();
        ResponseEntity<ProductDto> response = restTemplate.getForEntity(url, ProductDto.class);

        // Assert
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(response.getBody()).isNotNull();
        assertThat(response.getBody().getName()).isEqualTo("E2E Test Product");
    }
}
```

**2. Sử dụng `RestAssured`**

RestAssured là một thư viện cung cấp một cú pháp fluent, theo phong cách BDD (Given/When/Then) để viết các bài kiểm thử API REST. Nó thường được ưa chuộng vì khả năng đọc cao và các khả năng khẳng định tích hợp mạnh mẽ.

**Cài đặt:** Thêm phụ thuộc RestAssured vào `pom.xml` của bạn.

```xml
<dependency>
    <groupId>io.rest-assured</groupId>
    <artifactId>rest-assured</artifactId>
    <scope>test</scope>
</dependency>
```

**Ví dụ Kiểm thử:**

```java
import io.restassured.RestAssured;
import io.restassured.http.ContentType;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import static io.restassured.RestAssured.given;
import static org.hamcrest.Matchers.equalTo;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class ProductControllerRestAssuredTest extends AbstractIntegrationTest {

    @LocalServerPort
    private int port;

    @Autowired
    private ProductRepository productRepository;

    // Cấu hình RestAssured một lần trước bất kỳ bài kiểm thử nào
    @BeforeEach
    void setUp() {
        RestAssured.port = port;
        productRepository.deleteAll(); // Trạng thái sạch
    }

    @Test
    void whenProductExists_GetRequest_ReturnsProductDetails() {
        // Arrange
        Product savedProduct = productRepository.save(new Product(null, "RestAssured Product", 150.00));

        // Act & Assert (sử dụng cú pháp BDD của RestAssured)
        given()
            .contentType(ContentType.JSON)
        .when()
            .get("/products/{id}", savedProduct.getId())
        .then()
            .statusCode(200)
            .body("name", equalTo("RestAssured Product"))
            .body("price", equalTo(150.00f)); // Lưu ý: Số trong JSON thường là float
    }
}
```

**So sánh:**
*   `TestRestTemplate` đơn giản và được tích hợp vào Spring. Nó cho cảm giác như code Java thông thường.
*   `RestAssured` cung cấp một ngôn ngữ đặc tả miền (DSL) biểu cảm hơn cho việc kiểm thử API mà nhiều người thấy dễ đọc hơn. Các khẳng định `body()` tích hợp của nó thường ngắn gọn hơn so với việc sử dụng `jsonPath`.

---

#### **Chủ đề 5.3 & 5.4: Profile Kiểm thử (`application-test.yml`), `@ActiveProfiles`, và `@DirtiesContext`**

Đối với các bài kiểm thử toàn bộ context, bạn gần như luôn cần một cấu hình riêng biệt so với `application.yml` chính của mình.

**1. Tạo `src/test/resources/application-test.yml`**

Tệp này sẽ chỉ được tải khi profile "test" đang hoạt động. Đây là nơi hoàn hảo để ghi đè các thuộc tính cho môi trường kiểm thử của bạn.

```yaml
# src/test/resources/application-test.yml

server:
  port: 0 # Sử dụng một cổng ngẫu nhiên, tương đương với RANDOM_PORT

# Ví dụ: tắt một tính năng bạn không muốn trong các bài kiểm thử
feature-flags:
  send-emails: false

# Cấu hình logging cụ thể cho các bài kiểm thử
logging:
  level:
    com.yourapp: DEBUG
    org.springframework.transaction: TRACE

# Sử dụng Flyway? Hãy để nó quản lý schema.
spring:
  jpa:
    hibernate:
      ddl-auto: validate # Để Flyway tạo, Hibernate chỉ xác thực
  flyway:
    enabled: true

  # Chúng ta không cần đặt URL/user/pass của datasource ở đây vì
  # thiết lập Testcontainers của chúng ta sẽ ghi đè chúng một cách động.
```

**2. Kích hoạt Profile với `@ActiveProfiles("test")`**

Thêm annotation này vào lớp kiểm thử của bạn để bảo Spring kích hoạt profile nào.

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@ActiveProfiles("test") // <-- Bảo Spring tải application-test.yml
class ProductControllerE2ETest extends AbstractIntegrationTest {
    // ...
}
```

**3. Đặt lại Trạng thái với `@DirtiesContext`**

Spring TestContext Framework rất thông minh và lưu trữ `ApplicationContext` vào bộ đệm giữa các lớp kiểm thử để tăng tốc. Tuy nhiên, đôi khi một bài kiểm thử có thể làm điều gì đó thay đổi context một cách không thể đảo ngược, chẳng hạn như sửa đổi trạng thái của một bean singleton hoặc thay đổi một thuộc tính.

`@DirtiesContext` bảo Spring rằng application context đã bị "làm bẩn" và phải được đóng và tạo lại trước khi lớp kiểm thử tiếp theo chạy.

*   `@DirtiesContext(classMode = ClassMode.AFTER_CLASS)`: Tạo lại context sau khi tất cả các bài kiểm thử trong lớp này đã chạy xong (phổ biến nhất).
*   `@DirtiesContext(methodMode = MethodMode.AFTER_METHOD)`: Tạo lại context sau *mỗi phương thức* trong lớp này. **Sử dụng hết sức thận trọng! Điều này sẽ làm cho các bài kiểm thử của bạn cực kỳ chậm.**

**Khi nào nên sử dụng:**
*   Một bài kiểm thử sửa đổi trạng thái của một bean singleton theo cách không thể dễ dàng đặt lại.
*   Một bài kiểm thử thay đổi các thuộc tính ứng dụng một cách động.
*   Bạn đang kiểm thử một thứ gì đó dựa vào logic khởi động ứng dụng (ví dụ: một phương thức `@PostConstruct`) và cần một khởi đầu mới cho lớp kiểm thử tiếp theo.

**Quy tắc chung:** Tránh `@DirtiesContext` nếu bạn có thể. Nó là một kẻ giết chết hiệu suất và thường cho thấy các bài kiểm thử của bạn không được cô lập đúng cách. Ưu tiên làm sạch trạng thái cơ sở dữ liệu của bạn trong một phương thức `@BeforeEach` hoặc `@AfterEach`.

---

#### **Chủ đề 5.5: Khởi tạo Dữ liệu**

Đối với các bài kiểm thử E2E, bạn cần một cách đáng tin cậy để đưa cơ sở dữ liệu của mình về một trạng thái đã biết.

1.  **Flyway/Liquibase (Thực hành tốt nhất):** Như đã đề cập trong phần JPA, đây là phương pháp mạnh mẽ nhất. Các bài kiểm thử của bạn chạy trên một schema giống hệt với production. Bạn cũng có thể sử dụng các migration "có thể lặp lại" của Flyway để điền dữ liệu tham chiếu chung (ví dụ: danh sách các quốc gia hoặc vai trò người dùng).

2.  **`data.sql`:** Nếu bạn đặt một tệp `data.sql` trong `src/test/resources`, Spring Boot sẽ thực thi nó sau khi schema đã được tạo. Đây là một cách đơn giản để chèn dữ liệu mồi (seed data).
    *   **Ưu điểm:** Rất đơn giản.
    *   **Nhược điểm:** Có thể trở nên khó quản lý. Nó chạy cho *tất cả* các bài kiểm thử, điều này có thể không phải là điều bạn muốn. Thiết lập bằng lập trình thường linh hoạt hơn.

3.  **Thiết lập bằng Lập trình (Linh hoạt nhất):** Sử dụng một phương thức `@BeforeEach` để gọi các repository của bạn và thiết lập trạng thái chính xác cần thiết cho các bài kiểm thử của bạn.
    *   **Ưu điểm:**
        *   Mỗi bài kiểm thử có thể chỉ thiết lập dữ liệu mà nó cần.
        *   Nó là Java thuần túy, vì vậy nó an toàn về kiểu và dễ dàng tái cấu trúc.
        *   Bạn có thể sử dụng các factory đối tượng hoặc Test Data Builders để tạo các đồ thị đối tượng phức tạp một cách dễ dàng.
    *   **Nhược điểm:** Có thể hơi dài dòng hơn đối với dữ liệu đơn giản.

**Khuyến nghị:** Sử dụng Flyway để quản lý schema của bạn. Sử dụng một phương thức `@BeforeEach` lập trình để quản lý dữ liệu cụ thể cho mỗi phương thức kiểm thử, đảm bảo bạn `deleteAll()` trước để duy trì sự cô lập.

---

#### **Chủ đề 5.6: Xử lý các Vấn đề và Thực hành Tốt nhất về Hiệu suất**

*   **Xung đột Cổng:** Luôn sử dụng `WebEnvironment.RANDOM_PORT` hoặc `server.port=0` trong profile kiểm thử của bạn để tránh chúng.
*   **Độ trễ Khởi động & Hoạt động Bất đồng bộ:** Nếu ứng dụng của bạn thực hiện công việc trong nền, bài kiểm thử của bạn có thể kết thúc trước khi công việc hoàn thành. Sử dụng một thư viện như **Awaitility** để thăm dò kết quả trong một khoảng thời gian chờ (ví dụ: `await().atMost(5, SECONDS).until(() -> repository.count() == 1);`).
*   **Lưu trữ Context vào Bộ đệm là Chìa khóa:** Lợi ích hiệu suất lớn nhất đến từ việc tái sử dụng `ApplicationContext`.
    *   **Hài hòa hóa các cấu hình kiểm thử của bạn.** Cố gắng sử dụng cùng một `@ActiveProfiles` và tránh `@MockBean` trong các bài kiểm thử E2E nếu có thể.
    *   **Sử dụng mô hình container singleton** cho Testcontainers.
    *   **Tránh `@DirtiesContext`** trừ khi thực sự cần thiết.

***

Tiếp theo, chúng ta sẽ chính thức hóa sự hiểu biết của mình về **Profile và Cấu hình Kiểm thử**, xem xét chi tiết hơn về việc ghi đè thuộc tính và quản lý các cài đặt dành riêng cho từng môi trường.