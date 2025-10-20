### **Phần 6: Profile và Cấu hình Kiểm thử**

Quản lý cấu hình đúng cách là điều phân biệt một bộ kiểm thử mong manh, khó bảo trì với một bộ kiểm thử mạnh mẽ và linh hoạt. Trong phần này, chúng ta sẽ nắm vững các công cụ mà Spring Boot cung cấp để kiểm soát môi trường mà các bài kiểm thử của bạn chạy.

#### **Chủ đề 6.1: Cách Tạo và Kích hoạt các Profile Riêng biệt cho Kiểm thử**

Chúng ta đã đề cập đến điều này, nhưng hãy chính thức hóa khái niệm này. Một **profile** là một tập hợp các thuộc tính cấu hình được đặt tên. Theo mặc định, ứng dụng của bạn chạy mà không có profile nào hoạt động, tải các thuộc tính từ `application.properties` hoặc `application.yml`.

**Tại sao phải tạo một profile "test"?**

*   **Cô lập:** Để đảm bảo cấu hình kiểm thử của bạn (như kết nối đến cơ sở dữ liệu kiểm thử) không bao giờ vô tình rò rỉ vào bản dựng production của bạn.
*   **Kiểm soát:** Để bật hoặc tắt các tính năng cụ thể cho môi trường kiểm thử. Ví dụ, bạn có thể tắt một dịch vụ gửi email hoặc một kết nối đến cổng thanh toán trực tiếp trong quá trình kiểm thử.
*   **Hiệu suất:** Để sử dụng các cài đặt được tối ưu hóa cho việc kiểm thử, như sử dụng một bộ mã hóa mật khẩu nhanh hơn hoặc tắt các tác vụ khởi động tốn kém.

**Cách Thực hiện:**

1.  **Tạo Tệp Dành riêng cho Profile:** Trong `src/test/resources/`, tạo một tệp có tên `application-{tênProfile}.yml` (hoặc `.properties`). Quy ước tiêu chuẩn là `application-test.yml`.
2.  **Kích hoạt Profile trong Bài kiểm thử của bạn:** Sử dụng annotation `@ActiveProfiles("test")` trên lớp kiểm thử của bạn.

```java
@SpringBootTest
@ActiveProfiles("test") // Annotation này là chìa khóa
public class MyServiceIntegrationTest {
    // ...
}
```

Khi bài kiểm thử này chạy, Spring sẽ tải các thuộc tính từ `application.yml` trước, và sau đó tải các thuộc tính từ `application-test.yml`, với các thuộc tính dành riêng cho kiểm thử sẽ ghi đè lên các thuộc tính mặc định.

---

#### **Chủ đề 6.2: Ghi đè Thuộc tính và Sử dụng `@TestPropertySource`**

Trong khi `@ActiveProfiles` rất tuyệt vời cho một cấu hình được chia sẻ trên nhiều bài kiểm thử, đôi khi một lớp kiểm thử duy nhất hoặc thậm chí một phương thức kiểm thử duy nhất cần một thuộc tính ghi đè riêng. Đây là công việc của `@TestPropertySource`.

`@TestPropertySource` là một annotation cho phép bạn thêm hoặc ghi đè các thuộc tính cho một bài kiểm thử cụ thể. **Các thuộc tính được định nghĩa ở đây có độ ưu tiên cao hơn các thuộc tính trong các tệp dành riêng cho profile.**

**Hai cách để sử dụng nó:**

**1. Thuộc tính Nội tuyến (thuộc tính `properties`)**

Cách này hoàn hảo để ghi đè một hoặc hai giá trị đơn giản.

```java
@SpringBootTest
@TestPropertySource(properties = {
    "feature-flags.new-algorithm.enabled=true",
    "some.api.timeout=500"
})
class NewAlgorithmTest {
    // Trong bài kiểm thử này, tính năng thuật toán mới được bật,
    // bất kể có gì trong application.yml hay application-test.yml
}
```

**2. Tệp Thuộc tính Bên ngoài (thuộc tính `locations`)**

Cách này hữu ích để nhóm một tập hợp các ghi đè liên quan vào một tệp chuyên dụng. Đường dẫn được giải quyết từ classpath.

```java
// src/test/resources/test-configs/disable-caching.properties
// caching.enabled=false

@SpringBootTest
@TestPropertySource(locations = "/test-configs/disable-caching.properties")
class NoCacheTest {
    // Trong bài kiểm thử này, việc lưu vào bộ đệm sẽ bị tắt.
}
```

**Thứ tự Ưu tiên của Thuộc tính (Từ cao nhất đến thấp nhất):**

