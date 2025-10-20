### **Part 8: Edge Cases and Advanced Scenarios**

Welcome to the final and most advanced part of our course. Here, we tackle the complex, real-world scenarios that often prove challenging to test. Mastering these techniques will demonstrate a senior-level understanding of integration testing.

#### **Topic 8.1: Testing Asynchronous Operations (`@Async`)**

**The Challenge:** When you call a method annotated with `@Async`, it executes in a separate thread from your main test thread. Your test method might finish and its assertions might run *before* the asynchronous method has completed its work, leading to flaky or incorrect test results.

**The Solution: Awaitility**
Awaitility is a small but powerful library that provides a DSL for polling and waiting for an asynchronous operation to complete.

**Setup:** Add the Awaitility dependency.
```xml
<dependency>
    <groupId>org.awaitility</groupId>
    <artifactId>awaitility</artifactId>
    <scope>test</scope>
</dependency>
```

**Example:** Imagine a service that sends an email asynchronously.
```java
// NotificationService.java
@Service
public class NotificationService {
    private final EmailGateway emailGateway;

    @Async
    public void sendNotification(String message) {
        // Simulate a delay
        Thread.sleep(1000);
        emailGateway.send(message);
    }
}
```

**The Test:**
```java
import static org.awaitility.Awaitility.await;
import static java.util.concurrent.TimeUnit.SECONDS;
import static org.mockito.Mockito.verify;

@SpringBootTest
// We need to enable async support for tests
@EnableAsync
class NotificationServiceTest extends AbstractIntegrationTest {

    @Autowired
    private NotificationService notificationService;

    @MockBean
    private EmailGateway emailGateway;

    @Test
    void whenSendNotification_thenGatewayIsCalledEventually() {
        // Act: Call the async method. It returns immediately.
        notificationService.sendNotification("Test message");

        // Assert: Use Awaitility to poll.
        // It will repeatedly check the condition until it passes or the timeout is reached.
        await().atMost(5, SECONDS).untilAsserted(() -> {
            verify(emailGateway).send("Test message");
        });
    }
}
```
Awaitility elegantly solves the race condition between the test thread and the async thread, making your asynchronous tests stable and reliable.

---

#### **Topic 8.2: Testing Scheduled Tasks (`@Scheduled`)**

**The Challenge:** `@Scheduled` tasks run based on a cron expression or a fixed delay, controlled by the system clock. You can't wait for an hour in your integration test for a task to run.

**The Solution:** Test the *action*, not the *trigger*. The `@Scheduled` annotation is a configuration detail. The business logic is inside the method itself. The most pragmatic approach is to call the public scheduled method directly in your test. A `@SpringBootTest` context is still valuable to ensure all the dependencies of the scheduled task are correctly wired.

**Example:**
```java
// ReportGenerator.java
@Service
public class ReportGenerator {
    private final ReportRepository reportRepository;

    @Scheduled(cron = "0 0 1 * * *") // Runs daily at 1 AM
    public void generateDailyReport() {
        // ... complex logic to generate a report ...
        reportRepository.save(new Report("daily_report"));
    }
}
```

**The Test:**
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

        // Act: Call the method directly!
        reportGenerator.generateDailyReport();

        // Assert
        assertThat(reportRepository.count()).isEqualTo(countBefore + 1);
    }
}
```
This test verifies that the report generation logic works correctly within a full application context, which is the primary goal. Verifying that the Spring scheduler itself works is generally unnecessary.

---

#### **Topic 8.3: Testing Cache Behavior (`@Cacheable`, `@CacheEvict`)**

**The Challenge:** Caching is a stateful, cross-cutting concern. You need to verify that your cache is being used (i.e., expensive operations are skipped) and that it can be correctly invalidated.

**The Solution:** Use a `@MockBean` for the dependency that gets called and verify the number of invocations using Mockito's `verify()`.

**Example:** A service with a cacheable method.
```java
// ProductService.java
@Service
public class ProductService {
    private final ProductRepository productRepository;

    @Cacheable("products")
    public Product findById(Long id) {
        // This should only be called once for the same id
        return productRepository.findById(id).orElse(null);
    }

    @CacheEvict(value = "products", key = "#id")
    public void updateProduct(Long id, ProductUpdateDto dto) {
        // ... update logic ...
    }
}
```

**The Test:**
```java
@SpringBootTest
// Enable caching for the test context
@EnableCaching
class ProductServiceCacheTest extends AbstractIntegrationTest {

    @Autowired
    private ProductService productService;

    @MockBean // Mock the repository to track calls
    private ProductRepository productRepository;

    @Test
    void givenCacheableMethod_whenCalledTwice_thenRepositoryIsHitOnlyOnce() {
        // Arrange
        Long productId = 1L;
        Product product = new Product(productId, "Test Product", 10.0);
        given(productRepository.findById(productId)).willReturn(Optional.of(product));

        // Act 1: First call - should hit the repository and populate the cache
        productService.findById(productId);

        // Act 2: Second call - should hit the cache, NOT the repository
        productService.findById(productId);

        // Assert: Verify the repository method was called exactly one time.
        verify(productRepository, times(1)).findById(productId);
    }

