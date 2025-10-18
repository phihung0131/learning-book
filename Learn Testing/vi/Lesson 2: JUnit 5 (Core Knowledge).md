### **Giai đoạn 1, Bài 2: JUnit 5 (Kiến thức Cốt lõi)**

JUnit là framework kiểm thử tiêu chuẩn *trên thực tế* cho Java. JUnit 5 là phiên bản lớn mới nhất, được viết lại hoàn toàn so với các phiên bản trước, làm cho nó mạnh mẽ và linh hoạt hơn nhiều. Nó cung cấp "trình chạy test" (test runner) và các annotation, assertion nền tảng mà chúng ta cần để viết và thực thi các bài test của mình.

Hãy coi JUnit như là động cơ giúp khám phá, chạy và báo cáo kết quả các bài test của bạn.

### **1. Các Annotation Cơ bản: Những Viên gạch Xây dựng nên một Lớp Test**

Annotation là các nhãn đặc biệt (metadata) mà bạn thêm vào mã Java của mình, với tiền tố là ký hiệu `@`. JUnit 5 sử dụng các annotation để xác định phương thức nào là test và chúng nên được chạy như thế nào.

Hãy tạo lớp test đầu tiên của chúng ta. Theo quy ước, nếu bạn có một lớp tên là `Calculator`, lớp test của nó sẽ được đặt tên là `CalculatorTest`.

```java
import org.junit.jupiter.api.*;

class CalculatorTest {

    // 1. @Test
    @Test
    void myFirstTest() {
        // Đây là một phương thức test!
    }

    // 2. @BeforeEach
    @BeforeEach
    void setUp() {
        System.out.println("Đang thực thi trước mỗi test...");
    }

    // 3. @AfterEach
    @AfterEach
    void tearDown() {
        System.out.println("Đang thực thi sau mỗi test...");
    }

    // 4. @BeforeAll
    @BeforeAll
    static void setupAll() {
        System.out.println("Đang thực thi trước tất cả các test...");
    }

    // 5. @AfterAll
    @AfterAll
    static void tearDownAll() {
        System.out.println("Đang thực thi sau tất cả các test...");
    }

    @Test
    void anotherTest() {
        // Đây là một phương thức test khác.
    }
}
```

Hãy phân tích chức năng của từng annotation này.

#### **`@Test`**
Đây là annotation quan trọng nhất. Nó báo hiệu cho JUnit rằng phương thức mà nó được đính kèm là một phương thức test.
*   **Mục đích:** Để định nghĩa một bài test.
*   **Đặc điểm:**
    *   Các phương thức được chú thích bằng `@Test` **không được** là `private` hoặc `static`.
    *   Chúng **không được** trả về giá trị (tức là kiểu trả về của chúng phải là `void`).

#### **`@BeforeEach` và `@AfterEach` (Thiết lập và Dọn dẹp cho mỗi Test)**
Các annotation này được sử dụng cho logic thiết lập và dọn dẹp cần chạy **trước và sau mỗi phương thức `@Test`** trong lớp.

*   **`@BeforeEach`:** Phương thức được chú thích sẽ chạy *trước* mỗi bài test. Nó hoàn hảo cho việc đặt lại trạng thái của các đối tượng để đảm bảo mỗi bài test chạy một cách cô lập. Ví dụ, tạo một instance mới của lớp bạn đang kiểm thử.
*   **`@AfterEach`:** Phương thức được chú thích sẽ chạy *sau* mỗi bài test. Nó được dùng để dọn dẹp, như đóng tệp, đặt lại trạng thái cơ sở dữ liệu, hoặc giải phóng tài nguyên để ngăn các bài test can thiệp lẫn nhau.

#### **`@BeforeAll` và `@AfterAll` (Thiết lập và Dọn dẹp ở Cấp độ Lớp)**
Các annotation này được sử dụng cho logic thiết lập và dọn dẹp tốn kém mà chỉ nên chạy **một lần cho toàn bộ lớp test**.

*   **`@BeforeAll`:** Phương thức được chú thích sẽ chạy một lần *trước khi bất kỳ* bài test nào trong lớp được chạy.
*   **`@AfterAll`:** Phương thức được chú thích sẽ chạy một lần *sau khi tất cả* các bài test trong lớp đã hoàn thành.
*   **Quy tắc Quan trọng:** Các phương thức được chú thích bằng `@BeforeAll` hoặc `@AfterAll` **phải là `static`**. Điều này là do chúng được thực thi trước khi instance của lớp test được tạo.

#### **Thứ tự Thực thi**

