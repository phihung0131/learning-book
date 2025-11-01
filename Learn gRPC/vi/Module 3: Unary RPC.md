### **Module 3: Unary RPC**

#### **1. Giải thích**
Unary RPC là loại gọi thủ tục từ xa đơn giản nhất và là mô hình giao tiếp phổ biến nhất trong gRPC. Nó hoạt động giống hệt một lời gọi hàm thông thường: máy khách (client) gửi một thông điệp yêu cầu (request) duy nhất đến máy chủ (server) và đợi một thông điệp phản hồi (response) duy nhất được trả về.

Luồng hoạt động như sau:
1.  Máy khách gọi một phương thức trên client stub đã được tạo ra.
2.  Stub tuần tự hóa (serialize) thông điệp yêu cầu (sử dụng Protocol Buffers) và gửi nó đến máy chủ qua một kết nối HTTP/2.
3.  Máy chủ nhận yêu cầu, giải tuần tự hóa (deserialize) nó, và gọi phương thức tương ứng trong phần triển khai dịch vụ.
4.  Logic của máy chủ xử lý yêu cầu và xây dựng một thông điệp phản hồi.
5.  Máy chủ tuần tự hóa phản hồi và gửi lại cho máy khách dưới dạng một thông điệp duy nhất.
6.  Máy khách nhận và giải tuần tự hóa phản hồi, hoàn thành cuộc gọi.

Về mặt khái niệm, mô hình yêu cầu-phản hồi này giống hệt với một lệnh gọi REST API thông thường, chẳng hạn như một yêu cầu `GET` hoặc `POST`.

```ascii
   Máy khách                   Máy chủ
     |                           |
     |---- Gửi Yêu cầu ---->     |
     | (VD: GetUserRequest)      |
     |                           |
     |  (Đang chờ phản hồi)      | Xử lý Yêu cầu
     |                           |
     |<--- Gửi Phản hồi ----     |
     | (VD: GetUserResponse)     |
     |                           |
```

#### **2. Tầm quan trọng trong các hệ thống thực tế**
Unary RPC là nền tảng của hầu hết các thiết kế API và là xương sống cho giao tiếp giữa các microservice. Chúng được sử dụng cho đại đa số các tương tác tiêu chuẩn, truy vấn trạng thái, hoặc dựa trên lệnh.

*   **Truy xuất tài nguyên (Resource Retrieval)**: Lấy một thực thể cụ thể, như lấy hồ sơ người dùng bằng ID của họ (`GetUser`), truy xuất chi tiết sản phẩm (`GetProduct`), hoặc tra cứu số dư tài khoản (`GetBalance`).
*   **Tạo/Sửa đổi tài nguyên (Resource Creation/Modification)**: Tạo các thực thể mới, chẳng hạn như đăng ký người dùng mới (`CreateUser`) hoặc cập nhật một đơn hàng hiện có (`UpdateOrder`).
*   **Kích hoạt hành động (Action Triggers)**: Thực hiện một hành động cụ thể và trả về một xác nhận thành công/thất bại đơn giản, như `SendMessage` hoặc `ValidatePayment`.
*   **Thay thế trực tiếp cho các Endpoints REST**: Đối với bất kỳ hoạt động `GET`, `POST`, `PUT`, hoặc `DELETE` tiêu chuẩn nào trong một hệ thống RESTful, Unary RPC là phương án tương đương trực tiếp trong gRPC, nhưng với các lợi ích bổ sung về hiệu suất và kiểu dữ liệu mạnh.

#### **3. Ví dụ với Spring Boot (Java)**

**1. Định nghĩa Unary RPC trong tệp `.proto` (`src/main/proto/user_service.proto`):**
Ở đây, `GetUser` là một Unary RPC cổ điển. Nó nhận vào một `GetUserRequest` và trả về một `GetUserResponse`.

```protobuf
syntax = "proto3";

package com.example.grpc;

option java_package = "com.example.grpc.user";
option java_multiple_files = true;

// Định nghĩa dịch vụ người dùng.
service UserService {
  // Lấy thông tin người dùng bằng ID.
  rpc GetUser (GetUserRequest) returns (GetUserResponse);
}

// Thông điệp yêu cầu cho GetUser.
message GetUserRequest {
  string user_id = 1;
}

// Thông điệp phản hồi cho GetUser.
message GetUserResponse {
  string user_id = 1;
  string name = 2;
  string email = 3;
}
```

