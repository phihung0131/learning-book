## Lesson 1: Giới thiệu tổng quan về Spring Framework và triết lý cốt lõi

### 1. Giải thích khái niệm chi tiết

#### Vấn đề của Java truyền thống và lý do Spring ra đời

Trước khi có Spring, việc phát triển các ứng dụng doanh nghiệp (enterprise applications) bằng Java, đặc biệt là với nền tảng J2EE (nay là Jakarta EE), thường rất phức tạp và cồng kềnh. Lập trình viên phải đối mặt với các vấn đề sau:

*   **Sự kết dính cao (Tight Coupling):** Các đối tượng trong ứng dụng phụ thuộc trực tiếp lẫn nhau. Ví dụ, một lớp `UserService` có thể phải tự khởi tạo một đối tượng `UserRepository` bằng từ khóa `new`. Điều này làm cho code khó thay đổi, khó bảo trì và đặc biệt là rất khó để viết kiểm thử đơn vị (unit test).
*   **Code soạn sẵn rườm rà (Boilerplate Code):** Để thực hiện các tác vụ cơ bản như kết nối cơ sở dữ liệu (JDBC), gọi EJB (Enterprise JavaBeans), lập trình viên phải viết rất nhiều dòng code lặp đi lặp lại và xử lý ngoại lệ phức tạp.
*   **Khó kiểm thử (Difficult to Test):** Do sự kết dính cao, việc "giả lập" (mock) một đối tượng phụ thuộc để kiểm thử một lớp riêng lẻ trở nên gần như bất khả thi nếu không có những kỹ thuật phức tạp.

Spring Framework ra đời vào khoảng năm 2003 bởi Rod Johnson như một giải pháp để giải quyết những vấn đề trên. Mục tiêu của Spring là làm cho việc phát triển ứng dụng Java trở nên đơn giản, hiệu quả và linh hoạt hơn.

#### Triết lý thiết kế: Inversion of Control (IoC) & Dependency Injection (DI)

Đây là hai khái niệm nền tảng và là "trái tim" của Spring Framework.

*   **Inversion of Control (IoC - Đảo ngược điều khiển):** Đây là một nguyên lý thiết kế phần mềm. Trong lập trình truyền thống, code của bạn (ví dụ: lớp `UserService`) có quyền kiểm soát việc tạo ra và quản lý các đối tượng mà nó cần (ví dụ: `new UserRepository()`). Với IoC, quyền kiểm soát này bị "đảo ngược". Thay vì bạn chủ động tạo đối tượng, bạn giao phó trách nhiệm này cho một thực thể bên ngoài, đó chính là **Spring IoC Container**. Container sẽ đọc các chỉ dẫn cấu hình của bạn và tự động khởi tạo, cấu hình, và "lắp ráp" các đối tượng lại với nhau.

*   **Dependency Injection (DI - Tiêm phụ thuộc):** DI là một *mẫu thiết kế (design pattern)* cụ thể để hiện thực hóa nguyên lý IoC. "Dependency" (sự phụ thuộc) là một đối tượng mà đối tượng khác cần để hoạt động. Thay vì đối tượng tự đi tìm hoặc tự tạo ra dependency của nó, Spring Container sẽ "tiêm" (inject) các dependency đó vào đối tượng khi cần thiết. Điều này giúp loại bỏ sự phụ thuộc cứng (hard-coded dependencies) và làm cho các thành phần trở nên độc lập hơn.

> **Tóm lại:** IoC là *nguyên lý* (bạn giao quyền kiểm soát cho framework), còn DI là *cơ chế* cụ thể để thực hiện nguyên lý đó (framework cung cấp/tiêm phụ thuộc cho code của bạn).

#### Các module trong Spring Framework

Spring được thiết kế theo kiến trúc module hóa, cho phép bạn chỉ sử dụng những phần mình cần. Các module chính được nhóm lại như sau:

*   **Core Container:** Đây là nền tảng của Spring, cung cấp các chức năng cốt lõi.
    *   `spring-core` và `spring-beans`: Cung cấp chức năng IoC và DI cơ bản thông qua cơ chế `BeanFactory`.
    *   `spring-context`: Mở rộng `beans` và cung cấp `ApplicationContext`, một container mạnh mẽ hơn với nhiều tính năng doanh nghiệp như hỗ trợ đa ngôn ngữ, sự kiện ứng dụng.
    *   `spring-expression` (SpEL): Cung cấp ngôn ngữ biểu thức mạnh mẽ.
*   **Data Access / Integration:** Cung cấp các công cụ để làm việc với dữ liệu.
    *   `spring-jdbc`: Đơn giản hóa việc sử dụng JDBC.
    *   `spring-orm`: Tích hợp với các framework ORM phổ biến như Hibernate, JPA.
    *   `spring-tx`: Hỗ trợ quản lý giao dịch (transaction management).
*   **Web:** Cung cấp các tính năng để xây dựng ứng dụng web.
    *   `spring-web`: Cung cấp các tính năng web cơ bản.
    *   `spring-webmvc`: Chứa đựng Spring MVC, một framework để xây dựng ứng dụng web theo mô hình Model-View-Controller.
*   **AOP (Aspect-Oriented Programming):** Cung cấp khả năng lập trình hướng khía cạnh, giúp tách biệt các mối quan tâm xuyên suốt (cross-cutting concerns) như logging, security.
*   **Test:** Hỗ trợ việc viết unit test và integration test cho các ứng dụng Spring.

#### Ứng dụng thực tế của Spring

Trong các hệ thống doanh nghiệp, Spring được sử dụng để xây dựng phần backend của các ứng dụng web, microservices, xử lý dữ liệu hàng loạt (batch processing), và tích hợp hệ thống. Nhờ triết lý thiết kế của mình, Spring giúp xây dựng các hệ thống lớn, phức tạp nhưng vẫn đảm bảo tính module hóa, dễ bảo trì và dễ dàng mở rộng.

### 2. Ví dụ minh họa thực tế

Hãy xem sự khác biệt giữa cách viết code truyền thống và cách viết theo triết lý của Spring.

**Tình huống:** Chúng ta có một `NotificationService` để gửi thông báo cho người dùng, và nó cần một `EmailClient` để thực hiện việc gửi email.

**Cách 1: Lập trình Java truyền thống (Tight Coupling)**

```java
// Lớp phụ thuộc: EmailClient
public class EmailClient {
    public void sendEmail(String recipient, String message) {
        System.out.println("Đang gửi email tới " + recipient + ": " + message);
        // Logic gửi email thực tế...
    }
}

// Lớp chính: NotificationService
public class NotificationService {
    // Tự tạo dependency của chính mình -> Phụ thuộc cứng!
    private EmailClient emailClient = new EmailClient();

    public void sendNotification(String user, String message) {
        // Sử dụng dependency
        emailClient.sendEmail(user, "Thông báo: " + message);
    }
}

// Cách sử dụng
public class MainApp {
    public static void main(String[] args) {
        NotificationService notificationService = new NotificationService();
        notificationService.sendNotification("example@email.com", "Chào mừng bạn!");
    }
}
```

*   **Vấn đề:** Lớp `NotificationService` bị "buộc chặt" vào lớp `EmailClient`. Nếu sau này bạn muốn đổi sang `SmsClient` hoặc muốn thay `EmailClient` bằng một phiên bản giả (mock) để test, bạn phải sửa đổi code bên trong lớp `NotificationService`.

**Cách 2: Áp dụng triết lý Spring (Loosely Coupled với DI)**

Chúng ta sẽ sử dụng các annotation cơ bản để chỉ dẫn cho Spring Container.

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

// Đánh dấu lớp này là một "thành phần" để Spring quản lý
@Component
class EmailClient {
    public void sendEmail(String recipient, String message) {
        System.out.println("Đang gửi email tới " + recipient + ": " + message);
    }
}

// Đánh dấu lớp này cũng là một thành phần
@Component
class NotificationService {
    
    private final EmailClient emailClient;

    // Yêu cầu Spring "tiêm" (inject) một đối tượng EmailClient vào đây
    // thông qua constructor. Đây là cách làm tốt nhất (best practice).
    @Autowired
    public NotificationService(EmailClient emailClient) {
        this.emailClient = emailClient;
    }

    public void sendNotification(String user, String message) {
        emailClient.sendEmail(user, "Thông báo: " + message);
    }
}

