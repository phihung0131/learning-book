### **Phần 1: Nền tảng của Kiểm thử Tích hợp (Integration Testing)**

#### **Chủ đề 1.1: Kiểm thử Tích hợp trong Spring Boot là gì và nó khác với Kiểm thử Đơn vị (Unit Testing) như thế nào?**

#### **Lý thuyết: Kim tự tháp Kiểm thử (The Testing Pyramid)**

Hãy tưởng tượng một kim tự tháp. Phần đế rộng lớn là **Kiểm thử Đơn vị (Unit Tests)**, lớp giữa là **Kiểm thử Tích hợp (Integration Tests)**, và đỉnh nhỏ là **Kiểm thử Đầu cuối (End-to-End - E2E Tests)**.

*   **Unit Tests (Đế):** Các bài kiểm thử này nhanh, nhiều về số lượng và có tính cô lập. Chúng kiểm tra một "đơn vị" code duy nhất—thường là một phương thức trong một lớp—hoàn toàn tách biệt khỏi các phụ thuộc của nó. Nếu một phương thức trong `UserService` của bạn tính tuổi của người dùng, một unit test sẽ gọi phương thức cụ thể đó và khẳng định giá trị trả về của nó. Tất cả các phụ thuộc bên ngoài, như một repository cơ sở dữ liệu hoặc một service khác, đều được thay thế bằng các "đối tượng đóng thế" (mock hoặc stub).

*   **Integration Tests (Thân):** Các bài kiểm thử này xác minh rằng các "đơn vị" hoặc các thành phần khác nhau của ứng dụng của bạn hoạt động cùng nhau như mong đợi. Thay vì kiểm tra một phương thức duy nhất một cách cô lập, bạn kiểm tra một luồng hoàn chỉnh. Ví dụ, bạn có thể kiểm tra rằng khi một controller nhận một yêu cầu HTTP, nó sẽ gọi service một cách chính xác, sau đó service tương tác thành công với cơ sở dữ liệu.

*   **E2E Tests (Đỉnh):** Đây là những bài kiểm thử phức tạp và chậm nhất. Chúng kiểm tra toàn bộ luồng ứng dụng từ giao diện người dùng (hoặc một API client) cho đến cơ sở dữ liệu và ngược lại, mô phỏng một hành trình thực tế của người dùng.

#### **So sánh Unit Testing và Integration Testing trong Spring Boot**

Hãy phân tích những khác biệt chính trong bối cảnh một ứng dụng Spring Boot.

| Đặc điểm | Unit Testing | Integration Testing |
| :--- | :--- | :--- |
| **Phạm vi** | Một lớp hoặc phương thức duy nhất ("đơn vị"). | Nhiều thành phần tương tác cùng nhau (ví dụ: Controller → Service → Repository). |
| **Phụ thuộc** | Các phụ thuộc bên ngoài được **mock**. Ví dụ, một `UserRepository` bên trong `UserService` được thay thế bằng một đối tượng mock. | Các phụ thuộc bên ngoài thường là **thật**. Bạn có thể sử dụng một cơ sở dữ liệu thật (như PostgreSQL trong một Docker container) hoặc một hàng đợi tin nhắn (message queue) thật. |
| **Tốc độ** | **Rất nhanh.** Các bài kiểm thử chạy trong vài mili giây vì chúng không liên quan đến I/O, gọi mạng, hay khởi động máy chủ. | **Chậm hơn.** Các bài kiểm thử có thể mất vài giây hoặc thậm chí vài phút vì chúng tải các phần của Spring context, khởi động cơ sở dữ liệu, hoặc lắng nghe trên một cổng thật. |
| **Spring Context** | **Không tải Spring `ApplicationContext`.** Các bài kiểm thử là các bài kiểm thử JUnit/Mockito thông thường. Bạn tự khởi tạo lớp đang được kiểm thử. | **Tải Spring `ApplicationContext`.** Cơ chế tiêm phụ thuộc (dependency injection) và các tính năng khác của Spring được kích hoạt, giống như trong ứng dụng thực tế. |
| **Mục tiêu** | Xác minh logic của một đơn vị là chính xác. "Phương thức `calculatePrice` của tôi có trả về giá trị đúng với các đầu vào này không?" | Xác minh rằng các thành phần phối hợp với nhau một cách chính xác. "Endpoint REST của tôi có lưu dữ liệu vào cơ sở dữ liệu và trả về mã trạng thái HTTP đúng không?" |
| **Vòng lặp Phản hồi** | **Ngay lập tức.** Lập trình viên chạy chúng liên tục trong quá trình phát triển. | **Có độ trễ nhỏ.** Thường được chạy trước khi commit code hoặc là một phần của quy trình CI/CD. |

---

#### **Ví dụ Code: Câu chuyện về Hai loại Kiểm thử**

Hãy tưởng tượng một `UserService` đơn giản và `UserRepository` của nó.

**Các thành phần:**

```java
// UserRepository.java - Interface của JPA
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByUsername(String username);
}

// UserService.java - Lớp chúng ta muốn kiểm thử
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

**1. Unit Test (`UserServiceTest.java`)**

Ở đây, chúng ta chỉ quan tâm đến logic của `UserService`. Chúng ta **không** quan tâm đến cơ sở dữ liệu. Chúng ta sẽ sử dụng Mockito để mock `UserRepository`.

```java
// Không cần các annotation của Spring ở đây!
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import java.util.Optional;

import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.Mockito.when;

