### **Module 7: Deadlines, Metadata, Status & Xử lý lỗi**

#### **1. Giải thích**

Ba thành phần này rất quan trọng để xây dựng các dịch vụ gRPC mạnh mẽ, sẵn sàng cho môi trường production. Chúng cung cấp bối cảnh và quyền kiểm soát cần thiết cho việc giao tiếp, vượt ra ngoài việc trao đổi dữ liệu nghiệp vụ đơn thuần.

*   **Deadlines (Thời hạn)**: Thời hạn cho phép một client gRPC chỉ định khoảng thời gian nó sẵn lòng chờ đợi một RPC hoàn thành. Đó là một điểm thời gian tuyệt đối. Nếu thời hạn bị vượt quá trước khi server phản hồi, RPC sẽ bị hủy ở cả phía client và server. Client nhận được lỗi `DEADLINE_EXCEEDED`, và server có thể kiểm tra việc hủy bỏ này để dừng xử lý và tiết kiệm tài nguyên. Đây là một tính năng cốt lõi để ngăn chặn các sự cố dây chuyền trong một hệ thống phân tán. Client cũng có thể chỉ định một timeout (ví dụ: "chờ trong 5 giây"), mà gRPC sẽ chuyển đổi thành một thời hạn tuyệt đối.

*   **Metadata**: Metadata là một danh sách các cặp khóa-giá trị, về mặt khái niệm tương tự như các tiêu đề HTTP (HTTP headers). Các khóa là chuỗi ký tự, và giá trị có thể là chuỗi ký tự hoặc dữ liệu nhị phân. Nó được sử dụng để gửi thông tin về chính cuộc gọi RPC, thay vì dữ liệu nghiệp vụ chứa trong thông điệp Protobuf. Các trường hợp sử dụng phổ biến bao gồm gửi mã thông báo xác thực (authentication tokens), ID theo dõi (tracing IDs) cho việc theo dõi phân tán, và thông tin định tuyến dành riêng cho client. Metadata có thể được gửi từ client (headers) và được trả về từ server (headers và trailers).

*   **Status & Xử lý lỗi**: gRPC có một khuôn khổ được định nghĩa rõ ràng để thông báo lỗi. Khi một RPC thất bại, server sẽ gửi một mã trạng thái (status code) và một thông điệp chuỗi mô tả tùy chọn. Không giống như REST, nơi bạn có thể lạm dụng các mã trạng thái HTTP, gRPC có bộ mã riêng biệt và cụ thể hơn (ví dụ: `NOT_FOUND`, `INVALID_ARGUMENT`, `UNAUTHENTICATED`, `UNAVAILABLE`). Ở phía client, một lỗi gRPC được nhận dưới dạng một `StatusRuntimeException` chứa mã trạng thái, mô tả, và bất kỳ metadata nào đi kèm (trailing metadata). Điều này cung cấp một cách xử lý lỗi có cấu trúc và không phụ thuộc vào ngôn ngữ.

```ascii
Ứng dụng Client                    Server
   |                                  |
   |---- Yêu cầu ------------------> |
   |  - Thời hạn: 5 giây            |
   |  - Metadata (Headers):          |
   |    "authorization": "jwt..."   |
   |  - Thông điệp Protobuf          |
   |                                  |
   | (Đang chờ...)                    | (Xử lý thất bại...)
   |                                  |
   |<--- Phản hồi (Lỗi) -------------|
   |  - Trạng thái: NOT_FOUND        |
   |  - Mô tả: "Không tìm thấy người dùng"|
   |  - Metadata (Trailers):         |
   |    "server-id": "srv-prod-3"    |
   |                                  |
```

#### **2. Tầm quan trọng trong các hệ thống thực tế**

*   **Khả năng phục hồi và ổn định (Deadlines)**: Trong kiến trúc microservice, một dịch vụ thường gọi một dịch vụ khác, dịch vụ này lại có thể gọi một dịch vụ khác nữa (một chuỗi gọi). Nếu một dịch vụ ở cuối chuỗi `C` bị chậm và không phản hồi, nó có thể khiến dịch vụ `B` bị treo, và đến lượt nó lại khiến `A` bị treo. Sự cố dây chuyền này có thể làm sập toàn bộ hệ thống. Bằng cách đặt thời hạn, dịch vụ `A` có thể "thất bại nhanh" và từ bỏ yêu cầu của mình đến `B` sau một khoảng thời gian chờ đã định, cho phép nó xử lý lỗi một cách mượt mà (ví dụ: trả về một phản hồi từ bộ đệm, hiển thị thông báo lỗi) thay vì trở nên không phản hồi.

