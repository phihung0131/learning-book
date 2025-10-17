## Lesson 1: Tổng quan về ORM, JPA và Hibernate

### 1. Giải thích khái niệm chi tiết

#### ORM là gì và tại sao lại cần thiết?

Trong lập trình Java, chúng ta làm việc với các **đối tượng (Objects)**. Đối tượng có thuộc tính, phương thức, và các mối quan hệ phức tạp (kế thừa, composition). Ngược lại, trong cơ sở dữ liệu quan hệ, chúng ta làm việc với các **bảng (Tables)**, bao gồm các hàng và cột.

Sự khác biệt cơ bản này tạo ra một vấn đề gọi là **"Object-Relational Impedance Mismatch"** (Sự không tương thích trở kháng giữa Hướng đối tượng và Quan hệ). Ví dụ:
*   Làm thế nào để biểu diễn quan hệ kế thừa trong bảng?
*   Làm thế nào để chuyển đổi một đối tượng `Order` chứa một danh sách các đối tượng `OrderItem` thành các hàng trong bảng `orders` và `order_items`?

Trước đây, lập trình viên phải giải quyết vấn đề này bằng cách viết rất nhiều code **JDBC (Java Database Connectivity)** lặp đi lặp lại: mở kết nối, tạo `PreparedStatement`, viết câu lệnh SQL `INSERT`/`UPDATE`/`SELECT`, thực thi, rồi lặp qua `ResultSet` để gán từng cột vào từng thuộc tính của đối tượng. Code này rất tẻ nhạt, dễ sinh lỗi, và khó bảo trì.

**ORM (Object-Relational Mapping)** ra đời để giải quyết vấn đề này. Nó là một kỹ thuật lập trình hoạt động như một "người phiên dịch" tự động giữa thế giới đối tượng và thế giới cơ sở dữ liệu quan hệ.

*   **Mapping:** Bạn định nghĩa cách một lớp Java (ví dụ: `class User`) ánh xạ tới một bảng trong database (ví dụ: `table users`).
*   **Automation:** ORM framework sẽ tự động tạo ra các câu lệnh SQL cần thiết (INSERT, UPDATE, DELETE, SELECT) dựa trên các thao tác bạn thực hiện trên đối tượng Java. Ví dụ, khi bạn gọi `userRepository.save(newUser)`, ORM sẽ tự sinh ra câu lệnh `INSERT INTO users (...) VALUES (...)`.

#### Mối quan hệ giữa JPA và Hibernate

Đây là một trong những câu hỏi phỏng vấn phổ biến nhất.

1.  **JPA (Java Persistence API):**
    *   Đây là một **Specification (Đặc tả / Bộ quy tắc)**. Nó KHÔNG phải là một chương trình hay một thư viện có thể chạy được.
    *   JPA chỉ định nghĩa một tập hợp các interface (ví dụ: `EntityManager`, `EntityTransaction`) và các annotation (ví dụ: `@Entity`, `@Id`, `@Table`) như một chuẩn chung cho việc làm ORM trong Java.
    *   **Mục đích:** Giúp ứng dụng của bạn không bị phụ thuộc vào một nhà cung cấp ORM cụ thể. Nếu bạn viết code theo chuẩn JPA, về lý thuyết bạn có thể chuyển đổi ORM framework (ví dụ: từ Hibernate sang EclipseLink) mà không cần thay đổi code nghiệp vụ.

2.  **Hibernate:**
    *   Đây là một **Implementation (Bản triển khai)** của đặc tả JPA. Nó là một framework ORM cụ thể, cung cấp code thực thi cho các interface và xử lý logic đằng sau các annotation mà JPA định nghĩa.
    *   Hibernate là implementation JPA phổ biến và mạnh mẽ nhất hiện nay.
    *   **Tóm lại bằng một ví dụ:** JPA giống như chuẩn cổng USB. Nó định nghĩa hình dáng, số chân cắm, cách truyền điện... Hibernate giống như một chiếc USB Flash Drive của hãng SanDisk. SanDisk sản xuất một thiết bị vật lý tuân theo chuẩn USB.

Khi bạn dùng `spring-boot-starter-data-jpa`, nó sẽ tự động kéo theo **Hibernate** làm implementation mặc định.

#### Các thành phần cốt lõi

*   **EntityManagerFactory:** Là một đối tượng "nặng", thread-safe, thường chỉ được tạo một lần khi ứng dụng khởi động. Nhiệm vụ của nó là tạo ra các `EntityManager`. Trong thế giới Hibernate thuần, nó tương đương với `SessionFactory`.
*   **EntityManager:** Đây là interface chính mà lập trình viên tương tác. Nó là một đối tượng "nhẹ", **không thread-safe**, đại diện cho một "unit of work" (đơn vị công việc) với database. Mỗi `EntityManager` quản lý một tập hợp các đối tượng (entity).
*   **Persistence Context (PC - Ngữ cảnh bền vững):**
    *   Đây là khái niệm nội bộ quan trọng nhất. Mỗi `EntityManager` có một Persistence Context.
    *   PC hoạt động như một **bộ đệm (cache cấp 1)** hoặc một "staging area" (khu vực chuẩn bị). Nó là một map chứa các đối tượng (entity) đang được quản lý. Khi bạn lấy một entity từ database, nó sẽ được đặt vào PC. Khi bạn lưu một entity mới, nó cũng được đưa vào PC trước khi được đồng bộ xuống database.
    *   PC là chìa khóa cho nhiều tính năng "phép màu" của JPA như **dirty checking** (tự động phát hiện thay đổi), quản lý trạng thái entity, và tối ưu hóa hiệu năng.

#### persistence.xml vs Spring Boot Auto-Configuration

*   **`persistence.xml`:** Trong các dự án Java EE hoặc JPA thuần, đây là file cấu hình XML tiêu chuẩn để định nghĩa "persistence unit", chỉ định nhà cung cấp JPA, thông tin kết nối database, và các thuộc tính khác.
*   **Spring Boot Auto-Configuration:** Spring Boot loại bỏ hoàn toàn sự cần thiết của `persistence.xml`. Nó sẽ tự động cấu hình `EntityManagerFactory` và `DataSource` dựa trên các thuộc tính trong file `application.properties` hoặc `application.yml` (ví dụ: `spring.datasource.url`, `spring.jpa.hibernate.ddl-auto`).

### 2. Ví dụ minh họa

**Tình huống:** Thiết lập một dự án Spring Boot đơn giản để lưu một đối tượng `Course` vào database H2 trong bộ nhớ.

**1. `pom.xml`:**
```xml
<dependencies>
    <!-- Starter cho JPA, tự động kéo Hibernate vào -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <!-- Database H2 chạy trong bộ nhớ, rất tiện cho việc phát triển -->
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

**2. `application.properties`:**
```properties
# Cấu hình kết nối tới H2 Database
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# Bật H2 Console để xem dữ liệu trên trình duyệt tại http://localhost:8080/h2-console
spring.h2.console.enabled=true

# Cấu hình JPA và Hibernate
spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
# update: Tự động tạo/cập nhật schema database dựa trên các Entity.
# Rất hữu ích khi phát triển, nhưng KHÔNG ĐƯỢC DÙNG TRONG PRODUCTION.
spring.jpa.hibernate.ddl-auto=update
```

**3. Tạo Entity `Course`:**
```java
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.Id;

@Entity // Đánh dấu đây là một JPA Entity, nó sẽ được ánh xạ tới một bảng
public class Course {

    @Id // Đánh dấu đây là khóa chính
    @GeneratedValue // Yêu cầu JPA tự động sinh giá trị cho khóa chính
    private Long id;

    private String name;

    // JPA yêu cầu một constructor không tham số
    protected Course() {}

    public Course(String name) {
        this.name = name;
    }

    // Getters and Setters
}
```

**4. Chạy ứng dụng và lưu dữ liệu:**
Chúng ta sẽ sử dụng `CommandLineRunner` để thực thi code sau khi ứng dụng khởi động.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.transaction.annotation.Transactional;

import javax.persistence.EntityManager;
import javax.persistence.PersistenceContext;

@SpringBootApplication
public class JpaDemoApplication implements CommandLineRunner {

    // Tiêm EntityManager vào để tương tác trực tiếp với JPA
    @PersistenceContext
    private EntityManager entityManager;

    public static void main(String[] args) {
        SpringApplication.run(JpaDemoApplication.class, args);
    }

    @Override
    @Transactional // Mọi thao tác thay đổi dữ liệu phải nằm trong một transaction
    public void run(String... args) throws Exception {
        System.out.println("Bắt đầu lưu một Course mới...");
        
        Course newCourse = new Course("Mastering Spring Data JPA");
        
        // Dùng EntityManager để lưu (persist) entity
        // Thao tác này sẽ đưa newCourse vào Persistence Context
        entityManager.persist(newCourse);
        
        System.out.println("Đã lưu Course thành công!");
        // Khi phương thức này kết thúc, transaction sẽ commit,
        // và Hibernate sẽ tự động flush các thay đổi từ PC xuống DB.
    }
}
```
Khi chạy ứng dụng, bạn sẽ thấy log của Hibernate tạo ra bảng `course` và chèn dữ liệu vào đó.

### 3. Mini Exercise

1.  Thêm một thuộc tính mới `String author` vào entity `Course`.
2.  Khởi động lại ứng dụng.
3.  Truy cập H2 Console (`http://localhost:8080/h2-console`, dùng URL JDBC là `jdbc:h2:mem:testdb`) và kiểm tra xem cột `author` đã được tự động thêm vào bảng `COURSE` hay chưa. Quan sát log của Hibernate để xem câu lệnh SQL `ALTER TABLE` được tạo ra.

### 4. Quiz Question

**Câu hỏi:** Đâu là mô tả chính xác nhất về mối quan hệ giữa **JPA** và **Hibernate**?

a) Chúng là hai tên gọi khác nhau cho cùng một công nghệ ORM.
b) Hibernate là một phiên bản cũ hơn, trong khi JPA là phiên bản mới và hiện đại hơn.
c) JPA là một đặc tả (bộ quy tắc và interface), còn Hibernate là một trong những thư viện thực thi (implement) đặc tả đó.
d) JPA được dùng cho các ứng dụng đơn giản, còn Hibernate được dùng cho các ứng dụng doanh nghiệp phức tạp.

---
**Đáp án:** **c) JPA là một đặc tả (bộ quy tắc và interface), còn Hibernate là một trong những thư viện thực thi (implement) đặc tả đó.**

## Lesson 2: Entity Mapping Cơ Bản

### 1. Giải thích khái niệm chi tiết

#### Các Annotation Mapping Cốt lõi

Để biến một lớp Java POJO (Plain Old Java Object) thành một thứ mà JPA có thể hiểu và lưu vào database, chúng ta sử dụng một tập hợp các annotation.

*   `@Entity`: Đây là annotation quan trọng nhất. Nó đánh dấu một lớp Java là một **Entity**, tức là một đối tượng có thể được JPA quản lý và ánh xạ tới một bảng trong cơ sở dữ liệu.
*   `@Table(name = "ten_bang")`: Mặc định, JPA sẽ ánh xạ entity `UserAccount` tới bảng `user_account`. Annotation này cho phép bạn tùy chỉnh tên bảng, schema, và thêm các ràng buộc (constraints) khác.
*   `@Id`: Đánh dấu một trường (field) là **khóa chính (primary key)** của bảng. Mọi entity bắt buộc phải có một `@Id`.
*   `@GeneratedValue`: Được sử dụng cùng với `@Id`, nó chỉ định cách giá trị của khóa chính được sinh ra tự động.
    *   **`strategy = GenerationType.IDENTITY`**: Giao phó việc tạo ID cho cơ sở dữ liệu, thường sử dụng cột `AUTO_INCREMENT` (MySQL) hoặc `IDENTITY` (SQL Server).
        *   **Cơ chế nội bộ (Phỏng vấn!):** Khi bạn gọi `persist()`, Hibernate phải thực thi câu lệnh `INSERT` **ngay lập tức** để lấy được ID do database trả về. Điều này vô hiệu hóa một số tối ưu hóa của Hibernate như JDBC batch inserts.
    *   **`strategy = GenerationType.SEQUENCE`**: Sử dụng một sequence trong database để tạo ID (phổ biến trong Oracle, PostgreSQL).
        *   **Cơ chế nội bộ (Phỏng vấn!):** Hibernate có thể lấy một loạt các ID từ sequence chỉ trong một lần gọi đến database. Điều này cho phép nó gán ID cho các entity trong bộ nhớ và thực hiện batch inserts một cách hiệu quả, do đó **hiệu năng thường tốt hơn IDENTITY**.
    *   **`strategy = GenerationType.AUTO`**: Mặc định. Cho phép JPA provider (Hibernate) tự chọn chiến lược phù hợp nhất dựa trên dialect của database.
