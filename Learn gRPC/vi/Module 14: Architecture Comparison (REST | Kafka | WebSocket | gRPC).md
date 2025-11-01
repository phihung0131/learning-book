### **Module 14: So sánh về Kiến trúc (REST | Kafka | WebSocket | gRPC)**

#### **1. Giải thích**

Việc lựa chọn giao thức giao tiếp phù hợp là một quyết định kiến trúc quan trọng. Mỗi công nghệ trong bốn công nghệ này giải quyết một loại vấn đề giao tiếp khác nhau, và chúng thường được sử dụng cùng nhau trong một hệ thống lớn.

*   **gRPC**: Một framework RPC (Gọi thủ tục từ xa) hiệu năng cao. Nó được thiết kế cho giao tiếp đồng bộ, độ trễ thấp, dựa trên hợp đồng (contract-based) giữa các dịch vụ. Chế độ chính của nó là yêu cầu-phản hồi, nhưng nó cũng hỗ trợ mạnh mẽ cho việc truyền-luồng thời gian thực.
*   **REST (Representational State Transfer)**: Một kiểu kiến trúc để xây dựng các dịch vụ web, thường qua HTTP/1.1 với JSON. Nó là tiêu chuẩn thực tế cho các API công cộng và có thể được tiêu thụ bởi trình duyệt. Nó không trạng thái (stateless) và được xây dựng xung quanh mô hình yêu cầu-phản hồi dựa trên tài nguyên (danh từ) và các động từ (GET, POST, PUT, DELETE).
*   **Kafka**: Một nền tảng truyền-luồng sự kiện phân tán. Nó không phải là một hệ thống yêu cầu-phản hồi. Nó hoạt động theo mô hình publish-subscribe (công bố-đăng ký), nơi các dịch vụ (producers) công bố các thông điệp (sự kiện) đến một topic, và các dịch vụ khác (consumers) đăng ký vào topic đó để nhận các thông điệp một cách bất đồng bộ. Nó cung cấp độ bền vững, thứ tự và khả năng mở rộng mạnh mẽ cho các kiến trúc hướng sự kiện.
*   **WebSocket**: Một giao thức cung cấp một kênh giao tiếp song công toàn phần (full-duplex), bền bỉ trên một kết nối TCP duy nhất. Một khi kết nối được thiết lập, cả máy khách và máy chủ đều có thể gửi thông điệp cho nhau bất cứ lúc nào. Nó được thiết kế cho giao tiếp hai chiều, độ trễ thấp, tần suất cao, đặc biệt là giữa một trình duyệt và một máy chủ.

#### **2. Tầm quan trọng trong các hệ thống thực tế**

Việc chọn sai công cụ cho công việc sẽ dẫn đến các hệ thống phức tạp, không hiệu quả và mong manh. Việc hiểu rõ các sự đánh đổi là rất quan trọng để thiết kế một kiến trúc có khả năng mở rộng và dễ bảo trì. Ví dụ, việc sử dụng REST cho các thông báo thời gian thực dẫn đến việc polling không hiệu quả, trong khi việc sử dụng Kafka cho một yêu cầu truy xuất dữ liệu đơn giản lại quá phức tạp.

Dưới đây là một bảng phân tích so sánh:

| Đặc điểm | gRPC | REST | Kafka | WebSocket |
| :--- | :--- | :--- | :--- | :--- |
| **Kiểu Giao tiếp** | Yêu cầu-Phản hồi, Truyền-luồng hai chiều | Yêu cầu-Phản hồi | Pub/Sub Bất đồng bộ (Hướng sự kiện) | Gửi tin hai chiều, Song công toàn phần |
| **Khớp nối (Coupling)** | Khớp nối chặt chẽ (client và server phải trực tuyến và biết nhau) | Khớp nối chặt chẽ (client và server phải trực tuyến) | **Khớp nối lỏng lẻo** (producer và consumer được tách rời bởi broker; chúng không cần phải trực tuyến cùng một lúc) | Khớp nối chặt chẽ (client và server phải trực tuyến với một kết nối bền bỉ) |
| **Định dạng Payload** | Nhị phân (Protocol Buffers) | Văn bản (JSON, XML) | Nhị phân (bất kỳ định dạng nào, thường là Avro, JSON, hoặc Protobuf) | Văn bản hoặc Nhị phân (bất kỳ định dạng nào) |
| **Hợp đồng API** | **Mạnh** (định nghĩa trong tệp `.proto`, tạo mã tự động, thực thi nghiêm ngặt) | **Yếu** (định nghĩa theo quy ước, ví dụ: OpenAPI/Swagger, nhưng không được giao thức thực thi nghiêm ngặt) | **Trung bình** (Schema Registry có thể thực thi lược đồ cho các topic, ví dụ: Avro) | **Không có** (Định dạng thông điệp là mối quan tâm ở cấp ứng dụng) |
| **Trường hợp sử dụng chính**| **Giao tiếp nội bộ giữa các dịch vụ**, client di động hiệu năng cao | **API công cộng**, giao tiếp trình duyệt-máy chủ, các tích hợp đơn giản | **Các quy trình làm việc bất đồng bộ**, event sourcing, các đường ống dữ liệu, tách rời các dịch vụ | **Ứng dụng tương tác thời gian thực** (trò chuyện, bảng điều khiển trực tiếp, chỉnh sửa cộng tác) |
| **Hiệu suất** | **Rất cao** (độ trễ thấp, thông lượng cao do HTTP/2 và payload nhị phân) | **Trung bình** (độ trễ cao hơn do HTTP/1.1 và việc phân tích cú pháp JSON) | **Thông lượng rất cao** (thiết kế cho việc nhập dữ liệu khổng lồ), nhưng không dành cho các yêu cầu độ trễ thấp | **Độ trễ rất thấp** (sau khi bắt tay ban đầu, chi phí cho mỗi thông điệp là tối thiểu) |
| **Giao thức Mạng** | HTTP/2 | HTTP/1.1 (thường là vậy) | Giao thức TCP tùy chỉnh | Kết nối HTTP được nâng cấp thành một kênh TCP trực tiếp |
| **Hỗ trợ Trình duyệt** | Không (yêu cầu proxy gRPC-Web) | **Có** (hỗ trợ tự nhiên) | Không | **Có** (hỗ trợ tự nhiên) |

#### **3. Ví dụ với Spring Boot (Java) (Bối cảnh Kiến trúc)**

Đây là một module mang tính khái niệm, vì vậy các "ví dụ" tập trung vào cách các công nghệ này được tích hợp ở cấp độ kiến trúc trong một ứng dụng Spring Boot.

*   **gRPC**:
    *   **Cách làm**: Bạn sẽ sử dụng `grpc-spring-boot-starter`. Một "user-service" định nghĩa một `UserService` trong một tệp `.proto` và triển khai lớp cơ sở được tạo ra. Một "order-service" sẽ sử dụng một stub `@GrpcClient` để gọi trực tiếp và đồng bộ đến "user-service" để lấy chi tiết người dùng khi tạo một đơn hàng.
    *   **Trường hợp sử dụng**: "order-service" cần xác thực ID của một người dùng. Nó thực hiện một lệnh gọi gRPC trực tiếp, chặn, độ trễ thấp đến "user-service".

*   **REST**:
    *   **Cách làm**: Bạn sẽ sử dụng `spring-boot-starter-web`. Một `@RestController` được tạo với `@GetMapping`, `@PostMapping`, v.v. Controller này có thể là điểm vào công cộng cho toàn bộ ứng dụng của bạn, chạy trên một Cổng API.
    *   **Trường hợp sử dụng**: Một frontend dựa trên trình duyệt cần hiển thị chi tiết đơn hàng. Nó thực hiện một yêu cầu `HTTP GET` đến `https://api.myapp.com/v1/orders/{orderId}`.

