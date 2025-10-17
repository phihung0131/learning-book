## Lesson 1: Giới thiệu và Kiến trúc Spring Security

### 1. Giải thích khái niệm chi tiết

#### Spring Security là gì và vấn đề nó giải quyết?

Trong bất kỳ ứng dụng nào, có hai câu hỏi an ninh cơ bản luôn phải được trả lời:

1.  **"Bạn là ai?"** - Đây là quá trình **Xác thực (Authentication)**.
2.  **"Bạn được phép làm gì?"** - Đây là quá trình **Ủy quyền / Phân quyền (Authorization)**.

Việc tự xây dựng một framework để xử lý các vấn đề này từ đầu là cực kỳ phức tạp, tốn thời gian và rất dễ mắc lỗi bảo mật nghiêm trọng. Spring Security là một framework mạnh mẽ và linh hoạt, cung cấp một giải pháp toàn diện, đã được kiểm chứng cho việc xác thực và ủy quyền trong các ứng dụng Java. Nó giúp bạn bảo vệ các endpoint, các phương thức service, và quản lý danh tính người dùng một cách an toàn.

#### Cấu trúc và Cơ chế hoạt động: Filter Chain

Đây là khái niệm **cốt lõi và quan trọng nhất** của Spring Security.

*   **Vấn đề:** Làm thế nào để "chặn" một request HTTP trước khi nó đến controller để kiểm tra xem request đó có hợp lệ hay không?
*   **Giải pháp của Java Servlet:** Sử dụng một chuỗi các **Filter**. Mỗi filter là một mảnh code nhỏ thực hiện một nhiệm vụ cụ thể (ví dụ: kiểm tra header, giải mã token, kiểm tra CSRF). Request phải đi qua lần lượt từng filter trong chuỗi. Nếu một filter quyết định request không hợp lệ, nó có thể chặn request lại và trả về lỗi ngay lập tức.

*   **Cách Spring Security hoạt động:**
    1.  Khi bạn thêm `spring-boot-starter-security`, Spring Boot sẽ tự động đăng ký một filter đặc biệt tên là `DelegatingFilterProxy`. Filter này được đăng ký với Servlet Container (Tomcat).
    2.  Nhiệm vụ duy nhất của `DelegatingFilterProxy` là **ủy quyền (delegate)** request cho một bean Spring có tên là `springSecurityFilterChain`.
    3.  `springSecurityFilterChain` không phải là một filter đơn lẻ, mà là một **chuỗi các filter nội bộ của Spring Security** (khoảng 15 filter theo mặc định). Mỗi filter chịu trách nhiệm cho một khía cạnh bảo mật khác nhau.

**Luồng hoạt động của một request (Phỏng vấn!):**
`HTTP Request` -> `Tomcat` -> `DelegatingFilterProxy` -> **`springSecurityFilterChain`**
-> `CsrfFilter`
-> `UsernamePasswordAuthenticationFilter` (xử lý login)
-> `BearerTokenAuthenticationFilter` (xử lý JWT)
-> `AuthorizationFilter` (kiểm tra quyền)
-> ... (các filter khác) ...
-> `DispatcherServlet` -> `Controller`

Nếu request vượt qua tất cả các filter, nó sẽ đến controller. Nếu không, một filter nào đó sẽ chặn nó lại.

#### Authentication vs. Authorization

Đây là câu hỏi phỏng vấn cơ bản nhưng rất quan trọng để phân biệt.

*   **Authentication (Xác thực):** Là quá trình xác minh danh tính của một chủ thể (principal), thường là người dùng. Nó trả lời câu hỏi "Bạn là ai?".
    *   **Ví dụ:** Người dùng cung cấp username và password. Hệ thống kiểm tra xem chúng có khớp với dữ liệu trong database không. Nếu khớp, người dùng được xác thực.
*   **Authorization (Ủy quyền/Phân quyền):** Là quá trình quyết định xem một chủ thể đã được xác thực có được phép thực hiện một hành động cụ thể hay không. Nó trả lời câu hỏi "Bạn được phép làm gì?".
    *   **Ví dụ:** Người dùng "user1" đã đăng nhập (đã xác thực). Nhưng khi anh ta cố gắng truy cập endpoint `/admin/dashboard`, hệ thống kiểm tra và thấy anh ta chỉ có vai trò `ROLE_USER`, không có `ROLE_ADMIN`. Do đó, request bị từ chối (bị cấm).

**Quy tắc:** Authorization luôn xảy ra **sau khi** Authentication thành công.

#### SecurityContext, UserDetails, và AuthenticationManager

*   **`SecurityContext` và `SecurityContextHolder`:**
    *   Sau khi một người dùng được xác thực thành công, Spring Security cần một nơi để **lưu trữ thông tin** về người dùng đó (username, các quyền hạn...) để có thể sử dụng trong suốt quá trình xử lý request.
    *   Nơi lưu trữ này được gọi là `SecurityContext`.
    *   `SecurityContextHolder` là một lớp helper, sử dụng `ThreadLocal` để gắn `SecurityContext` với luồng (thread) đang xử lý request hiện tại. Điều này có nghĩa là bạn có thể lấy thông tin người dùng đang đăng nhập ở bất kỳ đâu trong code (service, repository) bằng cách gọi `SecurityContextHolder.getContext()`.
    *   **Khi nào nó bị xóa?** `SecurityContext` sẽ tự động được xóa khỏi `ThreadLocal` sau khi request kết thúc.
*   **`Authentication` object:**
    *   Đối tượng được lưu trữ bên trong `SecurityContext`. Nó chứa:
        *   `getPrincipal()`: Thông tin về chủ thể, thường là một đối tượng `UserDetails`.
        *   `getAuthorities()`: Danh sách các quyền (authorities) mà chủ thể có (ví dụ: `ROLE_ADMIN`, `READ_PRIVILEGE`).
        *   `isAuthenticated()`: Cờ cho biết chủ thể đã được xác thực hay chưa.
*   **`UserDetails` và `UserDetailsService`:**
    *   `UserDetails`: Là một interface của Spring Security, đại diện cho "người dùng" trong hệ thống của bạn. Nó định nghĩa các phương thức cốt lõi như `getUsername()`, `getPassword()`, `getAuthorities()`. Lớp `User` entity của bạn thường sẽ implement interface này.
    *   `UserDetailsService`: Là một interface bạn cần phải implement. Nó chỉ có một phương thức duy nhất: `loadUserByUsername(String username)`. Nhiệm vụ của nó là nhận vào username, tìm kiếm người dùng trong database (hoặc bất kỳ nguồn nào khác), và trả về một đối tượng `UserDetails`. Spring Security sẽ sử dụng service này trong quá trình xác thực.
*   **`AuthenticationManager`:**
    *   Là interface chính chịu trách nhiệm xử lý việc xác thực. Nó nhận vào một đối tượng `Authentication` (chứa username/password do người dùng cung cấp), gọi đến `UserDetailsService` để lấy thông tin người dùng thật, so sánh mật khẩu (sử dụng `PasswordEncoder`), và nếu thành công, trả về một đối tượng `Authentication` hoàn chỉnh (đã được xác thực và có đầy đủ quyền hạn).

### 2. Ví dụ minh họa

Thiết lập Spring Security với cấu hình đơn giản nhất, sử dụng **in-memory authentication** (người dùng được định nghĩa cứng trong code, không cần database).

**1. Thêm dependency:**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

**2. Tạo một Security Configuration class:**
```java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;
import org.springframework.security.web.SecurityFilterChain;

@Configuration
@EnableWebSecurity // Bật tính năng Spring Security Web
public class BasicSecurityConfig {

    // 1. Định nghĩa một UserDetailsService bean
    @Bean
    public UserDetailsService userDetailsService() {
        // Tạo một người dùng trong bộ nhớ
        UserDetails user = User.withDefaultPasswordEncoder() // Chỉ dùng cho demo, không an toàn!
                .username("user")
                .password("password")
                .roles("USER") // Tự động thêm tiền tố ROLE_ -> ROLE_USER
                .build();
        
        UserDetails admin = User.withDefaultPasswordEncoder()
                .username("admin")
                .password("password")
                .roles("ADMIN", "USER")
                .build();

        return new InMemoryUserDetailsManager(user, admin);
    }
    
    // 2. Định nghĩa SecurityFilterChain bean để cấu hình các quy tắc bảo mật
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(auth -> auth
                .antMatchers("/admin/**").hasRole("ADMIN") // Yêu cầu role ADMIN cho /admin/**
                .antMatchers("/user/**").hasAnyRole("USER", "ADMIN") // Yêu cầu USER hoặc ADMIN
                .antMatchers("/").permitAll() // Cho phép tất cả truy cập vào trang chủ
                .anyRequest().authenticated() // Tất cả các request khác phải được xác thực
            )
            .formLogin(); // Bật trang login mặc định của Spring Security
        
        return http.build();
    }
}
```

