### **Phần 7: Công cụ và Thực hành Tốt nhất**

Biết lý thuyết và các annotation là một nửa trận chiến. Nửa còn lại là sử dụng đúng công cụ cho công việc và tuân theo các quy ước đã được thiết lập để giữ cho bộ kiểm thử của bạn nhanh, dễ đọc và dễ bảo trì. Phần này bao gồm các thực hành tốt nhất giúp phân biệt một chiến lược kiểm thử chuyên nghiệp.

#### **Chủ đề 7.1: So sánh MockMvc, TestRestTemplate, và RestAssured**

Chúng ta đã sử dụng ba công cụ này, nhưng bây giờ hãy đặt chúng cạnh nhau để làm rõ sự lựa chọn.

| Đặc điểm | MockMvc | TestRestTemplate | RestAssured |
| :--- | :--- | :--- | :--- |
| **Loại Kiểm thử** | Kiểm thử Lát cắt (`@WebMvcTest`) | Kiểm thử E2E Đầy đủ (`@SpringBootTest`) | Kiểm thử E2E Đầy đủ (`@SpringBootTest`) |
| **Máy chủ** | Servlet Giả lập (Không có máy chủ thật) | Máy chủ Nhúng Thật (Tomcat) | Máy chủ Nhúng Thật (Tomcat) |
| **Cuộc gọi Mạng** | Không (Phân phối nội bộ) | Có (Cuộc gọi HTTP trên `localhost`) | Có (Cuộc gọi HTTP trên `localhost`) |
| **Tốc độ** | 🚀 **Nhanh nhất** | 🐢 Chậm hơn | 🐢 Chậm hơn |
| **Mục tiêu Chính** | Kiểm thử logic controller một cách cô lập | Kiểm thử toàn bộ ngăn xếp từ đầu đến cuối | Kiểm thử toàn bộ ngăn xếp từ đầu đến cuối |
| **Cú pháp** | API Java Fluent | Các phương thức Java tiêu chuẩn | Phong cách BDD (`given/when/then`) |
| **Tốt nhất cho** | Validation, xử lý exception, ánh xạ request, tuần tự hóa JSON. | Các bài kiểm thử E2E cơ bản, các kịch bản bạn cần kiểm soát client HTTP trực tiếp. | Các bài kiểm thử E2E dễ đọc, xác thực JSON/XML phức tạp. |
| **Context Điển hình**| `@WebMvcTest` | `@SpringBootTest(webEnvironment=RANDOM_PORT)` | `@SpringBootTest(webEnvironment=RANDOM_PORT)` |

**Khi nào nên Sử dụng Mỗi công cụ: Cây Quyết định**

1.  **Bạn đang kiểm thử logic *bên trong* một controller?** (ví dụ: "Nó có gọi đúng phương thức service không?", "Nó có trả về 400 với đầu vào không hợp lệ không?")
    *   **Có** -> Sử dụng **`MockMvc`** với `@WebMvcTest`. Đó là công cụ nhanh nhất và tập trung nhất cho công việc này.

2.  **Bạn đang kiểm thử toàn bộ luồng ứng dụng, từ một request HTTP cho đến cơ sở dữ liệu?**
    *   **Có** -> Bạn cần một bài kiểm thử E2E đầy đủ với `@SpringBootTest(webEnvironment=RANDOM_PORT)`. Bây giờ hãy chọn client của bạn:
        *   Bạn thích một client đơn giản, tiêu chuẩn được tích hợp với Spring và các khẳng định Java thông thường? -> Sử dụng **`TestRestTemplate`**.
        *   Bạn ưu tiên khả năng đọc của bài kiểm thử và muốn có một cú pháp phong phú, theo phong cách BDD với các khẳng định JSON tích hợp mạnh mẽ? -> Sử dụng **`RestAssured`**.

