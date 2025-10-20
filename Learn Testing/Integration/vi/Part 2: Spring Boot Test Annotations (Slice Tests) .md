### **Phần 2: Các Annotation Kiểm thử của Spring Boot (Kiểm thử Lát cắt - Slice Tests)**

Trước khi chúng ta đi sâu vào các annotation cụ thể, hãy cùng tìm hiểu ý tưởng cốt lõi đằng sau chúng.

#### **"Lát cắt Kiểm thử" (Test Slice) là gì?**

Hãy hình dung ứng dụng Spring Boot của bạn như một chiếc bánh nhiều lớp.

*   Lớp dưới cùng là **Tầng Persistence** (JPA, Repositories).
*   Lớp giữa là **Tầng Service/Logic Nghiệp vụ** (các bean `@Service`).
*   Lớp trên cùng là **Tầng Web/Trình bày** (`@RestController`, `@Controller`).

Một **kiểm thử lát cắt (slice test)** tập trung vào việc kiểm thử chỉ *một* trong những lớp này (hoặc "lát cắt") một cách cô lập. Nó tải một `ApplicationContext` rất hạn chế và có mục tiêu, chỉ chứa các bean cần thiết cho lát cắt cụ thể đó.

**Tại sao nên sử dụng kiểm thử lát cắt?**

1.  **Tốc độ:** Tải một context nhỏ, có mục tiêu sẽ nhanh hơn đáng kể so với việc tải toàn bộ ứng dụng bằng `@SpringBootTest`.
2.  **Sự tập trung:** Nó buộc bạn phải suy nghĩ về các trách nhiệm cụ thể của lớp bạn đang kiểm thử và phải mock các phụ thuộc của nó một cách đúng đắn. Điều này dẫn đến các bài kiểm thử được thiết kế tốt hơn và ít ràng buộc hơn.
3.  **Sự đơn giản:** Các annotation này tự động cấu hình mọi thứ bạn cần cho lát cắt đó, vì vậy bạn không cần phải thực hiện các thiết lập thủ công phức tạp.

Bây giờ, hãy khám phá annotation kiểm thử lát cắt phổ biến và hữu ích nhất.

---

### **Chủ đề 2.1: `@WebMvcTest`**

#### **Nó tải phần nào của application context?**

`@WebMvcTest` được thiết kế để kiểm thử **tầng web** của ứng dụng và không gì khác. Khi bạn sử dụng nó, Spring Boot sẽ chỉ tự động cấu hình các thành phần liên quan đến Spring MVC.

**Những gì được tải:**
*   Các bean `@Controller`, `@RestController`, `@ControllerAdvice`.
*   Các bean `@JsonComponent`, `Converter`, `GenericConverter`.
*   Các bean `Filter`, `WebMvcConfigurer`, `HandlerMethodArgumentResolver`.
*   Các bean cơ sở hạ tầng quan trọng như `ObjectMapper` để xử lý JSON.

**Những gì KHÔNG được tải:**
*   `@Service`, `@Repository`, `@Component` hoặc bất kỳ bean thông thường nào khác.
*   Cấu hình bảo mật đầy đủ (mặc dù một số cơ sở hạ tầng bảo mật vẫn có mặt).
*   DataSource và toàn bộ tầng persistence.

#### **Khi nào và tại sao nên sử dụng nó?**

Sử dụng `@WebMvcTest` khi mục tiêu chính của bạn là xác minh hành vi của các controller Spring MVC. Cụ thể:

*   **Ánh xạ Request (Request Mapping):** Controller của tôi có đang lắng nghe đúng URL và phương thức HTTP không?
*   **Giải tuần tự hóa Request (Request Deserialization):** Controller của tôi có thể phân tích cú pháp JSON/XML đầu vào thành một DTO một cách chính xác không?
*   **Xác thực (Validation):** Các annotation `@Valid` và các quy tắc xác thực của tôi có được kích hoạt đúng cách đối với đầu vào không hợp lệ không?
*   **Tương tác với Tầng Service:** Controller của tôi có gọi đúng phương thức trên phụ thuộc service của nó không?
*   **Tuần tự hóa Response (Response Serialization):** Controller của tôi có tuần tự hóa đối tượng phản hồi thành JSON một cách chính xác không?
*   **Mã trạng thái HTTP:** Endpoint của tôi có trả về các mã trạng thái chính xác không (200 OK, 201 Created, 400 Bad Request, 404 Not Found)?
*   **Xử lý Exception:** `@ControllerAdvice` của tôi có xử lý chính xác các exception được ném ra bởi controller và trả về một phản hồi lỗi phù hợp không?

#### **Những phụ thuộc nào được tự động cấu hình?**

