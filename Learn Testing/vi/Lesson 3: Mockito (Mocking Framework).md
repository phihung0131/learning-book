### **Giai đoạn 1, Bài 3: Mockito (Framework Mocking)**

Cho đến nay, chúng ta đã kiểm thử các lớp đơn giản, khép kín như một `Calculator`. Nhưng trong một ứng dụng thực tế, các đối tượng hiếm khi hoạt động một mình. Một `UserService` có thể phụ thuộc vào `UserRepository` để nói chuyện với cơ sở dữ liệu. Một `PaymentService` có thể phụ thuộc vào `StripeClient` để xử lý thanh toán.

Nếu bài test cho `UserService` thực sự cố gắng kết nối với một cơ sở dữ liệu thật, nó **không còn là một unit test**. Nó trở thành một **bài kiểm thử tích hợp (integration test)** chậm, không đáng tin cậy và phức tạp.

**Vấn đề:** Làm thế nào để chúng ta kiểm thử logic của `UserService` trong sự **cô lập** hoàn toàn khỏi `UserRepository`?

**Giải pháp:** Chúng ta sử dụng một **mock**.

### **1. Khái niệm về Mocking, Stubbing và Verification**

#### **Mock là gì?**

Một **đối tượng mock** là một loại đối tượng đóng thế (test double) đặc biệt—một đối tượng "giả" mà chúng ta kiểm soát hoàn toàn. Nó trông và hoạt động giống như đối tượng thật (ví dụ: một `UserRepository`), nhưng thay vì thực hiện hành động thật (như truy cập cơ sở dữ liệu), nó làm chính xác những gì chúng ta bảo nó làm trong suốt thời gian của một bài test duy nhất.

Hãy coi nó như một hình nộm thử nghiệm va chạm. Các nhà sản xuất ô tô không đặt một người thật vào xe để kiểm tra túi khí mới. Họ sử dụng một hình nộm mà họ có thể kiểm soát và theo dõi để mô phỏng hành vi của một người trong một vụ va chạm. Một mock là hình nộm thử nghiệm va chạm của chúng ta cho các phụ thuộc.

#### **Ba Hoạt động Chính của Mocking**

Làm việc với mock bao gồm ba bước riêng biệt:

1.  **Tạo (Creation):** Đầu tiên, bạn tạo đối tượng mock. Mockito là framework cho phép chúng ta tạo ra những đối tượng giả này một cách dễ dàng.
2.  **Stubbing (Thiết lập Hành vi):** Đây là phần "bảo mock phải làm gì". Bạn định nghĩa các quy tắc cho mock. Ví dụ: "**Khi** phương thức `findUserById(5)` được gọi trên mock `UserRepository`, **thì trả về** một đối tượng `User` cụ thể." Điều này còn được gọi là "lập trình hành vi" của mock. Lời gọi phương thức mà chúng ta đang giả mạo được gọi là một "stub".
3.  **Xác minh (Verification):** Sau khi bạn gọi phương thức đang được kiểm thử, bạn có thể tùy chọn đặt câu hỏi cho mock để xem nó có được sử dụng đúng cách hay không. Ví dụ: "**Xác minh** rằng phương thức `save(user)` trên mock `UserRepository` đã được gọi **chính xác một lần**."

Hãy minh họa bằng một ví dụ khái niệm:

```java
// Mã nguồn chính
public class UserService {
    private final UserRepository userRepository;

    public UserService(UserRepository userRepository) { // Phụ thuộc được tiêm vào
        this.userRepository = userRepository;
    }

    public String getUserEmail(int userId) {
        User user = userRepository.findUserById(userId);
        if (user != null) {
            return user.getEmail();
        }
        return null;
    }
}
```

**Unit Test cho `getUserEmail`:**

1.  **Tạo:** Chúng ta cần một `UserRepository` giả. Chúng ta sử dụng Mockito để tạo một đối tượng `UserRepository` mock.
2.  **Stubbing:** Chúng ta cần kiểm thử kịch bản "tìm thấy người dùng". Vì vậy, chúng ta bảo mock của mình: "**Khi `findUserById(10)` được gọi, thì trả về một đối tượng `User` mới với email 'test@example.com'.**"
3.  **Hành động:** Chúng ta gọi `userService.getUserEmail(10)`.
4.  **Assertion:** Chúng ta sử dụng JUnit/AssertJ để khẳng định rằng email trả về là `"test@example.com"`.
5.  **Xác minh (Tùy chọn):** Chúng ta có thể xác minh rằng `findUserById(10)` thực sự đã được gọi trên mock của chúng ta.

