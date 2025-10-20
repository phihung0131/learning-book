### **Phần 3: Kiểm thử Cơ sở dữ liệu với JPA**

Chúng ta đã thấy cách `@DataJpaTest` cung cấp một lát cắt tuyệt vời để kiểm thử tầng persistence. Bây giờ, hãy đi sâu hơn vào quyết định quan trọng nhất bạn sẽ đưa ra khi kiểm thử cơ sở dữ liệu của mình: kiểm thử trên loại cơ sở dữ liệu nào.

#### **Chủ đề 3.1: Cơ sở dữ liệu trong bộ nhớ H2 so với Testcontainers**

Đây là một trong những khác biệt chuyên nghiệp quan trọng nhất trong kiểm thử tích hợp hiện đại. Lựa chọn của bạn ở đây ảnh hưởng trực tiếp đến độ tin cậy và sự tự tin mà các bài kiểm thử của bạn mang lại.

**Lý do chọn H2 (và các cơ sở dữ liệu trong bộ nhớ khác)**

*   **Ưu điểm:**
    *   **Cực kỳ nhanh:** Nó chạy trong bộ nhớ của JVM. Không có chi phí mạng, không có Docker container cần khởi động. Các bài kiểm thử thực thi trong vài mili giây.
    *   **Không cần cấu hình:** Chỉ cần thêm phụ thuộc H2 vào `pom.xml` hoặc `build.gradle` của bạn, và `@DataJpaTest` sẽ tự động xử lý phần còn lại. Đó là con đường ít trở ngại nhất.
    *   **Tốt cho các trường hợp đơn giản:** Đối với các repository CRUD cơ bản và các truy vấn JPQL đơn giản tuân thủ nghiêm ngặt tiêu chuẩn JPA, H2 hoạt động hoàn hảo.

*   **Nhược điểm (Khoảng cách về độ trung thực - "Fidelity Gap"):**
    *   **Nó không phải là Cơ sở dữ liệu Production của bạn:** Đây là thiếu sót nghiêm trọng. H2 không phải là PostgreSQL. Nó không phải là MySQL. Nó không phải là Oracle. Điều này tạo ra một "khoảng cách về độ trung thực", nơi các bài kiểm thử của bạn có thể thành công, nhưng code vẫn có thể thất bại trong môi trường production.
    *   **Sự khác biệt về SQL Dialect:** Một câu truy vấn SQL gốc được viết cho các tính năng nâng cao của PostgreSQL (như các hàm `JSONB`, window functions, hoặc CTEs) gần như chắc chắn sẽ thất bại trong H2.
    *   **Sự không khớp về Hàm:** Ngay cả các hàm đơn giản cũng có thể khác nhau. `TRUNCATE` có thể hoạt động khác. Tên và hành vi của các hàm ngày/giờ có thể thay đổi.
    *   **Sự khác biệt về Ràng buộc và Kiểu dữ liệu:** H2 có thể không hỗ trợ chính xác các kiểu dữ liệu giống nhau hoặc thực thi các ràng buộc (như các ràng buộc có thể trì hoãn - deferrable constraints) theo cách tương tự như cơ sở dữ liệu production của bạn.

**Lý do chọn Testcontainers**

Testcontainers là một thư viện Java cho phép bạn điều khiển các Docker container nhẹ, dùng một lần một cách có lập trình. Đối với kiểm thử cơ sở dữ liệu, điều này có nghĩa là bạn có thể khởi động một **cơ sở dữ liệu PostgreSQL, MySQL thật, hoặc bất kỳ cơ sở dữ liệu nào khác** trong một Docker container để các bài kiểm thử của bạn chạy trên đó.

*   **Ưu điểm:**
    *   **Độ trung thực tối đa:** Bạn có thể kiểm thử trên *chính xác cùng một cơ sở dữ liệu và phiên bản* mà bạn sử dụng trong production (ví dụ: `PostgreSQL:14.5`). Điều này loại bỏ khoảng cách về độ trung thực. Nếu một câu truy vấn gốc hoạt động trong bài kiểm thử của bạn, bạn có thể tự tin cao rằng nó sẽ hoạt động trong production.
    *   **Tính thực tế:** Nó kiểm thử toàn bộ ngăn xếp, bao gồm cả trình điều khiển JDBC và bất kỳ cấu hình dành riêng cho cơ sở dữ liệu nào.
    *   **Hỗ trợ đầy đủ tính năng:** Tất cả các tính năng của cơ sở dữ liệu của bạn (JSONB, PostGIS, v.v.) đều có sẵn để bạn sử dụng và kiểm thử.

