### **Giai đoạn 1, Bài 4: AssertJ (Fluent Assertions)**

Mặc dù JUnit 5 cung cấp một bộ phương thức assertion vững chắc (`assertEquals`, `assertTrue`, v.v.), đôi khi chúng có thể hơi khó sử dụng. Thứ tự của `(expected, actual)` rất dễ bị nhầm lẫn, và việc nối chuỗi nhiều kiểm tra có thể khá vụng về.

AssertJ là một thư viện của bên thứ ba giải quyết vấn đề này bằng một **API lưu loát (fluent API)**. Điều này có nghĩa là bạn có thể nối chuỗi các phương thức assertion lại với nhau để tạo thành các câu lệnh dễ đọc, giống như một câu văn.

**Assertion của JUnit 5:**
`assertEquals("Frodo", user.getName());`
`assertTrue(user.getAge() > 30);`
`assertNotNull(user.getRing());`

**Assertion lưu loát của AssertJ:**
`assertThat(user.getName()).isEqualTo("Frodo");`
`assertThat(user.getAge()).isGreaterThan(30);`
`assertThat(user.getRing()).isNotNull();`

Phiên bản của AssertJ dễ đọc hơn, và tính năng tự động hoàn thành của IDE sẽ hiển thị cho bạn tất cả các assertion có thể thực hiện trên một kiểu đối tượng nhất định, đây là một sự thúc đẩy năng suất rất lớn.

### **1. Cách sử dụng Cơ bản: `assertThat(value).isEqualTo(expected)`**

Điểm khởi đầu cho mọi assertion của AssertJ là phương thức tĩnh `assertThat()`. Bạn truyền giá trị *thực tế* (kết quả từ mã của bạn) vào phương thức này. Nó trả về một đối tượng assertion đặc biệt có các phương thức được thiết kế riêng cho kiểu của giá trị bạn đã truyền vào.

```java
import static org.assertj.core.api.Assertions.assertThat; // Import duy nhất bạn cần!

class AssertJBasicsTest {

    @Test
    void a_simple_assertion() {
        String name = "Frodo";
        int age = 50;

        // Bắt đầu với assertThat() và nối chuỗi assertion của bạn
        assertThat(name).isEqualTo("Frodo");
        assertThat(age).isEqualTo(50);

        // Nó cũng cho thấy rõ sự khác biệt giữa thực tế và mong đợi
        assertThat(name).isNotEqualTo("Sauron");
    }
}
```

### **2. Các Phương thức Phổ biến: Nền tảng của AssertJ**

Hãy cùng khám phá những assertion phổ biến nhất mà bạn sẽ sử dụng hàng ngày.

#### **Assertion cho Chuỗi (String)**
```java
String fellowship = "The Lord of the Rings";
assertThat(fellowship).isNotNull();
assertThat(fellowship).startsWith("The");
assertThat(fellowship).endsWith("Rings");
assertThat(fellowship).contains(" of the ");
assertThat(fellowship).isEqualToIgnoringCase("the lord of the rings");

String emptyString = "";
assertThat(emptyString).isEmpty();
assertThat("  ").isBlank(); // Kiểm tra null, rỗng, hoặc chỉ chứa khoảng trắng
```

#### **Assertion cho Tập hợp (Collections - Lists, Sets)**
```java
List<String> hobbits = List.of("Frodo", "Sam", "Pippin", "Merry");

assertThat(hobbits).isNotNull();
assertThat(hobbits).hasSize(4);
assertThat(hobbits).contains("Sam", "Frodo"); // Theo thứ tự bất kỳ, có thể có các phần tử khác
assertThat(hobbits).containsExactly("Frodo", "Sam", "Pippin", "Merry"); // Các phần tử chính xác, theo đúng thứ tự
assertThat(hobbits).containsExactlyInAnyOrder("Sam", "Merry", "Frodo", "Pippin"); // Các phần tử chính xác, thứ tự bất kỳ
assertThat(hobbits).doesNotContain("Sauron");
```

### **3. Assertion cho Đối tượng: `extracting()` và `usingRecursiveComparison()`**

Đây là lúc AssertJ thực sự tỏa sáng, đặc biệt khi xử lý các tập hợp đối tượng phức tạp.

#### **`extracting()`: Khẳng định trên các Thuộc tính của Đối tượng trong một Tập hợp**

Hãy tưởng tượng bạn có một `List<User>` và bạn muốn xác minh danh sách tên người dùng.

```java
@Test
void testing_a_list_of_users() {
    List<User> users = List.of(
        new User("alex", "alex@example.com"),
        new User("bob", "bob@example.com")
    );

    // Cách cũ sẽ là một vòng lặp. Cách của AssertJ thì sạch sẽ và mạnh mẽ.
    assertThat(users)
        .extracting(User::getUsername) // Trích xuất thuộc tính 'username' từ mỗi user
        .containsExactly("alex", "bob"); // Khẳng định trên danh sách chuỗi kết quả

    // Bạn thậm chí có thể trích xuất nhiều thuộc tính vào một bộ (tuple)
    assertThat(users)
        .extracting(User::getUsername, User::getEmail)
        .containsExactly(
            tuple("alex", "alex@example.com"),
            tuple("bob", "bob@example.com")
        );
}
```

