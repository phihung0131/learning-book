### **Phần 8: Các Trường hợp Biên và Kịch bản Nâng cao**

Chào mừng bạn đến với phần cuối cùng và nâng cao nhất của khóa học của chúng ta. Ở đây, chúng ta sẽ giải quyết các kịch bản phức tạp, thực tế thường khó kiểm thử. Việc thành thạo các kỹ thuật này sẽ thể hiện một sự hiểu biết ở cấp độ senior về kiểm thử tích hợp.

#### **Chủ đề 8.1: Kiểm thử các Hoạt động Bất đồng bộ (`@Async`)**

**Thách thức:** Khi bạn gọi một phương thức được chú thích bằng `@Async`, nó sẽ thực thi trong một luồng riêng biệt so với luồng kiểm thử chính của bạn. Phương thức kiểm thử của bạn có thể kết thúc và các khẳng định của nó có thể chạy *trước khi* phương thức bất đồng bộ hoàn thành công việc, dẫn đến kết quả kiểm thử không ổn định hoặc không chính xác.

**Giải pháp: Awaitility**
Awaitility là một thư viện nhỏ nhưng mạnh mẽ, cung cấp một DSL để thăm dò (polling) và chờ đợi một hoạt động bất đồng bộ hoàn thành.

**Cài đặt:** Thêm phụ thuộc Awaitility.
```xml
<dependency>
    <groupId>org.awaitility</groupId>
    <artifactId>awaitility</artifactId>
    <scope>test</scope>
</dependency>
```

**Ví dụ:** Tưởng tượng một service gửi email một cách bất đồng bộ.
```java
// NotificationService.java
@Service
public class NotificationService {
    private final EmailGateway emailGateway;

    @Async
    public void sendNotification(String message) {
        // Mô phỏng một độ trễ
        Thread.sleep(1000);
        emailGateway.send(message);
    }
}
```

**Bài kiểm thử:**
```java
import static org.awaitility.Awaitility.await;
import static java.util.concurrent.TimeUnit.SECONDS;
import static org.mockito.Mockito.verify;

@SpringBootTest
// Chúng ta cần bật hỗ trợ async cho các bài kiểm thử
@EnableAsync
class NotificationServiceTest extends AbstractIntegrationTest {

    @Autowired
    private NotificationService notificationService;

    @MockBean
    private EmailGateway emailGateway;

    @Test
    void whenSendNotification_thenGatewayIsCalledEventually() {
        // Act: Gọi phương thức async. Nó trả về ngay lập tức.
        notificationService.sendNotification("Test message");

        // Assert: Sử dụng Awaitility để thăm dò.
        // Nó sẽ liên tục kiểm tra điều kiện cho đến khi nó thành công hoặc hết thời gian chờ.
        await().atMost(5, SECONDS).untilAsserted(() -> {
            verify(emailGateway).send("Test message");
        });
    }
}
```
Awaitility giải quyết một cách thanh lịch tình trạng tranh chấp (race condition) giữa luồng kiểm thử và luồng bất đồng bộ, làm cho các bài kiểm thử bất đồng bộ của bạn trở nên ổn định và đáng tin cậy.

---

#### **Chủ đề 8.2: Kiểm thử các Tác vụ Định kỳ (`@Scheduled`)**

**Thách thức:** Các tác vụ `@Scheduled` chạy dựa trên một biểu thức cron hoặc một khoảng thời gian trễ cố định, được kiểm soát bởi đồng hồ hệ thống. Bạn không thể đợi một giờ trong bài kiểm thử tích hợp của mình để một tác vụ chạy.

**Giải pháp:** Kiểm thử *hành động*, không phải *trình kích hoạt*. Annotation `@Scheduled` là một chi tiết cấu hình. Logic nghiệp vụ nằm bên trong chính phương thức đó. Cách tiếp cận thực tế nhất là gọi trực tiếp phương thức định kỳ public trong bài kiểm thử của bạn. Một context `@SpringBootTest` vẫn có giá trị để đảm bảo tất cả các phụ thuộc của tác vụ định kỳ được kết nối chính xác.

**Ví dụ:**
```java
// ReportGenerator.java
@Service
public class ReportGenerator {
    private final ReportRepository reportRepository;

    @Scheduled(cron = "0 0 1 * * *") // Chạy hàng ngày lúc 1 giờ sáng
    public void generateDailyReport() {
        // ... logic phức tạp để tạo báo cáo ...
        reportRepository.save(new Report("daily_report"));
    }
}
```

**Bài kiểm thử:**
```java
@SpringBootTest
class ReportGeneratorTest extends AbstractIntegrationTest {

    @Autowired
    private ReportGenerator reportGenerator;

    @Autowired
    private ReportRepository reportRepository;

    @Test
    void generateDailyReport_should_saveReportToDatabase() {
        // Arrange
        long countBefore = reportRepository.count();

        // Act: Gọi trực tiếp phương thức!
        reportGenerator.generateDailyReport();

        // Assert
        assertThat(reportRepository.count()).isEqualTo(countBefore + 1);
    }
}
```
Bài kiểm thử này xác minh rằng logic tạo báo cáo hoạt động chính xác trong một context ứng dụng đầy đủ, đó là mục tiêu chính. Việc xác minh rằng chính bộ lập lịch của Spring hoạt động là thường không cần thiết.