*   **Nhược điểm:**
    *   **Chi phí hiệu suất:** Khởi động một Docker container lần đầu tiên không phải là tức thời. Nó có thể thêm vài giây (hoặc hơn) vào lần khởi động ban đầu của bộ kiểm thử của bạn.
    *   **Cài đặt phức tạp hơn:** Nó yêu cầu Docker phải được cài đặt trên máy của lập trình viên và trong môi trường CI. Cấu hình ban đầu phức tạp hơn so với việc chỉ thêm một phụ thuộc Maven.
    *   **Sử dụng tài nguyên:** Nó tiêu thụ nhiều bộ nhớ và CPU hơn so với cơ sở dữ liệu trong bộ nhớ.

**Khuyến nghị chuyên nghiệp:**
Đối với bất kỳ microservice nghiêm túc, cấp độ production nào, **hãy sử dụng Testcontainers**. Chi phí cài đặt ban đầu là một cái giá nhỏ phải trả cho sự tự tin to lớn bạn có được bằng cách kiểm thử trên một cơ sở dữ liệu thật. Chỉ sử dụng H2 cho các dự án rất đơn giản hoặc tạo mẫu nhanh.

---

#### **Chủ đề 3.2: Cấu hình Testcontainers và Đảm bảo Hiệu suất Khởi động**

Hãy cùng thực hiện việc thiết lập một container PostgreSQL cho các bài `@DataJpaTest` của chúng ta.

**Bước 1: Thêm các Phụ thuộc**

Bạn sẽ cần ba phụ thuộc trong `pom.xml` của mình (trong `<scope>test</scope>`):

```xml
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>postgresql</artifactId>
    <version>1.19.7</version> <!-- Sử dụng phiên bản mới nhất -->
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.testcontainers</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>1.19.7</version>
    <scope>test</scope>
</dependency>
<!-- Đảm bảo bạn cũng có trình điều khiển PostgreSQL thông thường -->
<dependency>
    <groupId>org.postgresql</groupId>
    <artifactId>postgresql</artifactId>
    <scope>runtime</scope>
</dependency>
```

**Bước 2: Tạo một Cấu hình Container có thể tái sử dụng (Mô hình Singleton)**

Kẻ giết chết hiệu suất lớn nhất là khởi động một container mới cho mỗi lớp kiểm thử. Cách thực hành tốt nhất là khởi động container **một lần duy nhất** cho toàn bộ lần chạy bộ kiểm thử. Chúng ta có thể đạt được điều này với một lớp cơ sở hoặc một interface.

```java
// AbstractIntegrationTest.java
import org.springframework.test.context.DynamicPropertyRegistry;
import org.springframework.test.context.DynamicPropertySource;
import org.testcontainers.containers.PostgreSQLContainer;
import org.testcontainers.junit.jupiter.Container;
import org.testcontainers.junit.jupiter.Testcontainers;

// Annotation này không thực sự cần thiết trên lớp cơ sở nhưng giúp làm rõ ràng
@Testcontainers
public abstract class AbstractIntegrationTest {

    // Điều này sẽ tạo một container PostgreSQL cho tất cả các bài kiểm thử kế thừa lớp này.
    // Từ khóa "static" là phép màu làm cho nó trở thành một singleton.
    // Nó sẽ được khởi động một lần trước khi bất kỳ bài kiểm thử nào trong bất kỳ lớp kế thừa nào chạy,
    // và dừng lại sau khi bài kiểm thử cuối cùng trong lớp kế thừa cuối cùng đã chạy xong.
    @Container
    private static final PostgreSQLContainer<?> postgresqlContainer =
            new PostgreSQLContainer<>("postgres:14.5");

    // Phương thức này đặt các thuộc tính Spring một cách động để kết nối đến
    // cơ sở dữ liệu của testcontainer.
    @DynamicPropertySource
    private static void registerDatasourceProperties(DynamicPropertyRegistry registry) {
        registry.add("spring.datasource.url", postgresqlContainer::getJdbcUrl);
        registry.add("spring.datasource.username", postgresqlContainer::getUsername);
        registry.add("spring.datasource.password", postgresqlContainer::getPassword);
    }
}
```

