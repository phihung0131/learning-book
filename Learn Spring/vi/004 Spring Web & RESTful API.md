## Lesson 1: Kiến trúc Spring MVC và Vòng đời của Request

### 1. Giải thích khái niệm chi tiết

#### Cấu trúc tầng Controller - Service - Repository

Trước khi đi vào cơ chế của Spring MVC, điều quan trọng là phải hiểu kiến trúc phân tầng kinh điển mà hầu hết các ứng dụng Spring tuân theo. Đây là một biến thể của kiến trúc 3 lớp:

1.  **Controller Layer (Tầng điều khiển):**
    *   **Nhiệm vụ:** Đây là lớp ngoài cùng, là "cổng vào" của ứng dụng. Nó chịu trách nhiệm **duy nhất** cho việc xử lý các request HTTP đến và trả về các response HTTP.
    *   **Công việc cụ thể:**
        *   Tiếp nhận request (ví dụ: `GET /users/1`).
        *   Phân tích request (lấy dữ liệu từ `@PathVariable`, `@RequestParam`, `@RequestBody`).
        *   **Xác thực (Validate)** dữ liệu đầu vào.
        *   **Ủy quyền (Delegate)** công việc xử lý logic nghiệp vụ cho tầng Service.
        *   Nhận kết quả từ Service, đóng gói nó thành một response HTTP (chọn đúng status code, header, và body) và gửi về cho client.
    *   **KHÔNG NÊN làm gì:** Controller **không** nên chứa business logic phức tạp hoặc trực tiếp tương tác với database.

2.  **Service Layer (Tầng dịch vụ):**
    *   **Nhiệm vụ:** Đây là "bộ não" của ứng dụng. Nó chứa đựng tất cả **business logic (logic nghiệp vụ)**.
    *   **Công việc cụ thể:**
        *   Thực hiện các quy trình nghiệp vụ (ví dụ: `placeOrder`, `registerUser`).
        *   Điều phối sự tương tác giữa nhiều Repository khác nhau.
        *   Quản lý transaction (`@Transactional`).
        *   Tương tác với các dịch vụ bên ngoài (gửi email, gọi API khác).
    *   Nó không biết gì về HTTP. Nó nhận đầu vào là các đối tượng Java thuần và trả về các đối tượng Java thuần.

3.  **Repository Layer (Tầng kho dữ liệu):**
    *   **Nhiệm vụ:** Chịu trách nhiệm cho việc truy cập và thao tác với dữ liệu trong database.
    *   **Công việc cụ thể:** Cung cấp các phương thức CRUD (Create, Read, Update, Delete). Tầng này được trừu tượng hóa bởi Spring Data JPA.
    *   Nó không biết gì về business logic hay HTTP.

#### Vòng đời của một Request trong Spring MVC

Đây là một trong những câu hỏi phoungr vấn phổ biến nhất: **"Khi một request HTTP được gửi đến một ứng dụng Spring Boot, chuyện gì sẽ xảy ra?"**. Câu trả lời nằm ở **DispatcherServlet** và các thành phần của nó.

Sơ đồ luồng:
`HTTP Request` -> `Servlet Container (Tomcat)` -> **`DispatcherServlet`** -> `HandlerMapping` -> `Controller` -> `HandlerAdapter` -> `ViewResolver (ít dùng trong REST)` -> `HTTP Response`

1.  **DispatcherServlet (Người điều phối):**
    *   Đây là "trái tim" của Spring MVC. Nó là một Servlet duy nhất (theo mẫu thiết kế **Front Controller**). Tất cả các request đến ứng dụng của bạn đều đi qua nó đầu tiên.
    *   Nhiệm vụ của nó không phải là xử lý request, mà là **điều phối** request đó đến đúng bộ xử lý (handler) phù hợp.

2.  **HandlerMapping (Người chỉ đường):**
    *   `DispatcherServlet` hỏi `HandlerMapping`: "Với request `GET /users/1`, ai sẽ xử lý nó?".
    *   `HandlerMapping` sẽ quét qua tất cả các `@Controller`/`@RestController` trong ứng dụng, tìm phương thức được đăng ký với mapping tương ứng (`@GetMapping("/users/{id}")`).
    *   Nó sẽ trả về một đối tượng `HandlerExecutionChain` chứa thông tin về Controller và phương thức sẽ xử lý request.

3.  **HandlerAdapter (Người phiên dịch):**
    *   `DispatcherServlet` đã biết controller nào cần gọi, nhưng nó không biết *cách gọi* phương thức đó một cách chính xác (ví dụ: làm sao để lấy `{id}` từ URL và truyền vào tham số `Long id`? Làm sao để chuyển đổi JSON trong body thành đối tượng `UserDto`?).
    *   `HandlerAdapter` sẽ làm việc này. Nó phân tích chữ ký (signature) của phương thức controller, thực hiện việc binding dữ liệu (data binding) và chuyển đổi kiểu, sau đó mới thực sự **thực thi** phương thức controller.

4.  **Controller (Người xử lý):**
    *   Phương thức controller của bạn được thực thi. Nó gọi đến tầng Service, nhận kết quả, và trả về một đối tượng (ví dụ: một `UserDto` hoặc một `ResponseEntity`).

5.  **HttpMessageConverter (Người đóng gói - quan trọng cho REST):**
    *   Sau khi controller trả về một đối tượng Java, `HandlerAdapter` sẽ sử dụng một tập hợp các `HttpMessageConverter` (ví dụ: `MappingJackson2HttpMessageConverter` cho JSON) để **chuyển đổi (serialize)** đối tượng Java đó thành một định dạng mà client có thể hiểu (ví dụ: chuỗi JSON).
    *   Nó cũng thiết lập `Content-Type` header phù hợp trong response (ví dụ: `application/json`).

6.  **DispatcherServlet và Response:**
    *   `DispatcherServlet` nhận lại response đã được định dạng và gửi nó trở lại cho client.

#### Các Annotation chính

*   `@Controller` vs `@RestController`:
    *   `@Controller`: Annotation gốc, đánh dấu một lớp là một controller. Mặc định, các phương thức trong `@Controller` sẽ trả về một tên `View` (dành cho các ứng dụng web truyền thống trả về HTML).
    *   `@RestController`: Là một annotation tổng hợp, bao gồm `@Controller` và `@ResponseBody`. Nó là một "lối tắt", báo cho Spring rằng tất cả các phương thức trong lớp này sẽ trả về dữ liệu được ghi trực tiếp vào response body (thường là JSON), chứ không phải là một `View`. **Luôn dùng `@RestController` khi xây dựng API.**
*   `@RequestMapping` và các biến thể:
    *   `@RequestMapping("/users")`: Có thể được dùng ở cả cấp độ class và phương thức.
    *   `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`: Các lối tắt cho các HTTP method tương ứng, giúp code rõ ràng hơn. `@GetMapping("/users")` tương đương `@RequestMapping(value = "/users", method = RequestMethod.GET)`.
*   Truyền dữ liệu vào Controller:
    *   `@PathVariable`: Lấy dữ liệu từ một phần của URL path. Ví dụ: `/users/{id}`.
    *   `@RequestParam`: Lấy dữ liệu từ query string của URL. Ví dụ: `/users?status=ACTIVE`.
    *   `@RequestBody`: Yêu cầu Spring đọc toàn bộ body của request (thường là JSON) và chuyển đổi nó thành một đối tượng Java. Dùng cho `POST`, `PUT`, `PATCH`.
    *   `@ResponseEntity`: Một đối tượng bao bọc response. Nó cho phép bạn kiểm soát hoàn toàn mọi thứ: HTTP status code, headers, và cả response body. **Đây là cách trả về response tốt nhất và linh hoạt nhất.**

### 2. Ví dụ minh họa

**Tình huống:** Xây dựng một endpoint đơn giản để lấy thông tin một User.

```java
// com/example/api/controller/UserController.java

@RestController // Quan trọng: dùng @RestController cho API
@RequestMapping("/api/v1/users") // Base path cho tất cả các endpoint trong controller này
public class UserController {

    // Giả lập tầng service để tập trung vào controller
    // Trong thực tế, bạn sẽ @Autowired một UserService thật
    private final Map<Long, UserDto> users = Map.of(
        1L, new UserDto(1L, "john.doe", "john.doe@example.com"),
        2L, new UserDto(2L, "jane.doe", "jane.doe@example.com")
    );

    // Endpoint: GET /api/v1/users/{userId}
    @GetMapping("/{userId}")
    public ResponseEntity<UserDto> getUserById(@PathVariable("userId") Long id) {
        UserDto user = users.get(id);
        
        if (user != null) {
            // Trả về user và status 200 OK
            return ResponseEntity.ok(user); 
        } else {
            // Trả về không có body và status 404 Not Found
            return ResponseEntity.notFound().build();
        }
    }

    // Endpoint: POST /api/v1/users
    @PostMapping
    public ResponseEntity<UserDto> createUser(@RequestBody CreateUserRequest request) {
        // Giả lập tạo user mới và gán ID
        UserDto newUser = new UserDto(3L, request.getUsername(), request.getEmail());
        
        System.out.println("Đã nhận và tạo user: " + newUser);
        
        // Trả về user vừa tạo và status 201 Created
        // URI của resource mới được đặt trong header 'Location'
        URI location = URI.create("/api/v1/users/" + newUser.getId());
        return ResponseEntity.created(location).body(newUser);
    }
}

// Các lớp DTO (Data Transfer Object)
class UserDto { /* id, username, email - getters/setters */ }
class CreateUserRequest { /* username, email - getters/setters */ }
```
*   **Khi client gọi `GET /api/v1/users/1`:**
    1.  `DispatcherServlet` nhận request.
    2.  `HandlerMapping` tìm thấy phương thức `getUserById`.
    3.  `HandlerAdapter` thấy `@PathVariable("userId") Long id`, nó sẽ trích xuất `1` từ URL, chuyển thành `Long`, và truyền vào phương thức.
    4.  Phương thức chạy, trả về `ResponseEntity` chứa `UserDto` và status `200`.
    5.  `MappingJackson2HttpMessageConverter` chuyển đổi `UserDto` thành JSON.
    6.  Response được gửi về client.

### 3. Mini Exercise

1.  Thêm một endpoint mới vào `UserController` sử dụng `@GetMapping` (không có path con, tức là `/api/v1/users`).
2.  Endpoint này sẽ nhận một `@RequestParam` tùy chọn tên là `status`.
3.  Phương thức sẽ trả về một danh sách tất cả các user nếu không có `status`, hoặc một danh sách user được lọc theo `status` nếu có. (Bạn có thể giả lập dữ liệu).
4.  Sử dụng `ResponseEntity.ok()` để trả về danh sách.

### 4. Quiz Question

**Câu hỏi:** Sự khác biệt cơ bản nhất giữa `@RestController` và `@Controller` là gì?

a) `@RestController` chỉ dùng cho các HTTP method GET, trong khi `@Controller` dùng cho tất cả.
b) `@RestController` là một annotation tổng hợp bao gồm `@Controller` và `@ResponseBody`, nó làm cho tất cả các phương thức mặc định trả về dữ liệu (như JSON/XML) thay vì một tên View.
c) `@Controller` không thể xử lý `@RequestBody`, trong khi `@RestController` có thể.
d) `@RestController` chạy nhanh hơn `@Controller` vì nó bỏ qua `ViewResolver`.

---
**Đáp án:** **b) `@RestController` là một annotation tổng hợp bao gồm `@Controller` và `@ResponseBody`, nó làm cho tất cả các phương thức mặc định trả về dữ liệu (như JSON/XML) thay vì một tên View.**

**Giải thích:** Đây là sự khác biệt cốt lõi. `@RestController` là một sự tiện lợi được tạo ra đặc biệt cho việc xây dựng RESTful API. Nếu bạn dùng `@Controller`, bạn sẽ phải thêm `@ResponseBody` vào *từng* phương thức để đạt được hành vi tương tự.

## Lesson 2: Xây dựng RESTful APIs chuẩn

### 1. Giải thích khái niệm chi tiết

Chỉ việc tạo ra các endpoint trả về JSON thôi thì chưa đủ để gọi là một API "RESTful". REST (Representational State Transfer) là một **kiểu kiến trúc (architectural style)**, không phải là một công nghệ hay một tiêu chuẩn cứng nhắc. Nó đưa ra một tập hợp các ràng buộc (constraints) để thiết kế các ứng dụng web phân tán sao cho chúng có khả năng mở rộng, đơn giản và đáng tin cậy.