Nếu bạn chạy lớp `CalculatorTest` ở trên, đầu ra trên console sẽ là:

```
Đang thực thi trước tất cả các test...
Đang thực thi trước mỗi test...
Đang thực thi sau mỗi test...
Đang thực thi trước mỗi test...
Đang thực thi sau mỗi test...
Đang thực thi sau tất cả các test...
```

*(Lưu ý: Thứ tự của `myFirstTest` và `anotherTest` không được đảm bảo theo mặc định, nhưng `@BeforeEach`/`@AfterEach` sẽ bao bọc mỗi bài test).*

Điều này minh họa vòng đời một cách hoàn hảo:
1.  `@BeforeAll` chạy một lần.
2.  `@BeforeEach` -> `@Test` -> `@AfterEach` chạy cho bài test đầu tiên.
3.  `@BeforeEach` -> `@Test` -> `@AfterEach` chạy cho bài test thứ hai.
4.  `@AfterAll` chạy một lần.

### **2. Quy ước Đặt tên Test (shouldReturnX_whenY)**

Cách bạn đặt tên cho các bài test là một trong những yếu tố quan trọng nhất để tạo ra một bộ test dễ bảo trì và có giá trị. Tên của một bài test phải mô tả rõ ràng và ngắn gọn chính xác những gì đang được kiểm thử và trong điều kiện nào. Khi một bài test thất bại, tên của nó phải là tất cả những gì bạn cần để hiểu về lỗi đó.

Tránh các tên chung chung, vô nghĩa như `test1`, `testAdd`, hoặc `testError`. Chúng không cho bạn biết gì về kịch bản cụ thể đã thất bại.

Một quy ước đặt tên rất hiệu quả và được áp dụng rộng rãi là mẫu **`should_when_`** hoặc **`methodName_should_when_`**.

**Cấu trúc:** `should`**[Kết quả mong đợi]**`When`**[Trạng thái hoặc Điều kiện]**

Hãy áp dụng điều này cho ví dụ `Calculator` của chúng ta. Giả sử chúng ta có một phương thức `add(int a, int b)`.

*   **Tên Tệ:** `@Test void testAdd()`
    *   *Nếu test này thất bại, chúng ta biết được gì? Chỉ biết rằng phương thức `add` bị hỏng, nhưng không biết làm thế nào hay tại sao.*
*   **Tên Tốt:** `@Test void shouldReturnFive_whenTwoAndThreeAreAdded()`
    *   *Nếu test này thất bại, chúng ta biết chính xác kịch bản: cộng 2 và 3 đã không cho kết quả là 5. Điều này cung cấp thông tin ngay lập tức.*

Hãy xem xét một ví dụ khác từ phương thức `isUsernameValid(String username)` của `RegistrationService`.

*   **Happy Path:**
    *   `@Test void isUsernameValid_shouldReturnTrue_whenUsernameIsWithinBounds()`
*   **Trường hợp Biên:**
    *   `@Test void isUsernameValid_shouldReturnFalse_whenUsernameIsOneCharShorterThanMinimum()`
*   **Trường hợp Phủ định:**
    *   `@Test void isUsernameValid_shouldThrowIllegalArgumentException_whenUsernameIsNull()`

**Lợi ích của Quy ước này:**

1.  **Dễ đọc:** Tên test đọc giống như một câu mô tả một phần chức năng. Nó trở thành tài liệu sống.
2.  **Rõ ràng:** Khi một bài test thất bại trong báo cáo build của bạn, chỉ riêng cái tên đã cho bạn biết yêu cầu nào không còn được đáp ứng.
3.  **Tập trung:** Nó buộc bạn phải suy nghĩ về một hành vi cụ thể cho mỗi bài test, ngăn bạn viết các bài test lớn cố gắng làm quá nhiều việc cùng một lúc.

Bạn có thể sử dụng camelCase (`shouldReturnFiveWhen...`) hoặc snake_case với dấu backtick trong Java (`should_return_five_when...` - ít phổ biến hơn nhưng vẫn có thể). Điều quan trọng là phải nhất quán với nhóm của bạn.

### **3. Các Assertion (Tất cả các loại)**

Một assertion là một câu lệnh xác minh kết quả của bài test của bạn. Đó là một kiểm tra tuyên bố liệu kết quả thực tế của một hành động có khớp với kết quả mong đợi hay không. Nếu một assertion thất bại, phương thức test sẽ dừng ngay lập tức và được đánh dấu là thất bại.

