### **I. Core Java & Lập trình hướng đối tượng (OOP)**

**1. Khái niệm cơ bản & Nguyên tắc OOP**
*   **JVM vs JRE vs JDK:** Giải thích sự khác biệt và cách chúng hoạt động cùng nhau để chạy một chương trình Java.
*   **Nguyên tắc OOP:** Giải thích 4 trụ cột (Đóng gói, Kế thừa, Đa hình, Trừu tượng).
    *   **Đa hình (Polymorphism):** Đa hình là gì và bạn đã áp dụng nó trong một dự án thực tế như thế nào?
    *   **Trừu tượng (Abstraction) vs Đóng gói (Encapsulation):** So sánh hai khái niệm này.
*   **Lớp trừu tượng (Abstract Class) vs Giao diện (Interface):** So sánh sự khác biệt và thảo luận về các trường hợp sử dụng trong thực tế.
*   **Nguyên tắc SOLID:** Giải thích 5 nguyên tắc SOLID kèm theo ví dụ.
*   **Constructor:** Một constructor có thể là `private` không? Tại sao bạn lại làm vậy? (ví dụ: trong mẫu thiết kế Singleton, Factory).

**2. Từ khóa & Kiểu dữ liệu cơ bản**
*   **String:**
    *   Tại sao String là bất biến (immutable) và lợi ích của nó là gì?
    *   Điều gì xảy ra khi bạn sử dụng `==` với các đối tượng String so với `.equals()`?
*   **Từ khóa `final`, `finally`, và `finalize`:** Giải thích sự khác biệt.
*   **Từ khóa `synchronized`:** Nó thực sự làm gì và hoạt động như thế nào ở cấp độ đối tượng và cấp độ lớp?
*   **Từ khóa `volatile`:** Vai trò của từ khóa `volatile` là gì và nó liên quan đến Mô hình bộ nhớ Java (JMM) như thế nào?
*   **Lớp bất biến (Immutable Class):** Làm thế nào để tạo một lớp bất biến và tại sao nó an toàn trong môi trường đa luồng?

**3. Collections Framework**
*   **HashMap:** Giải thích chi tiết cách hoạt động bên trong: cơ chế hashing, cách xử lý xung đột (collision), và quá trình thay đổi kích thước (resizing).
*   **So sánh các loại Map:**
    *   ConcurrentHashMap, `Collections.synchronizedMap()`, và Hashtable.
    *   HashMap, LinkedHashMap, và TreeMap.
*   **So sánh các loại List:** ArrayList vs LinkedList - cái nào tốt hơn cho việc chèn (inserts) nhanh hơn và tại sao?
*   **Iterators:** Giải thích về Fail-fast vs Fail-safe iterators và khi nào chúng xuất hiện.
*   **Comparator:** Làm thế nào để thực hiện sắp xếp đa cấp (multi-level sorting) bằng Comparator?

**4. Concurrency & Multithreading (Đa luồng)**
*   **Tạo Thread:** So sánh giữa việc triển khai `Runnable` và kế thừa từ `Thread`.
*   **Deadlock:** Deadlock là gì? Làm thế nào để phát hiện và ngăn chặn nó trong các ứng dụng đa luồng?
*   **ExecutorService:** So sánh ExecutorService, ForkJoinPool, và CompletableFuture.
*   **Vòng đời Thread:** Giải thích vòng đời của một luồng và sự khác biệt giữa việc gọi `start()` và `run()`.

**5. Java 8+ & Lập trình hàm**
*   **Stream API:** Stream trong Java 8 là gì và nó giúp đơn giản hóa code như thế nào?
*   **Functional Interfaces:** Chúng là gì và tại sao chúng quan trọng trong Java 8+?
*   **Optional:** `Optional` là gì và tại sao nó tốt hơn việc kiểm tra `null` thủ công?

**6. Memory Management & Exception Handling**
*   **Garbage Collection:** Cơ chế thu gom rác hoạt động trong Java như thế nào?
*   **Memory Leaks:** Các loại rò rỉ bộ nhớ phổ biến và các công cụ để phát hiện chúng.
*   **Exception Handling:**
    *   So sánh Checked vs Unchecked Exceptions và khi nào nên sử dụng chúng.
    *   Một khối `finally` có thể ghi đè giá trị trả về từ khối `try` không?
    *   Điều gì xảy ra nếu bạn ném một ngoại lệ (exception) từ khối `finally` hoặc khối `catch`?
    *   So sánh `throw` và `throws`.
    *   Tại sao nên tạo các ngoại lệ tùy chỉnh (custom exceptions)?