#### Các nguyên tắc (ràng buộc) của REST

Một API được coi là "RESTful" nếu nó tuân thủ các nguyên tắc sau:

1.  **Client-Server:** Tách biệt rõ ràng giữa client (giao diện người dùng) và server (lưu trữ và xử lý dữ liệu). Chúng giao tiếp với nhau qua một giao thức chung (HTTP). Sự tách biệt này cho phép chúng phát triển độc lập.

2.  **Stateless (Phi trạng thái):**
    *   Đây là nguyên tắc quan trọng nhất. Mỗi request từ client đến server **phải chứa tất cả thông tin cần thiết** để server hiểu và xử lý request đó.
    *   Server **không được lưu trữ bất kỳ trạng thái nào của client** giữa các request. Ví dụ, server không được lưu "người dùng này đang ở trang thứ 2 của danh sách sản phẩm". Thông tin đó (trang 2) phải được client gửi lên trong request tiếp theo.
    *   **Lợi ích (Phỏng vấn!):**
        *   **Khả năng mở rộng (Scalability):** Vì không có trạng thái, bất kỳ server nào trong một cụm (cluster) cũng có thể xử lý request của client, giúp việc load balancing trở nên dễ dàng.
        *   **Độ tin cậy (Reliability):** Nếu một server chết, request có thể được gửi lại đến một server khác mà không làm mất dữ liệu phiên.
    *   **Vậy認証 (authentication) thì sao?** Thông tin xác thực (ví dụ JWT token) được client gửi lên trong *mỗi* request (thường là qua header `Authorization`), server xác thực token đó và xử lý, chứ không lưu lại session "đã đăng nhập".

3.  **Cacheable (Có thể cache):** Response từ server nên chỉ định rõ nó có thể được cache hay không. Điều này giúp cải thiện hiệu năng và giảm tải cho server. Server làm điều này thông qua các HTTP header như `Cache-Control`, `Expires`.

4.  **Uniform Interface (Giao diện đồng nhất):** Đây là nguyên tắc cốt lõi giúp đơn giản hóa và tách rời kiến trúc. Nó bao gồm 4 khía cạnh:
    *   **Resource-Based:** Mọi thứ trong hệ thống đều là một "tài nguyên" (resource), và mỗi tài nguyên được định danh bằng một URI (Uniform Resource Identifier) duy nhất. Ví dụ: `/users/123` là URI cho tài nguyên người dùng có ID 123.
    *   **Manipulation of Resources Through Representations:** Client tương tác với tài nguyên thông qua "biểu diễn" (representation) của nó, thường là JSON hoặc XML. Client không bao giờ tương tác trực tiếp với tài nguyên trên server.
    *   **Self-descriptive Messages:** Mỗi message (request/response) phải tự mô tả đủ để hiểu nó. Ví dụ, response `Content-Type: application/json` cho biết body là JSON.
    *   **HATEOAS (Hypermedia as the Engine of Application State):** Client chỉ cần biết URI khởi đầu. Response từ server nên chứa các liên kết (links) để client có thể khám phá các hành động và tài nguyên tiếp theo. Ví dụ, response cho `/users/123` có thể chứa link đến `/users/123/orders`. (Nguyên tắc này ít được áp dụng chặt chẽ trong thực tế).

#### Thiết kế API đúng chuẩn

Dựa trên các nguyên tắc trên, chúng ta có các quy ước thiết kế sau:

*   **Sử dụng danh từ (nouns), không dùng động từ (verbs) trong URI:**
    *   **Tốt:** `GET /users`, `GET /users/123`, `POST /users`
    *   **Xấu:** `GET /getAllUsers`, `GET /getUserById?id=123`, `POST /createNewUser`
    *   Hành động được thể hiện qua **HTTP Method**, còn URI chỉ để định danh **tài nguyên**.

*   **Sử dụng đúng HTTP Method cho đúng hành động:**
    *   **`GET`**: Lấy dữ liệu (chỉ đọc). Phải an toàn (safe) và **idempotent**.
    *   **`POST`**: Tạo một tài nguyên mới. Không idempotent.
    *   **`PUT`**: Cập nhật **toàn bộ** một tài nguyên đã có (thay thế). Phải **idempotent**.
    *   **`PATCH`**: Cập nhật **một phần** của một tài nguyên đã có. Không nhất thiết phải idempotent.
    *   **`DELETE`**: Xóa một tài nguyên. Phải **idempotent**.

*   **Idempotency (Tính bất biến lặp lại - Phỏng vấn!):**
    *   Một thao tác được gọi là idempotent nếu việc gọi nó **nhiều lần** với cùng một tham số cho kết quả giống hệt như gọi nó **một lần**.
    *   `GET`, `PUT`, `DELETE` là idempotent. Ví dụ: `DELETE /users/123` lần đầu xóa user, các lần sau vẫn trả về "đã xóa" (kết quả cuối cùng của hệ thống không đổi).
    *   `POST` không idempotent. Gọi `POST /users` hai lần sẽ tạo ra hai user khác nhau.

*   **Sử dụng đúng HTTP Status Code:**
    *   **2xx (Success):**
        *   `200 OK`: Thành công cho `GET`, `PUT`, `PATCH`.
        *   `201 Created`: Tạo tài nguyên thành công (`POST`). Response nên chứa header `Location` trỏ đến URI của tài nguyên mới.
        *   `204 No Content`: Thành công nhưng không có body trả về (thường dùng cho `DELETE`).
    *   **4xx (Client Error):**
        *   `400 Bad Request`: Request không hợp lệ (sai cú pháp, thiếu dữ liệu, validation thất bại).
        *   `401 Unauthorized`: Chưa xác thực (chưa đăng nhập).
        *   `403 Forbidden`: Đã xác thực nhưng không có quyền truy cập.
        *   `404 Not Found`: Không tìm thấy tài nguyên.
        *   `409 Conflict`: Xung đột trạng thái, ví dụ tạo một user với username đã tồn tại.
    *   **5xx (Server Error):**
        *   `500 Internal Server Error`: Lỗi chung phía server (exception không được xử lý).

#### Versioning (Quản lý phiên bản API)

Khi API của bạn đã được sử dụng, bạn không thể tùy tiện thay đổi nó (breaking change). Versioning cho phép bạn ra mắt phiên bản mới trong khi vẫn duy trì phiên bản cũ.

*   **URI Versioning (Phổ biến nhất):** ` /api/v1/users`, `/api/v2/users`
*   **Request Param Versioning:** `/api/users?version=1`
*   **Header Versioning:** `Accept: application/vnd.company.app-v1+json`

### 2. Ví dụ minh họa

Xây dựng một API CRUD đầy đủ cho tài nguyên `Product`.

```java
@RestController
@RequestMapping("/api/v1/products")
public class ProductController {

    @Autowired
    private ProductService productService; // Giả sử đã có ProductService

    // 1. GET /api/v1/products - Lấy danh sách sản phẩm
    @GetMapping
    public ResponseEntity<List<ProductDto>> getAllProducts() {
        List<ProductDto> products = productService.findAll();
        return ResponseEntity.ok(products); // 200 OK
    }

    // 2. GET /api/v1/products/{id} - Lấy một sản phẩm
    @GetMapping("/{id}")
    public ResponseEntity<ProductDto> getProductById(@PathVariable Long id) {
        ProductDto product = productService.findById(id);
        // Giả sử service sẽ ném ResourceNotFoundException nếu không tìm thấy
        return ResponseEntity.ok(product); // 200 OK
    }

    // 3. POST /api/v1/products - Tạo sản phẩm mới
    @PostMapping
    public ResponseEntity<ProductDto> createProduct(@RequestBody CreateProductRequest request) {
        ProductDto newProduct = productService.create(request);
        
        URI location = ServletUriComponentsBuilder
                .fromCurrentRequest() // Lấy URI hiện tại (/api/v1/products)
                .path("/{id}") // Thêm path variable
                .buildAndExpand(newProduct.getId()) // Gán giá trị cho path variable
                .toUri();
        
        return ResponseEntity.created(location).body(newProduct); // 201 Created
    }

    // 4. PUT /api/v1/products/{id} - Cập nhật toàn bộ sản phẩm
    @PutMapping("/{id}")
    public ResponseEntity<ProductDto> updateProduct(@PathVariable Long id, @RequestBody UpdateProductRequest request) {
        ProductDto updatedProduct = productService.update(id, request);
        return ResponseEntity.ok(updatedProduct); // 200 OK
    }
    
    // 5. DELETE /api/v1/products/{id} - Xóa sản phẩm
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deleteProduct(@PathVariable Long id) {
        productService.delete(id);
        return ResponseEntity.noContent().build(); // 204 No Content
    }
}
```

### 3. Mini Exercise

1.  Thêm một endpoint mới vào `ProductController`: `PATCH /api/v1/products/{id}/stock`.
2.  Endpoint này sẽ chỉ nhận một `@RequestBody` đơn giản chứa số lượng tồn kho mới, ví dụ: `{"quantity": 50}`.
3.  Gọi đến một phương thức trong `ProductService` để chỉ cập nhật số lượng tồn kho của sản phẩm.
4.  Endpoint nên trả về sản phẩm đã được cập nhật với status `200 OK`.
5.  **Câu hỏi tư duy:** Hành động này có idempotent không? (Trả lời: Không, vì gọi nhiều lần sẽ ghi đè giá trị tồn kho, nhưng kết quả cuối cùng không phụ thuộc vào số lần gọi, nó phụ thuộc vào giá trị cuối cùng được gửi lên. Tuy nhiên, nếu request là "tăng số lượng tồn kho lên 10", thì nó sẽ không idempotent).

### 4. Quiz Question

**Câu hỏi:** Một client gửi một request `POST /orders` để tạo một đơn hàng mới. Server xử lý thành công. Theo chuẩn RESTful, server nên trả về HTTP status code nào và header nào là quan trọng nhất?

a) `200 OK`
b) `201 Created` và header `Location` chứa URI của đơn hàng vừa tạo (ví dụ: `/orders/567`).
c) `204 No Content`
d) `400 Bad Request` vì client không nên tự tạo đơn hàng.

---
**Đáp án:** **b) `201 Created` và header `Location` chứa URI của đơn hàng vừa tạo (ví dụ: `/orders/567`).**

**Giải thích:** `201 Created` là status code tiêu chuẩn để thông báo việc tạo tài nguyên thành công. Cung cấp header `Location` giúp client biết được URI của tài nguyên mới để có thể tương tác với nó trong các request sau này (ví dụ `GET /orders/567`). Đây là một phần quan trọng của việc thiết kế API theo chuẩn REST.

## Lesson 3: Validation và Data Binding

### 1. Giải thích khái niệm chi tiết

#### Vấn đề: "Garbage In, Garbage Out" (Rác vào, Rác ra)

Một trong những nguyên tắc cơ bản của việc xây dựng phần mềm an toàn và đáng tin cậy là **không bao giờ tin tưởng dữ liệu đầu vào từ client**. Client (trình duyệt, mobile app, hệ thống khác) có thể gửi dữ liệu thiếu, sai định dạng, hoặc thậm chí là dữ liệu độc hại. Nếu ứng dụng của bạn xử lý trực tiếp dữ liệu này mà không kiểm tra, nó có thể gây ra:
*   `NullPointerException` ở tầng service.
*   Lỗi `ConstraintViolationException` từ database.
*   Các lỗ hổng bảo mật (ví dụ: SQL Injection, XSS).
*   Trải nghiệm người dùng kém (thông báo lỗi chung chung).

**Validation (Xác thực)** là quá trình kiểm tra để đảm bảo rằng dữ liệu đầu vào tuân thủ các quy tắc nghiệp vụ (business rules) trước khi xử lý nó.

#### Bean Validation (JSR-303 / JSR-380)

Thay vì viết các khối `if/else` lồng nhau để kiểm tra từng trường trong controller, Java cung cấp một đặc tả (specification) gọi là **Bean Validation**. Spring Boot tích hợp sẵn với **Hibernate Validator**, là implementation tham chiếu của đặc tả này.

