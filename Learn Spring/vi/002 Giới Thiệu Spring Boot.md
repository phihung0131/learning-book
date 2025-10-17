## Lesson 1: Giới thiệu Spring Boot và cơ chế Auto-Configuration

### 1. Giải thích khái niệm chi tiết

#### Spring Boot là gì và tại sao ra đời?

Nếu Spring Framework cung cấp cho bạn các "viên gạch" (IoC, DI, AOP,...) để xây dựng một ngôi nhà, thì Spring Boot cung cấp cho bạn một "bộ khung nhà lắp ghép" gần như hoàn chỉnh.

Trước Spring Boot, việc khởi tạo một dự án Spring Framework đòi hỏi rất nhiều cấu hình thủ công:

*   **Quản lý Dependency phức tạp:** Bạn phải tự chọn và quản lý phiên bản tương thích của hàng chục thư viện (spring-core, spring-web, jackson, hibernate, tomcat...). Một sai sót nhỏ có thể gây ra lỗi xung đột phiên bản.
*   **Cấu hình Boilerplate:** Phải viết rất nhiều code cấu hình lặp đi lặp lại cho những thứ cơ bản như `DispatcherServlet`, `DataSource`, `EntityManagerFactory`, `TransactionManager`...
*   **Triển khai rườm rà:** Cần phải cấu hình một web server riêng (như Tomcat, Jetty) và đóng gói ứng dụng thành file WAR để triển khai lên đó.

Spring Boot ra đời để giải quyết triệt để những vấn đề này với triết lý **"Convention over Configuration"** (Ưa chuộng quy ước hơn cấu hình). Nó đưa ra các quy ước và cấu hình mặc định hợp lý, giúp bạn có thể tạo ra một ứng dụng Spring "cứ chạy là được" (just run) chỉ trong vài phút.

#### Sự khác biệt giữa Spring Framework và Spring Boot

| Tiêu chí | Spring Framework | Spring Boot |
| :--- | :--- | :--- |
| **Mục đích** | Cung cấp một framework toàn diện với các module (Core, Web, Data...) để phát triển ứng dụng Java. | Cung cấp một cách **nhanh chóng và dễ dàng** để tạo và chạy các ứng dụng dựa trên Spring Framework. |
| **Cấu hình** | Yêu cầu cấu hình thủ công và tường minh cho hầu hết mọi thứ. | **Tự động cấu hình (Auto-configuration)** nhiều nhất có thể dựa trên các thư viện có trong classpath. |
| **Dependency** | Phải tự quản lý phiên bản của tất cả các dependency. | Cung cấp các **"Starters"** để quản lý dependency một cách đơn giản. Chỉ cần khai báo `spring-boot-starter-web`, bạn sẽ có đủ bộ thư viện cho ứng dụng web. |
| **Web Server** | Cần một web server bên ngoài (Tomcat, Jetty...). | Tích hợp sẵn web server (embedded server), cho phép chạy ứng dụng như một file JAR độc lập. |
| **Điểm cuối** | Cần phải tự xây dựng các tính năng cho môi trường production (kiểm tra sức khỏe, metrics...). | Cung cấp sẵn **Actuator** với các endpoint để giám sát và quản lý ứng dụng. |
| **Kết luận** | Là nền tảng, cung cấp sự linh hoạt tối đa nhưng đòi hỏi nhiều công sức. | Là một "lớp tiện ích" bên trên Spring Framework, giúp tăng tốc độ phát triển và đơn giản hóa việc triển khai. |

#### @SpringBootApplication và các thành phần bên trong nó

Đây là annotation quan trọng nhất trong một ứng dụng Spring Boot, thường được đặt trên class `main`. Nó là một annotation tổng hợp, bao gồm 3 annotation chính:

1.  **`@SpringBootConfiguration`:**
    *   Về bản chất, nó chính là `@Configuration` của Spring Core. Nó đánh dấu lớp này là một nguồn định nghĩa bean.
    *   Sự khác biệt là nó giúp Spring Boot tìm thấy cấu hình chính của ứng dụng trong quá trình test.

2.  **`@EnableAutoConfiguration`:**
    *   Đây là "phép màu" chính của Spring Boot. Nó kích hoạt cơ chế tự động cấu hình.
    *   Nó ra lệnh cho Spring Boot hãy "đoán" và cấu hình các bean mà bạn có thể cần, dựa trên các dependency mà bạn đã thêm vào (ví dụ: thấy `spring-webmvc.jar` thì tự cấu hình `DispatcherServlet`).

3.  **`@ComponentScan`:**
    *   Giống hệt `@ComponentScan` trong Spring Core.
    *   Nó yêu cầu Spring quét các component (như `@Service`, `@Controller`...) trong package chứa lớp này và các package con của nó để đăng ký chúng thành bean.

#### Cơ chế Auto-Configuration hoạt động như thế nào?

Đây không phải là phép thuật. Nó hoạt động dựa trên một quy trình rõ ràng:

1.  **Kích hoạt:** `@EnableAutoConfiguration` là công tắc bật cơ chế này.

2.  **Quét và Nạp cấu hình:**
    *   Spring Boot sẽ tìm đến file `META-INF/spring.factories` (trong các phiên bản cũ hơn 3.x) hoặc `META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports` (từ 3.x trở đi) bên trong file `spring-boot-autoconfigure.jar`.
    *   File này chứa một danh sách rất dài các lớp Configuration (ví dụ: `DataSourceAutoConfiguration`, `WebMvcAutoConfiguration`, `JacksonAutoConfiguration`...).
    *   Spring Boot sẽ nạp tất cả các lớp này vào để xem xét.

3.  **Áp dụng có điều kiện (Conditional Annotations):**
    *   Đây là phần "thông minh" nhất. Mỗi lớp auto-configuration được đánh dấu bằng các annotation `@Conditional...` để quyết định xem nó có nên được áp dụng hay không.
    *   **`@ConditionalOnClass`:** Lớp configuration này chỉ được kích hoạt **NẾU** một class cụ thể nào đó tồn tại trong classpath.
        *   *Ví dụ:* `DataSourceAutoConfiguration` có `@ConditionalOnClass(DataSource.class)`. Điều này có nghĩa là: "Chỉ tự động cấu hình `DataSource` nếu có thư viện JDBC trong dự án".
    *   **`@ConditionalOnMissingBean`:** Một bean trong lớp configuration này chỉ được tạo ra **NẾU** lập trình viên chưa tự định nghĩa một bean cùng kiểu.
        *   *Ví dụ:* Bên trong `DataSourceAutoConfiguration`, phương thức tạo `DataSource` có `@ConditionalOnMissingBean(DataSource.class)`. Điều này có nghĩa là: "Hãy tạo một `DataSource` mặc định, nhưng nếu tôi (lập trình viên) đã tự tạo một `@Bean` `DataSource` của riêng mình rồi thì đừng làm gì cả".
    *   Ngoài ra còn nhiều annotation điều kiện khác như `@ConditionalOnProperty` (dựa vào thuộc tính trong `application.properties`), `@ConditionalOnWebApplication`, v.v.

**Tóm tắt luồng hoạt động:**
`@SpringBootApplication` -> `@EnableAutoConfiguration` -> Nạp danh sách các lớp AutoConfiguration -> Lần lượt kiểm tra từng lớp -> Nếu điều kiện (`@Conditional...`) được thỏa mãn -> Tạo các bean mặc định.

Điều này cho thấy Spring Boot vừa **"opinionated"** (đưa ra các lựa chọn mặc định) nhưng cũng rất **linh hoạt** (cho phép bạn dễ dàng ghi đè các mặc định đó).

### 2. Ví dụ minh họa thực tế

**Tạo một ứng dụng Web "Hello World" đơn giản.**

**1. Cấu trúc `pom.xml`:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project ...>
    <modelVersion>4.0.0</modelVersion>
    <!-- Kế thừa từ parent POM của Spring Boot để quản lý phiên bản -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.5</version> <!-- Hoặc phiên bản mới hơn -->
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <java.version>11</java.version>
    </properties>

    <dependencies>
        <!-- Chỉ cần một starter này cho ứng dụng web -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
    </dependencies>
    
    <build>
        <plugins>
            <!-- Plugin để đóng gói thành file JAR thực thi được -->
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```
*   **Lưu ý:** Chúng ta không cần khai báo phiên bản cho `spring-webmvc`, `jackson`, `tomcat`... vì `spring-boot-starter-parent` đã định nghĩa tất cả các phiên bản tương thích.

**2. Lớp Application chính:**

```java
// com/example/demo/DemoApplication.java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}```

**3. Lớp Controller:**

```java
// com/example/demo/HelloController.java
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/")
    public String sayHello() {
        return "Hello from Spring Boot!";
    }
}
```

**Chạy ứng dụng và phân tích:**

*   Bạn chỉ cần chạy phương thức `main` trong `DemoApplication`.
*   Console sẽ hiển thị log, trong đó có những dòng quan trọng như:
    ```
    .s.b.w.embedded.tomcat.TomcatWebServer  : Tomcat started on port(s): 8080 (http)
    .s.d.s.DefaultServlet                  : Initializing Spring DispatcherServlet 'dispatcherServlet'
    o.s.b.a.w.s.WelcomePageHandlerMapping  : Adding welcome page: class path resource [static/index.html]
    ```
*   **Chuyện gì đã xảy ra "sau cánh gà"?**
    1.  `pom.xml` có `spring-boot-starter-web`, nó kéo theo `spring-webmvc` và `tomcat-embed-core`.
    2.  Vì `Tomcat.class` và `DispatcherServlet.class` có trong classpath, các điều kiện `@ConditionalOnClass` của `ServletWebServerFactoryAutoConfiguration` và `WebMvcAutoConfiguration` được thỏa mãn.
    3.  `ServletWebServerFactoryAutoConfiguration` tạo ra một bean Tomcat server chạy ở cổng 8080.
    4.  `WebMvcAutoConfiguration` tự động cấu hình `DispatcherServlet`, `HandlerMapping`, và các thành phần cần thiết khác cho một ứng dụng web MVC.
    5.  `@ComponentScan` tìm thấy `HelloController` và đăng ký nó như một bean.
        Tất cả những việc này bạn phải làm thủ công với Spring Core, nhưng Spring Boot đã tự động hóa hoàn toàn.