#### **`usingRecursiveComparison()`: So sánh các Đối tượng Phức tạp theo từng Trường**

Nếu bạn muốn khẳng định rằng hai đối tượng `User` bằng nhau, nhưng lớp `User` không có phương thức `equals()` thì sao? Hoặc bạn muốn bỏ qua một trường như `id` được tạo ngẫu nhiên?

`usingRecursiveComparison()` so sánh các đối tượng bằng cách kiểm tra tất cả các trường của chúng một cách đệ quy.

```java
@Test
void comparing_two_user_objects() {
    User user1 = new User(1L, "alex", "alex@example.com");
    User user2 = new User(1L, "alex", "alex@example.com");
    User user3 = new User(2L, "alex", "alex@example.com"); // ID khác

    // Điều này sẽ qua vì tất cả các trường đều giống hệt nhau
    assertThat(user1).usingRecursiveComparison().isEqualTo(user2);

    // Điều này sẽ thất bại vì ID khác nhau. Nhưng nếu chúng ta không quan tâm đến ID thì sao?
    // Chúng ta có thể bảo phép so sánh bỏ qua các trường cụ thể.
    assertThat(user1)
        .usingRecursiveComparison()
        .ignoringFields("id") // Rất hữu ích cho các trường được tạo tự động
        .isEqualTo(user3);
}
```

### **4. Assertion cho Ngoại lệ: `assertThatThrownBy()` và `catchThrowable()`**

AssertJ cung cấp một cách lưu loát để kiểm tra các ngoại lệ, thường dễ đọc hơn so với `assertThrows` của JUnit.

#### **`assertThatThrownBy()`**
Đây là cách trực tiếp nhất. Bạn cung cấp một lambda cho đoạn mã được cho là sẽ ném ra một ngoại lệ.

```java
@Test
void testing_for_an_exception() {
    User user = new User(null, null); // User không hợp lệ

    assertThatThrownBy(() -> {
        // Mã được mong đợi sẽ ném ra ngoại lệ
        validator.validateUser(user);
    })
    .isInstanceOf(IllegalArgumentException.class) // Bạn có thể nối chuỗi các assertion về ngoại lệ
    .hasMessageContaining("Username must not be null");
}
```

#### **`catchThrowable()`**
Cách này hữu ích nếu bạn muốn bắt đối tượng ngoại lệ để thực hiện các assertion phức tạp hơn, nhiều dòng.

```java
@Test
void capturing_an_exception() {
    // Act: Thực thi mã và bắt ngoại lệ được ném ra
    Throwable thrown = catchThrowable(() -> validator.validateUser(null));

    // Assert: Chạy các assertion trên đối tượng throwable đã bắt được
    assertThat(thrown)
        .isInstanceOf(IllegalArgumentException.class)
        .hasMessage("User cannot be null");
}
```

### **5. Các Trường hợp Biên: Khẳng định trên `Optional`, Maps và Đối tượng Phức tạp**

AssertJ có hỗ trợ phong phú cho hầu hết mọi kiểu bạn có thể nghĩ đến.

*   **Giá trị `Optional`:**
    ```java
    Optional<String> maybeName = Optional.of("Gandalf");
    assertThat(maybeName).isPresent();
    assertThat(maybeName).hasValue("Gandalf");

    Optional<String> emptyOptional = Optional.empty();
    assertThat(emptyOptional).isEmpty();
    ```
*   **Maps:**
    ```java
    Map<String, String> translations = Map.of("one", "uno", "two", "dos");
    assertThat(translations).hasSize(2);
    assertThat(translations).containsKey("one");
    assertThat(translations).containsValue("dos");
    assertThat(translations).containsEntry("one", "uno");
    ```

### **Tóm tắt Bài 4: AssertJ**

*   **API lưu loát (Fluent API):** Bạn hiểu cách các phương thức có thể nối chuỗi của AssertJ cải thiện khả năng đọc và bảo trì của test.
*   **Các Assertion Cốt lõi:** Bạn có thể viết các assertion cơ bản cho Chuỗi, Tập hợp và các kiểu phổ biến khác bằng cách sử dụng `assertThat(...)`.
*   **Assertion Đối tượng Nâng cao:** Bạn có thể thực hiện các assertion mạnh mẽ trên các tập hợp đối tượng bằng cách sử dụng `extracting()` và so sánh các đối tượng phức tạp theo từng trường với `usingRecursiveComparison()`.
*   **Kiểm tra Ngoại lệ:** Bạn có thể viết các bài test ngoại lệ sạch sẽ và mô tả với `assertThatThrownBy()`.
*   **Hỗ trợ Kiểu Dữ liệu Phong phú:** Bạn biết rằng AssertJ có các assertion chuyên dụng cho hầu hết mọi kiểu, bao gồm `Optional` và `Map`.

Bây giờ bạn đã thành thạo "Bộ Ba Lớn" của kiểm thử đơn vị trong Java: **JUnit 5**, **Mockito**, và **AssertJ**. Với những công cụ này, bạn có thể viết các bài test sạch sẽ, chuyên nghiệp và dễ bảo trì cho hầu hết mọi tình huống.