**Giải thích về Phép màu:**

*   `@Testcontainers`: Annotation JUnit 5 này từ thư viện Testcontainers kích hoạt extension quản lý vòng đời của các container của chúng ta.
*   `@Container`: Annotation này đánh dấu một trường là một tài nguyên được Testcontainers quản lý.
*   `static`: Đây là phần quan trọng. Bằng cách làm cho trường `PostgreSQLContainer` là `static`, chúng ta đang nói với Testcontainers hãy quản lý nó như một singleton. Nó sẽ chỉ được khởi động một lần và được chia sẻ trên tất cả các bài kiểm thử.
*   `@DynamicPropertySource`: Annotation mạnh mẽ này của Spring cho phép chúng ta ghi đè các thuộc tính ứng dụng một cách động *sau khi* container đã khởi động. Chúng ta lấy URL JDBC, tên người dùng và mật khẩu ngẫu nhiên từ container và tiêm chúng vào cấu hình `DataSource` của Spring. Đây là cách hiện đại, sạch sẽ để kết nối Spring với một Testcontainer.

**Bước 3: Viết Lớp Kiểm thử**

Bây giờ, lớp kiểm thử của bạn trở nên rất đơn giản. Nó chỉ cần kế thừa lớp cơ sở của chúng ta.

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
// Annotation này CỰC KỲ QUAN TRỌNG. Nó bảo Spring Boot KHÔNG thay thế
// datasource của chúng ta bằng một datasource trong bộ nhớ. Chúng ta muốn sử dụng
// cái mà chúng ta đã cấu hình bằng @DynamicPropertySource.
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
class ProductRepositoryPostgresTest extends AbstractIntegrationTest {

    @Autowired
    private ProductRepository productRepository;