JUnit 5 cung cấp một bộ phương thức assertion phong phú trong lớp `org.junit.jupiter.api.Assertions`. Thông lệ phổ biến là import tĩnh các phương thức này để dễ đọc hơn.

```java
// Ở đầu lớp test của bạn
import static org.junit.jupiter.api.Assertions.*;
```

Hãy khám phá những cái quan trọng nhất.

#### **`assertEquals(expected, actual)`**
Đây là assertion chủ lực. Nó xác minh rằng hai giá trị bằng nhau.
```java
@Test
void shouldReturnFive_whenTwoAndThreeAreAdded() {
    Calculator calculator = new Calculator();
    int result = calculator.add(2, 3);
    assertEquals(5, result, "Tổng của 2 và 3 phải là 5"); // Thông báo lỗi tùy chọn
}```

#### **`assertTrue(condition)` và `assertFalse(condition)`**
Xác minh rằng một điều kiện boolean cho trước là đúng hoặc sai.
```java
@Test
void isUsernameValid_shouldReturnTrue_whenUsernameIsNormal() {
    RegistrationService service = new RegistrationService();
    boolean isValid = service.isUsernameValid("ValidUser");
    assertTrue(isValid);
}
```

#### **`assertNotNull(object)` và `assertNull(object)`**
Xác minh rằng một đối tượng không phải là null hoặc là null.
```java
@Test
void shouldReturnAUser_whenIdExists() {
    UserService service = new UserService();
    User user = service.findById(1);
    assertNotNull(user);
}
```

#### **`assertThrows(expectedException, executable)`**
Đây là cách đúng để kiểm tra các ngoại lệ. Nó xác minh rằng một đoạn mã cụ thể (một lambda "thực thi") ném ra một loại ngoại lệ cụ thể.
```java
@Test
void isUsernameValid_shouldThrowIllegalArgumentException_whenUsernameIsNull() {
    RegistrationService service = new RegistrationService();
    // Lambda () -> service.isUsernameValid(null) là mã thực thi
    assertThrows(IllegalArgumentException.class, () -> {
        service.isUsernameValid(null);
    });
}
```

#### **`assertAll(executables...)`**
Một assertion rất mạnh mẽ. Nó nhóm nhiều assertion lại với nhau. Không giống như các assertion thông thường, `assertAll` sẽ chạy *mọi* assertion trong nhóm, và nếu có cái nào thất bại, nó sẽ báo cáo *tất cả* các lỗi cùng một lúc. Điều này rất tốt để kiểm tra nhiều thuộc tính của một đối tượng duy nhất.
```java
@Test
void shouldCreateUserWithCorrectProperties() {
    // ... mã để tạo một user
    User user = new User("alex", "alex@example.com", 25);

    assertAll("Các thuộc tính của User",
        () -> assertEquals("alex", user.getUsername()),
        () -> assertEquals("alex@example.com", user.getEmail()),
        () -> assertEquals(25, user.getAge())
    );
}
```

#### **`assertTimeout(duration, executable)`**
Được sử dụng để kiểm tra hiệu năng và đảm bảo một phương thức không mất quá nhiều thời gian để thực thi. Test sẽ thất bại nếu mã thực thi mất nhiều thời gian hơn `Duration` đã chỉ định.
```java
import java.time.Duration;

@Test
void shouldPerformActionWithin100Milliseconds() {
    assertTimeout(Duration.ofMillis(100), () -> {
        // Một số thao tác có thể chạy lâu
        service.performHeavyCalculation();
    });
}
```

### **4. Kiểm thử Tham số hóa: `@ParameterizedTest`, `@ValueSource`, `@CsvSource`**

Thông thường, bạn muốn kiểm thử cùng một phương thức với một loạt các đầu vào khác nhau để kiểm tra các happy path, điều kiện biên và kịch bản tiêu cực khác nhau.

**Cách "Tệ" (Không có Kiểm thử Tham số hóa):**

Bạn có thể viết một phương thức test riêng cho mỗi đầu vào. Điều này dẫn đến rất nhiều mã rập khuôn và trùng lặp.

```java
// ANTI-PATTERN: Mã lặp đi lặp lại
class StringUtilsTest {
    @Test
    void isBlank_shouldReturnTrueForNullString() {
        assertTrue(StringUtils.isBlank(null));
    }

    @Test
    void isBlank_shouldReturnTrueForEmptyString() {
        assertTrue(StringUtils.isBlank(""));
    }

    @Test
    void isBlank_shouldReturnTrueForWhitespaceString() {
        assertTrue(StringUtils.isBlank("   "));
    }
}
```
Điều này khó bảo trì. Nếu logic thay đổi, bạn phải cập nhật nhiều bài test.