**2. Triển khai phía Máy chủ (`UserService.java`):**
Phần triển khai dịch vụ kế thừa từ lớp cơ sở do gRPC tạo ra. Đối với một Unary RPC, bạn tạo phản hồi, gọi `responseObserver.onNext()` đúng một lần, và sau đó gọi `responseObserver.onCompleted()` để kết thúc cuộc gọi.

```java
package com.example.grpc.server;

import com.example.grpc.user.GetUserRequest;
import com.example.grpc.user.GetUserResponse;
import com.example.grpc.user.UserServiceGrpc;
import io.grpc.stub.StreamObserver;
import net.devh.boot.grpc.server.service.GrpcService;

import java.util.Map;

@GrpcService
public class UserServiceImpl extends UserServiceGrpc.UserServiceImplBase {

    // Cơ sở dữ liệu giả
    private static final Map<String, String> userDatabase = Map.of(
        "1", "Alice",
        "2", "Bob"
    );

    @Override
    public void getUser(GetUserRequest request, StreamObserver<GetUserResponse> responseObserver) {
        String userId = request.getUserId();
        String userName = userDatabase.get(userId);

        if (userName == null) {
            // Xử lý trường hợp không tìm thấy người dùng
            responseObserver.onError(io.grpc.Status.NOT_FOUND
                .withDescription("Không tìm thấy người dùng với ID: " + userId)
                .asRuntimeException());
            return;
        }

        GetUserResponse response = GetUserResponse.newBuilder()
            .setUserId(userId)
            .setName(userName)
            .setEmail(userName.toLowerCase() + "@example.com")
            .build();

        // Gửi phản hồi duy nhất về cho máy khách.
        responseObserver.onNext(response);

        // Thông báo cho máy khách rằng cuộc gọi đã hoàn tất.
        responseObserver.onCompleted();
    }
}
```

**3. Triển khai phía Máy khách:**
Sử dụng `net.devh.boot.grpc.client.inject.GrpcClient`, chúng ta có thể tiêm (inject) một client stub trực tiếp vào bất kỳ Spring bean nào.

```java
package com.example.grpc.client;

import com.example.grpc.user.GetUserRequest;
import com.example.grpc.user.GetUserResponse;
import com.example.grpc.user.UserServiceGrpc;
import io.grpc.StatusRuntimeException;
import net.devh.boot.grpc.client.inject.GrpcClient;
import org.springframework.stereotype.Service;

@Service
public class UserClientService {

    // Tiêm một gRPC client stub được quản lý bởi Spring.
    // "user-service" phải khớp với tên được cấu hình trong application.properties
    @GrpcClient("user-service")
    private UserServiceGrpc.UserServiceBlockingStub blockingStub;

    public void getUser() {
        System.out.println("--- Client: Yêu cầu người dùng với ID 1 ---");
        try {
            GetUserRequest request = GetUserRequest.newBuilder()
                .setUserId("1")
                .build();
            
            // Thực hiện cuộc gọi Unary RPC blocking.
            GetUserResponse response = blockingStub.getUser(request);
            
            System.out.println("Client nhận được phản hồi: " + response);

        } catch (StatusRuntimeException e) {
            System.err.println("gRPC thất bại: " + e.getStatus());
        }

        System.out.println("\n--- Client: Yêu cầu người dùng với ID 99 (không tìm thấy) ---");
        try {
            GetUserRequest request = GetUserRequest.newBuilder()
                    .setUserId("99")
                    .build();
            blockingStub.getUser(request);
        } catch (StatusRuntimeException e) {
            System.err.println("gRPC thất bại như mong đợi: " + e.getStatus());
        }
    }
}
```
**Tệp `application.properties` cho máy khách:**
```properties
grpc.client.user-service.address=static://127.0.0.1:9090
grpc.client.user-service.negotiationType=PLAINTEXT
```