**3. Tạo một Controller đơn giản để kiểm tra:**
```java
@RestController
public class HomeController {
    @GetMapping("/")
    public String home() { return "Welcome!"; }
    
    @GetMapping("/user/dashboard")
    public String userDashboard() { return "Welcome User!"; }

    @GetMapping("/admin/dashboard")
    public String adminDashboard() { return "Welcome Admin!"; }
}
```

*   **Kết quả:**
    *   Truy cập `http://localhost:8080/`: Thành công.
    *   Truy cập `http://localhost:8080/user/dashboard`: Sẽ bị chuyển hướng đến trang login. Đăng nhập với `user`/`password` sẽ vào được.
    *   Đăng nhập với `user`/`password` rồi truy cập `/admin/dashboard`: Sẽ nhận lỗi `403 Forbidden`.
    *   Đăng nhập với `admin`/`password` rồi truy cập `/admin/dashboard`: Thành công.

### 3. Mini Exercise

1.  Trong `BasicSecurityConfig`, hãy thêm một người dùng mới trong `InMemoryUserDetailsManager`.
2.  Người dùng này có `username` là "guest", `password` là "guest", nhưng **không có role nào cả**.
3.  Tạo một endpoint mới `/guest/info`.
4.  Cập nhật `SecurityFilterChain` để yêu cầu người dùng phải được **xác thực (`authenticated()`)** để truy cập endpoint này, nhưng không yêu cầu bất kỳ role cụ thể nào.
5.  Kiểm tra xem bạn có thể đăng nhập với "guest" và truy cập `/guest/info` không.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** Sau khi người dùng đăng nhập thành công, thông tin của họ (principal, authorities) được lưu ở đâu để sử dụng cho các request tiếp theo trong cùng một phiên làm việc (session)?

a) Trong một cookie ở phía client.
b) Trong `HTTPSession` của server, và được gắn vào `SecurityContextHolder` (sử dụng `ThreadLocal`) cho mỗi request.
c) Trong `AuthenticationManager`.
d) Trong một bảng tạm thời trong database.

---
**Đáp án:** **b) Trong `HTTPSession` của server, và được gắn vào `SecurityContextHolder` (sử dụng `ThreadLocal`) cho mỗi request.**

**Giải thích:**
*   Đối với bảo mật **stateful** (mặc định với form login), sau khi xác thực, đối tượng `Authentication` được lưu vào `SecurityContext`. Sau đó, `SecurityContext` được lưu vào `HTTPSession`.
*   Đối với mỗi request tiếp theo từ cùng một client, một filter của Spring Security (`SecurityContextPersistenceFilter`) sẽ lấy `SecurityContext` từ `HTTPSession` và đặt nó vào `SecurityContextHolder` (là một `ThreadLocal`). Điều này làm cho thông tin người dùng có sẵn trong suốt quá trình xử lý request đó. Sau khi request hoàn thành, context sẽ được xóa khỏi `ThreadLocal`.

## Lesson 2: Cấu hình Cơ bản (Form Login & Basic Auth)

### 1. Giải thích khái niệm chi tiết

Ở bài trước, chúng ta đã có cái nhìn tổng quan. Bây giờ, chúng ta sẽ đi sâu vào cách cấu hình chi tiết `SecurityFilterChain` và thay thế cơ chế xác thực trong bộ nhớ bằng một cơ chế thực tế hơn.

#### `@EnableWebSecurity` và `SecurityFilterChain`

*   `@EnableWebSecurity`: Annotation này được đặt trên một lớp `@Configuration`. Nó hoạt động như một công tắc, kích hoạt toàn bộ cơ chế tích hợp của Spring Security với Spring MVC. Nó sẽ import các cấu hình cần thiết, trong đó quan trọng nhất là tạo ra bean `springSecurityFilterChain`.
*   **`SecurityFilterChain`:** Thay vì kế thừa từ `WebSecurityConfigurerAdapter` (cách làm đã lỗi thời), cách tiếp cận hiện đại là định nghĩa một bean kiểu `SecurityFilterChain`. Bean này nhận vào một đối tượng `HttpSecurity` và trả về một `SecurityFilterChain` đã được cấu hình. Cách làm này linh hoạt và dễ đọc hơn.
    *   **DSL (Domain-Specific Language):** Đối tượng `HttpSecurity` cung cấp một loạt các phương thức được thiết kế theo kiểu "builder" (builder pattern) và sử dụng lambda, tạo thành một DSL giúp bạn cấu hình chuỗi filter một cách lưu loát. Ví dụ: `http.authorizeHttpRequests(...).formLogin(...).build()`.

#### Cấu hình `HttpSecurity`

*   **`authorizeHttpRequests()`:** Đây là nơi bạn định nghĩa các **quy tắc ủy quyền (authorization rules)**. Bạn sử dụng các `antMatchers` (hoặc `mvcMatchers`, `regexMatchers`) để chỉ định các mẫu URL và các phương thức như `permitAll()`, `authenticated()`, `hasRole()`, `hasAuthority()` để áp dụng quy tắc.
    *   **Quy tắc:** Các quy tắc được khớp **theo thứ tự**. Do đó, bạn nên đặt các quy tắc **cụ thể hơn lên trước** các quy tắc chung chung hơn. `anyRequest()` luôn phải đặt cuối cùng.
*   **`formLogin()`:** Kích hoạt cơ chế xác thực bằng form login.
    *   Nó sẽ tự động thêm `UsernamePasswordAuthenticationFilter` vào chuỗi filter.
    *   Tự động tạo ra một trang login tại `/login`.
    *   Tự động tạo ra một endpoint `POST /login` để xử lý việc submit username/password.
*   **`httpBasic()`:** Kích hoạt cơ chế xác thực HTTP Basic.
    *   Client phải gửi username và password trong header `Authorization` dưới dạng `Basic base64(username:password)`.
    *   Thích hợp cho các API được gọi bởi các script hoặc các dịch vụ khác, nhưng không an toàn nếu không có HTTPS vì thông tin bị mã hóa base64 rất dễ giải mã.
*   **`csrf()`:** Cấu hình bảo vệ chống lại tấn công **CSRF (Cross-Site Request Forgery)**. Chúng ta sẽ tìm hiểu kỹ hơn ở bài sau, nhưng đối với **REST API stateless, cơ chế này thường được tắt** (`csrf().disable()`).
*   **`cors()`:** Cấu hình **CORS (Cross-Origin Resource Sharing)**.

#### `PasswordEncoder`

**Câu hỏi phỏng vấn cực kỳ quan trọng:** Tại sao không bao giờ được lưu trữ mật khẩu dưới dạng văn bản thuần (plain text) trong database?

*   **Trả lời:** Nếu database bị rò rỉ, toàn bộ mật khẩu của người dùng sẽ bị lộ, gây ra thảm họa bảo mật.
*   **Giải pháp:** Chúng ta phải **băm (hash)** mật khẩu trước khi lưu. Băm là một quá trình một chiều, không thể đảo ngược.
    *   **Vấn đề với các thuật toán hash cũ (MD5, SHA-1):** Chúng quá nhanh, kẻ tấn công có thể sử dụng kỹ thuật "rainbow table" hoặc "brute-force" để tìm ra mật khẩu gốc từ chuỗi hash.
*   **Giải pháp hiện đại: Adaptive One-way Functions.**
    *   Các thuật toán này (như **BCrypt, SCrypt, Argon2**) được thiết kế để **cố tình chạy chậm**.
    *   Chúng sử dụng một "yếu tố công việc" (work factor) có thể điều chỉnh để tăng độ khó tính toán.
    *   Chúng kết hợp một chuỗi ngẫu nhiên gọi là **"salt"** vào mỗi mật khẩu trước khi băm. Điều này đảm bảo rằng hai người dùng có cùng một mật khẩu sẽ có hai chuỗi hash hoàn toàn khác nhau, vô hiệu hóa rainbow table.
*   **`PasswordEncoder` trong Spring Security:**
    *   Là một interface để mã hóa và so sánh mật khẩu.
    *   `BCryptPasswordEncoder`: Là implementation được **khuyên dùng mạnh mẽ nhất**.
    *   Bạn **bắt buộc** phải định nghĩa một `@Bean` `PasswordEncoder`. Spring Security sẽ tự động sử dụng bean này để:
        1.  **Mã hóa** mật khẩu người dùng cung cấp khi đăng ký.
        2.  **So sánh** mật khẩu người dùng nhập khi đăng nhập (đã được băm) với chuỗi hash trong database.

#### Custom `UserDetailsService`

Cơ chế `InMemoryUserDetailsManager` chỉ dùng để demo. Trong thực tế, bạn phải lấy thông tin người dùng từ database.