// (Trong một ứng dụng Spring thực tế, bạn sẽ không viết hàm main như thế này
// mà sẽ khởi tạo Spring ApplicationContext. Đây chỉ là minh họa ý tưởng.)
```

*   **Điều gì xảy ra ở hậu trường?**
    1.  Khi ứng dụng Spring khởi động, nó sẽ quét các lớp và thấy `@Component` trên `EmailClient` và `NotificationService`.
    2.  Spring IoC Container sẽ tạo ra một đối tượng (gọi là *bean*) cho `EmailClient` và lưu nó vào trong "kho" của mình.
    3.  Tiếp theo, container tạo bean cho `NotificationService`. Nó thấy constructor của lớp này được đánh dấu `@Autowired` và cần một tham số kiểu `EmailClient`.
    4.  Container sẽ tìm trong "kho" của nó, thấy bean `EmailClient` đã tạo ở bước 2 và "tiêm" nó vào constructor của `NotificationService`.
    5.  Kết quả là `NotificationService` nhận được dependency mà không cần tự mình tạo ra nó. Sự phụ thuộc được đảo ngược.

### 3. Mini Exercise

1.  Hãy tưởng tượng bạn cần xây dựng một hệ thống thanh toán. Viết cấu trúc (chỉ cần khai báo lớp và phương thức) cho hai lớp: `PaymentService` và `CreditCardProcessor`.
2.  Viết hai phiên bản:
    *   **Phiên bản 1 (Truyền thống):** `PaymentService` tự tạo một instance của `CreditCardProcessor` bên trong nó.
    *   **Phiên bản 2 (Theo hướng Spring):** `PaymentService` khai báo `CreditCardProcessor` như một dependency sẽ được tiêm vào qua constructor. (Bạn có thể giả sử có các annotation `@Component` và `@Autowired`).

### 4. Quiz Question

**Câu hỏi:** Nguyên lý "Inversion of Control" (IoC) có mục đích chính là gì?

a) Làm cho ứng dụng chạy nhanh hơn bằng cách tối ưu hóa việc tạo đối tượng.
b) Đảo ngược quyền kiểm soát việc tạo và quản lý đối tượng từ code của lập trình viên sang một container bên ngoài.
c) Bắt buộc sử dụng file XML để cấu hình ứng dụng.
d) Tự động hóa việc viết các câu lệnh SQL.

## Lesson 2: Inversion of Control (IoC) và Dependency Injection (DI) chi tiết

### 1. Giải thích khái niệm chi tiết

#### Bean và IoC Container là gì?

*   **Bean:** Trong Spring, một "bean" là một đối tượng được khởi tạo, lắp ráp và quản lý bởi Spring IoC Container. Thay vì bạn tự dùng `new MyClass()`, bạn định nghĩa một bean, và Spring Container sẽ lo phần còn lại. Bean là những khối xây dựng cơ bản của bất kỳ ứng dụng Spring nào.

*   **IoC Container (hoặc Spring Container):** Đây là "bộ não" của Spring. Nhiệm vụ chính của nó là:
    1.  **Tạo và quản lý Bean:** Nó đọc các chỉ dẫn cấu hình (từ XML, annotation, hoặc Java code) để biết cần tạo những bean nào.
    2.  **Thực hiện Dependency Injection:** Nó "tiêm" các bean phụ thuộc vào những bean cần chúng.
    3.  **Quản lý vòng đời (Lifecycle):** Nó kiểm soát toàn bộ vòng đời của bean, từ lúc tạo ra cho đến lúc hủy bỏ.

#### BeanFactory và ApplicationContext

Spring cung cấp hai loại container chính:

1.  **BeanFactory:**
    *   Là interface gốc và cơ bản nhất cho IoC container.
    *   Nó cung cấp các chức năng cốt lõi của DI.
    *   Sử dụng cơ chế "lazy-loading", nghĩa là bean chỉ được tạo ra khi bạn yêu cầu nó một cách tường minh qua phương thức `getBean()`.
    *   Thường ít được sử dụng trực tiếp trong các ứng dụng hiện đại vì thiếu các tính năng nâng cao.

2.  **ApplicationContext:**
    *   Là một interface con, kế thừa tất cả chức năng của `BeanFactory`.
    *   Nó cung cấp thêm rất nhiều tính năng doanh nghiệp quan trọng, chẳng hạn như:
        *   Tích hợp với Spring AOP.
        *   Quản lý sự kiện (application events).
        *   Hỗ trợ đa ngôn ngữ (internationalization).
        *   Dễ dàng tích hợp với các dịch vụ web.
    *   Sử dụng cơ chế "eager-loading" theo mặc định, nghĩa là tất cả các bean có scope `singleton` sẽ được khởi tạo ngay khi container khởi động. Điều này giúp phát hiện lỗi cấu hình sớm hơn.
    *   **Trong 99% các dự án thực tế, bạn sẽ luôn làm việc với `ApplicationContext`.**

#### Các loại Dependency Injection

Spring hỗ trợ ba cách chính để "tiêm" một dependency vào một bean:

1.  **Constructor Injection (Tiêm qua hàm khởi tạo) - Best Practice**
    *   Các dependency được truyền vào dưới dạng tham số của constructor.
    *   **Cách hoạt động:** Spring Container sẽ tìm các bean tương ứng với kiểu dữ liệu của tham số và truyền chúng vào khi gọi constructor để tạo bean.
    *   **Ưu điểm:**
        *   **Bất biến (Immutability):** Bạn có thể khai báo các trường dependency là `final`, đảm bảo chúng không bao giờ bị thay đổi sau khi đối tượng được tạo.
        *   **Đảm bảo dependency:** Đối tượng sẽ không bao giờ được tạo ra trong trạng thái "chưa hoàn chỉnh" (thiếu dependency).
        *   **Rõ ràng:** Nhìn vào constructor, bạn biết ngay đối tượng này cần những gì để hoạt động.
        *   **Phát hiện sớm Circular Dependency:** Nếu bean A cần bean B và bean B lại cần bean A, Spring sẽ báo lỗi ngay khi khởi tạo, thay vì lỗi lúc chạy.

2.  **Setter Injection (Tiêm qua hàm setter)**
    *   Container sẽ gọi hàm `setter` của bean để tiêm dependency sau khi bean được khởi tạo bằng constructor mặc định.
    *   **Cách hoạt động:** Spring tạo bean bằng constructor không tham số, sau đó gọi các phương thức `set...()` được đánh dấu `@Autowired`.
    *   **Khi nào dùng:** Thích hợp cho các dependency *không bắt buộc* (optional). Bạn có thể cung cấp một giá trị mặc định và chỉ tiêm nếu cần.
    *   **Nhược điểm:** Đối tượng có thể tồn tại ở trạng thái chưa sẵn sàng (các dependency có thể là `null` trong một khoảng thời gian ngắn sau khi tạo).

3.  **Field Injection (Tiêm trực tiếp vào trường) - Thường không khuyến khích**
    *   Dependency được tiêm thẳng vào trường (field/property) của lớp, không cần constructor hay setter.
    *   **Cách hoạt động:** Spring sử dụng reflection để gán giá trị trực tiếp cho trường được đánh dấu `@Autowired`.
    *   **Tại sao không nên dùng:**
        *   **Che giấu dependency:** Nhìn vào lớp, bạn không biết được các dependency bắt buộc.
        *   **Khó kiểm thử (Unit Test):** Rất khó để inject các đối tượng giả (mock) vào các trường private mà không dùng reflection.
        *   **Vi phạm nguyên tắc Single Responsibility:** Dễ dàng thêm quá nhiều dependency vào một lớp vì nó quá đơn giản.

#### Các Annotation phổ biến

*   **Stereotype Annotations (Đánh dấu vai trò của bean):**
    *   `@Component`: Annotation chung nhất, đánh dấu một lớp là một bean do Spring quản lý.
    *   `@Service`: Một chuyên biệt hóa của `@Component`, dùng để đánh dấu các lớp ở tầng business logic (ví dụ: `UserService`, `OrderService`). Nó chỉ mang ý nghĩa về mặt ngữ nghĩa và giúp phân tầng ứng dụng.
    *   `@Repository`: Dùng cho các lớp ở tầng truy cập dữ liệu (Data Access Layer - DAO). Ngoài việc là một `@Component`, nó còn giúp Spring chuyển đổi các ngoại lệเฉพาะ của nền tảng (như `SQLException`) thành các ngoại lệ nhất quán của Spring (`DataAccessException`).
    *   `@Controller`: Dùng trong Spring MVC để đánh dấu các lớp xử lý request từ người dùng.

*   **Annotations cho Dependency Injection:**
    *   `@Autowired`: Đánh dấu một constructor, setter hoặc field để yêu cầu Spring tự động tiêm một bean tương thích vào đó.
    *   `@Qualifier`: Khi có nhiều bean cùng một kiểu, `@Autowired` sẽ không biết chọn bean nào. `@Qualifier("tên_bean")` được dùng để chỉ định rõ bean nào cần tiêm.
    *   `@Primary`: Cũng dùng khi có nhiều bean cùng kiểu. Bean được đánh dấu `@Primary` sẽ là lựa chọn ưu tiên mặc định nếu không có `@Qualifier` nào được chỉ định.

### 2. Ví dụ minh họa thực tế

**Tình huống:** Xây dựng một `UserService` cần `UserRepository` (để lấy dữ liệu người dùng) và `EmailService` (để gửi email chào mừng).

**Bước 1: Định nghĩa các interface và implementation**

```java
// Interface cho tầng dữ liệu
public interface UserRepository {
    String findUserById(int id);
}

// Lớp thực thi cụ thể, đánh dấu là @Repository
@Repository
class InMemoryUserRepository implements UserRepository {
    @Override
    public String findUserById(int id) {
        // Giả lập tìm user trong bộ nhớ
        return "John Doe";
    }
}

// Interface cho dịch vụ email
public interface EmailService {
    void sendWelcomeEmail(String user);
}

// Lớp thực thi cụ thể, đánh dấu là @Service
@Service("realEmailService") // Đặt tên cụ thể cho bean này
class SmtpEmailService implements EmailService {
    @Override
    public void sendWelcomeEmail(String user) {
        System.out.println("Đang gửi email chào mừng tới " + user + " qua SMTP.");
    }
}
```

**Bước 2: Xây dựng UserService và tiêm dependency qua constructor**

```java
@Service
class UserService {
    private final UserRepository userRepository;
    private final EmailService emailService;

    // Constructor Injection: Yêu cầu Spring cung cấp UserRepository và EmailService
    @Autowired
    public UserService(UserRepository userRepository, EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }

    public void registerUser(int id) {
        String user = userRepository.findUserById(id);
        System.out.println("Đã tìm thấy người dùng: " + user);
        emailService.sendWelcomeEmail(user);
    }
}
```

*   **Chuyện gì xảy ra bên trong Spring Container?**
    1.  Spring quét và tìm thấy `InMemoryUserRepository` và `SmtpEmailService`, tạo bean cho chúng.
    2.  Khi tạo bean `UserService`, nó thấy constructor yêu cầu `UserRepository` và `EmailService`.
    3.  Nó tìm trong container, thấy có một bean `InMemoryUserRepository` khớp với kiểu `UserRepository` và một bean `SmtpEmailService` khớp với kiểu `EmailService`.
    4.  Nó "tiêm" hai bean này vào constructor và hoàn tất việc tạo `UserService`.

**Bước 3: Giải quyết xung đột với `@Qualifier` và `@Primary`**

Giả sử chúng ta thêm một `EmailService` khác để dùng cho việc test.

```java
@Component("mockEmailService") // Một bean EmailService khác
class MockEmailService implements EmailService {
    @Override
    public void sendWelcomeEmail(String user) {
        System.out.println("Đang gửi email GIẢ tới " + user + ".");
    }
}
```

Bây giờ, nếu khởi động ứng dụng, Spring sẽ báo lỗi `NoUniqueBeanDefinitionException` vì nó thấy có hai bean (`realEmailService` và `mockEmailService`) cùng triển khai `EmailService` và không biết phải tiêm cái nào vào `UserService`.

**Cách giải quyết 1: Dùng `@Qualifier`**

Chỉ định rõ bean bạn muốn.

```java
@Service
class UserService {
    // ...
    @Autowired
    public UserService(UserRepository userRepository, @Qualifier("realEmailService") EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }
    // ...
}
```

**Cách giải quyết 2: Dùng `@Primary`**

Đánh dấu một bean là lựa chọn mặc định.

```java
@Service("realEmailService")
@Primary // Nếu không ai chỉ định, hãy dùng tôi!
class SmtpEmailService implements EmailService {
    // ...
}
```

Bây giờ, nếu `UserService` chỉ dùng `@Autowired` mà không có `@Qualifier`, Spring sẽ tự động chọn `SmtpEmailService` vì nó được đánh dấu là `@Primary`.

### 3. Mini Exercise

Bạn được giao nhiệm vụ xây dựng một `OrderService` (dịch vụ đặt hàng). Dịch vụ này cần hai thứ:
1.  `ProductRepository`: Để kiểm tra thông tin sản phẩm.
2.  `NotificationService`: Để gửi thông báo xác nhận đơn hàng.

**Yêu cầu:**

1.  Định nghĩa interface cho `ProductRepository` và `NotificationService`.
2.  Tạo các lớp implementation (ví dụ: `DatabaseProductRepository`, `SmsNotificationService`) và đánh dấu chúng bằng các stereotype annotation phù hợp (`@Repository`, `@Service`).
3.  Tạo lớp `OrderService`, đánh dấu nó là một `@Service` và sử dụng **Constructor Injection** để tiêm `ProductRepository` và `NotificationService` vào.

### 4. Quiz Question

**Câu hỏi:** Trong Dependency Injection, tại sao Constructor Injection thường được coi là best practice so với Field Injection?

a) Vì nó là cách duy nhất hoạt động với các trường được khai báo là `final`, giúp đảm bảo tính bất biến (immutability).
b) Vì code của nó ngắn gọn hơn so với Setter Injection.
c) Vì nó cho phép tạo ra các dependency vòng tròn (circular dependencies) một cách dễ dàng.
d) Vì nó là cách duy nhất được Spring Framework hỗ trợ.

## Lesson 3: Bean Scope và Vòng đời của Bean (Bean Lifecycle)

### 1. Giải thích khái niệm chi tiết

#### Bean Scope (Phạm vi của Bean)

Bean Scope quyết định vòng đời và khả năng hiển thị của một instance bean trong container. Nói cách khác, nó trả lời câu hỏi: "Khi nào thì tạo một instance mới của bean này?" và "Instance này tồn tại trong bao lâu?".

Spring Core định nghĩa các scope chính sau:

1.  **singleton (Mặc định):**
    *   **Mô tả:** Chỉ có **một và chỉ một** instance của bean được tạo ra cho mỗi Spring IoC container. Mỗi khi có yêu cầu (request) cho bean này, container sẽ trả về cùng một instance đó.
    *   **Cách hoạt động nội bộ:** Khi container khởi động, nó sẽ tạo ra instance cho tất cả các bean singleton, tiêm phụ thuộc, và lưu trữ trong một bộ đệm (cache, thường là một `Map`). Mọi yêu cầu sau đó chỉ đơn giản là lấy đối tượng từ cache này.
    *   **Khi nào dùng:** Hầu hết các service không trạng thái (stateless) trong ứng dụng (ví dụ: services, repositories, controllers) nên là singleton để tối ưu hiệu năng, vì không cần phải tạo lại đối tượng liên tục.
    *   **Cảnh báo (Common Mistake):** Vì chỉ có một instance được chia sẻ, bean singleton **không nên lưu trữ trạng thái cụ thể của một phiên làm việc** (ví dụ: thông tin giỏ hàng của người dùng A). Nếu bean singleton có các biến thành viên (fields) có thể thay đổi, bạn phải đảm bảo code của mình là an toàn luồng (thread-safe).

2.  **prototype:**
    *   **Mô tả:** Một instance mới của bean sẽ được tạo ra **mỗi khi có một yêu cầu** cho bean đó.
    *   **Cách hoạt động nội bộ:** Mỗi lần `getBean("myPrototypeBean")` được gọi, container sẽ thực hiện lại toàn bộ quá trình: tạo mới instance, tiêm phụ thuộc, và trả về cho bạn.
    *   **Vòng đời đặc biệt:** Spring container **không quản lý toàn bộ vòng đời** của bean prototype. Sau khi tạo và trả về bean cho client, container không còn giữ tham chiếu đến nó nữa. Do đó, các callback hủy (destruction callbacks) như `@PreDestroy` hoặc `destroyMethod` sẽ **không được gọi** cho bean prototype. Việc dọn dẹp tài nguyên (nếu có) là trách nhiệm của client code đã yêu cầu bean đó.
    *   **Khi nào dùng:** Khi bạn cần một đối tượng có trạng thái (stateful) riêng cho mỗi lần sử dụng, ví dụ như một đối tượng builder, một tiến trình xử lý công việc.

3.  **Các Scope dành cho ứng dụng Web:** (Chỉ có hiệu lực khi sử dụng `WebApplicationContext`)
    *   **request:** Một instance bean mới được tạo cho mỗi HTTP request. Khi request kết thúc, bean cũng bị hủy. Rất hữu ích để chứa dữ liệu liên quan đến request hiện tại.
    *   **session:** Một instance bean được tạo cho mỗi HTTP session của người dùng. Bean tồn tại xuyên suốt các request của cùng một người dùng cho đến khi session hết hạn. Ví dụ điển hình là giỏ hàng (shopping cart).
    *   **application:** Một instance bean duy nhất được tạo cho toàn bộ vòng đời của `ServletContext` (tương tự singleton nhưng trong ngữ cảnh web).

#### Vòng đời của Bean (Bean Lifecycle)

Đây là chu trình các sự kiện xảy ra từ khi một bean được Spring container tạo ra cho đến khi nó bị hủy. Hiểu rõ vòng đời giúp bạn can thiệp vào các giai đoạn quan trọng để thực thi logic tùy chỉnh (ví dụ: khởi tạo kết nối, dọn dẹp tài nguyên).

Vòng đời của một bean singleton trong `ApplicationContext` diễn ra theo các bước chính sau:

1.  **Instantiation (Khởi tạo):** Spring container tìm thấy định nghĩa bean và sử dụng reflection để tạo một instance của lớp đó, tương tự như gọi constructor.
2.  **Populate Properties (Tiêm phụ thuộc):** Spring xác định các dependency của bean (qua `@Autowired`, etc.) và tiêm các bean phụ thuộc vào instance vừa tạo. Tại thời điểm này, bean đã được tạo ra nhưng chưa sẵn sàng để sử dụng vì logic khởi tạo tùy chỉnh chưa chạy.
3.  **BeanNameAware's `setBeanName()`:** Nếu bean implement interface `BeanNameAware`, Spring sẽ gọi phương thức `setBeanName()` để truyền vào ID của bean. (Đây là callback nâng cao).
4.  **ApplicationContextAware's `setApplicationContext()`:** Nếu bean implement `ApplicationContextAware`, Spring sẽ gọi phương thức này và truyền vào chính `ApplicationContext`. (Nâng cao).
5.  **`BeanPostProcessor` (before initialization):** Trước khi các phương thức khởi tạo tùy chỉnh được gọi, Spring sẽ chạy phương thức `postProcessBeforeInitialization()` của tất cả các `BeanPostProcessor` đã đăng ký. Đây là cơ hội để chỉnh sửa bean trước khi nó hoàn tất.
6.  **Initialization (Khởi tạo tùy chỉnh):** Spring gọi các phương thức khởi tạo do lập trình viên định nghĩa. Có hai cách phổ biến:
    *   Phương thức được đánh dấu với annotation `@PostConstruct` (chuẩn JSR-250, được khuyến khích).
    *   Phương thức được chỉ định trong thuộc tính `initMethod` của `@Bean` hoặc XML.
        Đây là nơi lý tưởng để đặt logic cần chạy ngay sau khi tất cả dependency đã được tiêm, ví dụ như khởi tạo cache, mở kết nối tới một dịch vụ khác.
7.  **`BeanPostProcessor` (after initialization):** Sau khi các phương thức khởi tạo tùy chỉnh hoàn tất, Spring sẽ chạy phương thức `postProcessAfterInitialization()` của các `BeanPostProcessor`. Đây là nơi các "phép màu" như tạo proxy cho AOP thường xảy ra.
8.  **Bean is ready (Bean sẵn sàng sử dụng):** Tại thời điểm này, bean đã được khởi tạo hoàn chỉnh và sẵn sàng để ứng dụng sử dụng. Nó sẽ tồn tại trong cache của container.
9.  **Destruction (Hủy bỏ):** Khi `ApplicationContext` bị đóng lại (ví dụ khi ứng dụng tắt), nó sẽ quản lý việc hủy các bean singleton.
    *   Phương thức được đánh dấu với annotation `@PreDestroy` (chuẩn JSR-250, được khuyến khích).
    *   Phương thức được chỉ định trong thuộc tính `destroyMethod`.
        Đây là nơi để dọn dẹp tài nguyên, ví dụ đóng kết nối cơ sở dữ liệu, dừng các luồng nền.

### 2. Ví dụ minh họa thực tế

**Ví dụ 1: So sánh Scope Singleton và Prototype**

```java
import org.springframework.context.annotation.*;

@Configuration
@ComponentScan("com.example.scopes")
class AppConfig {}

// ---- BEANS ----
package com.example.scopes;

import org.springframework.context.annotation.Scope;
import org.springframework.stereotype.Component;

@Component // Mặc định là singleton
class SingletonService {
    public SingletonService() {
        System.out.println("SingletonService: Instance được tạo!");
    }
}

@Component
@Scope("prototype")
class PrototypeService {
    public PrototypeService() {
        System.out.println("PrototypeService: Instance MỚI được tạo!");
    }
}

// ---- MAIN APP ----
public class ScopeDemo {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);

        System.out.println("--- Lấy bean Singleton ---");
        SingletonService s1 = context.getBean(SingletonService.class);
        SingletonService s2 = context.getBean(SingletonService.class);
        System.out.println("s1 == s2 ? " + (s1 == s2)); // Sẽ in ra true
        System.out.println("Hash code s1: " + s1.hashCode());
        System.out.println("Hash code s2: " + s2.hashCode());

        System.out.println("\n--- Lấy bean Prototype ---");
        PrototypeService p1 = context.getBean(PrototypeService.class);
        PrototypeService p2 = context.getBean(PrototypeService.class);
        System.out.println("p1 == p2 ? " + (p1 == p2)); // Sẽ in ra false
        System.out.println("Hash code p1: " + p1.hashCode());
        System.out.println("Hash code p2: " + p2.hashCode());
        
        context.close();
    }
}
```

*   **Kết quả:** Bạn sẽ thấy "SingletonService: Instance được tạo!" chỉ in ra một lần khi context khởi động, trong khi "PrototypeService: Instance MỚI được tạo!" in ra mỗi lần `getBean()` được gọi.

**Ví dụ 2: Vòng đời của Bean với `@PostConstruct` và `@PreDestroy`**

```java
import javax.annotation.PostConstruct;
import javax.annotation.PreDestroy;
import org.springframework.stereotype.Component;

@Component
class LifecycleDemoBean {

    public LifecycleDemoBean() {
        System.out.println("1. Constructor: Bean đang được khởi tạo (instantiated).");
    }

    // Được gọi sau khi dependency đã được tiêm
    @PostConstruct
    public void myInit() {
        System.out.println("2. @PostConstruct: Logic khởi tạo tùy chỉnh. Các dependency đã sẵn sàng.");
        // Ví dụ: Mở kết nối database, load file cấu hình...
    }
    