*   **Khả năng quan sát và Bảo mật (Metadata)**: Các hệ thống production đòi hỏi các mối quan tâm xuyên suốt (cross-cutting concerns) phải được xử lý một cách thống nhất.
    *   **Xác thực (Authentication)**: Một client phải truyền một mã thông báo định danh (như JWT) đến server trên mỗi cuộc gọi. Metadata là cách tiêu chuẩn để làm điều này.
    *   **Theo dõi phân tán (Distributed Tracing)**: Để theo dõi một yêu cầu qua nhiều microservice, một ID theo dõi duy nhất phải được truyền từ dịch vụ này sang dịch vụ khác. Metadata được sử dụng để truyền bá bối cảnh này.
    *   **Đa người thuê (Multi-tenancy)**: Để chỉ định dữ liệu của người thuê nào mà một yêu cầu đang hướng tới, một `tenant-id` có thể được truyền trong metadata.

*   **Hành vi Client có thể dự đoán (Status & Xử lý lỗi)**: Một mô hình lỗi có cấu trúc cho phép các ứng dụng client xây dựng logic mạnh mẽ. Ví dụ:
    *   Nếu client nhận được `UNAVAILABLE`, nó biết rằng server tạm thời không hoạt động hoặc không thể truy cập và có thể thử lại yêu cầu một cách an toàn sau một khoảng thời gian ngắn.
    *   Nếu client nhận được `INVALID_ARGUMENT`, nó biết rằng yêu cầu đã bị định dạng sai, và việc thử lại chính xác yêu cầu đó sẽ lại thất bại. Client không nên thử lại mà không sửa yêu cầu.
    *   Nếu client nhận được `UNAUTHENTICATED`, nó biết rằng thông tin xác thực của mình đã hết hạn và nó cần kích hoạt một quy trình xác thực lại.

#### **3. Ví dụ với Spring Boot (Java)**

**1. `pom.xml` (không cần thêm dependency mới nếu bạn đang sử dụng `grpc-spring-boot-starter`)**

**2. Phía Client: Đặt Thời hạn và Metadata, Bắt lỗi**

Ở đây chúng ta sửa đổi `UserClientService` để minh họa cả ba khái niệm.

```java
package com.example.grpc.client;

import com.example.grpc.user.GetUserRequest;
import com.example.grpc.user.GetUserResponse;
import com.example.grpc.user.UserServiceGrpc;
import io.grpc.Metadata;
import io.grpc.Status;
import io.grpc.StatusRuntimeException;
import io.grpc.stub.MetadataUtils;
import net.devh.boot.grpc.client.inject.GrpcClient;
import org.springframework.stereotype.Service;

import java.util.concurrent.TimeUnit;

@Service
public class UserClientService {

    @GrpcClient("user-service")
    private UserServiceGrpc.UserServiceBlockingStub blockingStub;

    public void getUserWithContext() {
        System.out.println("\n--- Client: Yêu cầu người dùng với ID 1 (có thời hạn và metadata) ---");
        try {
            // 1. Chuẩn bị Metadata
            Metadata headers = new Metadata();
            headers.put(Metadata.Key.of("Authorization", Metadata.ASCII_STRING_MARSHALLER), "Bearer my-secret-jwt");
            headers.put(Metadata.Key.of("x-request-id", Metadata.ASCII_STRING_MARSHALLER), "client-req-123");

            // 2. Gắn metadata và đặt thời hạn cho stub
            UserServiceGrpc.UserServiceBlockingStub stubWithContext = MetadataUtils.attachHeaders(blockingStub, headers)
                    .withDeadlineAfter(3, TimeUnit.SECONDS);

            GetUserRequest request = GetUserRequest.newBuilder().setUserId("1").build();
            GetUserResponse response = stubWithContext.getUser(request);
            
            System.out.println("Client đã nhận phản hồi: " + response.getName());

        } catch (StatusRuntimeException e) {
            Status status = e.getStatus();
            Metadata trailers = e.getTrailers(); // Trailers là metadata được gửi cùng với lỗi
            System.err.println("gRPC thất bại với trạng thái: " + status.getCode());
            System.err.println("Mô tả: " + status.getDescription());
            if (trailers != null) {
                System.err.println("Trailers: " + trailers);
            }
        }
    }

    public void getUserThatWillTimeout() {
        System.out.println("\n--- Client: Yêu cầu người dùng với thời hạn rất ngắn (mong đợi DEADLINE_EXCEEDED) ---");
        try {
            // Đặt một thời hạn quá ngắn để server có thể phản hồi
            UserServiceGrpc.UserServiceBlockingStub stubWithDeadline = blockingStub
                    .withDeadlineAfter(50, TimeUnit.MILLISECONDS);

            GetUserRequest request = GetUserRequest.newBuilder().setUserId("timeout").build();
            stubWithDeadline.getUser(request);

        } catch (StatusRuntimeException e) {
            System.err.println("gRPC thất bại như mong đợi: " + e.getStatus().getCode());
        }
    }
}
```