*   **Cách làm:**
    1.  Tạo một lớp Service (ví dụ: `JpaUserDetailsService`) implement interface `UserDetailsService`.
    2.  Tiêm `UserRepository` (từ Spring Data JPA) vào service này.
    3.  Trong phương thức `loadUserByUsername(String username)`, bạn gọi `userRepository.findByUsername(username)`.
    4.  Nếu không tìm thấy user, ném `UsernameNotFoundException`.
    5.  Nếu tìm thấy, bạn cần chuyển đổi đối tượng `User` entity của bạn thành một đối tượng `UserDetails`. Cách tốt nhất là cho lớp `User` entity của bạn implement trực tiếp `UserDetails`.

### 2. Ví dụ minh họa

**Tình huống:** Chuyển đổi từ in-memory authentication sang sử dụng database với `UserDetailsService` và `BCryptPasswordEncoder`.

**1. Tạo Entity `User`:**
```java
@Entity
public class User implements UserDetails { // Implement UserDetails
    @Id @GeneratedValue private Long id;
    private String username;
    private String password;
    private String role; // Giả sử đơn giản mỗi user có 1 role

    // Các phương thức của UserDetails
    @Override
    public Collection<? extends GrantedAuthority> getAuthorities() {
        // Trả về danh sách các quyền
        return List.of(new SimpleGrantedAuthority(role));
    }
    // ... password, username ...
    @Override public boolean isAccountNonExpired() { return true; }
    @Override public boolean isAccountNonLocked() { return true; }
    @Override public boolean isCredentialsNonExpired() { return true; }
    @Override public boolean isEnabled() { return true; }
    
    // Getters, setters...
}
```

**2. Tạo Repository:**
```java
public interface UserRepository extends JpaRepository<User, Long> {
    Optional<User> findByUsername(String username);
}
```

**3. Tạo Custom `UserDetailsService`:**
```java
@Service
public class JpaUserDetailsService implements UserDetailsService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
        return userRepository.findByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("User not found with username: " + username));
    }
}
```

**4. Cập nhật Security Configuration:**
```java
@Configuration
@EnableWebSecurity
public class DatabaseSecurityConfig {

    // Không cần bean UserDetailsService nữa vì Spring sẽ tự động tìm thấy
    // bean JpaUserDetailsService của chúng ta.
    
    // 1. Định nghĩa bean PasswordEncoder
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable() // Tắt CSRF cho REST API (sẽ giải thích sau)
            .authorizeHttpRequests(auth -> auth
                .antMatchers("/admin/**").hasAuthority("ROLE_ADMIN") // Dùng hasAuthority rõ ràng hơn
                .antMatchers("/user/**").hasAnyAuthority("ROLE_USER", "ROLE_ADMIN")
                .antMatchers("/", "/register").permitAll()
                .anyRequest().authenticated()
            )
            .formLogin(); // Vẫn dùng form login cho demo
        
        return http.build();
    }
}
```

**5. Tạo một CommandLineRunner để thêm dữ liệu mẫu (với mật khẩu đã mã hóa):**
```java
@Component
public class DataInitializer implements CommandLineRunner {
    @Autowired private UserRepository userRepository;
    @Autowired private PasswordEncoder passwordEncoder;

    @Override
    public void run(String... args) throws Exception {
        // Chỉ tạo user nếu chưa có
        if (userRepository.findByUsername("user").isEmpty()) {
            User user = new User();
            user.setUsername("user");
            user.setPassword(passwordEncoder.encode("password")); // Quan trọng: Mã hóa mật khẩu
            user.setRole("ROLE_USER");
            userRepository.save(user);
        }
    }
}
```
Bây giờ, ứng dụng của bạn sẽ xác thực người dùng dựa trên dữ liệu trong database H2, và mật khẩu được lưu trữ một cách an toàn.

### 3. Mini Exercise

1.  Tạo một endpoint `POST /register` trong một controller mới.
2.  Endpoint này nhận một DTO `RegisterRequest` chứa `username` và `password`.
3.  Trong service, hãy mã hóa mật khẩu nhận được bằng `PasswordEncoder` trước khi tạo và lưu một `User` mới vào database.
4.  Endpoint này phải được `permitAll()` trong `SecurityFilterChain`.
5.  Dùng Postman để đăng ký một tài khoản mới, sau đó thử đăng nhập bằng tài khoản đó qua form login.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** `BCryptPasswordEncoder` hoạt động như thế nào? Khi bạn gọi `encoder.matches("rawPassword", "encodedPasswordFromDb")`, nó có băm "rawPassword" rồi so sánh chuỗi hash không?

a) Có, nó băm "rawPassword" với cùng một salt và so sánh hai chuỗi hash.
b) Không, nó không thể làm vậy vì salt được lưu trong "encodedPasswordFromDb". Thay vào đó, nó giải mã "encodedPasswordFromDb" rồi so sánh với "rawPassword".
c) BCrypt là thuật toán một chiều. Chuỗi "encodedPasswordFromDb" đã chứa cả salt và hash. Phương thức `matches` sẽ trích xuất salt từ chuỗi hash trong DB, sử dụng salt đó để băm "rawPassword", sau đó so sánh kết quả với phần hash trong DB.
d) Nó gửi cả hai chuỗi đến một dịch vụ an toàn của Spring để so sánh.

---
**Đáp án:** **c) BCrypt là thuật toán một chiều. Chuỗi "encodedPasswordFromDb" đã chứa cả salt và hash. Phương thức `matches` sẽ trích xuất salt từ chuỗi hash trong DB, sử dụng salt đó để băm "rawPassword", sau đó so sánh kết quả với phần hash trong DB.**

**Giải thích:** Đây là cách hoạt động chính xác của các thuật toán băm mật khẩu hiện đại. Chúng không thể giải mã. Chuỗi hash được lưu trong DB có dạng `$2a$10$N9qo8uLOickgx2ZMRZoMye.IKbeTmM...`, trong đó `$2a$10` là phiên bản và work factor, `N9qo8uLOickgx2ZMRZoMye` là salt, và phần còn lại là hash. `matches()` sẽ phân tích chuỗi này để có được các thông tin cần thiết cho việc so sánh an toàn.

---

## Lesson 3: JWT Authentication cho RESTful API (Stateless Security)

### 1. Giải thích khái niệm chi tiết

#### Stateless vs. Stateful Security

*   **Stateful (Có trạng thái - Form Login/Session):**
    *   **Luồng hoạt động:**
        1.  Client gửi username/password.
        2.  Server xác thực, tạo một **session** trên server và lưu thông tin người dùng vào đó.
        3.  Server trả về cho client một `Session ID` (thường qua cookie `JSESSIONID`).
        4.  Trong các request tiếp theo, client gửi `Session ID` này. Server dùng ID để tìm lại session, lấy thông tin người dùng và xử lý.
    *   **Nhược điểm:**
        *   **Khó mở rộng (Scalability):** Session được lưu trên một server cụ thể. Nếu bạn có nhiều server (load balancing), bạn phải giải quyết bài toán "đồng bộ session" giữa các server, điều này rất phức tạp (sử dụng sticky session hoặc session replication).
        *   **Phụ thuộc vào cookie:** Không thân thiện với các client không phải trình duyệt (mobile app, IoT).
        *   **Tăng tải cho server:** Server phải tốn bộ nhớ để lưu trữ hàng ngàn, hàng triệu session.

*   **Stateless (Phi trạng thái - JWT):**
    *   **Luồng hoạt động:**
        1.  Client gửi username/password.
        2.  Server xác thực, tạo ra một **chuỗi JSON Web Token (JWT)** chứa thông tin người dùng.
        3.  Server trả về chuỗi JWT này cho client.
        4.  Client **tự lưu trữ** JWT (thường trong Local Storage hoặc Secure Storage).
        5.  Trong các request tiếp theo, client gửi JWT trong header `Authorization`.
        6.  Server nhận JWT, **xác minh chữ ký (verify signature)** của nó. Nếu chữ ký hợp lệ, server tin tưởng thông tin bên trong token và xử lý request.
    *   **Ưu điểm (Phỏng vấn!):**
        *   **Khả năng mở rộng tuyệt vời:** Server **không cần lưu trữ bất cứ thứ gì**. Mọi thông tin cần thiết đều nằm trong token mà client gửi lên. Bất kỳ server nào trong cụm cũng có thể xác thực token và xử lý request.
        *   **Linh hoạt:** Hoạt động tốt với mọi loại client (web, mobile, service-to-service).
        *   **Tách biệt:** Hoàn hảo cho kiến trúc microservices, nơi một token có thể được sử dụng để gọi nhiều dịch vụ khác nhau.

#### Cấu trúc của một JWT

Một chuỗi JWT trông giống như `xxxxx.yyyyy.zzzzz`. Nó bao gồm 3 phần được phân tách bởi dấu chấm, mỗi phần được mã hóa Base64Url:

1.  **Header:** Chứa thông tin về thuật toán được sử dụng để ký token.
    *   *Ví dụ:* `{"alg": "HS256", "typ": "JWT"}`