@ExtendWith(MockitoExtension.class) // Kích hoạt Mockito
class UserServiceTest {

    @Mock // Tạo một đối tượng mock của UserRepository
    private UserRepository userRepository;

    @InjectMocks // Tạo một instance của UserService và tiêm các mock vào đó
    private UserService userService;

    @Test
    void whenUserExists_findByUsername_returnsUser() {
        // 1. Arrange: Định nghĩa hành vi của mock
        User expectedUser = new User(1L, "testuser");
        when(userRepository.findByUsername("testuser")).thenReturn(Optional.of(expectedUser));

        // 2. Act: Gọi phương thức chúng ta đang kiểm thử
        User actualUser = userService.findByUsername("testuser");

        // 3. Assert: Xác minh kết quả
        assertThat(actualUser.getUsername()).isEqualTo("testuser");
    }

    @Test
    void whenUserDoesNotExist_findByUsername_throwsException() {
        // 1. Arrange: Định nghĩa hành vi của mock
        when(userRepository.findByUsername("nonexistent")).thenReturn(Optional.empty());

        // 2. Act & 3. Assert: Kiểm tra xem exception đúng có được ném ra không
        assertThrows(UserNotFoundException.class, () -> {
            userService.findByUsername("nonexistent");
        });
    }
}
```

**Những điểm chính từ Unit Test:**
*   **Không liên quan đến Spring:** Chúng ta không thấy `@SpringBootTest` hay bất kỳ annotation nào khác của Spring. Đây là một bài kiểm thử JUnit 5 và Mockito thuần túy.
*   **Tính cô lập:** Bài kiểm thử hoàn toàn độc lập với cơ sở dữ liệu. Chúng ta đã chỉ định rõ ràng cho `userRepository` mock cách hành xử.
*   **Tốc độ:** Bài kiểm thử này chạy trong vài mili giây.

**2. Integration Test (`UserServiceIntegrationTest.java`)**

Ở đây, chúng ta muốn xác minh rằng `UserService` và `UserRepository` hoạt động cùng nhau một cách chính xác với một **cơ sở dữ liệu thật**. Chúng ta sẽ sử dụng `@DataJpaTest`, một "lát cắt" kiểm thử tích hợp chuyên dụng cho tầng persistence.

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;
import org.springframework.context.annotation.Import;

import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest // <-- Đây là annotation chính
@Import(UserService.class) // Chúng ta cần bảo Spring tải bean UserService
class UserServiceIntegrationTest {

    @Autowired
    private TestEntityManager entityManager; // Một helper cho các bài kiểm thử JPA

    @Autowired
    private UserService userService;

    @Autowired
    private UserRepository userRepository; // Đây là bean thật, không phải mock

    @Test
    void whenUserExists_findByUsername_returnsUser() {
        // 1. Arrange: Lưu một người dùng thật vào cơ sở dữ liệu test
        User userToSave = new User(null, "realuser");
        entityManager.persist(userToSave);
        entityManager.flush(); // Đẩy dữ liệu vào CSDL

        // 2. Act: Gọi service, service sẽ truy vấn cơ sở dữ liệu test thật
        User foundUser = userService.findByUsername("realuser");

        // 3. Assert: Xác minh kết quả đến từ cơ sở dữ liệu
        assertThat(foundUser.getUsername()).isEqualTo("realuser");
    }
}
```

**Những điểm chính từ Integration Test:**
*   **Có sự tham gia của Spring:** `@DataJpaTest` là một annotation mạnh mẽ, mặc định sẽ cấu hình một cơ sở dữ liệu trong bộ nhớ (in-memory database) như H2, quét các lớp `@Entity` và các repository JPA của bạn, và cấu hình các bean cần thiết khác cho việc kiểm thử tầng persistence.
*   **Phụ thuộc thật:** Chúng ta không mock `UserRepository`. Chúng ta đang `@Autowired` bean repository *thật* mà Spring đã tạo. Bài kiểm thử này xác minh sự phối hợp giữa service và tầng persistence.
*   **Thực thi chậm hơn:** Bài kiểm thử này mất nhiều thời gian hơn vì Spring phải:
    *   Khởi động một `ApplicationContext`.
    *   Cấu hình một cơ sở dữ liệu trong bộ nhớ.
    *   Thực hiện tạo schema.
    *   Thực thi các câu lệnh SQL `INSERT` và `SELECT` thật.
*   **Tính cô lập của bài kiểm thử:** Theo mặc định, mỗi phương thức kiểm thử được chú thích bằng `@DataJpaTest` sẽ chạy trong một transaction riêng, và transaction này sẽ được rollback (hoàn tác) vào cuối bài kiểm thử. Điều này đảm bảo rằng dữ liệu từ một bài kiểm thử không bị rò rỉ sang bài kiểm thử khác, mang lại sự cô lập tuyệt vời.

---

### **Các câu hỏi phỏng vấn**

1.  **Câu hỏi:** "Bạn có một phương thức service với logic nghiệp vụ phức tạp gọi đến một repository. Bạn sẽ kiểm thử nó như thế nào và tại sao bạn chọn cách tiếp cận đó?"
    *   **Trả lời:** "Tôi sẽ sử dụng hai loại kiểm thử. Đầu tiên, tôi sẽ viết **unit tests** cho lớp service, mock repository. Điều này cho phép tôi kiểm thử tất cả logic nghiệp vụ phức tạp, bao gồm các trường hợp biên và các điều kiện lỗi, một cách rất nhanh chóng và cô lập. Thứ hai, tôi sẽ viết ít nhất một **integration test** sử dụng `@DataJpaTest` hoặc một phương pháp tương tự để xác minh rằng service tương tác chính xác với repository và các câu truy vấn JPQL hoặc native query của tôi trong repository là đúng cú pháp và trả về dữ liệu mong đợi. Cách tiếp cận hai hướng này đảm bảo cả tính đúng đắn về logic và sự tích hợp thành công."