    @Test
    void whenSaved_findById_returnsProductFromRealPostgres() {
        // Arrange
        Product product = new Product(null, "Test Product", 10.0);

        // Act
        Product savedProduct = productRepository.save(product);
        Product foundProduct = productRepository.findById(savedProduct.getId()).orElse(null);

        // Assert
        assertThat(foundProduct).isNotNull();
        assertThat(foundProduct.getName()).isEqualTo("Test Product");
    }
}
```

Khi bạn chạy bài kiểm thử này:
1.  Testcontainers sẽ khởi động Docker container `postgres:14.5` (điều này có thể mất vài giây lần đầu tiên).
2.  `@DynamicPropertySource` sẽ cấu hình Spring để kết nối đến container đang chạy này.
3.  `@DataJpaTest` sẽ tải context JPA. Theo mặc định, `spring.jpa.hibernate.ddl-auto` được đặt thành `create-drop` trong các bài kiểm thử, vì vậy Hibernate sẽ tạo schema trong container PostgreSQL của bạn.
4.  Bài kiểm thử sẽ chạy, thực thi SQL thật trên cơ sở dữ liệu PostgreSQL.
5.  Transaction của phương thức kiểm thử sẽ được rollback.
6.  Nếu bạn có một lớp kiểm thử khác kế thừa `AbstractIntegrationTest`, nó sẽ **tái sử dụng container đã đang chạy**, làm cho việc khởi động của nó trở nên tức thời.
7.  Sau khi tất cả các bài kiểm thử kết thúc, Testcontainers sẽ tự động dừng và xóa container.

---

### **Các câu hỏi phỏng vấn**

1.  **Câu hỏi:** "Bạn đã quyết định sử dụng Testcontainers. Điều quan trọng nhất bạn cần làm để đảm bảo bộ kiểm thử của bạn không trở nên cực kỳ chậm là gì?"
    *   **Trả lời:** "Tối ưu hóa quan trọng nhất là sử dụng **mô hình container singleton**. Điều này có nghĩa là khai báo instance của container là một trường `static` trong một lớp cơ sở hoặc cấu hình được chia sẻ. Điều này đảm bảo Docker container chỉ được khởi động một lần duy nhất cho toàn bộ lần chạy bộ kiểm thử và được tái sử dụng trên tất cả các lớp kiểm thử, loại bỏ chi phí khởi động tốn kém cho mỗi lớp."

2.  **Câu hỏi:** "Làm thế nào để bạn kết nối ứng dụng Spring Boot của mình với một cơ sở dữ liệu Testcontainer khởi động trên một cổng ngẫu nhiên và với thông tin đăng nhập ngẫu nhiên?"
    *   **Trả lời:** "Cách thực hành tốt nhất là sử dụng annotation `@DynamicPropertySource` trong lớp kiểm thử. Điều này cung cấp một `DynamicPropertyRegistry` cho phép tôi thêm hoặc ghi đè các thuộc tính của Spring *sau khi* container đã được khởi động. Tôi có thể gọi các phương thức như `container.getJdbcUrl()` và `container.getUsername()` và đăng ký chúng với registry, sau đó registry sẽ cấu hình `DataSource` của Spring trước khi application context được làm mới hoàn toàn."

3.  **Câu hỏi:** "Annotation `@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)` làm gì, và tại sao nó lại cần thiết khi sử dụng Testcontainers với `@DataJpaTest`?"
    *   **Trả lời:** "Theo mặc định, `@DataJpaTest` cố gắng hữu ích bằng cách thay thế `DataSource` đã được cấu hình của ứng dụng bằng một kết nối đến một cơ sở dữ liệu nhúng, trong bộ nhớ như H2. Cài đặt `replace = AutoConfigureTestDatabase.Replace.NONE` vô hiệu hóa hành vi này một cách rõ ràng. Điều này rất cần thiết khi sử dụng Testcontainers vì nó nói với Spring Boot, 'Đừng thay thế `DataSource` của tôi; tôi muốn bạn sử dụng cái mà tôi đã cấu hình thủ công để trỏ đến Docker container của tôi.'"

***

Tiếp theo, chúng ta sẽ đề cập chi tiết hơn về hành vi rollback giao dịch và thảo luận cách kiểm thử các kịch bản JPA cụ thể như ràng buộc và mối quan hệ thực thể.

***

#### **Chủ đề 3.3: Hành vi Rollback và Vai trò của `@Transactional` trong các Bài kiểm thử**

Chúng ta biết rằng các phương thức của `@DataJpaTest` là transactional và mặc định sẽ rollback. Đây là nền tảng của sự cô lập trong kiểm thử. Hãy khám phá cơ chế này chi tiết hơn và phát hiện ra một "cái bẫy" rất phổ biến.

#### **Lý thuyết: Cách Rollback Hoạt động**

Khi một phương thức kiểm thử được chú thích bằng `@Transactional` (được bao gồm ngầm trong `@DataJpaTest`) bắt đầu, Spring TestContext Framework sẽ bắt đầu một transaction. Tất cả các hoạt động cơ sở dữ liệu bạn thực hiện thông qua JPA/Hibernate trong phương thức kiểm thử đó đều diễn ra bên trong transaction duy nhất này.

Khi phương thức kiểm thử kết thúc, framework **không commit** transaction. Thay vào đó, nó đưa ra một lệnh `ROLLBACK` đến cơ sở dữ liệu. Điều này thực sự xóa mọi thay đổi được thực hiện trong quá trình kiểm thử, đảm bảo bài kiểm thử tiếp theo bắt đầu với một "bảng sạch".

**Ghi đè Hành vi Mặc định**

Đôi khi, bạn có thể muốn vô hiệu hóa hành vi rollback này.
*   **Gỡ lỗi (Debugging):** Bạn muốn kiểm tra cơ sở dữ liệu bằng một công cụ như `psql` hoặc một client GUI *sau khi* bài kiểm thử chạy để xem chính xác những gì đã được chèn vào.
*   **Logic Giao dịch Cụ thể:** Bạn đang kiểm thử một tính năng dựa trên việc dữ liệu được commit (ví dụ: một trigger được kích hoạt khi commit).

Bạn có thể kiểm soát điều này bằng hai annotation:
*   `@Commit`: Một annotation đơn giản báo cho framework hãy commit transaction thay vì rollback.
*   `@Rollback(false)`: Một phương án thay thế cho `@Commit`. `true` là mặc định.

```java
@DataJpaTest
class UserRepositoryCommitTest extends AbstractIntegrationTest {

    @Autowired private UserRepository userRepository;