2.  **Payload (Claims):** Chứa dữ liệu (các "claim") về người dùng và token.
    *   **Registered Claims (chuẩn):** `iss` (issuer), `exp` (expiration time), `sub` (subject - thường là username hoặc user ID), `iat` (issued at).
    *   **Custom Claims:** Bạn có thể thêm bất kỳ thông tin nào bạn muốn, ví dụ: `roles`, `email`.
    *   *Ví dụ:* `{"sub": "john.doe", "roles": ["ROLE_USER"], "exp": 1665840000}`
    *   **Cảnh báo:** Không bao giờ đặt thông tin nhạy cảm (như mật khẩu) vào payload, vì payload chỉ được mã hóa Base64, không phải mã hóa bảo mật. Bất kỳ ai cũng có thể giải mã và đọc nó.

3.  **Signature (Chữ ký):**
    *   Đây là phần quan trọng nhất đảm bảo tính toàn vẹn của token.
    *   **Cách tạo:** `HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret_key)`
    *   `secret_key` là một chuỗi bí mật **chỉ server biết**.
    *   **Cách xác minh:** Khi server nhận token, nó sẽ thực hiện lại phép tính trên với `secret_key` của mình. Nếu chữ ký tạo ra khớp với chữ ký trong token, server biết rằng:
        1.  Token này thực sự do server tạo ra (vì chỉ server có `secret_key`).
        2.  Payload của token không hề bị sửa đổi trên đường truyền.

#### Quy trình triển khai JWT trong Spring Security

Để thay thế cơ chế Session bằng JWT, chúng ta cần:
1.  **Tắt các tính năng stateful:** Vô hiệu hóa `formLogin`, `httpBasic`, và đặc biệt là `csrf`. Cấu hình session management thành `STATELESS`.
2.  **Tạo một endpoint để đăng nhập:** Một endpoint `POST /api/auth/login` không được bảo vệ, nhận `username`/`password`, xác thực chúng bằng `AuthenticationManager`. Nếu thành công, tạo JWT và trả về cho client.
3.  **Tạo một JWT Filter:** Đây là thành phần cốt lõi. Chúng ta sẽ tạo một filter tùy chỉnh (ví dụ: `JwtAuthenticationFilter`) kế thừa từ `OncePerRequestFilter`.
    *   Filter này sẽ được thêm vào `SecurityFilterChain` **trước** các filter xác thực chuẩn.
    *   **Nhiệm vụ của filter:**
        a.  Với mỗi request đến, kiểm tra xem có header `Authorization` và nó có bắt đầu bằng `Bearer ` không.
        b.  Nếu có, trích xuất chuỗi JWT.
        c.  Giải mã và xác minh (verify) token bằng `secret_key`.
        d.  Nếu token hợp lệ, trích xuất username và các quyền từ payload.
        e.  Tạo một đối tượng `UserDetails` (bằng cách gọi `UserDetailsService`).
        f.  Tạo một đối tượng `UsernamePasswordAuthenticationToken` (đại diện cho người dùng đã được xác thực) và đặt nó vào `SecurityContextHolder.getContext()`.
        g.  Cho phép request đi tiếp trong chuỗi filter.
    *   **Kết quả:** Khi request đến controller, `SecurityContext` đã chứa thông tin người dùng đã xác thực, cho phép các cơ chế phân quyền (`@PreAuthorize`, `antMatchers`) hoạt động bình thường.

### 2. Ví dụ minh họa

**Tình huống:** Xây dựng luồng đăng nhập và bảo vệ endpoint bằng JWT.

**1. Thêm dependency JWT:**
```xml
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-api</artifactId>
    <version>0.11.5</version>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-impl</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt-jackson</artifactId>
    <version>0.11.5</version>
    <scope>runtime</scope>
</dependency>
```

**2. Tạo một lớp tiện ích `JwtUtil` để tạo và xác minh token:**
```java
@Component
public class JwtUtil {
    @Value("${jwt.secret}")
    private String secret;

    @Value("${jwt.expiration.ms}")
    private long jwtExpirationMs;

    public String generateToken(Authentication authentication) {
        UserDetails userPrincipal = (UserDetails) authentication.getPrincipal();
        return Jwts.builder()
                .setSubject(userPrincipal.getUsername())
                .setIssuedAt(new Date())
                .setExpiration(new Date((new Date()).getTime() + jwtExpirationMs))
                .signWith(SignatureAlgorithm.HS512, secret)
                .compact();
    }
    
    public String getUsernameFromToken(String token) {
        return Jwts.parser().setSigningKey(secret).parseClaimsJws(token).getBody().getSubject();
    }

    public boolean validateToken(String token) {
        try {
            Jwts.parser().setSigningKey(secret).parseClaimsJws(token);
            return true;
        } catch (Exception e) {
            // log...
        }
        return false;
    }
}
```
*   `jwt.secret` và `jwt.expiration.ms` được định nghĩa trong `application.properties`.

**3. Tạo `JwtAuthenticationFilter`:**
```java
@Component
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    @Autowired private JwtUtil jwtUtil;
    @Autowired private UserDetailsService userDetailsService;

    @Override
    protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain)
            throws ServletException, IOException {
        String header = request.getHeader("Authorization");
        if (header == null || !header.startsWith("Bearer ")) {
            filterChain.doFilter(request, response);
            return;
        }

        String token = header.substring(7);
        if (jwtUtil.validateToken(token)) {
            String username = jwtUtil.getUsernameFromToken(token);
            UserDetails userDetails = userDetailsService.loadUserByUsername(username);
            
            UsernamePasswordAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(
                userDetails, null, userDetails.getAuthorities());
            
            SecurityContextHolder.getContext().setAuthentication(authentication);
        }
        filterChain.doFilter(request, response);
    }
}
```

**4. Cập nhật `SecurityConfig`:**
```java
@Configuration
@EnableWebSecurity
public class JwtSecurityConfig {
    @Autowired private JwtAuthenticationFilter jwtAuthenticationFilter;
    @Autowired private UserDetailsService userDetailsService;
    @Autowired private PasswordEncoder passwordEncoder;
    
    // Cần bean AuthenticationManager để xử lý đăng nhập
    @Bean
    public AuthenticationManager authenticationManager(AuthenticationConfiguration authConfig) throws Exception {
        return authConfig.getAuthenticationManager();
    }
    
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf().disable()
            // Cấu hình session thành STATELESS
            .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            .and()
            .authorizeHttpRequests(auth -> auth
                .antMatchers("/api/auth/**").permitAll() // Cho phép endpoint đăng nhập
                .anyRequest().authenticated()
            );
        
        // Thêm filter của chúng ta vào trước filter chuẩn
        http.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }
}
```

**5. Tạo Auth Controller:**
```java
@RestController
@RequestMapping("/api/auth")
public class AuthController {
    @Autowired AuthenticationManager authenticationManager;
    @Autowired JwtUtil jwtUtil;
    
    @PostMapping("/login")
    public ResponseEntity<String> login(@RequestBody LoginRequest loginRequest) {
        Authentication authentication = authenticationManager.authenticate(
            new UsernamePasswordAuthenticationToken(loginRequest.getUsername(), loginRequest.getPassword()));
        
        SecurityContextHolder.getContext().setAuthentication(authentication);
        String jwt = jwtUtil.generateToken(authentication);
        
        return ResponseEntity.ok(jwt);
    }
}
```

### 3. Mini Exercise

1.  Trong `JwtUtil`, payload của token hiện tại chỉ chứa `username` (`sub`).
2.  Hãy sửa lại phương thức `generateToken` để thêm một **custom claim** tên là "roles" vào payload. Giá trị của claim này nên là một danh sách các quyền của người dùng.
3.  **Gợi ý:** Sử dụng phương thức `claim("roles", authorities)` của `JwtBuilder`. Bạn có thể lấy danh sách quyền từ `authentication.getAuthorities()`.
4.  Sau khi đăng nhập, hãy dùng một trang web như `jwt.io` để dán token vào và kiểm tra xem payload đã chứa thông tin roles hay chưa.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** Khi một JWT đã hết hạn (expired), client nên làm gì? Server sẽ phản hồi như thế nào?

a) Client nên tiếp tục dùng token đó, server sẽ bỏ qua việc kiểm tra thời gian hết hạn.
b) Server sẽ tự động gia hạn token và trả về token mới trong response.
c) Server sẽ trả về lỗi `401 Unauthorized`. Client, khi nhận lỗi này, nên sử dụng một **Refresh Token** (nếu có) để lấy một Access Token mới mà không cần người dùng nhập lại mật khẩu.
d) Client nên xóa token và yêu cầu người dùng đăng nhập lại từ đầu.

---
**Đáp án:** **c) Server sẽ trả về lỗi `401 Unauthorized`. Client, khi nhận lỗi này, nên sử dụng một Refresh Token (nếu có) để lấy một Access Token mới mà không cần người dùng nhập lại mật khẩu.**

