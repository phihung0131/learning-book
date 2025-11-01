### **Module 10: gRPC với Cổng API (Envoy)**

#### **1. Giải thích**

Mặc dù gRPC rất tuyệt vời cho giao tiếp nội bộ, từ dịch vụ này đến dịch vụ khác, bạn thường cần phải cung cấp dịch vụ của mình cho các client bên ngoài không thể nói chuyện bằng gRPC một cách tự nhiên. Ví dụ phổ biến nhất là một trình duyệt web. Trình duyệt không thể thực hiện các yêu cầu gRPC HTTP/2 thô do những hạn chế trong API của trình duyệt.

Một **Cổng API (API Gateway)** là một máy chủ hoạt động như một điểm vào duy nhất cho các dịch vụ backend của bạn. Trong bối cảnh gRPC, nó nằm ở rìa mạng của bạn và thực hiện một số chức năng quan trọng:
1.  **Chuyển đổi Giao thức (Protocol Translation)**: Nó có thể chuyển đổi các giao thức khác nhau thành gRPC. Hai bản dịch quan trọng nhất là:
    *   **gRPC-Web**: Nó chuyển đổi các yêu cầu từ trình duyệt (sử dụng giao thức gRPC-Web, là một lớp bọc trên HTTP/1.1 hoặc HTTP/2) thành các yêu cầu gRPC tiêu chuẩn mà các dịch vụ backend hiểu được.
    *   **Chuyển mã gRPC-JSON (gRPC-JSON Transcoding)**: Nó chuyển đổi các yêu cầu RESTful JSON tiêu chuẩn (ví dụ: `HTTP GET /v1/users/123`) thành lệnh gọi Unary RPC gRPC tương ứng (ví dụ: `GetUser({user_id: "123"})`). Điều này cho phép bạn duy trì một backend gRPC duy nhất trong khi vẫn cung cấp một API REST truyền thống cho các client cần nó.
2.  **Định tuyến Yêu cầu (Request Routing)**: Nó định tuyến các yêu cầu đến đến microservice backend chính xác dựa trên đường dẫn URL hoặc tên phương thức gRPC.
3.  **Chức năng ở Rìa mạng (Edge Functions)**: Nó xử lý các mối quan tâm xuyên suốt như chấm dứt TLS, xác thực, giới hạn tốc độ (rate limiting), và khả năng quan sát (số liệu, log) tại rìa mạng.

**Envoy Proxy** là một proxy rìa và dịch vụ mã nguồn mở, hiệu năng cao được thiết kế cho các ứng dụng cloud-native. Nó là tiêu chuẩn thực tế cho các cổng API gRPC do sự hỗ trợ hàng đầu cho HTTP/2, gRPC, gRPC-Web, và chuyển mã gRPC-JSON.

```ascii
                               +----------------------------------+
                               |        Cổng API (Envoy)          |
                               |                                  |
Trình duyệt -- (gRPC-Web) ----> | [Bộ lọc gRPC-Web] ---+            |
                               |                       |            |
                               |                       +---> [Router] ---> Backend gRPC
                               |                       |            |
Client REST -- (JSON/HTTP) --> | [Bộ lọc Chuyển mã] --+            |
                               |                                  |
                               +----------------------------------+
```

#### **2. Tầm quan trọng trong các hệ thống thực tế**