Bài test này chạy trong mili giây, không bao giờ đụng đến cơ sở dữ liệu, và chỉ kiểm thử logic bên trong phương thức `getUserEmail`. Đó là sức mạnh của mocking.

### **2. Sự khác biệt giữa `@Mock`, `@InjectMocks`, và `@Spy`**

Mockito cung cấp các annotation để làm cho việc tạo và tiêm mock trở nên sạch sẽ và đơn giản. Để sử dụng chúng, bạn cần bảo JUnit 5 kích hoạt quá trình xử lý annotation của Mockito. Điều này thường được thực hiện bằng cách thêm `@ExtendWith(MockitoExtension.class)` vào lớp test của bạn.

```java
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.junit.jupiter.MockitoExtension;

@ExtendWith(MockitoExtension.class) // QUAN TRỌNG: Điều này kích hoạt các annotation của Mockito
class UserServiceTest {
    // ... các mock sẽ được định nghĩa ở đây
}
```

#### **`@Mock`**

Đây là annotation chính mà bạn sẽ sử dụng. Nó tạo ra một mock hoàn chỉnh (một "hình nộm") của một lớp hoặc giao diện.

*   **Mục đích:** Để tạo một đối tượng giả cho một phụ thuộc.
*   **Hành vi:** Theo mặc định, tất cả các phương thức trên một đối tượng `@Mock` không làm gì cả. Chúng trả về `null`, các collection rỗng, hoặc giá trị nguyên thủy mặc định (0, false). Bạn phải stub một cách tường minh bất kỳ phương thức nào bạn cần để trả về một giá trị.
*   **Sử dụng:** Bạn sử dụng `@Mock` cho tất cả các phụ thuộc mà lớp bạn đang kiểm thử dựa vào.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository mockUserRepository; // Tạo một UserRepository giả

    // ... các bài test ở đây
}
```

#### **`@InjectMocks`**

Annotation này tạo một instance của lớp bạn muốn kiểm thử và tự động tiêm bất kỳ trường nào được chú thích bằng `@Mock` hoặc `@Spy` vào đó.

*   **Mục đích:** Để khởi tạo **Lớp đang được kiểm thử** và tiêm các phụ thuộc đã được mock của nó.
*   **Hành vi:** Mockito cố gắng tiêm các mock bằng constructor, setter, hoặc tiêm thuộc tính. Tiêm qua constructor là cách sạch sẽ và phổ biến nhất.
*   **Sử dụng:** Bạn sử dụng `@InjectMocks` trên lớp mà bạn thực sự đang kiểm thử (ví dụ: `UserService`).

Hãy kết hợp chúng:

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository mockUserRepository; // Phụ thuộc

    @InjectMocks
    private UserService userService; // Lớp chúng ta đang kiểm thử

    @Test
    void testSomething() {
        // Ở đây, 'userService' là một instance UserService thật,
        // nhưng trường 'userRepository' của nó giữ đối tượng mock của chúng ta.
    }
}
```

#### **`@Spy`**

Một `@Spy` là một "mock một phần". Nó bao bọc một **instance thật** của một đối tượng. Đây là một tính năng nâng cao và ít được sử dụng hơn.

*   **Mục đích:** Để sử dụng một đối tượng thật nhưng chỉ ghi đè (stub) một vài phương thức của nó.
*   **Hành vi:** Khi bạn gọi một phương thức trên một spy, **phương thức thật sẽ được gọi**, trừ khi bạn đã stub nó một cách tường minh.
*   **Khi nào nên sử dụng (Sử dụng một cách cẩn thận!):**
    *   Kiểm thử mã nguồn cũ (legacy code) khó tái cấu trúc.
    *   Khi bạn cần gọi một phương thức thật trên một đối tượng nhưng lại giả mạo một phương thức khác trên *cùng một đối tượng*. Điều này hiếm gặp trong mã được thiết kế tốt.
*   **Nguy hiểm:** Spy có thể làm cho các bài test trở nên khó hiểu. Bạn đang trộn lẫn hành vi thật và giả, điều này có thể dẫn đến các bài test khó hiểu và khó gỡ lỗi. **Luôn ưu tiên `@Mock` khi có thể.**