**Khuyến nghị Chuyên nghiệp:** Sử dụng cả `MockMvc` và `RestAssured` trong dự án của bạn. Sử dụng `MockMvc` để kiểm thử toàn diện tất cả các trường hợp biên của controller. Sử dụng `RestAssured` cho một tập hợp nhỏ hơn các bài kiểm thử E2E "happy path" quan trọng để xác minh các hành trình người dùng quan trọng nhất của bạn hoạt động từ đầu đến cuối.

---

#### **Chủ đề 7.2: Kết hợp Hiệu quả JUnit 5, Mockito, và Spring Boot**

Ba thư viện này tạo thành "bộ ba thần thánh" của việc kiểm thử trong hệ sinh thái Spring. Điều quan trọng là phải hiểu vai trò riêng biệt của chúng.

*   **JUnit 5 (Trình chạy):** Đây là chính framework kiểm thử. Nó cung cấp các annotation cốt lõi (`@Test`, `@BeforeEach`, `@Disabled`), thư viện khẳng định (`org.junit.jupiter.api.Assertions`), và mô hình mở rộng cho phép các framework khác tích hợp vào. Khi bạn chạy các bài kiểm thử, bạn đang chạy chúng với JUnit.

*   **Mockito (Thư viện Mocking):** Đây là thư viện để tạo ra các "đối tượng đóng thế" hoặc mock. Công việc của nó là tạo ra các implementation giả của các phụ thuộc của bạn để bạn có thể kiểm thử một đơn vị duy nhất một cách cô lập. Các annotation chính là `@Mock`, `@InjectMocks`, và các phương thức cốt lõi là `when()`/`given()`, `verify()`. Spring Boot cung cấp annotation `@MockBean` để tích hợp liền mạch các mock Mockito vào `ApplicationContext` của Spring.

*   **Spring Boot Test (Lớp Tích hợp):** Đây là chất keo kết dính mọi thứ lại với nhau cho việc kiểm thử tích hợp. Nó cung cấp các annotation (`@SpringBootTest`, `@WebMvcTest`, v.v.) để tải `ApplicationContext`. Nó sử dụng một extension của JUnit 5 (`SpringExtension`) để quản lý vòng đời của context. Nó cung cấp các tiện ích kiểm thử như `MockMvc` và `TestRestTemplate`.

**Cách chúng hoạt động cùng nhau trong một `@WebMvcTest`:**
1.  **JUnit 5** thấy annotation `@Test` và quyết định chạy phương thức của bạn.
2.  Nó thấy `@ExtendWith(SpringExtension.class)` (được meta-annotated trên `@WebMvcTest`) và giao quyền kiểm soát cho Spring.
3.  **Spring Boot Test** tải `ApplicationContext` lát cắt web tối thiểu.
4.  Nó thấy annotation `@MockBean` và sử dụng **Mockito** để tạo một mock của `ProductService` của bạn.
5.  Nó tiêm mock này vào bean `ProductController` bên trong context.
6.  Phương thức kiểm thử chạy. Bạn sử dụng `given()` của **Mockito** để thiết lập hành vi của mock.
7.  Bạn sử dụng `MockMvc` của **Spring** để thực hiện một request.
8.  Bạn sử dụng `MockMvcResultMatchers` (thường sử dụng Hamcrest hoặc AssertJ bên trong) hoặc `Assertions` của **JUnit 5** để kiểm tra kết quả.

---

#### **Chủ đề 7.3: Tổ chức các Bài kiểm thử trong Cấu trúc Dự án**

Sự tách biệt rõ ràng giữa các bài kiểm thử đơn vị và tích hợp là rất quan trọng cho một dự án có khả năng mở rộng. Bố cục thư mục tiêu chuẩn của Maven/Gradle là: `src/test/java`.

Thực hành tốt nhất là sao chép cấu trúc gói chính của bạn và thêm các gói con cho các loại kiểm thử.