*   `@Column(name = "ten_cot", nullable = false, unique = true, length = 100)`: Tùy chỉnh việc ánh xạ một trường tới một cột trong bảng. Bạn có thể định nghĩa tên cột, ràng buộc `NOT NULL`, `UNIQUE`, độ dài, v.v.

#### Trạng thái của Entity (Entity Lifecycle States)

Đây là một khái niệm **cực kỳ quan trọng** và là câu hỏi phỏng vấn kinh điển để đánh giá độ hiểu sâu về JPA. Một đối tượng entity có thể tồn tại ở một trong bốn trạng thái sau:

1.  **Transient (Tạm thời / Mới):**
    *   **Mô tả:** Là một đối tượng Java vừa được tạo bằng từ khóa `new`. Nó chưa được gắn với bất kỳ **Persistence Context (PC)** nào. JPA hoàn toàn không biết về sự tồn tại của nó.
    *   **Ví dụ:** `User user = new User();`
2.  **Persistent (Bền vững / Được quản lý):**
    *   **Mô tả:** Là một entity đang được quản lý bởi một `EntityManager` và nằm trong **Persistence Context**. Mọi thay đổi trên đối tượng ở trạng thái này sẽ được JPA theo dõi.
    *   **Làm sao để có trạng thái này?**
        *   Khi bạn gọi `entityManager.persist(transientObject)`.
        *   Khi bạn lấy một entity từ database (ví dụ: `entityManager.find(...)` hoặc qua một repository).
    *   **"Phép màu" của JPA:** Khi một transaction commit, JPA sẽ tự động phát hiện các thay đổi trên các entity persistent (cơ chế này gọi là **dirty checking**) và tự động sinh ra câu lệnh `UPDATE` tương ứng.
3.  **Detached (Tách rời):**
    *   **Mô tả:** Là một entity đã từng ở trạng thái persistent, nhưng `EntityManager` (và PC) quản lý nó đã bị đóng, hoặc entity đã bị tách ra một cách tường minh (`entityManager.detach()`). JPA không còn theo dõi các thay đổi trên đối tượng này nữa.
    *   **Làm sao để lưu lại thay đổi?** Bạn phải "gắn" nó trở lại vào một PC mới bằng cách gọi `entityManager.merge(detachedObject)`.
4.  **Removed (Bị xóa):**
    *   **Mô tả:** Là một entity persistent đã được đánh dấu để xóa khỏi database bằng cách gọi `entityManager.remove()`. Nó vẫn còn trong PC cho đến khi transaction commit.
    *   Khi transaction commit, JPA sẽ sinh ra câu lệnh `DELETE` tương ứng.

#### Các Annotation Mapping khác

*   `@Transient`: Đánh dấu một trường trong class entity để JPA **bỏ qua**, không ánh xạ nó tới bất kỳ cột nào trong database. Hữu ích cho các trường chỉ mang tính tính toán tạm thời.
*   `@Enumerated`: Chỉ định cách ánh xạ một kiểu `enum` của Java.
    *   `EnumType.ORDINAL` (mặc định): Lưu chỉ số (0, 1, 2...) của enum. **CỰC KỲ NGUY HIỂM VÀ KHÔNG NÊN DÙNG!** Nếu bạn thay đổi thứ tự các hằng số trong enum, dữ liệu cũ trong database sẽ bị sai.
    *   `EnumType.STRING` (**Best Practice**): Lưu tên của hằng số enum dưới dạng chuỗi. An toàn và dễ đọc hơn nhiều.
*   `@Temporal`: Dành cho các kiểu ngày giờ cũ (`java.util.Date`, `Calendar`). Chỉ định xem nên lưu dưới dạng `DATE`, `TIME`, hay `TIMESTAMP`.
    *   **Best Practice:** Sử dụng các API ngày giờ mới của Java 8 (`java.time.LocalDate`, `LocalDateTime`). Chúng không cần annotation `@Temporal` và được Hibernate hỗ trợ mặc định.
*   `@Lob`: Dành cho các đối tượng lớn (Large Objects). Dùng cho các trường `String` rất dài (ánh xạ thành `CLOB`) hoặc mảng `byte[]` (ánh xạ thành `BLOB`).

### 2. Ví dụ minh họa

**Tình huống:** Tạo một entity `User` với các kiểu dữ liệu khác nhau và lưu vào database.

**1. Tạo Entity `User`:**
```java
import org.hibernate.annotations.CreationTimestamp;
import javax.persistence.*;
import java.time.LocalDateTime;

@Entity
@Table(name = "users") // Tùy chỉnh tên bảng
public class User {

    public enum UserStatus {
        ACTIVE, INACTIVE, PENDING
    }

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(name = "username", nullable = false, unique = true, length = 50)
    private String username;

    @Column(name = "full_name", length = 100)
    private String fullName;

    @Enumerated(EnumType.STRING) // Best practice cho enum
    @Column(length = 20)
    private UserStatus status;

    @CreationTimestamp // Annotation của Hibernate, tự động gán thời gian tạo
    @Column(updatable = false)
    private LocalDateTime createdAt;
    
    @Transient // JPA sẽ bỏ qua trường này
    private String temporaryInfo;

    // Constructor, Getters, Setters
}
```

**2. Tạo Repository (sử dụng Spring Data JPA):**
Interface `JpaRepository` cung cấp sẵn các phương thức CRUD (`save`, `findById`, `findAll`, `delete`...).

```java
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.stereotype.Repository;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {
    // Spring Data sẽ tự tạo query từ tên phương thức này
    User findByUsername(String username);
}
```

**3. Chạy và kiểm tra (trong lớp `CommandLineRunner`):**

```java
@SpringBootApplication
public class JpaDemoApplication implements CommandLineRunner {

    @Autowired
    private UserRepository userRepository;

    public static void main(String[] args) {
        SpringApplication.run(JpaDemoApplication.class, args);
    }

    @Override
    @Transactional
    public void run(String... args) throws Exception {
        // --- Trạng thái: Transient ---
        User user1 = new User("john.doe", "John Doe", UserStatus.PENDING);
        System.out.println("User 1 (Transient): " + user1);
        
        // --- Chuyển sang Persistent ---
        userRepository.save(user1); // entityManager.persist() được gọi ở hậu trường
        System.out.println("User 1 (Persistent) sau khi save, đã có ID: " + user1.getId());
        
        // --- Dirty Checking ---
        // user1 đang là Persistent. Mọi thay đổi sẽ được theo dõi.
        System.out.println("Thay đổi status của user1...");
        user1.setStatus(UserStatus.ACTIVE);
        
        // KHÔNG CẦN GỌI userRepository.save(user1) lần nữa!
        // Khi transaction này kết thúc (commit), Hibernate sẽ tự động
        // phát hiện sự thay đổi và tạo câu lệnh UPDATE.
        
        System.out.println("Kết thúc transaction. Hibernate sẽ tự động flush các thay đổi.");
    }
}
```
*   **Log Output:** Bạn sẽ thấy Hibernate sinh ra một câu lệnh `INSERT` khi `save()` được gọi lần đầu, và một câu lệnh `UPDATE` khi phương thức `run()` kết thúc.

### 3. Mini Exercise

1.  Thêm hai thuộc tính mới vào entity `User`:
    *   `private LocalDateTime lastLogin;`
    *   `private int failedLoginAttempts;`
2.  Sử dụng `@Column` để đảm bảo `failedLoginAttempts` có giá trị mặc định là `0` khi tạo cột trong database. (Gợi ý: dùng thuộc tính `columnDefinition = "INT DEFAULT 0"`).
3.  Chạy lại ứng dụng và kiểm tra schema của bảng `users` trong H2 Console.

### 4. Quiz Question

**Câu hỏi:** Giả sử bạn có đoạn code sau bên trong một phương thức `@Transactional`:

```java
User user = userRepository.findById(1L).orElse(null); // user bây giờ ở trạng thái Persistent
if (user != null) {
    user.setFullName("New Name");
}
// Không có lời gọi save() nào ở đây
```
Khi phương thức này kết thúc, điều gì sẽ xảy ra với bản ghi của user có ID=1 trong database?

a) Không có gì thay đổi, vì `userRepository.save(user)` đã không được gọi.
b) Tên của user trong database sẽ được cập nhật thành "New Name" do cơ chế dirty checking của Hibernate.
c) Sẽ có lỗi `LazyInitializationException` vì transaction đã kết thúc.
d) Tên sẽ được cập nhật, nhưng chỉ trong bộ nhớ cache, không phải trong database.

---
**Đáp án:** **b) Tên của user trong database sẽ được cập nhật thành "New Name" do cơ chế dirty checking của Hibernate.**

**Giải thích:** Khi `userRepository.findById()` được gọi, entity `user` được nạp vào Persistence Context và ở trạng thái **Persistent**. `EntityManager` sẽ theo dõi mọi thay đổi trên đối tượng này. Trước khi transaction commit, Hibernate sẽ "quét" qua các đối tượng trong PC, so sánh trạng thái hiện tại với trạng thái ban đầu lúc được nạp. Nếu có sự khác biệt, nó sẽ tự động sinh và thực thi câu lệnh `UPDATE`. Đây là một trong những tính năng mạnh mẽ và cốt lõi nhất của JPA/Hibernate.

## Lesson 3: Quan hệ giữa các Entity (Relationships)

### 1. Giải thích khái niệm chi tiết

Đây là phần phức tạp nhưng cũng là phần mạnh mẽ nhất của ORM. Nó cho phép bạn biểu diễn các mối quan hệ giữa các bảng (khóa chính - khóa ngoại) bằng các tham chiếu đối tượng trong Java.

#### Các loại quan hệ

1.  **`@ManyToOne` / `@OneToMany` (Nhiều-Một / Một-Nhiều):**
    *   Đây là mối quan hệ phổ biến nhất. Ví dụ: Một `User` có thể có nhiều `Order`, nhưng mỗi `Order` chỉ thuộc về một `User`.
    *   **`@ManyToOne` (phía "Nhiều"):** Đặt ở entity `Order`.
        ```java
        // Trong class Order
        @ManyToOne
        @JoinColumn(name = "user_id") // Định nghĩa cột khóa ngoại
        private User user;
        ```
        `@JoinColumn` chỉ định cột trong bảng `orders` sẽ lưu khóa ngoại (`user_id`) tham chiếu đến khóa chính của bảng `users`.
    *   **`@OneToMany` (phía "Một"):** Đặt ở entity `User`.
        ```java
        // Trong class User
        @OneToMany(mappedBy = "user") // "user" là tên field ở class Order
        private List<Order> orders;
        ```
        `mappedBy` là một thuộc tính cực kỳ quan-trọng. Nó báo cho JPA rằng: "Quan hệ này đã được quản lý bởi phía `@ManyToOne` (cụ thể là field `user` trong class `Order`). Tôi chỉ là 'gương phản chiếu' của quan hệ đó, đừng tạo thêm cột khóa ngoại nào ở đây cả." **Bên sở hữu (owning side) của quan hệ luôn là phía `@ManyToOne`**, nơi có `JoinColumn`.