    @Test
    @Commit // hoặc @Rollback(false)
    void shouldCommitTransaction() {
        userRepository.save(new User(null, "permanent_user"));
        // Sau khi bài kiểm thử này chạy, "permanent_user" sẽ thực sự có trong cơ sở dữ liệu
        // và sẽ KHÔNG được dọn dẹp, điều này có khả năng gây ra lỗi cho các bài kiểm thử sau đó.
    }
}
```
**Cảnh báo:** Sử dụng tính năng này một cách tiết kiệm và thận trọng. Một bài kiểm thử commit dữ liệu sẽ phá vỡ nguyên tắc cô lập và có thể làm cho bộ kiểm thử của bạn trở nên mong manh và phụ thuộc vào thứ tự.

#### **Cái bẫy giữa `@Transactional` và Kiểm thử `RANDOM_PORT`**

Đây là một trong những nguồn gây nhầm lẫn phổ biến nhất cho các lập trình viên mới làm quen với kiểm thử tích hợp full-stack.

Hãy xem xét kịch bản này với `@SpringBootTest(webEnvironment = WebEnvironment.RANDOM_PORT)`:

1.  Lớp kiểm thử của bạn được chú thích bằng `@Transactional`.
2.  Phương thức kiểm thử `testApiReturnsData()` của bạn lưu một entity vào cơ sở dữ liệu bằng repository.
3.  Cùng một phương thức kiểm thử sau đó sử dụng `TestRestTemplate` để gọi endpoint REST của bạn, mong đợi nhận được dữ liệu mà nó vừa lưu.
4.  Endpoint trả về **404 Not Found**.

**Tại sao điều này lại xảy ra?**

Phương thức kiểm thử và ứng dụng chạy trên cổng ngẫu nhiên hoạt động trong **các luồng (thread) khác nhau** và do đó là **các transaction khác nhau**.

*   Transaction của phương thức kiểm thử (Luồng 1) đã lưu dữ liệu, nhưng nó **chưa commit**. Dữ liệu chỉ tồn tại trong transaction cô lập đó.
*   Lời gọi `TestRestTemplate` gửi một request HTTP thực sự đến máy chủ ứng dụng của bạn (Luồng 2). Code controller xử lý request này bắt đầu một transaction *mới, của riêng nó*.
*   Từ góc độ của transaction mới này, dữ liệu từ transaction của bài kiểm thử không tồn tại vì nó chưa bao giờ được commit. Mức độ cô lập mặc định của cơ sở dữ liệu (`READ COMMITTED` trong PostgreSQL) ngăn một transaction nhìn thấy công việc chưa được commit của một transaction khác.
*   Vào cuối phương thức kiểm thử, transaction của bài kiểm thử được rollback, và dữ liệu biến mất mãi mãi.

**Giải pháp:**

Khi kiểm thử một luồng đầy đủ với một máy chủ thực, bạn không thể dựa vào transaction của phương thức kiểm thử để thiết lập dữ liệu. Bạn phải **commit dữ liệu** trước khi lời gọi API được thực hiện.

**Cách đúng:** Tách việc thiết lập dữ liệu khỏi logic kiểm thử transactional.

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
// KHÔNG thêm @Transactional vào lớp cho loại kiểm thử này!
class ProductControllerFullStackTest extends AbstractIntegrationTest {

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private ProductRepository productRepository;

    @BeforeEach
    void setUp() {
        // Thiết lập này chạy TRƯỚC mỗi phương thức kiểm thử, trong một transaction riêng
        // được commit. Điều này đảm bảo dữ liệu có thể nhìn thấy được đối với ứng dụng chính.
        productRepository.deleteAll(); // Dọn dẹp từ các bài kiểm thử trước
    }

    @Test
    void whenProductExists_thenApiReturnsProduct() {
        // Arrange: Lưu và commit dữ liệu TRƯỚC logic kiểm thử.
        Product savedProduct = productRepository.save(new Product(null, "Real Product", 50.0));

        // Act: Gọi endpoint API thật.
        ResponseEntity<Product> response = restTemplate.getForEntity("/products/" + savedProduct.getId(), Product.class);

        // Assert
        assertThat(response.getStatusCode()).isEqualTo(HttpStatus.OK);
        assertThat(response.getBody().getName()).isEqualTo("Real Product");
    }
}
```

---