### 3. Mini Exercise

1.  Trong project Spring Boot của bạn, hãy sử dụng IDE để mở các dependency của Maven.
2.  Tìm đến jar `spring-boot-autoconfigure-{version}.jar`.
3.  Tìm và mở file `org/springframework/boot/autoconfigure/web/servlet/WebMvcAutoConfiguration.class`.
4.  Quan sát các annotation ở đầu file. Hãy đọc và thử giải thích các điều kiện cần có để lớp cấu hình này được kích hoạt.

### 4. Quiz Question

**Câu hỏi:** Bạn thêm dependency `spring-boot-starter-data-jpa` vào `pom.xml` nhưng lại quên không cấu hình URL, username, password cho database trong `application.properties`. Điều gì sẽ xảy ra khi ứng dụng khởi động?

a) Ứng dụng sẽ khởi động thành công nhưng sẽ báo lỗi khi có request truy cập database.
b) Ứng dụng sẽ **không thể khởi động** và báo lỗi, vì `DataSourceAutoConfiguration` được kích hoạt nhưng không tìm thấy thông tin cần thiết để tạo bean `DataSource`.
c) Spring Boot sẽ tự động bỏ qua việc cấu hình `DataSource` và ứng dụng khởi động bình thường.
d) Spring Boot sẽ tự động sử dụng một H2 in-memory database mặc định và khởi động thành công.

---
**Đáp án:** **d) Spring Boot sẽ tự động sử dụng một H2 in-memory database mặc định và khởi động thành công.** (Với điều kiện H2 database driver có trong classpath, điều này thường đúng khi bạn thêm starter `data-jpa` mà không có driver khác).

**Giải thích:** `DataSourceAutoConfiguration` đủ thông minh để biết rằng nếu lập trình viên không cung cấp cấu hình `DataSource`, nó sẽ cố gắng tự cấu hình một embedded database (như H2, HSQLDB, Derby) nếu tìm thấy driver tương ứng trong classpath. Đây là một ví dụ điển hình về triết lý "Convention over Configuration" giúp ứng dụng có thể chạy ngay lập tức cho mục đích phát triển và kiểm thử. Nếu bạn thêm một driver database khác (như `mysql-connector-java`), câu trả lời sẽ là (b).

## Lesson 2: Dependency Management và Starters

### 1. Giải thích khái niệm chi tiết

#### Vấn đề của quản lý dependency thủ công

Như đã đề cập ở bài trước, một trong những nỗi đau lớn nhất khi dùng Spring Framework là quản lý dependency. Bạn phải:
1.  **Tìm và thêm** từng dependency một cách thủ công (`spring-core`, `spring-web`, `spring-webmvc`, `jackson-databind`, `tomcat-embed`, etc.).
2.  **Đảm bảo sự tương thích** về phiên bản giữa tất cả chúng. Ví dụ, Spring Framework phiên bản X.Y.Z yêu cầu Hibernate phiên bản A.B.C và Jackson phiên bản D.E.F. Nếu bạn chọn sai một phiên bản, ứng dụng có thể gặp lỗi `NoSuchMethodError` hoặc các lỗi khó lường khác lúc chạy.

Spring Boot giải quyết vấn đề này một cách triệt để thông qua hai cơ chế chính: **Parent POM** và **Starters**.

#### Parent POM: `spring-boot-starter-parent`

Khi bạn tạo một dự án Spring Boot từ `start.spring.io` hoặc IDE, file `pom.xml` của bạn sẽ kế thừa từ `spring-boot-starter-parent`:

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.7.5</version>
</parent>
```

`parent` POM này thực hiện hai nhiệm vụ cực kỳ quan trọng:

1.  **Quản lý phiên bản tập trung (`<dependencyManagement>`):**
    *   Bên trong `spring-boot-starter-parent` có một thẻ `<dependencyManagement>` khổng lồ. Thẻ này định nghĩa phiên bản cho một danh sách dài các thư viện phổ biến (Spring, Jackson, Hibernate, Logback, ...).
    *   **Cơ chế hoạt động:** Thẻ `<dependencyManagement>` trong Maven không tự động *thêm* dependency vào dự án của bạn. Thay vào đó, nó hoạt động như một "bảng tra cứu phiên bản". Khi bạn khai báo một dependency trong dự án của mình **mà không cần chỉ định phiên bản**, Maven sẽ tự động tìm trong thẻ `<dependencyManagement>` của parent POM để lấy phiên bản đã được kiểm chứng tương thích.
    *   **Lợi ích:** Bạn không cần phải lo lắng về phiên bản. Chỉ cần khai báo `groupId` và `artifactId`, Spring Boot sẽ lo phần còn lại.

2.  **Cung cấp cấu hình mặc định:**
    *   Nó cấu hình sẵn các plugin Maven phổ biến (như `maven-compiler-plugin` để mặc định dùng phiên bản Java bạn đã chỉ định) và các thiết lập build khác.

#### Spring Boot Starters là gì?

Starters là "trái tim" của việc quản lý dependency trong Spring Boot.

*   **Định nghĩa:** Một starter là một file POM tiện lợi, không chứa code mà chỉ chứa một danh sách các **dependency bắc cầu (transitive dependencies)** cần thiết cho một chức năng cụ thể.
*   **Ví dụ:** Khi bạn muốn xây dựng một ứng dụng web, thay vì phải thêm `spring-web`, `spring-webmvc`, `jackson-databind`, `tomcat-embed`, `spring-boot-starter-validation`... bạn chỉ cần thêm **một** dependency duy nhất:
    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    ```
*   **Lợi ích:**
    *   **One-Stop-Shop:** Cung cấp tất cả mọi thứ bạn cần cho một tác vụ cụ thể chỉ với một dòng khai báo.
    *   **Giảm thiểu lỗi:** Bạn không thể quên một dependency quan trọng hoặc thêm sai phiên bản.
    *   **Sạch sẽ:** `pom.xml` của bạn trở nên ngắn gọn và dễ đọc hơn rất nhiều.

#### Cách thêm, loại bỏ và ghi đè dependency

Dù Spring Boot đã làm rất tốt việc quản lý tự động, bạn vẫn có toàn quyền kiểm soát.

1.  **Thêm Dependency:** Đơn giản là thêm thẻ `<dependency>` như bình thường. Nếu dependency đó được quản lý bởi parent POM, bạn không cần thêm thẻ `<version>`.

2.  **Ghi đè phiên bản (Overriding a Version):**
    *   **Best Practice:** Cách tốt nhất để ghi đè phiên bản của một dependency được quản lý bởi parent POM là định nghĩa một property trong thẻ `<properties>` của `pom.xml`. Tên của property phải khớp với tên được Spring Boot sử dụng.
    *   *Ví dụ:* Spring Boot parent POM định nghĩa phiên bản của Jackson thông qua property `jackson.version`. Để dùng một phiên bản khác, bạn chỉ cần thêm:
        ```xml
        <properties>
            <java.version>11</java.version>
            <jackson.version>2.14.0</jackson.version> <!-- Ghi đè phiên bản Jackson -->
        </properties>
        ```