2.  **`@OneToOne` (Một-Một):**
    *   Ví dụ: Một `User` chỉ có một `UserProfile`.
    *   Cách triển khai tương tự `@ManyToOne`, nhưng thường sẽ có thêm ràng buộc `unique=true` trên khóa ngoại để đảm bảo tính "một-một".
    *   Có thể dùng chung khóa chính hoặc dùng khóa ngoại.

3.  **`@ManyToMany` (Nhiều-Nhiều):**
    *   Ví dụ: Một `Post` có thể có nhiều `Tag`, và một `Tag` có thể được gán cho nhiều `Post`.
    *   Trong database, quan hệ này được biểu diễn bằng một **bảng trung gian (join table)**.
    *   **Cách triển khai:**
        ```java
        // Trong class Post
        @ManyToMany
        @JoinTable(
            name = "post_tags", // Tên bảng trung gian
            joinColumns = @JoinColumn(name = "post_id"), // Khóa ngoại trỏ về Post
            inverseJoinColumns = @JoinColumn(name = "tag_id") // Khóa ngoại trỏ về Tag
        )
        private Set<Tag> tags;
        ```
        **Lưu ý (Phỏng vấn!):** Quan hệ `@ManyToMany` thường không được khuyến khích trong các ứng dụng phức tạp vì bảng trung gian thường cần chứa thêm các cột thông tin (ví dụ: `created_at`). Trong trường hợp đó, **best practice** là phá vỡ quan hệ nhiều-nhiều thành hai quan hệ một-nhiều bằng cách tạo một entity trung gian (ví dụ: `PostTag`).

#### `CascadeType` và `orphanRemoval`

*   `CascadeType`: Nó trả lời câu hỏi: "Khi tôi thực hiện một hành động (persist, merge, remove...) trên entity cha, có nên tự động thực hiện hành động tương tự trên các entity con liên quan không?"
    *   `CascadeType.PERSIST`: Khi lưu `User`, tự động lưu cả các `Order` mới của user đó.
    *   `CascadeType.MERGE`: Khi cập nhật `User` (detached), tự động cập nhật cả các `Order`.
    *   `CascadeType.REMOVE`: Khi xóa `User`, tự động xóa tất cả `Order` của user đó.
    *   `CascadeType.ALL`: Bao gồm tất cả các loại trên.
    *   **Cảnh báo:** Dùng `CascadeType.ALL` hoặc `REMOVE` một cách cẩn thận, đặc biệt với các quan hệ không phải là sở hữu hoàn toàn (composition) vì có thể gây xóa dữ liệu không mong muốn.

*   `orphanRemoval = true`: Một tính năng của Hibernate, mạnh hơn `CascadeType.REMOVE`. Khi bạn **xóa một `Order` khỏi danh sách `orders` của `User`** (ví dụ: `user.getOrders().remove(someOrder)`) và sau đó lưu `User`, `Order` đó sẽ bị xóa khỏi database vì nó đã trở thành "trẻ mồ côi" (không còn được `User` tham chiếu đến).

#### `FetchType` và vấn đề N+1 Query

Đây là một trong những chủ đề quan trọng nhất về hiệu năng và là câu hỏi phỏng vấn **chắc chắn gặp**.

*   `FetchType`: Nó trả lời câu hỏi: "Khi tôi tải một entity cha (ví dụ `User`), có nên tải luôn các entity con liên quan (ví dụ danh sách `Order`) không?"
    1.  **`EAGER` (Háo hức):**
        *   Tải entity cha và các entity con liên quan **trong cùng một câu lệnh SELECT** (thường dùng JOIN).
        *   **Mặc định cho các quan hệ `-ToOne` (`@ManyToOne`, `@OneToOne`).**
        *   **Ưu điểm:** Đơn giản, không bị `LazyInitializationException`.
        *   **Nhược điểm:** Có thể tải thừa dữ liệu không cần thiết, làm giảm hiệu năng.

    2.  **`LAZY` (Lười biếng):**
        *   Chỉ tải entity cha. Các entity con liên quan chỉ được tải từ database **khi bạn truy cập chúng lần đầu tiên** (ví dụ: gọi `user.getOrders()`).
        *   **Mặc định cho các quan hệ `-ToMany` (`@OneToMany`, `@ManyToMany`).**
        *   **Ưu điểm:** Hiệu năng tốt hơn vì chỉ tải dữ liệu khi thực sự cần.
        *   **Nhược điểm:** Dễ gây ra hai vấn đề lớn:
            *   **`LazyInitializationException`:** Nếu bạn cố gắng truy cập một collection `LAZY` sau khi session/transaction đã đóng (ví dụ trong tầng View), Hibernate sẽ không thể tải dữ liệu và ném ra exception này.
            *   **N+1 Query Problem:** Đây là "sát thủ thầm lặng" về hiệu năng.
                *   **Kịch bản:** Bạn tải N entity `User` (1 câu query). Sau đó, bạn lặp qua danh sách N `User` này và trong vòng lặp, bạn gọi `user.getOrders()` (là `LAZY`). Hibernate sẽ phải thực hiện thêm N câu query nữa để tải `orders` cho từng `User`. Tổng cộng, bạn mất **1 + N câu query** chỉ để hiển thị danh sách user và order của họ.

### 2. Ví dụ minh họa

**Tình huống:** Mô hình hóa quan hệ giữa `Author` (Tác giả) và `Book` (Sách). Một tác giả có thể viết nhiều sách.

**1. Tạo Entity:**

```java
// Author.java
@Entity
public class Author {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    @OneToMany(mappedBy = "author", cascade = CascadeType.ALL, orphanRemoval = true, fetch = FetchType.LAZY)
    private List<Book> books = new ArrayList<>();

    // Helper method để đồng bộ cả hai phía của quan hệ
    public void addBook(Book book) {
        books.add(book);
        book.setAuthor(this);
    }
    // Getters, Setters...
}

// Book.java
@Entity
public class Book {
    @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String title;

    @ManyToOne(fetch = FetchType.LAZY) // LAZY là tốt nhất cho hiệu năng
    @JoinColumn(name = "author_id")
    private Author author;
    
    // Getters, Setters...
}
```
*   **Best Practice:**
    *   Luôn sử dụng `LAZY` cho tất cả các quan hệ nếu có thể.
    *   Cung cấp các helper method (`addBook`, `removeBook`) để đảm bảo tính nhất quán của quan hệ ở cả hai phía.
    *   Sử dụng `Set` thay vì `List` cho các quan hệ `-ToMany` nếu thứ tự không quan trọng, để tránh các vấn đề về hiệu năng và trùng lặp.

**2. Repository và CommandLineRunner:**
```java
// AuthorRepository extends JpaRepository<Author, Long>
// BookRepository extends JpaRepository<Book, Long>

// Trong CommandLineRunner
@Transactional
public void run(String... args) throws Exception {
    // --- Tạo và lưu ---
    Author author = new Author("Joshua Bloch");
    
    Book book1 = new Book("Effective Java");
    Book book2 = new Book("Java Puzzlers");
    
    author.addBook(book1);
    author.addBook(book2);
    
    authorRepository.save(author); // Do có CascadeType.ALL, cả 2 book cũng sẽ được lưu
    
    // --- Thử N+1 Problem ---
    System.out.println("\n--- GÂY RA N+1 PROBLEM ---");
    // 1 query để lấy tất cả Author
    List<Author> authors = authorRepository.findAll(); 
    for (Author a : authors) {
        // N query nữa để lấy books cho mỗi author
        System.out.println("Tác giả: " + a.getName() + ", Sách: " + a.getBooks().size()); 
    }
}
```
*   **Log của Hibernate:** Bạn sẽ thấy rõ 1 câu `SELECT * FROM author` và sau đó là N câu `SELECT * FROM book WHERE author_id=?`.

### 3. Mini Exercise

Bạn đã thấy N+1 problem xảy ra. Bây giờ hãy khắc phục nó.

1.  Trong `AuthorRepository`, tạo một phương thức mới.
2.  Sử dụng annotation `@Query` với câu lệnh **JPQL** và từ khóa `JOIN FETCH` để tải `Author` và `books` của họ trong cùng một câu query.
    *   Gợi ý: `@Query("SELECT a FROM Author a JOIN FETCH a.books")`
3.  Gọi phương thức mới này trong `CommandLineRunner` và quan sát log của Hibernate. Bạn sẽ thấy chỉ còn **duy nhất một câu query** được thực thi.

### 4. Quiz Question

**Câu hỏi:** Trong quan hệ `OneToMany` giữa `User` và `Order`, thuộc tính `mappedBy = "user"` trong `User` entity có ý nghĩa là gì?

a) Nó chỉ định rằng cột `user` trong bảng `orders` là khóa ngoại.
b) Nó tạo ra một bảng trung gian tên là `user`.
c) Nó báo cho JPA rằng phía `Order` (cụ thể là trường `user` trong lớp `Order`) là bên sở hữu (owning side) của quan hệ và chịu trách nhiệm quản lý cột khóa ngoại.
d) Nó cho phép xóa các `Order` khi một `User` bị xóa.

---
**Đáp án:** **c) Nó báo cho JPA rằng phía `Order` (cụ thể là trường `user` trong lớp `Order`) là bên sở hữu (owning side) của quan hệ và chịu trách nhiệm quản lý cột khóa ngoại.**

**Giải thích:** `mappedBy` là một trong những khái niệm dễ gây nhầm lẫn nhất. Nó không định nghĩa cột, mà chỉ "tham chiếu ngược" lại trường ở phía bên kia. Nó nói rằng: "Đừng tạo cột khóa ngoại ở phía tôi. Hãy nhìn vào trường có tên được chỉ định trong `mappedBy` ở entity kia, đó mới là nơi quản lý quan hệ vật lý trong database."

## Lesson 4: Spring Data Repository Abstraction

### 1. Giải thích khái niệm chi tiết

#### Vấn đề: Code DAO lặp đi lặp lại

Trong Lesson 1, chúng ta đã tương tác với JPA thông qua `EntityManager`. Mặc dù nó rất mạnh mẽ, nhưng nếu tiếp tục sử dụng, bạn sẽ phải viết lặp đi lặp lại các phương thức cơ bản cho mỗi entity: `findById`, `findAll`, `save`, `delete`... Lớp chứa các phương thức này thường được gọi là **DAO (Data Access Object)**. Việc tự viết DAO rất tốn công và dễ sinh lỗi.

**Spring Data JPA** ra đời để giải quyết triệt để vấn đề này. Nó là một lớp trừu tượng (abstraction) nằm **bên trên** JPA provider (Hibernate). Mục tiêu của nó là giảm đáng kể lượng code boilerplate cần thiết cho tầng truy cập dữ liệu.

#### Các Interface Repository

Spring Data cung cấp một loạt các interface, bạn chỉ cần kế thừa chúng, và Spring Data sẽ **tự động tạo ra implementation** cho bạn tại runtime.

1.  **`Repository<T, ID>`:**
    *   Interface gốc, chỉ mang tính đánh dấu. Nó không cung cấp bất kỳ phương thức nào.
    *   Ít khi được sử dụng trực tiếp.

2.  **`CrudRepository<T, ID>`:**
    *   Cung cấp các phương thức CRUD (Create, Read, Update, Delete) cơ bản:
        *   `save(entity)`: Lưu một entity mới hoặc cập nhật một entity đã có.
        *   `findById(id)`: Tìm entity theo ID, trả về `Optional<T>`.
        *   `findAll()`: Lấy tất cả entity.
        *   `count()`: Đếm số lượng entity.
        *   `deleteById(id)`: Xóa entity theo ID.
        *   `existsById(id)`: Kiểm tra sự tồn tại.

3.  **`PagingAndSortingRepository<T, ID>`:**
    *   Kế thừa từ `CrudRepository`.
    *   Thêm các phương thức để hỗ trợ **phân trang (pagination)** và **sắp xếp (sorting)**:
        *   `findAll(Sort sort)`: Lấy tất cả entity và sắp xếp.
        *   `findAll(Pageable pageable)`: Lấy một "trang" dữ liệu.

