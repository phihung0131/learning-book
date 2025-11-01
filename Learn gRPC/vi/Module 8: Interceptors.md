### **Module 8: Interceptors (Bộ đánh chặn)**

#### **1. Giải thích**
gRPC Interceptors là một cơ chế cốt lõi để quan sát và sửa đổi các cuộc gọi gRPC. Chúng cung cấp một cách để chặn các RPC đến (phía máy chủ) hoặc đi (phía máy khách) và áp dụng logic chung, xuyên suốt (cross-cutting logic). Về mặt khái niệm, chúng rất giống với middleware trong các framework web (như Express hoặc ASP.NET Core) hoặc Lập trình hướng khía cạnh (Aspect-Oriented Programming - AOP).

*   **`ServerInterceptor`**: Chặn các RPC đến trên máy chủ trước khi chúng đến phần triển khai dịch vụ thực tế. Khi một máy khách thực hiện một cuộc gọi, yêu cầu sẽ đi qua một chuỗi các interceptor trước khi logic nghiệp vụ được thực thi. Điều này cho phép bạn thực hiện các hành động cho *tất cả* hoặc *một số* phương thức dịch vụ của mình ở một nơi tập trung.

*   **`ClientInterceptor`**: Chặn các RPC đi trên máy khách trước khi chúng được gửi qua mạng. Điều này cho phép bạn thao tác các yêu cầu trước khi chúng rời khỏi ứng dụng máy khách.

Các Interceptor có thể được kết nối thành một chuỗi. Thứ tự thực thi rất quan trọng; ví dụ, một interceptor ghi log có thể là lớp ngoài cùng để đo thời gian toàn bộ yêu cầu, trong khi một interceptor xác thực sẽ ở bên trong nó để xác thực thông tin đăng nhập.

```ascii
// Luồng Chặn phía Máy chủ

Yêu cầu Client --> [Interceptor 1] --> [Interceptor 2] --> [Logic Dịch vụ của bạn]
                                                                  |
Phản hồi Client <-- [Interceptor 1] <-- [Interceptor 2] <----------+
```

#### **2. Tầm quan trọng trong các hệ thống thực tế**
Interceptors là nền tảng để xây dựng các ứng dụng gRPC sạch sẽ, dễ bảo trì và sẵn sàng cho môi trường production. Chúng cho phép bạn tách biệt các mối quan tâm xuyên suốt (cross-cutting concerns) khỏi logic nghiệp vụ cốt lõi, giữ cho các phần triển khai dịch vụ của bạn gọn gàng và tập trung.

*   **Xác thực & Ủy quyền (Authentication & Authorization)**: Trường hợp sử dụng phổ biến nhất. Một server interceptor có thể trích xuất một mã thông báo xác thực từ metadata của yêu cầu, xác thực nó, và kiểm tra quyền hạn. Nếu mã thông báo không hợp lệ, interceptor có thể từ chối cuộc gọi ngay lập tức với trạng thái `UNAUTHENTICATED` trước khi nó chạm đến mã dịch vụ của bạn.
*   **Khả năng quan sát (Logging & Metrics)**: Một interceptor có thể ghi log mọi yêu cầu đến và kết quả của nó (thành công hay thất bại). Nó cũng có thể đo thời gian của mỗi cuộc gọi và xuất các số liệu đến một hệ thống giám sát (như Prometheus), giúp bạn có cái nhìn sâu sắc về tình trạng và hiệu suất của các dịch vụ.
*   **Theo dõi phân tán (Distributed Tracing)**: Interceptors là nơi hoàn hảo để xử lý bối cảnh theo dõi. Một server interceptor có thể trích xuất một ID theo dõi từ metadata của một cuộc gọi đến, và một client interceptor có thể tiêm ID theo dõi đó vào bất kỳ cuộc gọi đi nào sau đó, cho phép bạn theo dõi một yêu cầu duy nhất khi nó đi qua nhiều microservice.
*   **Xác thực Yêu cầu (Request Validation)**: Một server interceptor có thể thực hiện xác thực ban đầu, phổ quát trên các yêu cầu đến, chẳng hạn như kiểm tra các tiêu đề metadata bắt buộc hoặc xác thực các trường thông điệp chung, và từ chối các yêu cầu không hợp lệ sớm.
*   **Xử lý & Chuẩn hóa lỗi (Error Handling & Normalization)**: Một interceptor có thể bắt các ngoại lệ được ném ra bởi logic dịch vụ và chuyển đổi chúng thành các phản hồi lỗi gRPC được tiêu chuẩn hóa.

#### **3. Ví dụ với Spring Boot (Java)**

`grpc-spring-boot-starter` giúp việc tạo và đăng ký các interceptor toàn cục trở nên dễ dàng.

**1. Phía Máy chủ: Một Interceptor Ghi log Mạnh mẽ**

Interceptor này sẽ ghi log thời điểm bắt đầu một cuộc gọi, kết thúc, thời gian thực hiện, và trạng thái cuối cùng.