**Cách "Tốt" (Với Kiểm thử Tham số hóa):**

Một bài test tham số hóa cho phép bạn chạy cùng một phương thức test nhiều lần với các đối số khác nhau.

1.  **Thay thế `@Test` bằng `@ParameterizedTest`.** Annotation này cho JUnit biết rằng phương thức sẽ nhận các đối số.
2.  **Cung cấp một nguồn đối số.** Bạn sử dụng một annotation khác để chỉ định các giá trị đến từ đâu.

Hãy xem xét các nguồn đối số phổ biến nhất.

#### **`@ValueSource`**

Đây là nguồn đơn giản nhất. Nó cho phép bạn cung cấp một mảng các giá trị chữ (shorts, bytes, ints, longs, floats, doubles, chars, booleans, strings, hoặc classes).

Hãy tái cấu trúc ví dụ trước bằng cách sử dụng `@ValueSource`.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.ValueSource;
import static org.junit.jupiter.api.Assertions.assertTrue;

class StringUtilsTest {

    @ParameterizedTest
    @ValueSource(strings = {"", "  ", "\t", "\n"}) // Cung cấp một đối số cho mỗi lần gọi
    void isBlank_shouldReturnTrueForEmptyOrWhitespaceStrings(String input) {
        assertTrue(StringUtils.isBlank(input));
    }
}
```

**Cách hoạt động:**
*   JUnit sẽ chạy phương thức `isBlank_shouldReturnTrueFor...` bốn lần.
*   Trong lần chạy đầu tiên, `input` sẽ là `""`.
*   Trong lần chạy thứ hai, `input` sẽ là `"  "`.
*   Và cứ thế.
*   Trình chạy test của IDE sẽ hiển thị chúng dưới dạng các lần chạy test riêng lẻ, giúp dễ dàng xem đầu vào cụ thể nào đã thất bại.

`@ValueSource` rất tuyệt, nhưng nó chỉ có thể cung cấp một đối số mỗi lần. Điều gì sẽ xảy ra nếu bạn cần cung cấp nhiều đối số, như một đầu vào và một *đầu ra mong đợi*?

#### **`@CsvSource`**

`@CsvSource` cho phép bạn chỉ định các đầu vào test dưới dạng các giá trị được phân tách bằng dấu phẩy (CSV). Mỗi chuỗi trong mảng đại diện cho một lần gọi test, với các giá trị được ánh xạ vào các tham số của phương thức.

Hãy kiểm thử phương thức `Calculator.add` của chúng ta.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;
import static org.junit.jupiter.api.Assertions.assertEquals;

class CalculatorTest {

    @ParameterizedTest(name = "Lần chạy {index}: {0} + {1} = {2}") // Tùy chỉnh tên hiển thị
    @CsvSource({
        "0,    1,   1",
        "1,    2,   3",
        "-5,   5,   0",
        "1000, 2000, 3000"
    })
    void add_shouldReturnCorrectSum_forMultipleInputs(int first, int second, int expectedResult) {
        Calculator calculator = new Calculator();
        int actualResult = calculator.add(first, second);
        assertEquals(expectedResult, actualResult);
    }
}
```

**Cách hoạt động:**
*   Phương thức test bây giờ có ba tham số: `first`, `second`, và `expectedResult`.
*   Trong lần chạy đầu tiên, `first` là `0`, `second` là `1`, và `expectedResult` là `1`.
*   Trong lần chạy thứ hai, `first` là `1`, `second` là `2`, và `expectedResult` là `3`.
*   Thuộc tính `name` trên `@ParameterizedTest` là tùy chọn nhưng rất được khuyến khích. Nó tạo ra một tên mô tả cho mỗi lần chạy trong báo cáo test, giúp việc gỡ lỗi dễ dàng hơn nhiều.

Các bài test tham số hóa là nền tảng của việc viết các unit test ngắn gọn, mạnh mẽ và dễ bảo trì.

### **5. Test Lồng nhau với `@Nested`**

Đôi khi một lớp test duy nhất có thể trở nên rất dài, đặc biệt khi kiểm thử một lớp phức tạp với nhiều phương thức hoặc trạng thái. Có thể khó để biết bài test nào thuộc về tính năng nào.

Annotation `@Nested` trong JUnit 5 cung cấp một giải pháp mạnh mẽ. Nó cho phép bạn nhóm các bài test liên quan vào một lớp bên trong, tạo ra một cấu trúc phân cấp, rõ ràng trong bộ test của bạn. Đây là một cách tuyệt vời để tổ chức các bài test của bạn theo hành vi hoặc phương thức bạn đang kiểm thử.