4.  **`JpaRepository<T, ID>`:**
    *   Kế thừa từ `PagingAndSortingRepository`.
    *   Đây là interface **được sử dụng phổ biến nhất** khi làm việc với JPA.
    *   Nó cung cấp thêm các phương thức dành riêng cho JPA, tận dụng sức mạnh của `EntityManager`:
        *   `findAll()`: Trả về `List<T>` (thay vì `Iterable<T>` như CrudRepository).
        *   `flush()`: Đồng bộ hóa Persistence Context xuống database ngay lập tức.
        *   `saveAndFlush(entity)`: Lưu và đồng bộ ngay.
        *   `deleteInBatch(entities)`: Xóa một loạt entity hiệu quả hơn.

**Cơ chế hoạt động nội bộ:** Khi ứng dụng khởi động, Spring Data sẽ quét tìm các interface kế thừa từ `Repository`. Đối với mỗi interface tìm thấy, nó sử dụng một `ProxyFactory` để tạo ra một đối tượng proxy tại runtime. Khi bạn gọi một phương thức trên proxy này (ví dụ: `userRepository.findById(1L)`), proxy sẽ ủy quyền lời gọi đến một implementation được tạo sẵn, implementation này sẽ sử dụng `EntityManager` để thực hiện thao tác tương ứng.

#### Derived Queries (Query tự suy từ tên phương thức)

Đây là tính năng "ma thuật" và mạnh mẽ nhất của Spring Data JPA. Bạn không cần viết code query, chỉ cần đặt tên phương thức theo một quy ước nhất định, Spring Data sẽ tự động phân tích tên phương thức và tạo ra câu lệnh JPQL tương ứng.

*   **Quy tắc:** `findBy{TênThuộcTính}{ToánTử}{GiáTrị}[And/Or][OrderBy...]`
*   **Ví dụ:**
    *   `List<User> findByStatus(UserStatus status);` -> `SELECT u FROM User u WHERE u.status = ?1`
    *   `Optional<User> findByUsernameAndEmail(String username, String email);` -> `... WHERE u.username = ?1 AND u.email = ?2`
    *   `List<User> findByAgeGreaterThan(int age);` -> `... WHERE u.age > ?1`
    *   `List<User> findByFullNameContainingIgnoreCase(String keyword);` -> `... WHERE lower(u.fullName) LIKE lower(?1)`
    *   `List<User> findTop5ByOrderByCreatedAtDesc();` -> Lấy 5 user mới nhất.

#### `@Query` (JPQL và Native Query)

Khi một query quá phức tạp để biểu diễn bằng tên phương thức, bạn có thể tự viết nó bằng `@Query`.

1.  **JPQL (Java Persistence Query Language):**
    *   Là ngôn ngữ truy vấn hướng đối tượng, làm việc với **Entity và thuộc tính của Entity**, chứ không phải bảng và cột.
    *   **Best Practice:** Luôn ưu tiên dùng JPQL vì nó độc lập với database (database-agnostic).
    *   ```java
        @Query("SELECT u FROM User u WHERE u.status = :status AND u.age > :age")
        List<User> findUsersByStatusAndAge(@Param("status") UserStatus status, @Param("age") int age);
        ```

2.  **Native SQL Query:**
    *   Cho phép bạn viết câu lệnh SQL thuần, dành riêng cho một loại database cụ thể.
    *   **Khi nào dùng?** Khi bạn cần sử dụng các tính năng đặc thù của database mà JPQL không hỗ trợ (ví dụ: Common Table Expressions - CTE, các hàm window, query hint...).
    *   **Cảnh báo:** Sử dụng Native Query làm cho ứng dụng của bạn bị phụ thuộc vào một loại database cụ thể.
    *   ```java
        @Query(value = "SELECT * FROM users u WHERE u.username = ?1", nativeQuery = true)
        User findByUsernameNative(String username);
        ```

#### Projection

Đôi khi, bạn không cần tải toàn bộ entity với tất cả các cột, mà chỉ cần một vài thuộc tính. Việc này giúp giảm lượng dữ liệu truyền từ database về ứng dụng, tăng hiệu năng.

*   **Interface-based Projection:**
    1.  Định nghĩa một interface với các phương thức `get...()` tương ứng với các thuộc tính bạn muốn lấy.
    2.  Khai báo phương thức repository trả về interface này.
    ```java
    // 1. Định nghĩa Projection
    interface UserSummary {
        String getUsername();
        String getFullName();
    }
    
    // 2. Sử dụng trong Repository
    List<UserSummary> findByStatus(UserStatus status);
    ```
    Spring Data sẽ tự động tạo ra câu lệnh `SELECT u.username, u.full_name FROM users u ...` và tạo proxy object implement interface đó.

### 2. Ví dụ minh họa

**Tình huống:** Mở rộng `UserRepository` để thêm các query phức tạp.

```java
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.data.jpa.repository.Modifying;
import org.springframework.data.jpa.repository.Query;
import org.springframework.data.repository.query.Param;
import org.springframework.stereotype.Repository;

import java.util.List;

@Repository
public interface UserRepository extends JpaRepository<User, Long> {

    // --- Derived Queries ---
    
    // Tìm user theo username, bỏ qua trường hợp chữ hoa/thường
    User findByUsernameIgnoreCase(String username);
    
    // Tìm tất cả user có status ACTIVE và sắp xếp theo ngày tạo giảm dần
    List<User> findByStatusOrderByCreatedAtDesc(User.UserStatus status);

    // --- @Query với JPQL ---
    
    @Query("SELECT u FROM User u WHERE u.fullName LIKE %:keyword%")
    List<User> findByFullNameKeyword(@Param("keyword") String keyword);

    // --- Phân trang ---

    // Tìm user theo status và trả về kết quả dưới dạng trang
    Page<User> findByStatus(User.UserStatus status, Pageable pageable);

    // --- Projection ---
    
    interface UserSummary {
        Long getId();
        String getUsername();
    }
    
    @Query("SELECT u FROM User u")
    List<UserSummary> findAllSummaries(Sort sort);
    
    // --- Query cập nhật ---
    
    @Modifying // Bắt buộc cho các query UPDATE, DELETE
    @Transactional // Bắt buộc cho các query thay đổi dữ liệu
    @Query("UPDATE User u SET u.status = :newStatus WHERE u.status = :oldStatus")
    int updateStatusForUsers(@Param("newStatus") User.UserStatus newStatus, @Param("oldStatus") User.UserStatus oldStatus);
}

// --- Cách sử dụng trong Service hoặc CommandLineRunner ---
// ...
// Phân trang: Lấy trang đầu tiên, 5 user mỗi trang, sắp xếp theo fullName
Pageable firstPageWithFiveUsers = PageRequest.of(0, 5, Sort.by("fullName").ascending());
Page<User> userPage = userRepository.findByStatus(User.UserStatus.ACTIVE, firstPageWithFiveUsers);

System.out.println("Tổng số user: " + userPage.getTotalElements());
System.out.println("Tổng số trang: " + userPage.getTotalPages());
userPage.getContent().forEach(System.out::println); // Lấy danh sách user của trang hiện tại
```
*   **`@Modifying`:** Khi bạn dùng `@Query` để thực hiện câu lệnh `UPDATE` hoặc `DELETE`, bạn bắt buộc phải thêm annotation này.
*   **Lưu ý (Phỏng vấn!):** Các query `@Modifying` sẽ **bỏ qua Persistence Context**. Nó thực thi trực tiếp trên database. Điều này có nghĩa là các entity đang được quản lý trong PC có thể trở nên lỗi thời. Cần phải cẩn thận khi sử dụng.

### 3. Mini Exercise

1.  Trong `UserRepository`, hãy viết một **derived query** để tìm tất cả các `User` có `failedLoginAttempts` lớn hơn một giá trị cho trước VÀ thuộc một `status` cụ thể.
2.  Phương thức nên nhận 2 tham số: `int attemptCount` và `UserStatus status`.
3.  Hãy thử gọi phương thức này để kiểm tra.

### 4. Quiz Question

**Câu hỏi:** Phương thức repository `List<User> findFirst5ByStatusOrderByNameAsc(UserStatus status);` sẽ được Spring Data JPA dịch thành hành động gì?

a) Lấy tất cả user có status cho trước, sắp xếp theo tên, sau đó trả về 5 user đầu tiên từ danh sách kết quả trong bộ nhớ.
b) Lấy 5 user đầu tiên trong bảng, sau đó lọc ra những user có status phù hợp.
c) Tạo ra một câu lệnh SQL với `WHERE status = ?`, `ORDER BY name ASC`, và `LIMIT 5` (hoặc tương đương) để chỉ lấy đúng 5 bản ghi từ database.
d) Gây ra lỗi vì tên phương thức quá dài và phức tạp.

---
**Đáp án:** **c) Tạo ra một câu lệnh SQL với `WHERE status = ?`, `ORDER BY name ASC`, và `LIMIT 5` (hoặc tương đương) để chỉ lấy đúng 5 bản ghi từ database.**

**Giải thích:** Spring Data rất thông minh trong việc tối ưu hóa. Các từ khóa như `Top` và `First` trong tên phương thức sẽ được dịch thành các mệnh đề giới hạn số lượng kết quả (`LIMIT`, `TOP`, `FETCH FIRST N ROWS ONLY`...) ngay trong câu lệnh SQL gửi xuống database. Điều này đảm bảo hiệu năng tối ưu bằng cách chỉ lấy lượng dữ liệu cần thiết.

## Lesson 5: Transaction Management & Concurrency Control

### 1. Giải thích khái niệm chi tiết

#### `@Transactional` và cách hoạt động

Transaction (giao dịch) là một khái niệm nền tảng trong bất kỳ hệ thống làm việc với database nào. Nó đảm bảo một chuỗi các hành động tuân thủ theo các thuộc tính **ACID**:
*   **Atomicity (Tính nguyên tử):** Hoặc tất cả các hành động trong giao dịch đều thành công, hoặc không hành động nào được thực hiện cả.
*   **Consistency (Tính nhất quán):** Giao dịch đưa database từ một trạng thái hợp lệ này sang một trạng thái hợp lệ khác.
*   **Isolation (Tính cô lập):** Các giao dịch đồng thời không được ảnh hưởng lẫn nhau.
*   **Durability (Tính bền vững):** Một khi giao dịch đã được commit, kết quả của nó sẽ tồn tại vĩnh viễn, ngay cả khi hệ thống gặp sự cố.

Trong Spring, cách đơn giản và phổ biến nhất để quản lý transaction là sử dụng annotation `@Transactional`.

*   **Nó làm gì?** Bạn đặt `@Transactional` trên một phương thức (thường là ở tầng Service). Khi phương thức này được gọi, Spring sẽ tự động **bắt đầu một transaction** trước khi thực thi code của bạn.
    *   Nếu phương thức kết thúc bình thường, Spring sẽ **commit** transaction.
    *   Nếu phương thức ném ra một `RuntimeException` (hoặc `Error`), Spring sẽ **rollback** transaction, hủy bỏ mọi thay đổi đã thực hiện.
*   **Cơ chế hoạt động nội bộ (Phỏng vấn!):** Nó hoạt động dựa trên **Spring AOP (Proxy)**.
    1.  Khi Spring phát hiện một bean có phương thức được đánh dấu `@Transactional`, nó sẽ không tạo ra bean đó trực tiếp. Thay vào đó, nó tạo ra một **proxy** bao bọc bean gốc.
    2.  Khi bạn gọi phương thức `@Transactional` từ một bean khác, thực chất bạn đang gọi phương thức trên proxy.
    3.  Proxy sẽ chặn lời gọi này, bắt đầu một transaction (bằng cách tương tác với `PlatformTransactionManager`), sau đó mới gọi đến phương thức gốc trên bean thật.
    4.  Sau khi phương thức gốc kết thúc, proxy sẽ commit hoặc rollback transaction tùy theo kết quả.
    *   **Hệ quả:** Giống như các advice AOP khác, lời gọi `@Transactional` sẽ **không có hiệu lực** nếu được gọi từ một phương thức khác **bên trong cùng một lớp** (`this.myTransactionalMethod()`), vì lời gọi này không đi qua proxy.