3.  **Loại bỏ Dependency bắc cầu (Excluding a Transitive Dependency):**
    *   Đôi khi một starter kéo theo một thư viện mà bạn không muốn dùng.
    *   *Ví dụ:* `spring-boot-starter-web` mặc định dùng Tomcat. Nếu bạn muốn dùng Undertow, bạn cần loại bỏ Tomcat trước.
    *   Sử dụng thẻ `<exclusions>` bên trong thẻ `<dependency>`.
        ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!-- Sau đó thêm dependency cho Undertow -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-undertow</artifactId>
        </dependency>
        ```

### 2. Ví dụ minh họa thực tế

**Tình huống:** Xây dựng một ứng dụng REST API. Yêu cầu:
1.  Sử dụng `spring-boot-starter-web`.
2.  Máy chủ mặc định là Tomcat, nhưng chúng ta muốn đổi sang **Jetty**.
3.  Do yêu cầu bảo mật, dự án phải sử dụng thư viện `jackson-databind` phiên bản `2.13.4.2` thay vì phiên bản mặc định của Spring Boot.

**File `pom.xml` sẽ trông như thế này:**

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project ...>
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.7.5</version> <!-- Phiên bản này mặc định dùng Jackson < 2.13.4.2 -->
        <relativePath/>
    </parent>

    <groupId>com.example</groupId>
    <artifactId>custom-deps-demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <properties>
        <java.version>11</java.version>
        <!-- Bước 3: Ghi đè phiên bản Jackson bằng property -->
        <jackson.version>2.13.4.2</jackson.version>
    </properties>

    <dependencies>
        <!-- Bước 1: Thêm starter-web nhưng loại bỏ Tomcat -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-tomcat</artifactId>
                </exclusion>
            </exclusions>
        </dependency>

        <!-- Bước 2: Thêm starter cho Jetty -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-jetty</artifactId>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

**Cách kiểm tra:**

*   Sau khi build và chạy ứng dụng, hãy nhìn vào log khởi động. Thay vì thấy `TomcatWebServer`, bạn sẽ thấy:
    ```
    o.s.b.w.embedded.jetty.JettyWebServer   : Jetty started on port(s): 8080 (http/1.1)
    ```
*   Kiểm tra cây dependency trong IDE hoặc bằng lệnh `mvn dependency:tree`, bạn sẽ thấy `jackson-databind` có phiên bản là `2.13.4.2`.

### 3. Mini Exercise

Bạn được yêu cầu xây dựng một ứng dụng Spring Boot sử dụng `spring-boot-starter-web`. Tuy nhiên, thay vì sử dụng framework logging mặc định là Logback (do `spring-boot-starter-logging` kéo vào), bạn cần phải chuyển sang sử dụng **Log4j2**.

**Yêu cầu:**

1.  Trong `pom.xml`, hãy tìm cách loại bỏ dependency `spring-boot-starter-logging` (là dependency bắc cầu của `spring-boot-starter-web`).
2.  Thêm dependency `spring-boot-starter-log4j2`.
3.  **Gợi ý:** Bạn sẽ cần dùng thẻ `<exclusions>`.

### 4. Quiz Question

**Câu hỏi:** Nếu dự án của bạn kế thừa từ `spring-boot-starter-parent`, và bạn thêm dependency sau vào `pom.xml`, điều gì sẽ xảy ra?

```xml
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-webmvc</artifactId>
</dependency>
```

a) Lỗi build của Maven vì thiếu thẻ `<version>`.
b) Maven sẽ tự động tải về phiên bản **mới nhất** của `spring-webmvc` từ Maven Central.
c) Maven sẽ sử dụng phiên bản của `spring-webmvc` đã được định nghĩa sẵn trong mục `<dependencyManagement>` của `spring-boot-starter-parent`.
d) Dependency này sẽ bị bỏ qua vì nó đã là một phần của `spring-boot-starter-web`.

---
**Đáp án:** **c) Maven sẽ sử dụng phiên bản của `spring-webmvc` đã được định nghĩa sẵn trong mục `<dependencyManagement>` của `spring-boot-starter-parent`.**

**Giải thích:** Đây chính là sức mạnh của `parent` POM. Nó cung cấp một "bảng tra cứu" phiên bản tập trung. Miễn là bạn khai báo một dependency có trong bảng đó, bạn không cần chỉ định phiên bản, và Maven sẽ tự động áp dụng phiên bản đã được đội ngũ Spring Boot kiểm chứng là tương thích.

## Lesson 3: Configuration và Profiles

### 1. Giải thích khái niệm chi tiết

#### Externalized Configuration (Ngoại hóa cấu hình)

Một trong những nguyên tắc cốt lõi của một ứng dụng "12-factor" là tách biệt cấu hình khỏi code. Spring Boot tuân thủ triệt để nguyên tắc này. Thay vì hard-code các giá trị như URL database, API keys,... trong code Java, bạn đưa chúng ra các file hoặc môi trường bên ngoài. Điều này cho phép bạn chạy cùng một file JAR/WAR trên nhiều môi trường (dev, staging, prod) chỉ bằng cách thay đổi cấu hình.

#### `application.properties` vs `application.yml`

Spring Boot hỗ trợ hai định dạng file cấu hình chính, đặt trong thư mục `src/main/resources`. Bạn có thể chọn một trong hai, hoặc cả hai (properties sẽ được ưu tiên hơn nếu có key trùng lặp).

1.  **`application.properties`:**
    *   Định dạng key-value truyền thống của Java.
    *   Mỗi thuộc tính nằm trên một dòng.
    *   **Ví dụ:**
        ```properties
        server.port=8081
        spring.datasource.url=jdbc:mysql://localhost:3306/mydb
        spring.datasource.username=root
        ```
    *   **Ưu điểm:** Đơn giản, dễ hiểu, được nhiều công cụ Java hỗ trợ.
    *   **Nhược điểm:** Dễ bị lặp lại các tiền tố (prefix) như `spring.datasource`, khó nhìn ra cấu trúc phân cấp.

2.  **`application.yml` (hoặc `.yaml`):**
    *   Sử dụng định dạng YAML (YAML Ain't Markup Language).
    *   Có cấu trúc phân cấp (hierarchical) và sử dụng thụt đầu dòng (indentation) để thể hiện cấu trúc.
    *   **Ví dụ:**
        ```yaml
        server:
          port: 8081
        spring:
          datasource:
            url: jdbc:mysql://localhost:3306/mydb
            username: root
        ```    *   **Ưu điểm:** Gọn gàng hơn, dễ đọc hơn cho các cấu hình phức tạp, tránh lặp lại.
    *   **Nhược điểm:** **Nhạy cảm với thụt đầu dòng.** Sai một dấu cách cũng có thể làm hỏng toàn bộ cấu hình.
    *   **Best Practice:** YAML thường được ưa chuộng hơn trong các dự án hiện đại vì sự rõ ràng của nó.

#### Cách truy cập giá trị cấu hình

Có hai cách chính để đưa các giá trị từ file cấu hình vào trong code của bạn.

1.  **`@Value`:**
    *   Giống hệt `@Value` trong Spring Core. Dùng để tiêm một giá trị đơn lẻ vào một trường (field).
    *   **Cú pháp:** `@Value("${tên.thuộc.tính}")`
    *   **Nhược điểm:**
        *   Nếu có nhiều thuộc tính liên quan, bạn phải tiêm chúng một cách riêng lẻ, làm code trở nên dài dòng.
        *   Không có tính type-safe mạnh mẽ cho các cấu hình phức tạp (ví dụ một danh sách các đối tượng).

2.  **`@ConfigurationProperties` (Cách làm được khuyên dùng):**
    *   Đây là cách làm rất mạnh mẽ và đặc trưng của Spring Boot. Nó cho phép bạn **liên kết (bind)** một nhóm các thuộc tính có cùng tiền tố vào một đối tượng POJO (Plain Old Java Object).
    *   **Cách hoạt động:**
        1.  Tạo một class POJO với các trường tương ứng với các key cấu hình.
        2.  Đánh dấu class đó với `@ConfigurationProperties(prefix = "tiền_tố_chung")`.
        3.  Kích hoạt nó bằng cách thêm `@EnableConfigurationProperties(MyProperties.class)` vào lớp `@Configuration` của bạn, hoặc đơn giản là đánh dấu lớp POJO đó là một `@Component`.
    *   **Ưu điểm:**
        *   **Type-safe:** Các giá trị được tự động ép kiểu (String, int, boolean, List,...).
        *   **Gom nhóm logic:** Tất cả cấu hình liên quan được gom vào một chỗ.
        *   **Hỗ trợ validation:** Bạn có thể dùng các annotation validation (như `@NotNull`, `@Min`) ngay trên các trường của POJO.

#### Hệ thống phân cấp cấu hình (Configuration Hierarchy)

Spring Boot rất linh hoạt trong việc nạp cấu hình. Nó sẽ tìm và nạp các thuộc tính từ nhiều nguồn khác nhau theo một **thứ tự ưu tiên** nghiêm ngặt. Nguồn có độ ưu tiên cao hơn sẽ ghi đè lên giá trị của nguồn có độ ưu tiên thấp hơn. Dưới đây là một số nguồn phổ biến, xếp theo thứ tự ưu tiên **từ cao xuống thấp**:

1.  **Command-line arguments:** Các tham số truyền vào khi chạy ứng dụng (ví dụ: `java -jar app.jar --server.port=9000`).
2.  **Environment variables:** Các biến môi trường của hệ điều hành.
3.  **Các thuộc tính trong file `application.properties` hoặc `application.yml` được đóng gói trong file JAR.**
4.  **Các thuộc tính trong file `application-{profile}.properties` hoặc `application-{profile}.yml` được đóng gói trong file JAR.**
5.  **Các thuộc tính trong file `application.properties` hoặc `application.yml` nằm bên ngoài file JAR (cùng thư mục).**

> **Debug Tip:** Khi một cấu hình không hoạt động như mong đợi, hãy kiểm tra endpoint `/actuator/env`. Nó sẽ hiển thị tất cả các thuộc tính đang được áp dụng và chúng đến từ nguồn nào, giúp bạn biết được giá trị của mình có bị ghi đè ở đâu đó không.

#### Profiles

Profiles là cách để định nghĩa các nhóm cấu hình khác nhau và kích hoạt chúng trong các môi trường cụ thể.

*   **Cách định nghĩa:** Tạo các file cấu hình theo quy ước `application-{tên_profile}.yml` (hoặc `.properties`).
    *   `application-dev.yml`: Cấu hình cho môi trường phát triển (dùng H2 database, tắt caching).
    *   `application-prod.yml`: Cấu hình cho môi trường production (dùng PostgreSQL, bật caching, cấu hình log level INFO).
    *   `application.yml` (file chung): Chứa các cấu hình không thay đổi giữa các môi trường.
*   **Cách kích hoạt một Profile:**
    1.  **Trong `application.yml` (cách phổ biến khi phát triển):**
        ```yaml
        spring:
          profiles:
            active: dev
        ```
    2.  **Qua Command-line argument (ưu tiên cao nhất, thường dùng khi triển khai):**
        ```bash
        java -jar my-app.jar --spring.profiles.active=prod
        ```
    3.  **Qua Environment variable:**
        ```bash
        export SPRING_PROFILES_ACTIVE=prod
        java -jar my-app.jar
        ```

### 2. Ví dụ minh họa thực tế

**Tình huống:** Xây dựng một `AppInfoService` hiển thị thông tin về ứng dụng. Thông tin này sẽ khác nhau giữa môi trường `dev` và `prod`.

**1. Tạo các file cấu hình YAML:**

*   `src/main/resources/application.yml` (file chung)
    ```yaml
    # Đặt profile mặc định là dev
    spring:
      profiles:
        active: dev

    # Tiền tố chung cho cấu hình
    app:
      author: "The Team"
      contact-email: "contact@example.com"
    ```
*   `src/main/resources/application-dev.yml`
    ```yaml
    app:
      name: "My Awesome App (DEV)"
      environment: "Development Environment"
    server:
      port: 8081
    ```
*   `src/main/resources/application-prod.yml`
    ```yaml
    app:
      name: "My Awesome App"
      environment: "Production Environment"
    server:
      port: 80
    ```

**2. Tạo lớp POJO để hứng cấu hình (sử dụng `@ConfigurationProperties`):**

```java
// com/example/demo/config/AppProperties.java
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

@Component
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String name;
    private String environment;
    private String author;
    private String contactEmail;

    // Getters and Setters...
    // Bắt buộc phải có getters/setters để binding hoạt động

    @Override
    public String toString() {
        return "AppProperties{" +
               "name='" + name + '\'' +
               ", environment='" + environment + '\'' +
               ", author='" + author + '\'' +
               ", contactEmail='" + contactEmail + '\'' +
               '}';
    }
    
    // Getters & Setters
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getEnvironment() { return environment; }
    public void setEnvironment(String environment) { this.environment = environment; }
    public String getAuthor() { return author; }
    public void setAuthor(String author) { this.author = author; }
    public String getContactEmail() { return contactEmail; }
    public void setContactEmail(String contactEmail) { this.contactEmail = contactEmail; }
}
```

**3. Tạo một Service để sử dụng các thuộc tính này:**

```java
// com/example/demo/service/AppInfoService.java
import com.example.demo.config.AppProperties;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class AppInfoService {

    private final AppProperties appProperties;

    @Autowired
    public AppInfoService(AppProperties appProperties) {
        this.appProperties = appProperties;
    }

    public void printAppInfo() {
        System.out.println("--- Application Information ---");
        System.out.println(appProperties.toString());
        System.out.println("-----------------------------");
    }
}
```

**4. Chạy ứng dụng:**

```java
// Trong lớp main
@SpringBootApplication
public class DemoApplication implements CommandLineRunner { // Implement CommandLineRunner để chạy code sau khi app khởi động