**Giải thích:** JWT (Access Token) được thiết kế để có thời gian sống ngắn (ví dụ 15 phút). Khi nó hết hạn, nó trở nên vô giá trị. Luồng làm mới token (Refresh Token flow) là một cơ chế tiêu chuẩn. Refresh Token là một token khác có thời gian sống dài hơn nhiều (ví dụ 7 ngày), được lưu trữ an toàn hơn. Khi Access Token hết hạn, client sẽ gửi Refresh Token đến một endpoint đặc biệt (`/api/auth/refresh`) để đổi lấy một Access Token mới. Nếu Refresh Token cũng hết hạn, lúc đó người dùng mới phải đăng nhập lại.

---

## Lesson 4: Role-Based và Method-Level Security

### 1. Giải thích khái niệm chi tiết

#### Role vs. Authority

Trong Spring Security, hai khái niệm này thường được sử dụng thay thế cho nhau, nhưng có một sự khác biệt nhỏ về mặt ngữ nghĩa:

*   **Authority (Quyền hạn):** Là một quyền đơn lẻ, chi tiết. Nó thường đại diện cho một hành động cụ thể.
    *   **Ví dụ:** `product:read`, `product:create`, `user:delete`.
    *   Đây là cách tiếp cận **chi tiết (fine-grained)** và linh hoạt hơn.
*   **Role (Vai trò):** Là một tập hợp các quyền hạn. Nó đại diện cho một vai trò của người dùng trong hệ thống.
    *   **Ví dụ:** Vai trò `ADMIN` có thể bao gồm các quyền `product:read`, `product:create`, `product:delete`, `user:read`, `user:delete`. Vai trò `USER` có thể chỉ có quyền `product:read`.
    *   **Quy ước của Spring:** Khi bạn sử dụng các phương thức như `hasRole("ADMIN")`, Spring Security sẽ tự động tìm kiếm một authority có tên là **`ROLE_ADMIN`**. Tiền tố `ROLE_` là một quy ước. Nếu bạn dùng `hasAuthority("ROLE_ADMIN")`, nó sẽ hoạt động tương tự. Tuy nhiên, `hasAuthority("product:create")` cho phép bạn kiểm tra các quyền chi tiết hơn.

**Best Practice:**
*   Thiết kế hệ thống của bạn dựa trên các **Authority** chi tiết.
*   Gom nhóm các Authority này vào các **Role**.
*   Trong code, ưu tiên sử dụng `hasAuthority()` vì nó rõ ràng và không phụ thuộc vào tiền tố `ROLE_`. Tuy nhiên, `hasRole()` vẫn rất phổ biến và tiện lợi.

#### Phân quyền ở cấp độ Web (Endpoint)

Đây là cách chúng ta đã làm ở các bài trước, sử dụng `authorizeHttpRequests()` trong `SecurityFilterChain`.

```java
http.authorizeHttpRequests(auth -> auth
    .antMatchers(HttpMethod.GET, "/api/products/**").hasAuthority("product:read")
    .antMatchers(HttpMethod.POST, "/api/products").hasAuthority("product:create")
    .antMatchers("/api/admin/**").hasRole("ADMIN")
    .anyRequest().authenticated()
);
```

*   **Ưu điểm:**
    *   Tập trung tất cả các quy tắc bảo mật ở một nơi duy nhất.
    *   Dễ dàng xem tổng quan về cách các endpoint được bảo vệ.
    *   Đây là lớp bảo vệ đầu tiên và quan trọng nhất.
*   **Nhược điểm:**
    *   Các quy tắc có thể trở nên phức tạp và khó quản lý khi ứng dụng lớn dần.
    *   Nó chỉ bảo vệ được "cổng vào" (endpoint). Nếu một developer khác vô tình gọi một phương thức service nhạy cảm từ một controller không được bảo vệ, logic nghiệp vụ vẫn sẽ bị thực thi.

#### Phân quyền ở cấp độ Phương thức (Method-Level Security)

Đây là một lớp bảo vệ thứ hai, sâu hơn và mạnh mẽ hơn. Nó cho phép bạn đặt các annotation bảo mật trực tiếp trên các phương thức trong tầng Service.

*   **Cách kích hoạt:** Thêm annotation `@EnableGlobalMethodSecurity` (cho các phiên bản cũ hơn) hoặc `@EnableMethodSecurity` (cách làm hiện đại) vào lớp `@Configuration` của bạn.
    ```java
    @Configuration
    @EnableWebSecurity
    @EnableMethodSecurity // Kích hoạt bảo mật ở cấp độ phương thức
    public class SecurityConfig { ... }
    ```
*   **Các Annotation chính:**
    1.  **`@Secured("ROLE_ADMIN")`:**
        *   Đây là annotation gốc và đơn giản nhất.
        *   Nó chỉ hỗ trợ kiểm tra role (cần có tiền tố `ROLE_`).
        *   Ít linh hoạt hơn các annotation khác.
    2.  **`@RolesAllowed("ADMIN")`:**
        *   Là một annotation chuẩn của JSR-250.
        *   Tương tự `@Secured`, chỉ kiểm tra role.
    3.  **`@PreAuthorize` và `@PostAuthorize` (Mạnh mẽ và được khuyên dùng nhất):**
        *   Các annotation này hỗ trợ **SpEL (Spring Expression Language)**, cho phép bạn viết các quy tắc phân quyền cực kỳ phức tạp và linh hoạt.
        *   **`@PreAuthorize`:**
            *   Biểu thức SpEL được thực thi **TRƯỚC KHI** phương thức được gọi.
            *   Nếu biểu thức trả về `false`, phương thức sẽ **không được thực thi** và `AccessDeniedException` sẽ được ném ra.
            *   Đây là annotation được **sử dụng nhiều nhất**.
            *   **Các biểu thức phổ biến:**
                *   `@PreAuthorize("hasRole('ADMIN')")`
                *   `@PreAuthorize("hasAnyRole('ADMIN', 'USER')")`
                *   `@PreAuthorize("hasAuthority('product:create')")`
                *   `@PreAuthorize("isAuthenticated()")`
                *   **Biểu thức động:** Bạn có thể truy cập vào các tham số của phương thức.
                    ```java
                    // Chỉ cho phép user chỉnh sửa thông tin của chính mình, hoặc admin được làm tất cả
                    @PreAuthorize("#username == authentication.principal.username or hasRole('ADMIN')")
                    void updateUser(String username, UserUpdateDto dto);
                    ```
                    Ở đây, `authentication.principal` chính là đối tượng `UserDetails` được lưu trong `SecurityContext`.
        *   **`@PostAuthorize`:**
            *   Biểu thức SpEL được thực thi **SAU KHI** phương thức đã chạy xong, nhưng **TRƯỚC KHI** kết quả được trả về.
            *   Ít được sử dụng hơn, nhưng hữu ích khi bạn cần kiểm tra quyền dựa trên kết quả trả về của phương thức.
            *   **Ví dụ:**
                ```java
                // Chỉ trả về đơn hàng nếu người dùng hiện tại là chủ sở hữu của đơn hàng đó
                @PostAuthorize("returnObject.owner == authentication.principal.username")
                Order findOrderById(Long id);
                ```
                Ở đây, `returnObject` là từ khóa đặc biệt để truy cập vào đối tượng mà phương thức trả về.

### 2. Ví dụ minh họa

**Tình huống:** Bảo vệ một `ProductService`. Chỉ `ADMIN` mới có quyền tạo sản phẩm. Bất kỳ ai đã đăng nhập đều có thể xem sản phẩm.

**1. Cấu hình `SecurityConfig`:**
```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity(prePostEnabled = true, securedEnabled = true, jsr250Enabled = true)
// Bật tất cả các loại annotation để so sánh
public class MethodSecurityConfig {
    // ... các bean SecurityFilterChain, UserDetailsService, PasswordEncoder ...
}
```

**2. Cập nhật `ProductService`:**
```java
public interface ProductService {
    ProductDto findById(Long id);
    ProductDto createProduct(CreateProductRequest request);
}

@Service
public class ProductServiceImpl implements ProductService {

    // Ai cũng có thể xem, miễn là đã đăng nhập
    @Override
    @PreAuthorize("isAuthenticated()")
    public ProductDto findById(Long id) {
        log.info("Finding product by id: {}", id);
        // ... logic tìm sản phẩm ...
        return new ProductDto(...);
    }
    
    // Chỉ người có quyền 'product:create' mới được tạo
    // (Giả sử role ADMIN có quyền này)
    @Override
    @PreAuthorize("hasAuthority('product:create')")
    public ProductDto createProduct(CreateProductRequest request) {
        log.info("Creating a new product...");
        // ... logic tạo sản phẩm ...
        return new ProductDto(...);
    }

    // Ví dụ với @Secured
    @Secured("ROLE_ADMIN")
    public void deleteProduct(Long id) {
        log.info("Deleting product...");
        // ... logic xóa ...
    }
}
```