*   **Cách hoạt động:** Bạn khai báo các "ràng buộc" (constraints) trực tiếp trên các trường (fields) của các lớp DTO (Data Transfer Object) hoặc Command Object bằng cách sử dụng các annotation.
*   **Kích hoạt:** Khi một phương thức trong controller có tham số được đánh dấu `@RequestBody` và `@Valid`, Spring sẽ tự động kích hoạt Hibernate Validator để kiểm tra đối tượng đó sau khi đã được deserialization từ JSON.

#### Các Annotation Validation phổ biến

*   **Null Checks:**
    *   `@NotNull`: Trường không được là `null`.
    *   `@NotEmpty`: Dành cho `String` hoặc `Collection`. Không được `null` và không được rỗng (ví dụ: `""` hoặc `new ArrayList<>()` đều không hợp lệ).
    *   `@NotBlank`: Chỉ dành cho `String`. Không được `null` và phải chứa ít nhất một ký tự không phải khoảng trắng. Đây là lựa chọn tốt nhất để validate các trường `String` bắt buộc.
*   **Size/Length Checks:**
    *   `@Size(min = 5, max = 50)`: Dành cho `String`, `Collection`, `Map`, `Array`. Kiểm tra kích thước/độ dài.
    *   `@Min(18)`: Dành cho số. Giá trị phải lớn hơn hoặc bằng 18.
    *   `@Max(100)`: Giá trị phải nhỏ hơn hoặc bằng 100.
*   **Format Checks:**
    *   `@Email`: Kiểm tra định dạng email.
    *   `@Pattern(regexp = "^[0-9]{10}$")`: Kiểm tra một chuỗi có khớp với một biểu thức chính quy (Regular Expression) hay không. Ví dụ trên dùng để validate số điện thoại có 10 chữ số.
*   **Date Checks:**
    *   `@Future`: Ngày phải ở tương lai.
    *   `@Past`: Ngày phải ở quá khứ.
*   **Composition:**
    *   `@Valid`: Dùng để kích hoạt validation lồng nhau. Nếu một DTO chứa một DTO khác, bạn cần đánh dấu trường đó với `@Valid` để Spring kiểm tra cả đối tượng bên trong.

#### Xử lý lỗi Validation

*   **Chuyện gì xảy ra khi validation thất bại?**
    *   Nếu một ràng buộc bị vi phạm, Hibernate Validator sẽ thu thập tất cả các lỗi.
    *   Spring MVC, khi thấy validation thất bại, sẽ **không** gọi phương thức controller của bạn. Thay vào đó, nó sẽ ném ra một exception là `MethodArgumentNotValidException`.
*   **Làm sao để bắt lỗi này?**
    *   Như đã học ở bài trước, chúng ta có thể sử dụng một `@ControllerAdvice` để tạo một `@ExceptionHandler` chuyên xử lý `MethodArgumentNotValidException`.
    *   Bên trong handler này, chúng ta có thể trích xuất danh sách các lỗi chi tiết từ exception, định dạng chúng thành một cấu trúc JSON thân thiện và trả về cho client với status code `400 Bad Request`.
*   **`BindingResult`:**
    *   Là một cách khác để xử lý lỗi validation, nhưng **ít được khuyến khích trong REST API**.
    *   Nếu bạn khai báo một tham số `BindingResult` **ngay sau** tham số được validate, Spring sẽ không ném `MethodArgumentNotValidException`. Thay vào đó, nó sẽ đặt các lỗi vào đối tượng `BindingResult` và vẫn gọi phương thức controller. Bạn sẽ phải tự kiểm tra `bindingResult.hasErrors()` bằng tay.
    *   Cách này phù hợp hơn với các ứng dụng web MVC truyền thống, nơi bạn cần hiển thị lại form với các thông báo lỗi. Với REST API, việc để exception được ném ra và xử lý tập trung ở `@ControllerAdvice` là sạch sẽ và nhất quán hơn.

#### Custom Validation Annotation

Đôi khi, các annotation có sẵn không đủ. Ví dụ, bạn muốn validate một trường `username` không được chứa ký tự đặc biệt và phải là duy nhất trong database. Bạn có thể tạo annotation của riêng mình.

*   **Các bước:**
    1.  Tạo một interface annotation (ví dụ: `@UniqueUsername`).
    2.  Đánh dấu nó với `@Constraint(validatedBy = UniqueUsernameValidator.class)`.
    3.  Tạo lớp `UniqueUsernameValidator` implement `ConstraintValidator<UniqueUsername, String>`.
    4.  Trong phương thức `isValid()` của validator, bạn có thể tiêm `UserRepository` vào và thực hiện logic kiểm tra.

### 2. Ví dụ minh họa

**Tình huống:** Validate request tạo một `User` mới. Username không được trống, email phải đúng định dạng, và password phải dài ít nhất 8 ký tự.

**1. Thêm dependency (thường đã có sẵn với `starter-web`):**
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>```

**2. Thêm annotation vào DTO:**
```java
// CreateUserRequest.java
public class CreateUserRequest {

    @NotBlank(message = "Username cannot be blank")
    @Size(min = 3, max = 50, message = "Username must be between 3 and 50 characters")
    private String username;

    @NotBlank(message = "Email cannot be blank")
    @Email(message = "Email should be valid")
    private String email;

    @NotBlank(message = "Password cannot be blank")
    @Size(min = 8, message = "Password must be at least 8 characters long")
    private String password;

    // Getters and Setters...
}
```
*   **Best Practice:** Luôn cung cấp thuộc tính `message` để trả về thông báo lỗi thân thiện thay vì thông báo mặc định của Hibernate Validator.

**3. Kích hoạt validation trong Controller:**
```java
@RestController
@RequestMapping("/api/v1/users")
public class UserController {

    // ...

    @PostMapping
    public ResponseEntity<UserDto> createUser(@Valid @RequestBody CreateUserRequest request) {
        // Nếu validation thành công, code ở đây mới được chạy
        UserDto newUser = userService.createUser(request);
        // ... trả về 201 Created
        return ...;
    }
}
```

**4. Tạo Global Exception Handler để xử lý lỗi:**
```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    // ... các handler khác ...

    @ExceptionHandler(MethodArgumentNotValidException.class)
    @ResponseStatus(HttpStatus.BAD_REQUEST)
    public Map<String, Object> handleValidationExceptions(MethodArgumentNotValidException ex) {
        Map<String, String> errors = new HashMap<>();
        
        // Lấy tất cả các lỗi và gom chúng vào một Map
        ex.getBindingResult().getAllErrors().forEach((error) -> {
            String fieldName = ((FieldError) error).getField();
            String errorMessage = error.getDefaultMessage();
            errors.put(fieldName, errorMessage);
        });

        Map<String, Object> response = new LinkedHashMap<>();
        response.put("timestamp", LocalDateTime.now());
        response.put("status", HttpStatus.BAD_REQUEST.value());
        response.put("errors", errors);

        return response;
    }
}
```

**Kết quả:**
Nếu client gửi một request `POST /api/v1/users` với body JSON sau:
```json
{
  "username": "u1",
  "email": "invalid-email",
  "password": "123"
}
```
Server sẽ trả về response với status `400 Bad Request` và body:
```json
{
    "timestamp": "2025-10-17T10:30:00.12345",
    "status": 400,
    "errors": {
        "username": "Username must be between 3 and 50 characters",
        "email": "Email should be valid",
        "password": "Password must be at least 8 characters long"
    }
}
```

### 3. Mini Exercise

1.  Giả sử bạn có một DTO `UpdateUserRequest` chứa `fullName` và `phoneNumber`.
2.  Áp dụng các ràng buộc sau:
    *   `fullName`: Không được trống.
    *   `phoneNumber`: Phải khớp với một biểu thức chính quy (regex) cho số điện thoại Việt Nam (ví dụ, bắt đầu bằng 0, theo sau là 9 chữ số). (Gợi ý: `@Pattern`).
3.  Tạo một endpoint `PUT /api/v1/users/{id}` và áp dụng validation cho request này.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** Sự khác biệt giữa `@Valid` và `@Validated` là gì?

a) Không có sự khác biệt, chúng có thể thay thế cho nhau.
b) `@Valid` là chuẩn của JSR-303, trong khi `@Validated` là một annotation của riêng Spring. `@Validated` hỗ trợ tính năng "validation groups".
c) `@Valid` chỉ có thể dùng trên tham số phương thức, còn `@Validated` có thể dùng trên cả class.
d) `@Valid` dùng để validate DTO, còn `@Validated` dùng để validate Entity.

---
**Đáp án:** **b) `@Valid` là chuẩn của JSR-303, trong khi `@Validated` là một annotation của riêng Spring. `@Validated` hỗ trợ tính năng "validation groups".**

**Giải thích:**
*   `@Valid` là annotation chuẩn, nó kích hoạt validation mặc định.
*   `@Validated` là một "phiên bản nâng cấp" của Spring. Nó cung cấp một tính năng rất mạnh mẽ là **Validation Groups**. Groups cho phép bạn định nghĩa các nhóm ràng buộc khác nhau và chỉ kích hoạt một nhóm cụ thể trong một tình huống nhất định. Ví dụ, khi tạo một `User` (`POST`), bạn cần validate cả `username` và `password`. Nhưng khi cập nhật (`PUT`), bạn có thể chỉ muốn validate `username` mà không cần client gửi `password`. Validation Groups giúp bạn thực hiện điều này một cách thanh lịch.

## Lesson 4: Exception Handling và Global Error Management

### 1. Giải thích khái niệm chi tiết

#### Vấn đề: Tại sao cần xử lý Exception một cách tập trung?

Trong một ứng dụng phức tạp, exception có thể xảy ra ở bất cứ đâu:
*   **Controller:** Dữ liệu đầu vào không hợp lệ (`MethodArgumentNotValidException`).
*   **Service:** Logic nghiệp vụ thất bại (ví dụ: `InsufficientFundsException`).
*   **Repository:** Không tìm thấy tài nguyên (`ResourceNotFoundException`), hoặc lỗi kết nối database.

Nếu không có một chiến lược xử lý tập trung, bạn sẽ phải đặt các khối `try-catch` ở khắp mọi nơi trong controller, dẫn đến:
*   **Code lặp lại (Boilerplate Code):** Cùng một logic xử lý lỗi xuất hiện ở nhiều nơi.
*   **Thiếu nhất quán:** Mỗi endpoint có thể trả về một định dạng lỗi khác nhau, gây khó khăn cho client.
*   **Rò rỉ chi tiết nội bộ:** Stack trace đầy đủ có thể bị lộ ra cho client, đây là một rủi ro bảo mật.
*   **Khó bảo trì:** Khi muốn thay đổi định dạng lỗi, bạn phải sửa ở hàng chục chỗ.

#### `@ControllerAdvice` và `@RestControllerAdvice`

Spring cung cấp một giải pháp cực kỳ mạnh mẽ và thanh lịch cho vấn đề này: **Advice**.

*   `@ControllerAdvice`: Là một annotation đánh dấu một class là một "cố vấn" cho các controller. Nó cho phép bạn định nghĩa các logic có thể được áp dụng **toàn cục** cho tất cả (hoặc một nhóm) các controller.
*   `@RestControllerAdvice`: Là một annotation tiện lợi kết hợp `@ControllerAdvice` và `@ResponseBody`. Nó đảm bảo rằng kết quả trả về từ các phương thức trong lớp này sẽ được ghi trực tiếp vào response body (dưới dạng JSON), phù hợp cho REST API. **Luôn dùng `@RestControllerAdvice` cho API.**

Lớp được đánh dấu `@RestControllerAdvice` sẽ "lắng nghe" các exception được ném ra từ tầng controller.

#### `@ExceptionHandler`

Bên trong một lớp `@RestControllerAdvice`, bạn sử dụng `@ExceptionHandler` để định nghĩa các phương thức sẽ xử lý các loại exception cụ thể.

*   **Cách hoạt động:**
    1.  Một exception (ví dụ `ResourceNotFoundException`) được ném ra từ một controller và không được xử lý cục bộ.
    2.  Spring sẽ bắt lấy exception này.
    3.  Nó sẽ quét qua tất cả các bean `@RestControllerAdvice` đã đăng ký.
    4.  Nó tìm một phương thức được đánh dấu `@ExceptionHandler` có tham số khớp với loại exception đó (hoặc lớp cha của exception đó).
    5.  Nếu tìm thấy, nó sẽ gọi phương thức handler đó và truyền exception vào làm tham số.
    6.  Kết quả trả về từ phương thức handler sẽ được chuyển đổi thành response HTTP và gửi về cho client.

*   **Best Practice:**
    *   Tạo các phương thức handler riêng cho từng loại exception nghiệp vụ cụ thể (`ResourceNotFoundException`, `DuplicateUsernameException`...) để trả về status code và thông báo lỗi chính xác nhất.
    *   Tạo một phương thức handler "bắt tất cả" cho `Exception.class`. Đây là "lưới an toàn" cuối cùng để xử lý mọi lỗi không lường trước và trả về một response `500 Internal Server Error` chung chung, tránh để lộ stack trace.

#### Chuẩn hóa định dạng lỗi

Một API tốt phải có một định dạng lỗi nhất quán trên tất cả các endpoint. Điều này giúp đội ngũ frontend hoặc các client khác dễ dàng phân tích và hiển thị lỗi một cách đồng bộ.

Một định dạng lỗi tốt thường bao gồm:
*   `timestamp`: Thời gian xảy ra lỗi.
*   `status`: Mã status HTTP.
*   `error`: Một mô tả ngắn gọn về loại lỗi (ví dụ: "Not Found", "Bad Request").
*   `message`: Một thông báo lỗi thân thiện với người dùng/lập trình viên.
*   `path`: URI của request gây ra lỗi.
*   `details` (tùy chọn): Một danh sách hoặc đối tượng chứa các lỗi chi tiết hơn (ví dụ: danh sách các lỗi validation).

#### Custom Exceptions

Thay vì ném các exception chung chung như `RuntimeException`, **best practice** là tạo ra các lớp exception tùy chỉnh (custom exception) có ý nghĩa nghiệp vụ rõ ràng.

*   **Ví dụ:**
    *   `ResourceNotFoundException extends RuntimeException`
    *   `InvalidOperationException extends RuntimeException`
    *   `EmailAlreadyExistsException extends RuntimeException`
*   **Lợi ích:**
    *   **Code dễ đọc hơn:** Nhìn vào `throw new ResourceNotFoundException(...)` rõ ràng hơn nhiều so với `throw new RuntimeException("...")`.
    *   **Xử lý lỗi chính xác hơn:** Cho phép bạn tạo các `@ExceptionHandler` riêng biệt cho từng trường hợp nghiệp vụ, trả về các status code và thông báo lỗi phù hợp.

### 2. Ví dụ minh họa

**Tình huống:** Xây dựng một Global Exception Handler hoàn chỉnh cho API quản lý người dùng.

**1. Tạo các Custom Exception:**
```java
// Dùng @ResponseStatus để gán một HTTP status mặc định cho exception này.
// Nếu @ExceptionHandler không bắt, status này sẽ được dùng.
@ResponseStatus(HttpStatus.NOT_FOUND)
public class ResourceNotFoundException extends RuntimeException {
    public ResourceNotFoundException(String message) {
        super(message);
    }
}