1.  `@DynamicPropertySource` (cho Testcontainers - ưu tiên cao nhất)
2.  `@TestPropertySource` (thuộc tính `properties` nội tuyến)
3.  `@TestPropertySource` (thuộc tính `locations`)
4.  Thuộc tính dành riêng cho profile (`application-test.yml`)
5.  Thuộc tính mặc định (`application.yml`)

---

#### **Chủ đề 6.3: Quản lý các Cấu hình Dành riêng cho Môi trường**

Hãy tóm tắt các chiến lược để xử lý các cấu hình động như URL cơ sở dữ liệu.

| Chiến lược | Khi nào Sử dụng | Ví dụ |
| :--- | :--- | :--- |
| **Tĩnh (`application-test.yml`)** | Đối với các thuộc tính đã biết và cố định cho toàn bộ bộ kiểm thử. | `logging.level.com.myapp=DEBUG` `server.port=0` |
| **Động (`@DynamicPropertySource`)** | Đối với các thuộc tính có giá trị chưa biết cho đến khi chạy, thường được cung cấp bởi một Testcontainer. | `registry.add("spring.datasource.url", container::getJdbcUrl);` |
| **Theo từng Bài kiểm thử (`@TestPropertySource`)** | Để ghi đè một cài đặt kiểm thử toàn cục cho một kịch bản kiểm thử rất cụ thể. | `properties="caching.enabled=false"` |

Sự kết hợp của một `application-test.yml` cơ bản và các ghi đè động cho các dịch vụ bên ngoài như cơ sở dữ liệu cung cấp một thiết lập mạnh mẽ và dễ bảo trì nhất.

---

#### **Chủ đề 6.4: Cấu hình Logging và các Bean có Điều kiện**

**Logging trong các Bài kiểm thử**

Việc gỡ lỗi các bài kiểm thử tích hợp thất bại có thể khó khăn. Có nhật ký (log) chi tiết là điều cần thiết. Bạn có thể đặt các cấp độ logging cụ thể trong `application-test.yml` của mình mà sẽ chỉ áp dụng khi profile `test` đang hoạt động.

```yaml
# src/test/resources/application-test.yml
logging:
  level:
    # Đặt gói cơ sở của ứng dụng của bạn thành DEBUG
    com.example.myapp: DEBUG
    # Xem các truy vấn SQL chính xác mà Hibernate đang chạy
    org.hibernate.SQL: DEBUG
    # Xem các giá trị đang được ràng buộc vào các câu lệnh đã chuẩn bị
    org.hibernate.type.descriptor.sql: TRACE
    # Xem các transaction nào đang được tạo và rollback
    org.springframework.transaction.interceptor: TRACE
```

**Các Bean có Điều kiện trong Kiểm thử (`@Profile`)**

Đây là một phương án thay thế nâng cao nhưng sạch sẽ cho việc sử dụng `@MockBean`. Hãy tưởng tượng bạn có một dịch vụ tương tác với một bucket S3 thực, bên ngoài để lưu trữ tệp. Việc gọi dịch vụ này trong các bài kiểm thử sẽ chậm, tốn kém và không ổn định.

Thay vào đó, bạn có thể tạo một implementation "giả" hoặc trong bộ nhớ của interface của dịch vụ đó mà chỉ hoạt động trong quá trình kiểm thử.

**1. Interface**
```java
public interface FileStorageService {
    void saveFile(String name, byte[] content);
    byte[] loadFile(String name);
}
```

**2. Implementation "Thật"**
```java
@Service
@Profile("!test") // Hoạt động khi profile KHÔNG phải là "test"
public class S3FileStorageService implements FileStorageService {
// ... logic client S3 thật ...
}
```

**3. Implementation "Giả" cho Kiểm thử**

```java
@Service
@Profile("test") // CHỈ hoạt động khi profile là "test"
public class InMemoryFileStorageService implements FileStorageService {
    private final Map<String, byte[]> storage = new ConcurrentHashMap<>();

    @Override
    public void saveFile(String name, byte[] content) {
        storage.put(name, content);
    }
    // ... implement loadFile và có thể là một phương thức clear() cho các bài kiểm thử
}
```

**Kết quả:**

Khi bạn chạy ứng dụng của mình bình thường, `S3FileStorageService` sẽ được tải. Nhưng khi bạn chạy bất kỳ bài kiểm thử nào được chú thích bằng `@ActiveProfiles("test")`, quá trình quét component của Spring sẽ thấy các annotation `@Profile` và sẽ tạo và tiêm `InMemoryFileStorageService` thay thế.

Điều này cực kỳ mạnh mẽ vì:
*   Bạn không cần phải thêm `@MockBean` vào mọi lớp kiểm thử sử dụng `FileStorageService`.
*   Implementation kiểm thử của bạn có thể có hành vi thực tế, không giống như một mock đơn giản.
*   Nó giữ cho logic kiểm thử của bạn sạch sẽ và tập trung vào sự tích hợp, chứ không phải vào việc mock cơ sở hạ tầng.

