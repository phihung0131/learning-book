### **Giai đoạn 1, Bài 5: Cấu trúc & Thiết lập Dự án Test**

Chúng ta sẽ tập trung vào cấu trúc được sử dụng bởi **Maven**, công cụ xây dựng phổ biến nhất trong hệ sinh thái Java. Gradle và các công cụ khác sử dụng một bố cục rất tương tự.

### **1. Cấu trúc Dự án Maven cho việc Kiểm thử**

Maven áp dụng phương pháp "quy ước hơn cấu hình". Điều này có nghĩa là nó có một bố cục thư mục tiêu chuẩn hoạt động ngay mà không cần nhiều thiết lập.

Một dự án Maven điển hình trông như thế này:

```
my-awesome-project/
├── pom.xml                   <-- Trái tim của dự án Maven: cấu hình, các phụ thuộc.
└── src/
    ├── main/
    │   ├── java/             <-- MÃ NGUỒN CHÍNH CỦA BẠN NẰM Ở ĐÂY.
    │   │   └── com/
    │   │       └── mycompany/
    │   │           └── app/
    │   │               └── service/
    │   │                   └── UserService.java
    │   └── resources/        <-- Các tài nguyên production (tệp cấu hình, hình ảnh, v.v.).
    │
    └── test/
        ├── java/             <-- MÃ TEST CỦA BẠN NẰM Ở ĐÂY.
        │   └── com/
        │       └── mycompany/
        │           └── app/
        │               └── service/
        │                   └── UserServiceTest.java
        └── resources/        <-- Các tài nguyên chỉ dành cho test (dữ liệu test, tệp JSON giả, v.v.).
```

### **2. Cách Tách biệt Nguồn Test: `src/main/java` vs. `src/test/java`**

Sự tách biệt này là khái niệm cơ bản nhất của việc tổ chức dự án.

*   **`src/main/java`**: Chứa tất cả mã nguồn chính của ứng dụng. Đây là mã được biên dịch, đóng gói (thành tệp JAR hoặc WAR), và giao cho người dùng.
*   **`src/test/java`**: Chứa tất cả mã test của bạn.

**Tại sao sự tách biệt này lại quan trọng đến vậy?**

1.  **Giữ Mã Test khỏi Môi trường Production:** Mã trong `src/test/java` chỉ được sử dụng trong giai đoạn test của quá trình build. Nó **không bao giờ** được bao gồm trong sản phẩm ứng dụng cuối cùng. Điều này có nghĩa là tệp JAR production của bạn không bị phình to bởi hàng ngàn lớp test và thư viện kiểm thử.
2.  **Các Phụ thuộc Khác nhau:** Bạn thường cần các thư viện cho việc kiểm thử (như JUnit, Mockito, AssertJ) mà bạn không cần trong ứng dụng production. Sự tách biệt này cho phép bạn quản lý các phụ thuộc "phạm vi test" này một cách sạch sẽ.
3.  **Sự rõ ràng:** Nó tạo ra một ranh giới rõ ràng. Bất kỳ ai nhìn vào dự án đều biết chính xác nơi để tìm mã ứng dụng và nơi để tìm các bài test cho nó.
4.  **Cấu trúc Gói Phản chiếu:** Cấu trúc gói bên trong `src/test/java` nên **phản chiếu** cấu trúc trong `src/main/java`. Nếu lớp production của bạn nằm trong `com.mycompany.app.service`, bài test của nó cũng nên nằm trong cùng một gói đó bên trong thư mục nguồn test. Điều này giúp việc tìm kiếm bài test cho bất kỳ lớp nào trở nên vô cùng dễ dàng.

### **3. Quy ước Đặt tên cho các Lớp Test**

Như chúng ta đã thảo luận, quy ước tiêu chuẩn là đặt tên một lớp test theo tên lớp mà nó kiểm thử, với hậu tố là `Test`.

*   Lớp cần được kiểm thử: `UserService.java`
*   Lớp test: `UserServiceTest.java`

Quy ước này phổ biến đến mức các IDE và công cụ xây dựng dựa vào nó để tự động xác định lớp nào là test.

### **4. Các Phụ thuộc `pom.xml` cho việc Kiểm thử**

Tệp `pom.xml` của bạn khai báo tất cả các thư viện mà dự án của bạn cần. Để kiểm thử, bạn cần thêm JUnit 5, Mockito, và AssertJ. Phần quan trọng nhất là thẻ `<scope>test</scope>`.

Thẻ này cho Maven biết rằng phụ thuộc này **chỉ** cần thiết cho việc biên dịch và chạy mã trong `src/test/java`. Nó sẽ không được bao gồm trong sản phẩm production cuối cùng.

Đây là một khối `dependencies` điển hình trong một `pom.xml` cho một thiết lập kiểm thử hiện đại:

```xml
<dependencies>
    <!-- Thêm các phụ thuộc production của bạn ở đây (ví dụ: Spring Boot) -->

    <!-- =============================================== -->
    <!--  CÁC PHỤ THUỘC KIỂM THỬ                         -->
    <!-- =============================================== -->

    <!-- JUnit 5 (Jupiter) -->
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-engine</artifactId>
        <version>5.10.1</version> <!-- Sử dụng phiên bản mới nhất -->
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-params</artifactId>
        <version>5.10.1</version>
        <scope>test</scope>
    </dependency>

    <!-- Mockito -->
    <dependency>
        <groupId>org.mockito</groupId>
        <artifactId>mockito-core</artifactId>
        <version>5.7.0</version> <!-- Sử dụng phiên bản mới nhất -->
        <scope>test</scope>
    </dependency>
    <dependency>
        <!-- Cần thiết để mock các lớp final/phương thức static -->
        <groupId>org.mockito</groupId>
        <artifactId>mockito-inline</artifactId>
        <version>5.2.0</version>
        <scope>test</scope>
    </dependency>

    <!-- AssertJ -->
    <dependency>
        <groupId>org.assertj</groupId>
        <artifactId>assertj-core</artifactId>
        <version>3.24.2</version> <!-- Sử dụng phiên bản mới nhất -->
        <scope>test</scope>
    </dependency>
</dependencies>
```
*(Lưu ý: Người dùng Spring Boot thường được quản lý các phụ thuộc này một cách tự động thông qua phụ thuộc `spring-boot-starter-test`, điều này đơn giản hóa đáng kể việc thiết lập này.)*

### **5. Cấu hình Test trên IDE (IntelliJ)**

Các IDE hiện đại như IntelliJ IDEA có sự tích hợp sâu với Maven và JUnit.

*   **Tự động Nhận dạng:** Bởi vì bạn tuân theo cấu trúc thư mục Maven tiêu chuẩn, IntelliJ tự động nhận dạng `src/test/java` là một thư mục gốc chứa mã nguồn test.
*   **Biểu tượng ở lề ("Gutter" icons):** Bạn sẽ thấy các biểu tượng "play" nhỏ ở lề bên cạnh các lớp và phương thức test của mình. Bạn có thể nhấp vào chúng để chạy hoặc gỡ lỗi một bài test riêng lẻ hoặc toàn bộ một lớp test.
*   **Giao diện Trình chạy Test:** Khi bạn chạy test, IntelliJ sẽ mở một cửa sổ Trình chạy Test chuyên dụng hiển thị dạng cây các bài test của bạn, thanh tiến trình, và chỉ báo đỏ/xanh rõ ràng về thành công hay thất bại.

### **6. Sử dụng Thẻ/Nhóm để Chạy có chọn lọc (`@Tag`, `@Disabled`)**

Đôi khi bạn chỉ muốn chạy một tập hợp con các bài test của mình. Ví dụ, bạn có thể có một số bài test rất chậm mà bạn chỉ muốn chạy trước khi phát hành, chứ không phải mỗi khi thay đổi mã.

#### **`@Disabled`**
Annotation này rất đơn giản: nó bảo JUnit bỏ qua một phương thức test hoặc toàn bộ một lớp test. Bạn có thể cung cấp một lý do tùy chọn.

```java
@Test
@Disabled("Tính năng này hiện đang được làm lại.")
void someWipTest() {
    // Bài test này sẽ không được thực thi.
}
```
**Thông lệ tốt nhất:** Đừng để các bài test `@Disabled` tích tụ trong codebase của bạn. Chúng là một dạng nợ kỹ thuật (technical debt). Một bài test hoặc là phải hoạt động, hoặc là phải bị xóa.

#### **`@Tag`**
`@Tag` là một cách mạnh mẽ hơn để phân loại các bài test của bạn. Bạn có thể áp dụng một thẻ (một chuỗi đơn giản) cho các phương thức hoặc lớp test.

```java
@Test
@Tag("fast")
void fastUnitTest() { /* ... */ }

@Test
@Tag("slow")
@Tag("database")
void slow_database_integration_test() { /* ... */ }
```

Sau đó, bạn có thể cấu hình công cụ xây dựng (Maven/Gradle) hoặc IDE của mình để **bao gồm** hoặc **loại trừ** các bài test dựa trên các thẻ này. Ví dụ, bạn có thể cấu hình một bước build để chạy tất cả các bài test `!slow` (không chậm) và một bước build chạy hàng đêm để chạy *tất cả* các bài test, bao gồm cả những bài test chậm.

### **Tóm tắt Bài 5: Thiết lập Dự án**

*   **Cấu trúc Tiêu chuẩn:** Bạn biết bố cục Maven với `src/main/java` cho mã production và `src/test/java` cho mã test.
*   **Tách biệt Trách nhiệm:** Bạn hiểu tại sao sự tách biệt này lại quan trọng để giữ cho sản phẩm production của bạn sạch sẽ.
*   **Đặt tên và Gói:** Bạn biết cách phản chiếu cấu trúc gói và sử dụng hậu tố `Test` cho các lớp test.
*   **`pom.xml`:** Bạn có thể thêm các phụ thuộc cần thiết cho JUnit 5, Mockito, và AssertJ với phạm vi `test` chính xác.
*   **Chạy có chọn lọc:** Bạn có thể sử dụng `@Disabled` để tạm thời bỏ qua các bài test và `@Tag` để phân loại các bài test cho các lần chạy test nâng cao, có chọn lọc hơn.