    public void doWork() {
        System.out.println("3. Bean đang thực thi công việc...");
    }

    // Được gọi khi context bị đóng
    @PreDestroy
    public void myCleanup() {
        System.out.println("4. @PreDestroy: Dọn dẹp tài nguyên trước khi bean bị hủy.");
        // Ví dụ: Đóng kết nối database...
    }
}

// ---- MAIN APP ----
public class LifecycleDemo {
    public static void main(String[] args) {
        // Sử dụng try-with-resources để đảm bảo context.close() được gọi
        try (AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(LifecycleDemoBean.class)) {
            LifecycleDemoBean bean = context.getBean(LifecycleDemoBean.class);
            bean.doWork();
        } // context.close() được tự động gọi ở đây, trigger @PreDestroy
    }
}
```

*   **Kết quả:** Các thông báo sẽ được in ra theo đúng thứ tự 1, 2, 3, 4, minh họa rõ ràng các giai đoạn chính của vòng đời bean.

### 3. Mini Exercise

1.  Tạo một lớp tên `DatabaseConnectionManager`.
2.  Định nghĩa nó như một bean **singleton**.
3.  Bên trong lớp, viết một phương thức `openConnection()` được đánh dấu với `@PostConstruct`. Phương thức này sẽ in ra "CONNECTION OPENED.".
4.  Viết một phương thức `closeConnection()` được đánh dấu với `@PreDestroy`. Phương thức này sẽ in ra "CONNECTION CLOSED.".
5.  Viết một hàm `main` để khởi tạo Spring context, lấy bean này ra, và sau đó đóng context để quan sát log.

### 4. Quiz Question

**Câu hỏi:** Điều gì sẽ xảy ra nếu bạn định nghĩa một phương thức được đánh dấu `@PreDestroy` bên trong một bean có scope là `"prototype"`?

a) Spring sẽ báo lỗi `BeanCreationException` khi khởi tạo context.
b) Phương thức `@PreDestroy` sẽ được gọi ngay khi bean không còn được sử dụng.
c) Phương thức `@PreDestroy` sẽ **không bao giờ** được Spring container gọi.
d) Phương thức `@PreDestroy` chỉ được gọi nếu bean đó có lỗi xảy ra trong quá trình sử dụng.

### Đáp án Quiz Lesson 3

**Câu hỏi:** Điều gì sẽ xảy ra nếu bạn định nghĩa một phương thức được đánh dấu `@PreDestroy` bên trong một bean có scope là `"prototype"`?

**Đáp án đúng là c) Phương thức `@PreDestroy` sẽ không bao giờ được Spring container gọi.**

**Giải thích:** Spring container quản lý toàn bộ vòng đời của bean singleton. Tuy nhiên, đối với bean prototype, sau khi container tạo và giao bean cho client, nó sẽ không còn giữ tham chiếu đến bean đó nữa. Trách nhiệm hủy và dọn dẹp tài nguyên của bean prototype thuộc về client code, do đó Spring không thể gọi các callback hủy như `@PreDestroy`.

---

## Lesson 4: Cấu hình và Quản lý Bean

### 1. Giải thích khái niệm chi tiết

#### Các phương pháp cấu hình Bean

Spring cung cấp ba cách chính để định nghĩa bean và các dependency của chúng. Trong lịch sử phát triển, các phương pháp này đã tiến hóa để trở nên ngày càng linh hoạt và dễ sử dụng hơn.

1.  **XML-based Configuration (Cấu hình dựa trên XML):**
    *   Đây là cách truyền thống và sơ khai nhất. Tất cả các bean và dependency được định nghĩa trong các file XML.
    *   **Ưu điểm:** Tách biệt hoàn toàn cấu hình ra khỏi code Java. Người không phải lập trình viên (ví dụ: quản trị hệ thống) có thể thay đổi cấu hình mà không cần biên dịch lại code.
    *   **Nhược điểm:** Cồng kềnh, dài dòng, khó điều hướng (refactor) khi code thay đổi (ví dụ đổi tên lớp), và không có sự an toàn về kiểu (type safety) lúc biên dịch. Ngày nay, nó ít được sử dụng cho các dự án mới nhưng vẫn cần biết để bảo trì các hệ thống cũ.

2.  **Annotation-based Configuration (Cấu hình dựa trên Annotation):**
    *   Sử dụng các annotation như `@Component`, `@Service`, `@Autowired`... ngay trên các lớp Java. Spring sẽ tự động quét (scan) các package để tìm và đăng ký chúng thành bean.
    *   **Ưu điểm:** Gọn nhẹ, cấu hình nằm ngay bên cạnh code liên quan, dễ đọc và bảo trì.
    *   **Nhược điểm:** Cấu hình bị trộn lẫn với business logic. Nó hoạt động tốt cho các lớp do bạn viết, nhưng không thể dùng để cấu hình các lớp từ thư viện bên thứ ba (bạn không thể thêm `@Component` vào code của họ).

3.  **Java-based Configuration (Cấu hình dựa trên Java) - Phương pháp hiện đại và được khuyên dùng:**
    *   Sử dụng các lớp Java và các annotation đặc biệt (`@Configuration`, `@Bean`) để định nghĩa bean. Đây là sự kết hợp ưu điểm của hai phương pháp trên.
    *   **Ưu điểm:**
        *   **An toàn về kiểu và dễ refactor:** Vì là code Java, bạn được trình biên dịch kiểm tra lỗi và IDE hỗ trợ refactor.
        *   **Mạnh mẽ và linh hoạt:** Bạn có thể dùng logic Java (if/else, vòng lặp) để định nghĩa bean một cách có điều kiện.
        *   **Tách biệt cấu hình:** Giữ cho các lớp business logic sạch sẽ, không bị "nhiễm" các annotation cấu hình.
        *   Có thể tạo bean cho cả các lớp của bên thứ ba.

#### Các Annotation chính trong Java-based Configuration

*   `@Configuration`: Đánh dấu một lớp là một nguồn định nghĩa bean. Các lớp này tương đương với một file XML cấu hình.
    *   **Cơ chế nội bộ:** Khi Spring thấy `@Configuration`, nó sẽ tạo một proxy CGLIB cho lớp đó. Proxy này sẽ chặn các lời gọi đến phương thức `@Bean` để đảm bảo rằng các dependency giữa các bean được xử lý đúng theo scope (ví dụ: đảm bảo bean singleton chỉ được tạo một lần ngay cả khi được gọi nhiều lần).
*   `@Bean`: Đặt trên một phương thức bên trong lớp `@Configuration`. Nó báo cho Spring rằng phương thức này sẽ trả về một đối tượng cần được đăng ký làm bean trong container.
    *   Tên của phương thức sẽ là ID của bean theo mặc định.
    *   Bạn có toàn quyền kiểm soát việc khởi tạo và cấu hình đối tượng trước khi trả về.
*   `@ComponentScan`: Dùng cùng với `@Configuration`, nó chỉ cho Spring biết cần quét package nào để tìm các lớp được đánh dấu `@Component` (và các chuyên biệt hóa của nó) để tự động đăng ký chúng.
*   `@Import`: Cho phép nhập các định nghĩa bean từ một lớp `@Configuration` khác. Rất hữu ích để module hóa cấu hình.
*   `@PropertySource`: Dùng để chỉ định vị trí của một file properties (ví dụ: `application.properties`). Các thuộc tính trong file này sẽ được nạp vào `Environment` của Spring.

#### Externalized Configuration (Ngoại hóa cấu hình)

Đây là một best practice quan trọng: không bao giờ hard-code các giá trị cấu hình (URL database, mật khẩu, API key) trực tiếp trong code. Thay vào đó, hãy đưa chúng ra các file bên ngoài.

*   `@Value`: Annotation dùng để tiêm các giá trị từ file properties vào các trường của bean. Cú pháp phổ biến là `@Value("${tên.thuộc.tính}")`.
*   `Environment`: Là một interface trong Spring đại diện cho môi trường mà ứng dụng đang chạy. Nó cung cấp một cách thống nhất để truy cập các thuộc tính từ nhiều nguồn khác nhau (file properties, biến môi trường hệ thống, tham số dòng lệnh...).
*   **Profiles (`@Profile`):** Profiles là một cơ chế cực kỳ mạnh mẽ để định nghĩa các nhóm bean khác nhau và chỉ kích hoạt một nhóm tại một thời điểm. Điều này rất cần thiết để quản lý các môi trường khác nhau như `dev`, `test`, và `prod`.
    *   Bạn có thể đánh dấu một `@Bean` hoặc một `@Configuration` với `@Profile("tên_profile")`.
    *   Bean/Configuration đó sẽ chỉ được tạo nếu profile tương ứng được kích hoạt.
    *   Bạn có thể kích hoạt profile bằng nhiều cách, phổ biến là qua `spring.profiles.active` trong file `application.properties` hoặc biến môi trường.

### 2. Ví dụ minh họa thực tế

**Tình huống:** Chúng ta cần cấu hình một `DataSource` để kết nối tới database. Thông tin kết nối sẽ khác nhau giữa môi trường `dev` (dùng database H2 trong bộ nhớ) và `prod` (dùng database PostgreSQL thật).

**Bước 1: Tạo các file properties**

*   `src/main/resources/application-dev.properties`
    ```properties
    db.driver=org.h2.Driver
    db.url=jdbc:h2:mem:testdb
    db.user=sa
    db.password=
    ```
*   `src/main/resources/application-prod.properties`
    ```properties
    db.driver=org.postgresql.Driver
    db.url=jdbc:postgresql://prod-server:5432/mydb
    db.user=prod_user
    db.password=secret_password
}
```
*   `src/main/resources/application.properties`
    ```properties
    # Kích hoạt profile mặc định là dev
    spring.profiles.active=dev
    ```

**Bước 2: Tạo lớp cấu hình Java-based**

```java
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.*;
import javax.sql.DataSource;
import org.springframework.jdbc.datasource.DriverManagerDataSource;

@Configuration
// Dựa vào profile đang active mà nạp file properties tương ứng
@PropertySource("classpath:application-${spring.profiles.active}.properties")
public class DataSourceConfig {

    @Value("${db.driver}")
    private String driverClassName;

    @Value("${db.url}")
    private String url;

    @Value("${db.user}")
    private String username;

    @Value("${db.password}")
    private String password;

    private DataSource createDataSource() {
        DriverManagerDataSource dataSource = new DriverManagerDataSource();
        dataSource.setDriverClassName(driverClassName);
        dataSource.setUrl(url);
        dataSource.setUsername(username);
        dataSource.setPassword(password);
        return dataSource;
    }