@ResponseStatus(HttpStatus.CONFLICT)
public class DuplicateResourceException extends RuntimeException {
    public DuplicateResourceException(String message) {
        super(message);
    }
}
```

**2. Tạo lớp DTO cho Error Response:**
```java
// ErrorDetail.java
public class ErrorDetail {
    private LocalDateTime timestamp;
    private int status;
    private String error;
    private String message;
    private String path;

    // Constructors, Getters, Setters...
}
```

**3. Tạo Global Exception Handler:**
```java
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;
import org.springframework.web.context.request.WebRequest;

import java.time.LocalDateTime;

@RestControllerAdvice
public class GlobalExceptionHandler {

    // Xử lý các exception nghiệp vụ cụ thể
    @ExceptionHandler(ResourceNotFoundException.class)
    public ResponseEntity<ErrorDetail> handleResourceNotFoundException(ResourceNotFoundException ex, WebRequest request) {
        ErrorDetail errorDetail = new ErrorDetail(
                LocalDateTime.now(),
                HttpStatus.NOT_FOUND.value(),
                "Resource Not Found",
                ex.getMessage(),
                request.getDescription(false).replace("uri=", "")
        );
        return new ResponseEntity<>(errorDetail, HttpStatus.NOT_FOUND);
    }

    @ExceptionHandler(DuplicateResourceException.class)
    public ResponseEntity<ErrorDetail> handleDuplicateResourceException(DuplicateResourceException ex, WebRequest request) {
        ErrorDetail errorDetail = new ErrorDetail(
                LocalDateTime.now(),
                HttpStatus.CONFLICT.value(),
                "Duplicate Resource",
                ex.getMessage(),
                request.getDescription(false).replace("uri=", "")
        );
        return new ResponseEntity<>(errorDetail, HttpStatus.CONFLICT);
    }

    // "Lưới an toàn" - xử lý tất cả các exception khác
    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorDetail> handleGlobalException(Exception ex, WebRequest request) {
        ErrorDetail errorDetail = new ErrorDetail(
                LocalDateTime.now(),
                HttpStatus.INTERNAL_SERVER_ERROR.value(),
                "Internal Server Error",
                "An unexpected error occurred: " + ex.getMessage(), // Che giấu chi tiết lỗi
                request.getDescription(false).replace("uri=", "")
        );
        // Quan trọng: Log stack trace đầy đủ ở đây để đội ngũ dev có thể debug
        // log.error("Unhandled exception occurred", ex); 
        return new ResponseEntity<>(errorDetail, HttpStatus.INTERNAL_SERVER_ERROR);
    }
}
```
*   `WebRequest`: Một đối tượng được Spring cung cấp, cho phép truy cập thông tin của request hiện tại, ví dụ như URI.

**4. Sử dụng trong Service:**
```java
@Service
public class UserServiceImpl implements UserService {

    @Autowired
    private UserRepository userRepository;

    @Override
    public UserDto findUserById(Long id) {
        User user = userRepository.findById(id)
                .orElseThrow(() -> new ResourceNotFoundException("User not found with id: " + id));
        // ... convert to DTO and return
    }

    @Override
    public UserDto createUser(CreateUserRequest request) {
        if (userRepository.existsByUsername(request.getUsername())) {
            throw new DuplicateResourceException("Username already exists: " + request.getUsername());
        }
        // ... create user logic
    }
}
```
*   **Kết quả:**
    *   Khi gọi `GET /users/999` (không tồn tại), client sẽ nhận về status `404` và một body JSON được định dạng bởi `handleResourceNotFoundException`.
    *   Khi tạo user với username đã có, client nhận về status `409` và body từ `handleDuplicateResourceException`.
    *   Nếu có một `NullPointerException` bất ngờ xảy ra, client sẽ nhận về status `500` và body từ `handleGlobalException`.

### 3. Mini Exercise

1.  Tạo một custom exception mới tên là `InvalidInputException extends RuntimeException`.
2.  Thêm một `@ExceptionHandler` cho exception này trong `GlobalExceptionHandler`.
3.  Handler này nên trả về HTTP status `422 Unprocessable Entity` (một lựa chọn khác cho lỗi nghiệp vụ, thay vì `400 Bad Request`).
4.  Trong một phương thức service bất kỳ, hãy thử ném ra exception này và kiểm tra xem response trả về có đúng như mong đợi không.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** Sự khác biệt chính giữa `@ControllerAdvice` và việc kế thừa từ `ResponseEntityExceptionHandler` là gì?

a) `@ControllerAdvice` chỉ xử lý custom exception, trong khi `ResponseEntityExceptionHandler` xử lý các exception nội bộ của Spring MVC.
b) Hoàn toàn không có sự khác biệt.
c) `ResponseEntityExceptionHandler` là một lớp cơ sở (base class) tiện lợi đã cung cấp sẵn các handler cho các exception phổ biến của Spring MVC (như `MethodArgumentNotValidException`, `HttpRequestMethodNotSupportedException`). Việc kế thừa nó giúp bạn không phải viết lại các handler này từ đầu.
d) `ResponseEntityExceptionHandler` là cách làm cũ, `@ControllerAdvice` là cách làm mới và hiện đại hơn.

---
**Đáp án:** **c) `ResponseEntityExceptionHandler` là một lớp cơ sở (base class) tiện lợi đã cung cấp sẵn các handler cho các exception phổ biến của Spring MVC (như `MethodArgumentNotValidException`, `HttpRequestMethodNotSupportedException`). Việc kế thừa nó giúp bạn không phải viết lại các handler này từ đầu.**

**Giải thích:** Bạn có thể tự viết tất cả các handler từ đầu chỉ với `@ControllerAdvice`. Tuy nhiên, `ResponseEntityExceptionHandler` cung cấp một điểm khởi đầu tuyệt vời. Nó đã xử lý sẵn các lỗi "kỹ thuật" của Spring MVC. Bạn chỉ cần kế thừa nó, sau đó `override` các phương thức bạn muốn tùy chỉnh định dạng lỗi, và thêm các `@ExceptionHandler` mới cho các custom exception nghiệp vụ của riêng bạn. Đây là một best practice phổ biến.

## Lesson 5: Serialization, Deserialization & JSON Customization

### 1. Giải thích khái niệm chi tiết

#### Jackson là gì và nó hoạt động như thế nào?

Khi bạn xây dựng REST API với Spring Boot và `spring-boot-starter-web`, thư viện **Jackson** được tự động thêm vào và cấu hình làm trình xử lý JSON mặc định. Jackson là một thư viện Java cực kỳ mạnh mẽ và phổ biến để làm việc với JSON.

*   **Serialization (Tuần tự hóa):** Là quá trình chuyển đổi một đối tượng Java (POJO) thành một chuỗi JSON.
    *   **Khi nào xảy ra?** Khi một phương thức trong `@RestController` của bạn trả về một đối tượng, `MappingJackson2HttpMessageConverter` sẽ sử dụng Jackson để serialize đối tượng đó trước khi ghi vào response body.
*   **Deserialization (Giải tuần tự hóa):** Là quá trình chuyển đổi một chuỗi JSON thành một đối tượng Java.
    *   **Khi nào xảy ra?** Khi một request `POST` hoặc `PUT` có body là JSON được gửi đến một endpoint có tham số được đánh dấu `@RequestBody`, Spring sẽ dùng Jackson để deserialize chuỗi JSON đó thành một đối tượng Java tương ứng.

Jackson sử dụng reflection để tự động phát hiện các trường (fields) hoặc các phương thức getter trong lớp Java và ánh xạ chúng tới các key trong JSON. Ví dụ, một phương thức `getUsername()` sẽ được ánh xạ tới key `"username"`.

#### Tùy chỉnh JSON với các Annotation của Jackson

Mặc dù hành vi mặc định của Jackson rất tốt, nhưng có nhiều lúc bạn cần tùy chỉnh đầu ra/đầu vào của JSON.

*   **`@JsonProperty("ten_moi")`:**
    *   **Công dụng:** Đổi tên key trong JSON. Mặc định, key sẽ là tên của trường Java. Annotation này cho phép bạn định nghĩa một tên khác, ví dụ để tuân thủ quy ước đặt tên `snake_case` trong JSON (`user_name`) trong khi vẫn dùng `userName` (camelCase) trong Java.
    *   ```java
        @JsonProperty("full_name")
        private String fullName; // Sẽ thành "full_name": "..." trong JSON
        ```

*   **`@JsonIgnore`:**
    *   **Công dụng:** Bỏ qua hoàn toàn một trường, không cho nó xuất hiện trong cả quá trình serialization và deserialization.
    *   **Khi nào dùng?** Rất hữu ích để ẩn các thông tin nhạy cảm (như `password`) hoặc các trường chỉ dùng nội bộ.

*   **`@JsonInclude(JsonInclude.Include.NON_NULL)`:**
    *   **Công dụng:** Chỉ bao gồm các trường có giá trị **không phải là `null`** vào trong JSON output.
    *   **Khi nào dùng?** Giúp cho JSON response gọn gàng hơn, không chứa các key vô nghĩa có giá trị `null`. Có thể đặt ở cấp độ class để áp dụng cho tất cả các trường.

*   **`@JsonFormat(shape = ..., pattern = "...")`:**
    *   **Công dụng:** Định dạng cách một giá trị được biểu diễn trong JSON, đặc biệt hữu ích cho ngày giờ.
    *   ```java
        @JsonFormat(pattern = "dd-MM-yyyy HH:mm:ss")
        private LocalDateTime createdAt; // Sẽ thành "createdAt": "17-10-2025 10:30:00"
        ```

#### Xử lý quan hệ hai chiều (Bidirectional Relationships)

Đây là một trong những lỗi phổ biến nhất khi làm việc với JPA và JSON.

*   **Vấn đề:** Giả sử bạn có entity `User` và `Order` với quan hệ hai chiều (`User` có `List<Order>`, và `Order` có tham chiếu ngược lại `User`). Khi Jackson cố gắng serialize một đối tượng `User`:
    1.  Nó bắt đầu serialize `User`.
    2.  Nó thấy trường `orders` và bắt đầu serialize danh sách các `Order`.
    3.  Trong mỗi `Order`, nó thấy trường `user` và lại bắt đầu serialize đối tượng `User` ban đầu.
    4.  Quá trình này lặp lại vô tận, gây ra lỗi **vòng lặp vô hạn (infinite recursion)**, thường là `StackOverflowError`.

*   **Giải pháp:**
    1.  **`@JsonManagedReference` và `@JsonBackReference`:**
        *   Đây là cặp annotation được thiết kế đặc biệt để giải quyết vấn đề này.
        *   Đặt `@JsonManagedReference` ở phía "cha" (bên `OneToMany`, ví dụ trường `orders` trong `User`). Phía này sẽ được serialize bình thường.
        *   Đặt `@JsonBackReference` ở phía "con" (bên `ManyToOne`, ví dụ trường `user` trong `Order`). Phía này sẽ **bị bỏ qua** trong quá trình serialization, phá vỡ vòng lặp.
    2.  **`@JsonIgnore`:** Một giải pháp đơn giản hơn là đặt `@JsonIgnore` trên trường tham chiếu ngược (`user` trong `Order`).
    3.  **DTO Pattern (Best Practice):** Cách tốt nhất và linh hoạt nhất là **không bao giờ trả về trực tiếp các đối tượng Entity** từ controller. Thay vào đó, hãy tạo các lớp DTO (Data Transfer Object) riêng, chỉ chứa các trường bạn thực sự muốn hiển thị cho client. Tầng service sẽ chịu trách nhiệm chuyển đổi từ Entity sang DTO. Cách này giúp:
        *   Tránh hoàn toàn các vấn đề về vòng lặp và `LazyInitializationException`.
        *   Tách biệt API của bạn khỏi cấu trúc database. Bạn có thể thay đổi Entity mà không làm ảnh hưởng đến client.
        *   Che giấu các thông tin không cần thiết.

### 2. Ví dụ minh họa

**Tình huống:** Trả về thông tin một `User` và các `Order` của họ, tùy chỉnh các trường JSON và xử lý quan hệ hai chiều.

**1. Định nghĩa các Entity (với giải pháp `@JsonManagedReference` / `@JsonBackReference`):**

```java
// User.java
@Entity
public class User {
    @Id private Long id;
    