Thành phần nổi bật nhất được `@WebMvcTest` tự động cấu hình là **`MockMvc`**. Spring Boot sẽ tự động cấu hình một bean `MockMvc` trong context kiểm thử của bạn, bạn có thể `@Autowired` nó và sử dụng để điều khiển các bài kiểm thử của mình. `MockMvc` cho phép bạn thực hiện các request đến controller mà không cần một máy chủ đang chạy, giúp các bài kiểm thử nhanh và đáng tin cậy.

#### **Các lỗi thường gặp và hạn chế.**

1.  **Quên Mock các Phụ thuộc:** Đây là sai lầm số 1. Vì các bean `@Service` và `@Repository` không được tải, nếu controller của bạn phụ thuộc vào một service, bạn **bắt buộc** phải cung cấp một mock cho nó bằng `@MockBean`. Nếu bạn quên, bài kiểm thử của bạn sẽ thất bại với lỗi `NoSuchBeanDefinitionException` khi Spring cố gắng tiêm service vào controller.

2.  **Kiểm thử Logic Nghiệp vụ:** `@WebMvcTest` không dùng để kiểm thử logic nghiệp vụ bên trong service của bạn. Bài kiểm thử của bạn nên mock service, định nghĩa hành vi của nó (ví dụ: "khi `productService.findById(1L)` được gọi, thì trả về sản phẩm này"), và xác minh rằng controller hoạt động chính xác dựa trên hành vi đã được định nghĩa trước đó.

3.  **Quy tắc Bảo mật Đầy đủ:** Theo mặc định, `@WebMvcTest` cũng sẽ tự động cấu hình Spring Security. Điều này có nghĩa là nếu bạn có các quy tắc bảo mật, các request `MockMvc` của bạn có thể thất bại với lỗi 401 Unauthorized hoặc 403 Forbidden. Bạn thường cần phải tắt bảo mật một cách rõ ràng trong annotation (`@WebMvcTest(excludeAutoConfiguration = SecurityAutoConfiguration.class)`) hoặc sử dụng các annotation như `@WithMockUser` để mô phỏng một người dùng đã được xác thực.

#### **Ví dụ code kiểm thử và hành vi mong đợi.**

Hãy kiểm thử một `ProductController`.

**Controller:**
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
**`@WebMvcTest`:**
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

// Chỉ nhắm đến ProductController cho bài kiểm thử này
@WebMvcTest(ProductController.class)
class ProductControllerWebMvcTest {

    @Autowired
    private MockMvc mockMvc; // Điểm truy cập được tự động cấu hình cho các bài kiểm thử của chúng ta

    @MockBean // Tạo một mock của ProductService và thêm nó vào context
    private ProductService productService;

    @Test
    void whenProductExists_getProductById_returnsProductJsonAnd200() throws Exception {
        // 1. Arrange: Định nghĩa hành vi của mock
        Product product = new Product(1L, "Gaming Keyboard", 99.99);
        given(productService.findById(1L)).willReturn(product);

        // 2. Act & 3. Assert: Thực hiện request và xác minh response
        mockMvc.perform(get("/products/{id}", 1L))
                .andExpect(status().isOk())
                .andExpect(content().contentType("application/json"))
                .andExpect(jsonPath("$.id", is(1)))
                .andExpect(jsonPath("$.name", is("Gaming Keyboard")))
                .andExpect(jsonPath("$.price", is(99.99)));
    }