```java
class SpyExampleTest {
    @Spy
    private List<String> spiedList = new ArrayList<>();

    @Test
    void testSpy() {
        // Gọi một phương thức thật
        spiedList.add("one");
        spiedList.add("two");
        assertEquals(2, spiedList.size()); // Phương thức add() thật đã được gọi

        // Stub một phương thức
        when(spiedList.size()).thenReturn(100); // Bây giờ chúng ta đang ghi đè size()
        assertEquals(100, spiedList.size()); // Giá trị đã stub được trả về
    }
}
```

**Bảng tóm tắt:**

| Annotation | Mục đích | Là Đối tượng thật? | Hành vi Mặc định |
| :--- | :--- | :--- | :--- |
| **`@Mock`** | Giả mạo một phụ thuộc | **Không**, nó là một hình nộm | Các phương thức không làm gì, trả về `null`/`0`/`false` |
| **`@InjectMocks`**| Tạo Lớp đang được kiểm thử | **Có**, nó là một instance thật | Tiêm các `@Mock` vào đối tượng này |
| **`@Spy`** | Mock một phần đối tượng thật| **Có**, nó bao bọc một instance thật| Các phương thức gọi đến việc triển khai **thật** trừ khi được stub |

### **3. Thiết lập Hành vi: `when(...)`, `thenReturn(...)`, `thenThrow(...)`, và `thenAnswer(...)`**

Một khi bạn có một đối tượng mock, nó chỉ là một cái vỏ rỗng. **Stubbing** là quá trình mang lại hành vi cho cái vỏ đó. Cách tiêu chuẩn để làm điều này trong Mockito là sử dụng cấu trúc `when(...)`.

Cú pháp rất dễ đọc và tuân theo mẫu "Given-When-Then", nhưng được áp dụng cho hành vi của mock:
`when(mock.methodCall()).thenReturn(valueToReturn);`

Hãy sử dụng ví dụ `UserService` và `UserRepository` của chúng ta.

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest {

    @Mock
    private UserRepository mockUserRepository; // Phụ thuộc giả của chúng ta

    @InjectMocks
    private UserService userService; // Lớp đang được kiểm thử

    private User sampleUser;

    @BeforeEach
    void setUp() {
        // Tạo một người dùng mẫu để các bài test của chúng ta sử dụng
        sampleUser = new User(1, "testuser", "test@example.com");
    }

    // ... các bài test theo sau
}
```

#### **`when(...).thenReturn(...)`**

Đây là phương thức stubbing phổ biến nhất. Bạn sử dụng nó khi bạn muốn một phương thức được mock trả về một giá trị cụ thể.

*   **Mục tiêu:** Kiểm thử kịch bản trong đó người dùng được tìm thấy trong repository.
*   **Logic:** Chúng ta phải bảo `mockUserRepository` trả về `sampleUser` của chúng ta khi phương thức `findById` của nó được gọi với ID `1`.

```java
@Test
void getUserById_shouldReturnUser_whenUserExists() {
    // 1. Stubbing hành vi của mock
    when(mockUserRepository.findById(1)).thenReturn(sampleUser);

    // 2. Gọi phương thức chúng ta đang kiểm thử
    User foundUser = userService.getUserById(1);

    // 3. Khẳng định kết quả
    assertNotNull(foundUser);
    assertEquals("test@example.com", foundUser.getEmail());
}
```

Còn trường hợp người dùng *không* tồn tại thì sao? Repository thật sẽ trả về `null`. Chúng ta cũng có thể stub điều đó.

```java
@Test
void getUserById_shouldReturnNull_whenUserDoesNotExist() {
    // 1. Stubbing: khi findById được gọi với bất kỳ ID nào khác, nó nên trả về null.
    // Theo mặc định, một mock đã trả về null cho các kiểu đối tượng, nhưng thực hành tốt
    // là nên tường minh cho rõ ràng.
    when(mockUserRepository.findById(99)).thenReturn(null);

    // 2. Hành động
    User foundUser = userService.getUserById(99);

    // 3. Khẳng định
    assertNull(foundUser);
}
```

#### **`when(...).thenThrow(...)`**

Đôi khi bạn cần kiểm tra cách mã của bạn phản ứng khi một phụ thuộc ném ra một ngoại lệ. Ví dụ, điều gì sẽ xảy ra nếu cơ sở dữ liệu không khả dụng và repository ném ra một `DatabaseException`?

`thenThrow(...)` cho phép bạn stub một phương thức để ném ra một ngoại lệ khi nó được gọi.

*   **Mục tiêu:** Kiểm tra rằng service của chúng ta xử lý các lỗi cơ sở dữ liệu một cách ổn thỏa.

```java
// Giả sử UserService của chúng ta có một phương thức như thế này:
public void deleteUser(int userId) {
    try {
        userRepository.delete(userId);
    } catch (DatabaseException e) {
        // Có thể nó ghi lại lỗi và ném ra một ngoại lệ tùy chỉnh, cụ thể hơn
        throw new UserDeletionFailedException("Không thể xóa người dùng", e);
    }
}