---

#### **Chủ đề 8.3: Kiểm thử Hành vi Cache (`@Cacheable`, `@CacheEvict`)**

**Thách thức:** Caching là một mối quan tâm xuyên suốt, có trạng thái. Bạn cần xác minh rằng cache của bạn đang được sử dụng (tức là các hoạt động tốn kém được bỏ qua) và nó có thể được vô hiệu hóa một cách chính xác.

**Giải pháp:** Sử dụng một `@MockBean` cho phụ thuộc được gọi đến và xác minh số lần gọi bằng cách sử dụng `verify()` của Mockito.

**Ví dụ:** Một service với một phương thức có thể cache.
```java
// ProductService.java
@Service
public class ProductService {
    private final ProductRepository productRepository;

    @Cacheable("products")
    public Product findById(Long id) {
        // Phương thức này chỉ nên được gọi một lần cho cùng một id
        return productRepository.findById(id).orElse(null);
    }

    @CacheEvict(value = "products", key = "#id")
    public void updateProduct(Long id, ProductUpdateDto dto) {
        // ... logic cập nhật ...
    }
}
```

**Bài kiểm thử:**
```java
@SpringBootTest
// Bật caching cho context kiểm thử
@EnableCaching
class ProductServiceCacheTest extends AbstractIntegrationTest {

    @Autowired
    private ProductService productService;

    @MockBean // Mock repository để theo dõi các cuộc gọi
    private ProductRepository productRepository;

    @Test
    void givenCacheableMethod_whenCalledTwice_thenRepositoryIsHitOnlyOnce() {
        // Arrange
        Long productId = 1L;
        Product product = new Product(productId, "Test Product", 10.0);
        given(productRepository.findById(productId)).willReturn(Optional.of(product));

        // Act 1: Lần gọi đầu tiên - nên gọi đến repository và điền vào cache
        productService.findById(productId);

        // Act 2: Lần gọi thứ hai - nên gọi đến cache, KHÔNG phải repository
        productService.findById(productId);

        // Assert: Xác minh phương thức repository được gọi chính xác một lần.
        verify(productRepository, times(1)).findById(productId);
    }

    @Test
    void givenCachedItem_whenEvictIsCalled_thenCacheIsCleared() {
        // Arrange
        Long productId = 2L;
        Product product = new Product(productId, "Another Product", 20.0);
        given(productRepository.findById(productId)).willReturn(Optional.of(product));

        // Act 1: Điền vào cache
        productService.findById(productId);

        // Act 2: Vô hiệu hóa cache
        productService.updateProduct(productId, new ProductUpdateDto());

        // Act 3: Gọi lại - bây giờ nên gọi đến repository một lần nữa
        productService.findById(productId);

        // Assert: Xác minh repository được gọi hai lần tổng cộng (trước và sau khi vô hiệu hóa)
        verify(productRepository, times(2)).findById(productId);
    }
}
```

---

#### **Chủ đề 8.4: Kiểm thử các API liên quan đến Bảo mật (`@WithMockUser`)**

**Thách thức:** API của bạn có các endpoint được bảo vệ bởi các quy tắc xác thực và ủy quyền (`@PreAuthorize`, chuỗi bộ lọc bảo mật). Làm thế nào để bạn kiểm thử các quy tắc này mà không cần một luồng đăng nhập đầy đủ?

**Giải pháp:** Module `spring-security-test` cung cấp một tập hợp các annotation để dễ dàng tạo một `SecurityContext` giả.

*   `@WithMockUser`: Thực hiện bài kiểm thử như thể một người dùng đã được xác thực đang đăng nhập. Bạn có thể chỉ định tên người dùng, vai trò và quyền hạn.
*   `@WithAnonymousUser`: Thực hiện bài kiểm thử như một người dùng ẩn danh, chưa được xác thực.

**Ví dụ:** Một controller được bảo mật.
```java
@RestController
public class AdminController {
    @PostMapping("/admin/tasks")
    @PreAuthorize("hasRole('ADMIN')")
    public ResponseEntity<Void> createTask() {
        return ResponseEntity.ok().build();
    }
}
```

**`@WebMvcTest`:**
```java
@WebMvcTest(AdminController.class)
class AdminControllerSecurityTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    @WithMockUser(roles = "ADMIN") // Mô phỏng một người dùng với vai trò ADMIN
    void whenCalledByAdmin_thenReturns200() throws Exception {
        mockMvc.perform(post("/admin/tasks"))
               .andExpect(status().isOk());
    }

    @Test
    @WithMockUser(roles = "USER") // Mô phỏng một người dùng với vai trò không đủ
    void whenCalledByUser_thenReturns403() throws Exception {
        mockMvc.perform(post("/admin/tasks"))
               .andExpect(status().isForbidden()); // 403 Forbidden
    }

    @Test
    @WithAnonymousUser // Hoặc không có annotation nào cả
    void whenCalledByAnonymous_thenReturns401() throws Exception {
        mockMvc.perform(post("/admin/tasks"))
               .andExpect(status().isUnauthorized()); // 401 Unauthorized
    }
}
```