2.  **Câu hỏi:** "Khi nào bạn sẽ chọn viết một integration test thay vì một unit test?"
    *   **Trả lời:** "Tôi sẽ chọn một integration test khi mục tiêu chính là xác minh sự phối hợp giữa các thành phần, chứ không phải logic nội tại của một thành phần duy nhất. Điều này rất quan trọng đối với:
        *   **Tương tác Cơ sở dữ liệu:** Đảm bảo các entity JPA được ánh xạ chính xác và các câu truy vấn hoạt động như mong đợi.
        *   **Hợp đồng API (API Contracts):** Xác minh rằng một REST controller tuần tự hóa và giải tuần tự hóa các payload JSON một cách chính xác và tương tác với tầng service.
        *   **Hệ thống bên ngoài:** Kiểm thử sự tương tác với các dịch vụ bên ngoài, hàng đợi tin nhắn, hoặc cache.
            Unit test không đủ cho những kịch bản này vì việc mock các tương tác này sẽ không chứng minh được rằng sự tích hợp thực tế hoạt động."

3.  **Câu hỏi:** "Theo bạn, sai lầm lớn nhất mà các lập trình viên mắc phải khi nói đến unit test và integration test là gì?"
    *   **Trả lời:** "Một sai lầm phổ biến là viết các integration test chậm, phức tạp cho logic mà lẽ ra nên được bao phủ bởi các unit test đơn giản. Ví dụ, sử dụng `@SpringBootTest` để kiểm tra một phép tính đơn giản bên trong một service. Điều này làm chậm chu kỳ build và phản hồi một cách không cần thiết. Mục tiêu nên là có nhiều unit test nhanh chóng bao phủ tất cả các nhánh logic và ít hơn, tập trung hơn vào các integration test để xác minh các điểm kết nối quan trọng giữa các tầng ứng dụng."

---

### **Bài tập thực hành**

1.  **Cài đặt:** Tạo một dự án Spring Boot mới với các starter `Web`, `JPA`, `H2 Database`, và `Lombok`.
2.  **Tạo một Entity:** Định nghĩa một entity `Product` với `id` (Long), `name` (String), và `price` (double).
3.  **Tạo một Repository:** Tạo một interface `ProductRepository` kế thừa từ `JpaRepository<Product, Long>`.
4.  **Tạo một Service:** Tạo một `ProductService` với phương thức `applyDiscount(Long productId, double discountPercentage)` tìm một sản phẩm theo ID, tính giá đã giảm, cập nhật sản phẩm và trả về nó.
5.  **Nhiệm vụ của bạn:**
    *   Viết một **unit test** cho `ProductService`. Mock `ProductRepository`. Kiểm tra cụ thể logic giảm giá. Điều gì xảy ra nếu giảm giá 100%? Điều gì xảy ra nếu nó là số âm (nên ném ra một exception)?
    *   Viết một **integration test** cho `ProductService`. Sử dụng `@DataJpaTest`. Trong bài kiểm thử của bạn, hãy lưu một sản phẩm thật vào cơ sở dữ liệu, gọi phương thức `applyDiscount`, sau đó sử dụng repository để lấy lại sản phẩm và khẳng định rằng giá của nó đã được cập nhật chính xác trong cơ sở dữ liệu.

Bài tập này sẽ củng cố sự hiểu biết của bạn về ranh giới giữa hai loại kiểm thử quan trọng này.

***

Tiếp theo, chúng ta sẽ tìm hiểu sâu hơn về cách Spring `ApplicationContext` được tải trong các bài kiểm thử và tầm quan trọng của việc cô lập kiểm thử.

***

#### **Chủ đề 1.2: Cách Spring Application Context được tải trong Integration Tests**

#### **Lý thuyết: Phép màu đằng sau hậu trường**

Trong một ứng dụng Spring Boot thông thường, phương thức `main` kích hoạt việc tạo ra một `ApplicationContext`. Context này chịu trách nhiệm tạo và kết nối tất cả các bean của bạn (`@Component`, `@Service`, `@Repository`, v.v.).

Trong một integration test, bạn không có phương thức `main`. Vậy làm thế nào context được tạo ra? Điều này được xử lý bởi **Spring TestContext Framework**.

Khi bạn chạy một lớp kiểm thử được chú thích bằng một annotation như `@SpringBootTest` hoặc `@DataJpaTest`, JUnit 5 (thông qua `@ExtendWith(SpringExtension.class)` được tự động bao gồm trong các annotation kiểm thử của Spring Boot) sẽ giao quyền kiểm soát cho Spring TestContext Framework.