```java
package com.example.grpc.server;

import io.grpc.*;
import io.grpc.ForwardingServerCall.SimpleForwardingServerCall;
import net.devh.boot.grpc.server.interceptor.GrpcGlobalServerInterceptor;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.core.annotation.Order;

@GrpcGlobalServerInterceptor
@Order(10) // Thiết lập thứ tự thực thi cho các interceptor
public class LoggingServerInterceptor implements ServerInterceptor {

    private static final Logger log = LoggerFactory.getLogger(LoggingServerInterceptor.class);

    @Override
    public <ReqT, RespT> ServerCall.Listener<ReqT> interceptCall(
            ServerCall<ReqT, RespT> call,
            Metadata headers,
            ServerCallHandler<ReqT, RespT> next) {

        final long startTime = System.nanoTime();
        String methodName = call.getMethodDescriptor().getFullMethodName();

        log.info("RPC bắt đầu: {}", methodName);

        // Bọc cuộc gọi ban đầu để chặn các sự kiện của nó, như việc đóng
        ServerCall<ReqT, RespT> wrappedCall = new SimpleForwardingServerCall<>(call) {
            @Override
            public void close(Status status, Metadata trailers) {
                long durationNanos = System.nanoTime() - startTime;
                long durationMillis = durationNanos / 1_000_000;

                if (status.isOk()) {
                    log.info("RPC kết thúc thành công: {}, Thời gian: {}ms", methodName, durationMillis);
                } else {
                    log.error("RPC thất bại: {}, Trạng thái: {}, Mô tả: {}, Thời gian: {}ms",
                            methodName, status.getCode(), status.getDescription(), durationMillis);
                }
                super.close(status, trailers);
            }
        };

        // Tiếp tục chuỗi gọi, nhưng với cuộc gọi đã được bọc của chúng ta
        return next.startCall(wrappedCall, headers);
    }
}
```

**2. Phía Máy khách: Một Interceptor để Thêm ID Yêu cầu**

Interceptor này sẽ tự động thêm một tiêu đề `x-request-id` vào mọi RPC đi, điều này rất cần thiết cho khả năng truy vết.

**Đầu tiên, bản thân interceptor:**
```java
package com.example.grpc.client;

import io.grpc.*;
import java.util.UUID;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class ClientRequestIdInterceptor implements ClientInterceptor {

    private static final Logger log = LoggerFactory.getLogger(ClientRequestIdInterceptor.class);
    public static final Metadata.Key<String> REQUEST_ID_KEY =
            Metadata.Key.of("x-request-id", Metadata.ASCII_STRING_MARSHALLER);

    @Override
    public <ReqT, RespT> ClientCall<ReqT, RespT> interceptCall(
            MethodDescriptor<ReqT, RespT> method,
            CallOptions callOptions,
            Channel next) {
        
        return new ForwardingClientCall.SimpleForwardingClientCall<>(next.newCall(method, callOptions)) {
            @Override
            public void start(Listener<RespT> responseListener, Metadata headers) {
                String requestId = UUID.randomUUID().toString();
                log.info("Đính kèm ID yêu cầu vào cuộc gọi đi: {}", requestId);
                headers.put(REQUEST_ID_KEY, requestId);
                super.start(responseListener, headers);
            }
        };
    }
}
```

**Thứ hai, cấu hình Spring Boot để áp dụng nó toàn cục:**
Chúng ta tạo một bean cấu hình để thêm client interceptor của chúng ta vào danh sách toàn cục.

```java
package com.example.grpc.client;

import net.devh.boot.grpc.client.interceptor.GlobalClientInterceptorConfigurer;
import org.springframework.context.annotation.Configuration;

@Configuration
public class GrpcClientConfig {

    @Bean
    public GlobalClientInterceptorConfigurer globalClientInterceptorConfigurer() {
        return registry -> registry.add(new ClientRequestIdInterceptor());
    }
}
```

Giờ đây, mọi cuộc gọi được thực hiện bằng một stub được tiêm `@GrpcClient` sẽ tự động có tiêu đề `x-request-id`.