*   **Cung cấp Dịch vụ cho Trình duyệt**: Đây là động lực chính. Nếu không có một cổng API chuyển đổi gRPC-Web, một ứng dụng frontend chạy trong trình duyệt đơn giản là không thể giao tiếp với một backend gRPC. Một Cổng API là cây cầu bắt buộc.
*   **Giao diện API Thống nhất**: Nó cho phép bạn cung cấp một điểm cuối API duy nhất, nhất quán cho công chúng, ngay cả khi backend của bạn bao gồm hàng chục microservice. Cổng API xử lý sự phức tạp trong việc định tuyến, che giấu kiến trúc nội bộ khỏi các client bên ngoài.
*   **Hỗ trợ các Client cũ và API REST**: Nhiều tổ chức không thể chuyển đổi tất cả các client sang gRPC cùng một lúc. Bằng cách sử dụng chuyển mã gRPC-JSON, một đội ngũ phát triển có thể xây dựng một dịch vụ mới bằng gRPC nhưng vẫn cung cấp một API JSON RESTful quen thuộc cho các client cũ hoặc cho các trường hợp sử dụng mà REST phù hợp hơn (ví dụ: các API công cộng đơn giản). Điều này cung cấp một lộ trình di chuyển suôn sẻ.
*   **Kiểm soát Tập trung ở Rìa mạng**: Các chính sách bảo mật và vận hành (như xác thực, giới hạn tốc độ, và mTLS) có thể được thực thi ở cấp cổng API. Điều này hiệu quả và an toàn hơn nhiều so với việc cố gắng triển khai các chính sách này trong mỗi microservice. Các nhà phát triển dịch vụ có thể tập trung hoàn toàn vào logic nghiệp vụ.

#### **3. Ví dụ với Spring Boot (Java)**

Đây là một chủ đề về cơ sở hạ tầng, vì vậy ví dụ không nằm trong mã nguồn Java mà là trong cấu hình của tệp `.proto` và tệp `envoy.yaml`. Máy chủ gRPC Spring Boot của bạn **không yêu cầu thay đổi nào** để hoạt động phía sau cổng API.

**1. Chú thích tệp `.proto` cho việc Chuyển mã gRPC-JSON**

Để báo cho Envoy cách ánh xạ một yêu cầu HTTP đến một RPC gRPC, bạn phải thêm các chú thích từ `http.proto` của Google.

**`user_service.proto` (đã sửa đổi):**
```protobuf
syntax = "proto3";

package com.example.grpc.user;

// Nhập các chú thích của Google
import "google/api/annotations.proto";

option java_package = "com.example.grpc.user";
option java_multiple_files = true;

service UserService {
  rpc GetUser (GetUserRequest) returns (GetUserResponse) {
    // Chú thích này báo cho bộ chuyển mã rằng một yêu cầu HTTP GET
    // đến một đường dẫn như /v1/users/some_id nên được ánh xạ đến RPC này.
    // Phần {user_id} của đường dẫn được ánh xạ vào trường user_id.
    option (google.api.http) = {
      get: "/v1/users/{user_id}"
    };
  }
}

message GetUserRequest {
  string user_id = 1;
}

message GetUserResponse {
  string user_id = 1;
  string name = 2;
  string email = 3;
}
```

**2. Cấu hình Envoy (`envoy.yaml`)**

Đây là một cấu hình Envoy đơn giản hóa, thiết lập một bộ lắng nghe trên cổng `8080` và áp dụng hai bộ lọc: một cho việc chuyển mã và một cho gRPC-Web. Sau đó, nó định tuyến cuộc gọi gRPC kết quả đến dịch vụ backend của bạn đang chạy trên cổng `9090`.