    @Test
    void whenProductDoesNotExist_getProductById_returns404() throws Exception {
        // 1. Arrange: Định nghĩa hành vi của mock cho một sản phẩm không tồn tại
        given(productService.findById(99L)).willThrow(new ProductNotFoundException("Product not found"));

        // 2. Act & 3. Assert
        mockMvc.perform(get("/products/{id}", 99L))
                .andExpect(status().isNotFound());
    }
}
```

**Hành vi mong đợi:**
*   Bài kiểm thử tải một context chỉ với `ProductController` và cơ sở hạ tầng MVC.
*   Trường `productService` trong instance `ProductController` thật được tiêm mock Mockito của chúng ta.
*   Bài kiểm thử đầu tiên thành công vì chúng ta đã bảo mock trả về một sản phẩm, và chúng ta khẳng định rằng controller đã tuần tự hóa sản phẩm này thành JSON một cách chính xác và trả về trạng thái 200 OK.
*   Bài kiểm thử thứ hai thành công vì chúng ta đã bảo mock ném ra một exception, và chúng ta khẳng định rằng controller (hoặc một `@ControllerAdvice`) đã chuyển đổi điều này thành trạng thái 404 Not Found một cách chính xác.

---

### **Các câu hỏi phỏng vấn**

1.  **Câu hỏi:** "Bạn đã viết một `@WebMvcTest` nhưng nó bị lỗi vì Spring không thể tìm thấy bean cho `UserService` của bạn. Bạn đã quên điều gì?"
    *   **Trả lời:** "Tôi đã quên khai báo `UserService` là một `@MockBean`. `@WebMvcTest` chỉ tải các thành phần của tầng web và không tải các bean `@Service`. Để đáp ứng việc tiêm phụ thuộc cho controller đang được kiểm thử, tôi phải cung cấp một implementation mock của service bằng cách sử dụng `@MockBean`."

2.  **Câu hỏi:** "Tại sao bạn lại chọn `@WebMvcTest` thay vì `@SpringBootTest` để kiểm thử một controller trả về một đối tượng JSON đơn giản?"
    *   **Trả lời:** "Vì hiệu suất và sự tập trung. `@WebMvcTest` nhanh hơn đáng kể vì nó tránh tải toàn bộ application context, bao gồm cả các tầng service và dữ liệu. Nó buộc phải có sự tách biệt rõ ràng về trách nhiệm, nơi tôi có thể kiểm thử các trách nhiệm của controller—như ánh xạ request, ràng buộc dữ liệu và tuần tự hóa—hoàn toàn cô lập khỏi logic nghiệp vụ, vốn nên được kiểm thử ở cấp độ unit test. Sử dụng `@SpringBootTest` cho việc này sẽ chậm và phức tạp một cách không cần thiết."

3.  **Câu hỏi:** "Bạn sẽ kiểm thử một endpoint tải tệp lên (file upload) bằng `@WebMvcTest` như thế nào?"
    *   **Trả lời:** "`MockMvc` hoàn toàn hỗ trợ kiểm thử việc tải tệp lên. Bạn sẽ sử dụng phương thức `MockMvcRequestBuilders.multipart()`. Điều này cho phép bạn xây dựng một request multipart, thêm các tệp vào đó bằng `file()`, và cũng có thể thêm các tham số request khác. Sau đó, bạn có thể thực hiện request này và khẳng định trạng thái và nội dung của response giống như bất kỳ endpoint nào khác."

---

### **Bài tập thực hành**

1.  **Mục tiêu:** Sử dụng `@WebMvcTest` để kiểm thử các quy tắc xác thực.
2.  **Cài đặt:**
    *   Trong dự án của bạn, tạo một lớp `ProductCreateDTO`.
    *   Thêm các trường `name` (String) và `price` (Double).
    *   Chú thích `name` bằng `@NotBlank` (không được null hoặc rỗng).
    *   Chú thích `price` bằng `@Positive` (phải lớn hơn không).
    *   Tạo một endpoint `POST /products` trong `ProductController` của bạn chấp nhận DTO này với annotation `@Valid`.
3.  **Nhiệm vụ của bạn:**
    *   Viết một phương thức kiểm thử mới trong lớp `@WebMvcTest` của bạn.
    *   **Trường hợp 1 (Tên không hợp lệ):** Sử dụng `MockMvc` để thực hiện một request POST đến `/products` với một body JSON trong đó `name` là một chuỗi rỗng (`""`). Khẳng định rằng mã trạng thái HTTP là **400 Bad Request**.
    *   **Trường hợp 2 (Giá không hợp lệ):** Thực hiện một request POST khác trong đó `price` là `-10`. Khẳng định rằng trạng thái là **400 Bad Request**.
    *   **Trường hợp 3 (Đầu vào hợp lệ):** Thực hiện một request POST với tên và giá hợp lệ. Bạn sẽ cần mock lời gọi đến tầng service (ví dụ: `productService.createProduct(...)`). Khẳng định rằng trạng thái là **201 Created**.

Bài tập này sẽ dạy bạn cách sử dụng `MockMvc` để kiểm thử đầu vào của controller và validation, một phần quan trọng của việc kiểm thử API.

***

Tiếp theo, chúng ta sẽ đi xuống một tầng trong ngăn xếp đến tầng persistence và khám phá `@DataJpaTest`.

***

#### **Chủ đề 2.2: `@DataJpaTest`**

#### **Nó tải phần nào của application context?**

`@DataJpaTest` là annotation kiểm thử lát cắt cho **tầng persistence**. Nó tập trung hoàn toàn vào việc kiểm thử các thành phần JPA. Nó được thiết kế để là cách nhanh nhất và dễ nhất để xác minh rằng các repository, entity và các câu truy vấn của bạn hoạt động chính xác.

**Những gì được tải:**
*   Các bean `@Repository` (cụ thể là các interface của bạn kế thừa `JpaRepository` hoặc các interface repository khác của Spring Data).
*   Các lớp `@Entity` của bạn (nó sẽ quét để tìm chúng).
*   Các bean cơ sở hạ tầng JPA cốt lõi như `EntityManagerFactory` và `TransactionManager`.
*   Một `DataSource` (thường được cấu hình cho một cơ sở dữ liệu trong bộ nhớ - in-memory).
*   Một bean đặc biệt gọi là `TestEntityManager`, một tiện ích hữu ích cho các bài kiểm thử persistence.

**Những gì KHÔNG được tải:**
*   Tầng web: `@Controller`, `@RestController`, `@ControllerAdvice`.
*   Tầng service và các thành phần chung khác: `@Service`, `@Component`.
*   Cấu hình bảo mật đầy đủ.

#### **Khi nào và tại sao nên sử dụng nó?**

Sử dụng `@DataJpaTest` bất cứ khi nào bạn cần kiểm thử bất cứ điều gì liên quan đến logic persistence cơ sở dữ liệu của bạn. Đây là công cụ lý tưởng để xác minh:

*   **Các thao tác CRUD của Repository:** `save()`, `findById()`, `delete()` có hoạt động như mong đợi không?
*   **Các truy vấn tùy chỉnh:** Các annotation `@Query` của bạn (sử dụng JPQL hoặc SQL gốc) có đúng cú pháp và trả về kết quả mong đợi không?
*   **Ánh xạ Entity:** Các mối quan hệ `@OneToMany`, `@ManyToOne`, v.v., của bạn có được cấu hình chính xác không? Các thao tác cascade có hoạt động không?
*   **Các ràng buộc Cơ sở dữ liệu:** Việc lưu một entity với một giá trị không duy nhất cho một trường duy nhất có ném ra `DataIntegrityViolationException` một cách chính xác không?
*   **Khóa Lạc quan (Optimistic Locking):** Annotation `@Version` của bạn có ngăn chặn được "dirty reads" không?

Bằng cách chỉ tập trung vào tầng persistence, `@DataJpaTest` cung cấp cho bạn một cách nhanh chóng và đáng tin cậy để xác nhận rằng tầng truy cập dữ liệu của bạn đang hoạt động chính xác như bạn dự định, mà không có chi phí của các tầng service hoặc web.

#### **Những phụ thuộc nào được tự động cấu hình?**

1.  **Cơ sở dữ liệu trong bộ nhớ (In-Memory Database):** Theo mặc định, `@DataJpaTest` sẽ cố gắng cấu hình và kết nối đến một cơ sở dữ liệu trong bộ nhớ như **H2, HSQLDB, hoặc Derby** nếu chúng có trên classpath. Nó tự động tạo schema cơ sở dữ liệu dựa trên các entity JPA của bạn. Điều này cực kỳ tiện lợi vì nó không yêu cầu cấu hình gì cả.

2.  **`TestEntityManager`:** Đây là một tiện ích tuyệt vời do Spring Boot cung cấp đặc biệt cho các bài kiểm thử JPA. Nó là một trình bao bọc (wrapper) quanh `EntityManager` tiêu chuẩn với các phương thức được đơn giản hóa cho bối cảnh kiểm thử. Nó hoàn hảo cho giai đoạn "Arrange" của bài kiểm thử, nơi bạn cần thiết lập trạng thái cơ sở dữ liệu.
    *   `persist(entity)`: Thêm một entity mới vào persistence context.
    *   `flush()`: Đồng bộ hóa persistence context với cơ sở dữ liệu (tức là, thực thi các câu lệnh SQL `INSERT`, `UPDATE`, `DELETE`). Điều này rất quan trọng vì Hibernate thường nhóm các thay đổi lại, và `flush()` buộc chúng phải được gửi đến CSDL để bạn có thể truy vấn chúng.
    *   `find(id)`: Lấy một entity từ cơ sở dữ liệu.
    *   `persistAndFlush(entity)`: Một phím tắt tiện lợi cho hai thao tác phổ biến nhất.

3.  **Rollback Giao dịch:** Như đã thảo luận trong phần Nền tảng, mỗi phương thức kiểm thử trong một lớp `@DataJpaTest` được bao bọc trong một transaction và sẽ được **rollback** vào cuối. Điều này cung cấp sự cô lập kiểm thử hoàn hảo, đảm bảo rằng cơ sở dữ liệu sạch sẽ trước khi phương thức kiểm thử tiếp theo chạy.

#### **Các lỗi thường gặp và hạn chế.**

1.  **Sự khác biệt giữa dialect của CSDL In-Memory và CSDL Thật:** Một cơ sở dữ liệu H2 có thể hoạt động hơi khác so với cơ sở dữ liệu sản xuất của bạn (ví dụ: PostgreSQL hoặc MySQL). Một câu truy vấn native hoạt động trong H2 có thể thất bại trong PostgreSQL do sự khác biệt tinh tế về cú pháp SQL hoặc tên hàm. Đây là hạn chế lớn nhất của thiết lập mặc định. (Chúng ta sẽ giải quyết vấn đề này sau với Testcontainers).

2.  **Quên `flush()`:** Một lỗi rất phổ biến là lưu một entity bằng `TestEntityManager.persist()` và sau đó ngay lập tức cố gắng tìm nó bằng một truy vấn repository. Truy vấn có thể thất bại vì câu lệnh `INSERT` vẫn chưa thực sự được thực thi trên cơ sở dữ liệu. Bạn phải gọi `entityManager.flush()` để buộc các thay đổi được ghi lại.

3.  **Kiểm thử Service:** Không sử dụng `@DataJpaTest` để kiểm thử logic tầng service của bạn. Nếu service của bạn có logic giao dịch (`@Transactional`), hành vi của nó có thể khác với hành vi giao dịch của bài kiểm thử. Nếu bạn cần kiểm thử sự tương tác của một service với một repository, thường thì tốt hơn là viết một integration test riêng bằng `@SpringBootTest` hoặc đơn giản là import service vào `@DataJpaTest` của bạn bằng `@Import(MyService.class)`.

#### **Ví dụ code kiểm thử và hành vi mong đợi.**

Hãy kiểm thử một `ProductRepository` có một truy vấn tùy chỉnh.

**Repository:**
```java
public interface ProductRepository extends JpaRepository<Product, Long> {
    // Truy vấn JPQL tùy chỉnh
    List<Product> findByPriceGreaterThan(double price);
}
```

**`@DataJpaTest`:**
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
    private TestEntityManager entityManager; // Helper để lưu trữ dữ liệu

    @Autowired
    private ProductRepository productRepository; // Repository chúng ta đang kiểm thử

    @Test
    void whenSaved_findById_returnsProduct() {
        // 1. Arrange
        Product product = new Product(null, "Test Product", 10.0);
        entityManager.persistAndFlush(product); // Lưu vào DB và buộc đồng bộ

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

**Hành vi mong đợi:**
*   Spring Boot phát hiện H2 trên classpath và khởi động một cơ sở dữ liệu trong bộ nhớ mới, trống cho lớp kiểm thử này.
*   Nó quét entity `Product` của chúng ta và tạo bảng `PRODUCT` tương ứng.
*   Đối với mỗi phương thức kiểm thử:
    *   Một transaction mới bắt đầu.
    *   `entityManager` lưu dữ liệu trực tiếp vào cơ sở dữ liệu.
    *   `productRepository` được gọi, thực thi SQL thật trên cơ sở dữ liệu trong bộ nhớ.
    *   Các khẳng định (assertions) thành công.
    *   Transaction được rollback, xóa sạch tất cả dữ liệu đã được chèn vào.
*   Phương thức kiểm thử tiếp theo bắt đầu với một cơ sở dữ liệu hoàn toàn trống.

---

### **Các câu hỏi phỏng vấn**

1.  **Câu hỏi:** "`TestEntityManager` là gì và tại sao nó hữu ích?"
    *   **Trả lời:** "`TestEntityManager` là một lớp tiện ích do Spring Boot cung cấp cho các bài kiểm thử JPA. Nó là một trình bao bọc quanh `EntityManager` tiêu chuẩn nhưng với các phương thức được đơn giản hóa cho việc kiểm thử. Nó chủ yếu được sử dụng trong giai đoạn 'arrange' của một bài kiểm thử để thiết lập trạng thái cơ sở dữ liệu bằng cách lưu trữ các entity. Phương thức `flush()` của nó rất quan trọng để đảm bảo rằng bất kỳ thay đổi nào trong persistence context đều được ghi vào cơ sở dữ liệu trước khi giai đoạn 'act' của bài kiểm thử bắt đầu."

2.  **Câu hỏi:** "Ưu và nhược điểm của việc sử dụng cơ sở dữ liệu trong bộ nhớ H2 mặc định với `@DataJpaTest` là gì?"
    *   **Trả lời:** "**Ưu điểm** chính là tốc độ và sự tiện lợi. Nó không yêu cầu cấu hình và chạy rất nhanh, làm cho nó tuyệt vời để có phản hồi nhanh trong quá trình phát triển. **Nhược điểm** chính là H2 không phải là một sự thay thế hoàn hảo cho một cơ sở dữ liệu sản xuất như PostgreSQL hoặc Oracle. Có thể có những khác biệt tinh tế về các dialect SQL, hỗ trợ hàm, và xử lý ràng buộc, điều đó có nghĩa là một bài kiểm thử thành công trên H2 vẫn có thể thất bại trong môi trường sản xuất. Rủi ro này có thể được giảm thiểu bằng cách sử dụng Testcontainers."

3.  **Câu hỏi:** "`@DataJpaTest` của tôi bị lỗi `LazyInitializationException`. Chuyện gì đang xảy ra và làm thế nào để khắc phục?"
    *   **Trả lời:** "`LazyInitializationException` xảy ra khi bạn cố gắng truy cập một collection hoặc mối quan hệ được tải lười (lazily-loaded) trên một entity sau khi session Hibernate mà nó được tải vào đã bị đóng. Trong một `@DataJpaTest`, session được gắn với phương thức kiểm thử `@Transactional`. Exception có thể xảy ra nếu bạn trả về một entity từ một lời gọi repository và sau đó cố gắng truy cập một trường lazy *bên ngoài* phạm vi giao dịch đó hoặc sau khi session được flush và clear. Để khắc phục, bạn có thể thay đổi kiểu fetch thành `EAGER` (không phải lúc nào cũng được khuyến nghị), sử dụng `JOIN FETCH` trong câu truy vấn của bạn để tải dữ liệu một cách rõ ràng, hoặc thực hiện khẳng định chạm đến trường lazy trong cùng một phương thức kiểm thử, đảm bảo rằng transaction vẫn còn hoạt động."

***

Tiếp theo, chúng ta sẽ xem xét hai annotation kiểm thử lát cắt chuyên biệt hơn: `@JsonTest` và `@RestClientTest`.

***

#### **Chủ đề 2.3: `@JsonTest`**

#### **Nó tải phần nào của application context?**

`@JsonTest` là một annotation kiểm thử lát cắt rất chuyên biệt và nhẹ. Mục đích duy nhất của nó là kiểm thử việc **tuần tự hóa và giải tuần tự hóa JSON** của các đối tượng của bạn. Nó hoàn hảo để xác minh rằng các DTO hoặc Entity của bạn được chuyển đổi sang và từ JSON chính xác như bạn mong đợi.

**Những gì được tải:**
*   Một bean `ObjectMapper` (Jackson) hoặc `Gson`, tùy thuộc vào cấu hình của bạn. Đây là công cụ cốt lõi để xử lý JSON.
*   Bất kỳ bean `@JsonComponent` nào bạn đã định nghĩa. Đây là các bộ tuần tự hóa và giải tuần tự hóa tùy chỉnh mà `@JsonTest` sẽ nhận diện và đăng ký với `ObjectMapper`.
*   Các tiện ích kiểm thử JSON như `JacksonTester`, `GsonTester`, hoặc `JsonbTester`.

**Những gì KHÔNG được tải:**
*   Hầu như mọi thứ khác. Không có controller, không có service, không có repository, không có cơ sở dữ liệu. Điều này làm cho `@JsonTest` cực kỳ nhanh.

#### **Khi nào và tại sao nên sử dụng nó?**

Bạn nên sử dụng `@JsonTest` khi bạn cần xác minh cấu trúc chính xác của JSON của mình. Điều này đặc biệt hữu ích khi:

*   Bạn có logic tuần tự hóa tùy chỉnh (ví dụ: định dạng ngày theo một cách cụ thể như `yyyy-MM-dd`).
*   Bạn đang sử dụng các annotation của Jackson như `@JsonFormat`, `@JsonProperty`, `@JsonIgnore`, hoặc `@JsonView`.
*   Bạn muốn đảm bảo hợp đồng API của mình ổn định và những thay đổi đối với một DTO không vô tình phá vỡ định dạng JSON mà client mong đợi.
*   Bạn đã viết một `@JsonComponent` tùy chỉnh (ví dụ: một bộ tuần tự hóa tùy chỉnh cho một đối tượng phức tạp) và muốn unit test nó một cách cô lập.

Nó cho phép bạn kiểm thử tầng ánh xạ JSON mà không có chi phí của tầng web (`@WebMvcTest`) hoặc toàn bộ ứng dụng (`@SpringBootTest`).

#### **Những phụ thuộc nào được tự động cấu hình?**

Spring Boot tự động cấu hình một đối tượng kiểm thử dựa trên thư viện ánh xạ JSON của bạn. Phổ biến nhất là **`JacksonTester<T>`**. Đây là một trình bao bọc cung cấp các phương thức khẳng định tiện lợi để kiểm thử JSON.

*   `jacksonTester.write(object)`: Tuần tự hóa một đối tượng thành JSON.
*   `jacksonTester.parse(jsonString)`: Giải tuần tự hóa một chuỗi JSON thành một đối tượng.
*   Nó tích hợp liền mạch với các thư viện khẳng định như AssertJ, cho phép bạn kiểm tra các đường dẫn JSON cụ thể.

#### **Các lỗi thường gặp và hạn chế.**

1.  **Chỉ kiểm thử một chiều:** Một sai lầm phổ biến là chỉ kiểm thử việc tuần tự hóa (đối tượng sang JSON) mà quên kiểm thử việc giải tuần tự hóa (JSON sang đối tượng). Một trường có thể tuần tự hóa đúng nhưng lại không thể giải tuần tự hóa nếu nó chỉ đọc (read-only) hoặc thiếu một setter phù hợp. Luôn luôn kiểm thử cả hai chiều.
2.  **Kiểm thử sai mục đích:** Không sử dụng `@JsonTest` để kiểm thử logic của controller. Nó không biết gì về HTTP. Công việc duy nhất của nó là kiểm tra việc chuyển đổi của một đối tượng duy nhất.
3.  **Giả định `ObjectMapper` mặc định:** Nếu ứng dụng của bạn có một bean `ObjectMapper` được tùy chỉnh cao (ví dụ: với nhiều module tùy chỉnh được đăng ký), `@JsonTest` có thể không nhận diện được cấu hình chính xác đó trừ khi nó được cấu hình thông qua các thuộc tính tiêu chuẩn hoặc các `@JsonComponent`.

#### **Ví dụ code kiểm thử và hành vi mong đợi.**

Hãy tưởng tượng một `ProductDto` nơi chúng ta muốn kiểm soát định dạng ngày.

**DTO:**
```java
public class ProductDto {
    private String name;
    private double price;

