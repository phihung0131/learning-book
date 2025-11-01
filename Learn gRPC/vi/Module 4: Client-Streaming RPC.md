### **Module 4: RPC Truyền-luồng-từ-Client (Client-Streaming RPC)**

#### **1. Giải thích**
RPC Truyền-luồng-từ-Client là một mô hình giao tiếp trong đó máy khách (client) gửi một chuỗi (một luồng) các thông điệp đến máy chủ (server). Máy chủ xử lý luồng thông điệp này ngay khi chúng đến. Một khi máy khách đã gửi xong tất cả các thông điệp của mình, nó sẽ báo hiệu cho máy chủ biết rằng luồng đã kết thúc. Sau đó, máy chủ thực hiện các tính toán cuối cùng và gửi lại *một phản hồi duy nhất* cho máy khách.

Đây là mô hình ngược lại với server-streaming. Nó rất hữu ích cho việc gửi một lượng lớn dữ liệu từ máy khách đến máy chủ mà không cần phải đệm (buffer) toàn bộ dữ liệu vào bộ nhớ trước.

**Luồng hoạt động:**
1.  Máy khách khởi tạo cuộc gọi RPC. Máy chủ xác nhận và chuẩn bị nhận một luồng thông điệp.
2.  Máy khách gửi nhiều thông điệp trên cùng một kết nối, lần lượt từng cái một.
3.  Trình xử lý (handler) của máy chủ được gọi cho mỗi thông điệp nó nhận được, cho phép nó xử lý dữ liệu một cách tăng dần.
4.  Sau khi gửi tất cả các thông điệp, máy khách gọi một phương thức đặc biệt để báo hiệu rằng luồng đã hoàn tất.
5.  Logic "khi hoàn thành" (on-completion) của máy chủ được kích hoạt, nơi nó hoàn tất công việc của mình và gửi một phản hồi tóm tắt duy nhất trở lại cho máy khách.
6.  Máy khách nhận phản hồi duy nhất đó, hoàn thành cuộc gọi.

```ascii
   Máy khách                   Máy chủ
     |                           |
     |---- Khởi tạo cuộc gọi --> |
     |                           |
     |---- Gửi thông điệp 1 ---> | (Xử lý thông điệp 1)
     |                           |
     |---- Gửi thông điệp 2 ---> | (Xử lý thông điệp 2)
     |                           |
     |---- Gửi thông điệp 3 ---> | (Xử lý thông điệp 3)
     |                           |
     |---- Kết thúc luồng ---->  |
     |                           | (Hoàn tất công việc)
     |  (Đang chờ phản hồi)      |
     |                           |
     |<--- Gửi Phản hồi   ----    |
     |   (Tóm tắt duy nhất)      |
     |                           |
```

#### **2. Tầm quan trọng trong các hệ thống thực tế**
Client-streaming cực kỳ hiệu quả cho các kịch bản liên quan đến việc truyền dữ liệu lớn từ máy khách đến máy chủ hoặc các chuỗi sự kiện.

*   **Tải lên tệp/dữ liệu lớn**: Một máy khách có thể truyền-luồng một tệp video lớn, một kho lưu trữ sao lưu, hoặc một tệp log khổng lồ đến một dịch vụ lưu trữ. Dữ liệu được gửi theo từng khối (messages), điều này hiệu quả hơn nhiều về mặt bộ nhớ so với việc đọc toàn bộ tệp vào bộ nhớ trước khi gửi.
*   **Số liệu thời gian thực và Ghi log sự kiện**: Một ứng dụng client có thể mở một luồng đến dịch vụ giám sát và gửi các số liệu hiệu suất hoặc sự kiện (ví dụ: lượt nhấp của người dùng, lỗi) ngay khi chúng xảy ra. Máy chủ có thể tổng hợp các số liệu này trong thời gian thực và, một khi luồng được đóng lại (ví dụ: ứng dụng tắt), trả về một bản tóm tắt cuối cùng.
*   **Nhập dữ liệu hàng loạt (Bulk Data Ingestion)**: Nhập một số lượng lớn bản ghi vào cơ sở dữ liệu. Một máy khách có thể truyền-luồng hàng nghìn thông điệp `CreateUserRequest` đến máy chủ, và máy chủ có thể xử lý chúng trong một giao dịch duy nhất, hiệu quả, trả về một `ImportSummaryResponse` duy nhất ở cuối. Điều này hiệu quả hơn nhiều so với việc thực hiện hàng nghìn cuộc gọi Unary RPC riêng lẻ.