```yaml
static_resources:
  listeners:
  - name: listener_0
    address:
      socket_address: { address: 0.0.0.0, port_value: 8080 }
    filter_chains:
    - filters:
      - name: envoy.filters.network.http_connection_manager
        typed_config:
          "@type": type.googleapis.com/envoy.extensions.filters.network.http_connection_manager.v3.HttpConnectionManager
          stat_prefix: ingress_http
          http_filters:
          # Bộ lọc này kích hoạt hỗ trợ gRPC-Web. Nó phải đứng trước bộ chuyển mã và router.
          - name: envoy.filters.http.grpc_web
            typed_config: {}
          # Bộ lọc này kích hoạt chuyển mã gRPC-JSON.
          - name: envoy.filters.http.grpc_json_transcoder
            typed_config:
              "@type": type.googleapis.com/envoy.extensions.filters.http.grpc_json_transcoder.v3.GrpcJsonTranscoder
              proto_descriptor: "/etc/envoy/user_descriptor.pb" # Đường dẫn đến tập mô tả proto đã biên dịch
              services: ["com.example.grpc.user.UserService"] # Các dịch vụ gRPC để chuyển mã
              print_options:
                always_print_primitive_fields: true
          # Bộ lọc router là cuối cùng và gửi yêu cầu đến cụm backend.
          - name: envoy.filters.http.router
            typed_config: {}
          route_config:
            name: local_route
            virtual_hosts:
            - name: local_service
              domains: ["*"]
              routes:
              - match: { prefix: "/" }
                route:
                  # Tất cả lưu lượng được định tuyến đến cụm 'grpc_backend_service'.
                  cluster: grpc_backend_service
                  # Đặt thời gian chờ cho cuộc gọi đến upstream.
                  timeout: 3s

  clusters:
  - name: grpc_backend_service
    connect_timeout: 0.25s
    type: LOGICAL_DNS
    # Quan trọng: Cụm backend phải được cấu hình cho HTTP/2.
    typed_extension_protocol_options:
      envoy.extensions.upstreams.http.v3.HttpProtocolOptions:
        "@type": "type.googleapis.com/envoy.extensions.upstreams.http.v3.HttpProtocolOptions"
        explicit_http_config:
          http2_protocol_options: {}
    lb_policy: ROUND_ROBIN
    load_assignment:
      cluster_name: grpc_backend_service
      endpoints:
      - lb_endpoints:
        - endpoint:
            address:
              # Đây là địa chỉ của máy chủ gRPC Spring Boot của bạn.
              # Sử dụng 'host.docker.internal' nếu chạy Envoy trong Docker trên Docker Desktop.
              socket_address: { address: host.docker.internal, port_value: 9090 }
```
**Để làm cho điều này hoạt động, bạn cũng cần tạo một "tập mô tả proto" từ tệp `.proto` của mình:**
```shell
protoc --include_imports --proto_path=src/main/proto \
  --descriptor_set_out=user_descriptor.pb \
  user_service.proto google/api/annotations.proto http.proto
```
Tệp `user_descriptor.pb` này chứa lược đồ đã được biên dịch mà Envoy cần để hiểu API của bạn và thực hiện việc chuyển mã.

#### **5. Những cạm bẫy thường gặp**
*   **Cấu hình Envoy Phức tạp**: Cấu hình của Envoy cực kỳ mạnh mẽ nhưng cũng nổi tiếng là phức tạp và dài dòng. Một lỗi cú pháp nhỏ hoặc cấu hình sai có thể rất khó để gỡ lỗi.
*   **Quản lý Mô tả Proto**: Đối với việc chuyển mã gRPC-JSON, Envoy cần quyền truy cập vào tệp mô tả proto đã được biên dịch. Việc giữ cho tệp này đồng bộ với các định nghĩa `.proto` của bạn khi API phát triển là một tác vụ CI/CD quan trọng thường bị bỏ qua.
*   **Vấn đề về Content-Type của gRPC-Web**: Một vấn đề phổ biến là cấu hình sai `content-type` cho các yêu cầu gRPC-Web. Bộ lọc gRPC-Web trong Envoy xử lý việc này, nhưng các proxy tùy chỉnh hoặc các client bị cấu hình sai có thể gây ra sự cố.
*   **Chi phí Hiệu suất**: Mặc dù Envoy có hiệu suất rất cao, nó vẫn là một bước nhảy mạng (network hop) bổ sung. Điều này thêm một lượng nhỏ độ trễ vào mỗi yêu cầu so với một cuộc gọi gRPC trực tiếp. Đối với các dịch vụ nội bộ hiệu năng cao, bạn vẫn nên sử dụng giao tiếp gRPC-trực-tiếp-gRPC và chỉ sử dụng cổng API cho lưu lượng truy cập bên ngoài.
*   **Dịch Thông báo Lỗi**: Khi dịch vụ gRPC của bạn trả về một lỗi cụ thể (ví dụ: `NOT_FOUND`), cổng API cần được cấu hình để dịch nó thành mã trạng thái HTTP thích hợp (ví dụ: `404 Not Found`). Việc ánh xạ này là tiêu chuẩn nhưng có thể bị mất trong một cổng API bị cấu hình sai.