#### Transaction Propagation (Sự lan truyền của Transaction)

Điều gì xảy ra khi một phương thức `@Transactional` gọi một phương thức `@Transactional` khác? `Propagation` behavior sẽ quyết định điều đó.

*   `Propagation.REQUIRED` (**Mặc định**):
    *   "Nếu đã có một transaction đang chạy, hãy tham gia vào nó. Nếu chưa có, hãy tạo một transaction mới."
    *   Đây là lựa chọn phổ biến nhất và phù hợp cho 95% các trường hợp. Tất cả các phương thức sẽ chạy trong cùng một transaction vật lý.

*   `Propagation.REQUIRES_NEW`:
    *   "Luôn luôn bắt đầu một transaction mới cho phương thức này. Nếu có một transaction cũ đang chạy, hãy tạm dừng nó lại."
    *   **Khi nào dùng?** Hữu ích cho các tác vụ cần phải thành công một cách độc lập, bất kể transaction bên ngoài có bị rollback hay không. Ví dụ: ghi log kiểm toán (audit log). Dù việc cập nhật đơn hàng thất bại, bạn vẫn muốn lưu lại log về hành động đó.

*   `Propagation.NESTED`:
    *   Tạo một "transaction lồng nhau" sử dụng tính năng `SAVEPOINT` của JDBC. Transaction con có thể rollback độc lập mà không ảnh hưởng đến transaction cha. Nếu transaction cha rollback, transaction con cũng sẽ bị rollback.
    *   Ít được hỗ trợ bởi các `TransactionManager`.

#### Isolation Levels (Mức độ cô lập)

Isolation quyết định mức độ một transaction được "nhìn thấy" các thay đổi chưa được commit của các transaction khác. Đây là một sự cân bằng giữa **tính nhất quán** và **hiệu năng**.

*   `READ_UNCOMMITTED`: Thấp nhất. Một transaction có thể đọc cả dữ liệu chưa được commit của transaction khác (**dirty read**). Rất nguy hiểm, hiếm khi dùng.
*   `READ_COMMITTED`: Một transaction chỉ có thể đọc dữ liệu đã được commit. Ngăn chặn dirty read. Tuy nhiên, nếu đọc cùng một dòng dữ liệu hai lần, kết quả có thể khác nhau nếu có transaction khác commit ở giữa (**non-repeatable read**). Đây là mức **mặc định của hầu hết các database** (PostgreSQL, Oracle...).
*   `REPEATABLE_READ`: Cao hơn. Đảm bảo rằng nếu bạn đọc cùng một dòng dữ liệu nhiều lần trong cùng một transaction, bạn sẽ luôn nhận được kết quả giống nhau. Ngăn chặn non-repeatable read. Tuy nhiên, nếu bạn thực hiện một query theo khoảng (range query), bạn có thể thấy các "hàng ma" (**phantom read**) xuất hiện nếu có transaction khác chèn thêm dữ liệu. Đây là mức **mặc định của MySQL**.
*   `SERIALIZABLE`: Cao nhất. Đảm bảo tính cô lập hoàn toàn, như thể các transaction đang chạy tuần tự. Ngăn chặn mọi vấn đề nhưng **ảnh hưởng nghiêm trọng đến hiệu năng** do sử dụng cơ chế khóa (locking) rất chặt chẽ.

#### Rollback Rules và `readOnly`

*   **Rollback Rules:** Mặc định, Spring chỉ rollback khi có `RuntimeException` hoặc `Error`. Các `Checked Exception` sẽ **không** gây rollback.
    *   Bạn có thể tùy chỉnh hành vi này: `@Transactional(rollbackFor = {IOException.class, MyCustomException.class})`.
*   **`@Transactional(readOnly = true)` (Tối ưu hóa quan trọng!):**
    *   **Nó làm gì?** Đánh dấu một transaction chỉ dành cho việc đọc dữ liệu.
    *   **Lợi ích (Phỏng vấn!):**
        1.  **Tối ưu hóa ở tầng JDBC Driver:** Driver có thể thực hiện một số tối ưu hóa khi biết rằng không có thao tác ghi nào.
        2.  **Tối ưu hóa ở tầng Hibernate:** Hibernate sẽ bỏ qua cơ chế **dirty checking**. Nó không cần phải theo dõi và so sánh trạng thái của các entity, giúp giảm đáng kể gánh nặng xử lý và bộ nhớ, đặc biệt khi làm việc với nhiều entity.
    *   **Best Practice:** Luôn luôn sử dụng `readOnly = true` cho tất cả các phương thức chỉ đọc (ví dụ: các phương thức `get`, `find`, `search`).

#### Concurrency Control (Kiểm soát tương tranh)

Khi nhiều người dùng cùng lúc sửa một bản ghi, làm sao để tránh mất mát dữ liệu?

*   **Pessimistic Locking (Khóa bi quan):**
    *   **Tư tưởng:** "Tôi cho rằng sẽ có xung đột, vì vậy tôi sẽ khóa bản ghi lại ngay khi tôi đọc nó, không cho ai khác sửa cho đến khi tôi xong việc."
    *   **Cách hoạt động:** Sử dụng các cơ chế khóa của database (`SELECT ... FOR UPDATE`). Transaction khác sẽ bị block (chờ) khi cố gắng truy cập bản ghi đã bị khóa.
    *   **Ưu điểm:** Đảm bảo an toàn dữ liệu tuyệt đối.
    *   **Nhược điểm:** Giảm đáng kể hiệu năng và khả năng chịu tải của hệ thống.

*   **Optimistic Locking (Khóa lạc quan):**
    *   **Tư tưởng:** "Tôi cho rằng xung đột hiếm khi xảy ra. Tôi sẽ cứ thực hiện thay đổi, nhưng trước khi commit, tôi sẽ kiểm tra xem có ai khác đã sửa bản ghi này trong lúc tôi đang làm việc không."
    *   **Cách hoạt động:** Thêm một cột `version` vào bảng (`@Version` trong entity).
        1.  User A đọc bản ghi, version = 1.
        2.  User B đọc cùng bản ghi, version = 1.
        3.  User B sửa và lưu lại. Câu lệnh `UPDATE` sẽ có dạng: `UPDATE ... SET ..., version = 2 WHERE id = ? AND version = 1`. Thành công. Version trong DB giờ là 2.
        4.  User A sửa và lưu lại. Câu lệnh `UPDATE` của User A cũng sẽ là: `UPDATE ... SET ..., version = 2 WHERE id = ? AND version = 1`. Câu lệnh này sẽ không cập nhật được hàng nào cả (vì `version` trong DB đã là 2).
        5.  Hibernate phát hiện không có hàng nào được cập nhật, nó sẽ ném ra `ObjectOptimisticLockingFailureException`. Ứng dụng sẽ phải bắt lỗi này và thông báo cho User A.
    *   **Ưu điểm:** Hiệu năng cao, không block database.
    *   **Best Practice:** Đây là lựa chọn mặc định và phù hợp cho hầu hết các ứng dụng web.

### 2. Ví dụ minh họa

**Tình huống:** Chuyển tiền giữa hai tài khoản. Thao tác này phải có tính nguyên tử.

```java
// Account.java
@Entity
public class Account {
    @Id private Long id;
    private BigDecimal balance;
    
    @Version // Bật Optimistic Locking
    private Long version;
    
    // ...
}

// AccountRepository extends JpaRepository<Account, Long>

// BankService.java
@Service
public class BankService {
    @Autowired private AccountRepository accountRepository;

    @Transactional // Mặc định là REQUIRED, READ_COMMITTED
    public void transfer(Long fromId, Long toId, BigDecimal amount) {
        Account fromAccount = accountRepository.findById(fromId)
                .orElseThrow(() -> new RuntimeException("Tài khoản nguồn không tồn tại"));
        Account toAccount = accountRepository.findById(toId)
                .orElseThrow(() -> new RuntimeException("Tài khoản đích không tồn tại"));

        if (fromAccount.getBalance().compareTo(amount) < 0) {
            throw new RuntimeException("Số dư không đủ"); // Ném RuntimeException sẽ gây rollback
        }
        
        fromAccount.setBalance(fromAccount.getBalance().subtract(amount));
        toAccount.setBalance(toAccount.getBalance().add(amount));
        
        // Không cần gọi save() vì có dirty checking
        
        // Nếu có lỗi xảy ra ở đây, ví dụ mất điện, transaction sẽ rollback.
        // Cả hai tài khoản sẽ trở về trạng thái ban đầu.
    }
    
    @Transactional(readOnly = true)
    public BigDecimal getBalance(Long accountId) {
        // Tối ưu hóa cho việc đọc
        return accountRepository.findById(accountId).get().getBalance();
    }
    
    @Transactional(propagation = Propagation.REQUIRES_NEW)
    public void logTransfer(String message) {
        // Logic ghi log vào bảng khác.
        // Transaction này sẽ commit ngay cả khi transaction transfer() bị rollback.
    }
}
```

### 3. Mini Exercise

1.  Trong `BankService`, tạo một phương thức public mới tên là `transferWithLogging(Long fromId, Long toId, BigDecimal amount)`.
2.  Bên trong phương thức này, hãy gọi hai phương thức `this.transfer(...)` và `this.logTransfer(...)`.
3.  Đặt `@Transactional` trên phương thức `transferWithLogging`.
4.  Thử nghiệm kịch bản khi `transfer()` ném ra exception "Số dư không đủ". Dựa vào kiến thức về AOP Proxy và Propagation, hãy dự đoán xem log có được lưu lại không và giải thích tại sao.

### 4. Quiz Question

**Câu hỏi:** Bạn đặt `@Transactional(readOnly = true)` trên một phương thức service. Bên trong phương thức đó, bạn vô tình gọi `userRepository.save(someUser)`. Điều gì sẽ xảy ra?

a) Ứng dụng sẽ ném ra exception ngay lập tức vì bạn đang cố ghi trong một transaction chỉ đọc.
b) Thao tác `save` sẽ được thực hiện thành công, `readOnly=true` chỉ là một gợi ý và không có tính bắt buộc.
c) Hibernate sẽ bỏ qua lệnh `save`, không có gì xảy ra và không có lỗi.
d) Tùy thuộc vào cấu hình của database, một số database sẽ cho phép ghi, một số sẽ báo lỗi.

---
**Đáp án:** **a) Ứng dụng sẽ ném ra exception ngay lập tức vì bạn đang cố ghi trong một transaction chỉ đọc.**

**Giải thích:** Khi `readOnly=true` được thiết lập, Hibernate sẽ đặt session ở chế độ "flush mode" là `FlushModeType.MANUAL` và cờ "read-only" của kết nối JDBC có thể được bật. Khi bạn cố gắng thực hiện một thao tác ghi (`save`, `update`, `delete`), Hibernate sẽ phát hiện sự không nhất quán này và thường ném ra một exception (ví dụ: `InvalidDataAccessApiUsageException` hoặc tương tự) để báo cho bạn biết rằng bạn đang vi phạm hợp đồng "chỉ đọc".

## Lesson 6: Performance Tuning & Optimization

### 1. Giải thích khái niệm chi tiết

Viết code chạy đúng chỉ là bước đầu. Viết code chạy **nhanh** và **hiệu quả** trong môi trường chịu tải cao mới là mục tiêu của một lập trình viên chuyên nghiệp. Hibernate là một công cụ mạnh mẽ, nhưng nếu dùng sai cách, nó có thể trở thành "cỗ máy tạo query chậm".

#### N+1 Query Problem và các giải pháp

Đây là vấn đề hiệu năng phổ biến và nghiêm trọng nhất khi làm việc với ORM. Chúng ta đã nhắc đến nó ở Lesson 3, bây giờ sẽ đi sâu vào các giải pháp.