#### **3. Ví dụ với Spring Boot (Java)**

**1. Định nghĩa RPC Client-Streaming trong tệp `.proto` (`src/main/proto/metrics_service.proto`):**
Từ khóa `stream` trên kiểu dữ liệu yêu cầu `RecordMetricsRequest` cho biết đây là một RPC client-streaming.

```protobuf
syntax = "proto3";

package com.example.grpc;

option java_package = "com.example.grpc.metrics";
option java_multiple_files = true;

service MetricsService {
  // Client gửi một luồng các số liệu, server phản hồi bằng một bản tóm tắt.
  rpc RecordMetrics(stream RecordMetricsRequest) returns (RecordMetricsSummary);
}

message RecordMetricsRequest {
  string metric_name = 1;
  double value = 2;
  int64 timestamp = 3;
}

message RecordMetricsSummary {
  int32 received_metrics_count = 1;
  string status_message = 2;
}
```

**2. Triển khai phía Máy chủ (`MetricsServiceImpl.java`):**
Phương thức triển khai không nhận vào một đối tượng request. Thay vào đó, nó **trả về** một `StreamObserver<RecordMetricsRequest>`. Observer được trả về này là một đối tượng định nghĩa các hàm callback để xử lý luồng dữ liệu đến từ máy khách.

```java
package com.example.grpc.server;

import com.example.grpc.metrics.MetricsServiceGrpc;
import com.example.grpc.metrics.RecordMetricsRequest;
import com.example.grpc.metrics.RecordMetricsSummary;
import io.grpc.stub.StreamObserver;
import net.devh.boot.grpc.server.service.GrpcService;
import java.util.concurrent.atomic.AtomicInteger;

@GrpcService
public class MetricsServiceImpl extends MetricsServiceGrpc.MetricsServiceImplBase {

    @Override
    public StreamObserver<RecordMetricsRequest> recordMetrics(StreamObserver<RecordMetricsSummary> responseObserver) {
        // Trả về một StreamObserver mới để xử lý luồng đến từ client.
        return new StreamObserver<>() {
            private final AtomicInteger metricCount = new AtomicInteger(0);

            // Được gọi cho mỗi thông điệp trong luồng của client.
            @Override
            public void onNext(RecordMetricsRequest request) {
                System.out.println("Server đã nhận số liệu: " + request.getMetricName() + " = " + request.getValue());
                // Trong hệ thống thực tế, bạn sẽ tổng hợp hoặc lưu trữ dữ liệu này.
                metricCount.incrementAndGet();
            }

            // Được gọi nếu luồng của client có lỗi.
            @Override
            public void onError(Throwable t) {
                System.err.println("Lỗi từ luồng client: " + t.getMessage());
            }

            // Được gọi khi client kết thúc việc gửi thông điệp.
            // Đây là nơi chúng ta gửi phản hồi duy nhất của mình trở lại.
            @Override
            public void onCompleted() {
                System.out.println("Client đã kết thúc truyền-luồng. Hoàn tất tóm tắt.");
                RecordMetricsSummary summary = RecordMetricsSummary.newBuilder()
                    .setReceivedMetricsCount(metricCount.get())
                    .setStatusMessage("Đã nhận các số liệu thành công.")
                    .build();
                
                // Gửi phản hồi duy nhất.
                responseObserver.onNext(summary);
                // Và hoàn thành cuộc gọi.
                responseObserver.onCompleted();
            }
        };
    }
}
```