#### **5. Những cạm bẫy thường gặp**
*   **Thứ tự Interceptor**: Thứ tự áp dụng các interceptor là rất quan trọng. Một interceptor xác thực phải chạy trước logic nghiệp vụ, và một interceptor ghi log có thể cần phải bao bọc tất cả những cái khác để đo tổng thời gian. Trong Spring, bạn có thể sử dụng chú thích `@Order` để kiểm soát điều này.
*   **Chi phí Hiệu suất**: Interceptors làm tăng độ trễ. Mặc dù thường không đáng kể, một interceptor thực hiện các hoạt động chậm (như một lệnh gọi cơ sở dữ liệu hoặc một yêu cầu mạng) có thể trở thành một nút thắt cổ chai lớn. Hãy giữ cho logic của interceptor nhanh và không chặn.
*   **Quản lý Trạng thái**: Lý tưởng nhất, interceptor nên không có trạng thái (stateless). Lưu trữ trạng thái cho từng RPC bên trong một thể hiện interceptor là nguy hiểm vì cùng một thể hiện được chia sẻ giữa nhiều cuộc gọi đồng thời. Hãy sử dụng `io.grpc.Context` để truyền trạng thái theo vòng đời của một RPC duy nhất.
*   **Chặn trong một Interceptor**: Không bao giờ chặn trong một interceptor. Máy chủ gRPC sử dụng một số lượng nhỏ các luồng để xử lý nhiều yêu cầu. Việc chặn một trong những luồng này có thể nhanh chóng làm cạn kiệt năng lực của máy chủ.
*   **Quên Ủy quyền (Delegate)**: Lỗi phổ biến nhất là quên gọi `next.startCall(...)` trong một server interceptor hoặc `next.newCall(...)` trong một client interceptor. Điều này sẽ phá vỡ chuỗi và RPC sẽ không bao giờ được xử lý.

#### **6. Câu hỏi phỏng vấn**

**Q1: gRPC Interceptor là gì và mục đích chính của nó là gì?**
**A:** gRPC Interceptor là một cơ chế middleware cho phép bạn chặn và xử lý các cuộc gọi RPC trước khi chúng được điều phối đến logic dịch vụ (trên máy chủ) hoặc được gửi qua mạng (trên máy khách). Mục đích chính của nó là để triển khai các mối quan tâm xuyên suốt—chẳng hạn như ghi log, xác thực, số liệu, và theo dõi yêu cầu—tại một nơi tập trung, giữ cho logic dịch vụ cốt lõi sạch sẽ và tách rời khỏi các tác vụ cơ sở hạ tầng này.

**Q2: Bạn cần triển khai xác thực cho tất cả các dịch vụ gRPC của mình. Bạn sẽ làm điều đó như thế nào mà không đặt logic xác thực vào mỗi phương thức dịch vụ?**
**A:** Tôi sẽ sử dụng một `ServerInterceptor`. Tôi sẽ tạo một interceptor toàn cục chạy cho mỗi cuộc gọi đến. Bên trong interceptor này, tôi sẽ:
1.  Trích xuất mã thông báo xác thực từ `Metadata` của yêu cầu (ví dụ: từ tiêu đề "Authorization").
2.  Xác thực mã thông báo (ví dụ: kiểm tra chữ ký và ngày hết hạn).
3.  Nếu mã thông báo không hợp lệ hoặc thiếu, tôi sẽ ngay lập tức chấm dứt cuộc gọi bằng cách trả về trạng thái `UNAUTHENTICATED`. Phương thức dịch vụ sẽ không bao giờ được thực thi.
4.  Nếu mã thông báo hợp lệ, tôi sẽ tiếp tục chuỗi gọi bằng cách gọi `next.startCall()`, cho phép yêu cầu tiếp tục đến phần triển khai dịch vụ thực tế.

**Q3: Trong một server interceptor, làm thế nào để bạn đo tổng thời gian của một RPC và ghi log trạng thái cuối cùng của nó? Tại sao bạn không thể chỉ sử dụng một khối try-finally đơn giản xung quanh `next.startCall()`?**
**A:** Bạn không thể sử dụng một khối `try-finally` đơn giản vì các cuộc gọi gRPC, đặc biệt là các cuộc gọi streaming, là bất đồng bộ. Phương thức `next.startCall()` trả về ngay lập tức và không chặn cho đến khi RPC kết thúc. Để đo thời gian và lấy trạng thái cuối cùng một cách chính xác, bạn phải bọc đối tượng `ServerCall`. Bằng cách tạo một `ForwardingServerCall`, bạn có thể ghi đè phương thức `close()` của nó. Phương thức `close()` này được đảm bảo sẽ được gọi đúng một lần vào cuối cùng của RPC, dù nó thành công hay thất bại. Bên trong phương thức được ghi đè này, bạn có thể tính toán thời gian từ một thời điểm bắt đầu đã được ghi lại trước đó và kiểm tra `Status` cuối cùng để ghi log kết quả một cách chính xác.

**Q4: Làm thế nào bạn có thể tự động thêm một ID yêu cầu duy nhất vào mọi cuộc gọi client đi cho mục đích theo dõi?**
**A:** Cách tốt nhất là sử dụng một `ClientInterceptor`. Tôi sẽ tạo một interceptor mà, trong phương thức `interceptCall` của nó, sẽ bọc `ClientCall`. Trong phương thức `start()` được ghi đè của `ForwardingClientCall`, tôi sẽ tạo một ID duy nhất (như UUID), thêm nó vào đối tượng `Metadata` dưới một khóa như `x-request-id`, và sau đó gọi phương thức `start()` ban đầu để tiếp tục. Bằng cách đăng ký nó như một interceptor client toàn cục, nó sẽ được tự động áp dụng cho mọi RPC được thực hiện bởi ứng dụng client.