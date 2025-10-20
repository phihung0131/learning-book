### **Giai đoạn 1, Bài 7: Unit & Integration Testing trong Ứng dụng Spring Boot**

Spring Boot không phát minh lại bánh xe; nó cung cấp một lớp tích hợp mạnh mẽ trên nền tảng các công cụ bạn đã học (JUnit 5, Mockito, AssertJ). Mục tiêu chính của framework kiểm thử của Spring Boot là giúp việc kiểm thử các phần khác nhau của ứng dụng trở nên dễ dàng với cấu hình tối thiểu cần thiết.

Nó đạt được điều này thông qua hai cách tiếp cận chính:
1.  **Kiểm thử Tích hợp Toàn bộ Ngữ cảnh (Full Context Integration Tests):** Tải toàn bộ ứng dụng của bạn để kiểm tra cách tất cả các thành phần hoạt động cùng nhau.
2.  **Lát cắt Kiểm thử (Test Slices):** Chỉ tải một "lát cắt" cụ thể của ứng dụng (ví dụ: chỉ tầng Web hoặc chỉ tầng Dữ liệu) để viết các bài test nhanh hơn, tập trung hơn.

---

### **1. Phụ thuộc `spring-boot-starter-test`**

Đầu tiên, một tin tốt. Khi bạn tạo một dự án Spring Boot, nó thường bao gồm phụ thuộc `spring-boot-starter-test` trong `pom.xml` của bạn.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```

Phụ thuộc duy nhất này tự động nhập vào:
*   **JUnit 5:** Framework kiểm thử cốt lõi.
*   **Mockito:** Thư viện mocking.
*   **AssertJ:** Thư viện assertion lưu loát.
*   **Spring Test & Spring Boot Test:** Các tiện ích cốt lõi để kiểm thử các ứng dụng Spring.
*   Và các thư viện hữu ích khác như `JsonPath` để kiểm thử các phản hồi JSON.

Bạn có được mọi thứ bạn cần trong một gói duy nhất, được quản lý phiên bản một cách hoàn hảo bởi Spring Boot.

### **2. `@SpringBootTest`: Bài kiểm thử Tích hợp "Tất cả trong một"**

Annotation `@SpringBootTest` là công cụ kiểm thử mạnh mẽ và "nặng" nhất trong kho vũ khí của Spring. Khi bạn thêm annotation này vào một lớp test, nó sẽ khởi động và tải **toàn bộ Spring Application Context**.

*   **Nó làm gì:** Nó khởi động toàn bộ ứng dụng của bạn—tìm lớp `@SpringBootApplication` của bạn, quét tất cả các component (`@Service`, `@Repository`, `@Controller`, v.v.), thực hiện tiêm phụ thuộc, và làm cho toàn bộ ứng dụng của bạn có sẵn cho việc kiểm thử.
*   **Khi nào sử dụng:** Cho các bài kiểm thử tích hợp đầu cuối nơi bạn cần nhiều tầng của ứng dụng (ví dụ: Controller -> Service -> Repository) hoạt động cùng nhau. Bởi vì nó tải mọi thứ, nó chậm hơn đáng kể so với một unit test hoặc một slice test.

**Các Annotation chính được sử dụng với `@SpringBootTest`:**

*   **`@Autowired`**: Trong ngữ cảnh test, bạn có thể sử dụng nó để tiêm bất kỳ bean thật nào từ application context của bạn trực tiếp vào lớp test.
*   **`@MockBean`**: Đây là sự thay thế mạnh mẽ của Spring Boot cho `@Mock` của Mockito. Nó bảo Spring tìm một bean của một loại nhất định trong application context và **thay thế nó bằng một mock của Mockito**. Nếu không có bean nào như vậy tồn tại, nó sẽ thêm mock đó vào context. Đây là cách chính để bạn mock các phụ thuộc trong các bài kiểm thử tích hợp của Spring.

**Ví dụ: Kiểm thử một `UserService` sử dụng `UserRepository`**

```java
// Mã nguồn chính: UserService.java
@Service
public class UserService {
    private final UserRepository userRepository;
    public UserService(UserRepository userRepository) { this.userRepository = userRepository; }