**3. Triển khai phía Máy khách (`MetricsClientService.java`):**
Máy khách phải sử dụng một **stub bất đồng bộ (asynchronous stub)**. Nó khởi tạo cuộc gọi bằng cách cung cấp một `StreamObserver` để xử lý phản hồi cuối cùng từ máy chủ. Cuộc gọi này trả về một `StreamObserver` *mới* mà sau đó máy khách sử dụng để gửi luồng thông điệp của mình đến máy chủ.

```java
package com.example.grpc.client;

import com.example.grpc.metrics.MetricsServiceGrpc;
import com.example.grpc.metrics.RecordMetricsRequest;
import com.example.grpc.metrics.RecordMetricsSummary;
import io.grpc.stub.StreamObserver;
import net.devh.boot.grpc.client.inject.GrpcClient;
import org.springframework.stereotype.Service;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;

@Service
public class MetricsClientService {

    @GrpcClient("metrics-service")
    private MetricsServiceGrpc.MetricsServiceStub asyncStub;

    public void streamMetrics() throws InterruptedException {
        // Sử dụng một Latch để đợi phản hồi từ server.
        final CountDownLatch finishLatch = new CountDownLatch(1);

        // 1. Tạo một response observer để xử lý phản hồi tóm tắt duy nhất của server.
        StreamObserver<RecordMetricsSummary> responseObserver = new StreamObserver<>() {
            @Override
            public void onNext(RecordMetricsSummary summary) {
                System.out.println("Client đã nhận tóm tắt từ server: " + summary);
            }

            @Override
            public void onError(Throwable t) {
                System.err.println("Truyền-luồng số liệu thất bại: " + t.getMessage());
                finishLatch.countDown();
            }

            @Override
            public void onCompleted() {
                System.out.println("Client: Server đã xử lý luồng.");
                finishLatch.countDown();
            }
        };

        // 2. Khởi tạo cuộc gọi RPC. Lệnh này trả về một request observer mà chúng ta dùng để gửi thông điệp.
        StreamObserver<RecordMetricsRequest> requestObserver = asyncStub.recordMetrics(responseObserver);

        try {
            // 3. Gửi một luồng thông điệp đến server.
            for (int i = 0; i < 5; i++) {
                RecordMetricsRequest request = RecordMetricsRequest.newBuilder()
                    .setMetricName("cpu_usage")
                    .setValue(Math.random())
                    .setTimestamp(System.currentTimeMillis())
                    .build();
                
                System.out.println("Client đang gửi số liệu: " + request.getValue());
                requestObserver.onNext(request);
                Thread.sleep(500); // Giả lập việc truyền-luồng theo thời gian
            }
        } catch (RuntimeException e) {
            requestObserver.onError(e);
            throw e;
        }

        // 4. Đánh dấu kết thúc luồng.
        System.out.println("Client đã gửi xong số liệu.");
        requestObserver.onCompleted();

        // Chờ server gửi phản hồi. Đặt timeout là một thói quen tốt.
        if (!finishLatch.await(10, TimeUnit.SECONDS)) {
            System.err.println("Client hết thời gian chờ tóm tắt từ server.");
        }
    }
}
```