    @JsonFormat(shape = JsonFormat.Shape.STRING, pattern = "yyyy-MM-dd'T'HH:mm:ssZ")
    private Date creationDate;

    // Constructors, getters, setters...
}
```

**`@JsonTest`:**
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
    private JacksonTester<ProductDto> json; // Tester được tự động cấu hình

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

        // Xác minh ngày đã được phân tích cú pháp chính xác
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd'T'HH:mm:ssZ");
        assertThat(sdf.format(result.getCreationDate())).isEqualTo("2025-10-26T10:00:00+0000");
    }
}
```
---

### **Chủ đề 2.4: `@RestClientTest`**

#### **Nó tải phần nào của application context?**

`@RestClientTest` là lát cắt kiểm thử cho **phía client** của một giao tiếp REST. Nó được sử dụng để kiểm thử code *gọi* một dịch vụ REST bên ngoài, thường sử dụng `RestTemplate` hoặc `RestClient` mới hơn.

**Những gì được tải:**
*   Một `ObjectMapper` để xử lý JSON.
*   Hỗ trợ cho `RestTemplateBuilder` và `RestClient.Builder`.
*   Một máy chủ mock đặc biệt (`MockRestServiceServer`) chặn các request đi ra.
*   Các interface được chú thích `@RestClient` của bạn (nếu sử dụng declarative client).