    public String getUsername(Long id) {
        User user = userRepository.findById(id).orElse(null);
        return user != null ? user.getUsername() : "User Not Found";
    }
}
```

```java
// Mã Test: UserServiceIntegrationTest.java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.Mockito.when;

@SpringBootTest // <-- Tải toàn bộ ứng dụng Spring
class UserServiceIntegrationTest {

    // Tiêm instance THẬT của UserService từ context
    @Autowired
    private UserService userService;

    // Tìm bean UserRepository trong context và THAY THẾ nó bằng một mock
    @MockBean
    private UserRepository mockUserRepository;

    @Test
    void getUsername_shouldReturnUsername_whenUserExists() {
        // Arrange: Stub hành vi của repository đã được mock
        User sampleUser = new User(1L, "alex");
        when(mockUserRepository.findById(1L)).thenReturn(Optional.of(sampleUser));

        // Act: Gọi phương thức trên service thật
        String username = userService.getUsername(1L);

        // Assert: Kiểm tra kết quả
        assertThat(username).isEqualTo("alex");
    }
}
```

**Điều gì đã xảy ra ở đây?**
1.  `@SpringBootTest` đã khởi động ứng dụng của chúng ta.
2.  Spring đã tạo một bean `UserService` thật.
3.  `@MockBean` thấy rằng `UserService` cần một `UserRepository`, vì vậy nó đã tạo một mock của `UserRepository` và tiêm mock đó vào `UserService` thật.
4.  Bài test của chúng ta đã sử dụng `when()` của Mockito để kiểm soát mock.
5.  Chúng ta đã kiểm thử logic của `UserService` thật một cách cô lập khỏi cơ sở dữ liệu, nhưng trong một môi trường Spring đầy đủ.

---

### **3. Lát cắt Kiểm thử (Test Slices): Các bài kiểm thử Tích hợp Nhanh hơn, Tập trung hơn**

Việc tải toàn bộ application context cho mỗi bài test là chậm và không cần thiết. Nếu bạn chỉ muốn kiểm thử tầng Web, tại sao lại phải tải tầng Cơ sở dữ liệu? **Lát cắt Kiểm thử** giải quyết vấn đề này.

Một annotation "lát cắt" bảo Spring Boot chỉ tải các bean cần thiết cho một phần cụ thể của ứng dụng của bạn.

#### **A. `@WebMvcTest`: Kiểm thử tầng Controller**

Đây là một trong những lát cắt hữu ích nhất. Nó tập trung hoàn toàn vào tầng web.

*   **Nó làm gì:**
    *   Tải **chỉ** các bean liên quan đến tầng web (`@Controller`, `@RestController`, `@ControllerAdvice`, `Filter`, v.v.).
    *   Nó **KHÔNG** tải các bean `@Service` hay `@Repository`. Điều này rất quan trọng.
    *   Nó tự động cấu hình một bean đặc biệt gọi là `MockMvc`, một công cụ mạnh mẽ để thực hiện các yêu cầu HTTP mô phỏng đến các controller của bạn mà không cần một máy chủ web thực sự.
*   **Cách hoạt động:** Bởi vì các bean `@Service` và `@Repository` không được tải, bạn **phải** cung cấp các mock cho bất kỳ phụ thuộc nào mà controller của bạn cần, bằng cách sử dụng `@MockBean`.

**Ví dụ: Kiểm thử một `UserController`**

```java
// Mã nguồn chính: UserController.java
@RestController
public class UserController {
    private final UserService userService;
    public UserController(UserService userService) { this.userService = userService; }