**3. Phía Server: Đọc Metadata và Trả về Lỗi**

Chúng ta cần một interceptor để đọc headers và một dịch vụ đã được sửa đổi để trả về lỗi.

**Server Interceptor để đọc Metadata:**

```java
package com.example.grpc.server;

import io.grpc.*;
import net.devh.boot.grpc.server.interceptor.GrpcGlobalServerInterceptor;

@GrpcGlobalServerInterceptor
public class LogGrpcInterceptor implements ServerInterceptor {

    public static final Context.Key<String> REQUEST_ID_KEY = Context.key("requestId");
    
    @Override
    public <ReqT, RespT> ServerCall.Listener<ReqT> interceptCall(
            ServerCall<ReqT, RespT> call, Metadata headers, ServerCallHandler<ReqT, RespT> next) {

        String requestId = headers.get(Metadata.Key.of("x-request-id", Metadata.ASCII_STRING_MARSHALLER));
        if (requestId != null) {
            System.out.println("Server Interceptor: Tìm thấy x-request-id trong metadata: " + requestId);
            // Đặt nó vào gRPC context để có thể truy cập ở các bước sau
            Context context = Context.current().withValue(REQUEST_ID_KEY, requestId);
            return Contexts.interceptCall(context, call, headers, next);
        }

        System.out.println("Server Interceptor: Không tìm thấy x-request-id.");
        return next.startCall(call, headers);
    }
}
```

**`UserServiceImpl` đã được sửa đổi để xử lý lỗi và timeout:**

```java
package com.example.grpc.server;

import com.example.grpc.user.GetUserRequest;
import com.example.grpc.user.GetUserResponse;
import com.example.grpc.user.UserServiceGrpc;
import io.grpc.Context;
import io.grpc.Status;
import io.grpc.stub.StreamObserver;
import net.devh.boot.grpc.server.service.GrpcService;

@GrpcService
public class UserServiceImpl extends UserServiceGrpc.UserServiceImplBase {

    @Override
    public void getUser(GetUserRequest request, StreamObserver<GetUserResponse> responseObserver) {
        String userId = request.getUserId();

        // Trường hợp đặc biệt để giả lập một hoạt động chậm cho bài kiểm tra thời hạn
        if ("timeout".equals(userId)) {
            try {
                System.out.println("Server: Giả lập tác vụ chạy lâu trong 100ms...");
                Thread.sleep(100);
            } catch (InterruptedException e) {
                Thread.currentThread().interrupt();
            }
        }
        
        // Kiểm tra xem client đã hủy yêu cầu chưa (ví dụ: hết thời hạn)
        if (Context.current().isCancelled()) {
            System.out.println("Server: Client đã hủy yêu cầu. Không cần tiếp tục.");
            responseObserver.onError(Status.CANCELLED
                    .withDescription("Yêu cầu đã bị hủy bởi client")
                    .asRuntimeException());
            return;
        }

        // Xử lý không tìm thấy người dùng
        if (!"1".equals(userId)) {
            responseObserver.onError(Status.NOT_FOUND
                .withDescription("Không tìm thấy người dùng với ID: " + userId)
                .asRuntimeException());
            return;
        }

        GetUserResponse response = GetUserResponse.newBuilder()
            .setUserId(userId)
            .setName("Alice")
            .setEmail("alice@example.com")
            .build();

        responseObserver.onNext(response);
        responseObserver.onCompleted();
    }
}
```

#### **5. Những cạm bẫy thường gặp**
*   **Không Đặt Thời hạn**: Cạm bẫy phổ biến nhất là client không đặt thời hạn. Điều này nguy hiểm và có thể dẫn đến cạn kiệt tài nguyên và sự cố dây chuyền. Một thực hành tốt nhất là luôn đặt một thời hạn mặc định hợp lý.
*   **Ném ra Ngoại lệ Thô**: Trong phần triển khai của server, việc ném ra một `java.lang.RuntimeException` tiêu chuẩn hoặc một ngoại lệ đã được kiểm tra (checked exception) sẽ khiến client nhận được một lỗi `UNKNOWN` hoặc `INTERNAL` chung chung. Luôn bắt các ngoại lệ nội bộ và ánh xạ chúng đến một `io.grpc.Status` cụ thể để cung cấp các lỗi có ý nghĩa cho client.
*   **Sử dụng Metadata cho Dữ liệu Logic Nghiệp vụ**: Metadata nên dành cho các mối quan tâm xuyên suốt. Đừng truyền một thứ gì đó như `product_id` trong metadata nếu nó là một tham số cốt lõi cho RPC. Các tham số cốt lõi thuộc về phần thân của thông điệp Protobuf để trở thành một phần của hợp đồng API chính thức.
*   **Bỏ qua `Context.isCancelled()`**: Nếu một RPC tốn nhiều tài nguyên tính toán, server nên định kỳ kiểm tra `Context.current().isCancelled()`. Nếu nó trả về true, server nên hủy bỏ công việc của mình để tránh lãng phí chu kỳ CPU cho một kết quả sẽ không bao giờ được sử dụng.