*   **Kafka**:
    *   **Cách làm**: Bạn sẽ sử dụng `spring-kafka`. Khi một đơn hàng được đặt thành công trong "order-service", nó không gọi trực tiếp các dịch vụ khác. Thay vào đó, nó công bố một thông điệp `OrderPlacedEvent` đến một topic Kafka có tên là `orders`.
    *   **Trường hợp sử dụng**: "notification-service" và "shipping-service" đều đăng ký vào topic `orders`. Khi chúng nhận được `OrderPlacedEvent`, chúng phản ứng một cách độc lập và bất đồng bộ: dịch vụ thông báo gửi một email, và dịch vụ vận chuyển bắt đầu quá trình hoàn tất đơn hàng. Dịch vụ đặt hàng không hề biết về các consumer ở tầng dưới này.

*   **WebSocket**:
    *   **Cách làm**: Bạn sẽ sử dụng `spring-boot-starter-websocket`. Bạn sẽ cấu hình một `WebSocketHandler` để quản lý vòng đời của các kết nối.
    *   **Trường hợp sử dụng**: Sau khi một đơn hàng được đặt, người dùng đang xem một trang "Trạng thái Đơn hàng". Máy chủ giữ một kết nối WebSocket đến trình duyệt của người dùng. Khi dịch vụ vận chuyển xử lý đơn hàng, nó cập nhật trạng thái, và máy chủ đẩy các cập nhật thời gian thực như "Đơn hàng đã xác nhận," "Đã vận chuyển," và "Đang giao hàng" trực tiếp đến trình duyệt qua WebSocket.

#### **5. Những cạm bẫy thường gặp**
*   **Đồng bộ hóa trên nền tảng Bất đồng bộ**: Một phản-mẫu kinh điển là cố gắng ép buộc một mô hình yêu cầu-phản hồi lên trên Kafka. Ví dụ, một dịch vụ công bố một thông điệp với một topic "trả lời đến" và sau đó chặn lại, chờ đợi một phản hồi. Điều này không hiệu quả và phá vỡ mục đích của việc khớp nối lỏng lẻo. Nếu bạn cần một phản hồi đồng bộ, hãy sử dụng gRPC hoặc REST.
*   **Tư duy "REST cho mọi thứ"**: Sử dụng cơ chế polling của REST (`GET /status` mỗi 2 giây) cho các cập nhật thời gian thực. Điều này rất không hiệu quả và tạo ra lưu lượng mạng và tải máy chủ không cần thiết. WebSocket hoặc server-streaming của gRPC là các công cụ đúng đắn cho công việc này.
*   **Sử dụng gRPC cho các API công cộng**: Việc cung cấp một điểm cuối gRPC trực tiếp cho công chúng có thể gây ra vấn đề. Nó buộc tất cả người tiêu dùng của bạn phải sử dụng ngăn xếp gRPC và làm cho việc khám phá API đơn giản bằng các công cụ như `curl` trở nên không thể. Tốt hơn là nên sử dụng gRPC trong nội bộ và cung cấp một API REST/JSON thông qua một cổng API như Envoy.
*   **Bỏ qua mô hình "Broker Ngốc, Consumer Thông minh" của Kafka**: Cố gắng đặt logic định tuyến phức tạp vào chính broker Kafka. Kafka được thiết kế để là một bản ghi (log) đơn giản, bền vững. Tất cả logic nghiệp vụ thuộc về các ứng dụng consumer.

#### **6. Câu hỏi phỏng vấn**