@Test
void deleteUser_shouldThrowCustomException_whenDatabaseFails() {
    // 1. Stubbing: Bảo mock ném ra một ngoại lệ khi delete được gọi.
    when(mockUserRepository.delete(1)).thenThrow(new DatabaseException("DB is down"));
    // Bạn cũng có thể truyền lớp: .thenThrow(DatabaseException.class);

    // 2. Hành động & Khẳng định (sử dụng assertThrows của JUnit 5)
    assertThrows(UserDeletionFailedException.class, () -> {
        userService.deleteUser(1);
    });
}
```

#### **`when(...).thenAnswer(...)`**

Đây là một hình thức stubbing nâng cao và mạnh mẽ hơn. Thay vì trả về một giá trị cố định, `thenAnswer(...)` cho phép bạn cung cấp một đoạn logic nhỏ (một lambda) sẽ được thực thi khi mock được gọi. Điều này hữu ích khi giá trị trả về cần được tính toán động dựa trên các đối số đầu vào.

*   **Mục tiêu:** Kiểm tra một kịch bản trong đó giá trị trả về của mock phụ thuộc vào đầu vào của nó.
*   **Ví dụ:** Hãy tưởng tượng một mock sẽ trả về một đối tượng `User` có email dựa trên ID mà nó được yêu cầu tìm.

```java
@Test
void findById_shouldReturnUserWithDynamicEmail() {
    // 1. Stubbing với một Answer
    when(mockUserRepository.findById(anyInt())).thenAnswer(invocation -> {
        Integer id = invocation.getArgument(0); // Lấy đối số đầu tiên được truyền cho findById
        return new User(id, "user" + id, "user" + id + "@example.com");
    });

    // 2. Hành động
    User user5 = userService.getUserById(5);
    User user10 = userService.getUserById(10);

    // 3. Khẳng định
    assertEquals("user5@example.com", user5.getEmail());
    assertEquals("user10@example.com", user10.getEmail());
}
```

Trong lambda, `invocation.getArgument(0)` lấy đối số đầu tiên được truyền cho phương thức được mock (`findById`). `anyInt()` là một "trình khớp đối số" (argument matcher), một khái niệm chúng ta sẽ sớm đề cập.

### **4. Xác minh Tương tác: `verify()`, `times()`, `never()`**

Trong khi **stubbing** là về việc thiết lập hành vi trước khi bài test chạy, **xác minh (verification)** xảy ra *sau khi* hành động test đã được thực thi. Mục đích của nó là để kiểm tra xem các phương thức nhất định trên các đối tượng mock của bạn có được gọi với các đối số chính xác hay không.

Xác minh đặc biệt quan trọng khi kiểm thử các phương thức không trả về giá trị (tức là các phương thức `void`). Đối với các phương thức này, bạn không thể khẳng định một kết quả; toàn bộ mục đích của chúng là tạo ra một tác dụng phụ bằng cách tương tác với một phụ thuộc.

Điểm khởi đầu cho tất cả việc xác minh là phương thức `Mockito.verify()`.

Hãy tưởng tượng một `RegistrationService` lưu một người dùng mới và sau đó gửi cho họ một email chào mừng.

```java
// Lớp đang được kiểm thử
public class RegistrationService {
    private final UserRepository userRepository;
    private final EmailService emailService;

    // Constructor...

    public void registerUser(String username, String email) {
        if (username == null || email == null) {
            throw new IllegalArgumentException("Tên người dùng và email không được null");
        }
        User newUser = new User(username, email);
        userRepository.save(newUser);
        emailService.sendWelcomeEmail(newUser); // Đây là tương tác chúng ta muốn xác minh
    }
}
```

Bây giờ, hãy viết một bài test cho nó.

```java
@ExtendWith(MockitoExtension.class)
class RegistrationServiceTest {

    @Mock
    private UserRepository mockUserRepository;

    @Mock
    private EmailService mockEmailService;

    @InjectMocks
    private RegistrationService registrationService;

