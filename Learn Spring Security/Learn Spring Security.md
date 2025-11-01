### **Mục lục Toàn diện về Spring Security - Từ Zero đến Hero**

#### **[Phần 1: Nền tảng cốt lõi - "Hello Security"](#phần-1-nền-tảng-cốt-lõi---hello-security)**
*   **[1.1. Security là gì? Hai khái niệm VÀNG:](#11-security-là-gì-hai-khái-niệm-vàng)**
    *   **Authentication (Xác thực):** "Bạn là ai?" - Ví dụ về thẻ CMND.
    *   **Authorization (Phân quyền):** "Bạn được làm gì?" - Ví dụ về chìa khóa phòng.
*   **[1.2. Kiến trúc của Spring Security:](#12-kiến-trúc-của-spring-security)**
    *   **Servlet Filters:** Chuỗi "bảo vệ" mà mọi request phải đi qua.
    *   **SecurityFilterChain:** Sơ đồ cấu hình chuỗi bảo vệ đó.
*   **[1.3. "Hello World" với Spring Security:](#13-hello-world-với-spring-security)**
    *   Thêm dependency `spring-boot-starter-security`.
    *   Sự "kỳ diệu" xảy ra: Tự động bảo vệ tất cả, trang đăng nhập mặc định.
*   **[1.4. Cấu hình Tối thiểu với `WebSecurityConfig`:](#14-cấu-hình-tối-thiểu-với-websecurityconfig)**
    *   `@EnableWebSecurity` và `@Configuration`.
    *   `SecurityFilterChain` Bean: Trái tim của cấu hình.
    *   `HttpSecurity`: "Bộ công cụ" để xây dựng chuỗi bảo vệ.
    *   Ví dụ: Cho phép truy cập trang chủ, yêu cầu đăng nhập cho các trang khác.

#### **[Phần 2: Quản lý Người dùng và Mật khẩu](#phần-2-quản-lý-người-dùng-và-mật-khẩu)**
*   **[2.1. `UserDetails`, `UserDetailsService`, `GrantedAuthority`:](#21-userdetails-userdetailsservice-grantedauthority)**
    *   `UserDetails`: "Hồ sơ nhân viên" theo chuẩn Spring.
    *   `UserDetailsService`: "Phòng nhân sự" có nhiệm vụ tìm hồ sơ.
    *   `GrantedAuthority`: Một "quyền" duy nhất trong hồ sơ.
*   **[2.2. In-Memory Authentication:](#22-in-memory-authentication)**
    *   Lưu người dùng trực tiếp trong code - Dùng cho demo và test.
    *   `InMemoryUserDetailsManager`.
*   **[2.3. Mã hóa Mật khẩu với `PasswordEncoder`:](#23-mã-hóa-mật-khẩu-với-passwordencoder)**
    *   Tại sao không bao giờ được lưu mật khẩu dạng plain text?
    *   `BCryptPasswordEncoder`: Tiêu chuẩn vàng hiện nay.
    *   Cách định nghĩa `@Bean` và sử dụng.
*   **[2.4. JDBC/JPA Authentication:](#24-jdbcjpa-authentication)**
    *   Lấy thông tin người dùng từ Cơ sở dữ liệu - Cách làm trong thực tế.
    *   Triển khai `UserDetailsServiceImpl` để kết nối với `UserRepository`.

#### **[Phần 3: Phân quyền Chi tiết (Authorization)](#phần-3-phân-quyền-chi-tiết-authorization)**
*   **[3.1. Phân quyền dựa trên URL:](#31-phân-quyền-dựa-trên-url)**
    *   `.requestMatchers(...)`: Chỉ định các URL.
    *   `.permitAll()`, `.authenticated()`.
    *   `.hasRole("ADMIN")`, `.hasAnyRole("USER", "ADMIN")`.
    *   `.hasAuthority("permission:name")`.
    *   Sự khác biệt giữa `Role` và `Authority` (`ROLE_` prefix).
*   **[3.2. Phân quyền ở cấp độ Phương thức (Method Security):](#32-phân-quyền-ở-cấp-độ-phương-thức-method-security)**
    *   `@EnableMethodSecurity`: Bật "công tắc".
    *   `@PreAuthorize`: "Kiểm tra trước khi chạy" - Mạnh mẽ và linh hoạt nhất.
    *   `@PostAuthorize`: "Kiểm tra sau khi chạy" - Ít dùng hơn.
    *   Sử dụng Spring Expression Language (SpEL) trong annotation.

#### **[Phần 4: Bảo mật cho REST API - Kỷ nguyên Stateless](#phần-4-bảo-mật-cho-rest-api---kỷ-nguyên-stateless)**
*   **[4.1. Stateful vs. Stateless:](#41-stateful-vs-stateless)**
    *   Tại sao Session/Cookie không phù hợp với API?
*   **[4.2. JSON Web Tokens (JWT):](#42-json-web-tokens-jwt)**
    *   JWT là gì? Cấu trúc 3 phần: Header, Payload, Signature.
    *   Luồng hoạt động: Đăng nhập -> Nhận Token -> Gửi Token trong mỗi request.
*   **[4.3. Triển khai JWT trong Spring Security:](#43-triển-khai-jwt-trong-spring-security)**
    *   Tạo `JwtUtils` để tạo và xác thực token.
    *   Tạo `JwtAuthFilter` (kế thừa `OncePerRequestFilter`) để đọc token từ header và thiết lập `SecurityContext`.
    *   Cấu hình `SecurityFilterChain` để sử dụng `JwtAuthFilter` và đặt chế độ `STATELESS`.

#### **[Phần 5: Các Chủ đề Nâng cao và Thực tế](#phần-5-các-chủ-đề-nâng-cao-và-thực-tế)**
*   **[5.1. Xử lý Lỗi Security một cách Chuyên nghiệp:](#51-xử-lý-lỗi-security-một-cách-chuyên-nghiệp)**
    *   Tùy chỉnh lỗi 401 Unauthorized với `AuthenticationEntryPoint`.
    *   Tùy chỉnh lỗi 403 Forbidden với `@RestControllerAdvice` hoặc `AccessDeniedHandler`.
    *   Tại sao phải trả về lỗi JSON cho API?
*   **[5.2. Refresh Tokens:](#52-refresh-tokens)**
    *   Vấn đề của Access Token hết hạn.
    *   Luồng hoạt động của Refresh Token để duy trì đăng nhập.
*   **[5.3. Tích hợp Đăng nhập Mạng xã hội (OAuth2/OIDC):](#53-tích-hợp-đăng-nhập-mạng-xã-hội-oauth2oidc)**
    *   OAuth2 là gì? Khái niệm `Client`, `Resource Server`, `Authorization Server`.
    *   Cấu hình `spring-boot-starter-oauth2-client` để đăng nhập với Google/Facebook.
*   **[5.4. CORS (Cross-Origin Resource Sharing):](#54-cors-cross-origin-resource-sharing)**
    *   Tại sao API của bạn bị lỗi CORS khi gọi từ trình duyệt?
    *   Cách cấu hình CORS trong Spring Security.

#### **[Phần 6: Kiến trúc cho Doanh nghiệp (Enterprise Patterns)](#phần-6-kiến-trúc-cho-doanh-nghiệp-enterprise-patterns)**
*   **[6.1. Phân quyền Động (Dynamic Permissions):](#61-phân-quyền-động-dynamic-permissions)**
    *   Thiết kế CSDL với `User`, `Role`, `Permission`.
    *   Tách biệt `Permission` cho User (UI) và cho API Key.
*   **[6.2. Bảo mật trong Kiến trúc Microservices:](#62-bảo-mật-trong-kiến-trúc-microservices)**
    *   **API Gateway:** Điểm vào duy nhất, người gác cổng.
    *   **Authentication Service:** Dịch vụ cấp phát token.
    *   Xác thực tập trung, Phân quyền phi tập trung.
    *   Giao tiếp Service-to-Service: OAuth2 Client Credentials Flow.
*   **[6.3. Tối ưu Hiệu suất với Caching (Redis):](#63-tối-ưu-hiệu-suất-với-caching-redis)**
    *   Cache những gì ở Auth Service? (Thông tin User, Refresh Token).
    *   Cache những gì ở API Gateway? (Kết quả xác thực API Key, JWT Blacklist).

---

<a id="phần-1-nền-tảng-cốt-lõi---hello-security"></a>
### **Phần 1: Nền tảng cốt lõi - "Hello Security"**

<a id="1.1-security-là-gì-hai-khái-niệm-vàng"></a>
#### **1.1. Security là gì? Hai khái niệm VÀNG**

Trong phát triển phần mềm, "Security" là một lĩnh vực rộng lớn, nhưng đối với một lập trình viên ứng dụng, nó thường xoay quanh hai câu hỏi cốt lõi. Hiểu rõ sự khác biệt giữa hai khái niệm này là điều kiện tiên quyết để làm việc với bất kỳ framework bảo mật nào.

*   **Authentication (Xác thực): "Bạn là ai?"**
    *   **Định nghĩa:** Là quá trình chứng minh danh tính của một chủ thể (người dùng, hệ thống). Hệ thống cần phải chắc chắn rằng bạn chính là người mà bạn tuyên bố.
    *   **Ví dụ đời thực:** Khi bạn đến ngân hàng, nhân viên yêu cầu bạn xuất trình Chứng minh nhân dân (CMND) hoặc Căn cước công dân (CCCD). Họ đang **xác thực** danh tính của bạn.
    *   **Ví dụ trong ứng dụng:** Khi bạn truy cập một trang web, nó yêu cầu bạn nhập **Username và Password**. Đây là hình thức xác thực phổ biến nhất. Các hình thức khác bao gồm: mã OTP, vân tay, nhận diện khuôn mặt, API Key.

*   **Authorization (Phân quyền): "Bạn được làm gì?"**
    *   **Định nghĩa:** Là quá trình quyết định xem một chủ thể (đã được xác thực) có được phép thực hiện một hành động cụ thể hoặc truy cập vào một tài nguyên cụ thể hay không. Quá trình này diễn ra **SAU KHI** xác thực thành công.
    *   **Ví dụ đời thực:** Sau khi ngân hàng xác nhận đúng là bạn (xác thực), họ sẽ kiểm tra xem tài khoản của bạn có đủ tiền để rút 100 triệu không. Nếu không, bạn không được phép rút. Hoặc, một nhân viên ngân hàng (đã xác thực bằng thẻ nhân viên) có quyền truy cập quầy giao dịch, nhưng không có quyền vào kho tiền. Đây là **phân quyền**.
    *   **Ví dụ trong ứng dụng:** Một người dùng thông thường (`ROLE_USER`) sau khi đăng nhập (xác thực) có thể xem sản phẩm, nhưng không thể xóa sản phẩm. Chỉ có người quản trị (`ROLE_ADMIN`) mới có quyền đó.

> **Câu hỏi phỏng vấn:** "Bạn hãy phân biệt giữa Authentication và Authorization?"
> **Trả lời:** "Authentication là quá trình xác minh danh tính, trả lời câu hỏi 'Bạn là ai?', ví dụ như đăng nhập bằng username và password. Authorization là quá trình kiểm tra quyền truy cập sau khi đã xác thực, trả lời câu hỏi 'Bạn được phép làm gì?', ví dụ như kiểm tra xem user có phải là admin để thực hiện một chức năng đặc biệt hay không."

---

<a id="1.2-kiến-trúc-của-spring-security"></a>
#### **1.2. Kiến trúc của Spring Security**

Spring Security không phải là một "hộp đen" ma thuật. Nó được xây dựng dựa trên một kiến trúc rõ ràng và mạnh mẽ dựa trên các tiêu chuẩn của Java Servlet.

*   **Servlet Filters (Các bộ lọc Servlet):**
    *   Hãy tưởng tượng ứng dụng web của bạn là một tòa nhà. Mọi yêu cầu (request) từ bên ngoài muốn đi vào tòa nhà này đều phải đi qua một dãy các "chốt kiểm soát an ninh" được xếp thành một hàng dài ở cổng. Mỗi chốt có một nhiệm vụ riêng. Đây chính là các **Servlet Filter**.
    *   Ví dụ:
        *   Chốt 1: Kiểm tra xem request có chứa mã độc không (`SecurityContextHolderFilter`).
        *   Chốt 2: Kiểm tra xem người dùng có muốn đăng xuất không (`LogoutFilter`).
        *   Chốt 3: Kiểm tra username/password nếu đây là request đăng nhập (`UsernamePasswordAuthenticationFilter`).
        *   Chốt 4: Kiểm tra xem người dùng có đủ quyền truy cập không (`AuthorizationFilter`).
    *   Spring Security cung cấp một bộ các filter tiêu chuẩn cho hầu hết các nhu cầu bảo mật.

*   **SecurityFilterChain:**
    *   **Định nghĩa:** Là một `@Bean` đặc biệt trong Spring, nơi bạn định nghĩa xem chuỗi các "chốt kiểm soát" (Servlet Filter) sẽ được cấu hình và sắp xếp như thế nào. Một ứng dụng có thể có nhiều `SecurityFilterChain`, mỗi chuỗi áp dụng cho một nhóm URL khác nhau (ví dụ: một chuỗi cho API, một chuỗi cho trang quản trị).
    *   **Vai trò:** Đây là nơi bạn, với tư cách là lập trình viên, tương tác chính để "dạy" cho Spring Security biết cách bảo vệ ứng dụng của mình. Bạn sẽ định nghĩa các quy tắc như "URL `/api/**` cần được bảo vệ, nhưng URL `/login` thì không".

---

<a id="1.3-hello-world-với-spring-security"></a>
#### **1.3. "Hello World" với Spring Security**

Cách dễ nhất để thấy Spring Security hoạt động là thêm nó vào một dự án Spring Boot mới.

1.  **Thêm Dependency:**
    Trong file `pom.xml` của một dự án Spring Boot, chỉ cần thêm dependency sau:
    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
    ```

2.  **Sự "kỳ diệu" xảy ra:**
    Ngay sau khi bạn thêm dependency này và khởi động ứng dụng (mà không cần viết thêm bất kỳ dòng code cấu hình nào), Spring Boot Auto-configuration sẽ tự động kích hoạt Spring Security với các cài đặt mặc định:
    *   **Bảo vệ tất cả các Endpoint:** MỌI URL trong ứng dụng của bạn bây giờ đều yêu cầu phải xác thực.
    *   **Tạo một User mặc định:** Một người dùng được tạo ra với username là `user` và một mật khẩu ngẫu nhiên rất dài. Mật khẩu này sẽ được in ra console mỗi khi bạn khởi động ứng dụng.
    *   **Tạo một Trang đăng nhập:** Một trang đăng nhập và đăng xuất cơ bản (dạng form HTML) được tự động tạo ra.
    *   **Kích hoạt Bảo vệ CSRF:** Một cơ chế bảo mật quan trọng được bật mặc định.

    Bây giờ, nếu bạn truy cập `http://localhost:8080`, bạn sẽ không thấy trang chủ của mình mà sẽ bị chuyển hướng đến trang đăng nhập.

---

<a id="1.4-cấu-hình-tối-thiểu-với-websecurityconfig"></a>
#### **1.4. Cấu hình Tối thiểu với `WebSecurityConfig`**

Sử dụng cài đặt mặc định là không thực tế. Chúng ta cần tự định nghĩa các quy tắc của riêng mình.

*   **Tạo lớp cấu hình:**
    ```java
    package com.example.demo.config;

    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.security.config.annotation.web.builders.HttpSecurity;
    import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
    import org.springframework.security.web.SecurityFilterChain;
    import static org.springframework.security.config.Customizer.withDefaults;

    @Configuration
    @EnableWebSecurity // Bật công tắc tổng cho Spring Security
    public class WebSecurityConfig {

        // @Bean này định nghĩa một "chuỗi bộ lọc bảo mật"
        @Bean
        public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
            http
                // Bắt đầu cấu hình các quy tắc cho request HTTP
                .authorizeHttpRequests(authorize -> authorize
                    // 1. Cho phép tất cả các request đến URL "/" và "/home"
                    .requestMatchers("/", "/home").permitAll()
                    // 2. Tất cả các request khác đều phải được xác thực (authenticated)
                    .anyRequest().authenticated()
                )
                // 3. Kích hoạt tính năng đăng nhập bằng form với các cài đặt mặc định
                .formLogin(withDefaults());

            // Xây dựng và trả về đối tượng cấu hình
            return http.build();
        }
    }
    ```

*   **Giải thích các thành phần:**
    *   `@Configuration`: Đánh dấu đây là một lớp chứa các cấu hình cho Spring.
    *   `@EnableWebSecurity`: Annotation quan trọng nhất, kích hoạt các tính năng bảo mật web của Spring Security và cho phép chúng ta tùy chỉnh nó.
    *   **`@Bean SecurityFilterChain filterChain(HttpSecurity http)`**: Đây là phương thức định nghĩa "chuỗi bộ lọc". Spring sẽ tự động inject đối tượng `HttpSecurity` vào đây.
    *   **`HttpSecurity http`**: Đối tượng này là "bộ công cụ" chính. Chúng ta sử dụng nó để xâu chuỗi các phương thức cấu hình lại với nhau.
    *   **`.authorizeHttpRequests(...)`**: Bắt đầu một khối cấu hình để định nghĩa các quy tắc phân quyền cho các URL.
    *   **`.requestMatchers(...)`**: Chỉ định các mẫu URL mà quy tắc sẽ áp dụng.
    *   **`.permitAll()`**: Cho phép tất cả các request (kể cả từ người dùng ẩn danh) truy cập vào các URL đã chỉ định.
    *   **`.authenticated()`**: Yêu cầu người dùng phải đăng nhập (đã được xác thực) để truy cập.
    *   **`.formLogin(withDefaults())`**: Bật tính năng đăng nhập bằng form HTML do Spring Security cung cấp.
    *   **`.build()`**: Kết thúc quá trình cấu hình và tạo ra đối tượng `SecurityFilterChain`.

<a id="phần-2-quản-lý-người-dùng-và-mật-khẩu"></a>
### **Phần 2: Quản lý Người dùng và Mật khẩu**

Sau khi đã thiết lập được các quy tắc cơ bản, bước tiếp theo là "dạy" cho Spring Security biết về những người dùng hợp lệ trong hệ thống của chúng ta. Thay vì dùng user mặc định, chúng ta cần định nghĩa user của riêng mình, và quan trọng hơn là phải lưu trữ mật khẩu của họ một cách an toàn.

---

<a id="2.1-userdetails-userdetailsservice-grantedauthority"></a>
#### **2.1. `UserDetails`, `UserDetailsService`, `GrantedAuthority`**

Đây là bộ ba interface cốt lõi mà Spring Security sử dụng để làm việc với thông tin người dùng một cách trừu tượng, không phụ thuộc vào cách bạn lưu trữ dữ liệu (trong CSDL, LDAP, hay file text).

*   **`GrantedAuthority`:**
    *   **Vai trò:** Đại diện cho một **quyền hạn duy nhất** được cấp cho người dùng. Đây là đơn vị nhỏ nhất của sự phân quyền. Nó có thể là một vai trò (role) như "ADMIN" hoặc một quyền cụ thể (permission) như "DELETE_PRODUCT".
    *   **Triển khai phổ biến:** `SimpleGrantedAuthority`.
    *   **Ví dụ:** `new SimpleGrantedAuthority("ROLE_ADMIN")` hoặc `new SimpleGrantedAuthority("product:delete")`.

*   **`UserDetails`:**
    *   **Vai trò:** Đại diện cho một **"hồ sơ người dùng"** hoàn chỉnh theo cách mà Spring Security hiểu được. Nó chứa các thông tin thiết yếu cho việc xác thực và phân quyền.
    *   **Các phương thức chính:**
        *   `getUsername()`: Trả về tên đăng nhập.
        *   `getPassword()`: Trả về mật khẩu đã được **HASH**.
        *   `getAuthorities()`: Trả về một `Collection` các `GrantedAuthority` (danh sách quyền hạn).
        *   Các phương thức khác để kiểm tra trạng thái tài khoản (có bị khóa, hết hạn không...).
    *   **Triển khai phổ biến:** `org.springframework.security.core.userdetails.User`.

*   **`UserDetailsService`:**
    *   **Vai trò:** Là một interface đóng vai trò như một **"DAO (Data Access Object)"** hay **"Repository"** cho `UserDetails`. Nó có duy nhất một phương thức: `loadUserByUsername(String username)`.
    *   **Nhiệm vụ:** Khi một người dùng cố gắng đăng nhập, Spring Security sẽ gọi phương thức này, truyền vào username mà người dùng đã nhập. Nhiệm vụ của bạn là triển khai phương thức này để tìm kiếm người dùng trong nguồn dữ liệu của bạn (ví dụ: CSDL), sau đó đóng gói thông tin tìm được vào một đối tượng `UserDetails` và trả về.

---

<a id="2.2-in-memory-authentication-xác-thực-trong-bộ-nhớ"></a>
#### **2.2. In-Memory Authentication (Xác thực trong bộ nhớ)**

Đây là cách đơn giản nhất để bắt đầu và rất hữu ích cho việc phát triển, demo hoặc viết test. Chúng ta sẽ định nghĩa người dùng ngay trong lớp cấu hình.

*   **Cách triển khai:** Tạo một `@Bean` kiểu `UserDetailsService`.

```java
// Trong lớp WebSecurityConfig.java

import org.springframework.security.core.userdetails.User;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.provisioning.InMemoryUserDetailsManager;

// ...

@Configuration
@EnableWebSecurity
public class WebSecurityConfig {

    // ... Bean SecurityFilterChain giữ nguyên ...

    @Bean
    public UserDetailsService userDetailsService() {
        // Tạo một user tên "user", mật khẩu "password", và có vai trò "USER"
        UserDetails user = User.withDefaultPasswordEncoder()
            .username("user")
            .password("password")
            .roles("USER")
            .build();

        // Tạo một user tên "admin", mật khẩu "password", có cả vai trò "USER" và "ADMIN"
        UserDetails admin = User.withDefaultPasswordEncoder()
            .username("admin")
            .password("password")
            .roles("USER", "ADMIN")
            .build();
        
        // Trả về một manager quản lý 2 user này trong bộ nhớ
        return new InMemoryUserDetailsManager(user, admin);
    }
}
```

> **Cảnh báo phỏng vấn:** Phương thức `withDefaultPasswordEncoder()` đã bị đánh dấu là deprecated và **không an toàn cho môi trường production**. Nó chỉ nên được dùng cho mục đích demo nhanh. Lý do là vì nó sử dụng một cơ chế mã hóa yếu và không khuyến khích. Luôn luôn sử dụng một `PasswordEncoder` tường minh.

---

<a id="2.3-mã-hóa-mật-khẩu-với-passwordencoder"></a>
#### **2.3. Mã hóa Mật khẩu với `PasswordEncoder`**

Đây là một trong những khái niệm bảo mật cơ bản và quan trọng nhất.

*   **Tại sao không bao giờ được lưu mật khẩu dạng plain text?**
    *   Nếu cơ sở dữ liệu của bạn bị rò rỉ, toàn bộ mật khẩu của người dùng sẽ bị lộ. Kẻ tấn công có thể dùng chúng để truy cập không chỉ ứng dụng của bạn mà còn các dịch vụ khác nếu người dùng tái sử dụng mật khẩu.
*   **Giải pháp: Hashing một chiều (One-way Hashing)**
    *   Chúng ta sử dụng một thuật toán để "băm" mật khẩu thành một chuỗi ký tự gần như không thể dịch ngược lại.
    *   `"password123"` -> `(BCrypt)` -> `"$2a$10$3zHzb.NpvL2jZbY0Soa25uKy5t4M5iLlOCeB1g3zMA20asg7a40fK"`
    *   Khi người dùng đăng nhập, hệ thống sẽ băm mật khẩu mà họ vừa nhập và so sánh chuỗi đã băm đó với chuỗi được lưu trong CSDL.

*   **`BCryptPasswordEncoder`:**
    *   Đây là thuật toán được khuyến nghị bởi Spring Security.
    *   **Ưu điểm:** Nó chậm một cách có chủ đích để chống lại các cuộc tấn công brute-force. Nó cũng tích hợp "salt" (một chuỗi ngẫu nhiên) vào mỗi hash, nghĩa là hai người dùng có cùng mật khẩu "123456" sẽ có hai chuỗi hash khác nhau trong CSDL, tăng cường bảo mật.

*   **Cách triển khai:**
    1.  Định nghĩa `PasswordEncoder` như một `@Bean`.
    2.  Spring Security sẽ tự động phát hiện và sử dụng `@Bean` này để so sánh mật khẩu trong quá trình xác thực.

    ```java
    // Trong lớp WebSecurityConfig.java
    import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
    import org.springframework.security.crypto.password.PasswordEncoder;

    // ...

    @Bean
    public PasswordEncoder passwordEncoder() {
        // Sử dụng BCrypt, độ mạnh mặc định là 10
        return new BCryptPasswordEncoder();
    }
    ```

---

<a id="2.4-jdbc-jpa-authentication-xác-thực-qua-csdl"></a>
#### **2.4. JDBC/JPA Authentication (Xác thực qua CSDL)**

Đây là cách làm tiêu chuẩn trong hầu hết các ứng dụng thực tế.

*   **Luồng hoạt động:**
    1.  Tạo các Entity `User` và `Role` (hoặc `Permission`).
    2.  Tạo `UserRepository` để truy vấn CSDL.
    3.  Tạo một class `UserDetailsServiceImpl` implement `UserDetailsService`.
    4.  Trong phương thức `loadUserByUsername`, dùng `UserRepository` để tìm user, sau đó chuyển đổi Entity `User` thành một đối tượng `UserDetails`.

*   **Ví dụ triển khai:**

    **1. Entity `User.java` (ví dụ đơn giản):**
    ```java
    @Entity
    @Table(name = "users")
    public class User {
        @Id
        private Long id;
        private String username;
        private String password; // Mật khẩu đã được hash
        private String role;     // Ví dụ: "ROLE_USER", "ROLE_ADMIN"
        // ... getters, setters
    }
    ```
    **2. `UserRepository.java`:**
    ```java
    public interface UserRepository extends JpaRepository<User, Long> {
        Optional<User> findByUsername(String username);
    }
    ```
    **3. `UserDetailsServiceImpl.java`:**
    ```java
    package com.example.demo.service;

    import com.example.demo.model.User;
    import com.example.demo.repository.UserRepository;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.security.core.authority.SimpleGrantedAuthority;
    import org.springframework.security.core.userdetails.UserDetails;
    import org.springframework.security.core.userdetails.UserDetailsService;
    import org.springframework.security.core.userdetails.UsernameNotFoundException;
    import org.springframework.stereotype.Service;

    import java.util.Collections;

    @Service // Đánh dấu đây là một Spring Bean
    public class UserDetailsServiceImpl implements UserDetailsService {

        @Autowired
        private UserRepository userRepository;

        @Override
        public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
            // 1. Tìm user trong CSDL
            User user = userRepository.findByUsername(username)
                .orElseThrow(() -> new UsernameNotFoundException("User not found with username: " + username));
            
            // 2. Chuyển đổi thông tin user thành một đối tượng UserDetails
            return new org.springframework.security.core.userdetails.User(
                user.getUsername(),
                user.getPassword(),
                // Chuyển đổi chuỗi role thành một GrantedAuthority
                Collections.singletonList(new SimpleGrantedAuthority(user.getRole()))
            );
        }
    }
    ```
    **4. Cấu hình `WebSecurityConfig.java`:**
    Khi bạn đã có một `@Bean` kiểu `UserDetailsService` và một `@Bean` kiểu `PasswordEncoder`, Spring Security sẽ tự động kết nối chúng lại với nhau. Bạn không cần phải cấu hình gì thêm. Nó sẽ tự động sử dụng `UserDetailsServiceImpl` của bạn để tìm người dùng và `BCryptPasswordEncoder` để kiểm tra mật khẩu.

<a id="phần-3-phân-quyền-chi-tiết-authorization"></a>
### **Phần 3: Phân quyền Chi tiết (Authorization)**

Sau khi hệ thống đã xác thực thành công người dùng (biết họ là ai), bước tiếp theo và quan trọng không kém là quyết định xem người dùng đó được phép làm gì. Đây là lúc chúng ta đi sâu vào **Authorization**. Spring Security cung cấp hai cách tiếp cận chính, mạnh mẽ và bổ trợ cho nhau: phân quyền dựa trên URL và phân quyền ở cấp độ phương thức.

---

<a id="3.1-phân-quyền-dựa-trên-url-url-based-authorization"></a>
#### **3.1. Phân quyền dựa trên URL (URL-based Authorization)**

Đây là cách tiếp cận phổ biến và cơ bản nhất. Chúng ta định nghĩa các quy tắc truy cập trực tiếp trong `WebSecurityConfig` dựa trên các mẫu URL (URL patterns). Nó giống như việc bạn đặt các biển báo "Chỉ nhân viên", "Khu vực hạn chế" ở các hành lang và cửa ra vào khác nhau trong một tòa nhà.

*   **Cách cấu hình:** Sử dụng phương thức `authorizeHttpRequests` trên đối tượng `HttpSecurity`.

*   **Các phương thức chính:**
    *   **`.requestMatchers(...)`**: Dùng để chỉ định một hoặc nhiều mẫu URL mà quy tắc sẽ được áp dụng. Nó hỗ trợ cả Ant patterns (ví dụ: `/admin/**`, `/users/?`, `/*.css`).
    *   **`.permitAll()`**: Cho phép tất cả các request đến URL này, không cần xác thực. Thường dùng cho trang chủ, trang đăng nhập, đăng ký, các file CSS/JS.
    *   **`.authenticated()`**: Yêu cầu người dùng phải đăng nhập (đã được xác thực) để truy cập. Không quan trọng họ có vai trò gì, miễn là đã đăng nhập.
    *   **`.hasRole("...")`**: Yêu cầu người dùng phải có một vai trò (role) cụ thể. Ví dụ: `.hasRole("ADMIN")`.
    *   **`.hasAnyRole("...", "...")`**: Yêu cầu người dùng có ít nhất một trong các vai trò được liệt kê. Ví dụ: `.hasAnyRole("ADMIN", "MANAGER")`.
    *   **`.hasAuthority("...")`**: Yêu cầu người dùng phải có một quyền hạn (authority) cụ thể. Ví dụ: `.hasAuthority("product:delete")`.
    *   **`.hasAnyAuthority("...", "...")`**: Yêu cầu người dùng có ít nhất một trong các quyền hạn được liệt kê.

*   **Quy tắc Vàng về Thứ tự:** Spring Security sẽ áp dụng quy tắc **đầu tiên** mà nó tìm thấy khớp với URL. Do đó, bạn phải luôn đặt các quy tắc **cụ thể hơn lên trên** các quy tắc chung chung hơn.

*   **Ví dụ chi tiết trong `WebSecurityConfig.java`:**

    ```java
    // Trong lớp WebSecurityConfig.java

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .authorizeHttpRequests(authorize -> authorize
                // QUY TẮC CỤ THỂ LÊN TRÊN
                // 1. Chỉ những người có vai trò ADMIN mới được truy cập vào bất kỳ URL nào bắt đầu bằng /admin
                .requestMatchers("/admin/**").hasRole("ADMIN")

                // 2. Những người có vai trò MANAGER hoặc ADMIN mới được truy cập URL /management
                .requestMatchers("/management").hasAnyRole("MANAGER", "ADMIN")

                // 3. Chỉ những người có quyền hạn 'product:write' mới được tạo sản phẩm
                .requestMatchers(HttpMethod.POST, "/products").hasAuthority("product:write")

                // 4. Bất kỳ ai (kể cả đã đăng nhập) cũng có thể xem sản phẩm
                .requestMatchers(HttpMethod.GET, "/products", "/products/**").permitAll()

                // QUY TẮC CHUNG CHUNG XUỐNG DƯỚI
                // 5. Bất kỳ URL nào bắt đầu bằng /api/ đều yêu cầu người dùng phải đăng nhập (authenticated)
                .requestMatchers("/api/**").authenticated()

                // 6. Cho phép tất cả mọi người truy cập trang chủ, trang đăng nhập và các file tĩnh
                .requestMatchers("/", "/login", "/css/**", "/js/**").permitAll()

                // 7. QUY TẮC CUỐI CÙNG: Bất kỳ request nào khác không khớp với các quy tắc trên đều phải đăng nhập.
                // Đây là quy tắc "bắt tất cả" để đảm bảo an toàn.
                .anyRequest().authenticated()
            )
            .formLogin(withDefaults());
        return http.build();
    }
    ```

> **Câu hỏi phỏng vấn:** "Sự khác biệt giữa `hasRole` và `hasAuthority` là gì?"
> **Trả lời:** "`hasAuthority('SOME_PERMISSION')` sẽ kiểm tra xem danh sách quyền của người dùng có chính xác chuỗi 'SOME_PERMISSION' hay không. Trong khi đó, `hasRole('ADMIN')` là một cách viết tắt tiện lợi, nó sẽ tự động kiểm tra quyền `ROLE_ADMIN`. Nói cách khác, `hasRole("ADMIN")` tương đương với `hasAuthority("ROLE_ADMIN")`. Việc sử dụng `hasRole` giúp code rõ ràng hơn khi làm việc với các vai trò."

---

<a id="3.2-phân-quyền-ở-cấp-độ-phương-thức-method-security"></a>
#### **3.2. Phân quyền ở cấp độ Phương thức (Method Security)**

Phân quyền dựa trên URL là rất tốt, nhưng đôi khi nó không đủ chi tiết. Ví dụ, bạn có một phương thức service nhận vào ID của một hóa đơn và bạn muốn kiểm tra xem người dùng hiện tại có phải là chủ sở hữu của hóa đơn đó không. Logic này rất khó để biểu diễn bằng URL. Đây là lúc Method Security tỏa sáng.

Nó cho phép bạn viết các quy tắc phân quyền trực tiếp trên các phương thức trong lớp Service hoặc Controller.

*   **Bước 1: Bật tính năng:**
    Thêm annotation `@EnableMethodSecurity` vào lớp `WebSecurityConfig`.
    ```java
    @Configuration
    @EnableWebSecurity
    @EnableMethodSecurity(prePostEnabled = true) // prePostEnabled = true là cần thiết để dùng @PreAuthorize
    public class WebSecurityConfig {
        // ...
    }
    ```

*   **Bước 2: Sử dụng các Annotation:**

    *   **`@PreAuthorize("...")`**: Đây là annotation mạnh mẽ và được sử dụng nhiều nhất.
        *   **Chức năng:** Nó thực thi biểu thức logic **TRƯỚC KHI** phương thức được gọi. Nếu biểu thức trả về `false`, phương thức sẽ không được thực thi và một `AccessDeniedException` sẽ được ném ra.
        *   **Ưu điểm:** Cực kỳ linh hoạt vì nó hỗ trợ **Spring Expression Language (SpEL)**.

    *   **`@PostAuthorize("...")`**:
        *   **Chức năng:** Thực thi biểu thức logic **SAU KHI** phương thức đã được thực thi và trả về kết quả.
        *   **Cách dùng:** Ít phổ biến hơn. Thường dùng trong trường hợp bạn cần kiểm tra quyền dựa trên kết quả trả về của phương thức. Ví dụ: "Chỉ trả về đối tượng `Invoice` nếu người dùng hiện tại là chủ sở hữu của nó".

*   **Spring Expression Language (SpEL) trong Security:**
    SpEL cho phép bạn viết các biểu thức phức tạp bên trong annotation. Một số biểu thức phổ biến:
    *   `hasRole('...')`, `hasAnyRole('...')`
    *   `hasAuthority('...')`, `hasAnyAuthority('...')`
    *   `isAuthenticated()`, `isAnonymous()`
    *   `principal`: Tham chiếu đến đối tượng principal (thường là `UserDetails`). Bạn có thể truy cập các thuộc tính của nó, ví dụ: `principal.username`.
    *   **Tham chiếu đến tham số của phương thức:** Bạn có thể truy cập các tham số của phương thức bằng cách sử dụng `#` theo sau là tên tham số. Ví dụ: `#invoiceId`.

*   **Ví dụ chi tiết trong một lớp Service:**

    ```java
    package com.example.demo.service;

    import com.example.demo.model.Invoice;
    import org.springframework.security.access.prepost.PostAuthorize;
    import org.springframework.security.access.prepost.PreAuthorize;
    import org.springframework.stereotype.Service;

    @Service
    public class InvoiceService {

        // Ví dụ 1: Chỉ admin mới có thể xem tất cả các hóa đơn
        @PreAuthorize("hasRole('ADMIN')")
        public List<Invoice> findAllInvoices() {
            // ... logic để lấy tất cả hóa đơn
        }

        // Ví dụ 2: Phân quyền dựa trên tham số đầu vào
        // Chỉ admin HOẶC người dùng có username khớp với username trên hóa đơn mới được xem
        @PostAuthorize("hasRole('ADMIN') or returnObject.username == principal.username")
        public Invoice findInvoiceById(Long invoiceId) {
            // ... logic để tìm hóa đơn theo id
            // Giả sử đối tượng Invoice có thuộc tính 'username'
            Invoice invoice = invoiceRepository.findById(invoiceId).orElse(null);
            return invoice;
        }

        // Ví dụ 3: Phân quyền dựa trên nội dung của đối tượng đầu vào
        // Người dùng phải có quyền 'invoice:update' VÀ họ phải là chủ sở hữu của hóa đơn
        @PreAuthorize("hasAuthority('invoice:update') and #invoice.username == authentication.name")
        public void updateInvoice(Invoice invoice) {
            // 'authentication.name' là một cách khác để lấy username hiện tại
            // ... logic để cập nhật hóa đơn
        }
    }
    ```

> **Câu hỏi phỏng vấn:** "Khi nào bạn sẽ chọn Method Security thay vì URL-based security?"
> **Trả lời:** "Tôi sẽ chọn URL-based security cho các quy tắc phân quyền chung và đơn giản, ví dụ như chặn toàn bộ khu vực `/admin` chỉ cho vai trò ADMIN. Tuy nhiên, khi cần các quy tắc phân quyền phức tạp, phụ thuộc vào dữ liệu (data-dependent), ví dụ như kiểm tra xem người dùng có phải là chủ sở hữu của một đối tượng cụ thể hay không, thì Method Security với `@PreAuthorize` và SpEL là lựa chọn vượt trội vì nó cho phép tôi viết logic kiểm tra ngay tại tầng nghiệp vụ và truy cập được vào cả tham số và kết quả trả về của phương thức."

<a id="phần-4-bảo-mật-cho-rest-api---kỷ-nguyên-stateless"></a>
### **Phần 4: Bảo mật cho REST API - Kỷ nguyên Stateless**

Khi chúng ta chuyển từ việc xây dựng các ứng dụng web truyền thống (server-side rendered) sang các ứng dụng hiện đại như Single Page Applications (React, Vue, Angular) hoặc ứng dụng di động, mô hình bảo mật cũng phải thay đổi. Các ứng dụng này giao tiếp với backend thông qua REST APIs, và mô hình bảo mật dựa trên Session-Cookie không còn là lựa chọn tối ưu.

---

<a id="4.1-stateful-vs-stateless"></a>
#### **4.1. Stateful vs. Stateless**

*   **Stateful (Dựa trên trạng thái - Mô hình truyền thống):**
    *   **Luồng hoạt động:**
        1.  Người dùng đăng nhập.
        2.  Server tạo một **Session** (một vùng nhớ) để lưu trữ thông tin về người dùng đã đăng nhập (ví dụ: `Authentication` object).
        3.  Server tạo một **Session ID** duy nhất và gửi nó về cho trình duyệt của người dùng dưới dạng một **Cookie**.
        4.  Trong các request tiếp theo, trình duyệt tự động đính kèm Cookie chứa Session ID.
        5.  Server nhận request, đọc Session ID từ Cookie, tìm đến Session tương ứng trong bộ nhớ của mình để biết người dùng là ai và có quyền gì.
    *   **Nhược điểm cho API:**
        *   **Khó mở rộng (Scalability):** Nếu bạn có nhiều server, bạn phải tìm cách đồng bộ hóa Session giữa chúng. Nếu một request đến server A, rồi request tiếp theo đến server B (chưa có session), người dùng sẽ bị đăng xuất.
        *   **Phụ thuộc vào Cookie:** Các client không phải trình duyệt (như ứng dụng di động, script, server khác) không quản lý Cookie một cách tự nhiên.
        *   **Tăng tải cho Server:** Server phải duy trì một lượng lớn các session trong bộ nhớ.

*   **Stateless (Phi trạng thái - Mô hình cho API):**
    *   **Nguyên tắc vàng:** Server **không lưu trữ bất kỳ thông tin nào** về trạng thái đăng nhập của người dùng. "Trí nhớ" của server được làm mới sau mỗi request.
    *   **Luồng hoạt động:**
        1.  Người dùng đăng nhập bằng credentials (username/password).
        2.  Server xác thực, nhưng thay vì tạo Session, nó tạo ra một **Token** (một chuỗi ký tự dài). Token này tự nó đã chứa tất cả thông tin cần thiết về người dùng (ID, vai trò, quyền) và đã được ký điện tử để chống giả mạo.
        3.  Server gửi Token về cho Client.
        4.  Client có trách nhiệm **lưu trữ Token** này (ví dụ: trong Local Storage của trình duyệt, hoặc Secure Storage của app di động).
        5.  Với mỗi request tiếp theo đến các tài nguyên được bảo vệ, Client phải **chủ động đính kèm Token** vào header của request (thường là header `Authorization`).
        6.  Server nhận request, đọc Token từ header, **xác thực chữ ký của Token** và đọc thông tin người dùng từ chính Token đó. Nó không cần phải tra cứu ở đâu khác.
    *   **Ưu điểm:**
        *   **Dễ mở rộng:** Bất kỳ server nào cũng có thể xác thực token miễn là nó có "chìa khóa bí mật", không cần đồng bộ session.
        *   **Linh hoạt:** Hoạt động tốt với mọi loại client (web, mobile, IoT...).
        *   **Hiệu suất:** Giảm tải cho server vì không phải quản lý session.

---

<a id="4.2-json-web-tokens-jwt"></a>
#### **4.2. JSON Web Tokens (JWT)**

JWT (phát âm là "jot") là một tiêu chuẩn mở (RFC 7519) và là cách phổ biến nhất để triển khai xác thực stateless.

*   **JWT là gì?** Nó là một chuỗi ký tự trông giống như `xxxxx.yyyyy.zzzzz`. Chuỗi này được tạo thành từ 3 phần, được mã hóa Base64Url và ngăn cách bởi dấu chấm.
    *   **1. Header (Phần đầu):** Chứa thông tin về loại token và thuật toán ký được sử dụng.
        *   Ví dụ: `{ "alg": "HS512", "typ": "JWT" }`
    *   **2. Payload (Phần thân):** Chứa các "claims" (thông tin) về người dùng. Đây là nơi bạn sẽ đặt ID, username, vai trò, quyền, và thời gian hết hạn.
        *   Ví dụ: `{ "sub": "user@example.com", "authorities": ["ROLE_USER"], "exp": 1678886400 }`
    *   **3. Signature (Phần chữ ký):** Đây là phần quan trọng nhất về mặt bảo mật. Nó được tạo ra bằng cách:
        `Chữ ký = Thuật_toán_ký(mã_hóa_base64(Header) + "." + mã_hóa_base64(Payload), Chìa_khóa_bí_mật)`
        *   Chữ ký này đảm bảo rằng Header và Payload không bị ai chỉnh sửa trên đường truyền. Nếu ai đó cố gắng thay đổi `"authorities": ["ROLE_USER"]` thành `"authorities": ["ROLE_ADMIN"]`, chữ ký sẽ không còn khớp nữa.

> **Câu hỏi phỏng vấn:** "Dữ liệu trong JWT có được mã hóa không? Nó có an toàn không?"
> **Trả lời:** "Dữ liệu trong Header và Payload của JWT chỉ được **mã hóa Base64Url**, không phải mã hóa bảo mật. Bất kỳ ai cũng có thể giải mã Base64 để đọc nội dung bên trong. Do đó, **tuyệt đối không được lưu các thông tin nhạy cảm** như mật khẩu, số thẻ tín dụng trong payload. Sự an toàn của JWT nằm ở **phần Chữ ký (Signature)**. Chữ ký đảm bảo tính **toàn vẹn (integrity)** và **xác thực nguồn gốc (authenticity)** của token, nghĩa là nó đảm bảo token không bị chỉnh sửa và được cấp phát bởi một server đáng tin cậy."

---

<a id="4.3-triển-khai-jwt-trong-spring-security"></a>
#### **4.3. Triển khai JWT trong Spring Security**

Để tích hợp JWT, chúng ta cần thực hiện 3 bước chính:

**1. Tạo một lớp tiện ích `JwtUtils.java`:** Lớp này chịu trách nhiệm:
*   Tạo ra một JWT mới khi người dùng đăng nhập thành công.
*   Phân tích (parse) một JWT nhận được từ request.
*   Xác thực (validate) chữ ký và thời gian hết hạn của JWT.
*   Trích xuất username và các thông tin khác từ JWT.

**2. Tạo một bộ lọc `JwtAuthFilter.java`:**
*   Bộ lọc này sẽ kế thừa từ `OncePerRequestFilter`.
*   Nhiệm vụ của nó là chặn **mọi request** đi vào ứng dụng.
*   Nó sẽ đọc header `Authorization` để tìm chuỗi "Bearer `xxxxx.yyyyy.zzzzz`".
*   Nếu tìm thấy token, nó sẽ dùng `JwtUtils` để xác thực và phân tích token.
*   Nếu token hợp lệ, nó sẽ lấy thông tin người dùng và các quyền từ token, tạo ra một đối tượng `Authentication` của Spring Security, và **đặt nó vào `SecurityContextHolder`**.
*   Sau đó, nó cho request đi tiếp. Phần còn lại của Spring Security (ví dụ: `@PreAuthorize`) sẽ hoạt động như bình thường vì `SecurityContext` đã có thông tin người dùng.

**3. Cấu hình `WebSecurityConfig.java`:**
*   **Vô hiệu hóa các tính năng stateful:** Chúng ta cần tắt CSRF (vì không dùng cookie) và đặt chế độ quản lý session thành `STATELESS`.
```java
.csrf(csrf -> csrf.disable())
.sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
```
*   **Vô hiệu hóa `formLogin`:** Chúng ta sẽ không dùng trang đăng nhập do Spring cung cấp nữa, mà sẽ tự tạo một endpoint `/api/auth/login` để trả về JWT.
*   **Thêm `JwtAuthFilter` vào chuỗi bộ lọc:** Dùng phương thức `http.addFilterBefore(...)` để chèn bộ lọc của chúng ta vào đúng vị trí.

*   **Ví dụ Cấu hình trong `WebSecurityConfig.java`:**
    ```java
    // Trong WebSecurityConfig.java

    @Autowired
    private JwtAuthFilter jwtAuthFilter; // Inject bộ lọc của chúng ta

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            .csrf(csrf -> csrf.disable()) // Tắt CSRF
            // Cấu hình xử lý exception cho lỗi 401 (sẽ nói ở phần sau)
            // .exceptionHandling(...) 
            // Đặt chế độ session là STATELESS
            .sessionManagement(session -> session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .authorizeHttpRequests(authorize -> authorize
                // Endpoint đăng nhập và đăng ký thì cho phép tất cả
                .requestMatchers("/api/auth/**").permitAll()
                // Tất cả các request khác đều phải được xác thực
                .anyRequest().authenticated()
            );

        // Thêm bộ lọc JWT của chúng ta để xử lý xác thực cho mỗi request
        http.addFilterBefore(jwtAuthFilter, UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }
    ```

<a id="phần-5-các-chủ-đề-nâng-cao-và-thực-tế"></a>
### **Phần 5: Các Chủ đề Nâng cao và Thực tế**

Sau khi đã nắm vững các kiến thức nền tảng và cách bảo mật API bằng JWT, chúng ta sẽ đi sâu vào các vấn đề thực tế mà bất kỳ dự án nào cũng sẽ gặp phải. Việc xử lý tốt các chủ đề này sẽ giúp ứng dụng của bạn chuyên nghiệp, an toàn và thân thiện hơn với người dùng cũng như các lập trình viên khác.

---

<a id="5.1-xử-lý-lỗi-security-một-cách-chuyên-nghiệp"></a>
#### **5.1. Xử lý Lỗi Security một cách Chuyên nghiệp**

Khi một yêu cầu bảo mật thất bại, Spring Security theo mặc định sẽ trả về một trang lỗi HTML (cho lỗi 403) hoặc thực hiện chuyển hướng (cho lỗi 401). Điều này hoàn toàn không phù hợp với REST API, nơi client (ví dụ: ứng dụng React) mong muốn nhận được một phản hồi JSON có cấu trúc rõ ràng.

*   **Tại sao phải trả về lỗi JSON?**
    *   **Nhất quán:** Toàn bộ API của bạn giao tiếp bằng JSON, kể cả khi thành công hay thất bại.
    *   **Dễ xử lý cho Client:** Frontend có thể dễ dàng đọc mã lỗi, thông báo lỗi từ JSON để hiển thị cho người dùng một cách thân thiện, thay vì phải phân tích một trang HTML phức tạp.
    *   **Cung cấp thông tin:** Bạn có thể thêm các thông tin hữu ích vào response lỗi như timestamp, mã lỗi nội bộ, đường dẫn xảy ra lỗi.

Chúng ta cần xử lý hai loại lỗi chính:

*   **Lỗi 401 Unauthorized (Xác thực thất bại):**
    *   **Khi nào xảy ra?** Khi một người dùng chưa xác thực (không gửi token, gửi token sai, token hết hạn) cố gắng truy cập một tài nguyên yêu cầu đăng nhập. Lỗi này xảy ra rất sớm trong chuỗi bộ lọc.
    *   **Cách xử lý:** Sử dụng một `AuthenticationEntryPoint` tùy chỉnh. Đây là một component được Spring Security gọi đến khi nó phát hiện một `AuthenticationException`.
    *   **Ví dụ `CustomAuthenticationEntryPoint.java`:**
        ```java
        @Component
        public class CustomAuthenticationEntryPoint implements AuthenticationEntryPoint {
            @Override
            public void commence(HttpServletRequest request, HttpServletResponse response, AuthenticationException authException) throws IOException, ServletException {
                response.setContentType(MediaType.APPLICATION_JSON_VALUE);
                response.setStatus(HttpServletResponse.SC_UNAUTHORIZED);

                Map<String, Object> body = new HashMap<>();
                body.put("status", HttpServletResponse.SC_UNAUTHORIZED);
                body.put("error", "Unauthorized");
                body.put("message", "Token xác thực không hợp lệ hoặc đã hết hạn.");
                body.put("path", request.getServletPath());

                new ObjectMapper().writeValue(response.getOutputStream(), body);
            }
        }
        ```

*   **Lỗi 403 Forbidden (Từ chối truy cập):**
    *   **Khi nào xảy ra?** Khi một người dùng **đã xác thực thành công** nhưng lại không có đủ quyền (role/authority) để thực hiện một hành động (ví dụ: user thường cố gắng gọi API của admin). Lỗi này thường xảy ra muộn hơn, ở tầng phân quyền (ví dụ: khi `@PreAuthorize` thất bại).
    *   **Cách xử lý:** Sử dụng `@RestControllerAdvice`. Đây là một cơ chế của Spring MVC cho phép bạn tạo một "bộ xử lý exception toàn cục" để bắt và tùy chỉnh phản hồi cho các loại exception cụ thể.
    *   **Ví dụ `GlobalExceptionHandler.java`:**
        ```java
        @RestControllerAdvice
        public class GlobalExceptionHandler {
            @ExceptionHandler(AccessDeniedException.class)
            @ResponseStatus(HttpStatus.FORBIDDEN)
            public Map<String, Object> handleAccessDeniedException(AccessDeniedException ex, WebRequest request) {
                Map<String, Object> body = new HashMap<>();
                body.put("status", HttpStatus.FORBIDDEN.value());
                body.put("error", "Forbidden");
                body.put("message", "Bạn không có quyền thực hiện hành động này.");
                return body;
            }
        }
        ```

*   **Kết nối chúng vào `WebSecurityConfig`:**
    ```java
    // Trong WebSecurityConfig.java

    @Autowired
    private CustomAuthenticationEntryPoint unauthorizedHandler;

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
            // ...
            .exceptionHandling(exception -> exception
                .authenticationEntryPoint(unauthorizedHandler) // Xử lý lỗi 401
            );
            // ...
        return http.build();
    }
    ```
    *(Lỗi 403 sẽ được `@RestControllerAdvice` tự động bắt mà không cần cấu hình thêm trong `WebSecurityConfig`.)*

---

<a id="5.2-refresh-tokens"></a>
#### **5.2. Refresh Tokens**

*   **Vấn đề:** Access Token (JWT) được thiết kế để có thời gian sống ngắn (ví dụ: 15-60 phút) nhằm giảm thiểu rủi ro nếu bị lộ. Tuy nhiên, việc bắt người dùng đăng nhập lại mỗi 15 phút là một trải nghiệm cực kỳ tồi tệ.
*   **Giải pháp:** Sử dụng một cặp token:
    *   **Access Token:** Thời gian sống ngắn, dùng để truy cập tài nguyên.
    *   **Refresh Token:** Thời gian sống rất dài (vài ngày hoặc vài tuần), chỉ có một mục đích duy nhất là để **lấy một Access Token mới**.

*   **Luồng hoạt động:**
    1.  **Đăng nhập:** Người dùng đăng nhập thành công. Server trả về **cả hai**: `accessToken` và `refreshToken`.
    2.  **Lưu trữ:** Client lưu cả hai token một cách an toàn. `refreshToken` phải được bảo vệ cẩn thận hơn.
    3.  **Sử dụng:** Client dùng `accessToken` để gọi các API như bình thường.
    4.  **Hết hạn:** Khi một API trả về lỗi 401 (Unauthorized) do `accessToken` hết hạn, client sẽ không bắt người dùng đăng nhập lại.
    5.  **Làm mới:** Thay vào đó, client sẽ tự động gửi một request đến một endpoint đặc biệt (ví dụ: `/api/auth/refresh-token`), kèm theo `refreshToken`.
    6.  **Xác thực Refresh Token:** Server nhận `refreshToken`, kiểm tra trong CSDL xem nó có hợp lệ và chưa bị thu hồi không.
    7.  **Cấp phát Token mới:** Nếu hợp lệ, server sẽ tạo ra một `accessToken` mới (và tùy chọn, một `refreshToken` mới - cơ chế xoay vòng) và trả về cho client.
    8.  **Thử lại:** Client nhận `accessToken` mới, lưu lại và tự động thực hiện lại request API đã thất bại ở bước 4. Quá trình này diễn ra mượt mà và người dùng không hề hay biết.

---

<a id="5.3-tích-hợp-đăng-nhập-mạng-xã-hội-oauth2-oidc"></a>
#### **5.3. Tích hợp Đăng nhập Mạng xã hội (OAuth2/OIDC)**

*   **OAuth2 (Open Authorization 2.0):** Là một framework **phân quyền**. Nó cho phép một ứng dụng (ví dụ: ứng dụng của bạn) có được quyền truy cập hạn chế vào tài nguyên của người dùng trên một dịch vụ khác (ví dụ: Google, Facebook) mà không cần biết mật khẩu của người dùng.
*   **OIDC (OpenID Connect):** Là một lớp **xác thực** mỏng được xây dựng trên nền tảng OAuth2. Nó bổ sung thêm tính năng xác minh danh tính người dùng và trả về thông tin cơ bản của họ (như ID, email, tên) một cách chuẩn hóa. Khi bạn dùng "Login with Google", bạn đang dùng OIDC.

*   **Các khái niệm chính:**
    *   **Resource Owner:** Người dùng cuối (chủ sở hữu dữ liệu).
    *   **Client:** Ứng dụng của bạn.
    *   **Authorization Server:** Máy chủ của nhà cung cấp dịch vụ (ví dụ: Google), chịu trách nhiệm xác thực người dùng và cấp phát "giấy phép" (access token).
    *   **Resource Server:** Máy chủ của nhà cung cấp dịch vụ (ví dụ: Google API), nơi chứa dữ liệu của người dùng.

*   **Cách tích hợp với Spring Boot:**
    *   Thêm dependency `spring-boot-starter-oauth2-client`.
    *   Cấu hình `Client ID` và `Client Secret` (lấy từ Google/Facebook Developer Console) trong file `application.properties`.
    *   Spring Security sẽ tự động tạo ra các endpoint để bắt đầu luồng OAuth2 (ví dụ: `/oauth2/authorization/google`).
    *   Bạn cần cung cấp một `AuthenticationSuccessHandler` tùy chỉnh để xử lý sau khi người dùng đăng nhập thành công qua Google. Trong handler này, bạn sẽ nhận được thông tin của người dùng, kiểm tra xem họ đã có tài khoản trong hệ thống của bạn chưa (nếu chưa thì tạo mới), và cuối cùng là cấp phát JWT của hệ thống bạn cho họ, để họ có thể tiếp tục tương tác với API của bạn.

---

<a id="5.4-cors-cross-origin-resource-sharing"></a>
#### **5.4. CORS (Cross-Origin Resource Sharing)**

*   **Vấn đề:** Theo mặc định, các trình duyệt web tuân theo **Same-Origin Policy (Chính sách Cùng nguồn gốc)**. Điều này có nghĩa là một trang web được tải từ `http://my-frontend.com` sẽ không được phép gửi request AJAX (ví dụ: `fetch`, `axios`) đến một API ở một nguồn gốc khác, ví dụ `http://api.my-backend.com`. Trình duyệt sẽ chặn request này và báo lỗi CORS.
*   **Giải pháp:** CORS là một cơ chế cho phép server (backend của bạn) nói với trình duyệt rằng: "Tôi cho phép các request đến từ nguồn gốc `http://my-frontend.com`".
*   **Cách cấu hình trong Spring Security:**
    *   Cách đơn giản và an toàn nhất là định nghĩa một `CorsConfigurationSource` bean.

    ```java
    // Trong WebSecurityConfig.java

    @Bean
    public CorsConfigurationSource corsConfigurationSource() {
        CorsConfiguration configuration = new CorsConfiguration();
        // Cho phép các nguồn gốc (origin) cụ thể. Dùng "*" là không an toàn cho production.
        configuration.setAllowedOrigins(Arrays.asList("http://localhost:3000", "https://my-frontend.com"));
        // Cho phép các phương thức HTTP
        configuration.setAllowedMethods(Arrays.asList("GET", "POST", "PUT", "DELETE", "OPTIONS"));
        // Cho phép các header
        configuration.setAllowedHeaders(Arrays.asList("Authorization", "Content-Type", "X-Requested-With"));
        // Cho phép gửi cookie (nếu cần)
        configuration.setAllowCredentials(true);
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", configuration); // Áp dụng cho tất cả các URL
        return source;
    }

    // Sau đó, kích hoạt nó trong filterChain
    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http.cors(cors -> cors.configurationSource(corsConfigurationSource())); // Kích hoạt CORS
        // ... các cấu hình khác
        return http.build();
    }
    ```

Phần 6: Kiến trúc cho Doanh nghiệp (Enterprise Patterns)

Khi xây dựng các hệ thống lớn, đặc biệt là các ứng dụng SaaS hoặc kiến trúc Microservices, các yêu cầu về bảo mật sẽ trở nên phức tạp hơn. Chúng ta không chỉ cần xác thực và phân quyền đơn giản mà còn cần sự linh hoạt, khả năng quản lý tập trung và hiệu suất cao. Phần này sẽ đi sâu vào các mẫu thiết kế và kiến trúc bảo mật thường được áp dụng trong môi trường doanh nghiệp.

6.1. Phân quyền Động (Dynamic Permissions)

Vấn đề: Trong các phần trước, chúng ta đã định nghĩa quyền (roles/authorities) một cách "cứng" trong code (ví dụ: @PreAuthorize("hasRole('ADMIN')")). Điều này có nghĩa là mỗi khi bạn muốn thêm một vai trò mới hoặc thay đổi quyền của một vai trò, bạn phải sửa code và triển khai lại ứng dụng. Điều này không linh hoạt, đặc biệt trong một hệ thống SaaS nơi mỗi khách hàng (tenant) có thể có những yêu cầu phân quyền khác nhau.

Giải pháp: Xây dựng một hệ thống phân quyền dựa trên dữ liệu được lưu trong CSDL.

Thiết kế CSDL:

Permissions Table: Một bảng định nghĩa tất cả các quyền có thể có trong hệ thống dưới dạng chuỗi (ví dụ: product:create, product:read, user:invite).

Roles Table: Một bảng định nghĩa các vai trò (ví dụ: Administrator, Manager, Viewer).

Role_Permissions Table: Bảng trung gian (many-to-many) để gán các Permission cho các Role.

Users (hoặc Tenants) Table: Bảng người dùng.

User_Roles Table: Bảng trung gian (many-to-many) để gán các Role cho User.

Luồng hoạt động:

Quản lý: Xây dựng một giao diện quản trị cho phép admin (hoặc khách hàng) có thể tự tạo Role mới và "check/uncheck" các Permission để gán cho vai trò đó.

Đăng nhập: Khi người dùng đăng nhập, Auth Service sẽ truy vấn CSDL để lấy tất cả các Role của người dùng đó, sau đó tiếp tục truy vấn để tổng hợp lại tất cả các Permission từ các Role đó.

Nhúng vào JWT: Danh sách các Permission cuối cùng (ví dụ: ["product:create", "product:read"]) sẽ được nhúng vào claim authorities của JWT.

Thực thi: Các microservice vẫn sử dụng @PreAuthorize("hasAuthority('product:create')") như bình thường. Chúng không cần biết quyền này đến từ đâu, chỉ cần biết nó có trong JWT hay không.

Lợi ích: Quản trị viên có thể thay đổi, tạo mới, hoặc xóa bỏ các vai trò và quyền hạn của người dùng trong thời gian thực mà không cần bất kỳ sự can thiệp nào từ đội ngũ phát triển.

Tách biệt Permission cho User (UI) và cho API Key:

Trong một hệ thống SaaS, các hành động mà một người dùng thực hiện trên giao diện (ví dụ: ui:user:invite) thường khác với các hành động mà một hệ thống khác thực hiện qua API Key (ví dụ: api:product:sync).

Để tăng cường bảo mật, bạn nên có một cơ chế (ví dụ: một cột type trong bảng Permission) để phân loại quyền. Khi cấp phát JWT cho người dùng, chỉ nhúng các quyền loại UI. Khi xác thực API Key, chỉ xem xét các quyền loại API. Điều này ngăn chặn việc vô tình gán một quyền mạnh (dành cho API) cho một người dùng thông thường.

6.2. Bảo mật trong Kiến trúc Microservices

Trong một hệ thống gồm nhiều dịch vụ nhỏ, việc để mỗi dịch vụ tự quản lý user và xác thực là không khả thi. Thay vào đó, chúng ta áp dụng mô hình Xác thực tập trung và Phân quyền phi tập trung.

Các thành phần chính:

API Gateway: Là điểm vào duy nhất cho tất cả các request từ bên ngoài. Nó hoạt động như một "người gác cổng" trung tâm. Mọi request phải đi qua Gateway trước khi đến được các dịch vụ con.

Authentication Service (Auth Service): Là dịch vụ duy nhất trong hệ thống có trách nhiệm quản lý thông tin người dùng, xác thực credentials, và quan trọng nhất là cấp phát và ký (sign) JWT. Nó giữ Private Key.

Downstream Microservices (Các dịch vụ con): Như Product Service, Order Service... Chúng chỉ cần Public Key tương ứng để có thể xác minh (verify) chữ ký của JWT.

Luồng xác thực và phân quyền:

Xác thực tập trung (tại Gateway):

Client gửi request đến API Gateway kèm theo JWT.

API Gateway, với Public Key, sẽ xác thực chữ ký và thời gian hết hạn của JWT.

Nếu JWT không hợp lệ, Gateway sẽ từ chối request ngay lập tức với lỗi 401. Dịch vụ con không bao giờ nhận được request này.

Nếu JWT hợp lệ, Gateway tin tưởng vào thông tin trong đó và chuyển tiếp request đến dịch vụ con tương ứng.

Phân quyền phi tập trung (tại Microservice):

Product Service nhận được request (đã được Gateway xác thực).

Nhiệm vụ của nó bây giờ là phân quyền. Nó đọc các authorities từ JWT.

Nó tự quyết định xem với những quyền đó, người dùng có được phép thực hiện hành động trên tài nguyên "product" hay không (sử dụng @PreAuthorize).

Tương tự, Order Service sẽ tự quyết định về quyền trên tài nguyên "order".

Giao tiếp giữa các Service (Service-to-Service):

Khi một dịch vụ (ví dụ: Order Service) cần gọi đến một dịch vụ khác (ví dụ: Product Service), nó không nên dùng thông tin của người dùng.

Thay vào đó, nó nên sử dụng OAuth2 Client Credentials Flow. Mỗi dịch vụ sẽ có một Client ID và Client Secret. Nó dùng credentials này để gọi đến Auth Service và nhận về một JWT dành riêng cho "danh tính" của chính nó, với các scope được định nghĩa trước (ví dụ: internal:product:read).

Điều này đảm bảo mọi giao tiếp trong hệ thống đều được xác thực và phân quyền một cách an toàn.

6.3. Tối ưu Hiệu suất với Caching (Redis)

Việc xác thực và tra cứu quyền có thể tạo ra gánh nặng cho CSDL, đặc biệt là Auth Service. Sử dụng một lớp cache tốc độ cao như Redis là điều cần thiết.

Cache những gì ở Auth Service?

Mục tiêu: Giảm số lần đọc CSDL chính.

Dữ liệu:

Thông tin User/Tenant và các quyền đã được tổng hợp: Khi một người dùng được load lần đầu, lưu toàn bộ hồ sơ UserDetails của họ vào Redis với TTL (Time-To-Live) ngắn (ví dụ 15-30 phút). Các lần xác thực JWT tiếp theo (nếu cần tra cứu lại) sẽ đọc từ Redis.

Refresh Tokens: Redis là nơi lưu trữ lý tưởng cho refresh token vì nó có cơ chế TTL tự nhiên.

Invalidation: Khi quyền của một user bị thay đổi, cần phải xóa (invalidate) cache của user đó để lần truy cập tiếp theo sẽ đọc lại từ CSDL.

Cache những gì ở API Gateway?

Mục tiêu: Giảm độ trễ cho mọi request và giảm tải cho Auth Service.

Dữ liệu:

Kết quả xác thực API Key: Khi một API Key được xác thực lần đầu, Gateway sẽ gọi Auth Service để lấy danh sách quyền. Gateway nên cache lại kết quả này (API Key -> List<Permission>) với TTL rất ngắn (ví dụ 1-5 phút). Các request tiếp theo với cùng API key sẽ được xử lý ngay tại Gateway mà không cần gọi mạng đến Auth Service.

JWT Blacklist (Danh sách đen JWT): Khi người dùng đăng xuất hoặc đổi mật khẩu, JWT của họ vẫn còn hiệu lực cho đến khi hết hạn. Để vô hiệu hóa nó ngay lập tức, bạn có thể lưu ID của JWT đó vào một danh sách đen trong Redis. Với mỗi request, Gateway sẽ kiểm tra xem JWT có nằm trong danh sách đen này không trước khi xác thực.

Rate Limiting: Gateway là nơi hoàn hảo để triển khai giới hạn tần suất request (ví dụ: 100 request/phút/API key) bằng cách sử dụng các lệnh nguyên tử của Redis như INCR.

<a id="phần-6-kiến-trúc-cho-doanh-nghiệp-enterprise-patterns"></a>
### **Phần 6: Kiến trúc cho Doanh nghiệp (Enterprise Patterns)**

Khi xây dựng các hệ thống lớn, đặc biệt là các ứng dụng SaaS hoặc kiến trúc Microservices, các yêu cầu về bảo mật sẽ trở nên phức tạp hơn. Chúng ta không chỉ cần xác thực và phân quyền đơn giản mà còn cần sự linh hoạt, khả năng quản lý tập trung và hiệu suất cao. Phần này sẽ đi sâu vào các mẫu thiết kế và kiến trúc bảo mật thường được áp dụng trong môi trường doanh nghiệp.

---

<a id="6.1-phân-quyền-động-dynamic-permissions"></a>
#### **6.1. Phân quyền Động (Dynamic Permissions)**

*   **Vấn đề:** Trong các phần trước, chúng ta đã định nghĩa quyền (roles/authorities) một cách "cứng" trong code (ví dụ: `@PreAuthorize("hasRole('ADMIN')")`). Điều này có nghĩa là mỗi khi bạn muốn thêm một vai trò mới hoặc thay đổi quyền của một vai trò, bạn phải sửa code và triển khai lại ứng dụng. Điều này không linh hoạt, đặc biệt trong một hệ thống SaaS nơi mỗi khách hàng (tenant) có thể có những yêu cầu phân quyền khác nhau.

*   **Giải pháp:** Xây dựng một hệ thống phân quyền dựa trên dữ liệu được lưu trong CSDL.
    *   **Thiết kế CSDL:**
        *   **`Permissions` Table:** Một bảng định nghĩa tất cả các quyền có thể có trong hệ thống dưới dạng chuỗi (ví dụ: `product:create`, `product:read`, `user:invite`).
        *   **`Roles` Table:** Một bảng định nghĩa các vai trò (ví dụ: `Administrator`, `Manager`, `Viewer`).
        *   **`Role_Permissions` Table:** Bảng trung gian (many-to-many) để gán các `Permission` cho các `Role`.
        *   **`Users` (hoặc `Tenants`) Table:** Bảng người dùng.
        *   **`User_Roles` Table:** Bảng trung gian (many-to-many) để gán các `Role` cho `User`.
    *   **Luồng hoạt động:**
        1.  **Quản lý:** Xây dựng một giao diện quản trị cho phép admin (hoặc khách hàng) có thể tự tạo `Role` mới và "check/uncheck" các `Permission` để gán cho vai trò đó.
        2.  **Đăng nhập:** Khi người dùng đăng nhập, Auth Service sẽ truy vấn CSDL để lấy tất cả các `Role` của người dùng đó, sau đó tiếp tục truy vấn để tổng hợp lại tất cả các `Permission` từ các `Role` đó.
        3.  **Nhúng vào JWT:** Danh sách các `Permission` cuối cùng (ví dụ: `["product:create", "product:read"]`) sẽ được nhúng vào claim `authorities` của JWT.
        4.  **Thực thi:** Các microservice vẫn sử dụng `@PreAuthorize("hasAuthority('product:create')")` như bình thường. Chúng không cần biết quyền này đến từ đâu, chỉ cần biết nó có trong JWT hay không.
    *   **Lợi ích:** Quản trị viên có thể thay đổi, tạo mới, hoặc xóa bỏ các vai trò và quyền hạn của người dùng trong thời gian thực mà không cần bất kỳ sự can thiệp nào từ đội ngũ phát triển.

*   **Tách biệt Permission cho User (UI) và cho API Key:**
    *   Trong một hệ thống SaaS, các hành động mà một người dùng thực hiện trên giao diện (ví dụ: `ui:user:invite`) thường khác với các hành động mà một hệ thống khác thực hiện qua API Key (ví dụ: `api:product:sync`).
    *   Để tăng cường bảo mật, bạn nên có một cơ chế (ví dụ: một cột `type` trong bảng `Permission`) để phân loại quyền. Khi cấp phát JWT cho người dùng, chỉ nhúng các quyền loại `UI`. Khi xác thực API Key, chỉ xem xét các quyền loại `API`. Điều này ngăn chặn việc vô tình gán một quyền mạnh (dành cho API) cho một người dùng thông thường.

---

<a id="6.2-bảo-mật-trong-kiến-trúc-microservices"></a>
#### **6.2. Bảo mật trong Kiến trúc Microservices**

Trong một hệ thống gồm nhiều dịch vụ nhỏ, việc để mỗi dịch vụ tự quản lý user và xác thực là không khả thi. Thay vào đó, chúng ta áp dụng mô hình **Xác thực tập trung và Phân quyền phi tập trung**.

*   **Các thành phần chính:**
    *   **API Gateway:** Là điểm vào duy nhất cho tất cả các request từ bên ngoài. Nó hoạt động như một "người gác cổng" trung tâm. Mọi request phải đi qua Gateway trước khi đến được các dịch vụ con.
    *   **Authentication Service (Auth Service):** Là dịch vụ duy nhất trong hệ thống có trách nhiệm quản lý thông tin người dùng, xác thực credentials, và quan trọng nhất là **cấp phát và ký (sign) JWT**. Nó giữ **Private Key**.
    *   **Downstream Microservices (Các dịch vụ con):** Như Product Service, Order Service... Chúng chỉ cần **Public Key** tương ứng để có thể **xác minh (verify)** chữ ký của JWT.

*   **Luồng xác thực và phân quyền:**
    1.  **Xác thực tập trung (tại Gateway):**
        *   Client gửi request đến API Gateway kèm theo JWT.
        *   API Gateway, với Public Key, sẽ xác thực chữ ký và thời gian hết hạn của JWT.
        *   Nếu JWT không hợp lệ, Gateway sẽ **từ chối request ngay lập tức** với lỗi 401. Dịch vụ con không bao giờ nhận được request này.
        *   Nếu JWT hợp lệ, Gateway tin tưởng vào thông tin trong đó và chuyển tiếp request đến dịch vụ con tương ứng.
    2.  **Phân quyền phi tập trung (tại Microservice):**
        *   Product Service nhận được request (đã được Gateway xác thực).
        *   Nhiệm vụ của nó bây giờ là **phân quyền**. Nó đọc các `authorities` từ JWT.
        *   Nó tự quyết định xem với những quyền đó, người dùng có được phép thực hiện hành động trên tài nguyên "product" hay không (sử dụng `@PreAuthorize`).
        *   Tương tự, Order Service sẽ tự quyết định về quyền trên tài nguyên "order".

*   **Giao tiếp giữa các Service (Service-to-Service):**
    *   Khi một dịch vụ (ví dụ: Order Service) cần gọi đến một dịch vụ khác (ví dụ: Product Service), nó không nên dùng thông tin của người dùng.
    *   Thay vào đó, nó nên sử dụng **OAuth2 Client Credentials Flow**. Mỗi dịch vụ sẽ có một `Client ID` và `Client Secret`. Nó dùng credentials này để gọi đến Auth Service và nhận về một JWT dành riêng cho "danh tính" của chính nó, với các `scope` được định nghĩa trước (ví dụ: `internal:product:read`).
    *   Điều này đảm bảo mọi giao tiếp trong hệ thống đều được xác thực và phân quyền một cách an toàn.

---

<a id="6.3-tối-ưu-hiệu-suất-với-caching-redis"></a>
#### **6.3. Tối ưu Hiệu suất với Caching (Redis)**

Việc xác thực và tra cứu quyền có thể tạo ra gánh nặng cho CSDL, đặc biệt là Auth Service. Sử dụng một lớp cache tốc độ cao như Redis là điều cần thiết.

*   **Cache những gì ở Auth Service?**
    *   **Mục tiêu:** Giảm số lần đọc CSDL chính.
    *   **Dữ liệu:**
        *   **Thông tin User/Tenant và các quyền đã được tổng hợp:** Khi một người dùng được load lần đầu, lưu toàn bộ hồ sơ `UserDetails` của họ vào Redis với TTL (Time-To-Live) ngắn (ví dụ 15-30 phút). Các lần xác thực JWT tiếp theo (nếu cần tra cứu lại) sẽ đọc từ Redis.
        *   **Refresh Tokens:** Redis là nơi lưu trữ lý tưởng cho refresh token vì nó có cơ chế TTL tự nhiên.
    *   **Invalidation:** Khi quyền của một user bị thay đổi, cần phải xóa (invalidate) cache của user đó để lần truy cập tiếp theo sẽ đọc lại từ CSDL.

*   **Cache những gì ở API Gateway?**
    *   **Mục tiêu:** Giảm độ trễ cho mọi request và giảm tải cho Auth Service.
    *   **Dữ liệu:**
        *   **Kết quả xác thực API Key:** Khi một API Key được xác thực lần đầu, Gateway sẽ gọi Auth Service để lấy danh sách quyền. Gateway nên cache lại kết quả này (`API Key -> List<Permission>`) với TTL rất ngắn (ví dụ 1-5 phút). Các request tiếp theo với cùng API key sẽ được xử lý ngay tại Gateway mà không cần gọi mạng đến Auth Service.
        *   **JWT Blacklist (Danh sách đen JWT):** Khi người dùng đăng xuất hoặc đổi mật khẩu, JWT của họ vẫn còn hiệu lực cho đến khi hết hạn. Để vô hiệu hóa nó ngay lập tức, bạn có thể lưu ID của JWT đó vào một danh sách đen trong Redis. Với mỗi request, Gateway sẽ kiểm tra xem JWT có nằm trong danh sách đen này không trước khi xác thực.
        *   **Rate Limiting:** Gateway là nơi hoàn hảo để triển khai giới hạn tần suất request (ví dụ: 100 request/phút/API key) bằng cách sử dụng các lệnh nguyên tử của Redis như `INCR`.