**3. Cập nhật `SecurityFilterChain`:**
```java
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    http
        // ... csrf, sessionManagement ...
        .authorizeHttpRequests(auth -> auth
            // Giữ lại các quy tắc ở tầng web như một lớp bảo vệ chung
            // Nó có thể ít chi tiết hơn tầng service
            .antMatchers("/api/products/**").authenticated() 
            .anyRequest().permitAll()
        );
        // ... add JwtFilter ...
    return http.build();
}
```

*   **Kết quả:**
    *   Một người dùng với `ROLE_USER` (không có quyền `product:create`) gọi `POST /api/products`.
    *   Request sẽ vượt qua lớp bảo vệ của `SecurityFilterChain` (vì chỉ yêu cầu `authenticated()`).
    *   Tuy nhiên, khi phương thức `createProduct` trong `ProductService` sắp được gọi, proxy của AOP sẽ chặn lại.
    *   Biểu thức `@PreAuthorize("hasAuthority('product:create')")` được kiểm tra.
    *   Vì người dùng không có quyền này, `AccessDeniedException` sẽ được ném ra, và phương thức sẽ không bao giờ được thực thi. Server trả về lỗi `403 Forbidden`.
    *   Đây là một ví dụ điển hình của **"Defense in Depth"** (Phòng thủ theo chiều sâu).

### 3. Mini Exercise

1.  Tạo một phương thức `updateProduct(Long productId, UpdateProductRequest request)` trong `ProductService`.
2.  Sử dụng `@PreAuthorize` để viết một quy tắc động: chỉ cho phép người dùng cập nhật sản phẩm nếu họ là **người tạo ra sản phẩm đó**.
3.  Giả sử entity `Product` có một trường `createdBy` (String) lưu username của người tạo.
4.  **Gợi ý:**
    *   Bạn sẽ cần viết một câu JPQL trong repository để lấy `createdBy` của sản phẩm.
    *   Biểu thức SpEL có thể trông giống như: `@PreAuthorize("@productRepository.findCreatedByById(#productId) == authentication.principal.username or hasRole('ADMIN')")`.
    *   Lưu ý: để gọi bean repository trong SpEL, bean đó cần được expose với tên `@Repository("productRepository")`.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** Sự khác biệt chính giữa `@PreAuthorize` và việc kiểm tra quyền trong `SecurityFilterChain` là gì?

a) `SecurityFilterChain` chạy nhanh hơn.
b) `@PreAuthorize` cho phép sử dụng SpEL để viết các quy tắc động dựa trên tham số phương thức hoặc trạng thái của ứng dụng, trong khi `SecurityFilterChain` chủ yếu làm việc với các quy tắc tĩnh dựa trên URL và HTTP method.
c) `SecurityFilterChain` chỉ có thể kiểm tra role, còn `@PreAuthorize` có thể kiểm tra cả authority.
d) `@PreAuthorize` chỉ hoạt động với JWT, còn `SecurityFilterChain` hoạt động với cả Session.

---
**Đáp án:** **b) `@PreAuthorize` cho phép sử dụng SpEL để viết các quy tắc động dựa trên tham số phương thức hoặc trạng thái của ứng dụng, trong khi `SecurityFilterChain` chủ yếu làm việc với các quy tắc tĩnh dựa trên URL và HTTP method.**

**Giải thích:** Đây là điểm mạnh cốt lõi của bảo mật cấp độ phương thức. Nó cho phép bạn thực hiện việc phân quyền dựa trên ngữ cảnh (context-aware). Ví dụ, "user A có được sửa comment B không?" là một câu hỏi không thể trả lời chỉ bằng URL (`PUT /comments/123`). Bạn cần phải tải comment 123 lên, xem ai là chủ sở hữu, rồi so sánh với user A. `@PreAuthorize` cho phép bạn thực hiện logic phức tạp này một cách khai báo ngay tại tầng service.

---

## Lesson 5: CORS, CSRF và Session Management

### 1. Giải thích khái niệm chi tiết

#### Cross-Origin Resource Sharing (CORS)

Chúng ta đã đề cập ngắn gọn về CORS ở bài Spring Web. Ở đây, chúng ta sẽ xem xét nó dưới góc độ bảo mật.

*   **Nhắc lại vấn đề (Same-Origin Policy):** Theo mặc định, trình duyệt chặn các script từ một origin (ví dụ `https://my-awesome-frontend.com`) gửi request đến một API ở một origin khác (ví dụ `https://api.my-awesome-app.com`). Đây là một tính năng bảo mật cơ bản để ngăn chặn các trang web độc hại đọc dữ liệu từ các trang web khác mà bạn đã đăng nhập.
*   **CORS là cơ chế nới lỏng:** Nó cho phép server API của bạn khai báo một cách tường minh rằng: "Tôi tin tưởng và cho phép các request đến từ origin `https://my-awesome-frontend.com`".
*   **Cách hoạt động (với Preflight):**
    1.  Frontend (chạy trên trình duyệt) muốn gửi một request `PUT /api/users/1` với header `Authorization`.
    2.  Vì đây là "request phức tạp", trình duyệt sẽ tự động gửi một **preflight request** `OPTIONS /api/users/1` trước. Request này hỏi server: "Này server, tôi sắp gửi một request `PUT` từ origin `X` với header `Authorization`. Có được không?".
    3.  Server Spring Boot của bạn (đã được cấu hình CORS) sẽ trả lời với các header như:
        *   `Access-Control-Allow-Origin: https://my-awesome-frontend.com`
        *   `Access-Control-Allow-Methods: GET, POST, PUT, DELETE`
        *   `Access-Control-Allow-Headers: Authorization, Content-Type`
    4.  Trình duyệt nhận được câu trả lời, thấy rằng mọi thứ đều được cho phép, và lúc này nó mới gửi request `PUT` thật sự.
*   **Cấu hình trong Spring Security:**
    *   Cách tốt nhất là cấu hình tập trung trong `SecurityFilterChain`. Nó đảm bảo rằng các quy tắc CORS được áp dụng bởi `CorsFilter` của Spring, là filter chạy **rất sớm** trong chuỗi, trước cả các filter xác thực.
    ```java
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .cors(Customizer.withDefaults()) // Kích hoạt CORS với cấu hình mặc định hoặc tùy chỉnh
            // ... các cấu hình khác
        return http.build();
    }
    
    // Cung cấp một bean CorsConfigurationSource để tùy chỉnh
    @Bean
    CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration configuration = new CorsConfiguration();
        configuration.setAllowedOrigins(Arrays.asList("http://localhost:3000"));
        configuration.setAllowedMethods(Arrays.asList("GET","POST", "PUT", "DELETE"));
        configuration.setAllowedHeaders(List.of("*"));
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", configuration);
        return source;
    }
    ```

#### CSRF (Cross-Site Request Forgery)

Đây là một trong những loại tấn công web phổ biến và nguy hiểm nhất, nhưng chỉ áp dụng cho các ứng dụng **stateful (dựa trên session/cookie)**.

*   **Kịch bản tấn công:**
    1.  Bạn đăng nhập vào trang web ngân hàng của mình (`my-bank.com`). Server tạo session và gửi về cookie `JSESSIONID`. Trình duyệt của bạn sẽ tự động gửi cookie này trong mọi request tiếp theo đến `my-bank.com`.
    2.  Bạn lướt sang một trang web độc hại khác (`evil.com`).
    3.  Trên trang `evil.com`, có một form ẩn tự động submit một request `POST` đến `my-bank.com/transfer` với các tham số để chuyển tiền vào tài khoản của hacker.
    4.  Khi trình duyệt gửi request này, nó sẽ **tự động đính kèm cookie `JSESSIONID`** của bạn.
    5.  Server của `my-bank.com` nhận được request, thấy có cookie hợp lệ, nó cho rằng đây là request do chính bạn thực hiện và thực hiện lệnh chuyển tiền. Bạn đã bị mất tiền mà không hề hay biết.

*   **Cách Spring Security bảo vệ:**
    *   Spring Security sẽ tạo ra một token ngẫu nhiên, không thể đoán trước gọi là **CSRF token**.
    *   Token này sẽ được gửi đến client (ví dụ trong một hidden field của form hoặc trong một meta tag).
    *   Đối với mọi request thay đổi trạng thái (`POST`, `PUT`, `DELETE`), client **bắt buộc** phải gửi lại CSRF token này trong một header (thường là `X-XSRF-TOKEN`) hoặc một tham số request.
    *   `CsrfFilter` của Spring Security sẽ so sánh token nhận được với token nó đã tạo cho session đó. Nếu không khớp, request sẽ bị từ chối.
    *   Vì trang `evil.com` không thể đọc được CSRF token từ `my-bank.com` (do Same-Origin Policy), nó không thể gửi kèm token hợp lệ, và cuộc tấn công thất bại.