---

### **Các câu hỏi phỏng vấn**

1.  **Câu hỏi:** "Thứ tự ưu tiên của các thuộc tính được định nghĩa trong `@TestPropertySource`, `application-test.yml`, và `application.yml` là gì?"
    *   **Trả lời:** "Các thuộc tính từ `@TestPropertySource` có độ ưu tiên cao nhất và sẽ ghi đè các thuộc tính từ tất cả các nguồn khác. Tiếp theo là các thuộc tính dành riêng cho profile từ `application-test.yml`, chúng sẽ ghi đè các thuộc tính mặc định có trong `application.yml`."

2.  **Câu hỏi:** "Khi nào bạn sẽ chọn sử dụng `@Profile` trên một bean so với việc sử dụng `@MockBean` trong một bài kiểm thử?"
    *   **Trả lời:** "Tôi sẽ sử dụng `@Profile` để tạo một implementation dành riêng cho kiểm thử của một bean khi bean đó đại diện cho một thành phần cơ sở hạ tầng bên ngoài lớn, như dịch vụ lưu trữ tệp, client hàng đợi tin nhắn, hoặc cổng thanh toán. Điều này lý tưởng khi tôi muốn một 'bản giả' hoạt động đầy đủ có thể được sử dụng trên toàn bộ bộ kiểm thử. Tôi sẽ sử dụng `@MockBean` để kiểm soát chi tiết hơn trong một lớp kiểm thử duy nhất, thường là để mock một phụ thuộc của thành phần tôi đang kiểm thử (như mock một repository trong một bài kiểm thử tầng web) để tôi có thể xác minh các tương tác cụ thể và định nghĩa hành vi trên cơ sở từng bài kiểm thử."

3.  **Câu hỏi:** "Bạn có một `@SpringBootTest` đang bị lỗi. Điều đầu tiên bạn sẽ cấu hình trong `application-test.yml` của mình để giúp chẩn đoán vấn đề là gì?"
    *   **Trả lời:** "Điều đầu tiên tôi sẽ làm là tăng độ chi tiết của log. Tôi sẽ đặt cấp độ logging cho gói gốc của ứng dụng của mình thành `DEBUG` và, nếu đó là một vấn đề liên quan đến cơ sở dữ liệu, tôi sẽ đặt `org.hibernate.SQL` thành `DEBUG` và `org.hibernate.type.descriptor.sql` thành `TRACE`. Điều này cho phép tôi xem các truy vấn SQL chính xác đang được tạo ra và các tham số đang được ràng buộc, đây thường là chìa khóa để giải quyết các lỗi kiểm thử tích hợp liên quan đến persistence."

---

### **Bài tập thực hành**

1.  **Mục tiêu:** Sử dụng các profile và các nguồn thuộc tính để kiểm soát hành vi của một bean.
2.  **Cài đặt:**
    *   Tạo một `@Component` đơn giản có tên là `GreetingService`.
    *   Cho nó một phương thức `public String getGreeting()` trả về "Hello, World!".
    *   Tiêm dịch vụ này vào một controller và tạo một endpoint `/greeting` trả về chuỗi đó.
3.  **Nhiệm vụ:**
    *   Trong `application.yml` chính của bạn, thêm một thuộc tính: `greeting.message: "Hello from application.yml"`. Sửa đổi `GreetingService` để đọc thuộc tính này và trả về nó.
    *   Tạo một `application-test.yml` và đặt `greeting.message: "Hello from the test profile"`.
    *   Viết một `@SpringBootTest` với `@ActiveProfiles("test")` và khẳng định rằng endpoint `/greeting` trả về "Hello from the test profile".
    *   Trong *cùng một lớp kiểm thử*, viết một phương thức kiểm thử thứ hai. Chú thích **chỉ phương thức này** bằng `@TestPropertySource(properties = "greeting.message=Hello from a single test!")`. Khẳng định rằng endpoint bây giờ trả về thông điệp mới này cho bài kiểm thử này, nhưng bài kiểm thử đầu tiên vẫn thành công với thông điệp của riêng nó.

Bài tập này sẽ cung cấp cho bạn kinh nghiệm thực tế với các quy tắc ưu tiên cấu hình và chứng minh sức mạnh của các thuộc tính dành riêng cho profile và dành riêng cho từng bài kiểm thử.

***

Tiếp theo, chúng ta sẽ đề cập đến các công cụ của ngành trong phần **Công cụ và Thực hành Tốt nhất**, so sánh các client kiểm thử và thảo luận về cách tổ chức và chạy các bài kiểm thử của bạn một cách hiệu quả.