    @Autowired
    private AppInfoService appInfoService;

    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }

    @Override
    public void run(String... args) throws Exception {
        appInfoService.printAppInfo();
    }
}
```

*   **Chạy bình thường (profile `dev`):**
    Output sẽ là: `name='My Awesome App (DEV)', environment='Development Environment', author='The Team', ...` và server chạy ở port `8081`.
*   **Chạy với profile `prod`:**
    Đóng gói thành file JAR (`mvn clean package`) và chạy bằng lệnh:
    `java -jar target/demo-0.0.1-SNAPSHOT.jar --spring.profiles.active=prod`
    Output sẽ là: `name='My Awesome App', environment='Production Environment', author='The Team', ...` và server chạy ở port `80`.

### 3. Mini Exercise

1.  Tạo một profile mới tên là `staging` bằng cách tạo file `application-staging.yml`.
2.  Trong file này, định nghĩa lại `app.name` thành "My Awesome App (STAGING)" và `server.port` thành `9090`.
3.  Chạy ứng dụng của bạn với profile `staging` được kích hoạt từ command line và xác nhận rằng thông tin và cổng server đã thay đổi đúng như cấu hình.

### 4. Quiz Question

**Câu hỏi:** Giả sử bạn có các cấu hình sau:

*   **`application.properties`:** `myapp.feature.enabled=false`
*   **`application-prod.properties`:** `myapp.feature.enabled=true`
*   Bạn chạy ứng dụng bằng lệnh: `java -jar app.jar --spring.profiles.active=prod --myapp.feature.enabled=false`

Giá trị của thuộc tính `myapp.feature.enabled` mà ứng dụng sẽ sử dụng là gì?

a) `true`, vì profile `prod` được kích hoạt.
b) `false`, vì giá trị trong `application.properties` là mặc định.
c) `false`, vì command-line argument có độ ưu tiên cao nhất.
d) Gây ra lỗi vì có sự xung đột cấu hình.

---
**Đáp án:** **c) `false`, vì command-line argument có độ ưu tiên cao nhất.**

**Giải thích:** Theo hệ thống phân cấp cấu hình của Spring Boot, command-line arguments đứng ở vị trí ưu tiên cao nhất. Nó sẽ ghi đè lên tất cả các giá trị được định nghĩa trong các file `application.properties`, kể cả các file profile-specific. Đây là một cơ chế rất hữu ích để thực hiện các thay đổi cấu hình nhanh chóng khi triển khai mà không cần build lại ứng dụng.

## Lesson 4: Spring Boot Actuator và Monitoring

### 1. Giải thích khái niệm chi tiết

#### Vấn đề: "Hộp đen" trong môi trường Production

Khi ứng dụng của bạn đã được triển khai lên môi trường production, nó trở thành một "hộp đen". Bạn không thể dễ dàng debug hay xem log như khi ở môi trường local. Hàng loạt câu hỏi quan trọng nảy sinh:
*   Ứng dụng có còn "sống" không?
*   Nó có "khỏe" không? Database có kết nối được không? Dịch vụ bên ngoài có phản hồi không?
*   Nó đang sử dụng bao nhiêu bộ nhớ, CPU?
*   Các cấu hình nào đang được áp dụng? Giá trị của một thuộc tính cụ thể là gì?
*   Có bao nhiêu request đang được xử lý? Tỷ lệ lỗi là bao nhiêu?

**Spring Boot Actuator** ra đời để trả lời tất cả những câu hỏi này. Nó là một "cửa sổ" để nhìn vào bên trong ứng dụng đang chạy của bạn, cung cấp các endpoint sẵn sàng cho mục đích giám sát và quản lý.

#### Actuator Endpoints

Actuator cung cấp một tập hợp các endpoint (chủ yếu qua HTTP) để bạn truy vấn thông tin. Để sử dụng, bạn chỉ cần thêm dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Dưới đây là các endpoint quan trọng nhất:

*   **`/actuator/health`**:
    *   Đây là endpoint quan trọng nhất, dùng để kiểm tra "sức khỏe" tổng thể của ứng dụng.
    *   Nó trả về trạng thái `UP` nếu mọi thứ đều ổn. Nếu có bất kỳ thành phần quan trọng nào gặp sự cố (ví dụ: mất kết nối database), nó sẽ trả về `DOWN`.
    *   Trạng thái này là một tổng hợp từ nhiều `HealthIndicator` khác nhau. Spring Boot tự động cung cấp các indicator cho database (`db`), dung lượng đĩa (`diskSpace`), RabbitMQ, Redis...

*   **`/actuator/metrics`**:
    *   Cung cấp một loạt các số liệu (metrics) chi tiết về ứng dụng. Ví dụ:
        *   `jvm.memory.used`: Lượng bộ nhớ JVM đang sử dụng.
        *   `system.cpu.usage`: Mức độ sử dụng CPU.
        *   `http.server.requests`: Số lượng request HTTP, thời gian phản hồi, trạng thái (200, 404, 500...).
    *   Đây là nền tảng cho việc thiết lập các hệ thống cảnh báo (alerting) và vẽ biểu đồ (dashboard).

*   **`/actuator/env`**:
    *   Cực kỳ hữu ích để debug cấu hình. Nó liệt kê tất cả các thuộc tính đang được `Environment` của Spring sử dụng.
    *   Nó cho bạn biết giá trị cuối cùng của một thuộc tính và nó đến từ nguồn nào (ví dụ: file `application.yml`, biến môi trường, command-line argument), giúp bạn gỡ lỗi khi cấu hình bị ghi đè.

*   **`/actuator/info`**:
    *   Hiển thị các thông tin chung, phi kỹ thuật về ứng dụng. Bạn có thể tự định nghĩa các thông tin này trong file `application.yml` thông qua các thuộc tính có tiền tố `info.`.
    *   Ví dụ: `info.app.name`, `info.app.version`, `info.build.version`...

#### Bảo mật và Tùy chỉnh Endpoint

**Cảnh báo an ninh:** Việc lộ các endpoint như `/env` hay `/heapdump` ra ngoài internet là cực kỳ nguy hiểm, vì chúng có thể chứa các thông tin nhạy cảm như mật khẩu, API keys.

Spring Boot tuân thủ nguyên tắc **"an toàn theo mặc định" (secure by default)**.
*   **Mặc định:** Chỉ có endpoint `/health` được bật và exposé qua web. (Một số phiên bản cũ hơn có thể exposé cả `/info`).
*   **Cách exposé thêm endpoint:** Bạn phải khai báo một cách tường minh trong `application.yml`:
    ```yaml
    management:
      endpoints:
        web:
          exposure:
            include: health,info,metrics,prometheus # Chỉ exposé các endpoint này
            # exclude: env # Hoặc loại trừ một endpoint cụ thể
    ```
*   **Tùy chỉnh đường dẫn và cổng:** Để tăng cường bảo mật, bạn nên chạy các endpoint của Actuator trên một cổng khác và một đường dẫn khác so với ứng dụng chính.
    ```yaml
    management:
      server:
        port: 9091 # Chạy Actuator trên cổng riêng
      endpoints:
        web:
          base-path: /management # Đổi base path từ /actuator thành /management
    ```

#### Micrometer và Tích hợp với Prometheus/Grafana

*   **Micrometer:** Actuator sử dụng một thư viện tên là Micrometer để thu thập metrics. Micrometer hoạt động như một "facade" (bộ mặt) cho metrics, tương tự như SLF4J cho logging. Nó cho phép bạn ghi lại metrics một lần, sau đó xuất chúng ra nhiều định dạng khác nhau để tương thích với các hệ thống giám sát khác nhau (Prometheus, Datadog, New Relic...).

*   **Luồng giám sát phổ biến với Prometheus & Grafana:**
    1.  **Trong Spring Boot:** Thêm dependency `micrometer-registry-prometheus`.
    2.  **Actuator:** Dependency này sẽ tự động tạo ra một endpoint mới: `/actuator/prometheus`. Endpoint này trả về metrics theo định dạng mà Prometheus có thể hiểu.
    3.  **Prometheus:** Là một hệ thống thu thập và lưu trữ metrics dưới dạng chuỗi thời gian (time-series). Bạn cấu hình Prometheus để nó định kỳ "cào" (scrape) dữ liệu từ endpoint `/actuator/prometheus` của ứng dụng.
    4.  **Grafana:** Là một công cụ trực quan hóa. Bạn kết nối Grafana với Prometheus và xây dựng các biểu đồ (dashboard) đẹp mắt để theo dõi sức khỏe và hiệu năng của ứng dụng theo thời gian thực.

#### Viết Custom Health Indicator

Đôi khi, "sức khỏe" của ứng dụng không chỉ phụ thuộc vào database hay ổ đĩa, mà còn phụ thuộc vào một dịch vụ bên thứ ba (ví dụ: một API thanh toán). Bạn có thể tự viết một `HealthIndicator` để kiểm tra trạng thái của dịch vụ này.

*   **Cách làm:**
    1.  Tạo một class `@Component` implement interface `HealthIndicator`.
    2.  Override phương thức `health()`.
    3.  Trong phương thức này, thực hiện logic kiểm tra (ví dụ: gọi API health check của dịch vụ kia).
    4.  Nếu ổn, trả về `Health.up().build()`.
    5.  Nếu có sự cố, trả về `Health.down().withDetail("error", "Lý do lỗi").build()`.
*   Spring Boot sẽ tự động phát hiện `HealthIndicator` này và tích hợp nó vào kết quả của endpoint `/actuator/health`.

### 2. Ví dụ minh họa thực tế

**Tình huống:** Giám sát một ứng dụng web đơn giản và thêm một health check tùy chỉnh để kiểm tra một dịch vụ giả lập.

**1. `pom.xml`:**

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-actuator</artifactId>
    </dependency>
</dependencies>
```