    @JsonProperty("user_name") // Đổi tên key trong JSON
    private String username;

    @JsonIgnore // Ẩn password
    private String password;

    @OneToMany(mappedBy = "user")
    @JsonManagedReference // Phía này sẽ được serialize
    private List<Order> orders;
    
    // ...
}

// Order.java
@Entity
public class Order {
    @Id private Long id;
    
    @JsonFormat(pattern = "yyyy-MM-dd")
    private LocalDate orderDate;

    @ManyToOne
    @JsonBackReference // Phía này sẽ bị bỏ qua, phá vỡ vòng lặp
    private User user;

    // ...
}
```

**2. Sử dụng DTO Pattern (Cách làm tốt hơn):**

*   **Tạo các lớp DTO:**
    ```java
    // OrderSummaryDto.java
    @JsonInclude(JsonInclude.Include.NON_NULL) // Bỏ qua các trường null
    public class OrderSummaryDto {
        private Long id;
        @JsonFormat(pattern = "yyyy-MM-dd")
        private LocalDate orderDate;
        // Không có trường User user
        // ... getters/setters
    }

    // UserDetailDto.java
    public class UserDetailDto {
        private Long id;
        @JsonProperty("user_name")
        private String username;
        private List<OrderSummaryDto> orders;
        // ... getters/setters
    }
    ```
*   **Tầng Service thực hiện việc chuyển đổi:**
    ```java
    @Service
    public class UserServiceImpl implements UserService {
        // ...
        public UserDetailDto findUserWithOrders(Long id) {
            User user = userRepository.findById(id).orElseThrow(...);
            
            // Chuyển đổi thủ công hoặc dùng thư viện như ModelMapper, MapStruct
            UserDetailDto dto = new UserDetailDto();
            dto.setId(user.getId());
            dto.setUsername(user.getUsername());
            
            List<OrderSummaryDto> orderDtos = user.getOrders().stream()
                .map(order -> {
                    OrderSummaryDto orderDto = new OrderSummaryDto();
                    orderDto.setId(order.getId());
                    orderDto.setOrderDate(order.getOrderDate());
                    return orderDto;
                })
                .collect(Collectors.toList());
            
            dto.setOrders(orderDtos);
            return dto;
        }
    }
    ```
*   **Controller trả về DTO:**
    ```java
    @RestController
    public class UserController {
        // ...
        @GetMapping("/users/{id}/details")
        public ResponseEntity<UserDetailDto> getUserDetails(@PathVariable Long id) {
            return ResponseEntity.ok(userService.findUserWithOrders(id));
        }
    }
    ```
    *   **Kết quả:** JSON response sẽ sạch, chỉ chứa thông tin cần thiết và không bị vòng lặp.

### 3. Mini Exercise

1.  Tạo một DTO mới tên là `UserCreationResponseDto` chứa `id`, `username`, và `createdAt`.
2.  Sử dụng `@JsonProperty` để đổi tên `createdAt` thành `creation_timestamp` trong JSON.
3.  Sử dụng `@JsonFormat` để đảm bảo `creation_timestamp` có định dạng `yyyy-MM-dd'T'HH:mm:ss`.
4.  Sửa lại endpoint `POST /users` để trả về DTO này thay vì `UserDto` cũ.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** Bạn muốn một trường `String` trong DTO của mình chỉ được serialize nếu nó không phải là `null` và cũng không phải là một chuỗi rỗng (`""`). Bạn nên sử dụng annotation nào?

a) `@JsonIgnore`
b) `@JsonInclude(JsonInclude.Include.NON_NULL)`
c) `@JsonInclude(JsonInclude.Include.NON_EMPTY)`
d) `@JsonProperty(required = true)`

---
**Đáp án:** **c) `@JsonInclude(JsonInclude.Include.NON_EMPTY)`**

**Giải thích:**
*   `NON_NULL` chỉ kiểm tra giá trị `null`. Một chuỗi rỗng `""` vẫn sẽ được serialize.
*   `NON_EMPTY` kiểm tra cả `null` và "rỗng". Đối với `String`, "rỗng" là `""`. Đối với `Collection`, "rỗng" là một collection không có phần tử. Đây là lựa chọn chính xác cho yêu cầu này.
*   `NON_DEFAULT` sẽ bỏ qua nếu giá trị của trường bằng với giá trị mặc định của kiểu đó (ví dụ: `0` cho `int`, `false` cho `boolean`).

---

## Lesson 6: File Upload & Download

### 1. Giải thích khái niệm chi tiết

#### Xử lý File Upload

Việc cho phép người dùng tải file lên là một chức năng phổ biến, từ việc tải lên ảnh đại diện cho đến import một file CSV. Spring Boot làm cho việc này trở nên tương đối đơn giản thông qua việc tích hợp với **Apache Commons FileUpload**.

*   **HTML Form:** Ở phía client, việc upload file thường được thực hiện qua một form HTML có `enctype="multipart/form-data"`. Thuộc tính này báo cho trình duyệt rằng form sẽ gửi dữ liệu dưới dạng "multipart", tức là request body sẽ được chia thành nhiều phần, mỗi phần có thể là một trường form thông thường hoặc một file.

*   **`MultipartFile`:**
    *   Đây là một interface của Spring, là cách trừu tượng hóa một file được tải lên trong một request multipart.
    *   Khi Spring MVC nhận một request `multipart/form-data`, `DispatcherServlet` sẽ sử dụng một `MultipartResolver` để phân tích request đó. Nếu có file được tải lên, nó sẽ được bọc trong một đối tượng `MultipartFile`.
    *   Bạn có thể nhận đối tượng này trực tiếp trong phương thức controller bằng cách khai báo một tham số kiểu `MultipartFile`.

*   **Các phương thức hữu ích của `MultipartFile`:**
    *   `getOriginalFilename()`: Lấy tên file gốc từ máy của client.
    *   `getContentType()`: Lấy kiểu MIME của file (ví dụ: `image/jpeg`, `application/pdf`).
    *   `getSize()`: Lấy kích thước file (tính bằng byte).
    *   `getBytes()`: Đọc toàn bộ nội dung file vào một mảng `byte[]` trong bộ nhớ. **Cẩn thận:** Chỉ dùng cho file nhỏ, vì nó có thể gây `OutOfMemoryError` với file lớn.
    *   `getInputStream()`: Lấy một `InputStream` để đọc nội dung file theo kiểu streaming, hiệu quả hơn cho file lớn.
    *   `transferTo(File dest)`: Lưu nội dung file đã tải lên vào một file trên hệ thống của server. Đây là cách làm phổ biến và tiện lợi nhất.

*   **Cấu hình:** Mặc định, Spring Boot cho phép upload file với kích thước tối đa là **1MB/file** và **10MB/request**. Bạn có thể dễ dàng thay đổi các giới hạn này trong `application.properties`:
    ```properties
    # Kích thước tối đa cho một file
    spring.servlet.multipart.max-file-size=10MB
    # Kích thước tối đa cho toàn bộ request (bao gồm nhiều file và các trường khác)
    spring.servlet.multipart.max-request-size=100MB
    ```

#### Xử lý File Download

Việc trả về một file cho client cũng là một yêu cầu phổ biến. Điều quan trọng là phải thiết lập đúng các HTTP header để trình duyệt hiểu rằng đây là một file cần được tải xuống, chứ không phải là nội dung để hiển thị trực tiếp.

*   **`ResponseEntity<Resource>`:** Đây là cách làm mạnh mẽ và được khuyên dùng nhất trong Spring để xử lý việc download.
    *   **`Resource`:** Là một interface của Spring (đã học trong Spring Core) để trừu tượng hóa các nguồn tài nguyên (file trên hệ thống, file trong classpath...).
    *   **`InputStreamResource`:** Một implementation của `Resource`, bọc một `InputStream`. Cách này rất hiệu quả về bộ nhớ vì nó **stream** dữ liệu trực tiếp từ nguồn ra response, thay vì phải đọc toàn bộ file vào bộ nhớ trước.

*   **Các HTTP Header quan trọng:**
    *   **`Content-Type`:** Chỉ định kiểu MIME của file (ví dụ: `application/octet-stream` cho dữ liệu nhị phân chung, hoặc `image/png`, `application/pdf`). Điều này giúp trình duyệt biết cách xử lý file.
    *   **`Content-Disposition`:** Header này ra lệnh cho trình duyệt.
        *   `attachment; filename="ten_file.ext"`: Báo cho trình duyệt phải mở hộp thoại "Save As..." để người dùng tải file về với tên được chỉ định.
        *   `inline; filename="ten_file.ext"`: Gợi ý cho trình duyệt hãy cố gắng hiển thị file trực tiếp nếu có thể (ví dụ: hiển thị ảnh, file PDF).

### 2. Ví dụ minh họa

**Tình huống:** Xây dựng API cho phép upload ảnh sản phẩm và sau đó download ảnh đó.

**1. Tạo một Service để lưu trữ file:**
Để đơn giản, chúng ta sẽ lưu file vào một thư mục trên server. Trong thực tế, bạn nên lưu vào các dịch vụ lưu trữ đối tượng như Amazon S3, Azure Blob Storage.

```java
// FileStorageService.java
@Service
public class FileStorageService {
    // Path đến thư mục lưu file
    private final Path fileStorageLocation;

    public FileStorageService() {
        this.fileStorageLocation = Paths.get("./uploads").toAbsolutePath().normalize();
        try {
            Files.createDirectories(this.fileStorageLocation);
        } catch (Exception ex) {
            throw new RuntimeException("Could not create the directory where the uploaded files will be stored.", ex);
        }
    }