*   **CSRF và REST API Stateless (Phỏng vấn!):**
    *   **Khi nào nên tắt CSRF?** Bạn **NÊN TẮT** (`http.csrf().disable()`) khi xây dựng một API **stateless** (ví dụ dùng JWT).
    *   **Tại sao?** Vì trong mô hình stateless, client không dựa vào cookie để xác thực. Client phải chủ động gửi `Authorization: Bearer <jwt_token>` trong mỗi request. Trình duyệt không tự động đính kèm header này. Do đó, trang `evil.com` không thể giả mạo một request hợp lệ vì nó không có JWT token của bạn. Cuộc tấn công CSRF trở nên vô nghĩa.
    *   Bật CSRF cho API stateless chỉ làm tăng thêm sự phức tạp không cần thiết.

#### Session Management

`http.sessionManagement()` cho phép bạn kiểm soát cách Spring Security tạo và sử dụng `HttpSession`.

*   `sessionCreationPolicy(SessionCreationPolicy policy)`:
    *   `ALWAYS`: Luôn tạo một `HttpSession` nếu chưa có.
    *   `IF_REQUIRED` (**Mặc định**): Chỉ tạo `HttpSession` khi cần (ví dụ, sau khi đăng nhập thành công để lưu `SecurityContext`).
    *   `NEVER`: Sẽ không bao giờ tạo `HttpSession`, nhưng sẽ sử dụng session đã có nếu nó tồn tại.
    *   `STATELESS` (**Bắt buộc cho JWT/API**): Yêu cầu Spring Security **không** tạo hoặc sử dụng `HttpSession` để lưu trữ `SecurityContext`. Điều này đảm bảo ứng dụng của bạn thực sự stateless. `SecurityContext` sẽ được tái tạo lại cho mỗi request từ thông tin trong JWT.

### 2. Ví dụ minh họa

**Tình huống:** Cấu hình một `SecurityFilterChain` hoàn chỉnh cho một REST API stateless, bao gồm CORS, tắt CSRF và quản lý session.

```java
@Configuration
@EnableWebSecurity
@EnableMethodSecurity
public class CompleteApiSecurityConfig {

    // ... các bean khác như JwtAuthenticationFilter, UserDetailsService ...

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            // 1. Kích hoạt CORS với cấu hình tùy chỉnh
            .cors(customizer -> customizer.configurationSource(corsConfigurationSource()))
            
            // 2. Tắt CSRF vì chúng ta dùng JWT (stateless)
            .csrf(AbstractHttpConfigurer::disable)
            
            // 3. Cấu hình xử lý exception cho security
            .exceptionHandling(exceptions -> exceptions
                .authenticationEntryPoint(new HttpStatusEntryPoint(HttpStatus.UNAUTHORIZED)) // Lỗi xác thực
                .accessDeniedHandler((request, response, accessDeniedException) -> response.setStatus(HttpServletResponse.SC_FORBIDDEN)) // Lỗi phân quyền
            )

            // 4. Cấu hình session management thành STATELESS
            .sessionManagement(session -> session
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            
            // 5. Định nghĩa các quy tắc ủy quyền
            .authorizeHttpRequests(auth -> auth
                .antMatchers("/api/auth/**").permitAll()
                .antMatchers("/swagger-ui/**", "/v3/api-docs/**").permitAll()
                .anyRequest().authenticated()
            );

        // 6. Thêm JWT filter
        http.addFilterBefore(jwtAuthenticationFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }
    
    // Bean cung cấp cấu hình CORS
    @Bean
    CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration configuration = new CorsConfiguration();
        configuration.setAllowedOrigins(Arrays.asList("http://localhost:4200")); // Cho phép Angular app
        configuration.setAllowedMethods(Arrays.asList("GET","POST", "PUT", "DELETE", "PATCH", "OPTIONS"));
        configuration.setAllowedHeaders(Arrays.asList("Authorization", "Content-Type"));
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", configuration);
        return source;
    }
}
```

### 3. Mini Exercise

1.  Trong dự án của bạn, hãy thử **bật** lại CSRF (`http.csrf(Customizer.withDefaults())`) trong khi vẫn sử dụng JWT.
2.  Sử dụng Postman để gửi một request `POST` đến một endpoint được bảo vệ (đã đính kèm JWT hợp lệ).
3.  Quan sát kết quả. Request của bạn sẽ bị lỗi gì? (Đáp án: `403 Forbidden`).
4.  Đọc thông báo lỗi (nếu có) hoặc xem log để hiểu tại sao. (Lý do: vì request không chứa CSRF token hợp lệ).
5.  Thực hành này giúp bạn hiểu rõ tại sao CSRF phải được tắt cho API stateless.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** Bạn đang xây dựng một ứng dụng web truyền thống (server-side rendered) sử dụng Spring MVC và Thymeleaf, với cơ chế xác thực dựa trên session và cookie. Bạn có nên tắt CSRF không?

a) Có, CSRF chỉ cần thiết cho các ứng dụng single-page (SPA).
b) Có, vì Spring Security đã đủ an toàn rồi.
c) **Không**, tuyệt đối không. CSRF là một mối đe dọa nghiêm trọng đối với các ứng dụng stateful (dựa trên session/cookie) và việc bật bảo vệ CSRF là bắt buộc.
d) Tùy chọn, chỉ cần bật nếu ứng dụng của bạn xử lý các thông tin nhạy cảm.

---
**Đáp án:** **c) Không, tuyệt đối không. CSRF là một mối đe dọa nghiêm trọng đối với các ứng dụng stateful (dựa trên session/cookie) và việc bật bảo vệ CSRF là bắt buộc.**

**Giải thích:** Cuộc tấn công CSRF hoạt động chính xác là nhờ vào việc trình duyệt tự động gửi cookie khi có request đến một domain. Do đó, bất kỳ ứng dụng nào dựa vào session cookie để xác thực đều là mục tiêu của CSRF. Tắt CSRF trong trường hợp này sẽ tạo ra một lỗ hổng bảo mật nghiêm trọng. Quy tắc vàng là: **Stateful/Session/Cookie -> Bật CSRF. Stateless/JWT/Token Header -> Tắt CSRF.**

---

## Tổng kết chương: Spring Security

### 1. Bảng tổng hợp các khái niệm chính

| Khái niệm / Class / Annotation | Vai trò và Mô tả | Khi nên dùng / Best Practice | Hạn chế / Lưu ý |
| :--- | :--- | :--- | :--- |
| **`SecurityFilterChain`** | Chuỗi các filter xử lý an ninh cho request HTTP. Là trung tâm cấu hình của Spring Security. | Định nghĩa dưới dạng `@Bean` trong lớp `@Configuration`. Cấu hình mọi thứ: authorization, session, cors, csrf... | Thứ tự của các filter và các quy tắc `antMatchers` rất quan trọng. |
| **Authentication vs Authorization** | **AuthN**: Bạn là ai? (Xác thực danh tính). **AuthZ**: Bạn được làm gì? (Kiểm tra quyền). | Luôn phân biệt rõ ràng. AuthZ luôn xảy ra sau khi AuthN thành công. | |
| **`SecurityContextHolder`** | Sử dụng `ThreadLocal` để lưu trữ `SecurityContext` (chứa thông tin `Authentication`) cho request hiện tại. | Là cách chuẩn để truy cập thông tin người dùng đã đăng nhập ở bất kỳ đâu trong ứng dụng. | Context sẽ bị xóa sau khi request hoàn thành. |
| **`UserDetailsService`** | Interface có nhiệm vụ tải thông tin người dùng (thường từ DB) dựa trên username. | Bạn phải cung cấp một implementation của interface này để kết nối Spring Security với model User của bạn. | Phải ném `UsernameNotFoundException` nếu không tìm thấy user. |
| **`PasswordEncoder`** | Interface để mã hóa và so sánh mật khẩu. `BCryptPasswordEncoder` là implementation mặc định. | **Bắt buộc** phải có một bean `PasswordEncoder`. Không bao giờ lưu mật khẩu dạng plain text. | BCrypt được thiết kế để chạy chậm và sử dụng "salt" để tăng cường bảo mật. |
| **Stateless (JWT)** | Mô hình bảo mật không lưu session trên server. Mọi thông tin cần thiết nằm trong token client gửi lên. | **Best practice cho REST API và Microservices.** Tăng khả năng mở rộng và linh hoạt. | Cần cơ chế xử lý token hết hạn (Refresh Token). Payload của JWT không được chứa dữ liệu nhạy cảm. |
| **JWT Filter** | Một filter tùy chỉnh (kế thừa `OncePerRequestFilter`) để xác minh JWT từ header `Authorization` và thiết lập `SecurityContext`. | Là thành phần cốt lõi để tích hợp JWT vào `SecurityFilterChain`. Phải được đặt trước các filter xác thực chuẩn. | |
| **`@PreAuthorize`** | Annotation để bảo vệ phương thức ở tầng Service, hỗ trợ SpEL cho các quy tắc phân quyền động và phức tạp. | Cung cấp lớp bảo vệ thứ hai (Defense in Depth). Rất mạnh để kiểm tra quyền sở hữu (`#id == authentication...`). | Cần bật bằng `@EnableMethodSecurity`. |
| **CORS** | Cơ chế cho phép nới lỏng Same-Origin Policy, cho phép frontend ở origin khác gọi API. | Cần thiết cho các ứng dụng SPA (React, Angular). Cấu hình toàn cục trong `SecurityFilterChain` là tốt nhất. | Hiểu rõ về Preflight request (`OPTIONS`). |
| **CSRF** | Bảo vệ chống lại tấn công Cross-Site Request Forgery. | **Bắt buộc bật** cho các ứng dụng **Stateful** (dùng session/cookie). **Bắt buộc tắt** cho các ứng dụng **Stateless** (dùng JWT). | |
| **`AuthenticationEntryPoint`** | Interface xử lý khi một người dùng chưa xác thực cố gắng truy cập tài nguyên được bảo vệ. | Dùng để tùy chỉnh response trả về (ví dụ: trả về lỗi JSON 401 thay vì chuyển hướng đến trang login). | |
| **`AccessDeniedHandler`** | Interface xử lý khi một người dùng đã xác thực nhưng không có đủ quyền truy cập một tài nguyên. | Dùng để tùy chỉnh response trả về (ví dụ: trả về lỗi JSON 403). | |
| **`@WithMockUser`** | Annotation để giả lập một người dùng đã đăng nhập trong các bài test security. | Cực kỳ hữu ích để viết unit/integration test cho các endpoint/phương thức được bảo vệ. | |