*   **Nhắc lại vấn đề:** Khi bạn tải một danh sách N entity, sau đó truy cập một collection LAZY của từng entity trong một vòng lặp, bạn sẽ kích hoạt thêm N câu query nữa. Tổng cộng là 1 + N query.

*   **Giải pháp 1: `JOIN FETCH` trong JPQL:**
    *   **Cách làm:** Sử dụng `JOIN FETCH` trong câu lệnh `@Query` để báo cho Hibernate rằng "hãy tải entity cha VÀ cả collection con liên quan trong cùng một câu SELECT duy nhất".
    *   ```java
        @Query("SELECT a FROM Author a JOIN FETCH a.books WHERE a.id = :id")
        Author findByIdWithBooks(@Param("id") Long id);
        ```
    *   **Ưu điểm:** Giải quyết triệt để vấn đề, rất hiệu quả.
    *   **Nhược điểm:**
        *   Tạo ra **Tích Descartes (Cartesian Product)**. Nếu một `Author` có 5 `Book`, kết quả trả về từ database sẽ là 5 hàng bị lặp lại dữ liệu của `Author`. Hibernate sẽ xử lý để gom chúng lại thành một đối tượng `Author` duy nhất, nhưng việc truyền dữ liệu thừa có thể là một vấn đề.
        *   **Không thể kết hợp với phân trang (`Pageable`)** một cách hiệu quả trên collection (`-ToMany`). Hibernate sẽ cảnh báo và thực hiện phân trang trong bộ nhớ, điều này rất nguy hiểm với tập dữ liệu lớn.

*   **Giải pháp 2: `@EntityGraph`:**
    *   **Cách làm:** Một cách khai báo "kế hoạch tải dữ liệu" (fetch plan) một cách linh hoạt hơn `JOIN FETCH`. Bạn định nghĩa những thuộc tính nào cần được EAGER fetch cho một query cụ thể mà không cần sửa câu lệnh JPQL.
    *   ```java
        @Entity
        @NamedEntityGraph(name = "Author.withBooks", attributeNodes = @NamedAttributeNode("books"))
        public class Author { ... }

        // Trong Repository
        @EntityGraph(value = "Author.withBooks")
        Optional<Author> findById(Long id);
        ```
    *   **Ưu điểm:** Tách biệt định nghĩa query khỏi kế hoạch fetch. Có thể tái sử dụng.
    *   **Nhược điểm:** Về cơ bản, nó vẫn thực hiện `LEFT JOIN FETCH` ở hậu trường và gặp các vấn đề tương tự như `JOIN FETCH`.

*   **Giải pháp 3: Batch Fetching (Giải pháp tốt nhất cho N+1 khi có phân trang):**
    *   **Cách làm:** Đây là một cấu hình của Hibernate, không phải là một câu query. Bạn đánh dấu collection với `@BatchSize`.
    *   ```java
        // Trong class Author
        @OneToMany(...)
        @BatchSize(size = 10)
        private List<Book> books;
        ```
    *   **Cơ chế hoạt động (Phỏng vấn!):**
        1.  Hibernate thực hiện query đầu tiên để tải danh sách các `Author` (ví dụ, tải 30 `Author` của một trang).
        2.  Khi bạn truy cập `books` của `Author` **đầu tiên**, Hibernate thấy rằng nó cần tải `books`.
        3.  Thay vì chỉ tải `books` cho `Author` đó, nó sẽ lấy ID của **10** `Author` tiếp theo (vì `BatchSize=10`) và thực hiện một câu query thứ hai duy nhất với mệnh đề `IN`: `SELECT * FROM book WHERE author_id IN (?, ?, ?, ..., ?)` (10 ID).
        4.  Các lần truy cập `books` của 9 `Author` tiếp theo sẽ không cần query nữa vì dữ liệu đã có sẵn trong session.
    *   **Ưu điểm:** Giải quyết N+1 problem thành (1 + N/batch_size) query. **Hoạt động hoàn hảo với phân trang**. Không tạo ra Cartesian Product.

#### Caching trong Hibernate

Hibernate cung cấp hai cấp độ cache để giảm số lần truy cập database.

1.  **First-Level Cache (L1 Cache - Cache Cấp 1):**
    *   **Nó là gì?** Chính là **Persistence Context (Session Cache)**.
    *   **Phạm vi:** Gắn liền với một `Session` hoặc một `EntityManager` (tức là trong phạm vi một transaction).
    *   **Cách hoạt động:** Khi bạn gọi `session.get(User.class, 1L)`, Hibernate sẽ kiểm tra L1 cache trước. Nếu entity đã có, nó sẽ trả về ngay mà không cần query DB. Nếu chưa có, nó sẽ query DB, đặt entity vào L1 cache, rồi trả về. Mọi lời gọi `get` tiếp theo cho cùng ID trong cùng transaction sẽ được hưởng lợi từ cache này.
    *   **Tính năng:** Luôn được bật, không thể tắt. Đảm bảo tính nhất quán của đối tượng trong một transaction.

2.  **Second-Level Cache (L2 Cache - Cache Cấp 2):**
    *   **Nó là gì?** Là một cache tùy chọn, có thể được chia sẻ giữa nhiều session/transaction khác nhau.
    *   **Phạm vi:** Gắn liền với `SessionFactory` (toàn bộ ứng dụng).
    *   **Khi nào dùng?** Hữu ích cho các dữ liệu **ít thay đổi nhưng được đọc thường xuyên** (read-mostly data), ví dụ: danh mục quốc gia, các loại sản phẩm...
    *   **Cách kích hoạt:**
        1.  Thêm dependency cho một nhà cung cấp L2 cache (ví dụ: `ehcache`, `infinispan`).
        2.  Bật L2 cache trong `application.properties`: `spring.jpa.properties.hibernate.cache.use_second_level_cache=true`
        3.  Chỉ định nhà cung cấp: `spring.jpa.properties.hibernate.cache.region.factory_class=...`
        4.  Đánh dấu entity bạn muốn cache bằng `@Cacheable` và chỉ định chiến lược truy cập cache (ví dụ: `@Cache(usage = CacheConcurrencyStrategy.READ_ONLY)`).
    *   **Cảnh báo:** Quản lý L2 cache phức tạp, đặc biệt trong môi trường cluster. Cần hiểu rõ về chiến lược cache và nguy cơ dữ liệu bị lỗi thời (stale data).

#### Connection Pooling (HikariCP)

*   **Vấn đề:** Việc tạo và hủy kết nối đến database là một thao tác rất tốn kém.
*   **Giải pháp:** Connection Pool là một "bể chứa" các kết nối database đã được tạo sẵn. Khi ứng dụng cần một kết nối, nó sẽ "mượn" một cái từ pool. Sau khi dùng xong, nó "trả" kết nối về pool thay vì đóng nó.
*   **HikariCP:** Spring Boot 2.x trở đi sử dụng HikariCP làm connection pool mặc định. Nó được biết đến với hiệu năng cực cao và độ tin cậy.
*   **Cấu hình:** Bạn có thể tinh chỉnh các thông số quan trọng trong `application.properties`:
    ```properties
    # Số lượng kết nối tối đa trong pool
    spring.datasource.hikari.maximum-pool-size=20
    # Số lượng kết nối tối thiểu luôn được duy trì
    spring.datasource.hikari.minimum-idle=5
    # Thời gian chờ tối đa để lấy một kết nối từ pool (ms)
    spring.datasource.hikari.connection-timeout=30000
    ```

### 2. Ví dụ minh họa

**Tình huống:** Kích hoạt L2 Cache cho một entity `Country` (dữ liệu ít thay đổi).

**1. Thêm Dependency Ehcache:**
```xml
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-jcache</artifactId>
</dependency>
<dependency>
    <groupId>org.ehcache</groupId>
    <artifactId>ehcache</artifactId>
</dependency>
```

**2. Cấu hình `application.properties`:**
```properties
# Bật L2 Cache
spring.jpa.properties.hibernate.cache.use_second_level_cache=true
# Chỉ định nhà cung cấp JCache (Ehcache 3)
spring.jpa.properties.javax.persistence.sharedCache.mode=ENABLE_SELECTIVE
spring.jpa.properties.hibernate.cache.region.factory_class=org.hibernate.cache.jcache.JCacheRegionFactory
```

**3. Tạo file cấu hình Ehcache `src/main/resources/ehcache.xml`:**
```xml
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns="http://www.ehcache.org/v3"
        xsi:schemaLocation="http://www.ehcache.org/v3 http://www.ehcache.org/schema/ehcache-core-3.0.xsd">

    <cache alias="com.example.demo.entity.Country">
        <key-type>java.lang.Long</key-type>
        <value-type>com.example.demo.entity.Country</value-type>
        <resources>
            <heap unit="entries">200</heap>
        </resources>
    </cache>
</config>
```

**4. Đánh dấu Entity:**
```java
@Entity
@Cacheable // Báo cho Hibernate rằng entity này có thể được cache L2
@Cache(usage = CacheConcurrencyStrategy.READ_ONLY) // Chiến lược: chỉ đọc
public class Country {
    @Id private Long id;
    private String name;
    // ...
}
```

**5. Kiểm tra:**
Viết một bài test hoặc `CommandLineRunner` gọi `countryRepository.findById(1L)` hai lần trong hai transaction khác nhau. Bật log SQL của Hibernate (`spring.jpa.show-sql=true`). Bạn sẽ thấy câu lệnh `SELECT` chỉ được thực thi ở lần gọi đầu tiên. Lần thứ hai, dữ liệu được lấy thẳng từ L2 cache.

### 3. Mini Exercise

1.  Trong dự án của bạn, hãy tìm một quan hệ `@OneToMany` hoặc `@ManyToMany` đang gây ra N+1 problem.
2.  Áp dụng giải pháp `@BatchSize(size = 5)` cho collection đó.
3.  Viết một bài test hoặc một đoạn code để tải một danh sách các entity cha (ít nhất 10).
4.  Bật log SQL và quan sát. Thay vì thấy 1 + N query, bạn sẽ thấy bao nhiêu query được thực thi? (Đáp án: 1 + 10/5 = 3 query).

### 4. Quiz Question

**Câu hỏi:** Bạn đang phải tối ưu một trang web hiển thị danh sách 100 bài post, mỗi bài post hiển thị tên tác giả. Quan hệ giữa `Post` và `Author` là `@ManyToOne`. Hiện tại, nó đang gây ra N+1 problem. Giải pháp nào sau đây là **kém hiệu quả nhất**?

a) Dùng `@Query` với `JOIN FETCH` để tải `Post` và `Author` cùng lúc.
b) Thay đổi `FetchType` của quan hệ `@ManyToOne` từ `EAGER` (mặc định) thành `LAZY`.
c) Kích hoạt L2 Cache cho entity `Author`.
d) Dùng `@BatchSize` trên entity `Author`.

---
**Đáp án:** **b) Thay đổi `FetchType` của quan hệ `@ManyToOne` từ `EAGER` (mặc định) thành `LAZY`.**

**Giải thích:**
*   (a) `JOIN FETCH` sẽ giải quyết N+1 bằng 1 query duy nhất. Rất hiệu quả.
*   (c) Nếu dữ liệu `Author` ít thay đổi, L2 cache sẽ giúp các lần tải trang sau không cần query `Author` nữa. Hiệu quả.
*   (d) `@BatchSize` trên entity `Author` (cần thêm `@Proxy(lazy=false)` và một số cấu hình khác) hoặc `@BatchSize` trên collection `posts` của `Author` nếu bạn truy vấn ngược lại, cũng là một cách giải quyết N+1. Hiệu quả.
*   (b) `FetchType.EAGER` cho `@ManyToOne` chính là **nguyên nhân** của N+1 trong kịch bản này (1 query cho 100 Post, sau đó là 100 query riêng lẻ để lấy `Author` cho mỗi `Post` nếu L1 cache miss). Chuyển sang `LAZY` không giải quyết được vấn đề, mà chỉ **trì hoãn** nó. Ngay khi bạn gọi `post.getAuthor()` trong vòng lặp, N+1 query vẫn sẽ xảy ra. Do đó, đây là giải pháp vô dụng trong trường hợp này.