**Khi nào nên sử dụng `@Nested`:**

*   Khi kiểm thử một lớp có nhiều phương thức public (`createUser`, `deleteUser`, `updateUser`).
*   Khi kiểm thử một phương thức duy nhất dưới các điều kiện hoặc trạng thái khác nhau (ví dụ: kiểm thử một lớp `Stack` `khi mới`, `khi rỗng`, `khi đầy`).

Hãy xem một ví dụ. Hãy tưởng tượng chúng ta đang kiểm thử một `UserService`.

**Cách "Phẳng" (Không có `@Nested`):**

```java
// Khó đọc và điều hướng hơn
class UserServiceTest {

    @Test
    void createUser_shouldSucceed_whenUsernameIsUnique() { /* ... */ }

    @Test
    void createUser_shouldThrowException_whenUsernameAlreadyExists() { /* ... */ }

    @Test
    void deleteUser_shouldSucceed_whenUserExists() { /* ... */ }

    @Test
    void deleteUser_shouldThrowException_whenUserDoesNotExist() { /* ... */ }
}
```
Trong một lớp lớn, danh sách phẳng này trở nên khó hiểu. Không rõ ràng ngay lập tức bài test nào liên quan đến `createUser` và bài nào liên quan đến `deleteUser`.

**Cách "Tổ chức" (Với `@Nested`):**

Chúng ta có thể nhóm các bài test này vào các lớp lồng nhau, mỗi lớp tập trung vào một tính năng cụ thể.

```java
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Nested;
import org.junit.jupiter.api.Test;

@DisplayName("Một User Service")
class UserServiceTest {

    // Thiết lập chung cho tất cả các test lồng nhau có thể đặt ở đây

    @Nested
    @DisplayName("khi tạo một người dùng")
    class CreateUserTests {

        @Test
        @DisplayName("nên thành công với một tên người dùng duy nhất")
        void shouldSucceed_whenUsernameIsUnique() {
            // Logic test cho việc tạo thành công
        }

        @Test
        @DisplayName("nên ném ra một ngoại lệ nếu tên người dùng đã tồn tại")
        void shouldThrowException_whenUsernameAlreadyExists() {
            // Logic test cho tên người dùng trùng lặp
        }
    }

    @Nested
    @DisplayName("khi xóa một người dùng")
    class DeleteUserTests {

        @Test
        @DisplayName("nên thành công nếu người dùng tồn tại")
        void shouldSucceed_whenUserExists() {
            // Logic test cho việc xóa thành công
        }

        @Test
        @DisplayName("nên ném ra một ngoại lệ nếu người dùng không tồn tại")
        void shouldThrowException_whenUserDoesNotExist() {
            // Logic test cho việc xóa một người dùng không tồn tại
        }
    }
}
```

**Các Lợi ích và Quy tắc chính:**

*   **Cấu trúc và Dễ đọc:** Mục đích của bài test trở nên rõ ràng ngay từ cấu trúc lớp. IDE của bạn thường sẽ hiển thị các bài test này ở dạng cây, rất dễ điều hướng.
*   **Thiết lập Chung:** Lớp bên ngoài có thể chứa các phương thức `@BeforeEach` và `@AfterEach` áp dụng cho *tất cả* các test lồng nhau, cung cấp một ngữ cảnh chung. Mỗi lớp `@Nested` cũng có thể có các phương thức `@BeforeEach` và `@AfterEach` riêng để thiết lập cụ thể hơn.
*   **`@DisplayName`:** Annotation này thường được sử dụng với `@Nested` để tạo ra các tên dễ đọc cho các nhóm test trong báo cáo, làm cho chúng trở nên mô tả hơn nữa.
*   **Quy tắc:** Các lớp test lồng nhau phải là **các lớp bên trong không tĩnh (non-static inner classes)**. Điều này cho phép chúng chia sẻ các trường instance của lớp test bên ngoài.

Sử dụng `@Nested` là một dấu hiệu của một bộ test chuyên nghiệp, được tổ chức tốt.

### **6. Các Trường hợp Biên (Edge Cases): Kiểm thử Độ chính xác của Số thực, Ngoại lệ, Timeouts, Nulls và Tính Bất biến**

Các trường hợp biên là những kịch bản phức tạp ở ranh giới của đầu vào hoặc logic của bạn, dễ gây ra lỗi. Hãy làm chủ cách xử lý chúng với JUnit 5.