---

### 2. Câu hỏi phỏng vấn toàn diện

#### Cơ bản
1.  Spring Security là gì? Hai vấn đề chính mà nó giải quyết là gì? (Authentication & Authorization).
2.  `SecurityFilterChain` là gì? Hãy mô tả luồng của một request đi qua nó.
3.  `SecurityContextHolder` sử dụng cơ chế gì để làm cho thông tin `Authentication` có sẵn trong suốt một request? (`ThreadLocal`).
4.  Tại sao chúng ta phải sử dụng `PasswordEncoder` như `BCryptPasswordEncoder`? Tại sao không dùng MD5 hay SHA-256?
5.  Sự khác biệt giữa `hasRole('ADMIN')` và `hasAuthority('ROLE_ADMIN')`?

#### Nâng cao & Tình huống
6.  **Hãy so sánh chi tiết giữa mô hình bảo mật Stateful (Session-based) và Stateless (JWT-based). Trong bối cảnh Microservices, tại sao Stateless lại được ưa chuộng hơn?**
7.  **Mô tả chi tiết cấu trúc 3 phần của một JWT. Phần nào đảm bảo tính toàn vẹn của token và bằng cách nào?** (Signature).
8.  **Khi một JWT hết hạn, luồng xử lý lý tưởng ở cả client và server nên như thế nào? (Refresh Token Flow).**
9.  **CSRF là loại tấn công gì? Tại sao chúng ta lại tắt nó cho REST API dùng JWT?**
10. **So sánh ưu/nhược điểm của việc phân quyền ở `SecurityFilterChain` và phân quyền bằng `@PreAuthorize` ở tầng Service. Khi nào bạn sẽ dùng cả hai?** (Defense in Depth).
11. **Giải thích về Preflight Request trong cơ chế CORS.**
12. **Bạn có một phương thức `@Transactional` gọi đến một phương thức khác cũng `@Transactional` nhưng với `Propagation.REQUIRES_NEW`. Làm thế nào để thông tin `SecurityContext` (người dùng hiện tại) có thể được truyền từ transaction cha sang transaction con?** (Gợi ý: `SecurityContextHolder` mặc định dùng `ThreadLocal`, nhưng có thể được cấu hình để truyền qua các thread mới).
13. **`AuthenticationEntryPoint` và `AccessDeniedHandler` khác nhau như thế nào và khi nào chúng được kích hoạt?**
14. **Làm thế nào để bạn test một phương thức service được bảo vệ bởi `@PreAuthorize("hasRole('ADMIN')")`?** (`@WithMockUser(roles = "ADMIN")`).
15. **Trong `SecurityFilterChain`, `antMatchers("/users/**").hasRole("USER")` và `antMatchers("/users/profile").hasRole("ADMIN")` được đặt theo thứ tự đó. Điều gì sẽ xảy ra khi admin truy cập `/users/profile`?** (Sẽ bị từ chối vì quy tắc đầu tiên (`/users/**`) đã khớp và được áp dụng trước. Quy tắc cụ thể hơn phải đặt trước).

---

### 3. Mini Project: "Secure Task API"

Dự án này sẽ tổng hợp các kỹ năng cốt lõi đã học để xây dựng một REST API được bảo vệ hoàn chỉnh.

**Yêu cầu chức năng:**

*   **Xác thực:**
    *   Endpoint `POST /api/auth/register` để đăng ký người dùng mới (username, password, role). Mật khẩu phải được mã hóa bằng BCrypt.
    *   Endpoint `POST /api/auth/login` để đăng nhập. Nếu thành công, trả về một JWT Access Token.
*   **Tài nguyên:** `Task` (id, title, description, completed, owner_username).
*   **Phân quyền:**
    *   Có hai vai trò: `USER` và `ADMIN`.
    *   **Người dùng `USER`:**
        *   Có thể tạo task mới (`POST /api/tasks`). Task sẽ tự động được gán cho chính họ.
        *   Có thể xem danh sách các task **của chính mình** (`GET /api/tasks`).
        *   Có thể cập nhật/xóa các task **của chính mình** (`PUT /api/tasks/{id}`, `DELETE /api/tasks/{id}`).
    *   **Người dùng `ADMIN`:**
        *   Có tất cả các quyền của `USER`.
        *   Có thể xem danh sách task của **tất cả mọi người** (`GET /api/tasks/all`).
        *   Có thể xóa bất kỳ task nào.
*   **Bảo mật nâng cao:**
    *   API phải là **stateless**.
    *   Cấu hình **CORS** để cho phép request từ `http://localhost:3000`.
    *   Tùy chỉnh **Exception Handling** để trả về lỗi `401 Unauthorized` và `403 Forbidden` dưới dạng JSON chuẩn.
*   **Testing:**
    *   Viết unit test cho `JwtAuthenticationFilter`.
    *   Viết integration test cho các endpoint của `TaskController` bằng `MockMvc` và `@WithMockUser` để kiểm tra các quy tắc phân quyền.

#### Gợi ý triển khai:

1.  **Cấu hình Security:**
    *   Tạo `JwtUtil`, `JwtAuthenticationFilter`, `JpaUserDetailsService`.
    *   Tạo `SecurityConfig` với bean `SecurityFilterChain`:
        *   Tắt CSRF, cấu hình session `STATELESS`.
        *   Cấu hình CORS.
        *   Cấu hình `exceptionHandling` với `AuthenticationEntryPoint` và `AccessDeniedHandler`.
        *   Phân quyền ở `authorizeHttpRequests`: cho phép `/api/auth/**`, yêu cầu xác thực cho `/api/tasks/**`.

2.  **Controller & Service:**
    *   `AuthController`: Xử lý `register` và `login`.
    *   `TaskController`: Xử lý CRUD cho Task.
    *   `TaskService`:
        *   Đây là nơi áp dụng `@PreAuthorize` để kiểm tra quyền sở hữu.
        *   Ví dụ cho phương thức xóa:
            ```java
            @PreAuthorize("#task.ownerUsername == authentication.principal.username or hasRole('ADMIN')")
            public void deleteTask(Task task) {
                taskRepository.delete(task);
            }
            // Trong controller, bạn sẽ phải tải task lên trước rồi mới gọi service
            ```
        *   Cách khác (tốt hơn):
            ```java
            // Trong service
            public void deleteTask(Long taskId, UserDetails currentUser) {
                Task task = taskRepository.findById(taskId).orElseThrow(...);
                if (!task.getOwnerUsername().equals(currentUser.getUsername()) && 
                    !currentUser.getAuthorities().contains(new SimpleGrantedAuthority("ROLE_ADMIN"))) {
                    throw new AccessDeniedException("You don't have permission to delete this task");
                }
                taskRepository.delete(task);
            }
            ```

3.  **Testing:**
    *   Test kịch bản `GET /api/tasks` với `@WithMockUser(username="john", roles="USER")` -> chỉ thấy task của john.
    *   Test kịch bản `DELETE /api/tasks/{id}` của user khác với `@WithMockUser(username="jane", roles="USER")` -> nhận 403 Forbidden.
    *   Test kịch bản `DELETE /api/tasks/{id}` của user khác với `@WithMockUser(username="admin", roles="ADMIN")` -> thành công 204 No Content.