## Lesson 7: Advanced JPA Features

### 1. Giải thích khái niệm chi tiết

Sau khi đã nắm vững các khái niệm cơ bản, chúng ta sẽ khám phá các tính năng nâng cao của JPA và Hibernate giúp giải quyết các bài toán phức tạp và viết code gọn gàng hơn.

#### Auditing (Kiểm toán)

Trong hầu hết các ứng dụng, chúng ta cần theo dõi xem ai đã tạo/sửa một bản ghi và vào lúc nào. Thay vì phải gán các giá trị `createdAt`, `updatedAt`... bằng tay trong tầng service, Spring Data JPA có thể tự động hóa hoàn toàn việc này.

*   **Cách kích hoạt:**
    1.  Thêm annotation `@EnableJpaAuditing` vào lớp `@Configuration` chính của bạn.
    2.  Tạo một bean implement `AuditorAware<T>`. Bean này có nhiệm vụ cung cấp "người dùng hiện tại" (ví dụ, lấy từ `SecurityContextHolder` của Spring Security).
*   **Các Annotation:**
    *   `@CreatedDate`: Đánh dấu một trường để lưu thời gian tạo.
    *   `@LastModifiedDate`: Đánh dấu một trường để lưu thời gian cập nhật cuối cùng.
    *   `@CreatedBy`: Đánh dấu một trường để lưu người tạo.
    *   `@LastModifiedBy`: Đánh dấu một trường để lưu người cập nhật cuối cùng.
*   **Best Practice:** Gom các trường auditing này vào một lớp cha trừu tượng và cho các entity kế thừa.

#### `@Embeddable` và `@Embedded`

Đôi khi, một nhóm các thuộc tính có thể được tái sử dụng ở nhiều entity khác nhau. Ví dụ, một địa chỉ (`Address`) bao gồm `street`, `city`, `zipCode` có thể xuất hiện trong cả `User` và `Company`.

*   **Vấn đề:** Lặp lại các trường này ở nhiều entity làm code cồng kềnh.
*   **Giải pháp:**
    1.  Tạo một lớp `Address` và đánh dấu nó với `@Embeddable`. Lớp này không phải là một entity (không có `@Id`, không có bảng riêng).
    2.  Trong entity `User`, khai báo một trường `private Address address;` và đánh dấu nó với `@Embedded`.
*   **Cơ chế hoạt động:** Hibernate sẽ "làm phẳng" các thuộc tính của lớp `@Embeddable` thành các cột trong bảng của entity chứa nó (bảng `users` sẽ có các cột `street`, `city`, `zipCode`). Điều này giúp tổ chức code tốt hơn mà không cần tạo thêm bảng.

#### Inheritance Mapping (Ánh xạ kế thừa)

Làm thế nào để ánh xạ cấu trúc kế thừa của Java (`class Manager extends Employee`) vào các bảng database quan hệ? JPA cung cấp 3 chiến lược chính:

1.  **`SINGLE_TABLE` (Mặc định):**
    *   **Mô tả:** Tất cả các lớp trong cây kế thừa được lưu vào **một bảng duy nhất**.
    *   Hibernate sẽ tự động thêm một cột gọi là **discriminator column** (ví dụ: `DTYPE`) để phân biệt loại đối tượng (`'Manager'`, `'Employee'`).
    *   **Ưu điểm:** Hiệu năng cao nhất vì không cần JOIN.
    *   **Nhược điểm:** Các cột của lớp con phải `nullable`. Gây lãng phí không gian nếu các lớp con có nhiều thuộc tính khác nhau.

2.  **`JOINED`:**
    *   **Mô tả:** Mỗi lớp trong cây kế thừa có một bảng riêng. Bảng của lớp con chỉ chứa các thuộc tính của riêng nó và một khóa ngoại tham chiếu đến khóa chính của bảng lớp cha.
    *   **Ưu điểm:** Thiết kế database chuẩn hóa, không có cột `nullable` không cần thiết.
    *   **Nhược điểm:** Cần phải `JOIN` nhiều bảng khi truy vấn, có thể ảnh hưởng đến hiệu năng.

3.  **`TABLE_PER_CLASS`:**
    *   **Mô tả:** Mỗi lớp con có một bảng riêng, và bảng này chứa **tất cả các thuộc tính** (cả của cha và của con).
    *   **Ưu điểm:** Đơn giản.
    *   **Nhược điểm:** Không thể sử dụng các ràng buộc khóa ngoại một cách hiệu quả. Các query đa hình (polymorphic query, ví dụ `SELECT e FROM Employee e`) yêu cầu `UNION` trên nhiều bảng, hiệu năng rất kém. **Hiếm khi được sử dụng.**

#### Soft Delete (Xóa mềm)

Trong nhiều hệ thống, bạn không muốn xóa hẳn dữ liệu khỏi database, mà chỉ muốn "đánh dấu" nó là đã bị xóa. Đây gọi là xóa mềm.

*   **Cách làm truyền thống:** Thêm một cột `deleted` (boolean) và luôn thêm `WHERE deleted = false` vào mọi câu query. Rất dễ quên và gây lỗi.
*   **Cách của Hibernate:**
    1.  Thêm một trường `private boolean deleted = false;` vào entity.
    2.  Sử dụng hai annotation của Hibernate:
        *   `@SQLDelete(sql = "UPDATE my_table SET deleted = true WHERE id = ?")`: Ghi đè lệnh `DELETE` mặc định. Khi bạn gọi `repository.delete()`, Hibernate sẽ thực thi câu lệnh `UPDATE` này thay vì `DELETE`.
        *   `@Where(clause = "deleted = false")`: Tự động thêm mệnh đề `WHERE` này vào **mọi câu lệnh SELECT** mà Hibernate tạo ra cho entity này.

#### Specification API

Derived queries và `@Query` rất hữu ích, nhưng chúng là tĩnh. Điều gì xảy ra khi bạn cần xây dựng các query động dựa trên nhiều tiêu chí tìm kiếm mà người dùng có thể cung cấp hoặc không (ví dụ: một màn hình tìm kiếm sản phẩm phức tạp)?

*   **Vấn đề:** Dùng if/else để nối chuỗi JPQL rất rối và không an toàn.
*   **Giải pháp: Specification API.**
    *   Nó cho phép bạn xây dựng các "mảnh" của mệnh đề `WHERE` một cách lập trình, hướng đối tượng và an toàn về kiểu.
    *   **Cách làm:**
        1.  Repository của bạn phải kế thừa từ `JpaSpecificationExecutor<T>`.
        2.  Tạo một lớp chứa các phương thức tĩnh, mỗi phương thức trả về một `Specification<T>`.
        3.  Trong service, bạn kết hợp các specification này bằng các phương thức `and()`, `or()`.
        4.  Gọi `repository.findAll(specification)`.

### 2. Ví dụ minh họa

**Tình huống:** Xây dựng chức năng tìm kiếm `User` động theo `username` và `status`.

**1. Cho `UserRepository` kế thừa `JpaSpecificationExecutor`:**
```java
@Repository
public interface UserRepository extends JpaRepository<User, Long>, JpaSpecificationExecutor<User> {
}
```

**2. Tạo lớp `UserSpecification`:**
```java
import org.springframework.data.jpa.domain.Specification;
import javax.persistence.criteria.Predicate;
import java.util.ArrayList;
import java.util.List;

public class UserSpecifications {

    public static Specification<User> findByCriteria(String username, User.UserStatus status) {
        // Trả về một lambda implement interface Specification
        return (root, query, criteriaBuilder) -> {
            
            // root: Đại diện cho entity User, dùng để truy cập các thuộc tính
            // query: Đại diện cho câu query tổng thể
            // criteriaBuilder: Dùng để xây dựng các biểu thức điều kiện (Predicate)
            
            List<Predicate> predicates = new ArrayList<>();
            
            if (username != null && !username.isEmpty()) {
                // Thêm điều kiện: u.username LIKE '%username%'
                predicates.add(criteriaBuilder.like(root.get("username"), "%" + username + "%"));
            }
            
            if (status != null) {
                // Thêm điều kiện: u.status = status
                predicates.add(criteriaBuilder.equal(root.get("status"), status));
            }
            
            // Kết hợp tất cả các điều kiện bằng phép AND
            return criteriaBuilder.and(predicates.toArray(new Predicate[0]));
        };
    }
}
```

**3. Sử dụng trong Service:**
```java
@Service
public class UserService {
    @Autowired private UserRepository userRepository;

    public List<User> searchUsers(String username, User.UserStatus status) {
        Specification<User> spec = UserSpecifications.findByCriteria(username, status);
        return userRepository.findAll(spec);
    }
}

// --- Cách gọi ---
// searchUsers(null, UserStatus.ACTIVE) -> Chỉ tìm theo status
// searchUsers("john", null) -> Chỉ tìm theo username
// searchUsers("john", UserStatus.ACTIVE) -> Tìm theo cả hai
```
*   **Lợi ích:** Code trong service rất sạch sẽ. Logic xây dựng query được đóng gói hoàn toàn trong lớp Specification, dễ dàng tái sử dụng và mở rộng với các điều kiện `OR`, `JOIN` phức tạp.

### 3. Mini Exercise

1.  Áp dụng **Soft Delete** cho entity `User`.
2.  Thêm một trường `private boolean deleted = Boolean.FALSE;`.
3.  Thêm annotation `@SQLDelete` và `@Where` thích hợp.
4.  Viết một bài test `@DataJpaTest`:
    *   Tạo và lưu một user.
    *   Gọi `userRepository.deleteById()` cho user đó.
    *   Dùng `userRepository.findById()` để kiểm tra, kết quả phải là `Optional.empty()` (vì `@Where` đã được áp dụng).
    *   Dùng một câu native query để kiểm tra trực tiếp trong DB, bản ghi phải vẫn tồn tại với cột `deleted = true`.

### 4. Quiz Question

**Câu hỏi:** Bạn đang sử dụng chiến lược ánh xạ kế thừa `SINGLE_TABLE` cho `Employee` và `Manager`. Nếu bạn thực hiện query `SELECT count(e) FROM Employee e`, Hibernate sẽ tạo ra câu lệnh SQL như thế nào?

a) `SELECT count(*) FROM employee UNION SELECT count(*) FROM manager`
b) `SELECT count(*) FROM employee JOIN manager ON ...`
c) `SELECT count(*) FROM employee`
d) `SELECT count(*) FROM employee WHERE DTYPE IN ('Employee', 'Manager')`

---
**Đáp án:** **c) `SELECT count(*) FROM employee`**. Hoặc **d) `SELECT count(*) FROM employee WHERE DTYPE IN ('Employee', 'Manager')`** nếu có các lớp con khác. Tuy nhiên, nếu chỉ có hai lớp này thì (c) là câu trả lời phổ biến nhất.

**Giải thích:**
*   Với `SINGLE_TABLE`, tất cả các đối tượng (cả cha và con) đều nằm trong cùng một bảng (ví dụ `employee`). Câu lệnh JPQL `FROM Employee` được hiểu là "lấy tất cả các bản ghi trong bảng đại diện cho `Employee` và các lớp con của nó".
*   Nếu không có lớp con nào khác kế thừa `Employee`, thì câu query `SELECT count(*) FROM employee` là đủ.
*   Nếu có các lớp khác cũng được lưu trong bảng này (ví dụ `Intern`), Hibernate có thể tạo ra mệnh đề `WHERE DTYPE IN (...)` để đảm bảo chỉ đếm các loại `Employee` và `Manager`.
*   Cả (a) và (b) đều là hành vi của các chiến lược khác (`TABLE_PER_CLASS` và `JOINED`).

### 8. Testing Data Layer

*(Phần này được tích hợp từ các bài trước và tóm tắt lại theo yêu cầu)*

#### `@DataJpaTest`