```
src
├── main
│   └── java
│       └── com
│           └── myapp
│               ├── controller
│               ├── service
│               └── repository
└── test
    └── java
        └── com
            └── myapp
                ├── unit
                │   ├── controller
                │   │   └── ProductControllerUnitTest.java  // JUnit/Mockito thuần túy
                │   └── service
                │       └── ProductServiceUnitTest.java      // JUnit/Mockito thuần túy
                └── integration
                    ├── controller
                    │   └── ProductControllerWebMvcTest.java // @WebMvcTest
                    └── repository
                        └── ProductRepositoryDataJpaTest.java // @DataJpaTest
                        └── ProductControllerE2ETest.java     // @SpringBootTest
```

**Lợi ích của cấu trúc này:**
*   **Rõ ràng:** Ngay lập tức rõ ràng bạn đang xem loại kiểm thử nào.
*   **Cấu hình CI/CD:** Nó cho phép bạn cấu hình quy trình xây dựng của mình để chạy các bài kiểm thử theo từng giai đoạn. Ví dụ:
    1.  **Trên mỗi commit vào một nhánh tính năng:** Chỉ chạy các bài kiểm thử đơn vị nhanh (`/unit/**`).
    2.  **Trên một pull request vào nhánh chính:** Chạy tất cả các bài kiểm thử đơn vị *và* tất cả các bài kiểm thử tích hợp (`/integration/**`).

---

#### **Chủ đề 7.4: Tái sử dụng Testcontainers qua các Bài kiểm thử (Container Singleton)**

Đây là một thực hành tốt nhất về hiệu suất quan trọng đáng để nhắc lại. Khởi động một Docker container là phần tốn kém nhất của một bài kiểm thử dựa trên Testcontainers. **Bạn phải tránh khởi động một container mới cho mỗi lớp kiểm thử.**

Giải pháp là **Mô hình Singleton**, đạt được bằng cách khai báo container là một trường `static`.

```java
// Trong một lớp cơ sở được chia sẻ như AbstractIntegrationTest
@Container
private static final PostgreSQLContainer<?> postgresqlContainer =
        new PostgreSQLContainer<>("postgres:14.5");
```

Khi JUnit chạy lớp kiểm thử đầu tiên sử dụng lớp cơ sở này, Testcontainers sẽ khởi động container. Khi các lớp kiểm thử tiếp theo được chạy, Testcontainers thấy rằng container là `static` và đã đang chạy, vì vậy nó chỉ đơn giản là tái sử dụng kết nối. Container chỉ được dừng lại một lần khi toàn bộ tiến trình JVM (lần chạy bộ kiểm thử) hoàn tất.

---

#### **Chủ đề 7.5: Tích hợp Kiểm thử với CI/CD (GitHub Actions, Jenkins)**

Các bài kiểm thử của bạn cung cấp giá trị lớn nhất khi chúng được chạy tự động trong một quy trình Tích hợp Liên tục / Phân phối Liên tục (CI/CD). Điều này hoạt động như một cổng chất lượng, ngăn chặn lỗi đến môi trường production.

Đây là một quy trình làm việc mẫu cho **GitHub Actions** chạy cả các bài kiểm thử đơn vị và tích hợp.

```yaml
# .github/workflows/ci.yml
name: Spring Boot CI

on:
  push:
    branches: [ "main" ]
  pull_request:
    branches: [ "main" ]

jobs:
  build:
    runs-on: ubuntu-latest

    services:
      # Điều này khởi động một Docker container cho PostgreSQL có thể được truy cập
      # bởi thư viện testcontainers từ bên trong trình chạy job chính.
      # Điều này thường nhanh hơn việc testcontainers tự tải image về.
      postgres:
        image: postgres:14.5
        env:
          POSTGRES_USER: test
          POSTGRES_PASSWORD: test
          POSTGRES_DB: test
        ports:
          - 5432:5432

    steps:
    - name: Checkout code
      uses: actions/checkout@v4

    - name: Set up JDK 17
      uses: actions/setup-java@v4
      with:
        java-version: '17'
        distribution: 'temurin'
        cache: 'maven'

    - name: Run Unit Tests
      # Lệnh này bảo Maven chỉ chạy các bài kiểm thử trong gói 'unit'
      run: mvn -B test -Dtest="com/myapp/unit/**"

    - name: Run Integration Tests
      # Lệnh này chạy các bài kiểm thử còn lại (hoặc bạn có thể cụ thể hơn)
      # Các biến môi trường là cần thiết nếu các bài kiểm thử của bạn mong đợi chúng.
      run: mvn -B verify -Dtest="com/myapp/integration/**"
      env:
          SPRING_DATASOURCE_URL: jdbc:postgresql://localhost:5432/test
          SPRING_DATASOURCE_USERNAME: test
          SPRING_DATASOURCE_PASSWORD: test
```