    public String storeFile(MultipartFile file) {
        // Tạo một tên file duy nhất để tránh trùng lặp
        String fileName = StringUtils.cleanPath(UUID.randomUUID().toString() + "-" + file.getOriginalFilename());

        try {
            // Kiểm tra tên file không hợp lệ
            if (fileName.contains("..")) {
                throw new RuntimeException("Sorry! Filename contains invalid path sequence " + fileName);
            }
            Path targetLocation = this.fileStorageLocation.resolve(fileName);
            // Lưu file vào thư mục đích
            Files.copy(file.getInputStream(), targetLocation, StandardCopyOption.REPLACE_EXISTING);
            return fileName;
        } catch (IOException ex) {
            throw new RuntimeException("Could not store file " + fileName + ". Please try again!", ex);
        }
    }

    public Resource loadFileAsResource(String fileName) {
        try {
            Path filePath = this.fileStorageLocation.resolve(fileName).normalize();
            Resource resource = new UrlResource(filePath.toUri());
            if (resource.exists()) {
                return resource;
            } else {
                throw new FileNotFoundException("File not found " + fileName);
            }
        } catch (MalformedURLException | FileNotFoundException ex) {
            throw new RuntimeException("File not found " + fileName, ex);
        }
    }
}
```

**2. Tạo Controller:**

```java
// FileController.java
@RestController
@RequestMapping("/api/files")
public class FileController {

    @Autowired
    private FileStorageService fileStorageService;

    // Endpoint để upload file
    @PostMapping("/upload")
    public ResponseEntity<String> uploadFile(@RequestParam("file") MultipartFile file) {
        String fileName = fileStorageService.storeFile(file);

        // Tạo URI để download file
        String fileDownloadUri = ServletUriComponentsBuilder.fromCurrentContextPath()
                .path("/api/files/download/")
                .path(fileName)
                .toUriString();

        return ResponseEntity.ok("File uploaded successfully! Download URL: " + fileDownloadUri);
    }

    // Endpoint để download file
    @GetMapping("/download/{fileName:.+}")
    public ResponseEntity<Resource> downloadFile(@PathVariable String fileName, HttpServletRequest request) {
        // Tải file dưới dạng Resource
        Resource resource = fileStorageService.loadFileAsResource(fileName);

        // Cố gắng xác định Content-Type của file
        String contentType = null;
        try {
            contentType = request.getServletContext().getMimeType(resource.getFile().getAbsolutePath());
        } catch (IOException ex) {
            // Bỏ qua nếu không xác định được
        }

        // Đặt Content-Type mặc định nếu không xác định được
        if (contentType == null) {
            contentType = "application/octet-stream";
        }

        return ResponseEntity.ok()
                .contentType(MediaType.parseMediaType(contentType))
                .header(HttpHeaders.CONTENT_DISPOSITION, "attachment; filename=\"" + resource.getFilename() + "\"")
                .body(resource);
    }
}
```
*   `{fileName:.+}`: Phần regex `:.+` trong `@PathVariable` là cần thiết để Spring không cắt mất phần đuôi file có dấu chấm (ví dụ: `.jpg`).

### 3. Mini Exercise

1.  Sửa lại endpoint `uploadFile` để có thể nhận và xử lý **nhiều file cùng lúc** trong một request.
2.  **Gợi ý:** Tham số trong controller sẽ là một mảng hoặc một `List`: `@RequestParam("files") MultipartFile[] files`.
3.  Sử dụng `Arrays.stream()` hoặc một vòng lặp `for` để gọi `fileStorageService.storeFile()` cho mỗi file.
4.  Trả về một danh sách các URL download tương ứng.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** Tại sao việc sử dụng `multipartFile.getBytes()` để xử lý file upload lớn lại là một ý tưởng tồi?

a) Vì phương thức này chậm hơn `multipartFile.getInputStream()`.
b) Vì phương thức này đọc toàn bộ nội dung file vào heap memory của JVM, có thể gây ra lỗi `OutOfMemoryError` nếu file quá lớn hoặc có nhiều request đồng thời.
c) Vì `getBytes()` không hỗ trợ các file nhị phân, chỉ hỗ trợ file text.
d) Vì nó không an toàn và có thể bị tấn công Path Traversal.

---
**Đáp án:** **b) Vì phương thức này đọc toàn bộ nội dung file vào heap memory của JVM, có thể gây ra lỗi `OutOfMemoryError` nếu file quá lớn hoặc có nhiều request đồng thời.**

**Giải thích:** Heap memory là một tài nguyên hữu hạn. Nếu bạn tải một file 1GB vào bộ nhớ, và có 10 người dùng làm điều đó cùng lúc, ứng dụng của bạn sẽ cần 10GB heap memory và rất có thể sẽ sập. Sử dụng `InputStream` và xử lý file theo kiểu streaming (ghi trực tiếp từ stream đầu vào ra file đầu ra) là cách làm hiệu quả và an toàn nhất, vì nó chỉ sử dụng một bộ đệm (buffer) nhỏ trong bộ nhớ tại một thời điểm.

---

## Lesson 7: API Documentation và Testing

### 1. Giải thích khái niệm chi tiết

#### API Documentation với SpringDoc OpenAPI (Swagger)

*   **Vấn đề:** Khi bạn xây dựng một API, làm thế nào để các đội ngũ khác (frontend, mobile, Q&A, đối tác) biết được API của bạn có những endpoint nào, cần tham số gì, trả về cấu trúc dữ liệu ra sao, và các quy tắc xác thực là gì? Gửi file Word hay Postman collection rất thủ công và dễ bị lỗi thời.
*   **Giải pháp: OpenAPI Specification (trước đây là Swagger Specification).** Đây là một tiêu chuẩn mở để mô tả các REST API. Nó định nghĩa một file JSON hoặc YAML mô tả toàn bộ API của bạn.
*   **Swagger UI:** Là một công cụ mã nguồn mở, đọc file đặc tả OpenAPI và tự động tạo ra một giao diện web **tương tác** đẹp mắt. Từ giao diện này, người dùng có thể:
    *   Xem danh sách tất cả các endpoint.
    *   Xem chi tiết từng endpoint: method, path, tham số, request body mẫu, các response có thể có.
    *   **Thực thi các request API trực tiếp từ trình duyệt** để thử nghiệm.
*   **SpringDoc OpenAPI:** Là thư viện giúp tích hợp OpenAPI 3 vào ứng dụng Spring Boot. Nó sẽ **tự động sinh ra đặc tả OpenAPI** bằng cách quét các annotation của Spring Web (`@RestController`, `@GetMapping`, `@PathVariable`, `@RequestBody`...) và Bean Validation (`@NotNull`, `@Size`...) trong code của bạn.

**"Code as Documentation"**: Triết lý ở đây là tài liệu của bạn nên được tạo ra từ chính code. Khi bạn thay đổi một endpoint, tài liệu sẽ tự động cập nhật theo, giảm thiểu rủi ro tài liệu bị lỗi thời.

#### Testing REST API

Như đã học trong bài Spring Boot Test, chúng ta có nhiều cách để kiểm thử, nhưng đối với controller, có hai cách tiếp cận chính:

1.  **MockMVC (`@WebMvcTest`):**
    *   **Nó là gì?** Là một framework của Spring Test cho phép bạn thực hiện các bài test cho tầng web **mà không cần khởi động một HTTP server thật**. Nó hoạt động trong một môi trường giả lập (mocked servlet environment).
    *   **Cách hoạt động:** Nó gọi trực tiếp `DispatcherServlet` và chạy qua gần như toàn bộ stack của Spring MVC (filter, interceptor, controller, exception handler...), nhưng lớp mạng (network layer) bị bỏ qua.
    *   **Ưu điểm:**
        *   **Nhanh:** Vì không có chi phí khởi động server.
        *   **"White-box" testing:** Bạn có thể kiểm tra các thành phần nội bộ của Spring MVC, ví dụ như view nào được chọn, model chứa gì.
        *   Tích hợp sâu với Spring, dễ dàng mock các bean với `@MockBean`.
    *   **Khi nào dùng?** Đây là lựa chọn **tốt nhất cho unit test và integration test của controller**, tập trung vào việc kiểm tra logic của controller, validation, và serialization.

2.  **`@SpringBootTest` với `WebEnvironment.RANDOM_PORT`:**
    *   **Nó là gì?** Đây là một bài test tích hợp (integration test) đầy đủ. Nó sẽ khởi động **toàn bộ ứng dụng Spring Boot**, bao gồm cả embedded server (Tomcat) trên một cổng ngẫu nhiên.
    *   **Cách hoạt động:** Bạn sử dụng một client HTTP như `TestRestTemplate` hoặc `WebTestClient` để thực hiện các lời gọi HTTP **thật** đến server đang chạy đó.
    *   **Ưu điểm:**
        *   **Test gần với môi trường thật nhất:** Nó kiểm tra toàn bộ luồng từ lớp mạng xuống database.
        *   "Black-box" testing: Bạn test API từ góc nhìn của một client bên ngoài.
    *   **Nhược điểm:**
        *   **Chậm:** Mất nhiều thời gian để khởi động toàn bộ `ApplicationContext`.
    *   **Khi nào dùng?** Dùng cho các kịch bản test end-to-end quan trọng, kiểm tra luồng hoàn chỉnh của một tính năng.

### 2. Ví dụ minh họa

**Tình huống:** Thêm tài liệu Swagger và viết các bài test cho `UserController`.

**1. Thêm Documentation với SpringDoc OpenAPI:**

*   **Thêm dependency `pom.xml`:**
    ```xml
    <dependency>
        <groupId>org.springdoc</groupId>
        <artifactId>springdoc-openapi-ui</artifactId>
        <version>1.6.12</version> <!-- Hoặc phiên bản mới hơn -->
    </dependency>
    ```
*   **Không cần làm gì thêm!**
*   **Kết quả:** Khởi động lại ứng dụng của bạn và truy cập vào hai URL sau:
    *   `http://localhost:8080/v3/api-docs`: Bạn sẽ thấy file đặc tả OpenAPI (JSON) được tự động sinh ra.
    *   `http://localhost:8080/swagger-ui.html`: Bạn sẽ thấy giao diện Swagger UI tương tác, đọc từ file JSON trên.

*   **Tùy chỉnh tài liệu (Tùy chọn):**
    Bạn có thể dùng các annotation của OpenAPI (`@Operation`, `@ApiResponse`, `@Parameter`) để làm cho tài liệu chi tiết và rõ ràng hơn.
    ```java
    import io.swagger.v3.oas.annotations.Operation;
    import io.swagger.v3.oas.annotations.media.Content;
    import io.swagger.v3.oas.annotations.media.Schema;
    import io.swagger.v3.oas.annotations.responses.ApiResponse;
    import io.swagger.v3.oas.annotations.tags.Tag;

    @Tag(name = "User API", description = "APIs for managing users")
    @RestController
    @RequestMapping("/api/v1/users")
    public class UserController {

        @Operation(summary = "Get a user by their id")
        @ApiResponse(responseCode = "200", description = "Found the user",
                content = { @Content(mediaType = "application/json",
                        schema = @Schema(implementation = UserDto.class)) })
        @ApiResponse(responseCode = "404", description = "User not found", content = @Content)
        @GetMapping("/{userId}")
        public ResponseEntity<UserDto> getUserById(@PathVariable Long userId) {
            // ...
        }
    }
    ```

**2. Viết Test cho Controller với MockMvc:**

*   Đảm bảo `spring-boot-starter-test` đã có trong `pom.xml`.