    @Bean("dataSource")
    @Profile("dev") // Bean này chỉ được tạo nếu profile 'dev' được kích hoạt
    public DataSource devDataSource() {
        System.out.println("Đang tạo DEV DataSource...");
        return createDataSource();
    }

    @Bean("dataSource")
    @Profile("prod") // Bean này chỉ được tạo nếu profile 'prod' được kích hoạt
    public DataSource prodDataSource() {
        System.out.println("Đang tạo PRODUCTION DataSource...");
        // Có thể thêm cấu hình connection pool cho môi trường production ở đây
        return createDataSource();
    }
}
```

*   **Chuyện gì xảy ra bên trong Spring Container?**
    1.  Spring đọc `application.properties` và thấy `spring.profiles.active=dev`.
    2.  Nó nạp lớp `@Configuration` là `DataSourceConfig`.
    3.  Nó thấy `@PropertySource("classpath:application-${spring.profiles.active}.properties")`. Nó thay thế `${spring.profiles.active}` bằng `dev`, và nạp file `application-dev.properties` vào `Environment`.
    4.  Khi tạo các bean trong `DataSourceConfig`, nó thấy hai bean `dataSource`.
    5.  Bean đầu tiên có `@Profile("dev")`. Vì profile `dev` đang active, bean này được chọn để tạo.
    6.  Bean thứ hai có `@Profile("prod")`. Vì profile `prod` không active, bean này bị bỏ qua.
    7.  Spring tiêm các giá trị từ `Environment` vào các trường `driverClassName`, `url`... thông qua `@Value`.
    8.  Phương thức `devDataSource()` được gọi và một `DataSource` được cấu hình cho môi trường dev được tạo ra.

**Bước 3: Sử dụng bean đã cấu hình**

```java
@Component
class MyRepository {
    private final DataSource dataSource;

    @Autowired
    public MyRepository(DataSource dataSource) {
        this.dataSource = dataSource;
        System.out.println("MyRepository đã được tiêm với DataSource: " + dataSource);
    }
}
```

### 3. Mini Exercise

1.  Tạo một file `mail.properties` với hai thuộc tính: `mail.smtp.host=smtp.example.com` và `mail.smtp.port=587`.
2.  Tạo một lớp cấu hình `MailConfig` được đánh dấu `@Configuration`.
3.  Sử dụng `@PropertySource` để nạp file `mail.properties`.
4.  Bên trong `MailConfig`, định nghĩa một bean tên là `smtpMailSender` bằng phương thức có annotation `@Bean`. Phương thức này sẽ trả về một đối tượng `SmtpMailSender` (bạn có thể tự tạo lớp này với hai thuộc tính `host` và `port`).
5.  Sử dụng `@Value` để tiêm các giá trị `mail.smtp.host` và `mail.smtp.port` từ file properties vào các thuộc tính của bean `smtpMailSender` trước khi trả về.

### 4. Quiz Question

**Câu hỏi:** Đâu là sự khác biệt chính giữa việc sử dụng `@Component` trên một lớp và định nghĩa một phương thức với `@Bean` bên trong một lớp `@Configuration`?

a) Hoàn toàn không có sự khác biệt, chúng có thể thay thế cho nhau trong mọi trường hợp.
b) `@Component` dùng cho các lớp bạn tự viết, trong khi `@Bean` chủ yếu dùng để cấu hình các đối tượng từ thư viện của bên thứ ba.
c) `@Component` dựa vào cơ chế quét classpath để tự động phát hiện, trong khi `@Bean` cho phép bạn kiểm soát một cách tường minh và lập trình logic khởi tạo của đối tượng.
d) `@Bean` luôn tạo ra bean singleton, còn `@Component` mặc định tạo ra bean prototype.

---
**Đáp án:** **c) `@Component` dựa vào cơ chế quét classpath để tự động phát hiện, trong khi `@Bean` cho phép bạn kiểm soát một cách tường minh và lập trình logic khởi tạo của đối tượng.**

**Giải thích:**
*   `@Component` (và các biến thể) là một cách khai báo: "Này Spring, lớp này là một bean, hãy tự quản lý nó". Spring sẽ tự động tìm và tạo nó.
*   `@Bean` là một cách ra lệnh: "Này Spring, hãy chạy phương thức này, lấy đối tượng nó trả về và đăng ký đối tượng đó làm bean". Điều này cho phép bạn viết code Java để tùy chỉnh hoàn toàn việc tạo và cấu hình đối tượng, đặc biệt hữu ích cho các lớp mà bạn không sở hữu code (ví dụ: `DataSource`, `ObjectMapper` từ thư viện bên ngoài), như đã đề cập trong phương án (b). Phương án (b) là một hệ quả và là trường hợp sử dụng phổ biến của (c).

## Lesson 5: Spring AOP (Aspect-Oriented Programming - Lập trình hướng khía cạnh)

### 1. Giải thích khái niệm chi tiết

#### Cross-Cutting Concerns (Các mối quan tâm xuyên suốt) là gì?

Trong một ứng dụng, ngoài business logic chính (ví dụ: xử lý đơn hàng, đăng ký người dùng), có những chức năng phụ trợ cần được áp dụng ở nhiều nơi. Các chức năng này được gọi là "cross-cutting concerns". Ví dụ phổ biến bao gồm:

*   **Logging:** Ghi lại log khi một phương thức bắt đầu và kết thúc.
*   **Transaction Management:** Bắt đầu một giao dịch trước khi thực hiện thao tác database và commit hoặc rollback sau khi hoàn thành.
*   **Security:** Kiểm tra quyền của người dùng trước khi cho phép thực thi một phương thức.
*   **Caching:** Kiểm tra cache trước khi chạy phương thức, nếu có dữ liệu thì trả về, nếu không thì chạy phương thức rồi lưu kết quả vào cache.

Nếu không có AOP, bạn sẽ phải lặp lại code cho các chức năng này ở khắp mọi nơi, vi phạm nguyên tắc DRY (Don't Repeat Yourself) và làm cho code rất khó bảo trì. AOP ra đời để giải quyết vấn đề này bằng cách cho phép bạn **tách biệt** các cross-cutting concerns ra khỏi business logic và **áp dụng** chúng một cách linh hoạt.

#### Các thuật ngữ cốt lõi của AOP

Để hiểu AOP, bạn cần nắm vững các thuật ngữ sau:

*   **Aspect (Khía cạnh):** Là một module (thường là một class) chứa đựng logic của một cross-cutting concern. Ví dụ, bạn có thể có một `LoggingAspect` để xử lý tất cả mọi thứ liên quan đến logging. Trong Spring, một aspect là một class được đánh dấu với `@Aspect`.
*   **Join Point (Điểm nối):** Là một điểm cụ thể trong quá trình thực thi của chương trình. Trong Spring AOP, một join point **luôn là sự kiện thực thi một phương thức (method execution)**.
*   **Advice (Hành động):** Là hành động cụ thể mà Aspect sẽ thực hiện tại một Join Point. Đây chính là phần logic của cross-cutting concern (ví dụ: "ghi log", "bắt đầu transaction").
*   **Pointcut (Điểm cắt):** Là một biểu thức (expression) để **lọc và chọn** ra các Join Point mà Advice sẽ được áp dụng. Ví dụ: "áp dụng advice này cho tất cả các phương thức public trong package `com.example.service`". Pointcut quyết định "ở đâu" và "khi nào" advice sẽ chạy.
*   **Weaving (Dệt):** Là quá trình liên kết Aspect với các đối tượng mục tiêu (target objects) để tạo ra một đối tượng mới đã được "tăng cường" (advised object), thường gọi là **proxy**. Spring AOP thực hiện weaving tại thời điểm chạy (runtime).

#### Các loại Advice

Spring AOP cung cấp 5 loại advice, được định nghĩa bằng các annotation:

1.  `@Before`: Advice được thực thi **trước khi** join point (phương thức) chạy.
2.  `@AfterReturning`: Advice được thực thi **sau khi** join point hoàn thành bình thường (không ném ra exception).
3.  `@AfterThrowing`: Advice được thực thi **sau khi** join point ném ra một exception.
4.  `@After`: Advice được thực thi **sau khi** join point kết thúc, bất kể nó kết thúc bình thường hay ném ra exception (tương tự khối `finally`).
5.  `@Around`: Đây là loại advice mạnh mẽ nhất. Nó "bao bọc" xung quanh join point. Nó có thể thực thi logic trước và sau khi phương thức chạy, và thậm chí có thể quyết định **không cho phương thức gốc chạy**. Nó cũng có thể sửa đổi các tham số đầu vào hoặc kết quả trả về.

#### Cơ chế hoạt động nội bộ: JDK Dynamic Proxy vs. CGLIB

Spring AOP không sửa đổi bytecode của class gốc. Thay vào đó, nó tạo ra một đối tượng **proxy** tại runtime để bao bọc đối tượng gốc (target bean).

1.  **JDK Dynamic Proxy:**
    *   Là cơ chế mặc định của Java để tạo proxy.
    *   **Yêu cầu:** Class mục tiêu phải implement ít nhất một interface.
    *   **Cách hoạt động:** Spring tạo ra một class proxy mới tại runtime, class này implement cùng interface với class mục tiêu. Khi bạn gọi một phương thức trên proxy, proxy sẽ thực thi các advice liên quan, sau đó mới ủy quyền (delegate) lời gọi đến phương thức tương ứng trên đối tượng gốc.
2.  **CGLIB (Code Generation Library):**
    *   Là một thư viện bên thứ ba.
    *   **Yêu cầu:** Không yêu cầu interface. Tuy nhiên, class mục tiêu và các phương thức của nó không được khai báo là `final`.
    *   **Cách hoạt động:** CGLIB tạo ra một class con (subclass) của class mục tiêu tại runtime. Nó ghi đè (override) các phương thức của lớp cha để chèn logic của advice vào trước khi gọi phương thức gốc thông qua `super()`.
    *   Trong các phiên bản Spring Boot hiện đại, CGLIB thường được dùng làm mặc định.

**Lỗi thường gặp và giới hạn của Proxy:**
Một trong những cạm bẫy lớn nhất của AOP dựa trên proxy là **vấn đề tự gọi (self-invocation)**. Nếu một phương thức bên trong một bean gọi một phương thức khác của *chính bean đó* (ví dụ `this.anotherMethod()`), lời gọi này sẽ **không đi qua proxy**. Nó là một lời gọi trực tiếp bên trong đối tượng gốc, do đó các advice sẽ không được áp dụng. Đây là một câu hỏi phỏng vấn rất phổ biến.

### 2. Ví dụ minh họa thực tế

**Tình huống:** Chúng ta muốn đo thời gian thực thi của một phương thức trong `PaymentService`.

**Bước 1: Bật hỗ trợ AOP và tạo Service mục tiêu**

```java
// Trong lớp Configuration chính
import org.springframework.context.annotation.*;