Framework này chịu trách nhiệm:
1.  **Đọc Cấu hình Kiểm thử:** Nó kiểm tra các annotation trên lớp kiểm thử của bạn (`@SpringBootTest`, `@ActiveProfiles`, `@MockBean`, v.v.) để hiểu bạn cần loại context nào.
2.  **Tải `ApplicationContext`:** Nó xây dựng và khởi động một `ApplicationContext` dựa trên cấu hình đó.
3.  **Tiêm Phụ thuộc:** Nó tiêm các bean từ context đã tải vào các trường trong lớp kiểm thử của bạn bằng cách sử dụng `@Autowired`.
4.  **Quản lý Giao dịch (Transactions):** Nó có thể bắt đầu và rollback các transaction cho mỗi phương thức kiểm thử.
5.  **Lưu trữ Context vào Bộ đệm (Caching):** Đây là tính năng quan trọng nhất để tối ưu hiệu suất. Framework này một cách thông minh sẽ **lưu trữ `ApplicationContext` vào bộ đệm** và tái sử dụng nó qua các lớp kiểm thử khác nhau.

#### **Bộ đệm Context cực kỳ quan trọng (The All-Important Context Cache)**

Tải một Spring `ApplicationContext` là một hoạt động tốn kém. Nó có thể bao gồm việc quét classpath, khởi tạo bean, và tiêm phụ thuộc, có thể mất vài giây. Nếu Spring phải làm điều này cho mỗi *lớp* kiểm thử, một bộ gồm hàng trăm bài kiểm thử sẽ trở nên chậm chạp một cách đau đớn.

Để giải quyết vấn đề này, Spring TestContext Framework duy trì một **bộ đệm tĩnh (static cache) các context**.

**Cách hoạt động:**
1.  Khi lớp kiểm thử đầu tiên chạy, framework tạo ra một **khóa (key)** duy nhất dựa trên cấu hình của nó (các annotation, properties, profiles, v.v.).
2.  Nó xây dựng `ApplicationContext` và lưu trữ nó trong bộ đệm với khóa này.
3.  Khi lớp kiểm thử tiếp theo chạy, framework cũng tạo một khóa cho nó.
4.  Nó kiểm tra bộ đệm:
    *   Nếu một context với một **khóa giống hệt** đã tồn tại, nó sẽ được **tái sử dụng**. Chi phí khởi động hoàn toàn được bỏ qua.
    *   Nếu khóa khác, một **`ApplicationContext` mới sẽ được tạo** và thêm vào bộ đệm.

**Điều gì làm cho khóa cấu hình của một context trở nên duy nhất?**
Bất kỳ thay đổi nào đối với những điều sau (và nhiều hơn nữa) sẽ dẫn đến việc tạo ra một context mới:

*   **Các annotation được sử dụng:** Một bài kiểm thử với `@WebMvcTest` có khóa khác với một bài kiểm thử với `@SpringBootTest`.
*   **Thuộc tính `classes`:** `@SpringBootTest(classes = MyConfig.class)` khác với `@SpringBootTest(classes = AnotherConfig.class)`.
*   **Các profile đang hoạt động:** `@ActiveProfiles("test")` khác với `@ActiveProfiles("dev")`.
*   **Ghi đè thuộc tính:** `@TestPropertySource(properties = "my.prop=value1")` khác với `@TestPropertySource(properties = "my.prop=value2")`.
*   **Tập hợp các định nghĩa `@MockBean` hoặc `@SpyBean`:** Một lớp kiểm thử mock `ServiceA` sẽ không chia sẻ context với một lớp kiểm thử mock `ServiceB`.

**Hình dung về Bộ đệm:**

```
Bắt đầu chạy Kiểm thử...

TestClassA.java (@SpringBootTest, @ActiveProfiles("test"))
  -> Không tìm thấy context trong bộ đệm cho cấu hình này.
  -> ĐANG TẠO CONTEXT MỚI (mất 5 giây)...
  -> Lưu context vào bộ đệm với khóa [SpringBootTest, profiles={'test'}, ...].
  -> Đang chạy các bài kiểm thử trong TestClassA...

TestClassB.java (@SpringBootTest, @ActiveProfiles("test"))
  -> Tìm thấy context trong bộ đệm cho khóa [SpringBootTest, profiles={'test'}, ...].
  -> ĐANG TÁI SỬ DỤNG CONTEXT TỪ BỘ ĐỆM (mất 5 mili giây).
  -> Đang chạy các bài kiểm thử trong TestClassB...

TestClassC.java (@SpringBootTest, @ActiveProfiles("other"))
  -> Không tìm thấy context trong bộ đệm cho cấu hình này.
  -> ĐANG TẠO CONTEXT MỚI (mất 5 giây)...
  -> Lưu context vào bộ đệm với khóa [SpringBootTest, profiles={'other'}, ...].
  -> Đang chạy các bài kiểm thử trong TestClassC...

TestClassD.java (@DataJpaTest)
  -> Không tìm thấy context trong bộ đệm cho cấu hình này.
  -> ĐANG TẠO CONTEXT MỚI (mất 2 giây)... (Nhanh hơn vì nó là một "lát cắt")
  -> Lưu context vào bộ đệm với khóa [DataJpaTest, ...].
  -> Đang chạy các bài kiểm thử trong TestClassD...
```

---

#### **Chủ đề 1.3: Sự cô lập trong Kiểm thử, Trạng thái Cơ sở dữ liệu và Rollback**

Ngay cả khi `ApplicationContext` được tái sử dụng, bản thân các bài kiểm thử phải được **cô lập** với nhau. Một bài kiểm thử không nên bị ảnh hưởng bởi trạng thái do một bài kiểm thử đã chạy trước đó để lại.

Điều này đặc biệt quan trọng đối với trạng thái cơ sở dữ liệu. Nếu `TestA` tạo một người dùng tên "john", `TestB` không nên thất bại vì nó cũng cố gắng tạo một người dùng tên "john", dẫn đến vi phạm ràng buộc duy nhất (unique constraint violation).