```java
import com.fasterxml.jackson.databind.ObjectMapper;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.hamcrest.CoreMatchers.is;
import static org.mockito.ArgumentMatchers.any;
import static org.mockito.BDDMockito.given;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

// Chỉ test UserController, không tải toàn bộ context
@WebMvcTest(UserController.class)
public class UserControllerTest {

    @Autowired
    private MockMvc mockMvc; // Công cụ để thực hiện request giả

    @Autowired
    private ObjectMapper objectMapper; // Dùng để chuyển đổi Java object thành chuỗi JSON

    @MockBean
    private UserService userService; // Mock tầng service

    @Test
    void whenGetUserById_andUserExists_shouldReturnUser() throws Exception {
        // Given (Sắp xếp)
        UserDto user = new UserDto(1L, "testuser", "test@example.com");
        given(userService.findUserById(1L)).willReturn(user);

        // When & Then (Thực thi & Kiểm tra)
        mockMvc.perform(get("/api/v1/users/1"))
                .andExpect(status().isOk())
                .andExpect(content().contentType(MediaType.APPLICATION_JSON))
                .andExpect(jsonPath("$.username", is("testuser")));
    }
    
    @Test
    void whenGetUserById_andUserNotFound_shouldReturnNotFound() throws Exception {
        // Given
        given(userService.findUserById(99L)).willThrow(new ResourceNotFoundException("User not found"));
        
        // When & Then
        mockMvc.perform(get("/api/v1/users/99"))
                .andExpect(status().isNotFound());
    }

    @Test
    void whenCreateUser_withValidInput_shouldReturnCreated() throws Exception {
        // Given
        CreateUserRequest request = new CreateUserRequest("newuser", "new@example.com", "password123");
        UserDto createdUser = new UserDto(1L, "newuser", "new@example.com");
        
        // Dùng any() vì đối tượng request được tạo trong service có thể khác
        given(userService.createUser(any(CreateUserRequest.class))).willReturn(createdUser);

        // When & Then
        mockMvc.perform(post("/api/v1/users")
                .contentType(MediaType.APPLICATION_JSON)
                .content(objectMapper.writeValueAsString(request))) // Chuyển request object thành chuỗi JSON
                .andExpect(status().isCreated())
                .andExpect(jsonPath("$.username", is("newuser")));
    }
}
```

### 3. Mini Exercise

1.  Viết một bài test cho endpoint `POST /api/v1/users` với **dữ liệu không hợp lệ** (ví dụ: username quá ngắn).
2.  Sử dụng `mockMvc.perform()` để gửi request.
3.  Sử dụng `andExpect()` để xác nhận rằng:
    *   HTTP status là `400 Bad Request`.
    *   Response JSON chứa một key `errors`.
    *   Trong `errors`, có một key là `username` với thông báo lỗi tương ứng.
4.  **Lưu ý:** Bạn không cần mock `UserService` trong bài test này, vì validation xảy ra trước khi phương thức controller được gọi.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** Khi nào bạn nên chọn `@SpringBootTest` thay vì `@WebMvcTest` để kiểm thử một controller?

a) Luôn luôn, vì nó test toàn diện hơn.
b) Khi bạn muốn test logic validation của controller.
c) Khi bạn cần thực hiện một bài test end-to-end, kiểm tra sự tích hợp thực sự giữa controller, service, và database, và bạn chấp nhận rằng bài test sẽ chạy chậm hơn.
d) Khi controller của bạn không có dependency nào.

---
**Đáp án:** **c) Khi bạn cần thực hiện một bài test end-to-end, kiểm tra sự tích hợp thực sự giữa controller, service, và database, và bạn chấp nhận rằng bài test sẽ chạy chậm hơn.**

**Giải thích:** `@WebMvcTest` là để kiểm thử controller một cách cô lập (unit/integration test ở mức độ controller). `@SpringBootTest` là để kiểm thử toàn bộ ứng dụng (end-to-end integration test). Mỗi loại có mục đích riêng. Dùng `@SpringBootTest` cho mọi thứ sẽ làm bộ test của bạn rất chậm chạp. Quy tắc chung là: dùng công cụ "nhẹ" nhất có thể để hoàn thành công việc.

---

## Lesson 8: Advanced Topics & Best Practices

### 1. Giải thích khái niệm chi tiết

#### Pagination & Sorting trong API

Khi một endpoint có thể trả về một danh sách lớn các tài nguyên (ví dụ: `GET /orders`), việc trả về tất cả trong một lần là một thảm họa về hiệu năng.

*   **Vấn đề:** Gây quá tải cho database, mạng, và bộ nhớ của cả server và client.
*   **Giải pháp: Phân trang (Pagination).** Client sẽ yêu cầu từng "trang" dữ liệu một.
*   **Cách triển khai với Spring Data:** Spring Data làm việc này cực kỳ dễ dàng.
    1.  **Trong Controller:** Thêm một tham số kiểu `Pageable` vào phương thức của bạn. Spring Web sẽ tự động nhận diện các request parameter như `page`, `size`, và `sort` để tạo ra đối tượng `Pageable`.
    2.  **Trong Service/Repository:** Truyền đối tượng `Pageable` này xuống phương thức của repository (ví dụ: `findAll(Pageable pageable)`). Spring Data JPA sẽ tự động thêm các mệnh đề `LIMIT`, `OFFSET`, và `ORDER BY` vào câu lệnh SQL.
    3.  **Response:** Phương thức repository sẽ trả về một đối tượng `Page`, không chỉ chứa danh sách dữ liệu của trang hiện tại (`getContent()`) mà còn chứa các thông tin metadata rất hữu ích cho client:
        *   `getTotalElements()`: Tổng số phần tử.
        *   `getTotalPages()`: Tổng số trang.
        *   `getNumber()`: Số thứ tự của trang hiện tại (bắt đầu từ 0).
        *   `getSize()`: Kích thước của trang.
        *   `isFirst()`, `isLast()`: Kiểm tra có phải trang đầu/cuối không.

#### CORS (Cross-Origin Resource Sharing)

*   **Vấn đề (Same-Origin Policy):** Vì lý do bảo mật, trình duyệt web mặc định sẽ chặn các request JavaScript (ví dụ AJAX, Fetch API) gửi từ một "origin" (ví dụ `http://my-frontend.com`) đến một "origin" khác (ví dụ `http://my-api.com`). Một origin được định nghĩa bởi bộ ba: **protocol + domain + port**.
*   **Giải pháp: CORS.** Đây là một cơ chế cho phép server (API) nói với trình duyệt rằng: "Tôi cho phép các request đến từ origin X, Y, Z".
*   **Preflight Request:** Đối với các request "phức tạp" (ví dụ: `PUT`, `DELETE`, hoặc request có custom header như `Authorization`), trình duyệt sẽ tự động gửi một request **`OPTIONS`** trước (gọi là preflight request). Server phải trả về các header CORS (như `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`) để xác nhận. Nếu preflight thành công, trình duyệt mới gửi request thật sự.
*   **Cách cấu hình trong Spring Boot:**
    *   **Cách đơn giản (`@CrossOrigin`):** Đặt annotation này trên controller hoặc từng phương thức. Dễ dùng cho các trường hợp đơn giản.
    *   **Cách toàn cục (Best Practice):** Cấu hình tập trung trong một lớp `@Configuration` bằng cách implement `WebMvcConfigurer` và override phương thức `addCorsMappings`. Cách này cho phép kiểm soát tốt hơn và áp dụng cho toàn bộ ứng dụng.

#### Filters vs. Interceptors

Cả hai đều là cơ chế cho phép bạn chèn logic vào quá trình xử lý request, nhưng chúng hoạt động ở các cấp độ khác nhau.

1.  **Servlet Filter:**
    *   **Cấp độ:** Hoạt động ở tầng Servlet Container, **trước khi** request đến `DispatcherServlet`.
    *   **Nó là gì?** Là một chuẩn của Java Servlet API, không phải của riêng Spring.
    *   **Khi nào dùng?** Dùng cho các tác vụ cross-cutting ở mức độ thấp, không phụ thuộc vào context của Spring. Ví dụ: xử lý nén (compression), mã hóa, hoặc thiết lập MDC (như ví dụ ở bài logging).

2.  **HandlerInterceptor:**
    *   **Cấp độ:** Hoạt động ở tầng Spring MVC, **sau khi** `DispatcherServlet` đã tìm ra handler (controller) nhưng **trước khi** `HandlerAdapter` gọi đến controller.
    *   **Nó là gì?** Là một interface của Spring.
    *   **Các phương thức:** Cung cấp 3 "điểm neo": `preHandle` (trước controller), `postHandle` (sau controller, trước khi render view/response), `afterCompletion` (sau khi response đã hoàn thành).
    *   **Khi nào dùng?** Khi bạn cần truy cập vào context của Spring, ví dụ như handler sẽ xử lý request là ai, hoặc cần thực hiện logic liên quan đến model. Rất hữu ích cho việc kiểm tra quyền (authorization), logging chi tiết, hoặc đo lường thời gian xử lý của controller.

#### Logging Request/Response (MDC, traceId)

Như đã đề cập ở bài logging, việc có một `traceId` (hay `correlationId`) duy nhất cho mỗi request và in nó ra trong mỗi dòng log là cực kỳ quan trọng để debug trong môi trường microservices.

*   **Luồng hoạt động:**
    1.  Một **Filter** (chạy đầu tiên) sẽ kiểm tra xem request có header `X-Request-ID` (hoặc tên tương tự) không.
    2.  Nếu có, nó sẽ lấy giá trị đó. Nếu không, nó sẽ tự tạo ra một UUID mới.
    3.  Nó đặt giá trị này vào **MDC** (`MDC.put("traceId", ...)`).
    4.  Cấu hình Logback/Log4j2 để tự động in `traceId` từ MDC ra mỗi dòng log.
    5.  Khi service này gọi một service khác, nó phải **truyền `traceId` này đi** trong header của request mới.
    6.  Service được gọi sẽ lặp lại quy trình từ bước 1.
*   **Kết quả:** Bạn có thể dùng các công cụ quản lý log (như ELK Stack, Splunk) để lọc theo một `traceId` duy nhất và xem toàn bộ hành trình của một request qua nhiều service khác nhau.

### 2. Ví dụ minh họa

**Tình huống 1: Cấu hình CORS toàn cục**

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**") // Áp dụng cho tất cả các path bắt đầu bằng /api/
                .allowedOrigins("http://localhost:3000", "https://my-production-frontend.com") // Cho phép các origin này
                .allowedMethods("GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS") // Cho phép các method này
                .allowedHeaders("*") // Cho phép tất cả các header
                .allowCredentials(true) // Cho phép gửi cookie
                .maxAge(3600); // Thời gian cache của preflight request (giây)
    }
}
```

**Tình huống 2: Tạo một HandlerInterceptor để đo thời gian thực thi**

```java
// PerformanceInterceptor.java
@Component
public class PerformanceInterceptor implements HandlerInterceptor {

    private static final Logger log = LoggerFactory.getLogger(PerformanceInterceptor.class);
    
    // Sử dụng ThreadLocal để lưu thời gian bắt đầu cho mỗi request
    private ThreadLocal<Long> startTime = new ThreadLocal<>();

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        // Chạy trước khi controller được gọi
        startTime.set(System.currentTimeMillis());
        return true; // return true để tiếp tục chuỗi xử lý
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        // Chạy sau khi controller chạy xong nhưng trước khi response được gửi đi
        long endTime = System.currentTimeMillis();
        long duration = endTime - startTime.get();
        log.info("Request URL::{} took {} ms to execute", request.getRequestURI(), duration);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        // Chạy sau khi response đã hoàn thành, dọn dẹp ThreadLocal
        startTime.remove();
    }
}