**2. `application.yml`:**

```yaml
# Cấu hình thông tin cho /actuator/info
info:
  app:
    name: "Actuator Demo App"
    version: "1.0.0"
    description: "Một ví dụ về Spring Boot Actuator"

# Cấu hình Actuator
management:
  endpoints:
    web:
      exposure:
        # Exposé các endpoint cần thiết
        include: health,info,metrics,env
  endpoint:
    health:
      # Hiển thị chi tiết các health indicator (ví dụ: diskSpace, customService)
      show-details: always
```

**3. Viết Custom Health Indicator:**

```java
// Một dịch vụ giả lập có thể bị "hỏng"
@Service
class MyExternalService {
    private boolean isHealthy = true;

    // Phương thức để thay đổi trạng thái cho mục đích demo
    public void setHealthy(boolean healthy) {
        isHealthy = healthy;
    }

    public boolean checkStatus() {
        return this.isHealthy;
    }
}

// Health Indicator cho dịch vụ trên
@Component
public class ExternalServiceHealthIndicator implements HealthIndicator {

    private final MyExternalService myExternalService;

    @Autowired
    public ExternalServiceHealthIndicator(MyExternalService myExternalService) {
        this.myExternalService = myExternalService;
    }

    @Override
    public Health health() {
        if (myExternalService.checkStatus()) {
            return Health.up().withDetail("service", "Dịch vụ ngoài đang hoạt động tốt").build();
        } else {
            return Health.down().withDetail("service", "Không thể kết nối tới dịch vụ ngoài").build();
        }
    }
}
```

**4. Chạy và kiểm tra:**
*   Khởi động ứng dụng.
*   Truy cập `http://localhost:8080/actuator/info`. Bạn sẽ thấy JSON chứa thông tin đã định nghĩa trong `application.yml`.
*   Truy cập `http://localhost:8080/actuator/health`. Ban đầu, bạn sẽ thấy trạng thái `UP` với chi tiết từ `diskSpace` và `externalService`.
*   Để thử nghiệm, bạn có thể tạo một endpoint để thay đổi trạng thái của `MyExternalService` và gọi nó, sau đó kiểm tra lại `/actuator/health` để thấy trạng thái chuyển sang `DOWN`.

### 3. Mini Exercise

1.  Thêm dependency `spring-boot-starter-data-jpa` và `com.h2database:h2` vào `pom.xml` của bạn.
2.  **Không cần cấu hình gì thêm.** Hãy khởi động lại ứng dụng.
3.  Truy cập endpoint `/actuator/health` và quan sát kết quả. Bạn thấy có gì mới xuất hiện trong phần `components` không? Điều này minh họa điều gì về cơ chế auto-configuration của Spring Boot Actuator?

### 4. Quiz Question

**Câu hỏi:** Bạn muốn bảo mật các endpoint của Actuator bằng cách chạy chúng trên một cổng khác (ví dụ: 8081) so với cổng ứng dụng chính (8080). Bạn sẽ sử dụng thuộc tính nào trong `application.yml`?

a) `server.port=8081`
b) `management.server.port=8081`
c) `actuator.port=8081`
d) `management.port=8081`

---
**Đáp án:** **b) `management.server.port=8081`**

**Giải thích:** Các thuộc tính để cấu hình Actuator nằm dưới tiền tố `management`. `management.server.port` được thiết kế đặc biệt để tách cổng của các endpoint quản lý ra khỏi cổng chính của ứng dụng (`server.port`), đây là một best practice về bảo mật trong môi trường production.

## Lesson 5: Logging và Error Handling

### 1. Giải thích khái niệm chi tiết

#### Logging trong Spring Boot

Logging là một trong những khía cạnh quan trọng nhất của bất kỳ ứng dụng nào, đặc biệt là ở môi trường production. Nó giúp bạn theo dõi luồng thực thi, chẩn đoán lỗi và phân tích hành vi của hệ thống.

**1. SLF4J, Logback, và Log4j2:**

*   **SLF4J (Simple Logging Facade for Java):** Đây không phải là một framework logging, mà là một **Facade (bộ mặt)**. Nó cung cấp một API logging chung (ví dụ: `Logger.info()`, `Logger.error()`). Code của bạn sẽ chỉ tương tác với API của SLF4J. Điều này giúp ứng dụng của bạn không bị phụ thuộc vào một implementation logging cụ thể. Bạn có thể thay đổi framework logging (từ Logback sang Log4j2) mà không cần sửa một dòng code Java nào.
*   **Logback:** Đây là implementation logging **mặc định** trong Spring Boot. Nó là "người kế nhiệm" của Log4j, được thiết kế để nhanh hơn và mạnh mẽ hơn. `spring-boot-starter-logging` (một dependency được kéo vào bởi hầu hết các starter khác như `starter-web`) sẽ tự động cấu hình SLF4J để sử dụng Logback.
*   **Log4j2:** Một framework logging mạnh mẽ khác, là một sự lựa chọn phổ biến để thay thế Logback.

**2. Tùy chỉnh cấu hình Log trong Spring Boot:**

Spring Boot cung cấp hai cách chính để tùy chỉnh logging:

*   **Cách đơn giản (qua `application.yml`):**
    Bạn có thể định nghĩa các cấu hình cơ bản trực tiếp trong file `application.yml` hoặc `.properties`. Cách này phù hợp cho các tùy chỉnh nhanh và đơn giản.
    ```yaml
    logging:
      level:
        # Đặt log level mặc định cho toàn bộ ứng dụng là INFO
        root: INFO
        # Đặt log level cho package cụ thể là DEBUG để xem chi tiết hơn
        com.example.demo: DEBUG
      file:
        # Ghi log ra file
        name: logs/my-app.log
    ```
    Các log level (từ thấp đến cao): `TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`. Khi bạn đặt một level, tất cả các log có level cao hơn cũng sẽ được hiển thị (ví dụ: đặt `INFO` sẽ hiển thị cả `INFO`, `WARN`, `ERROR`).

*   **Cách nâng cao (qua file cấu hình riêng):**
    Để có toàn quyền kiểm soát (định dạng log, chiến lược xoay vòng file log - rolling policy), bạn nên cung cấp một file cấu hình riêng. Spring Boot sẽ tự động nhận diện các file này nếu chúng nằm trong `src/main/resources`:
    *   Cho Logback: `logback-spring.xml`
    *   Cho Log4j2: `log4j2-spring.xml`

    **Tại sao lại là `-spring.xml`?** Spring Boot mở rộng các file cấu hình này, cho phép bạn sử dụng các tính năng như `<springProfile>` để có cấu hình log khác nhau cho các môi trường khác nhau (dev, prod).

**3. Cấu trúc Log trong ứng dụng Enterprise (MDC):**

Trong một hệ thống xử lý nhiều request đồng thời, các dòng log từ các request khác nhau sẽ bị xen kẽ, tạo thành một mớ hỗn độn.
*   **Vấn đề:** Làm sao để theo dõi toàn bộ hành trình của một request cụ thể?
*   **Giải pháp: MDC (Mapped Diagnostic Context).** MDC là một map được gắn với mỗi luồng (thread-local). Bạn có thể đặt các thông tin định danh vào MDC ở đầu request, và sau đó cấu hình logger để tự động in các thông tin này ra mỗi dòng log.
*   **Correlation ID (hoặc Trace ID):** Là một ID duy nhất được tạo ra cho mỗi request. Nó được đặt vào MDC. Bằng cách lọc log theo ID này, bạn có thể thấy toàn bộ câu chuyện của một request, ngay cả khi nó đi qua nhiều microservices khác nhau.

#### Error Handling trong Spring Boot

**1. Cơ chế xử lý lỗi mặc định:**

*   Khi một exception không được xử lý (`uncaught exception`) xảy ra trong một controller, Spring Boot sẽ tự động bắt lấy nó.
*   Nếu request đến từ trình duyệt, nó sẽ hiển thị trang **"Whitelabel Error Page"**.
*   Nếu request là một lời gọi API (có header `Accept: application/json`), nó sẽ trả về một response JSON chứa các thông tin lỗi cơ bản (timestamp, status, error, path).
*   **Cơ chế nội bộ:** Spring Boot cung cấp một bean tên là `BasicErrorController`. Bean này đăng ký một mapping cho `/error` và xử lý tất cả các lỗi được chuyển hướng đến đó.

**2. Tùy chỉnh xử lý lỗi (Best Practice):**

Trang lỗi mặc định không thân thiện với người dùng và response JSON mặc định không đủ thông tin. Cách tốt nhất để tùy chỉnh là sử dụng `@ControllerAdvice` (hoặc `@RestControllerAdvice` cho API).

*   `@ControllerAdvice`: Là một annotation đánh dấu một class là một "bộ xử lý" toàn cục cho các controller. Nó cho phép bạn áp dụng cùng một logic xử lý exception cho toàn bộ ứng dụng ở một nơi duy nhất.
*   `@ExceptionHandler`: Đặt trên một phương thức bên trong lớp `@ControllerAdvice`. Nó khai báo rằng phương thức này sẽ chịu trách nhiệm xử lý một loại exception cụ thể.
*   **Luồng hoạt động:** Khi một exception (ví dụ `UserNotFoundException`) được ném ra từ bất kỳ controller nào, Spring sẽ tìm trong các lớp `@ControllerAdvice` xem có phương thức nào được đánh dấu `@ExceptionHandler(UserNotFoundException.class)` hay không. Nếu có, nó sẽ gọi phương thức đó để xử lý.

### 2. Ví dụ minh họa thực tế

**Tình huống:** Xây dựng một API lấy thông tin người dùng. API có thể ném `UserNotFoundException`. Chúng ta sẽ thêm logging với `traceId` và tạo một response lỗi JSON tùy chỉnh.

**1. Thêm Filter để xử lý MDC (Correlation ID):**