#### **6. Câu hỏi phỏng vấn**

**Q1: gRPC Deadline là gì và tại sao nó lại quan trọng trong kiến trúc microservices?**
**A:** Deadline là một giới hạn thời gian do client chỉ định cho một RPC. Nó rất quan trọng vì nó ngăn chặn các sự cố dây chuyền. Trong một chuỗi các lệnh gọi dịch vụ (A -> B -> C), nếu C bị chậm, một deadline được đặt bởi A sẽ đảm bảo nó sẽ từ bỏ sau một khoảng thời gian nhất định thay vì chờ đợi vô thời hạn. Điều này giữ cho dịch vụ A luôn phản hồi và ngăn chặn một dịch vụ chậm duy nhất làm sập toàn bộ hệ thống. Đó là một mẫu hình cơ bản để xây dựng các hệ thống phân tán có khả năng phục hồi, cấp độ production.

**Q2: Bạn sẽ truyền một mã thông báo xác thực từ client đến server trong gRPC như thế nào?**
**A:** Cách tiêu chuẩn là sử dụng Metadata. Ở phía client, tôi sẽ tạo một đối tượng `Metadata`, thêm mã thông báo dưới dạng một cặp khóa-giá trị (ví dụ: khóa: `"Authorization"`, giá trị: `"Bearer <token>"`), và gắn nó vào cuộc gọi RPC đi. Ở phía server, tôi sẽ triển khai một `ServerInterceptor` để đọc khóa "Authorization" từ metadata của các yêu cầu đến, xác thực mã thông báo, và nếu nó không hợp lệ, từ chối cuộc gọi với trạng thái `UNAUTHENTICATED`.

**Q3: So sánh việc xử lý lỗi của gRPC với REST (mã trạng thái HTTP). Ưu điểm của cách tiếp cận của gRPC là gì?**
**A:** REST sử dụng các mã trạng thái HTTP (như 404 cho Not Found, 400 cho Bad Request). Mặc dù có chức năng, chúng có thể mơ hồ. gRPC có một bộ mã trạng thái chi tiết và được tiêu chuẩn hóa hơn, được thiết kế đặc biệt cho RPCs. Các ưu điểm là:
1.  **Tính cụ thể**: gRPC có các mã như `UNAVAILABLE` (gợi ý rằng việc thử lại là an toàn) và `FAILED_PRECONDITION` (một lỗi liên quan đến trạng thái), mô tả rõ ràng hơn so với các mã HTTP chung chung.
2.  **Tính nhất quán**: Ý nghĩa của mỗi mã được định nghĩa rõ ràng trong hệ sinh thái gRPC, bất kể ngôn ngữ lập trình nào.
3.  **Tải trọng phong phú**: Một trạng thái lỗi gRPC không chỉ bao gồm một mã mà còn có một chuỗi mô tả bắt buộc và các chi tiết lỗi tùy chọn thông qua trailing metadata, cung cấp nhiều bối cảnh hơn cho bên gọi.

**Q4: Sự khác biệt giữa mã trạng thái `CANCELLED` và `DEADLINE_EXCEEDED` là gì?**
**A:** Cả hai đều chỉ ra rằng RPC đã không hoàn thành, nhưng chúng có nguyên nhân khác nhau.
*   `DEADLINE_EXCEEDED` là một sự hủy bỏ tự động, dựa trên thời gian. Client đặt một thời hạn, và nếu thời gian đó trôi qua trước khi nhận được phản hồi, hệ thống sẽ tự động hủy RPC và trả về trạng thái này.
*   `CANCELLED` chỉ ra một sự hủy bỏ tường minh. Điều này có thể xảy ra vì nhiều lý do, ví dụ, ứng dụng client đã hủy bỏ cuộc gọi một cách rõ ràng trước khi hết thời hạn, hoặc một proxy/bộ cân bằng tải ở giữa đã chấm dứt kết nối. Server cũng có thể nhận được trạng thái này nếu nó kiểm tra context sau khi client đã từ bỏ vì bất kỳ lý do gì.