@Configuration
@ComponentScan("com.example.aop")
@EnableAspectJAutoProxy // Bật tính năng tự động tạo proxy cho AOP
public class AppConfig {}

// ---- Lớp Service mục tiêu ----
package com.example.aop;

import org.springframework.stereotype.Service;

@Service
public class PaymentService {
    // Phương thức chúng ta muốn đo thời gian
    public void processPayment(double amount) {
        System.out.println("Bắt đầu xử lý thanh toán: " + amount);
        try {
            // Giả lập một công việc tốn thời gian
            Thread.sleep(1500); 
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Hoàn tất xử lý thanh toán.");
    }
}
```

**Bước 2: Tạo Aspect để đo thời gian**

```java
package com.example.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect     // Đánh dấu đây là một Aspect
@Component  // Để Spring quản lý nó như một bean
public class PerformanceLoggingAspect {

    // @Around là advice bao bọc phương thức
    // Biểu thức pointcut bên trong chỉ định nơi áp dụng advice:
    // "execution(* com.example.aop.PaymentService.*(..))" có nghĩa là:
    // - execution: áp dụng cho việc thực thi phương thức
    // - *: bất kỳ kiểu trả về nào
    // - com.example.aop.PaymentService.*: bất kỳ phương thức nào trong lớp PaymentService
    // - (..): bất kỳ số lượng tham số nào
    @Around("execution(* com.example.aop.PaymentService.*(..))")
    public Object measureExecutionTime(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();

        // Gọi phương thức gốc, nếu không gọi dòng này, phương thức gốc sẽ không bao giờ chạy
        Object result = joinPoint.proceed(); 

        long endTime = System.currentTimeMillis();
        long duration = endTime - startTime;
        
        System.out.println("--- [AOP LOG] ---");
        System.out.println("Phương thức: " + joinPoint.getSignature().getName());
        System.out.println("Thời gian thực thi: " + duration + " ms");
        System.out.println("-----------------");

        return result;
    }
}
```

**Bước 3: Chạy ứng dụng**

```java
public class AopDemo {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        
        PaymentService paymentService = context.getBean(PaymentService.class);
        paymentService.processPayment(99.99);

        context.close();
    }
}
```

*   **Kết quả đầu ra:**
    ```
    Bắt đầu xử lý thanh toán: 99.99
    Hoàn tất xử lý thanh toán.
    --- [AOP LOG] ---
    Phương thức: processPayment
    Thời gian thực thi: 1502 ms
    -----------------
    ```
*   **Chuyện gì đã xảy ra?**
    1.  Spring tạo bean `PaymentService` gốc.
    2.  Nó thấy bean `PerformanceLoggingAspect` và pointcut khớp với các phương thức của `PaymentService`.
    3.  Nó tạo một **proxy** (dùng CGLIB vì `PaymentService` không có interface) bao bọc `PaymentService` gốc.
    4.  Khi `context.getBean(PaymentService.class)` được gọi, Spring trả về đối tượng proxy.
    5.  Khi bạn gọi `paymentService.processPayment()`, thực chất bạn đang gọi phương thức trên proxy.
    6.  Proxy sẽ gọi advice `@Around` là `measureExecutionTime`.
    7.  Advice này ghi lại thời gian bắt đầu, sau đó gọi `joinPoint.proceed()` để thực thi phương thức `processPayment` gốc.
    8.  Sau khi phương thức gốc chạy xong, advice ghi lại thời gian kết thúc và in ra kết quả.

### 3. Mini Exercise

1.  Trong `PerformanceLoggingAspect` đã tạo ở trên, hãy thêm một advice mới.
2.  Sử dụng advice `@Before` để in ra tên phương thức và các tham số của nó **trước khi** phương thức được thực thi.
3.  **Gợi ý:**
    *   Tạo một phương thức mới được đánh dấu `@Before` với cùng một biểu thức pointcut.
    *   Phương thức này sẽ nhận một tham số kiểu `JoinPoint`.
    *   Sử dụng `joinPoint.getSignature().getName()` để lấy tên phương thức và `joinPoint.getArgs()` để lấy một mảng các tham số.

### 4. Quiz Question

**Câu hỏi:** Giả sử trong lớp `PaymentService`, bạn có hai phương thức: `processPayment()` và `verifyPayment()`. Bên trong `processPayment()` có một lời gọi đến `this.verifyPayment()`. Cả hai phương thức đều khớp với pointcut của AOP. Khi bạn gọi `paymentService.processPayment()` từ bên ngoài, advice sẽ được áp dụng cho những phương thức nào?

a) Cả `processPayment()` và `verifyPayment()`.
b) Chỉ `processPayment()`.
c) Chỉ `verifyPayment()`.
d) Không phương thức nào cả.

---
**Đáp án:** **b) Chỉ `processPayment()`.**

**Giải thích:** Lời gọi `paymentService.processPayment()` từ bên ngoài sẽ đi qua proxy, vì vậy advice sẽ được kích hoạt. Tuy nhiên, lời gọi `this.verifyPayment()` từ bên trong `processPayment()` là một lời gọi trực tiếp đến đối tượng gốc (target object), nó không đi qua proxy. Do đó, advice sẽ không được áp dụng cho `verifyPayment()`. Đây chính là ví dụ kinh điển về vấn đề "self-invocation".

## Lesson 6: Application Events và Resource Handling

### 1. Giải thích khái niệm chi tiết

#### Lập trình hướng sự kiện (Event-Driven Programming) trong Spring

Trong các ứng dụng lớn, các component thường cần tương tác với nhau. Ví dụ, sau khi một người dùng đăng ký (`UserService`), hệ thống cần gửi email chào mừng (`EmailService`) và cập nhật số liệu phân tích (`AnalyticsService`).

*   **Cách tiếp cận truyền thống (kết dính cao):** `UserService` sẽ phải gọi trực tiếp `EmailService` và `AnalyticsService`. Điều này tạo ra sự phụ thuộc cứng. Nếu sau này bạn muốn thêm một hành động nữa (ví dụ: gửi tin nhắn SMS), bạn phải sửa đổi code của `UserService`.

*   **Cách tiếp cận hướng sự kiện (kết dính lỏng):** `UserService` không cần biết đến `EmailService` hay `AnalyticsService`. Nó chỉ cần thực hiện một công việc: **phát đi một sự kiện** (event) có tên là "UserRegisteredEvent". Bất kỳ component nào trong ứng dụng quan tâm đến sự kiện này có thể "lắng nghe" và phản ứng lại. `UserService` không cần quan tâm có bao nhiêu người nghe và họ là ai.

Cách tiếp cận này giúp các component được **tách rời (decoupled)**, làm cho hệ thống trở nên linh hoạt, dễ bảo trì và dễ mở rộng hơn.

Spring cung cấp một framework sự kiện đơn giản nhưng mạnh mẽ với 3 thành phần chính:

1.  **Event (Sự kiện):** Là một lớp Java đơn giản, kế thừa từ `ApplicationEvent`. Nó chứa thông tin về sự việc đã xảy ra. Ví dụ: `UserRegisteredEvent` sẽ chứa thông tin về người dùng vừa đăng ký.
2.  **Publisher (Người phát sự kiện):** Là bất kỳ bean nào tiêm `ApplicationEventPublisher` vào. Nó sử dụng phương thức `publishEvent()` để phát đi các sự kiện.
3.  **Listener (Người nghe sự kiện):** Là một phương thức trong bất kỳ bean nào được đánh dấu với annotation `@EventListener`. Phương thức này sẽ được tự động gọi khi một sự kiện mà nó quan tâm được phát đi.

**Cơ chế hoạt động nội bộ:**
Khi `applicationEventPublisher.publishEvent(myEvent)` được gọi, `ApplicationContext` (hoạt động như một bộ điều phối sự kiện) sẽ duyệt qua tất cả các bean của nó. Nó tìm các phương thức được đánh dấu `@EventListener` có tham số đầu vào khớp với kiểu của `myEvent`. Sau đó, nó sẽ gọi tuần tự từng phương thức listener này và truyền đối tượng `myEvent` vào.
**Lưu ý quan trọng:** Theo mặc định, việc xử lý sự kiện trong Spring là **đồng bộ (synchronous)**. Điều này có nghĩa là luồng của người phát sự kiện sẽ bị chặn lại và đợi cho đến khi tất cả người nghe xử lý xong sự kiện. Để xử lý bất đồng bộ, bạn cần sử dụng `@Async`.

#### Trừu tượng hóa Tài nguyên (Resource Abstraction)

Việc truy cập các tài nguyên như file trong Java có thể phức tạp vì vị trí của chúng rất đa dạng: nằm trên hệ thống file, trong classpath (bên trong file JAR), hoặc trên một URL. Code xử lý cho mỗi trường hợp lại khác nhau.

Spring giải quyết vấn đề này bằng cách cung cấp một interface chung là `Resource`. Interface này trừu tượng hóa các nguồn tài nguyên khác nhau, cho phép bạn làm việc với chúng một cách thống nhất.

*   **ResourceLoader:** `ApplicationContext` chính là một `ResourceLoader`. Bạn có thể tiêm nó vào bất kỳ bean nào để có khả năng nạp tài nguyên.
*   **Các tiền tố (Prefixes) phổ biến:** Khi yêu cầu một tài nguyên, bạn sử dụng các tiền tố để chỉ định vị trí của nó:
    *   `classpath:`: Nạp tài nguyên từ classpath. Đây là cách phổ biến nhất để đọc các file cấu hình, template... nằm trong `src/main/resources`.
    *   `file:`: Nạp tài nguyên từ một đường dẫn tuyệt đối trên hệ thống file (ví dụ: `file:/home/user/data.txt`).
    *   `http:`: Nạp tài nguyên từ một URL.

Sử dụng `ResourceLoader` giúp code của bạn sạch sẽ và không phụ thuộc vào vị trí vật lý của tài nguyên.

### 2. Ví dụ minh họa thực tế

**Tình huống:** Khi một đơn hàng được tạo (`OrderService`), chúng ta sẽ phát đi một sự kiện `OrderPlacedEvent`. `EmailNotificationListener` sẽ lắng nghe sự kiện này, đọc một mẫu email từ file tài nguyên và gửi thông báo cho khách hàng.

**Bước 1: Tạo sự kiện**

```java
// Lớp này chứa dữ liệu về sự kiện
// Nó nên là immutable
public class OrderPlacedEvent extends ApplicationEvent {
    private final String orderId;

    public OrderPlacedEvent(Object source, String orderId) {
        super(source);
        this.orderId = orderId;
    }