```java
import org.slf4j.MDC;
import org.springframework.stereotype.Component;
import javax.servlet.*;
import java.io.IOException;
import java.util.UUID;

@Component
public class MdcFilter implements Filter {
    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain)
            throws IOException, ServletException {
        try {
            // Tạo một traceId duy nhất cho mỗi request
            String traceId = UUID.randomUUID().toString();
            MDC.put("traceId", traceId);
            chain.doFilter(request, response);
        } finally {
            // Dọn dẹp MDC sau khi request kết thúc để tránh memory leak
            MDC.clear();
        }
    }
}
```

**2. Cấu hình `logback-spring.xml`:**

Tạo file `src/main/resources/logback-spring.xml`:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml"/>
    
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <!-- Thêm %X{traceId} vào pattern để in ra traceId từ MDC -->
            <!-- [%15.15t] là tên thread, %-40.40logger{39} là tên logger -->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%15.15t] %-5level %-40.40logger{39} [%X{traceId}] - %msg%n</pattern>
        </encoder>
    </appender>

    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
    
    <logger name="com.example.demo" level="DEBUG"/>
</configuration>
```

**3. Tạo Exception và Error Response DTO:**

```java
// Custom Exception
public class UserNotFoundException extends RuntimeException {
    public UserNotFoundException(String message) {
        super(message);
    }
}

// Custom Error DTO
public class ErrorResponse {
    private final int status;
    private final String message;
    private final long timestamp;

    // Constructor, Getters...
}
```

**4. Tạo Global Exception Handler:**

```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleUserNotFound(UserNotFoundException ex) {
        ErrorResponse errorResponse = new ErrorResponse(
            HttpStatus.NOT_FOUND.value(),
            ex.getMessage(),
            System.currentTimeMillis()
        );
        return new ResponseEntity<>(errorResponse, HttpStatus.NOT_FOUND);
    }
}
```

**5. Tạo Service và Controller:**

```java
@Service
public class UserService {
    private static final Logger log = LoggerFactory.getLogger(UserService.class);
    
    public String findUserById(int id) {
        log.info("Bắt đầu tìm kiếm user với id={}", id);
        if (id == 1) {
            log.debug("Đã tìm thấy user id=1");
            return "John Doe";
        } else {
            log.warn("Không tìm thấy user id={}. Sẽ ném exception.", id);
            throw new UserNotFoundException("User not found with id: " + id);
        }
    }
}

@RestController
public class UserController {
    private final UserService userService;
    // ... constructor injection ...
    
    @GetMapping("/users/{id}")
    public String getUser(@PathVariable int id) {
        return userService.findUserById(id);
    }
}
```

**Kết quả:**
*   **Khi gọi `/users/1`:**
    *   Console log sẽ có dạng:
        ```
        2025-10-15 12:30:00.123 [http-nio-8080-exec-1] INFO  c.e.d.service.UserService   [a1b2c3d4-e5f6-....] - Bắt đầu tìm kiếm user với id=1
        2025-10-15 12:30:00.124 [http-nio-8080-exec-1] DEBUG c.e.d.service.UserService   [a1b2c3d4-e5f6-....] - Đã tìm thấy user id=1
        ```
    *   Response: `John Doe`
*   **Khi gọi `/users/2`:**
    *   Console log:
        ```
        2025-10-15 12:31:00.456 [http-nio-8080-exec-2] INFO  c.e.d.service.UserService   [f9e8d7c6-b5a4-....] - Bắt đầu tìm kiếm user với id=2
        2025-10-15 12:31:00.457 [http-nio-8080-exec-2] WARN  c.e.d.service.UserService   [f9e8d7c6-b5a4-....] - Không tìm thấy user id=2. Sẽ ném exception.
        ```
    *   Response (Status 404):
        ```json
        {
          "status": 404,
          "message": "User not found with id: 2",
          "timestamp": 1665840660457
        }
        ```

### 3. Mini Exercise

1.  Trong `GlobalExceptionHandler`, hãy thêm một phương thức mới để xử lý `IllegalArgumentException`.
2.  Phương thức này nên trả về một `ErrorResponse` tương tự nhưng với HTTP status là `400 BAD REQUEST`.
3.  Trong `UserController`, tạo một endpoint mới mà khi gọi sẽ cố tình ném ra một `IllegalArgumentException` để kiểm tra handler của bạn.

### 4. Quiz Question

**Câu hỏi:** Bạn có cả hai file `application.yml` (định nghĩa `logging.level.com.example=INFO`) và `logback-spring.xml` (định nghĩa `<logger name="com.example" level="DEBUG"/>`). Khi ứng dụng chạy, log level cho package `com.example` sẽ là gì?

a) `INFO`, vì `application.yml` được ưu tiên hơn.
b) `DEBUG`, vì file cấu hình logging chuyên dụng (`logback-spring.xml`) sẽ ghi đè lên các thuộc tính chung trong `application.yml`.
c) Cả hai sẽ được áp dụng, gây ra lỗi khởi động.
d) `WARN`, vì đây là log level mặc định của root.

---
**Đáp án:** **b) `DEBUG`, vì file cấu hình logging chuyên dụng (`logback-spring.xml`) sẽ ghi đè lên các thuộc tính chung trong `application.yml`.**

**Giải thích:** Spring Boot sẽ ưu tiên sử dụng các file cấu hình logging chuyên dụng (`logback-spring.xml`, `log4j2-spring.xml`...). Nếu một trong các file này tồn tại, các thuộc tính `logging.*` trong `application.yml` sẽ bị bỏ qua. Điều này cho phép bạn kiểm soát logging một cách chi tiết và tập trung ở một nơi duy nhất.

## Lesson 6: Testing trong Spring Boot

### 1. Giải thích khái niệm chi tiết

#### Vấn đề: Tại sao testing lại quan trọng và phức tạp?

Viết code chỉ là một phần, đảm bảo code chạy đúng trong mọi tình huống mới là điều quan trọng. Testing giúp chúng ta tự tin khi thay đổi code, phát hiện lỗi sớm và xây dựng các ứng dụng đáng tin cậy.

Trong Spring Framework truyền thống, việc viết test, đặc biệt là integration test, khá cồng kềnh. Bạn phải tự quản lý việc khởi tạo `ApplicationContext`, cấu hình các bean, xử lý các dependency, và đảm bảo môi trường test sạch sẽ sau mỗi lần chạy.

Spring Boot tiếp tục triết lý "Convention over Configuration" của mình vào cả lĩnh vực testing, giúp việc này trở nên đơn giản và hiệu quả hơn rất nhiều.

#### `spring-boot-starter-test`

Đây là starter mặc định cho việc testing, được tự động thêm vào khi bạn tạo dự án từ `start.spring.io`. Nó cung cấp một bộ công cụ "tất cả trong một" đã được kiểm chứng tương thích với nhau:
*   **JUnit 5:** Framework testing tiêu chuẩn cho Java.
*   **Spring Test & Spring Boot Test:** Các module cung cấp sự tích hợp sâu giữa Spring và framework testing (ví dụ: quản lý `ApplicationContext`, tiêm bean vào test class).
*   **AssertJ:** Một thư viện assertion mạnh mẽ với cú pháp "fluent" (lưu loát), giúp code test dễ đọc hơn (ví dụ: `assertThat(user.getName()).isEqualTo("John")`).
*   **Mockito:** Framework mocking phổ biến nhất, giúp bạn tạo các đối tượng giả (mock object) để cô lập thành phần đang được test.

#### Test Slices (Kiểm thử theo lát cắt)

Đây là một trong những khái niệm mạnh mẽ nhất của Spring Boot Test. Thay vì phải khởi động *toàn bộ* ứng dụng (`ApplicationContext` đầy đủ) cho mỗi bài test, bạn có thể chỉ cần tải lên một "lát cắt" (slice) của ứng dụng liên quan đến phần bạn muốn test. Điều này giúp test chạy **nhanh hơn rất nhiều** và tập trung hơn.

Các "slice annotation" phổ biến nhất:

*   **`@DataJpaTest` (Slice cho tầng Persistence):**
    *   **Nó làm gì?** Nó chỉ cấu hình và tải lên các thành phần liên quan đến JPA và tầng dữ liệu: `DataSource`, `EntityManager`, các bean được đánh dấu `@Repository`.
    *   **Nó KHÔNG làm gì?** Nó sẽ không tải các bean `@Service`, `@Controller`, `@Component` khác.
    *   **Tính năng đặc biệt:**
        1.  **Sử dụng In-memory Database:** Theo mặc định, nó sẽ cố gắng cấu hình một cơ sở dữ liệu trong bộ nhớ (như H2) để chạy test, giúp test nhanh và không ảnh hưởng đến database thật.
        2.  **Tự động Rollback:** Mỗi phương thức test được tự động bọc trong một transaction và sẽ được rollback sau khi chạy xong. Điều này đảm bảo mỗi test đều bắt đầu với một database sạch, không bị ảnh hưởng bởi test trước đó.

*   **`@WebMvcTest` (Slice cho tầng Web):**
    *   **Nó làm gì?** Nó chỉ tải lên các thành phần của tầng Web MVC: các `@Controller`, `@RestController`, `@ControllerAdvice`, `Filter`, `WebMvcConfigurer`, Jackson configuration...
    *   **Nó KHÔNG làm gì?** Nó không tải các bean `@Service` hay `@Repository`.
    *   **Vấn đề:** Controller thường phụ thuộc vào Service. Nếu Service không được tải, làm sao Controller hoạt động? Đây là lúc `@MockBean` tỏa sáng.

*   **`@SpringBootTest` (Integration Test toàn diện):**
    *   **Nó làm gì?** Đây không phải là một "slice". Nó sẽ tải lên **toàn bộ `ApplicationContext`**, giống như khi bạn chạy ứng dụng thật.
    *   **Khi nào dùng?** Khi bạn cần thực hiện một bài test tích hợp (integration test) từ đầu đến cuối, kiểm tra sự tương tác giữa nhiều tầng khác nhau (ví dụ: từ Controller -> Service -> Repository).
    *   **Nhược điểm:** Chạy chậm hơn đáng kể so với các slice test.
    *   **Best Practice:** Ưu tiên sử dụng các slice test (`@DataJpaTest`, `@WebMvcTest`) để kiểm thử các đơn vị một cách độc lập. Chỉ sử dụng `@SpringBootTest` cho các kịch bản tích hợp quan trọng.

#### Mocking Dependencies với `@MockBean`

*   **`@MockBean` là gì?** Đây là một annotation của Spring Boot, kết hợp sức mạnh của Spring và Mockito. Nó ra lệnh cho Spring: "Tại vị trí của bean này trong `ApplicationContext`, đừng tạo bean thật, hãy thay thế nó bằng một mock object của Mockito".
*   **Tại sao nó quan trọng?** Nó là công cụ thiết yếu để cô lập các "slice". Trong một `@WebMvcTest`, controller của bạn cần một `UserService`. Bạn sẽ dùng `@MockBean` để cung cấp một `UserService` giả. Sau đó, bạn có thể dùng Mockito để định nghĩa hành vi cho mock này (ví dụ: `when(userService.findById(1)).thenReturn(user)`), cho phép bạn kiểm thử logic của controller một cách hoàn toàn độc lập với logic của service.

### 2. Ví dụ minh họa thực tế

**Tình huống:** Test một flow CRUD đơn giản cho `User`.

**1. Entity và Repository:**
```java
// User.java, UserRepository.java (giống các bài trước)
@Entity
public class User { @Id private Long id; private String name; /* getters, setters */ }
public interface UserRepository extends JpaRepository<User, Long> {}
```

**2. Test tầng Persistence với `@DataJpaTest`:**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;
import org.springframework.boot.test.autoconfigure.orm.jpa.TestEntityManager;
import static org.assertj.core.api.Assertions.assertThat;

@DataJpaTest
public class UserRepositoryTest {

    @Autowired
    private TestEntityManager entityManager; // Helper để thao tác với DB trong test

    @Autowired
    private UserRepository userRepository;

    @Test
    public void whenFindByName_thenReturnUser() {
        // given - sắp xếp dữ liệu
        User alex = new User(1L, "alex");
        entityManager.persistAndFlush(alex); // Lưu user vào DB test

        // when - thực hiện hành động
        User found = userRepository.findByName("alex");

        // then - kiểm tra kết quả
        assertThat(found.getName()).isEqualTo(alex.getName());
    }
}
```
*   **Giải thích:** Test này chỉ tải lên môi trường JPA. Nó dùng `TestEntityManager` để tạo dữ liệu, sau đó gọi phương thức của `userRepository` và xác nhận kết quả. Sau khi test này chạy xong, user "alex" sẽ bị xóa khỏi DB do cơ chế rollback tự động.