Spring giải quyết vấn đề này bằng **hỗ trợ kiểm thử giao dịch (transactional test support)**.

**Cách hoạt động:**

Theo mặc định, các phương thức kiểm thử sử dụng các annotation kiểm thử của Spring liên quan đến cơ sở dữ liệu (như `@DataJpaTest` và thậm chí cả `@SpringBootTest`) đều có tính giao dịch (transactional).

*   **`@Transactional` trên các Phương thức Kiểm thử:** Spring thực chất bao bọc mỗi phương thức kiểm thử trong một transaction.
*   **Tự động Rollback:** Khi kết thúc việc thực thi phương thức kiểm thử (dù nó thành công hay thất bại), transaction này sẽ được **tự động rollback (hoàn tác)**.

Điều này có nghĩa là bất kỳ hoạt động cơ sở dữ liệu nào được thực hiện trong quá trình kiểm thử (chèn, cập nhật, xóa) đều được hoàn tác hoàn toàn. Trạng thái cơ sở dữ liệu được đặt lại về trạng thái trước khi bài kiểm thử bắt đầu, đảm bảo một "bảng sạch" cho bài kiểm thử tiếp theo.

**Ví dụ:**

```java
@DataJpaTest
class UserRepositoryTest {

    @Autowired
    private UserRepository userRepository;

    @Test
    void testA_saveUser() {
        userRepository.save(new User(null, "userA"));
        // Tại thời điểm này, "userA" có trong cơ sở dữ liệu trong transaction này
        assertThat(userRepository.count()).isEqualTo(1);
    } // <-- Transaction cho testA được ROLLBACK ở đây. "userA" đã biến mất.

    @Test
    void testB_verifyNoUsers() {
        // Bài kiểm thử này bắt đầu với một "bảng sạch" vì testA đã được rollback.
        assertThat(userRepository.count()).isEqualTo(0);
    }
}
```

**Mock các Phụ thuộc để Cô lập**

Một khía cạnh quan trọng khác của sự cô lập là kiểm soát các phụ thuộc. Đôi khi bạn cần kiểm tra cách thành phần của mình hoạt động khi một phụ thuộc thất bại hoặc trả về dữ liệu cụ thể. Đây là lúc mocking phát huy tác dụng.

*   `@MockBean`: Annotation này, do Spring Boot cung cấp, cho phép bạn thêm một mock Mockito vào `ApplicationContext`. Nó sẽ thay thế bất kỳ bean hiện có nào cùng loại bằng mock đó.

Đây là một công cụ mạnh mẽ để cô lập:
*   Bạn có thể ngăn một bài kiểm thử thực hiện một cuộc gọi mạng thực sự đến một dịch vụ bên ngoài.
*   Bạn có thể buộc một phụ thuộc ném ra một exception để kiểm tra việc xử lý lỗi của bạn.
*   Bạn có thể cung cấp dữ liệu cụ thể từ một phụ thuộc mà không cần phải thiết lập nó trong cơ sở dữ liệu.

**Lưu ý quan trọng:** Việc sử dụng `@MockBean` sẽ thay đổi khóa cấu hình của context, điều này có khả năng ngăn cản việc tái sử dụng context. Hãy sử dụng nó khi cần thiết, nhưng hãy nhận thức về ảnh hưởng hiệu suất của nó.

---

### **Các câu hỏi phỏng vấn**

1.  **Câu hỏi:** "Tối ưu hóa hiệu suất chính mà Spring sử dụng khi chạy integration test là gì và nó hoạt động như thế nào?"
    *   **Trả lời:** "Tối ưu hóa chính là **bộ đệm `ApplicationContext`**. Spring TestContext Framework tạo ra một khóa duy nhất dựa trên cấu hình của bài kiểm thử (annotations, active profiles, properties, v.v.). Nếu một lớp kiểm thử sau đó có cấu hình giống hệt, Spring sẽ tái sử dụng context đã được khởi động từ bộ đệm, điều này làm giảm đáng kể thời gian thực thi kiểm thử bằng cách tránh quá trình khởi động context tốn kém."

2.  **Câu hỏi:** "Bạn có hai lớp kiểm thử. `TestA` chạy thành công. Khi `TestB` chạy, nó thất bại với lỗi vi phạm khóa duy nhất của cơ sở dữ liệu. Nguyên nhân có khả năng nhất là gì và bạn sẽ khắc phục nó như thế nào?"
    *   **Trả lời:** "Nguyên nhân có khả năng nhất là thiếu sự cô lập trong kiểm thử. `TestA` có thể đã commit dữ liệu vào cơ sở dữ liệu mà không được dọn dẹp trước khi `TestB` bắt đầu. Giải pháp tiêu chuẩn trong Spring là đảm bảo quản lý kiểm thử giao dịch được kích hoạt. Hầu hết các "lát cắt" kiểm thử của Spring Boot, như `@DataJpaTest`, đều làm điều này theo mặc định. Đối với một `@SpringBootTest` đầy đủ, bạn có thể thêm `@Transactional` vào lớp kiểm thử hoặc các phương thức riêng lẻ. Điều này đảm bảo rằng bất kỳ thay đổi cơ sở dữ liệu nào được thực hiện trong một bài kiểm thử đều được rollback khi kết thúc, cung cấp một trạng thái sạch cho bài kiểm thử tiếp theo."