---

#### **Chủ đề 8.5: Kiểm thử Lan truyền Giao dịch (Transaction Propagation)**

**Thách thức:** Làm thế nào để bạn kiểm thử rằng một phương thức với `Propagation.REQUIRES_NEW` thực sự chạy trong một giao dịch riêng, độc lập?

**Giải pháp:** Bạn cần tạo một kịch bản trong đó giao dịch bên ngoài rollback, và sau đó xác minh rằng công việc được thực hiện bởi giao dịch bên trong, `REQUIRES_NEW`, vẫn được commit vào cơ sở dữ liệu.

**Ví dụ:** Một `OrderService` gọi một `AuditService`.
```java
// OrderService.java
@Service
public class OrderService {
    private final AuditService auditService;
    @Transactional
    public void createOrder() {
        auditService.logAttempt("ORDER_CREATION");
        // ... một số logic bị lỗi và gây ra rollback ...
        throw new RuntimeException("Order failed");
    }
}

// AuditService.java
@Service
public class AuditService {
    private final AuditRepository auditRepository;
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void logAttempt(String event) {
        auditRepository.save(new AuditLog(event));
    }
}
```
**Bài kiểm thử:**
```java
@SpringBootTest
class TransactionPropagationTest extends AbstractIntegrationTest {

    @Autowired
    private OrderService orderService;

    @Autowired
    private AuditRepository auditRepository;

    @Test
    void whenOuterTxRollsBack_thenInnerRequiresNewTxCommits() {
        // Arrange: Kiểm tra trạng thái ban đầu
        assertThat(auditRepository.count()).isZero();

        // Act: Gọi phương thức sẽ ném ra một exception và rollback
        // assertThrows xác nhận giao dịch bên ngoài đã thất bại như mong đợi
        assertThrows(RuntimeException.class, () -> {
            orderService.createOrder();
        });

        // Assert: Kiểm tra quan trọng.
        // Mặc dù việc tạo đơn hàng đã thất bại, nhật ký kiểm toán vẫn nên được
        // commit vì nó đã chạy trong một giao dịch mới, riêng biệt.
        assertThat(auditRepository.count()).isEqualTo(1);
    }
}
```

---

#### **Chủ đề 8.6: Các Nút thắt Cổ chai Hiệu suất Phổ biến trong Kiểm thử Tích hợp**

Đây là tóm tắt các cân nhắc hiệu suất quan trọng nhất:

1.  **Không Tái sử dụng Application Context:** Nút thắt cổ chai số 1. Điều này gây ra bởi sự khác biệt nhỏ về cấu hình giữa các lớp kiểm thử (`@MockBean`, `@TestPropertySource`, `@ActiveProfiles`). Hãy cố gắng hài hòa hóa các cấu hình kiểm thử để tối đa hóa việc lưu trữ context vào bộ đệm.
2.  **Không Tái sử dụng Testcontainers:** Nút thắt cổ chai số 2. Luôn sử dụng mô hình container singleton (`static @Container`) để khởi động các tài nguyên nặng như cơ sở dữ liệu chỉ một lần cho mỗi lần chạy bộ kiểm thử.
3.  **Lạm dụng `@DirtiesContext`:** Đây là sự phá hủy rõ ràng của bộ đệm context. Hãy tránh nó bằng mọi giá. Ưu tiên dọn dẹp trạng thái bằng lập trình trong các phương thức `@BeforeEach` hoặc `@AfterEach`.
4.  **Sử dụng `@SpringBootTest` Khi một Kiểm thử Lát cắt là Đủ:** Công cụ nặng nhất trong hộp công cụ. Nếu bạn chỉ cần kiểm thử tầng web, hãy sử dụng `@WebMvcTest`. Nếu bạn chỉ cần kiểm thử tầng dữ liệu, hãy sử dụng `@DataJpaTest`. Điều này làm giảm đáng kể thời gian tải context.
5.  **Thiếu Thực thi Kiểm thử Song song:** Đối với các dự án lớn hơn, chạy các bài kiểm thử song song có thể giảm đáng kể thời gian xây dựng. Đây là một thiết lập nâng cao đòi hỏi sự quản lý cẩn thận các tài nguyên được chia sẻ để tránh các bài kiểm thử không ổn định.

Bằng cách lưu ý đến năm điểm này, bạn có thể giữ cho bộ kiểm thử tích hợp của mình gọn gàng và nhanh chóng, đảm bảo một vòng lặp phản hồi nhanh cho các lập trình viên.