---

### **II. Spring Framework & Spring Boot**

**1. Core Concepts & IoC/DI**
*   **Dependency Injection:** Cách Spring thực hiện DI và lợi ích của nó.
*   **Vòng đời Bean:** Giải thích vòng đời của một Spring Bean.
*   **So sánh:** BeanFactory vs ApplicationContext.
*   **Annotations:** So sánh `@Autowired`, `@Inject`, và `@Resource`. Tại sao đôi khi chúng ta sử dụng `@Service` thay vì `@Component`?

**2. Spring Boot Internals & Configuration**
*   **Khởi động:** Điều gì thực sự xảy ra khi một ứng dụng Spring Boot khởi động? (Giải thích về auto-configuration).
*   **Servlet:** Vai trò của `DispatcherServlet` trong luồng xử lý request.
*   **Runners:** `CommandLineRunner` và `ApplicationRunner` dùng để làm gì?
*   **Actuator:** Spring Boot Actuator là gì và nó hữu ích cho việc gì?
*   **Configuration:** Các cách để ngoại hóa cấu hình (externalize configuration) và cách sử dụng Profiles để quản lý cấu hình cho các môi trường khác nhau.

**3. Web & REST APIs**
*   **Annotations:** Tại sao nên dùng `@RestController` khi `@Controller` cũng hoạt động được?
*   **Exception Handling:**
    *   So sánh `@ExceptionHandler`, `@ControllerAdvice`, và `@RestControllerAdvice`.
    *   Làm thế nào để xử lý các ngoại lệ được ném ra bởi Filters hoặc Interceptors?
    *   Cách xử lý lỗi validation từ annotation `@Valid`.
*   **Mã trạng thái HTTP:** Các mã nào nên được trả về cho các trường hợp: input không hợp lệ, không tìm thấy tài nguyên, lỗi máy chủ. So sánh 500 và 503.

**4. Data Access & Transaction**
*   **@Transactional:** Giải thích cách hoạt động bên trong của nó (Propagation, Isolation).
*   **JPA/Hibernate:** Cách giải quyết vấn đề N+1 query, so sánh Lazy vs Eager loading, và `LazyInitializationException`.

---

### **III. Microservices & System Design**

**1. Câu hỏi thiết kế hệ thống**
*   Làm thế nào để bạn thiết kế một hệ thống rút gọn URL (URL shortener) có khả năng mở rộng?
*   Làm thế nào để bạn xử lý hàng triệu yêu cầu (millions of requests) trong một hệ thống backend Java?

**2. Kiến trúc & Khái niệm**
*   **Thách thức:** Các thách thức chung trong microservices: tính nhất quán (consistency), giám sát (monitoring), độ trễ (latency).
*   **Giao dịch phân tán:** So sánh cách xử lý: Saga vs 2PC (Two-Phase Commit).
*   **Độ tin cậy:** Giải thích về Circuit Breaker, Retry, và Rate Limiting.
*   **Observability:** Distributed Tracing (Truy vết phân tán) là gì? (Ví dụ: Zipkin, OpenTelemetry).
*   **Configuration:** Mục đích của Spring Cloud Config Server.

**3. Mẫu thiết kế (Design Patterns) cho Microservices**
*   Aggregator, API Gateway, Circuit Breaker, Saga, CQRS, Event Sourcing, Strangler Fig.

---

### **IV. Databases & SQL**

*   **Tối ưu hóa:** Cách tối ưu hóa các truy vấn SQL chậm, vai trò của Indexes (Clustered vs Non-clustered).
*   **Câu lệnh:** Viết các truy vấn SQL thông dụng (ví dụ: tìm mức lương cao thứ hai).
*   **JOINs:** So sánh INNER JOIN, LEFT JOIN, RIGHT JOIN.

---

### **V. Coding / Problem Solving**

*   **Linked List:** Đảo ngược một danh sách liên kết (lặp và đệ quy).
*   **Array:** Tìm phần tử lớn thứ hai trong một mảng.
*   **String:** Tìm ký tự không lặp lại đầu tiên trong một chuỗi.
*   **Logic & Sắp xếp:** Sắp xếp danh sách nhân viên theo lương rồi đến tên.
*   **Java 8 Streams:** Sử dụng Streams để nhóm các giao dịch theo ID người dùng.