3.  **Câu hỏi:** "Tôi có 10 lớp kiểm thử đều sử dụng `@SpringBootTest` và chạy rất chậm. Điều đầu tiên bạn sẽ kiểm tra để cải thiện hiệu suất của chúng là gì?"
    *   **Trả lời:** "Điều đầu tiên tôi sẽ kiểm tra là tại sao application context không được lưu vào bộ đệm và tái sử dụng. Tôi sẽ tìm kiếm những khác biệt nhỏ trong cấu hình của mỗi lớp kiểm thử. Chúng có sử dụng các `@ActiveProfiles` khác nhau không? Chúng có khai báo các thuộc tính `@TestPropertySource` khác nhau không? Chúng có sử dụng `@MockBean` để mock các service khác nhau không? Mỗi biến thể này sẽ khiến Spring tạo ra một application context mới, tốn kém. Mục tiêu là hài hòa các cấu hình này càng nhiều càng tốt giữa các bài kiểm thử để context có thể được tái sử dụng, giúp tăng tốc đáng kể bộ kiểm thử."

---

### **Bài tập thực hành**

1.  **Mục tiêu:** Thấy được việc lưu trữ context vào bộ đệm và rollback giao dịch trong thực tế.
2.  **Cài đặt:** Sử dụng cùng một dự án từ bài tập trước (entity `Product`, repository, service).
3.  **Nhiệm vụ:**
    *   Tạo một lớp kiểm thử `ProductRepoTestA` được chú thích bằng `@DataJpaTest`.
    *   Trong lớp này, viết một phương thức kiểm thử duy nhất `testSaveProduct()` lưu một `Product` mới và khẳng định rằng số lượng trong repository là 1.
    *   Tạo một lớp kiểm thử thứ hai `ProductRepoTestB` **giống hệt** lớp đầu tiên (bạn có thể sao chép và dán).
    *   Thêm logging vào các phương thức kiểm thử của bạn (ví dụ: `System.out.println("Đang chạy test trong TestA")`).
    *   Chạy *tất cả* các bài kiểm thử trong dự án của bạn.
4.  **Quan sát:**
    *   **Tải Context:** Bạn sẽ thấy trong logs rằng Spring context chỉ được tải **một lần**.
    *   **Cô lập Kiểm thử:** Cả hai bài kiểm thử đều sẽ thành công, mặc dù cả hai đều kiểm tra số lượng là 1. Điều này chứng tỏ rằng dữ liệu được lưu trong `TestA` đã được rollback trước khi `TestB` bắt đầu.

Bài tập đơn giản này sẽ chứng minh hai khái niệm cơ bản nhất mà chúng ta vừa thảo luận: lưu trữ context vào bộ đệm để tăng tốc độ và rollback giao dịch để đảm bảo sự cô lập.

***

Tiếp theo, chúng ta sẽ đi sâu vào annotation kiểm thử mạnh mẽ và toàn diện nhất: `@SpringBootTest`.

***

#### **Chủ đề 1.4: Tìm hiểu về `@SpringBootTest` và các tham số của nó**

`@SpringBootTest` là "nhà vô địch hạng nặng" của các annotation kiểm thử trong Spring Boot. Mục đích chính của nó là tải một `ApplicationContext` đầy đủ của Spring cho một integration test. Khi bạn cần kiểm thử sự tích hợp của nhiều tầng ứng dụng với nhau, đây thường là công cụ bạn sẽ tìm đến.

#### **Lý thuyết: "Lát cắt" Toàn bộ Ứng dụng**

Không giống như các annotation kiểm thử "lát cắt" (slice test) mà chúng ta sẽ đề cập sau (như `@WebMvcTest` hoặc `@DataJpaTest`), `@SpringBootTest` không đưa ra giả định nào về những gì bạn muốn kiểm thử. Theo mặc định, nó tải **mọi thứ**.

Nó làm điều này bằng cách tìm kiếm lớp ứng dụng chính của bạn (lớp được chú thích bằng `@SpringBootApplication`) và sử dụng cấu hình của nó để tạo ra một `ApplicationContext` giống hệt cho bài kiểm thử của bạn. Điều này có nghĩa là tất cả các service, repository, component, và cấu hình của bạn sẽ được tải, giống như khi bạn chạy ứng dụng thực tế.

**Khi nào nên sử dụng `@SpringBootTest`:**

*   Khi bạn cần kiểm thử sự tương tác giữa nhiều tầng mà không được bao phủ bởi một bài kiểm thử lát cắt cụ thể (ví dụ: kiểm thử một luồng từ tầng service đến tầng caching và sau đó đến cơ sở dữ liệu).
*   Khi bạn muốn chạy một bài kiểm thử đầu cuối (end-to-end) trên một máy chủ đang chạy thực sự.
*   Khi bạn đang kiểm thử một thứ gì đó phụ thuộc vào vòng đời của ứng dụng, như các tác vụ `@Scheduled`.
*   Khi bạn cần tải một cấu hình không được bao gồm trong một bài kiểm thử lát cắt theo mặc định.

**Khi nào nên TRÁNH `@SpringBootTest`:**