**Những gì KHÔNG được tải:**
*   Tầng web của bạn (`@RestController`, v.v.). Bạn không kiểm thử máy chủ *của bạn*, bạn đang kiểm thử *client của bạn*.
*   Các tầng service và repository.

#### **Khi nào và tại sao nên sử dụng nó?**

Sử dụng `@RestClientTest` khi bạn có một service hoặc component trong ứng dụng của mình cần giao tiếp với một API REST của bên thứ ba. Điều này cho phép bạn:

*   Kiểm thử REST client của bạn một cách hoàn toàn cô lập mà không thực hiện các cuộc gọi mạng thực tế.
*   Xác minh rằng client của bạn gửi request đến đúng URL với đúng phương thức HTTP và header.
*   Đảm bảo rằng body của request được tuần tự hóa thành JSON một cách chính xác.
*   Mô phỏng các phản hồi khác nhau của máy chủ (200 OK, 404 Not Found, 500 Internal Server Error) để kiểm tra cách client của bạn xử lý cả kịch bản thành công và thất bại.
*   Xác minh rằng client của bạn giải tuần tự hóa phản hồi JSON từ dịch vụ bên ngoài một cách chính xác.

#### **Những phụ thuộc nào được tự động cấu hình?**

Thành phần quan trọng là **`MockRestServiceServer`**. Bean này được tự động tạo và liên kết với `RestTemplate` của bạn. Khi code client của bạn cố gắng thực hiện một cuộc gọi HTTP, máy chủ mock này sẽ chặn nó lại.