#### **1. Kiểm thử Độ chính xác của Số thực (Floats và Doubles)**

Đây là một cái bẫy kinh điển. Do cách máy tính lưu trữ số thực dấu phẩy động, các phép tính thường không chính xác.

*   **Vấn đề:** `0.1 + 0.2` không bằng `0.3` trong số học dấu phẩy động nhị phân. Nó là một cái gì đó giống như `0.30000000000000004`.
*   **Cách Sai:** Bài test sau sẽ **thất bại**.
    ```java
    // TEST NÀY SẼ THẤT BẠI!
    @Test
    void testFloatingPointAddition_fails() {
        double result = 0.1 + 0.2;
        assertEquals(0.3, result); // Thất bại vì kết quả không chính xác là 0.3
    }
    ```
*   **Giải pháp:** Sử dụng phiên bản quá tải của `assertEquals` chấp nhận một `delta` (hoặc dung sai). Điều này khẳng định rằng giá trị thực tế nằm trong một phạm vi chấp nhận được của giá trị mong đợi.
    ```java
    @Test
    void testFloatingPointAddition_succeeds() {
        double result = 0.1 + 0.2;
        double expected = 0.3;
        double delta = 0.0001; // Sự khác biệt có thể chấp nhận
        assertEquals(expected, result, delta);
    }
    ```
    **Quy tắc chung:** Không bao giờ sử dụng `assertEquals(expected, actual)` cho các kiểu `double` hoặc `float` mà không có delta.

#### **2. Kiểm thử Ngoại lệ Nâng cao**

Chúng ta đã thấy `assertThrows`, nhưng nếu bạn cần xác minh một cái gì đó *về* ngoại lệ, như thông báo của nó hoặc một mã lỗi tùy chỉnh thì sao?

*   **Mục tiêu:** Đảm bảo không chỉ đúng loại ngoại lệ được ném ra, mà nó còn chứa thông tin chính xác.
*   **Giải pháp:** `assertThrows` trả về một cách tiện lợi ngoại lệ đã được ném ra, cho phép bạn thực hiện các assertion tiếp theo trên nó.
    ```java
    @Test
    void isUsernameValid_shouldThrowException_withCorrectMessage() {
        RegistrationService service = new RegistrationService();

        // 1. Gọi assertThrows và lưu trữ ngoại lệ trả về
        IllegalArgumentException thrownException = assertThrows(
            IllegalArgumentException.class,
            () -> service.isUsernameValid(null) // 2. Đoạn mã nên ném ra ngoại lệ
        );

        // 3. Khẳng định các thuộc tính của ngoại lệ đã bắt được
        assertEquals("Username cannot be null", thrownException.getMessage());
    }
    ```

#### **3. Kiểm thử Timeouts**

Đôi khi bạn cần đảm bảo rằng một phương thức phản hồi trong một khung thời gian nhất định.

*   **`assertTimeout(duration, executable)`:** Nó chạy mã và để nó kết thúc. *Sau khi* nó kết thúc, nó kiểm tra xem thời gian thực thi có vượt quá thời gian chờ hay không. Nếu có, test sẽ thất bại. Điều này hữu ích để kiểm tra các tác vụ chạy lâu mà bạn mong đợi sẽ hoàn thành.
*   **`assertTimeoutPreemptively(duration, executable)`:** Cái này nghiêm ngặt hơn. Nó chạy mã trong một luồng khác. Nếu vượt quá thời gian chờ, nó sẽ dừng việc thực thi và làm thất bại bài test ngay lập tức. Điều này hữu ích để kiểm tra các kịch bản có thể dẫn đến một vòng lặp vô hạn.

    ```java
    import java.time.Duration;

    class TimeoutTest {
        @Test
        void shouldFinishWithinOneSecond() {
            // Test này sẽ qua
            assertTimeout(Duration.ofSeconds(1), () -> {
                // Tác vụ mất 500ms
                Thread.sleep(500);
            });
        }

        @Test
        void shouldFailPreemptively_ifTaskRunsTooLong() {
            // Test này sẽ thất bại sau khoảng 100ms
            assertTimeoutPreemptively(Duration.ofMillis(100), () -> {
                // Một vòng lặp vô hạn hoặc một tác vụ rất dài
                Thread.sleep(1000);
            });
        }
    }
    ```

#### **4. Kiểm thử Nulls**

Như đã thảo luận trong bài học "Kịch bản Tiêu cực", `null` là một nguồn thường xuyên của `NullPointerException`. Bạn nên luôn coi nó như một đầu vào tiềm năng.