#### **Chủ đề 3.4: Kiểm thử Truy vấn, Ràng buộc và Mối quan hệ**

`@DataJpaTest` với Testcontainers là môi trường hoàn hảo để xác minh các chi tiết nhỏ của tầng persistence của bạn.

**Kiểm thử Truy vấn JPQL / Native Tùy chỉnh**

Điều này khá đơn giản. Mục tiêu là chứng minh truy vấn của bạn đúng cú pháp và trả về dữ liệu đúng.

```java
@Test
void whenFindByPriceGreaterThan_returnsMatchingProducts() {
    // Arrange: Thiết lập một bộ dữ liệu đa dạng
    entityManager.persistAndFlush(new Product(null, "Cheap", 10.0));
    entityManager.persistAndFlush(new Product(null, "Mid-range", 100.0));
    entityManager.persistAndFlush(new Product(null, "Expensive", 200.0));

    // Act
    List<Product> results = productRepository.findByPriceGreaterThan(50.0);

    // Assert
    assertThat(results).hasSize(2);
    assertThat(results).extracting(Product::getName).contains("Mid-range", "Expensive");
}
```

**Kiểm thử Ràng buộc Cơ sở dữ liệu (ví dụ: `unique = true`)**

Để kiểm thử một ràng buộc, bạn phải gây ra vi phạm nó và khẳng định rằng exception chính xác được ném ra.

```java
@Test
void whenSavingProductWithDuplicateName_thenThrowsException() {
    // Arrange: Lưu sản phẩm đầu tiên.
    entityManager.persistAndFlush(new Product(null, "Unique Name", 10.0));

    // Act & Assert: Cố gắng lưu sản phẩm thứ hai có cùng tên.
    Product duplicate = new Product(null, "Unique Name", 20.0);

    // Chúng ta khẳng định rằng thao tác này sẽ ném ra một DataIntegrityViolationException.
    // Điều này chứng tỏ @Column(unique=true) của chúng ta đang hoạt động ở cấp CSDL.
    assertThrows(DataIntegrityViolationException.class, () -> {
        productRepository.saveAndFlush(duplicate);
    });
}
```

**Kiểm thử Mối quan hệ Thực thể (ví dụ: Cascading)**

Bạn cần xác minh rằng các quy tắc cascading của mình (`CascadeType.PERSIST`, `CascadeType.REMOVE`, v.v.) và `orphanRemoval` hoạt động như dự định.

**Ví dụ: Kiểm thử `CascadeType.PERSIST`**

Hãy tưởng tượng một entity `Store` có mối quan hệ `@OneToMany` với `Product`, được cấu hình với `cascade = CascadeType.ALL`.

```java
@Test
void whenSavingStore_thenCascadesToProducts() {
    // Arrange
    Store store = new Store("My Store");
    store.addProduct(new Product(null, "Product A", 10.0));
    store.addProduct(new Product(null, "Product B", 20.0));

    // Act: Chỉ lưu entity cha (cửa hàng).
    storeRepository.saveAndFlush(store);

    // Xóa persistence context để đảm bảo chúng ta đang đọc dữ liệu mới từ CSDL
    entityManager.clear();

    // Assert: Xác minh rằng các thực thể con (sản phẩm) cũng đã được lưu.
    Store foundStore = storeRepository.findById(store.getId()).get();
    assertThat(foundStore.getProducts()).hasSize(2);
}
```
---
#### **Chủ đề 3.5: Xử lý các vấn đề JPA phổ biến trong các Bài kiểm thử**

**`LazyInitializationException`**

*   **Vấn đề:** Bạn tải một entity, transaction/session đóng lại, và sau đó bạn cố gắng truy cập một mối quan hệ `@OneToMany` hoặc `@ManyToOne` được đánh dấu là `FetchType.LAZY`.
*   **Giải pháp:** Thực hiện khẳng định cần dữ liệu lazy **bên trong phương thức kiểm thử transactional**. Nếu bạn cần trả về dữ liệu từ một service, hãy sử dụng một truy vấn `JOIN FETCH` trong repository của bạn để tải trước association trong một truy vấn duy nhất.

**Các Entity bị tách rời (Detached Entities)**