*   **Đối với Unit Tests:** Không bao giờ sử dụng `@SpringBootTest` để kiểm tra logic nội tại của một phương thức duy nhất. Đây là sai lầm phổ biến và tốn kém nhất mà các lập trình viên mắc phải. Nó giống như dùng búa tạ để đập một hạt dẻ.
*   **Đối với các Kiểm thử Tầng Web Đơn giản:** Nếu bạn chỉ cần kiểm thử các controller, tuần tự hóa yêu cầu/phản hồi và xác thực, `@WebMvcTest` hiệu quả hơn nhiều.
*   **Đối với các Kiểm thử JPA/Cơ sở dữ liệu Đơn giản:** Nếu bạn chỉ cần kiểm thử các repository và các câu truy vấn JPA, `@DataJpaTest` nhanh hơn nhiều.

---

#### **Các tham số chính của `@SpringBootTest`**

Hành vi của `@SpringBootTest` có thể được tinh chỉnh bằng các thuộc tính của nó. Thuộc tính quan trọng nhất là `webEnvironment`.

##### **`webEnvironment`**

Thuộc tính này kiểm soát việc có khởi động một máy chủ web thực sự (như Tomcat hoặc Netty) cho bài kiểm thử của bạn hay không. Nó có bốn giá trị khả thi:

1.  **`WebEnvironment.MOCK` (Mặc định)**
    *   **Nó làm gì:** Tải một `ApplicationContext` đầy đủ của Spring nhưng **không** khởi động một máy chủ HTTP thực sự. Thay vào đó, nó cung cấp một môi trường servlet "giả lập" (mock).
    *   **Cách kiểm thử:** Bạn có thể tiêm và sử dụng `MockMvc` để gửi các yêu cầu đến các controller của mình mà không tốn chi phí mạng.
    *   **Khi nào sử dụng:** Đây là cài đặt phổ biến nhất. Sử dụng nó khi bạn muốn kiểm thử các controller và logic nghiệp vụ của mình cùng nhau trong một context đầy đủ, nhưng bạn không cần kiểm thử trên một máy chủ đang chạy thực sự trên một cổng cụ thể. Nó nhanh hơn việc khởi động một máy chủ thực sự.

2.  **`WebEnvironment.RANDOM_PORT`**
    *   **Nó làm gì:** Tải một `ApplicationContext` đầy đủ và khởi động một **máy chủ web nhúng thực sự** trên một cổng ngẫu nhiên, khả dụng.
    *   **Cách kiểm thử:** Bạn có thể sử dụng `TestRestTemplate` hoặc `WebTestClient` để thực hiện các cuộc gọi HTTP thực tế đến `http://localhost:<cổng_ngẫu_nhiên>`. Cổng này có thể được tiêm vào bài kiểm thử của bạn.
    *   **Khi nào sử dụng:** Dành cho các bài kiểm thử đầu cuối (end-to-end) nơi bạn muốn xác minh toàn bộ ngăn xếp HTTP, từ yêu cầu của client đến cơ sở dữ liệu và ngược lại. Đây là loại integration test toàn diện nhất.

3.  **`WebEnvironment.DEFINED_PORT`**
    *   **Nó làm gì:** Tương tự như `RANDOM_PORT`, nhưng nó sử dụng cổng được định nghĩa trong `application.properties` của bạn (ví dụ: `server.port=8080`).
    *   **Khi nào sử dụng:** Hiếm khi. Điều này có thể dẫn đến xung đột cổng nếu ứng dụng chính của bạn hoặc một bài kiểm thử khác đã chạy trên cổng đó. `RANDOM_PORT` hầu như luôn là một lựa chọn an toàn hơn.

4.  **`WebEnvironment.NONE`**
    *   **Nó làm gì:** Tải một `ApplicationContext` đầy đủ nhưng không cung cấp bất kỳ môi trường web nào (mock hay thật).
    *   **Khi nào sử dụng:** Khi ứng dụng của bạn không phải là một ứng dụng web (ví dụ: một tiện ích dòng lệnh hoặc một microservice điều khiển bằng tin nhắn) và bạn không muốn chi phí thiết lập ngay cả một web context giả lập.

##### **Các tham số hữu ích khác**

*   **`classes`**: Điều này cho phép bạn chỉ định các lớp cấu hình nào sẽ được tải thay vì để Spring tìm lớp `@SpringBootApplication` chính của bạn. Đây là một cách mạnh mẽ để giới hạn phạm vi của bài kiểm thử và tăng tốc nó. Ví dụ: `@SpringBootTest(classes = {UserService.class, UserRepository.class, TestDatabaseConfig.class})` tạo ra một application context mini, tùy chỉnh chỉ cho bài kiểm thử này.

*   **`properties`**: Một cách thuận tiện để ghi đè các thuộc tính từ tệp `application.properties` của bạn trực tiếp trong annotation.
    *   **Ví dụ:** `@SpringBootTest(properties = "spring.datasource.url=jdbc:h2:mem:testdb")`

---

#### **Ví dụ Code: `MOCK` so với `RANDOM_PORT`**

Giả sử chúng ta có một `ProductController` sử dụng `ProductService` của chúng ta.

**1. Kiểm thử với `webEnvironment = MOCK`**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;

// Mặc định là MOCK, nhưng chúng ta có thể chỉ định rõ ràng.
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.MOCK)
@AutoConfigureMockMvc // Kích hoạt và cấu hình MockMvc
class ProductControllerMockEnvTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ProductRepository productRepository;

    @Test
    void whenProductExists_thenGetProductReturns200() throws Exception {
        // Arrange: Lưu một sản phẩm trực tiếp vào repo
        productRepository.save(new Product(1L, "Test Book", 29.99));

        // Act & Assert
        mockMvc.perform(get("/products/1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.name").value("Test Book"));
    }
}
```

**2. Kiểm thử với `webEnvironment = RANDOM_PORT`**

```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.boot.test.web.server.LocalServerPort; // Cổng được tiêm vào
import org.springframework.http.ResponseEntity;