    @Test
    void registerUser_shouldSaveUserAndSendWelcomeEmail() {
        // 1. Arrange (Thiết lập)
        String username = "testuser";
        String email = "test@example.com";
        User user = new User(username, email);

        // Chúng ta không cần stub bất cứ điều gì cho bài test này, vì các phương thức
        // chúng ta đang gọi trên các mock (save, sendWelcomeEmail) trả về void.

        // 2. Act (Gọi phương thức đang được kiểm thử)
        registrationService.registerUser(username, email);

        // 3. Verify (Kiểm tra các tương tác với các mock)

        // Xác minh rằng phương thức save đã được gọi trên repository chính xác một lần
        // với một đối tượng khớp với người dùng của chúng ta. Chúng ta sẽ sử dụng một ArgumentMatcher bây giờ.
        verify(mockUserRepository).save(any(User.class));

        // Xác minh rằng phương thức sendWelcomeEmail cũng đã được gọi chính xác một lần.
        verify(mockEmailService).sendWelcomeEmail(any(User.class));
    }
}
```

#### **Xác minh Mặc định: `times(1)`**

Khi bạn viết `verify(mock).methodCall()`, đó là viết tắt cho `verify(mock, times(1)).methodCall()`. Nó kiểm tra rằng phương thức đã được gọi **chính xác một lần**. Đây là loại xác minh phổ biến nhất.

#### **`times(n)`**

Bạn có thể xác minh một số lần gọi chính xác bằng cách sử dụng `times(n)`.

*   **Ví dụ:** Hãy tưởng tượng một dịch vụ thử gửi lại email 3 lần trước khi từ bỏ.
    ```java
    @Test
    void shouldRetrySendingEmailThreeTimes() {
        // ... thiết lập để làm cho việc gửi email thất bại

        // Act
        emailGateway.sendEmailWithRetries(message);

        // Verify
        verify(mockEmailClient, times(3)).send(message);
    }
    ```

#### **`never()`**

Đây là một trình xác minh cực kỳ mạnh mẽ và hữu ích. `never()` là một bí danh cho `times(0)`. Nó khẳng định rằng một tương tác cụ thể **đã không xảy ra**. Điều này hoàn hảo để kiểm tra logic điều kiện.

Hãy kiểm tra luồng tiêu cực của phương thức `registerUser` của chúng ta.

```java
@Test
void registerUser_shouldNotSaveOrSendEmail_whenUsernameIsNull() {
    // 1. Arrange
    String username = null;
    String email = "test@example.com";

    // 2. Act & Assert (kiểm tra ngoại lệ mong đợi)
    assertThrows(IllegalArgumentException.class, () -> {
        registrationService.registerUser(username, email);
    });

    // 3. Verify (kiểm tra rằng KHÔNG có tương tác nào xảy ra với các mock)
    verify(mockUserRepository, never()).save(any(User.class));
    verify(mockEmailService, never()).sendWelcomeEmail(any(User.class));
}
```
Bài test này xác nhận một cách tuyệt vời rằng nếu việc xác thực thất bại, dịch vụ của chúng ta sẽ dừng xử lý một cách chính xác và không thực hiện bất kỳ tác dụng phụ không mong muốn nào.

Các trình xác minh hữu ích khác bao gồm `atLeast(n)`, `atMost(n)`, và `atLeastOnce()`.

### **5. `ArgumentCaptor` (Bắt các giá trị được truyền cho Mock)**

Cho đến nay, `verify()` đã giúp chúng ta xác nhận *liệu* một phương thức có được gọi hay không và *bao nhiêu lần*. Chúng ta đã sử dụng các trình khớp đối số như `any(User.class)` hoặc `eq("some string")` để khớp với các lần gọi.

Nhưng nếu chúng ta cần xác minh điều gì đó *về đối tượng đã được truyền* cho mock thì sao?

**Vấn đề:**

Trong ví dụ `registerUser` của chúng ta, `RegistrationService` tạo một đối tượng `new User(...)` bên trong phương thức. Chúng ta không có tham chiếu đến đối tượng cụ thể này trong bài test của mình, vì vậy chúng ta không thể truyền nó vào `verify(mock).save(ourUserObject)`. Chúng ta đã sử dụng `any(User.class)`, nhưng đó là một kiểm tra mơ hồ. Nó không xác nhận rằng đối tượng `User` được truyền cho phương thức `save` có tên người dùng và email chính xác.

Làm thế nào chúng ta có thể bắt được đối tượng "vô hình" đã được truyền cho mock để chúng ta có thể chạy các assertion trên nó?

**Giải pháp: `ArgumentCaptor`**

Một `ArgumentCaptor` là một đối tượng Mockito đặc biệt hoạt động như một cái lưới. Bạn sử dụng nó trong một lệnh gọi `verify()` để "bắt" một đối số đã được truyền cho một phương thức được mock. Sau khi bị bắt, bạn có thể lấy giá trị và thực hiện các assertion chi tiết trên nó.

Quá trình này bao gồm ba bước:
1.  **Tạo:** Tạo một instance của `ArgumentCaptor`.
2.  **Bắt:** Sử dụng captor trong lệnh gọi `verify()` của bạn.
3.  **Khẳng định:** Lấy giá trị đã bắt được từ captor và chạy các assertion của bạn.

Hãy tinh chỉnh bài test `registerUser` của chúng ta để chính xác hơn.

```java
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.ArgumentCaptor;
import org.mockito.Captor;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.mockito.Mockito.verify;