*   **Vấn đề:** Một entity không còn được theo dõi bởi một persistence context. Nếu bạn sửa đổi nó và cố gắng lưu nó, Hibernate không biết phải làm gì.
*   **Giải pháp:** Sử dụng `entityManager.merge(detachedEntity)`. Phương thức này nhận một entity đã bị tách rời, tìm phiên bản được đính kèm trong persistence context hiện tại, sao chép các thay đổi của bạn vào nó, và trả về instance được quản lý.

**Kiểm thử Khóa Lạc quan (`@Version`)**

*   **Mục tiêu:** Để chứng minh rằng nếu hai người dùng tải cùng một dữ liệu, và Người dùng 1 lưu một thay đổi, thì nỗ lực lưu sau đó của Người dùng 2 sẽ thất bại.
*   **Phương pháp:** Điều này đòi hỏi sự quản lý cẩn thận để mô phỏng hai persistence context khác nhau.

```java
@Test
void whenConcurrentUpdate_thenThrowsObjectOptimisticLockingFailureException() {
    // Arrange: Người dùng 1 và Người dùng 2 cùng tải một sản phẩm.
    Product saved = productRepository.saveAndFlush(new Product(null, "Original Name", 100.0));
    entityManager.clear(); // Tách tất cả các entity

    Product user1View = productRepository.findById(saved.getId()).get();
    Product user2View = productRepository.findById(saved.getId()).get();
    assertThat(user1View.getVersion()).isEqualTo(0);

    // Act 1: Người dùng 1 cập nhật và lưu sản phẩm.
    user1View.setName("User 1's Update");
    productRepository.saveAndFlush(user1View); // Commit, phiên bản trong CSDL bây giờ là 1

    // Act 2 & Assert: Người dùng 2, người vẫn còn phiên bản cũ (0), cố gắng cập nhật.
    user2View.setName("User 2's Update");
    assertThrows(ObjectOptimisticLockingFailureException.class, () -> {
        productRepository.saveAndFlush(user2View); // Thao tác này sẽ thất bại
    });
}
```

---

#### **Chủ đề 3.6: Tích hợp Di chuyển Cơ sở dữ liệu (Flyway/Liquibase)**

Dựa vào `hibernate.ddl-auto` là tiện lợi nhưng rủi ro. Các kịch bản di chuyển (migration scripts) của bạn là nguồn chân lý duy nhất cho schema cơ sở dữ liệu của bạn. Các bài kiểm thử của bạn nên sử dụng chúng.

**Cấu hình:**
1.  Thêm phụ thuộc `flyway-core` (hoặc `liquibase-core`).
2.  Tạo các kịch bản di chuyển của bạn trong `src/main/resources/db/migration`.
3.  Cấu hình các thuộc tính kiểm thử của bạn để sử dụng Flyway và vô hiệu hóa DDL của Hibernate.

**Ví dụ `application-test.properties` hoặc `@TestPropertySource`:**
```properties
# Chúng ta đang sử dụng Testcontainers, vì vậy không thay thế datasource
spring.test.database.replace=none

# Báo cho Flyway chạy. Nó được bật theo mặc định nếu có phụ thuộc.
spring.flyway.enabled=true

# Báo cho Hibernate không đụng đến schema.
# 'validate' còn tốt hơn, vì nó sẽ thất bại nếu entity và schema không khớp.
spring.jpa.hibernate.ddl-auto=validate
```

Với cấu hình này, khi `@DataJpaTest` hoặc `@SpringBootTest` của bạn bắt đầu:
1.  Testcontainers khởi động PostgreSQL.
2.  Spring kết nối đến container.
3.  **Flyway chạy, áp dụng tất cả các kịch bản di chuyển `V1__...`, `V2__...` của bạn để tạo schema.**
4.  Hibernate khởi động và xác thực rằng các định nghĩa `@Entity` của bạn khớp với schema do Flyway tạo ra.
5.  Các bài kiểm thử của bạn chạy trên một schema giống hệt với production.

Cách tiếp cận này kết hợp độ trung thực của Testcontainers với tính chính xác về schema của Flyway/Liquibase, mang lại cho bạn sự tự tin cao nhất có thể trong các bài kiểm thử tầng persistence của mình.

***

Tiếp theo, chúng ta sẽ quay trở lại ngăn xếp lên **Tầng Web**, khám phá chi tiết sâu về `@WebMvcTest` và `MockMvc`.