    @GetMapping("/users/{id}")
    public ResponseEntity<String> getUser(@PathVariable Long id) {
        String username = userService.getUsername(id);
        return ResponseEntity.ok(username);
    }
}
```

```java
// Mã Test: UserControllerTest.java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(UserController.class) // <-- Lát cắt cho tầng web, nhắm vào MỘT controller
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc; // Công cụ để thực hiện các yêu cầu HTTP giả

    @MockBean
    private UserService mockUserService; // Phụ thuộc của controller PHẢI được mock

    @Test
    void getUser_shouldReturnUsername() throws Exception {
        // Arrange: Stub tầng service
        when(mockUserService.getUsername(1L)).thenReturn("test-user");

        // Act & Assert: Thực hiện một yêu cầu GET giả và kiểm tra phản hồi
        mockMvc.perform(get("/users/1"))
               .andExpect(status().isOk()) // Kiểm tra HTTP 200 OK
               .andExpect(content().string("test-user")); // Kiểm tra nội dung phản hồi
    }
}
```

Bài test này cực kỳ nhanh vì nó không bao giờ đụng đến `UserService` thật hay tầng cơ sở dữ liệu. Đây là một bài test hoàn hảo cho logic của controller: kiểm tra ánh xạ yêu cầu, mã trạng thái, và nội dung phản hồi.

#### **B. `@DataJpaTest`: Kiểm thử tầng Repository/Persistence**

Đây là lát cắt kiểm thử được thiết kế riêng cho việc kiểm thử các JPA repository. Mục tiêu của nó là xác minh rằng các truy vấn, ánh xạ thực thể và các phương thức repository của bạn hoạt động chính xác với một cơ sở dữ liệu.

*   **Nó làm gì:**
    *   Tải **chỉ** cấu hình liên quan đến JPA và các bean `@Repository` của bạn.
    *   Nó **KHÔNG** tải các bean `@Service` hay `@Controller`.
    *   **Quan trọng nhất, nó cấu hình một cơ sở dữ liệu trong bộ nhớ (in-memory database) (như H2) theo mặc định.** Điều này có nghĩa là các bài test của bạn chạy trên một cơ sở dữ liệu tạm thời, nhanh chóng, chứ không phải cơ sở dữ liệu production thật của bạn (như PostgreSQL hay MySQL).
    *   Bao bọc mỗi phương thức test trong một **giao dịch (transaction) mà sẽ được rollback** vào cuối phương thức. Điều này đảm bảo mỗi bài test bắt đầu với một cơ sở dữ liệu sạch, cung cấp sự cô lập hoàn hảo cho test.
    *   Nó cung cấp một bean đặc biệt gọi là `TestEntityManager` để thiết lập trạng thái cơ sở dữ liệu của bạn.

**`TestEntityManager`**: Hãy coi đây là một trình trợ giúp được thiết kế riêng cho các bài test. Nó cho phép bạn lưu trữ và tìm kiếm các thực thể trong các phương thức test của mình mà không cần sử dụng repository thực tế của bạn, cái mà bạn có thể đang cố gắng kiểm thử.

**Ví dụ: Kiểm thử một `UserRepository`**

```java
// Mã nguồn chính: User.java (Entity)
@Entity
public class User {
    @Id @GeneratedValue
    private Long id;
    private String username;
    // Constructors, getters...
}

// Mã nguồn chính: UserRepository.java
@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByUsername(String username);
}
```

```java
// Mã Test: UserRepositoryTest.java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;
import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest // <-- Lát cắt cho tầng persistence
class UserRepositoryTest {

    @Autowired
    private TestEntityManager entityManager; // Trình trợ giúp cho các hoạt động DB trong test

    @Autowired
    private UserRepository userRepository; // Repository thực tế chúng ta đang kiểm thử

    @Test
    void findByUsername_shouldReturnUser_whenUserExists() {
        // Arrange: Tạo và lưu một người dùng vào cơ sở dữ liệu trong bộ nhớ
        User newUser = new User("testuser");
        entityManager.persistAndFlush(newUser); // persist() lưu nó, flush() ghi nó vào DB

        // Act: Gọi phương thức repository chúng ta muốn kiểm thử
        Optional<User> foundUser = userRepository.findByUsername("testuser");

        // Assert: Kiểm tra rằng người dùng chính xác đã được trả về
        assertThat(foundUser).isPresent();
        assertThat(foundUser.get().getUsername()).isEqualTo("testuser");
    }