@ExtendWith(MockitoExtension.class)
class RegistrationServiceTest {

    @Mock
    private UserRepository mockUserRepository;

    @Mock
    private EmailService mockEmailService;

    @InjectMocks
    private RegistrationService registrationService;

    // Lựa chọn 1: Tạo captor bằng annotation
    @Captor
    private ArgumentCaptor<User> userArgumentCaptor;

    @Test
    void registerUser_shouldSaveUserWithCorrectDetails() {
        // Arrange
        String username = "testuser";
        String email = "test@example.com";

        // Lựa chọn 2: Tạo captor thủ công nếu không dùng @Captor
        // ArgumentCaptor<User> userArgumentCaptor = ArgumentCaptor.forClass(User.class);

        // Act
        registrationService.registerUser(username, email);

        // Bắt đối tượng User đã được truyền cho phương thức save
        // Thay vì any(User.class), chúng ta sử dụng captor.capture()
        verify(mockUserRepository).save(userArgumentCaptor.capture());

        // Lấy giá trị đã bắt được
        User capturedUser = userArgumentCaptor.getValue();

        // Assert: Bây giờ chúng ta có thể chạy các assertion chi tiết trên đối tượng đã bắt được
        assertEquals(username, capturedUser.getUsername());
        assertEquals(email, capturedUser.getEmail());
    }
}
```

#### **Cách hoạt động:**

1.  **Annotation `@Captor`:** Giống như `@Mock`, annotation này bảo `MockitoExtension` khởi tạo `ArgumentCaptor` cho chúng ta. Đó là cách sạch sẽ nhất để tạo ra nó.
2.  **`userArgumentCaptor.capture()`:** Chúng ta truyền nó vào bên trong lệnh gọi `verify()`. Nó trông giống như một trình khớp đối số, nhưng nó có một tác dụng phụ: nó bí mật lưu lại đối số đã được truyền cho phương thức `save`.
3.  **`userArgumentCaptor.getValue()`:** Phương thức này lấy giá trị cuối cùng được bắt. Nếu phương thức được mock được gọi nhiều lần, `getValue()` trả về đối số từ lần gọi cuối cùng. Bạn có thể sử dụng `getAllValues()` để lấy một `List` chứa tất cả các đối số từ tất cả các lần gọi.

`ArgumentCaptor` là một công cụ không thể thiếu để xác minh các tác dụng phụ của mã của bạn với độ chính xác cao. Nó đảm bảo rằng phương thức của bạn không chỉ gọi các phụ thuộc của nó, mà còn gọi chúng với *dữ liệu được xây dựng chính xác*.

### **6. Các Trường hợp Biên: Nhiều Mock, Mock Nối chuỗi, Phương thức Void, Ngoại lệ, và Mocking các Lớp Static/Final**

#### **1. Mocking Lệnh gọi Phương thức Nối chuỗi**

Đôi khi bạn gặp phải mã sử dụng kiểu API "lưu loát" hoặc nối chuỗi, như `builder.setName("X").setAge(20).build()`. Điều gì sẽ xảy ra nếu một phụ thuộc trả về một đối tượng, mà từ đó bạn lại cần gọi một phương thức khác?

*   **Kịch bản:** `String street = userService.getAddress().getStreet();`
*   **Vấn đề:** Nếu bạn mock `userService`, `userService.getAddress()` sẽ trả về `null` theo mặc định, và lệnh gọi `.getStreet()` sẽ gây ra `NullPointerException` trong bài test của bạn.
*   **Giải pháp:** Bạn cần mock cả hai cấp. Bạn có thể tạo một mock cho `Address` và để mock `userService` trả về nó.

    ```java
    @Test
    void testChainedCall() {
        // 1. Tạo mock cho cả hai đối tượng trong chuỗi
        @Mock UserService mockUserService;
        @Mock Address mockAddress;

        // 2. Stub lệnh gọi đầu tiên để trả về mock thứ hai
        when(mockUserService.getAddress()).thenReturn(mockAddress);

        // 3. Stub lệnh gọi thứ hai trên mock được trả về
        when(mockAddress.getStreet()).thenReturn("Main St");

        // Act & Assert
        assertEquals("Main St", mockUserService.getAddress().getStreet());
    }
    ```
*   **Giải pháp thay thế (Recursive Mocking - Sử dụng cẩn thận):** Mockito hỗ trợ "deep stubs" hoặc mocking đệ quy, tự động tạo mock cho các lệnh gọi nối chuỗi. Điều này có thể làm cho các bài test kém tường minh và dễ vỡ hơn, vì vậy nên sử dụng một cách hạn chế.
    ```java
    // Tạo mock với DEEP_STUBS
    UserService mockUserService = mock(UserService.class, RETURNS_DEEP_STUBS);

    // Bây giờ bạn có thể stub lệnh gọi cuối cùng một cách trực tiếp
    when(mockUserService.getAddress().getStreet()).thenReturn("Main St");

    assertEquals("Main St", mockUserService.getAddress().getStreet());
    ```

#### **2. Xử lý các phương thức `void`**

Làm thế nào để bạn stub một phương thức không trả về gì? Bạn không thể sử dụng `when(...).thenReturn(...)`. Thay vào đó, Mockito cung cấp một họ cú pháp khác: `do...().when()`.

*   **Vấn đề:** Bạn muốn kiểm tra điều gì xảy ra khi một phương thức `void`, như `userRepository.delete(user)`, ném ra một ngoại lệ.
*   **Giải pháp:** Sử dụng `doThrow()`.

    ```java
    @Test
    void shouldHandleException_whenVoidMethodIsCalled() {
        // Arrange: Sử dụng cú pháp do-when cho các phương thức void
        doThrow(new DatabaseException("Connection failed"))
            .when(mockUserRepository)
            .delete(any(User.class));

        // Act & Assert
        assertThrows(DatabaseException.class, () -> {
            someService.deleteUser(sampleUser);
        });
    }
    ```
    *   **Các phương thức `do...()` khác:**
    *   **`doNothing()`:** Đây là hành vi mặc định cho các phương thức void được mock, nhưng bạn có thể sử dụng nó để ghi đè hành vi thật của một spy.
    *   **`doAnswer()`:** Cho phép bạn thực hiện một hành động phức tạp (như sửa đổi một đối số) khi phương thức void được gọi.
    *   **`doCallRealMethod()`:** Được sử dụng với spy để buộc phương thức thật phải được gọi nếu bạn đã stub nó trước đó.

**Hãy nhớ:** Đối với các phương thức `void`, cách chính để kiểm tra hiệu ứng của chúng là thông qua `verify()` và `ArgumentCaptor`.

#### **3. Mocking các phương thức Static, Final và Private (Nâng cao)**

Đây là một câu hỏi phỏng vấn rất phổ biến.

*   **Hạn chế:** Theo mặc định, Mockito **không thể** mock các lớp `final`, phương thức `static`, hoặc phương thức `private`. Nó hoạt động bằng cách tạo một lớp con (một proxy) của lớp bạn muốn mock tại thời điểm chạy. Điều này là không thể đối với các lớp `final` (không thể được kế thừa) hoặc các phương thức `static` (thuộc về lớp, không phải instance).
*   **Giải pháp chính thức: `mockito-inline`**
    Để khắc phục điều này, nhóm Mockito đã tạo ra một "trình tạo mock" thay thế. Bằng cách bao gồm phụ thuộc `mockito-inline` trong `pom.xml`, bạn cho phép Mockito sử dụng công cụ đo lường Java (Java instrumentation) để sửa đổi các lớp một cách nhanh chóng, cho phép nó mock những thứ trước đây "không thể mock".

    **Ví dụ: Mocking một phương thức `static`**
    Hãy tưởng tượng bạn có một lớp tiện ích mà bạn không thể thay đổi:
    ```java
    public final class IdGenerator {
        public static UUID generateId() {
            return UUID.randomUUID();
        }
    }
    ```
    Bài test của bạn cần kiểm soát ID được tạo ra.
    ```java
    @Test
    void shouldCreateUserWithPredictableId() {
        UUID predictableId = UUID.fromString("123e4567-e89b-12d3-a456-426614174000");

        // Sử dụng một khối try-with-resources để kiểm soát phạm vi của static mock
        try (MockedStatic<IdGenerator> mockedStatic = Mockito.mockStatic(IdGenerator.class)) {

            // Stub lời gọi phương thức static
            mockedStatic.when(IdGenerator::generateId).thenReturn(predictableId);

            // Act: Gọi service sử dụng phương thức static bên trong
            User newUser = userService.createNewUser("test");

            // Assert
            assertEquals(predictableId, newUser.getId());
        }
        // Static mock được tự động đóng và đặt lại sau khối try
    }
    ```
*   **Một Lời cảnh báo:** Nhu cầu mock một phương thức `static` hoặc `private` thường là một dấu hiệu của thiết kế tồi. Thường thì tốt hơn là tái cấu trúc mã để cho phép tiêm phụ thuộc thay thế. Tuy nhiên, `mockito-inline` là một công cụ vô giá khi làm việc với mã nguồn cũ mà bạn không thể thay đổi.

---

### **7. Các thông lệ tốt nhất và Lỗi thường gặp**

1.  **Đừng Mock Mọi thứ (Over-mocking):** Mục tiêu là mock các ranh giới của hệ thống của bạn (cơ sở dữ liệu, API, v.v.). Đừng mock mọi lớp mà đối tượng của bạn nói chuyện. Nếu `ClassA` sử dụng `ClassB`, và `ClassB` là một lớp trợ giúp đơn giản trong codebase của riêng bạn, bạn có lẽ nên sử dụng `ClassB` thật trong bài test cho `ClassA`.
2.  **Đừng Mock các Đối tượng Dữ liệu:** Không bao giờ mock một DTO, một đối tượng giá trị, hoặc một thực thể (ví dụ: `User`, `Product`). Mock dùng để giả mạo **hành vi**, không phải để giữ dữ liệu. Đơn giản, rõ ràng và an toàn hơn là chỉ cần tạo một instance thật: `User user = new User("name", "email");`.
3.  **Ưu tiên `@Mock` hơn `@Spy`:** Spy trộn lẫn hành vi thật và giả, có thể làm cho các bài test khó hiểu và khó gỡ lỗi. Luôn cố gắng sử dụng một mock thuần túy trước. Spy là một công cụ để đối phó với mã nguồn cũ khó khăn.
4.  **Tập trung Xác minh vào các Tương tác Thiết yếu:** Tránh cám dỗ `verify()` mọi lệnh gọi phương thức. Điều này dẫn đến các bài test giòn (đặc tả quá chi tiết). Chỉ xác minh các tương tác quan trọng đối với hành vi bạn đang kiểm thử. Ví dụ, việc xác minh một lệnh gọi đến một logger thường là không cần thiết.

### **Tóm tắt Bài 3: Mockito**

*   **Khái niệm Cốt lõi:** Bạn hiểu sự khác biệt giữa **Mocking** (tạo đối tượng giả), **Stubbing** (lập trình hành vi của chúng), và **Verification** (kiểm tra tương tác).
*   **Annotations:** Bạn có thể sử dụng hiệu quả `@Mock` cho các phụ thuộc, `@InjectMocks` cho lớp đang được kiểm thử, và biết khi nào nên sử dụng `@Spy` một cách thận trọng.
*   **Stubbing:** Bạn có thể kiểm soát các mock của mình bằng cách sử dụng `when(...).thenReturn(...)` cho các luồng thành công và `when(...).thenThrow(...)` cho các điều kiện lỗi.
*   **Verification:** Bạn có thể xác nhận các tác dụng phụ bằng cách sử dụng `verify()`, `times()`, và `never()`.
*   **`ArgumentCaptor`:** Bạn có thể bắt và khẳng định các đối số chính xác được truyền cho các mock của mình để có các bài test với độ chính xác cao.
*   **Các Trường hợp Biên:** Bây giờ bạn đã được trang bị để xử lý các phương thức `void`, các lệnh gọi nối chuỗi, và thậm chí mock các phương thức `static` bằng `mockito-inline`.