#### **5. Những cạm bẫy thường gặp**
*   **Làm tắc nghẽn Vòng lặp Sự kiện của Máy chủ (Server Event Loop)**: Ở phía máy chủ, nếu việc triển khai Unary RPC thực hiện một thao tác chặn (blocking), chạy lâu (ví dụ: một truy vấn cơ sở dữ liệu chậm hoặc gọi đến một dịch vụ chậm khác), nó có thể chiếm dụng các luồng xử lý (worker threads) của máy chủ. Điều này có thể làm suy giảm khả năng xử lý các yêu cầu đến khác của máy chủ, làm giảm thông lượng tổng thể.
*   **Xử lý lỗi không đúng cách**: Không bắt các ngoại lệ trong phần triển khai của máy chủ và chuyển đổi chúng thành các mã trạng thái gRPC phù hợp (như `NOT_FOUND`, `INVALID_ARGUMENT`, `UNAUTHENTICATED`). Nếu một ngoại lệ không được bắt xảy ra, máy khách sẽ nhận được một lỗi `UNKNOWN` hoặc `INTERNAL` chung chung và không hữu ích.
*   **Tắc nghẽn ở phía Máy khách (Client-Side Blocking)**: Sử dụng một stub blocking (`.newBlockingStub()`) trên một luồng giao diện người dùng (UI thread) hoặc một luồng quan trọng, không chặn khác trong ứng dụng máy khách sẽ khiến nó bị đóng băng cho đến khi cuộc gọi RPC hoàn tất. Đối với các ứng dụng đòi hỏi độ phản hồi cao, nên sử dụng một stub bất đồng bộ (`.newFutureStub()`).
*   **Bỏ qua Thời hạn (Deadlines)**: Máy khách nên đặt thời hạn cho các cuộc gọi của mình. Nếu không có thời hạn, một máy khách có thể đợi vô thời hạn để nhận phản hồi từ một máy chủ chậm hoặc không phản hồi, có khả năng dẫn đến cạn kiệt tài nguyên phía máy khách.

#### **6. Câu hỏi phỏng vấn**

**Q1: Unary RPC là gì và nó so sánh với một lệnh gọi REST API như thế nào?**
**A:** Unary RPC là mô hình giao tiếp gRPC cơ bản nhất, trong đó máy khách gửi một yêu cầu duy nhất và nhận về một phản hồi duy nhất, giống như một lời gọi hàm tiêu chuẩn. Về mặt chức năng, nó tương đương với một lệnh gọi REST API thông thường, như HTTP `GET` để lấy dữ liệu hoặc `POST` để tạo dữ liệu. Sự khác biệt chính nằm ở công nghệ nền tảng: Unary RPC sử dụng HTTP/2 và Protocol Buffers để có hiệu suất cao hơn và hợp đồng được định kiểu nghiêm ngặt, trong khi REST thường sử dụng HTTP/1.1 và JSON.

**Q2: Trong một máy chủ gRPC Java, chữ ký phương thức Unary RPC bao gồm một `StreamObserver`. Tại sao một "stream" observer lại được sử dụng cho một phản hồi đơn lẻ, không phải streaming?**
**A:** Đây là một lựa chọn thiết kế trong API gRPC của Java nhằm duy trì một mô hình lập trình bất đồng bộ, nhất quán trên tất cả các loại RPC (Unary, client-streaming, server-streaming, và bidirectional). Ngay cả đối với một cuộc gọi Unary, máy chủ vẫn sử dụng `StreamObserver` để gửi phản hồi của mình. Phần triển khai sẽ gọi `onNext()` một lần với thông điệp phản hồi duy nhất và sau đó ngay lập tức gọi `onCompleted()` để báo hiệu rằng RPC đã kết thúc. Giao diện thống nhất này giúp đơn giản hóa việc xử lý nội bộ của framework đối với các loại RPC khác nhau.

**Q3: Sự khác biệt giữa stub blocking và non-blocking (hoặc async) trong một gRPC client là gì? Khi nào bạn sẽ sử dụng mỗi loại?**
**A:**
*   **Stub Blocking (`.newBlockingStub()`):** Stub này cung cấp các phương thức làm chặn luồng đang gọi cho đến khi cuộc gọi RPC hoàn tất và phản hồi được nhận từ máy chủ. Lời gọi phương thức trả về trực tiếp thông điệp phản hồi. Bạn sẽ sử dụng stub blocking trong các công cụ dòng lệnh đơn giản, các luồng làm việc nền (background worker threads), hoặc trong các tình huống mà logic về bản chất là tuần tự và bạn cần kết quả trước khi tiếp tục.
*   **Stub Non-Blocking/Async (`.newFutureStub()` hoặc `.newStub()`):** Stub này trả về ngay lập tức mà không cần đợi phản hồi của máy chủ. Nó trả về một đối tượng future (ví dụ: `ListenableFuture` trong Java) hoặc yêu cầu một callback để xử lý phản hồi khi nó đến. Bạn nên luôn sử dụng stub non-blocking trong các ứng dụng nhạy cảm về hiệu suất hoặc có giao diện người dùng (UI) để tránh làm đóng băng luồng chính, và trong bất kỳ mô hình lập trình bất đồng bộ nào.