    @Test
    void findByUsername_shouldReturnEmpty_whenUserDoesNotExist() {
        // Arrange: (Không có người dùng nào được lưu cho trường hợp test này)

        // Act
        Optional<User> foundUser = userRepository.findByUsername("nonexistent");

        // Assert
        assertThat(foundUser).isNotPresent();
    }
}
```

Bài test này nhanh, an toàn (nó không bao giờ đụng đến DB thật của bạn), và được cô lập hoàn hảo. Nó mang lại cho bạn sự tự tin cao rằng truy vấn tùy chỉnh `findByUsername` của bạn được viết chính xác.

### **4. Còn các Unit Test thuần túy cho các Service thì sao?**

Cho đến nay, chúng ta đã thảo luận về các bài kiểm thử tích hợp (`@SpringBootTest`) và các bài test lát cắt (`@WebMvcTest`, `@DataJpaTest`). Nhưng còn `UserService` từ ví dụ đầu tiên của chúng ta thì sao? Chúng ta có luôn cần phải tải một Spring context để kiểm thử nó không?

**Hoàn toàn không!**

Đây là nơi kiến thức cốt lõi của bạn từ Bài 1-6 phát huy tác dụng. Một lớp service thường chỉ là một đối tượng Java thuần túy (POJO). Nếu nó không dựa vào các tính năng phức tạp của Spring, cách tốt nhất để kiểm thử nó là với một **unit test JUnit + Mockito thuần túy**. Đây là loại test nhanh nhất và đơn giản nhất bạn có thể viết.

**Xem lại bài test `UserService` mà không có bất kỳ annotation Spring Test nào:**

```java
// Mã Test: UserServiceUnitTest.java

import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;
import static org.assertj.core.api.Assertions.assertThat;
import static org.mockito.Mockito.when;

// Sử dụng extension Mockito tiêu chuẩn, KHÔNG phải bất kỳ trình chạy Spring nào.
@ExtendWith(MockitoExtension.class)
class UserServiceUnitTest {

    @Mock
    private UserRepository mockUserRepository; // Mockito mock tiêu chuẩn

    @InjectMocks
    private UserService userService; // Tiêm Mockito tiêu chuẩn

    @Test
    void getUsername_shouldReturnUsername_whenUserExists() {
        // Arrange
        User sampleUser = new User(1L, "alex");
        when(mockUserRepository.findById(1L)).thenReturn(Optional.of(sampleUser));

        // Act
        String username = userService.getUsername(1L);

        // Assert
        assertThat(username).isEqualTo("alex");
    }
}
```

Bài test này chạy trong mili giây vì nó không khởi động *bất kỳ* phần nào của Spring context. Nó là một unit test thực sự.

### **Tóm tắt Bài 7 & Chiến lược Kiểm thử**

Bây giờ bạn đã có một bộ công cụ kiểm thử hoàn chỉnh cho một ứng dụng Spring Boot, kết hợp kiến thức nền tảng của bạn với các công cụ cụ thể của Spring.

Đây là cách tất cả chúng phù hợp với **Kim tự tháp Kiểm thử**:

*   **Đáy của Kim tự tháp (Unit Tests ~70%):**
    *   **Mục tiêu:** Kiểm thử logic của một lớp duy nhất trong sự cô lập.
    *   **Công cụ:** **JUnit 5 + Mockito + AssertJ.**
    *   **Ví dụ:** Kiểm thử một lớp `@Service` không có bất kỳ annotation Spring nào (`UserServiceUnitTest`).

*   **Giữa của Kim tự tháp (Integration Tests ~20%):**
    *   **Mục tiêu:** Kiểm thử cách một tầng duy nhất (lát cắt) tích hợp với framework hoặc cơ sở dữ liệu của nó.
    *   **Công cụ:** **`@WebMvcTest`** cho controllers, **`@DataJpaTest`** cho repositories.
    *   **Ví dụ:** Kiểm thử ánh xạ yêu cầu của một controller hoặc các truy vấn tùy chỉnh của một repository.

*   **Đỉnh của Kim tự tháp (End-to-End Tests ~10%):**
    *   **Mục tiêu:** Kiểm thử một luồng người dùng hoàn chỉnh qua toàn bộ ứng dụng.
    *   **Công cụ:** **`@SpringBootTest`**.
    *   **Ví dụ:** Kiểm thử rằng một yêu cầu POST đến một controller dẫn đến việc dữ liệu được lưu thành công vào cơ sở dữ liệu.

Chiến lược phân tầng này mang lại cho bạn những điều tốt nhất của cả hai thế giới: một bộ lớn các unit test nhanh như chớp cho logic nghiệp vụ của bạn, và một bộ nhỏ hơn, có mục tiêu các bài kiểm thử tích hợp để đảm bảo tất cả các thành phần kết nối chính xác.