*   **Mục tiêu:** Kiểm tra một cách rõ ràng cách phương thức của bạn hoạt động khi nó nhận `null`. Nó có ném ra một ngoại lệ không (thông lệ tốt nhất cho các đối số không hợp lệ)? Nó có trả về một kết quả rỗng không?
*   **Giải pháp:** Kết hợp `assertThrows` hoặc các assertion khác để xác minh hành vi mong đợi.
    ```java
    @Test
    void shouldThrowNullPointerException_whenListIsNull() {
        ListProcessor processor = new ListProcessor();
        assertThrows(NullPointerException.class, () -> {
            processor.process(null); // Truyền vào một danh sách null
        });
    }
    ```

#### **5. Kiểm thử các Đối tượng Bất biến (Immutable Objects)**

Một đối tượng bất biến là một đối tượng mà trạng thái của nó không thể thay đổi sau khi nó được tạo (ví dụ: `String`, `Integer`). Khi bạn thiết kế các lớp bất biến của riêng mình, bạn cần kiểm tra xem chúng có thực sự là bất biến hay không.

*   **Mục tiêu:** Xác minh rằng các phương thức có vẻ như sửa đổi đối tượng thực ra trả về một *instance mới* với các thay đổi, để lại đối tượng ban đầu không bị ảnh hưởng.
*   **Giải pháp:** Tạo một instance, gọi một phương thức "thay đổi", và sau đó khẳng định hai điều:
    1.  Đối tượng mới có trạng thái chính xác.
    2.  Trạng thái của đối tượng ban đầu không thay đổi.

    ```java
    // Giả sử Money là một lớp bất biến
    @Test
    void money_add_shouldReturnNewInstance_andNotModifyOriginal() {
        Money initialAmount = new Money(10, "USD");
        Money amountToAdd = new Money(5, "USD");

        // Gọi phương thức 'thay đổi'
        Money finalAmount = initialAmount.add(amountToAdd);

        // Khẳng định 1: Đối tượng mới là chính xác
        assertEquals(15, finalAmount.getAmount());

        // Khẳng định 2: Đối tượng ban đầu không thay đổi
        assertEquals(10, initialAmount.getAmount());

        // Khẳng định 3: Đối tượng trả về là một instance khác
        assertNotSame(initialAmount, finalAmount);
    }
    ```

### **7. Vòng đời Test và Sắp xếp Thứ tự (`@TestMethodOrder`)**

Theo mặc định, JUnit 5 không đảm bảo thứ tự thực thi của các phương thức `@Test` của bạn. Nó sử dụng một thuật toán nội bộ có tính xác định nhưng không rõ ràng. Đây là một lựa chọn thiết kế có chủ ý để thực thi một thông lệ tốt nhất quan trọng: **các bài test của bạn phải độc lập và khép kín.** Một bài test không bao giờ nên phụ thuộc vào một bài test khác đã chạy trước nó.

Tuy nhiên, có một số kịch bản hiếm hoi (như một số loại kiểm thử tích hợp) mà một thứ tự cụ thể là cần thiết. Đối với những trường hợp này, JUnit 5 cung cấp annotation `@TestMethodOrder`.

Bạn áp dụng annotation này ở cấp độ lớp và cung cấp cho nó một `MethodOrderer` cụ thể.

Hãy khám phá các trình sắp xếp phổ biến nhất.

#### **1. Sắp xếp theo Annotation `@Order`**

Đây là cách rõ ràng và linh hoạt nhất để xác định một thứ tự. Bạn chú thích lớp test của mình bằng `@TestMethodOrder(MethodOrderer.OrderAnnotation.class)` và sau đó thêm annotation `@Order` vào mỗi phương thức test với một giá trị số nguyên.

```java
import org.junit.jupiter.api.MethodOrderer;
import org.junit.jupiter.api.Order;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestMethodOrder;

@TestMethodOrder(MethodOrderer.OrderAnnotation.class)
class OrderedTest {

    @Test
    @Order(1)
    void firstTest() {
        System.out.println("Đang thực thi test đầu tiên.");
    }

    @Test
    @Order(3)
    void thirdTest() {
        System.out.println("Đang thực thi test thứ ba.");
    }

    @Test
    @Order(2)
    void secondTest() {
        System.out.println("Đang thực thi test thứ hai.");
    }
}
```
Khi bạn chạy lớp này, đầu ra sẽ là:
```
Đang thực thi test đầu tiên.
Đang thực thi test thứ hai.
Đang thực thi test thứ ba.
```
Cách tiếp cận này rõ ràng và dễ bảo trì.