**3. Service và Controller:**
```java
@Service
public class UserService {
    private final UserRepository userRepository;
    public UserService(UserRepository repo) { this.userRepository = repo; }
    public User findUserById(Long id) {
        return userRepository.findById(id).orElseThrow(() -> new UserNotFoundException("..."));
    }
}

@RestController
public class UserController {
    private final UserService userService;
    public UserController(UserService service) { this.userService = service; }
    @GetMapping("/users/{id}")
    public User getUserById(@PathVariable Long id) {
        return userService.findUserById(id);
    }
}
```

**4. Test tầng Web với `@WebMvcTest` và `@MockBean`:**
```java
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.BDDMockito.given;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

// Chỉ định controller cần test để tăng tốc độ
@WebMvcTest(UserController.class) 
public class UserControllerTest {

    @Autowired
    private MockMvc mockMvc; // Công cụ chính để thực hiện các request HTTP giả

    @MockBean // Spring sẽ tạo mock cho UserService và tiêm vào UserController
    private UserService userService;

    @Test
    public void givenUser_whenGetUserById_thenReturnsJson() throws Exception {
        // given - định nghĩa hành vi của mock
        User alex = new User(1L, "alex");
        given(userService.findUserById(1L)).willReturn(alex);

        // when & then - thực hiện request và kiểm tra response
        mockMvc.perform(get("/users/1"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.name").value("alex"));
    }
}
```
*   **Giải thích:** `@WebMvcTest` chỉ tải `UserController`. `UserService` thật không tồn tại, thay vào đó là một `@MockBean`. Chúng ta dùng `given()` của Mockito để "dạy" cho mock biết phải trả về user "alex" khi `findUserById(1L)` được gọi. Sau đó, `MockMvc` thực hiện một request GET ảo và chúng ta kiểm tra HTTP status và nội dung JSON trả về.

### 3. Mini Exercise

Dựa trên ví dụ `UserControllerTest`, hãy viết một bài test mới cho kịch bản **không tìm thấy user**.

1.  Viết một phương thức test tên là `whenUserNotFound_thenReturns404`.
2.  Sử dụng `given()` của Mockito để định nghĩa rằng khi `userService.findUserById()` được gọi với một ID không tồn tại (ví dụ: 99L), nó sẽ ném ra `UserNotFoundException`. (Gợi ý: dùng `willThrow()`).
3.  Sử dụng `mockMvc` để thực hiện request `GET /users/99`.
4.  Sử dụng `andExpect()` để xác nhận rằng HTTP status trả về là `404 Not Found`.

### 4. Quiz Question

**Câu hỏi:** Trong một class được đánh dấu `@WebMvcTest`, bạn cần một dependency là `UserService`. Thay vì dùng `@MockBean`, bạn lại dùng `@Mock` (một annotation của Mockito) như sau:

```java
@WebMvcTest(UserController.class)
public class MyControllerTest {
    @Mock
    private UserService userService; // Dùng @Mock thay vì @MockBean
    // ...
}
```

Điều gì sẽ xảy ra khi bạn chạy test này?

a) Test chạy bình thường, vì `@Mock` và `@MockBean` là như nhau.
b) Test chạy bình thường, nhưng `userService` sẽ là `null`.
c) Test sẽ **thất bại khi khởi tạo**, Spring báo lỗi không tìm thấy bean `UserService` để tiêm vào `UserController`.
d) Test chạy, nhưng bạn phải tự tiêm mock `userService` vào controller bằng tay.

---
**Đáp án:** **c) Test sẽ thất bại khi khởi tạo, Spring báo lỗi không tìm thấy bean `UserService` để tiêm vào `UserController`.**

**Giải thích:** `@Mock` là một annotation của Mockito. Nó chỉ tạo ra một đối tượng mock, nhưng **Spring không hề biết về sự tồn tại của nó**. `@WebMvcTest` khi khởi tạo `UserController` sẽ cố gắng tìm một bean `UserService` trong `ApplicationContext` để tiêm vào. Vì không có bean `UserService` thật (do đây là slice test) và cũng không có `@MockBean` nào được đăng ký, Spring sẽ báo lỗi `NoSuchBeanDefinitionException` hoặc lỗi tương tự. `@MockBean` là công cụ đặc biệt của Spring Boot để vừa tạo mock, vừa đăng ký mock đó vào `ApplicationContext` để giải quyết các dependency.

## Lesson 7: Packaging và Deployment

### 1. Giải thích khái niệm chi tiết

#### Cơ chế Embedded Server (Máy chủ nhúng)

Đây là một trong những tính năng "thay đổi cuộc chơi" của Spring Boot.

*   **Cách truyền thống:** Bạn build ứng dụng của mình thành một file **WAR** (Web Application Archive). Sau đó, bạn cần cài đặt và cấu hình một máy chủ ứng dụng Java (Servlet Container) riêng biệt như Apache Tomcat, JBoss, hoặc Jetty. Cuối cùng, bạn "triển khai" (deploy) file WAR đó lên máy chủ.
*   **Cách của Spring Boot:** Thay vì ứng dụng được triển khai *trên* máy chủ, máy chủ lại được nhúng *bên trong* ứng dụng của bạn.
    *   **Nó là gì?** Embedded Server (ví dụ: Tomcat) không phải là một chương trình cài đặt bên ngoài, mà chỉ là một tập hợp các file JAR (thư viện).
    *   **Nó hoạt động như thế nào?** Khi bạn thêm `spring-boot-starter-web`, nó sẽ kéo theo `spring-boot-starter-tomcat`. Spring Boot Auto-Configuration sẽ phát hiện các lớp của Tomcat trong classpath, tự động tạo một bean `TomcatServletWebServerFactory`, và khởi động máy chủ này khi ứng dụng chạy. Ứng dụng của bạn sẽ tự mở một cổng mạng (mặc định 8080) và lắng nghe các request HTTP.
    *   **Các lựa chọn:** Tomcat là mặc định. Bạn có thể dễ dàng chuyển sang Jetty hoặc Undertow (một lựa chọn hiệu năng cao, nhẹ hơn) bằng cách loại bỏ `starter-tomcat` và thêm `starter-jetty` hoặc `starter-undertow` vào `pom.xml`.

**Lợi ích:** Sự đơn giản. Toàn bộ ứng dụng của bạn, bao gồm cả máy chủ, được đóng gói vào một file duy nhất. Bạn không cần lo lắng về việc cài đặt hay cấu hình máy chủ trên môi trường production.

#### JAR vs. WAR Packaging

Spring Boot hỗ trợ cả hai kiểu đóng gói, nhưng ưu tiên mạnh mẽ cho JAR.

1.  **Executable JAR (Fat JAR):**
    *   Đây là **cách làm mặc định và được khuyên dùng**.
    *   **Nó là gì?** `spring-boot-maven-plugin` sẽ tạo ra một file JAR "béo" (fat JAR). File này không chỉ chứa code đã biên dịch của bạn (`.class` files), mà còn chứa tất cả các dependency (`.jar` files của thư viện) và cả embedded server.
    *   **Cách chạy:** Cực kỳ đơn giản: `java -jar my-app-0.0.1-SNAPSHOT.jar`.
    *   **Ưu điểm:** Tự chứa, di động, lý tưởng cho kiến trúc microservices và các nền tảng container như Docker.