    @Test
    void givenCachedItem_whenEvictIsCalled_thenCacheIsCleared() {
        // Arrange
        Long productId = 2L;
        Product product = new Product(productId, "Another Product", 20.0);
        given(productRepository.findById(productId)).willReturn(Optional.of(product));

        // Act 1: Populate the cache
        productService.findById(productId);

        // Act 2: Evict the cache
        productService.updateProduct(productId, new ProductUpdateDto());

        // Act 3: Call again - should now hit the repository again
        productService.findById(productId);

        // Assert: Verify the repository was hit twice in total (before and after evict)
        verify(productRepository, times(2)).findById(productId);
    }
}
```

---

#### **Topic 8.4: Testing Security-Related APIs (`@WithMockUser`)**

**The Challenge:** Your API has endpoints that are protected by authentication and authorization rules (`@PreAuthorize`, security filter chains). How do you test these rules without a full login flow?

**The Solution:** The `spring-security-test` module provides a set of annotations to easily create a mock `SecurityContext`.

*   `@WithMockUser`: Performs the test as if an authenticated user is logged in. You can specify the username, roles, and authorities.
*   `@WithAnonymousUser`: Performs the test as an unauthenticated, anonymous user.

**Example:** A secured controller.
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

**The `@WebMvcTest`:**
```java
@WebMvcTest(AdminController.class)
class AdminControllerSecurityTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    @WithMockUser(roles = "ADMIN") // Simulate a user with the ADMIN role
    void whenCalledByAdmin_thenReturns200() throws Exception {
        mockMvc.perform(post("/admin/tasks"))
               .andExpect(status().isOk());
    }

    @Test
    @WithMockUser(roles = "USER") // Simulate a user with an insufficient role
    void whenCalledByUser_thenReturns403() throws Exception {
        mockMvc.perform(post("/admin/tasks"))
               .andExpect(status().isForbidden()); // 403 Forbidden
    }

    @Test
    @WithAnonymousUser // Or just no annotation at all
    void whenCalledByAnonymous_thenReturns401() throws Exception {
        mockMvc.perform(post("/admin/tasks"))
               .andExpect(status().isUnauthorized()); // 401 Unauthorized
    }
}
```

---

#### **Topic 8.5: Testing Transaction Propagation**

**The Challenge:** How do you test that a method with `Propagation.REQUIRES_NEW` actually runs in its own, independent transaction?

**The Solution:** You need to create a scenario where the outer transaction rolls back, and then verify that the work done by the inner, `REQUIRES_NEW` transaction was still committed to the database.

**Example:** An `OrderService` calls an `AuditService`.
```java
// OrderService.java
@Service
public class OrderService {
    private final AuditService auditService;
    @Transactional
    public void createOrder() {
        auditService.logAttempt("ORDER_CREATION");
        // ... some logic that fails and causes a rollback ...
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
**The Test:**
```java
@SpringBootTest
class TransactionPropagationTest extends AbstractIntegrationTest {

    @Autowired
    private OrderService orderService;

    @Autowired
    private AuditRepository auditRepository;

    @Test
    void whenOuterTxRollsBack_thenInnerRequiresNewTxCommits() {
        // Arrange: Check the initial state
        assertThat(auditRepository.count()).isZero();

        // Act: Call the method that will throw an exception and roll back
        // The assertThrows confirms the outer transaction failed as expected
        assertThrows(RuntimeException.class, () -> {
            orderService.createOrder();
        });

        // Assert: The crucial check.
        // Even though the order creation failed, the audit log should have been
        // committed because it ran in a new, separate transaction.
        assertThat(auditRepository.count()).isEqualTo(1);
    }
}
```

---

#### **Topic 8.6: Common Performance Bottlenecks in Integration Testing**

This is a summary of the most important performance considerations:

1.  **Not Reusing the Application Context:** The #1 bottleneck. This is caused by minor configuration differences between test classes (`@MockBean`, `@TestPropertySource`, `@ActiveProfiles`). Strive to harmonize test configurations to maximize context caching.
2.  **Not Reusing Testcontainers:** The #2 bottleneck. Always use the singleton container pattern (`static @Container`) to start heavyweight resources like databases only once per test suite run.
3.  **Overusing `@DirtiesContext`:** This is the explicit destruction of the context cache. Avoid it at all costs. Prefer programmatic state cleanup in `@BeforeEach` or `@AfterEach` methods.
4.  **Using `@SpringBootTest` When a Slice Test Will Do:** The heaviest tool in the box. If you only need to test the web layer, use `@WebMvcTest`. If you only need to test the data layer, use `@DataJpaTest`. This significantly reduces context loading time.
5.  **Lack of Parallel Test Execution:** For larger projects, running tests in parallel can drastically reduce build times. This is an advanced setup that requires careful management of shared resources to avoid flaky tests.

By being mindful of these five points, you can keep your integration test suite lean and fast, ensuring a quick feedback loop for developers.