#### **2. Sắp xếp theo thứ tự chữ và số (Alphanumerically)**

Trình sắp xếp này sắp xếp các phương thức test theo tên của chúng theo thứ tự chữ và số.

```java
import org.junit.jupiter.api.MethodOrderer;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.TestMethodOrder;

@TestMethodOrder(MethodOrderer.Alphanumeric.class)
class AlphanumericOrderedTest {

    @Test
    void testA_someScenario() {
        // Chạy đầu tiên
    }

    @Test
    void testC_anotherScenario() {
        // Chạy thứ ba
    }

    @Test
    void testB_middleScenario() {
        // Chạy thứ hai
    }
}
```

#### **3. Thứ tự Ngẫu nhiên (Random Order)**

Bạn cũng có thể để JUnit chạy các bài test của mình theo một thứ tự ngẫu nhiên. Đây có thể là một kỹ thuật hữu ích để tìm ra các phụ thuộc ẩn, không chủ ý giữa các bài test. Nếu bộ test của bạn vượt qua khi chạy theo một thứ tự cố định nhưng thất bại khi chạy ngẫu nhiên, đó là một dấu hiệu rõ ràng rằng một bài test đang vô tình dựa vào các tác dụng phụ của một bài test khác.

```java
import org.junit.jupiter.api.MethodOrderer;
import org.junit.jupiter.api.TestMethodOrder;

@TestMethodOrder(MethodOrderer.Random.class)
class RandomOrderedTest {
    // ... các test sẽ chạy theo một thứ tự ngẫu nhiên mới mỗi lần thực thi
}
```

**Một Lời Cảnh báo**

Mặc dù các công cụ này có sẵn, **luôn ưu tiên viết các bài test độc lập.** Việc cần phải sắp xếp thứ tự các unit test của bạn thường là một "dấu hiệu đáng ngờ" (code smell), cho thấy các bài test của bạn không được cô lập đúng cách. Dựa vào thứ tự có thể dẫn đến một bộ test giòn và khó bảo trì. Hãy sử dụng `@TestMethodOrder` một cách hạn chế.

---

### **Tóm tắt Bài 2: JUnit 5**

Bạn đã nắm vững cốt lõi của JUnit 5. Hãy tóm tắt lại những gì bạn đã học:

*   **Các Annotation Cơ bản:** Bạn có thể cấu trúc một lớp test bằng cách sử dụng `@Test`, `@BeforeEach`, `@AfterEach`, `@BeforeAll`, và `@AfterAll` để quản lý vòng đời của test.
*   **Quy ước Đặt tên:** Bạn biết cách viết các tên test mô tả như `shouldReturnX_whenY` đóng vai trò như tài liệu sống.
*   **Assertions:** Bạn có thể xác minh kết quả bằng cách sử dụng một loạt các assertion như `assertEquals`, `assertThrows`, `assertTrue`, và nhóm chúng lại với `assertAll`.
*   **Kiểm thử Tham số hóa:** Bạn có thể viết các bài test sạch sẽ, dựa trên dữ liệu bằng cách sử dụng `@ParameterizedTest` với `@ValueSource` và `@CsvSource` để tránh trùng lặp mã.
*   **Test Lồng nhau:** Bạn có thể tổ chức các lớp test phức tạp thành một cấu trúc phân cấp, dễ đọc với `@Nested`.
*   **Các Trường hợp Biên:** Bạn được trang bị để kiểm tra các kịch bản phức tạp liên quan đến số thực dấu phẩy động, thông báo ngoại lệ, timeouts, và tính bất biến.
*   **Sắp xếp Thứ tự:** Bạn hiểu tầm quan trọng của tính độc lập của test và biết cách kiểm soát thứ tự thực thi với `@TestMethodOrder` khi thực sự cần thiết.

Bây giờ bạn hoàn toàn có khả năng viết các unit test chuyên nghiệp, dễ bảo trì cho mã nguồn không có phụ thuộc bên ngoài.

Nhưng điều gì sẽ xảy ra khi mã của bạn cần nói chuyện với cơ sở dữ liệu, một API bên ngoài, hoặc một lớp khác? Chúng ta không thể sử dụng các phụ thuộc thực đó trong một *unit test* vì điều đó sẽ vi phạm nguyên tắc **cô lập**.

Đây là thời điểm hoàn hảo để giới thiệu chủ đề lớn tiếp theo của chúng ta: **Mockito**, framework mocking cho phép chúng ta giả mạo các phụ thuộc này.