#### **5. Những cạm bẫy thường gặp**
*   **Máy chủ phản hồi quá sớm**: Một lỗi phổ biến là máy chủ cố gắng gọi `responseObserver.onNext()` từ bên trong trình xử lý `onNext()` của chính nó. Máy chủ chỉ được phép gửi phản hồi duy nhất của mình sau khi máy khách đã kết thúc luồng—logic này phải nằm trong các trình xử lý `onCompleted()` hoặc `onError()` của máy chủ.
*   **Máy khách quên gọi `onCompleted`**: Nếu máy khách gửi tất cả thông điệp nhưng không bao giờ gọi `onCompleted()` trên luồng yêu cầu, máy chủ sẽ không bao giờ biết luồng đã kết thúc. Điều này sẽ khiến cuộc gọi RPC bị treo, tiêu tốn tài nguyên trên cả máy khách và máy chủ cho đến khi hết thời gian chờ (deadline hoặc timeout).
*   **Rò rỉ bộ nhớ trên Máy chủ**: Nếu phần triển khai của máy chủ ngây thơ đệm tất cả các thông điệp đến từ máy khách vào một danh sách, một máy khách độc hại hoặc bị lỗi có thể gửi một luồng dữ liệu khổng lồ, khiến máy chủ hết bộ nhớ. Máy chủ nên được thiết kế để xử lý dữ liệu một cách tăng dần bất cứ khi nào có thể, thay vì lưu trữ tất cả.
*   **Sử dụng Stub Blocking**: Không thể khởi tạo RPC client-streaming bằng stub blocking. Bản chất của cuộc gọi là bất đồng bộ, đòi hỏi một stub non-blocking và một cách tiếp cận dựa trên callback (`StreamObserver`) để xử lý phản hồi của máy chủ.

#### **6. Câu hỏi phỏng vấn**

**Q1: Mô tả một kịch bản mà RPC Client-Streaming sẽ là một lựa chọn tốt hơn so với Unary RPC.**
**A:** Một kịch bản hoàn hảo là tải lên một tệp lớn (ví dụ: một video vài gigabyte). Sử dụng Unary RPC sẽ yêu cầu máy khách tải toàn bộ tệp vào bộ nhớ dưới dạng một mảng byte, điều này không hiệu quả và có thể thất bại đối với các tệp rất lớn. Với RPC Client-Streaming, máy khách có thể đọc tệp theo từng khối nhỏ và gửi mỗi khối như một thông điệp riêng biệt trên luồng. Điều này giữ cho việc sử dụng bộ nhớ ở mức thấp và cho phép máy chủ bắt đầu xử lý dữ liệu (ví dụ: ghi ra đĩa) ngay khi nó đến, làm cho toàn bộ quá trình hiệu quả và có khả năng mở rộng hơn nhiều.

**Q2: Trong phần triển khai phía máy chủ bằng Java cho một luồng từ client, phương thức của bạn trả về một `StreamObserver`. Mục đích của đối tượng này là gì và các phương thức chính của nó là gì?**
**A:** `StreamObserver` được trả về hoạt động như một bộ các hàm callback mà framework gRPC gọi khi nó nhận dữ liệu từ luồng của máy khách. Nó cho phép máy chủ định nghĩa cách phản ứng với các thông điệp đến một cách bất đồng bộ. Ba phương thức chính của nó là:
*   `onNext(Request value)`: Được gọi mỗi khi có một thông điệp mới đến từ máy khách. Đây là nơi chứa logic xử lý hoặc tổng hợp chính cho mỗi thông điệp.
*   `onError(Throwable t)`: Được gọi nếu có lỗi xảy ra trên luồng (ví dụ: máy khách ngắt kết nối đột ngột). Đây là nơi nên thực hiện logic dọn dẹp.
*   `onCompleted()`: Được gọi khi máy khách đã gửi thành công tất cả các thông điệp và đóng luồng. Đây là nơi duy nhất mà máy chủ nên gửi phản hồi duy nhất của mình trở lại cho máy khách.

**Q3: Điều gì xảy ra trên máy chủ nếu kết nối mạng của máy khách bị rớt giữa chừng trong một RPC client-streaming?**
**A:** Nếu kết nối bị rớt, lớp vận chuyển gRPC trên máy chủ sẽ phát hiện ra lỗi. Sau đó, nó sẽ kích hoạt phương thức `onError()` trên thể hiện `StreamObserver` mà phần triển khai của máy chủ đã trả về. `Throwable` được truyền vào `onError` sẽ chứa thông tin về lỗi, thường là một `StatusRuntimeException` với trạng thái như `CANCELLED` hoặc `UNAVAILABLE`. Điều này cho phép máy chủ xử lý một cách mượt mà luồng chưa hoàn chỉnh và dọn dẹp bất kỳ tài nguyên hoặc trạng thái nào mà nó có thể đã tích lũy.