#### **6. Câu hỏi phỏng vấn**

**Q1: Tại sao một trình duyệt web không thể gọi trực tiếp một dịch vụ gRPC tiêu chuẩn, và giải pháp là gì?**
**A:** Một trình duyệt không thể thực hiện các cuộc gọi gRPC tiêu chuẩn vì nó không cung cấp các quyền kiểm soát HTTP/2 ở mức độ thấp cần thiết, chẳng hạn như thao tác các trailers (phần dữ liệu cuối), vốn rất cần thiết cho việc báo cáo trạng thái của gRPC. Giải pháp là **gRPC-Web**. Client trên trình duyệt sử dụng giao thức gRPC-Web, tương thích với các API của trình duyệt. Yêu cầu này được gửi đến một proxy (như Envoy), proxy này sẽ dịch yêu cầu gRPC-Web thành một yêu cầu gRPC tiêu chuẩn và chuyển tiếp nó đến dịch vụ backend.

**Q2: Cổng API trong bối cảnh gRPC là gì, và ba trách nhiệm chính của nó là gì?**
**A:** Một Cổng API là một proxy trung gian hoạt động như một điểm vào duy nhất cho tất cả lưu lượng truy cập bên ngoài đến các dịch vụ gRPC của bạn. Ba trách nhiệm chính của nó là:
1.  **Chuyển đổi Giao thức**: Chuyển đổi các giao thức thân thiện với client như gRPC-Web hoặc REST/JSON thành gRPC tiêu chuẩn cho backend.
2.  **Định tuyến**: Hướng các yêu cầu đến đến microservice hạ nguồn chính xác dựa trên phương thức hoặc đường dẫn của yêu cầu.
3.  **Áp dụng Chính sách ở Rìa mạng**: Quản lý tập trung các mối quan tâm xuyên suốt như chấm dứt TLS, xác thực, giới hạn tốc độ, và khả năng quan sát.

**Q3: Giải thích về chuyển mã gRPC-JSON. Khi nào và tại sao bạn lại sử dụng nó?**
**A:** Chuyển mã gRPC-JSON là quá trình tự động chuyển đổi một yêu cầu RESTful JSON/HTTP tiêu chuẩn thành một yêu cầu gRPC. Bạn kích hoạt nó bằng cách chú thích các phương thức dịch vụ của mình trong tệp `.proto` để ánh xạ các động từ HTTP và đường dẫn URL đến các RPC gRPC. Bạn sẽ sử dụng nó khi muốn hỗ trợ các client mong đợi một API REST truyền thống (như các hệ thống cũ, các nhà phát triển bên thứ ba, hoặc các kịch bản đơn giản) mà không cần phải viết và duy trì một máy chủ API REST riêng biệt. Nó cho phép bạn có một dịch vụ gRPC-native duy nhất làm nguồn chân lý (source of truth) trong khi vẫn phục vụ một giao diện JSON.

**Q4: Envoy là gì, và tại sao nó là một lựa chọn phổ biến cho một Cổng API gRPC?**
**A:** Envoy là một proxy dịch vụ mã nguồn mở, hiệu năng cao được thiết kế cho các ứng dụng cloud-native. Nó là một lựa chọn phổ biến cho cổng API gRPC vì nó được xây dựng với gRPC ngay từ đầu và có sự hỗ trợ tự nhiên, hàng đầu cho:
*   Proxying HTTP/2 và gRPC.
*   Bộ lọc giao thức gRPC-Web.
*   Bộ lọc chuyển mã gRPC-JSON.
*   Các khả năng cân bằng tải và khám phá dịch vụ nâng cao, vốn rất cần thiết cho các kiến trúc microservice.