**Q1: Bạn đang thiết kế một nền tảng thương mại điện tử. Một đơn hàng được đặt, yêu cầu xác thực tín dụng người dùng, giữ hàng trong kho và gửi email xác nhận. Bạn sẽ sử dụng gRPC hay Kafka để điều phối việc này, và tại sao?**
**A:** Tôi sẽ sử dụng một cách tiếp cận kết hợp, nhưng Kafka là ngôi sao cho việc điều phối. Yêu cầu "Đặt hàng" ban đầu có thể là một lệnh gọi gRPC hoặc REST đến một `OrderService`. Tuy nhiên, một khi đơn hàng được chấp nhận, `OrderService` nên công bố một sự kiện `OrderPlaced` đến một topic Kafka.
*   **Tại sao là Kafka?** Điều này tách rời `OrderService` khỏi các phụ thuộc ở tầng dưới của nó. `InventoryService` và `PaymentService` có thể tiêu thụ sự kiện này để thực hiện công việc của chúng một cách song song. `NotificationService` cũng tiêu thụ nó để gửi email. Đây là một quy trình làm việc bất đồng bộ, hướng sự kiện. Việc sử dụng các lệnh gọi gRPC đồng bộ cho việc này (Order -> Payment -> Inventory -> Notification) sẽ tạo ra một chuỗi chậm chạp, mong manh. Nếu `NotificationService` bị sập, toàn bộ quá trình đặt hàng sẽ thất bại, điều này là không đúng. Kafka cung cấp khả năng phục hồi và khớp nối lỏng lẻo cần thiết.

**Q2: Một người dùng đang trên một bản đồ theo dõi trực tiếp, xem vị trí của một tài xế giao hàng di chuyển trong thời gian thực. Hai công nghệ nào là phù hợp nhất cho việc này, và vai trò của chúng là gì?**
**A:** Hai công nghệ phù hợp nhất là **gRPC** và **WebSocket**.
*   **gRPC Client-Streaming**: Ứng dụng di động của tài xế sẽ sử dụng một client-stream gRPC để liên tục gửi tọa độ GPS của mình đến một `LocationService` ở backend. Điều này rất hiệu quả để gửi một luồng dữ liệu từ một thiết bị di động.
*   **WebSocket**: `LocationService`, sau khi nhận và xử lý các tọa độ, sẽ đẩy dữ liệu vị trí mới xuống trình duyệt web của người dùng qua một kết nối WebSocket. WebSocket là lý tưởng ở đây vì nó cung cấp một kênh hai chiều, độ trễ thấp trực tiếp đến trình duyệt, vốn hỗ trợ nó một cách tự nhiên.

**Q3: Công ty của bạn muốn cung cấp một API cho các nhà phát triển bên thứ ba. Tất cả các microservice nội bộ của bạn đều giao tiếp bằng gRPC. Chiến lược của bạn sẽ là gì?**
**A:** Chiến lược của tôi sẽ là **không** cung cấp gRPC trực tiếp. Tôi sẽ đặt một Cổng API (như Envoy) ở rìa mạng của chúng tôi. Cổng API này sẽ được cấu hình với tính năng chuyển mã gRPC-JSON. Điều này cho phép chúng tôi định nghĩa một API RESTful JSON tiêu chuẩn, thân thiện với người dùng cho công chúng. Khi cổng API nhận được một yêu cầu REST, nó sẽ dịch nó thành một lệnh gọi gRPC gốc đến microservice nội bộ thích hợp. Điều này cho chúng ta những điều tốt nhất của cả hai thế giới: gRPC hiệu năng cao, định kiểu mạnh cho giao tiếp nội bộ, và một API REST/JSON ổn định, dễ sử dụng cho các đối tác bên ngoài.

**Q4: Khi nào bạn sẽ chọn WebSocket thay vì streaming hai chiều của gRPC?**
**A:** Yếu tố quyết định chính là **môi trường của client**. Tôi sẽ chọn WebSocket khi client là một **trình duyệt web**. Các trình duyệt có sự hỗ trợ tự nhiên, tuyệt vời cho WebSockets, khiến nó trở thành lựa chọn mặc định cho giao tiếp song công toàn phần với một ứng dụng frontend. Streaming hai chiều của gRPC yêu cầu một proxy gRPC-Web và một thư viện client JavaScript, điều này làm tăng thêm sự phức tạp. Nếu tôi đang viết một ứng dụng từ máy chủ đến máy chủ hoặc từ ứng dụng di động gốc đến máy chủ, nơi tôi kiểm soát cả hai đầu của ngăn xếp và không có trình duyệt nào liên quan, tôi sẽ thích streaming hai chiều của gRPC hơn vì nó đi kèm với các lợi ích của Protobuf, định kiểu mạnh và kiểm soát luồng.