import static org.assertj.core.api.Assertions.assertThat;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class ProductControllerRandomPortTest {

    @LocalServerPort // Tiêm cổng ngẫu nhiên được máy chủ sử dụng
    private int port;

    @Autowired
    private TestRestTemplate restTemplate;

    @Autowired
    private ProductRepository productRepository;

    @Test
    void whenProductExists_thenGetProductReturns200() {
        // Arrange: Lưu một sản phẩm
        productRepository.save(new Product(1L, "Test Book", 29.99));

        // Act: Thực hiện một cuộc gọi HTTP thực sự
        String url = "http://localhost:" + port + "/products/1";
        ResponseEntity<Product> response = restTemplate.getForEntity(url, Product.class);

        // Assert
        assertThat(response.getStatusCode().is2xxSuccessful()).isTrue();
        assertThat(response.getBody().getName()).isEqualTo("Test Book");
    }
}
```

---

### **Các câu hỏi phỏng vấn**

1.  **Câu hỏi:** "Sự khác biệt giữa `@SpringBootTest(webEnvironment = WebEnvironment.MOCK)` và `@WebMvcTest` là gì? Cả hai đều sử dụng `MockMvc`."
    *   **Trả lời:** "Sự khác biệt chính là phạm vi của `ApplicationContext`. `@WebMvcTest` chỉ tải tầng web: các controller, bộ tuần tự hóa JSON, filter và các bean liên quan đến web khác. Nó rõ ràng *không* tải các tầng service hoặc repository; bạn phải mock chúng bằng `@MockBean`. Ngược lại, `@SpringBootTest(webEnvironment = MOCK)` tải *toàn bộ* application context, bao gồm cả service, repository và cấu hình cơ sở dữ liệu. Bạn sử dụng `MockMvc` để tương tác với ứng dụng đã được kết nối đầy đủ này, nhưng không có chi phí của một máy chủ HTTP thực sự."

2.  **Câu hỏi:** "Bạn đang viết một bài kiểm thử cho một phương thức `@Scheduled` chạy mỗi giờ. Bạn sẽ kiểm thử nó như thế nào và bạn sẽ sử dụng annotation nào?"
    *   **Trả lời:** "Một tác vụ định kỳ không phải là một phần của tầng web hay tầng dữ liệu, vì vậy một bài kiểm thử lát cắt không phù hợp. Tôi sẽ sử dụng `@SpringBootTest` để tải application context đầy đủ, điều này đảm bảo cấu hình lập lịch (`@EnableScheduling`) được kích hoạt và bean `@Scheduled` của tôi được tạo. Sau đó, tôi sẽ mock `Clock` hoặc sử dụng một thư viện như Awaitility để đợi tác vụ thực thi và xác minh các tác dụng phụ của nó, chẳng hạn như một bản cập nhật cơ sở dữ liệu hoặc một cuộc gọi đến một service đã được mock."

3.  **Câu hỏi:** "Khi nào là một ý tưởng tốt để sử dụng thuộc tính `classes` trong `@SpringBootTest`?"
    *   **Trả lời:** "Sử dụng thuộc tính `classes` là một chiến lược tối ưu hóa tuyệt vời. Nếu bạn biết bạn chỉ đang kiểm thử sự tích hợp giữa một service cụ thể (`OrderService`) và các phụ thuộc của nó (`ProductService`, `OrderRepository`), bạn có thể chỉ định `classes = {OrderService.class, ProductService.class, OrderRepository.class}`. Điều này ngăn Spring quét toàn bộ classpath và tải các thành phần không liên quan như web controller hoặc các tác vụ định kỳ, dẫn đến một bài integration test nhanh hơn và tập trung hơn nhiều trong khi vẫn sử dụng một Spring context thực sự."

---

### **Bài tập thực hành**

1.  **Mục tiêu:** Viết một integration test đầy đủ bằng cách sử dụng `@SpringBootTest`.
2.  **Cài đặt:** Sử dụng cùng một dự án `Product`. Tạo một `ProductController` với một endpoint GET `/products/{id}` lấy một sản phẩm bằng cách sử dụng `ProductService`.
3.  **Nhiệm vụ:**
    *   Tạo một lớp kiểm thử mới `ProductControllerIntegrationTest`.
    *   Chú thích nó bằng `@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)`.
    *   Tiêm `TestRestTemplate` và `ProductRepository`.
    *   Viết một bài kiểm thử thực hiện các bước sau:
        1.  Lưu một `Product` trực tiếp bằng repository để thiết lập trạng thái cơ sở dữ liệu.
        2.  Xây dựng URL bằng cách sử dụng cổng ngẫu nhiên được tiêm vào.
        3.  Sử dụng `restTemplate` để thực hiện một yêu cầu GET đến endpoint của bạn.
        4.  Khẳng định rằng mã trạng thái HTTP là 200 OK và phần thân của phản hồi chứa các chi tiết sản phẩm chính xác.

Bài tập này sẽ cung cấp cho bạn kinh nghiệm thực tế với hình thức integration testing toàn diện nhất trong Spring Boot.

***

Tiếp theo, chúng ta sẽ bắt đầu tìm hiểu sâu về các **Annotation Kiểm thử Lát cắt (Slice Test Annotations)**, bắt đầu với `@WebMvcTest`.