2.  **WAR (Web Application Archive):**
    *   **Khi nào dùng?** Khi bạn bắt buộc phải triển khai ứng dụng lên một máy chủ Servlet Container truyền thống đã có sẵn (ví dụ do yêu cầu của công ty hoặc hệ thống cũ).
    *   **Cách cấu hình:**
        1.  Trong `pom.xml`, thay đổi `<packaging>jar</packaging>` thành `<packaging>war</packaging>`.
        2.  Đánh dấu dependency của embedded server là `provided`, để báo cho Maven rằng máy chủ bên ngoài sẽ cung cấp các thư viện này rồi, không cần đóng gói vào file WAR nữa.
            ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-tomcat</artifactId>
                <scope>provided</scope>
            </dependency>
            ```
        3.  Sửa lớp Application chính để kế thừa từ `SpringBootServletInitializer`. Điều này tạo ra một "cầu nối" để máy chủ bên ngoài có thể khởi động ứng dụng Spring Boot.
            ```java
            @SpringBootApplication
            public class DemoApplication extends SpringBootServletInitializer {
                // ... main method ...
            }
            ```

#### Dockerize một ứng dụng Spring Boot

Docker là công nghệ container hóa hàng đầu, cho phép bạn đóng gói ứng dụng và tất cả các dependency của nó vào một "container" di động, đảm bảo ứng dụng chạy nhất quán trên mọi môi trường.

*   **Dockerfile:** Là một file văn bản chứa các chỉ dẫn để build một Docker image.
*   **Best Practice: Multi-stage builds.** Kỹ thuật này giúp tạo ra các Docker image nhỏ gọn và an toàn hơn.
    1.  **Giai đoạn 1 (Builder):** Sử dụng một image chứa đầy đủ công cụ build (như JDK, Maven). Trong giai đoạn này, ta sẽ biên dịch code và build file JAR.
    2.  **Giai đoạn 2 (Final):** Sử dụng một image tối giản chỉ chứa môi trường để chạy (như JRE). Ta chỉ copy file JAR đã được build từ giai đoạn 1 vào image này.
    *   **Lợi ích:** Image cuối cùng không chứa mã nguồn, Maven, hay JDK, làm nó nhỏ hơn hàng trăm MB và giảm bề mặt tấn công (ít công cụ để hacker khai thác hơn).

### 2. Ví dụ minh họa thực tế

**Tình huống:** Đóng gói ứng dụng REST API của chúng ta vào một Docker image sẵn sàng để triển khai.

**1. Đảm bảo `pom.xml` có `spring-boot-maven-plugin`:**
Plugin này là cần thiết để tạo ra executable JAR. Nó thường đã có sẵn nếu bạn tạo dự án từ `start.spring.io`.

```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

**2. Tạo `Dockerfile` ở thư mục gốc của dự án:**

```dockerfile
# ----- Giai đoạn 1: Build -----
# Sử dụng image chứa Maven và JDK 11
FROM maven:3.8.4-openjdk-11 AS builder

# Đặt thư mục làm việc bên trong container
WORKDIR /app

# Copy file pom.xml và source code vào container
COPY pom.xml .
COPY src ./src

# Chạy lệnh Maven để build ra file JAR. 
# -DskipTests để bỏ qua việc chạy test, giúp build nhanh hơn.
RUN mvn package -DskipTests

# ----- Giai đoạn 2: Run -----
# Sử dụng một image JRE tối giản để chạy
FROM openjdk:11-jre-slim

# Đặt thư mục làm việc
WORKDIR /app

# Copy file JAR đã được build từ giai đoạn 'builder' vào image hiện tại
COPY --from=builder /app/target/*.jar app.jar

# Mở cổng 8080 để bên ngoài có thể truy cập vào ứng dụng
EXPOSE 8080

# Lệnh để khởi động ứng dụng khi container chạy
ENTRYPOINT ["java", "-jar", "app.jar"]
```

**3. Build và Chạy Docker image:**

Mở terminal ở thư mục gốc của dự án và chạy các lệnh sau:

*   **Build image:**
    ```bash
    # Lệnh build. -t đặt tên cho image là 'my-spring-app'
    docker build -t my-spring-app .
    ```

*   **Chạy container từ image vừa build:**
    ```bash
    # Lệnh run. -p 8080:8080 ánh xạ cổng 8080 của máy host vào cổng 8080 của container.
    # --name đặt tên cho container là 'my-app-container'
    docker run -p 8080:8080 --name my-app-container my-spring-app
    ```

Bây giờ, ứng dụng Spring Boot của bạn đang chạy bên trong một container. Bạn có thể truy cập các endpoint (ví dụ: `http://localhost:8080/users/1`) từ trình duyệt hoặc Postman như bình thường.

### 3. Mini Exercise

Bạn đã có một ứng dụng Spring Boot được Docker hóa. Bây giờ hãy nâng cấp nó để chạy cùng với một database PostgreSQL, cũng trong một container, sử dụng `docker-compose`.

1.  Tạo một file tên là `docker-compose.yml` ở thư mục gốc.
2.  Định nghĩa hai services: `app` (cho ứng dụng Spring Boot) và `db` (cho PostgreSQL).
3.  Service `app` nên build từ `Dockerfile` của bạn.
4.  Service `db` nên sử dụng image `postgres:13`.
5.  **Nhiệm vụ:** Cấu hình các **biến môi trường (environment variables)** cho service `app` để nó có thể kết nối tới service `db`. (Gợi ý: URL của database sẽ là `jdbc:postgresql://db:5432/mydatabase`, với `db` là tên của service database).

### 4. Quiz Question

**Câu hỏi:** Đâu là ưu điểm chính của việc đóng gói ứng dụng Spring Boot thành một executable JAR so với một file WAR truyền thống?

a) Các file JAR luôn có kích thước nhỏ hơn file WAR.
b) Chỉ có file JAR mới hỗ trợ Spring Boot Actuator.
c) File JAR là một đơn vị tự chứa, bao gồm cả máy chủ nhúng, cho phép chạy ứng dụng bằng một lệnh `java -jar` đơn giản mà không cần máy chủ bên ngoài.
d) File JAR hỗ trợ cấu hình bằng YAML, còn file WAR thì không.

---
**Đáp án:** **c) File JAR là một đơn vị tự chứa, bao gồm cả máy chủ nhúng, cho phép chạy ứng dụng bằng một lệnh `java -jar` đơn giản mà không cần máy chủ bên ngoài.**

**Giải thích:** Đây là lợi ích cốt lõi. Sự đơn giản trong việc triển khai và vận hành là lý do chính khiến mô hình executable JAR trở nên cực kỳ phổ biến, đặc biệt trong thế giới microservices và container.

---

## Tổng kết chương: Spring Boot

### Bảng tổng hợp các khái niệm chính

| Khái niệm | Vai trò và Mô tả | Khi nên dùng | Lưu ý quan trọng |
| :--- | :--- | :--- | :--- |
| **Auto-Configuration** | "Phép màu" của Spring Boot. Tự động cấu hình các bean dựa trên các thư viện trong classpath và các thuộc tính được định nghĩa. | Luôn luôn. Đây là nền tảng của Spring Boot. | Hoạt động dựa trên các annotation `@Conditional...`. Bạn có thể vô hiệu hóa một auto-configuration cụ thể nếu cần. |
| **Starters** | Các file POM tiện lợi, gom nhóm các dependency cần thiết cho một chức năng cụ thể (web, data-jpa, actuator...). | Luôn sử dụng starter thay vì thêm dependency thủ công để đảm bảo tính tương thích và đơn giản hóa `pom.xml`. | Dùng `<exclusions>` để loại bỏ các dependency bắc cầu không mong muốn (ví dụ: đổi server, đổi framework logging). |
| **`@ConfigurationProperties`** | Liên kết (bind) một nhóm thuộc tính từ file `.yml`/`.properties` vào một đối tượng Java (POJO) một cách type-safe. | Cách tốt nhất để quản lý cấu hình có cấu trúc. Ưu tiên sử dụng thay cho `@Value` cho nhiều thuộc tính liên quan. | Class POJO phải có getters và setters. Hỗ trợ validation. |
| **Profiles** | Cho phép định nghĩa các cấu hình khác nhau cho các môi trường khác nhau (dev, test, prod) và kích hoạt chúng khi cần. | Bắt buộc phải dùng trong các dự án thực tế để quản lý cấu hình giữa các môi trường. | Kích hoạt qua `spring.profiles.active`. Cấu hình từ command-line hoặc biến môi trường sẽ ghi đè lên file properties. |
| **Actuator** | Cung cấp các endpoint (health, metrics, env...) để giám sát và quản lý ứng dụng đang chạy trong môi trường production. | Luôn bật Actuator trong các dự án thực tế. Endpoint `/health` và `/prometheus` là quan trọng nhất. | **Bảo mật!** Chỉ exposé các endpoint cần thiết và nên chạy chúng trên một cổng riêng (`management.server.port`). |
| **`@RestControllerAdvice`** | Cơ chế xử lý exception toàn cục cho các REST API, giúp tập trung logic xử lý lỗi vào một nơi duy nhất. | Luôn sử dụng để cung cấp các response lỗi nhất quán và thân thiện cho client thay vì trang lỗi mặc định. | Kết hợp với `@ExceptionHandler` để xử lý các loại exception cụ thể. |
| **Test Slices (`@WebMvcTest`, `@DataJpaTest`)** | Các annotation cho phép chỉ tải lên một "lát cắt" của ứng dụng để test, giúp test chạy nhanh và tập trung hơn. | Ưu tiên dùng slice test để kiểm thử các tầng độc lập. `@WebMvcTest` cho controller, `@DataJpaTest` cho repository. | Cần sử dụng `@MockBean` để giả lập các dependency không thuộc về "lát cắt" đó (ví dụ: mock Service trong `@WebMvcTest`). |
| **`@SpringBootTest`** | Chạy một bài test tích hợp bằng cách tải lên toàn bộ `ApplicationContext` của ứng dụng. | Dùng cho các bài test end-to-end quan trọng để kiểm tra sự tương tác giữa nhiều tầng. | Chạy chậm hơn slice test, nên hạn chế sử dụng cho các kịch bản thực sự cần thiết. |
| **Embedded Server & Executable JAR** | Spring Boot nhúng máy chủ (Tomcat) vào bên trong ứng dụng và đóng gói thành một file JAR duy nhất có thể tự chạy. | Đây là cách đóng gói và triển khai tiêu chuẩn cho các ứng dụng microservices và container. | `java -jar my-app.jar`. Cực kỳ đơn giản và di động. |