Sau đó, bạn có thể sử dụng API fluent của nó để:
1.  **Đặt kỳ vọng (Set Expectations):** Định nghĩa loại request bạn mong đợi client của mình thực hiện (`expect(requestTo("/api/users/1"))`).
2.  **Định nghĩa một phản hồi (Define a Response):** Chỉ định phản hồi mà máy chủ mock nên trả về khi nó thấy request đó (`andRespond(withSuccess(jsonResponse, MediaType.APPLICATION_JSON))`).

#### **Các lỗi thường gặp và hạn chế.**

1.  **Nhắm sai Component:** Bạn phải chỉ định client nào bạn đang kiểm thử trong annotation, ví dụ: `@RestClientTest(MyApiClient.class)`. Nếu không, Spring có thể không thể kết nối máy chủ mock một cách chính xác.
2.  **Kiểm thử Controller của chính bạn:** Một điểm gây nhầm lẫn thường xuyên. `@RestClientTest` **không** dùng để kiểm thử các `@RestController` của chính bạn. Nó dùng để kiểm thử code *gọi các dịch vụ khác*. Sử dụng `@WebMvcTest` hoặc `@SpringBootTest` cho các controller của bạn.
3.  **Sử dụng `WebClient`:** `@RestClientTest` được thiết kế cho `RestTemplate`/`RestClient` đồng bộ. Để kiểm thử `WebClient` bất đồng bộ (reactive), bạn nên sử dụng một công cụ khác, chẳng hạn như `MockWebServer` của `OkHttp`.