    public String getOrderId() {
        return orderId;
    }
}
```

**Bước 2: Tạo Publisher (Người phát sự kiện)**

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationEventPublisher;
import org.springframework.stereotype.Service;

@Service
public class OrderService {
    private final ApplicationEventPublisher eventPublisher;

    @Autowired
    public OrderService(ApplicationEventPublisher eventPublisher) {
        this.eventPublisher = eventPublisher;
    }

    public void placeOrder(String orderId) {
        System.out.println("[OrderService] Đang xử lý đơn hàng: " + orderId);
        // ... logic xử lý đơn hàng ...
        
        System.out.println("[OrderService] Đơn hàng xử lý xong. Phát đi sự kiện OrderPlacedEvent.");
        // Phát đi sự kiện
        OrderPlacedEvent event = new OrderPlacedEvent(this, orderId);
        eventPublisher.publishEvent(event);
        
        System.out.println("[OrderService] Đã quay trở lại sau khi phát sự kiện.");
    }
}
```

**Bước 3: Tạo Listener (Người nghe) và sử dụng ResourceLoader**

*   Đầu tiên, tạo một file template `src/main/resources/order-confirmation-template.txt`:
    ```
    Xin chao,
    
    Cam on ban da dat hang. Ma don hang cua ban la: {{orderId}}.
    
    Tran trong,
    Cua hang ABC.
    ```
*   Sau đó, tạo Listener:

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationListener;
import org.springframework.context.event.EventListener;
import org.springframework.core.io.Resource;
import org.springframework.core.io.ResourceLoader;
import org.springframework.stereotype.Component;
import java.nio.charset.StandardCharsets;
import org.springframework.util.StreamUtils;

@Component
public class EmailNotificationListener {

    private final ResourceLoader resourceLoader;

    @Autowired
    public EmailNotificationListener(ResourceLoader resourceLoader) {
        this.resourceLoader = resourceLoader;
    }

    @EventListener
    public void onOrderPlaced(OrderPlacedEvent event) {
        System.out.println("[EmailListener] Đã nhận được OrderPlacedEvent cho đơn hàng: " + event.getOrderId());
        System.out.println("[EmailListener] Bắt đầu gửi email...");
        
        try {
            // Sử dụng ResourceLoader để nạp template từ classpath
            Resource resource = resourceLoader.getResource("classpath:order-confirmation-template.txt");
            String template = StreamUtils.copyToString(resource.getInputStream(), StandardCharsets.UTF_8);
            
            // Thay thế placeholder bằng dữ liệu thực tế
            String emailContent = template.replace("{{orderId}}", event.getOrderId());
            
            System.out.println("--- Gửi Email ---");
            System.out.println(emailContent);
            System.out.println("-----------------");

            // Giả lập việc gửi email tốn thời gian
            Thread.sleep(2000);
            
        } catch (Exception e) {
            e.printStackTrace();
        }
        
        System.out.println("[EmailListener] Gửi email xong.");
    }
}
```

**Bước 4: Chạy ứng dụng**

```java
@Configuration
@ComponentScan("com.example.events")
class AppConfig {}

public class EventDemo {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        
        OrderService orderService = context.getBean(OrderService.class);
        orderService.placeOrder("ABC-12345");
        
        context.close();
    }
}
```
*   **Kết quả đầu ra:** Bạn sẽ thấy log của `OrderService` in ra, sau đó log của `EmailListener` được thực thi, và cuối cùng log "Đã quay trở lại..." của `OrderService` mới xuất hiện. Điều này chứng tỏ cơ chế mặc định là đồng bộ.

### 3. Mini Exercise

1.  Tạo một sự kiện mới tên là `UserLoggedInEvent` chứa `username` của người dùng.
2.  Sửa đổi `OrderService` (hoặc tạo một `UserService` mới) để phát đi sự kiện này.
3.  Tạo một component mới tên là `AuditLogListener` để lắng nghe sự kiện `UserLoggedInEvent` và in ra một dòng log như: `[AUDIT] User 'tên_user' logged in at 'thời_gian_hiện_tại'`.

### 4. Quiz Question

**Câu hỏi:** Trong một cấu hình Spring mặc định (không có `@Async`), khi một Publisher phát đi một sự kiện và có 3 Listener cùng lắng nghe sự kiện đó, luồng thực thi sẽ diễn ra như thế nào?

a) Publisher tiếp tục công việc của mình ngay lập tức, trong khi 3 Listener được thực thi song song trên các luồng riêng biệt.
b) Publisher sẽ bị chặn lại (dừng đợi) cho đến khi cả 3 Listener thực thi xong công việc của chúng, sau đó Publisher mới tiếp tục.
c) Publisher chỉ đợi Listener đầu tiên thực thi xong rồi sẽ tiếp tục, 2 Listener còn lại sẽ chạy ngầm.
d) Các Listener sẽ không được gọi vì cần phải cấu hình một `EventMulticaster` đặc biệt để xử lý nhiều Listener.

---
**Đáp án:** **b) Publisher sẽ bị chặn lại (dừng đợi) cho đến khi cả 3 Listener thực thi xong công việc của chúng, sau đó Publisher mới tiếp tục.**

**Giải thích:** Mặc định, Spring sử dụng một `SimpleApplicationEventMulticaster` để xử lý sự kiện, và nó hoạt động một cách đồng bộ. Nó sẽ duyệt qua danh sách các listener và gọi chúng một cách tuần tự trên cùng một luồng (thread) với publisher. Do đó, publisher phải chờ cho đến khi toàn bộ chuỗi xử lý sự kiện hoàn tất.

## Tổng kết chương: Spring Core

Sau khi đi qua các khái niệm nền tảng từ IoC/DI, vòng đời bean, cấu hình cho đến AOP và sự kiện, đây là phần tổng hợp lại các kiến thức trọng tâm, các câu hỏi phỏng vấn thường gặp và một dự án nhỏ để bạn áp dụng tất cả những gì đã học.

### 1. Bảng tổng hợp các khái niệm chính

| Khái niệm | Vai trò và Mô tả | Ưu điểm / Khi nên dùng | Hạn chế / Lưu ý |
| :--- | :--- | :--- | :--- |
| **IoC Container (`ApplicationContext`)** | "Bộ não" của Spring. Chịu trách nhiệm tạo, cấu hình, lắp ráp và quản lý vòng đời của các đối tượng (Bean). | Tách biệt việc tạo đối tượng khỏi logic nghiệp vụ, giảm sự phụ thuộc cứng (loose coupling), dễ dàng quản lý và thay thế các thành phần. | Tăng thời gian khởi động ứng dụng một chút vì phải thiết lập container và tạo các bean singleton. |
| **Bean** | Là một đối tượng được quản lý bởi IoC Container. Là khối xây dựng cơ bản của ứng dụng Spring. | Được quản lý vòng đời, dễ dàng tiêm phụ thuộc, có thể áp dụng AOP và các dịch vụ khác của Spring. | Cần được định nghĩa đúng cách để container có thể tìm thấy và quản lý. |
| **Dependency Injection (DI)** | Cơ chế mà IoC Container "tiêm" các đối tượng phụ thuộc (dependency) vào một bean thay vì bean đó tự tạo ra chúng. | Code trở nên module hóa, dễ đọc, và cực kỳ dễ viết unit test bằng cách tiêm các đối tượng giả (mock). | Có thể dẫn đến lỗi nếu cấu hình sai (ví dụ: `No qualifying bean`, `Circular Dependency`). |
| **Bean Scopes (`singleton`, `prototype`)** | `singleton` (mặc định): chỉ một instance duy nhất cho cả ứng dụng. `prototype`: một instance mới mỗi khi được yêu cầu. | `singleton` hiệu năng cao cho các đối tượng không trạng thái (stateless). `prototype` cần thiết cho các đối tượng có trạng thái (stateful). | Bean `singleton` phải là thread-safe nếu có trạng thái thay đổi. Spring không quản lý vòng đời hủy của bean `prototype`. |
| **Bean Lifecycle Callbacks (`@PostConstruct`, `@PreDestroy`)** | Các phương thức cho phép bạn thực thi logic tùy chỉnh tại các thời điểm quan trọng trong vòng đời của bean (sau khi khởi tạo và trước khi bị hủy). | Hữu ích để khởi tạo tài nguyên (kết nối DB, cache) hoặc dọn dẹp tài nguyên một cách an toàn. | Không nên lạm dụng để chứa business logic phức tạp. `@PreDestroy` không được gọi cho bean `prototype`. |
| **Java-based Config (`@Configuration`, `@Bean`)** | Cách cấu hình Spring hiện đại, sử dụng code Java để định nghĩa bean. Mang lại sự an toàn về kiểu và khả năng refactor mạnh mẽ. | Linh hoạt (có thể dùng logic if/else), an toàn kiểu, dễ điều hướng và bảo trì. Rất tốt để cấu hình bean từ thư viện bên thứ ba. | Có thể làm cho cấu hình phức tạp hơn một chút so với auto-scanning nếu có quá nhiều bean. |
| **Profiles (`@Profile`)** | Cho phép định nghĩa và kích hoạt các nhóm bean khác nhau cho các môi trường khác nhau (dev, test, prod). | Cực kỳ quan trọng trong thực tế để quản lý cấu hình (ví dụ: `DataSource`) giữa các môi trường mà không cần thay đổi code. | Cần quản lý file properties và cách kích hoạt profile cẩn thận để tránh lỗi cấu hình sai môi trường. |
| **AOP (`@Aspect`, `@Around`)** | Cho phép tách các mối quan tâm xuyên suốt (logging, security, transaction) ra khỏi logic nghiệp vụ chính. | Giữ cho business logic sạch sẽ, giảm lặp code, tăng tính module hóa. Dễ dàng bật/tắt các khía cạnh. | Vấn đề tự gọi (self-invocation) không đi qua proxy. Có thể làm luồng thực thi khó debug hơn nếu lạm dụng. |
| **Application Events (`@EventListener`)** | Cung cấp mô hình publish-subscribe để các component giao tiếp với nhau một cách lỏng lẻo. | Giảm sự kết dính giữa các component. Dễ dàng mở rộng hệ thống bằng cách thêm các Listener mới mà không cần sửa đổi Publisher. | Mặc định là đồng bộ (synchronous), có thể ảnh hưởng đến hiệu năng của publisher nếu listener xử lý lâu. Cần cấu hình `@Async` cho xử lý bất đồng bộ. |

---

### 2. Câu hỏi phỏng vấn thường gặp

#### Câu hỏi cơ bản

1.  **Spring Framework là gì? Nó giải quyết vấn đề gì?**
2.  **Inversion of Control (IoC) và Dependency Injection (DI) là gì? Mối quan hệ giữa chúng?**
3.  **Sự khác biệt giữa `BeanFactory` và `ApplicationContext`?**
4.  **Có bao nhiêu loại Bean Scope trong Spring Core? Kể tên và giải thích.**
5.  **`@Component`, `@Service`, `@Repository` và `@Controller` khác nhau như thế nào?**

#### Câu hỏi về kinh nghiệm thực tế

6.  **So sánh 3 loại Dependency Injection (Constructor, Setter, Field). Loại nào là tốt nhất và tại sao?**
    *   *Gợi ý trả lời:* Constructor Injection là tốt nhất vì nó đảm bảo các dependency bắt buộc luôn tồn tại và hỗ trợ tạo các đối tượng bất biến (immutable) với trường `final`. Nó cũng giúp phát hiện sớm circular dependency.
7.  **Làm thế nào để giải quyết xung đột khi có nhiều bean cùng một kiểu?**
    *   *Gợi ý trả lời:* Dùng `@Primary` để chỉ định một bean mặc định, hoặc dùng `@Qualifier("tên_bean")` cùng với `@Autowired` để chỉ định chính xác bean cần tiêm.
8.  **Giải thích vòng đời của một bean singleton trong Spring.**
9.  **Annotation `@PostConstruct` và `initMethod` trong `@Bean` khác nhau như thế nào? Khi nào dùng cái nào?**
    *   *Gợi ý trả lời:* Cả hai đều dùng để khởi tạo. `@PostConstruct` là chuẩn JSR-250, ưu tiên hơn vì nó không phụ thuộc vào Spring. `initMethod` dùng trong các lớp `@Configuration` khi bạn không thể sửa đổi code của lớp được tạo bean (ví dụ: lớp của thư viện bên thứ ba).

#### Câu hỏi nâng cao và tình huống

10. **Circular Dependency là gì? Spring xử lý nó như thế nào đối với bean singleton?**
    *   *Gợi ý trả lời:* Là khi bean A cần bean B và bean B lại cần bean A. Đối với singleton và setter/field injection, Spring có thể giải quyết bằng cách tạo bean A, lưu tham chiếu tạm thời, tạo bean B và tiêm tham chiếu tạm thời của A vào, sau đó hoàn thiện bean A. Đối với constructor injection, nó sẽ báo lỗi `BeanCurrentlyInCreationException`.
11. **Tại sao đôi khi các advice của AOP không được áp dụng khi một phương thức gọi một phương thức khác trong cùng một lớp? Làm thế nào để giải quyết?**
    *   *Gợi ý trả lời:* Đây là vấn đề self-invocation. Lời gọi `this.methodB()` không đi qua proxy mà đi trực tiếp vào đối tượng gốc. Giải pháp: (1) Tự tiêm chính bean đó vào (`@Autowired private MyService self;`), (2) Tách phương thức cần áp dụng AOP ra một bean khác.
12. **Sự khác biệt giữa proxy tạo bởi JDK Dynamic Proxy và CGLIB là gì?**
    *   *Gợi ý trả lời:* JDK Proxy yêu cầu đối tượng mục tiêu phải implement một interface và proxy sẽ implement cùng interface đó. CGLIB không cần interface, nó tạo ra một lớp con của đối tượng mục tiêu. Các phương thức `final` không thể được proxy bởi CGLIB.
13. **Trong tình huống nào bạn sẽ sử dụng `BeanPostProcessor` hoặc `BeanFactoryPostProcessor`?**
    *   *Gợi ý trả lời:* `BeanFactoryPostProcessor` được dùng để sửa đổi *định nghĩa* của bean trước khi chúng được tạo (ví dụ: thay đổi một thuộc tính cấu hình). `BeanPostProcessor` được dùng để thực hiện các hành động trên *instance* của bean sau khi nó được tạo (ví dụ: bao bọc nó bằng một proxy). AOP của Spring được triển khai bằng `BeanPostProcessor`.

---

### 3. Mini Project: Ứng dụng Console "Task Manager"

Dự án này sẽ giúp bạn kết hợp tất cả các khái niệm đã học để xây dựng một ứng dụng quản lý công việc đơn giản trên console.

**Yêu cầu chức năng:**

*   Thêm một công việc mới.
*   Hiển thị tất cả công việc.
*   Khi một công việc được thêm, ghi log AOP và phát đi một sự kiện.

#### Bước 1: Cấu trúc dự án và file `pom.xml`

Tạo một dự án Maven và thêm các dependency cần thiết:

```xml
<dependencies>
    <!-- Spring Core -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>5.3.23</version> <!-- Hoặc phiên bản mới hơn -->
    </dependency>
    <!-- Spring AOP -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-aspects</artifactId>
        <version>5.3.23</version>
    </dependency>
    <!-- Annotation JSR-250 cho @PostConstruct, @PreDestroy -->
    <dependency>
        <groupId>javax.annotation</groupId>
        <artifactId>javax.annotation-api</artifactId>
        <version>1.3.2</version>
    </dependency>