// WebConfig.java - Đăng ký Interceptor
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Autowired
    private PerformanceInterceptor performanceInterceptor;

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // Đăng ký interceptor và chỉ định path pattern nó sẽ áp dụng
        registry.addInterceptor(performanceInterceptor).addPathPatterns("/api/**");
    }
    
    // ... addCorsMappings ...
}
```

### 3. Mini Exercise

1.  Tạo một `HandlerInterceptor` mới tên là `ApiKeyInterceptor`.
2.  Trong phương thức `preHandle`, hãy kiểm tra xem request có chứa một header tên là `X-API-KEY` không.
3.  Nếu header không tồn tại hoặc giá trị của nó không phải là một chuỗi bí mật định trước (ví dụ: "SECRET123"), hãy ném ra một `RuntimeException("Invalid API Key")` và trả về `false`.
4.  Nếu header hợp lệ, trả về `true`.
5.  Đăng ký interceptor này trong `WebConfig` để nó áp dụng cho các path `/api/**`.
6.  Sử dụng Postman để kiểm tra: request không có header hoặc header sai sẽ bị chặn, request có header đúng sẽ đi qua.

### 4. Quiz Question

**Câu hỏi (Phỏng vấn!):** Đâu là sự khác biệt cơ bản về thời điểm thực thi giữa một `Servlet Filter` và một `HandlerInterceptor`?

a) `Filter` chạy sau `HandlerInterceptor`.
b) `Filter` là một phần của đặc tả Servlet và chạy trước khi request đến `DispatcherServlet` của Spring, trong khi `Interceptor` là của riêng Spring và chạy sau khi `DispatcherServlet` đã xác định được controller nào sẽ xử lý request.
c) `Filter` chỉ có thể đọc request, còn `Interceptor` có thể sửa đổi cả request và response.
d) `Filter` dùng cho REST API, còn `Interceptor` dùng cho ứng dụng web MVC truyền thống.

---
**Đáp án:** **b) `Filter` là một phần của đặc tả Servlet và chạy trước khi request đến `DispatcherServlet` của Spring, trong khi `Interceptor` là của riêng Spring và chạy sau khi `DispatcherServlet` đã xác định được controller nào sẽ xử lý request.**

**Giải thích:** Đây là sự khác biệt cốt lõi. `Filter` hoạt động ở mức độ thấp hơn và "bao quát" hơn, nó không biết gì về các khái niệm của Spring như handler hay model. `Interceptor` hoạt động trong context của Spring, do đó nó có quyền truy cập vào các thông tin phong phú hơn về request đang được xử lý (ví dụ: phương thức controller nào sẽ được gọi).

---
## Tổng kết chương: Spring Web & RESTful API

### 1. Bảng tổng hợp các khái niệm chính

| Khái niệm / Annotation | Vai trò và Mô tả | Khi nên dùng / Best Practice | Hạn chế / Lưu ý |
| :--- | :--- | :--- | :--- |
| **`DispatcherServlet`** | Front Controller của Spring MVC. Tiếp nhận mọi request và điều phối đến các handler phù hợp. | Là trái tim của Spring MVC, luôn hoạt động ngầm. | Hiểu luồng hoạt động của nó (HandlerMapping, HandlerAdapter) là chìa khóa để debug. |
| **`@RestController`** | Annotation tổng hợp (`@Controller` + `@ResponseBody`), đánh dấu một lớp là API controller. | **Luôn dùng cho các lớp API** trả về JSON hoặc các định dạng dữ liệu khác. | |
| **`@RequestMapping` & Co.** | Ánh xạ (map) các URI và HTTP method tới các phương thức controller (`@GetMapping`, `@PostMapping`...). | Sử dụng các annotation cụ thể (`@GetMapping`...) để code rõ ràng. Đặt path chung ở cấp độ class. | |
| **`@PathVariable` / `@RequestParam`** | Lấy dữ liệu từ URL path (`/users/{id}`) hoặc query string (`/users?status=ACTIVE`). | Dùng `@PathVariable` để định danh tài nguyên duy nhất. Dùng `@RequestParam` để lọc/sắp xếp. | |
| **`@RequestBody`** | Chuyển đổi (deserialize) request body (thường là JSON) thành một đối tượng Java. | Dùng cho các phương thức `POST`, `PUT`, `PATCH`. | Đối tượng Java tương ứng (DTO) phải có constructor mặc định và setters (hoặc là record). |
| **`ResponseEntity`** | Bao bọc toàn bộ HTTP response, cho phép kiểm soát hoàn toàn status code, headers, và body. | **Cách trả về response tốt nhất và linh hoạt nhất.** Cung cấp các builder tiện lợi (`ok()`, `created()`, `notFound()`). | |
| **Nguyên tắc RESTful** | Các ràng buộc kiến trúc (Stateless, Client-Server, Uniform Interface...). | Thiết kế API theo các nguyên tắc này để đảm bảo khả năng mở rộng, bảo trì và tính nhất quán. | **Stateless** là quan trọng nhất. |
| **HTTP Status Codes** | Sử dụng các mã code (`200`, `201`, `204`, `400`, `404`, `500`...) để truyền tải ngữ nghĩa kết quả của request. | Sử dụng đúng status code là cực kỳ quan trọng để client có thể xử lý response một cách chính xác. | |
| **Bean Validation (`@Valid`)**| Kích hoạt việc xác thực các ràng buộc (`@NotBlank`, `@Size`...) trên các đối tượng DTO. | Luôn validate dữ liệu đầu vào ở tầng controller để đảm bảo tính toàn vẹn và bảo mật. | Lỗi validation sẽ ném `MethodArgumentNotValidException`. |
| **`@RestControllerAdvice`** | Cơ chế xử lý exception toàn cục, tập trung logic xử lý lỗi vào một nơi. | **Luôn sử dụng** để chuẩn hóa định dạng lỗi và tránh lặp code. | Kết hợp với `@ExceptionHandler` để xử lý các loại exception cụ thể. |
| **Jackson Annotations** | Tùy chỉnh cách các đối tượng Java được serialize/deserialize thành JSON (`@JsonProperty`, `@JsonIgnore`...). | Dùng để đổi tên key, ẩn trường nhạy cảm, xử lý quan hệ hai chiều (`@JsonManagedReference`...). | **DTO Pattern** là cách tốt nhất để tránh các vấn đề serialization phức tạp. |
| **SpringDoc OpenAPI** | Tự động sinh ra tài liệu API tương tác (Swagger UI) từ code controller. | **Luôn tích hợp** vào dự án để giảm thời gian viết tài liệu thủ công và đảm bảo tài liệu luôn cập nhật. | |
| **`MockMvc` (`@WebMvcTest`)** | Framework để test các controller của Spring MVC trong một môi trường giả lập, không cần khởi động server. | **Cách tốt nhất để viết unit/integration test cho controller.** Nhanh và mạnh mẽ. | Cần dùng `@MockBean` để giả lập các dependency (như Service). |
| **CORS (`@CrossOrigin`)** | Cơ chế cho phép server nới lỏng Same-Origin Policy, cho phép các request từ origin khác. | Cần thiết khi frontend và backend được host trên các domain/port khác nhau. | Cấu hình toàn cục trong `WebMvcConfigurer` là best practice. |
| **`HandlerInterceptor`** | Cơ chế của Spring MVC để chèn logic vào các điểm khác nhau trong chu trình xử lý request. | Dùng cho các tác vụ cross-cutting cần truy cập context của Spring (ví dụ: authorization, logging nâng cao). | Chạy sau `Filter` nhưng trước `Controller`. |

---

### 2. Câu hỏi phỏng vấn toàn diện

#### Cơ bản
1.  `@Controller` và `@RestController` khác nhau như thế nào?
2.  `@RequestMapping`, `@GetMapping`, `@PostMapping` khác nhau ra sao?
3.  Hãy giải thích luồng xử lý của một request trong Spring MVC, vai trò của `DispatcherServlet` là gì?
4.  `@PathVariable` và `@RequestParam` khác nhau như thế nào? Cho ví dụ.
5.  REST là gì? Kể tên và giải thích ngắn gọn các nguyên tắc chính của REST.

#### Nâng cao
6.  **Stateless trong REST có nghĩa là gì? Tại sao nó lại quan trọng đối với khả năng mở rộng?**
7.  **Sự khác biệt giữa PUT và PATCH? Khi nào nên dùng cái nào?**
8.  **Idempotency là gì? Tại sao `POST` không idempotent nhưng `PUT` và `DELETE` lại có?**
9.  **Bạn sẽ xử lý lỗi validation trong Spring Boot như thế nào? Hãy mô tả cách bạn sẽ thiết kế một response lỗi chuẩn.** (Câu hỏi này kiểm tra kiến thức về `@Valid`, `MethodArgumentNotValidException`, `@RestControllerAdvice`).
10. **`@ControllerAdvice` và `ResponseEntityExceptionHandler` có mối quan hệ như thế nào?**
11. **Làm thế nào để xử lý quan hệ hai chiều (bidirectional relationship) khi serialize thành JSON để tránh lỗi vòng lặp vô hạn? Nêu ít nhất 2 cách.** (Câu hỏi này kiểm tra kiến thức về Jackson, `@JsonManagedReference`, `@JsonIgnore`, DTO Pattern).
12. **CORS là gì và Preflight Request hoạt động như thế nào?**
13. **So sánh `Servlet Filter` và `HandlerInterceptor`. Khi nào bạn sẽ chọn dùng cái này thay vì cái kia?**
14. **Mô tả cách bạn sẽ triển khai pagination và sorting cho một endpoint trả về danh sách tài nguyên.**
15. **Làm thế nào để bạn test một endpoint `POST` yêu cầu `@RequestBody` bằng `MockMvc`?**

---

### 3. Mini Project: "User Management API"

Dự án này sẽ tổng hợp các kỹ năng quan trọng nhất đã học để xây dựng một REST API hoàn chỉnh.

**Yêu cầu chức năng:**

*   API cho phép thực hiện các thao tác CRUD (Create, Read, Update, Delete) cho tài nguyên `User`.
*   Mỗi `User` có các thuộc tính: `id` (Long), `username` (String), `email` (String), `fullName` (String), `createdAt` (LocalDateTime).
*   Áp dụng validation cho các request tạo và cập nhật user.
*   Xử lý lỗi tập trung và trả về response lỗi theo một format JSON chuẩn.
*   Tích hợp Swagger để tự động tạo tài liệu API.
*   Viết unit test cho các endpoint của controller bằng `MockMvc`.
*   Ghi log cho các request đến và response đi.

#### Bước 1: Thiết lập dự án & Model/Entity

*   Tạo dự án Spring Boot với các dependency: `Web`, `Validation`, `Data JPA`, `H2 Database`, `Lombok` (tùy chọn), `SpringDoc OpenAPI`.
*   Tạo entity `User` và `UserRepository`.

#### Bước 2: Tạo các lớp DTO

*   `UserDto`: Dùng để trả về thông tin user (không có các trường nhạy cảm).
*   `CreateUserRequest`: Dùng cho `POST`, chứa các trường cần thiết để tạo user. Áp dụng các annotation validation (`@NotBlank`, `@Email`, `@Size`).
*   `UpdateUserRequest`: Dùng cho `PUT` hoặc `PATCH`, chứa các trường có thể được cập nhật. Áp dụng validation tương tự.

#### Bước 3: Tầng Service và Controller

*   Tạo `UserService` interface và implementation, chứa logic CRUD.
*   Tạo `UserController` với `@RestController` và `@RequestMapping("/api/v1/users")`.
*   Triển khai các endpoint:
    *   `POST /`: Tạo user mới (`@Valid @RequestBody`). Trả về `201 Created` với `Location` header.
    *   `GET /`: Lấy danh sách user (hỗ trợ pagination).
    *   `GET /{id}`: Lấy user theo ID. Trả về `404 Not Found` nếu không tồn tại.
    *   `PUT /{id}`: Cập nhật toàn bộ user.
    *   `DELETE /{id}`: Xóa user. Trả về `204 No Content`.

#### Bước 4: Exception Handling

*   Tạo các custom exception: `ResourceNotFoundException`, `EmailAlreadyExistsException`.
*   Tạo một `GlobalExceptionHandler` với `@RestControllerAdvice`.
*   Triển khai các `@ExceptionHandler` để xử lý:
    *   `MethodArgumentNotValidException` (cho lỗi validation).
    *   `ResourceNotFoundException`.
    *   `EmailAlreadyExistsException` (trả về `409 Conflict`).
    *   Một handler chung cho `Exception.class` (trả về `500 Internal Server Error`).
*   Thiết kế một DTO `ErrorResponse` chuẩn để sử dụng trong tất cả các handler.

#### Bước 5: Documentation & Testing

*   Chạy ứng dụng và truy cập `swagger-ui.html` để kiểm tra tài liệu đã được tự động sinh ra. Thêm các annotation `@Operation` để làm rõ tài liệu.
*   Tạo lớp `UserControllerTest` với `@WebMvcTest`.
*   Viết các bài test cho các kịch bản:
    *   Lấy user thành công (200).
    *   Lấy user không tồn tại (404).
    *   Tạo user thành công (201).
    *   Tạo user với email không hợp lệ (400).
    *   Xóa user thành công (204).

#### Bước 6 (Nâng cao): Logging

*   Tạo một `Servlet Filter` để thêm một `traceId` vào MDC cho mỗi request.
*   Cấu hình `logback-spring.xml` để in `traceId` này ra trong mỗi dòng log.
*   Trong `UserController`, thêm log ở đầu mỗi phương thức để ghi lại request đã được nhận.