Đây là "slice test" chuyên dụng cho tầng persistence.
*   **Nó làm gì:**
    *   Tự động cấu hình một in-memory database (thường là H2).
    *   Chỉ quét và tạo các bean `@Entity` và `@Repository`. Nó sẽ **không** tải `@Service` hay `@Controller`.
    *   Tự động bọc mỗi phương thức test trong một transaction và **rollback** sau khi kết thúc.
*   **Tại sao lại Rollback?** Đảm bảo mỗi bài test chạy trên một môi trường dữ liệu sạch, không bị ảnh hưởng bởi các bài test khác.
*   **Lưu ý:** Vì có rollback, bạn có thể không thấy dữ liệu trong H2 Console nếu đặt breakpoint. Để debug, bạn có thể thêm `@Rollback(false)` vào phương thức test, nhưng đừng commit code này.

#### Kiểm tra Transaction
```java
@Test
public void whenTransferFails_thenShouldRollback() {
    // Sắp xếp dữ liệu ban đầu
    // ...
    
    // Kiểm tra rằng một exception sẽ được ném ra
    assertThrows(RuntimeException.class, () -> {
        bankService.transfer(fromId, toId, tooMuchAmount);
    });
    
    // Tải lại các tài khoản từ DB SAU KHI transaction đã rollback
    Account fromAccountAfter = accountRepository.findById(fromId).get();
    
    // Xác nhận rằng số dư không thay đổi
    assertThat(fromAccountAfter.getBalance()).isEqualTo(initialBalance);
}
```

---

## Tổng kết chương: Spring Data JPA và Hibernate

### 1. Bảng tổng hợp các khái niệm chính

| Khái niệm / Annotation | Vai trò và Mô tả | Khi nên dùng / Best Practice | Hạn chế / Lưu ý |
| :--- | :--- | :--- | :--- |
| **JPA vs. Hibernate** | JPA là đặc tả (chuẩn), Hibernate là bản triển khai (thư viện thực thi). | Luôn code theo chuẩn JPA để ứng dụng không bị phụ thuộc. Hiểu Hibernate để tối ưu và debug. | Hibernate cung cấp nhiều tính năng mở rộng ngoài JPA (ví dụ `@SQLDelete`). |
| **`@Entity`** | Đánh dấu một lớp Java là một đối tượng được JPA quản lý, ánh xạ tới một bảng DB. | Lớp Entity nên là POJO, có constructor không tham số, và `@Id`. | |
| **Entity Lifecycle** | Các trạng thái của entity: `Transient` -> `Persistent` -> `Detached` -> `Removed`. | Hiểu rõ vòng đời là chìa khóa để debug và tối ưu. Thay đổi trên entity `Persistent` sẽ tự động được cập nhật. | |
| **Persistence Context** | Cache cấp 1 (Session cache), gắn liền với một `EntityManager`. Quản lý các entity ở trạng thái `Persistent`. | Là cơ chế đằng sau `dirty checking` và giúp giảm query trong một transaction. | Entity `Detached` (ngoài PC) sẽ không được theo dõi thay đổi. |
| **`@ManyToOne` / `@OneToMany`** | Định nghĩa quan hệ phổ biến nhất. `ManyToOne` là bên sở hữu (owning side), chứa khóa ngoại. | Luôn dùng `mappedBy` ở phía `@OneToMany`. **Luôn ưu tiên `FetchType.LAZY`**. | Quản lý cả hai phía của quan hệ một cách nhất quán (dùng helper method). |
| **`FetchType.LAZY` vs `EAGER`** | `LAZY`: Tải khi cần. `EAGER`: Tải cùng lúc. | **Mặc định là `LAZY` cho tất cả các quan hệ.** Chỉ dùng `EAGER` khi bạn chắc chắn 100% luôn cần dữ liệu đó. | `LAZY` có thể gây `LazyInitializationException` và N+1 problem. |
| **N+1 Query Problem** | 1 query lấy danh sách cha, sau đó N query lấy dữ liệu con cho mỗi cha. | Xảy ra khi lặp qua một collection `LAZY`. Là "sát thủ" hiệu năng. | Khắc phục bằng `JOIN FETCH`, `@EntityGraph`, hoặc `@BatchSize`. |
| **`JpaRepository`** | Interface của Spring Data, tự động cung cấp các phương thức CRUD, phân trang, sắp xếp. | Kế thừa interface này thay vì tự viết DAO để giảm boilerplate code. | |
| **Derived Queries** | Spring Data tự sinh query từ tên phương thức (ví dụ `findByName(...)`). | Rất tiện lợi cho các query đơn giản và giúp code sạch sẽ. | Không phù hợp cho các query phức tạp, khi đó nên dùng `@Query`. |
| **`@Query` (JPQL)** | Tự viết câu lệnh truy vấn bằng JPQL (ngôn ngữ hướng đối tượng, độc lập DB). | Dùng cho các query phức tạp. **Ưu tiên JPQL hơn native query.** | JPQL làm việc với tên Entity và thuộc tính, không phải tên bảng và cột. |
| **`@Transactional`** | Quản lý transaction một cách khai báo. Tự động begin, commit, rollback. | Đặt ở tầng Service. **Luôn dùng `readOnly = true` cho các phương thức chỉ đọc.** | Hoạt động dựa trên AOP Proxy, không có hiệu lực khi gọi nội bộ (`this.method()`). |
| **Propagation & Isolation** | `Propagation`: quy tắc lồng transaction. `Isolation`: mức độ các transaction ảnh hưởng lẫn nhau. | `REQUIRED` là propagation mặc định. `READ_COMMITTED` là isolation mặc định phổ biến. | Hiểu rõ các mức isolation để tránh `dirty read`, `non-repeatable read`. |
| **Optimistic Locking (`@Version`)** | Cơ chế kiểm soát tương tranh bằng cột version, không khóa database. | **Best practice cho hầu hết ứng dụng web.** Tránh được deadlock và tăng hiệu năng so với Pessimistic Locking. | Cần phải xử lý `ObjectOptimisticLockingFailureException` ở tầng service. |
| **`@DataJpaTest`** | Slice test cho tầng persistence. Tự động cấu hình in-memory DB và rollback transaction. | Cách tốt nhất để viết unit test cho Repository. Nhanh và cô lập. | Không tải các bean `@Service` hay `@Controller`. |
| **Specification API** | Xây dựng các query động, có điều kiện một cách an toàn về kiểu và hướng đối tượng. | Dùng cho các chức năng tìm kiếm phức tạp với nhiều bộ lọc tùy chọn. | Cú pháp ban đầu hơi phức tạp nhưng rất mạnh mẽ và linh hoạt. |

---

### 2. Câu hỏi phỏng vấn toàn diện

#### Cơ bản
1.  JPA và Hibernate khác nhau như thế nào? Tại sao chúng ta cần cả hai?
2.  `EntityManager` và `Persistence Context` là gì? Mối quan hệ giữa chúng?
3.  Giải thích 4 trạng thái của một Entity (`Transient`, `Persistent`, `Detached`, `Removed`).
4.  `save()`, `persist()`, và `merge()` khác nhau như thế nào?
5.  Sự khác biệt giữa `CrudRepository` và `JpaRepository`?
6.  `FetchType.LAZY` và `FetchType.EAGER` là gì? Khi nào nên dùng loại nào?

#### Nâng cao
7.  **N+1 Query Problem là gì? Nêu ít nhất 3 cách để giải quyết và phân tích ưu/nhược điểm của mỗi cách.**
8.  **`@Transactional` hoạt động như thế nào ở bên trong? Tại sao việc gọi một phương thức `@Transactional` từ một phương thức khác trong cùng một class lại không hoạt động?**
9.  **Dirty checking là gì? Nó hoạt động như thế nào và `@Transactional(readOnly = true)` ảnh hưởng đến nó ra sao?**
10. So sánh Optimistic Locking và Pessimistic Locking. Trong một ứng dụng web thông thường, tại sao Optimistic Locking thường được ưa chuộng hơn?
11. `LazyInitializationException` xảy ra khi nào và làm thế nào để khắc phục nó? (Gợi ý: Open Session In View, `JOIN FETCH`, DTO projection).
12. Cache cấp 1 (L1) và Cache cấp 2 (L2) trong Hibernate khác nhau như thế nào về phạm vi và mục đích sử dụng?
13. Khi nào bạn sẽ sử dụng một câu native query thay vì JPQL?
14. Chiến lược sinh khóa chính `IDENTITY` và `SEQUENCE` ảnh hưởng đến hiệu năng của Hibernate Batch Insert như thế nào?
15. `@Modifying` query có ảnh hưởng gì đến Persistence Context?

---

### 3. Mini Project: Ứng dụng "Online Store"

Dự án này sẽ tổng hợp tất cả kiến thức để xây dựng một backend API nhỏ cho một cửa hàng trực tuyến.

**Các Entity:**

1.  **`Customer`**: `id`, `name`, `email`, `address` (dùng `@Embeddable`).
2.  **`Product`**: `id`, `name`, `price`, `stockQuantity`.
3.  **`CustomerOrder`** (đổi tên từ `Order` để tránh trùng từ khóa SQL): `id`, `orderDate`, `status` (enum).
4.  **`OrderDetail`**: `id`, `quantity`, `priceAtTimeOfOrder`.

**Các mối quan hệ:**
*   `Customer` --`OneToMany`--> `CustomerOrder`
*   `CustomerOrder` --`ManyToOne`--> `Customer`
*   `CustomerOrder` --`OneToMany`--> `OrderDetail`
*   `Product` --`OneToMany`--> `OrderDetail`
*   `OrderDetail` --`ManyToOne`--> `CustomerOrder`
*   `OrderDetail` --`ManyToOne`--> `Product`

**Yêu cầu chức năng:**

1.  **Entity và Repository:**
    *   Tạo tất cả các entity trên với các mối quan hệ phù hợp.
    *   Sử dụng `@Embeddable` cho `Address`.
    *   Áp dụng **Auditing** (`@CreatedDate`, `@LastModifiedDate`) cho `CustomerOrder`.
    *   Tạo các `JpaRepository` tương ứng.

2.  **Service Layer:**
    *   Tạo một `OrderService` với phương thức `placeOrder(customerId, Map<productId, quantity>)`.
    *   Phương thức này phải được bọc trong `@Transactional`.
    *   **Logic:**
        *   Kiểm tra xem `Customer` có tồn tại không.
        *   Kiểm tra xem tất cả `Product` có tồn tại và đủ hàng không.
        *   Trừ số lượng tồn kho (`stockQuantity`) của `Product`.
        *   Tạo một `CustomerOrder` mới.
        *   Tạo các `OrderDetail` tương ứng và thêm vào `CustomerOrder`.
        *   Lưu `CustomerOrder` (cascade sẽ lưu các `OrderDetail`).
        *   Nếu bất kỳ bước nào thất bại, toàn bộ giao dịch phải được rollback (ví dụ: số lượng tồn kho không được thay đổi).

3.  **Khắc phục N+1 Problem:**
    *   Tạo một phương thức trong `CustomerOrderRepository` để tìm một `CustomerOrder` theo ID.
    *   Viết một bài test `@DataJpaTest` để chứng minh rằng việc gọi phương thức trên và sau đó truy cập `orderDetails` và `product` trong `orderDetail` gây ra N+1 problem.
    *   Sửa lại phương thức repository bằng cách sử dụng **`JOIN FETCH`** để tải tất cả dữ liệu cần thiết chỉ trong một câu query.

4.  **Query động với Specification:**
    *   Tạo một `ProductSpecification` để tìm kiếm `Product` theo `name` (LIKE) và một khoảng giá (`minPrice`, `maxPrice`).
    *   Tạo một endpoint API hoặc một phương thức service sử dụng Specification này.

5.  **Testing:**
    *   Viết một bài test `@DataJpaTest` cho `OrderService.placeOrder`.
    *   Kiểm tra kịch bản thành công.
    *   Kiểm tra kịch bản thất bại (hết hàng) và xác nhận rằng transaction đã rollback (số lượng tồn kho của sản phẩm không bị thay đổi).