</dependencies>
```

#### Bước 2: Tạo Model

```java
// com/example/taskmanager/model/Task.java
public class Task {
    private final int id;
    private final String description;
    
    public Task(int id, String description) { this.id = id; this.description = description; }
    public int getId() { return id; }
    public String getDescription() { return description; }

    @Override
    public String toString() { return "Task{id=" + id + ", description='" + description + "'}"; }
}
```

#### Bước 3: Tạo Repository Layer (IoC, DI)

```java
// com/example/taskmanager/repository/TaskRepository.java
public interface TaskRepository {
    Task save(Task task);
    List<Task> findAll();
}

// com/example/taskmanager/repository/InMemoryTaskRepository.java
import org.springframework.stereotype.Repository;
import java.util.concurrent.atomic.AtomicInteger;

@Repository
public class InMemoryTaskRepository implements TaskRepository {
    private final Map<Integer, Task> tasks = new ConcurrentHashMap<>();
    private final AtomicInteger idCounter = new AtomicInteger(0);

    @Override
    public Task save(String description) {
        int newId = idCounter.incrementAndGet();
        Task task = new Task(newId, description);
        tasks.put(newId, task);
        return task;
    }
    
    @Override
    public List<Task> findAll() { return new ArrayList<>(tasks.values()); }
}
```

#### Bước 4: Tạo Service Layer (IoC, DI)

```java
// com/example/taskmanager/service/TaskService.java
public interface TaskService {
    Task createTask(String description);
    List<Task> getAllTasks();
}

// com/example/taskmanager/service/DefaultTaskService.java
import org.springframework.stereotype.Service;

@Service
public class DefaultTaskService implements TaskService {
    private final TaskRepository taskRepository;

    public DefaultTaskService(TaskRepository taskRepository) {
        this.taskRepository = taskRepository;
    }

    @Override
    public Task createTask(String description) {
        System.out.println("Service: Đang tạo task...");
        return taskRepository.save(description);
    }
    
    @Override
    public List<Task> getAllTasks() { return taskRepository.findAll(); }
}
```

#### Bước 5: Tạo AOP Logging Aspect

```java
// com/example/taskmanager/aop/LoggingAspect.java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.taskmanager.service.TaskService.createTask(..))")
    public void logBeforeCreateTask(JoinPoint joinPoint) {
        System.out.println("[AOP LOG] Sắp thực thi createTask với tham số: " + Arrays.toString(joinPoint.getArgs()));
    }

    @AfterReturning(pointcut = "execution(* com.example.taskmanager.service.*.*(..))", returning = "result")
    public void logAfterMethodExecution(JoinPoint joinPoint, Object result) {
        System.out.println("[AOP LOG] Đã thực thi xong: " + joinPoint.getSignature().getName() + ". Kết quả: " + result);
    }
}
```

#### Bước 6: Tạo Application Events

```java
// com/example/taskmanager/event/TaskCreatedEvent.java
import org.springframework.context.ApplicationEvent;

public class TaskCreatedEvent extends ApplicationEvent {
    private final Task task;
    public TaskCreatedEvent(Object source, Task task) { super(source); this.task = task; }
    public Task getTask() { return task; }
}

// Sửa lại DefaultTaskService để phát sự kiện
@Service
public class DefaultTaskService implements TaskService {
    private final TaskRepository taskRepository;
    private final ApplicationEventPublisher eventPublisher; // Tiêm publisher

    public DefaultTaskService(TaskRepository taskRepository, ApplicationEventPublisher eventPublisher) {
        this.taskRepository = taskRepository;
        this.eventPublisher = eventPublisher;
    }

    @Override
    public Task createTask(String description) {
        System.out.println("Service: Đang tạo task...");
        Task newTask = taskRepository.save(description);
        // Phát sự kiện
        eventPublisher.publishEvent(new TaskCreatedEvent(this, newTask));
        return newTask;
    }
    
    //... getAllTasks()
}


// com/example/taskmanager/listener/NotificationListener.java
import org.springframework.context.event.EventListener;
import org.springframework.stereotype.Component;

@Component
public class NotificationListener {
    @EventListener
    public void handleTaskCreated(TaskCreatedEvent event) {
        System.out.println("[EVENT LISTENER] Nhận được thông báo: Task mới đã được tạo -> " + event.getTask());
    }
}
```

#### Bước 7: Cấu hình và chạy ứng dụng

```java
// com/example/taskmanager/config/AppConfig.java
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@ComponentScan("com.example.taskmanager")
@EnableAspectJAutoProxy // Bật AOP
public class AppConfig {}

// com/example/taskmanager/TaskManagerApp.java
public class TaskManagerApp {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext context = new AnnotationConfigApplicationContext(AppConfig.class);
        TaskService taskService = context.getBean(TaskService.class);
        
        Scanner scanner = new Scanner(System.in);
        while(true) {
            System.out.println("\n--- Task Manager ---");
            System.out.println("1. Add Task");
            System.out.println("2. Show All Tasks");
            System.out.println("3. Exit");
            System.out.print("Chọn: ");
            String choice = scanner.nextLine();
            
            switch(choice) {
                case "1":
                    System.out.print("Nhập mô tả task: ");
                    String desc = scanner.nextLine();
                    taskService.createTask(desc);
                    break;
                case "2":
                    System.out.println("Danh sách Tasks:");
                    taskService.getAllTasks().forEach(System.out::println);
                    break;
                case "3":
                    context.close();
                    scanner.close();
                    return;
                default:
                    System.out.println("Lựa chọn không hợp lệ.");
            }
        }
    }
}
```

Khi bạn chạy `TaskManagerApp` và thêm một task mới, bạn sẽ thấy log từ AOP và từ Event Listener xuất hiện trên console, chứng tỏ tất cả các thành phần của Spring Core đã hoạt động cùng nhau một cách hài hòa.