---

#### **Chủ đề 7.6: Quy ước Đặt tên Kiểm thử và Kiểm thử Tham số hóa**

**Quy ước Đặt tên**
Tên kiểm thử dễ đọc là rất quan trọng. Chúng hoạt động như tài liệu sống cho code của bạn. Một quy ước phổ biến là `tenPhuongThuc_should_hanhViMongDoi_when_trangThaiDuocKiemThu`.

*   `findById_should_returnProduct_when_productExists` (findById_nên_trảVềSảnPhẩm_khi_sảnPhẩmTồnTại)
*   `createProduct_should_throwValidationException_when_nameIsBlank` (createProduct_nên_némNgoạiLệXácThực_khi_tênTrống)

**Kiểm thử Tham số hóa với JUnit 5**

Khi bạn cần kiểm thử cùng một logic với nhiều đầu vào khác nhau, hãy tránh sao chép-dán các bài kiểm thử. Sử dụng `@ParameterizedTest`.

```java
import org.junit.jupiter.params.ParameterizedTest;
import org.junit.jupiter.params.provider.CsvSource;

@ParameterizedTest
@CsvSource({
    "100.0, 10, 90.0",   // giá, giảm giá, kết quả mong đợi
    "50.0,  50, 25.0",
    "20.0,  100, 0.0"
})
void calculateDiscount_should_returnCorrectPrice(double price, double discount, double expected) {
    // Act
    double result = priceCalculator.calculate(price, discount);
    // Assert
    assertThat(result).isEqualTo(expected);
}
```

---

#### **Chủ đề 7.7: Đo lường Độ bao phủ Code với Jacoco**

Độ bao phủ code cho bạn biết tỷ lệ phần trăm code của bạn được thực thi bởi các bài kiểm thử. Đó là một số liệu hữu ích để xác định các phần chưa được kiểm thử của ứng dụng, nhưng nó **không phải là thước đo chất lượng kiểm thử**. Độ bao phủ 100% không có nghĩa là code của bạn không có lỗi.

**Cài đặt với Maven:**
Thêm plugin Jacoco vào `pom.xml` của bạn. Điều quan trọng là cấu hình nó để tạo một báo cáo bao gồm cả các bài kiểm thử đơn vị và tích hợp.

```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <version>0.8.12</version>
    <executions>
        <execution>
            <id>prepare-agent</id>
            <goals>
                <goal>prepare-agent</goal>
            </goals>
        </execution>
        <execution>
            <id>report</id>
            <phase>verify</phase>
            <goals>
                <goal>report</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

Khi bạn chạy `mvn clean verify`, Jacoco sẽ:
1.  Gắn một Java agent vào JVM trong giai đoạn thực thi kiểm thử.
2.  Agent này theo dõi những dòng code nào của bạn được thực thi bởi cả các bài kiểm thử đơn vị và tích hợp.
3.  Trong giai đoạn `verify`, nó tạo ra một báo cáo HTML trong `target/site/jacoco/index.html` mà bạn có thể mở trong trình duyệt để khám phá độ bao phủ code của mình.

***

Tiếp theo, chúng ta sẽ đề cập đến một tập hợp các **Trường hợp Biên và Kịch bản Nâng cao**, chẳng hạn như kiểm thử code bất đồng bộ, caching, và bảo mật.