#### **Ví dụ code kiểm thử và hành vi mong đợi.**

Hãy kiểm thử một client lấy dữ liệu người dùng từ một API bên ngoài.

**Client:**
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

// Một DTO để chứa dữ liệu phản hồi
public class UserData { ... }
```

**`@RestClientTest`:**
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

// Chúng ta chỉ định rằng chúng ta đang kiểm thử UserApiClient
@RestClientTest(UserApiClient.class)
class UserApiClientTest {

    @Autowired
    private UserApiClient userApiClient; // Bean client thực tế mà chúng ta đang kiểm thử

    @Autowired
    private MockRestServiceServer server; // Máy chủ mock sẽ chặn các cuộc gọi

    @Autowired
    private ObjectMapper objectMapper; // Helper để tạo chuỗi JSON

    @Test
    void whenGetUserById_callsCorrectUrlAndReturnsUser() throws Exception {
        // 1. Arrange: Chuẩn bị phản hồi mong đợi từ máy chủ mock
        UserData expectedUser = new UserData("1", "John Doe");
        String expectedJsonResponse = objectMapper.writeValueAsString(expectedUser);

        // Thiết lập kỳ vọng của máy chủ mock
        this.server.expect(requestTo("https://api.example.com/users/1"))
                .andRespond(withSuccess(expectedJsonResponse, MediaType.APPLICATION_JSON));

        // 2. Act: Gọi phương thức trên client của chúng ta
        UserData actualUser = userApiClient.getUserById("1");

        // 3. Assert: Xác minh kết quả và rằng máy chủ mock đã được gọi
        this.server.verify(); // Làm bài kiểm thử thất bại nếu request mong đợi không bao giờ được thực hiện
        assertThat(actualUser.getName()).isEqualTo("John Doe");
    }
}
```
---

### **Các câu hỏi phỏng vấn**

1.  **Câu hỏi:** "Bạn có một DTO với một thuộc tính `@JsonIgnore`. Annotation kiểm thử lát cắt nào là tốt nhất và nhanh nhất để xác minh rằng thuộc tính này thực sự bị loại trừ khỏi đầu ra JSON?"
    *   **Trả lời:** "`@JsonTest` là công cụ hoàn hảo cho việc này. Nó cung cấp phản hồi nhanh nhất vì nó chỉ tải cơ sở hạ tầng JSON (`ObjectMapper`) và một `JacksonTester`. Tôi có thể viết một bài kiểm thử đơn giản để tuần tự hóa DTO và sử dụng `assertThat(json).doesNotHaveJsonPath("$.ignoredField")` để xác minh thuộc tính bị thiếu, xác nhận rằng annotation `@JsonIgnore` đang hoạt động."

2.  **Câu hỏi:** "Vai trò của `MockRestServiceServer` trong một `@RestClientTest` là gì?"
    *   **Trả lời:** "`MockRestServiceServer` hoạt động như một máy chủ giả mạo chặn các request HTTP đi ra được thực hiện bởi `RestTemplate` đang được kiểm thử. Thay vì thực hiện một cuộc gọi mạng thực sự, request được gửi đến máy chủ mock này. Sau đó, chúng ta có thể sử dụng API của nó để đặt kỳ vọng vào request (ví dụ: xác minh URL, phương thức và header) và để cung cấp một phản hồi giả lập (stubbed response). Điều này cho phép chúng ta kiểm thử hành vi của REST client một cách cô lập và mô phỏng các phản hồi API khác nhau, bao gồm cả lỗi, mà không cần bất kỳ phụ thuộc bên ngoài nào."

***

Phần này kết thúc chuyến tham quan của chúng ta về các annotation kiểm thử lát cắt chính. Tiếp theo, chúng ta sẽ đi sâu vào **Kiểm thử Cơ sở dữ liệu với JPA**, vượt ra ngoài cơ sở dữ liệu trong bộ nhớ H2 để đến với các giải pháp mạnh mẽ hơn như Testcontainers.