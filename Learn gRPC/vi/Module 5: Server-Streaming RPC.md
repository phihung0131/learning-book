### **Module 5: RPC Truyền-luồng-từ-Server (Server-Streaming RPC)**

#### **1. Giải thích**
RPC Truyền-luồng-từ-Server là một mô hình giao tiếp trong đó máy khách (client) gửi một yêu cầu (request) duy nhất đến máy chủ (server), và để đáp lại, máy chủ gửi về một chuỗi (một luồng) các thông điệp. Máy khách có thể xử lý từng thông điệp ngay khi nó đến. Máy chủ kết thúc luồng bằng cách gửi một thông báo trạng thái.

Mô hình này lý tưởng cho các tình huống mà máy chủ cần trả về một lượng lớn dữ liệu hoặc đẩy các bản cập nhật đến máy khách trong một khoảng thời gian.

**Luồng hoạt động:**
1.  Máy khách gửi một thông điệp yêu cầu duy nhất đến máy chủ.
2.  Máy chủ nhận yêu cầu và có thể bắt đầu gửi lại các thông điệp phản hồi ngay lập tức.
3.  Máy chủ gửi nhiều thông điệp phản hồi trên cùng một kết nối ngay khi chúng có sẵn.
4.  Sau khi gửi tất cả các thông điệp của mình, máy chủ gọi một phương thức đặc biệt để báo hiệu rằng luồng đã hoàn tất.
5.  Máy khách xử lý các thông điệp khi chúng đến và được thông báo khi luồng đã kết thúc.

```ascii
   Máy khách                   Máy chủ
     |                           |
     |---- Gửi Yêu cầu ---->     | (Xử lý Yêu cầu)
     | (VD: SubscribeRequest)    |
     |                           |
     |<--- Gửi Thông điệp 1 ---- |
     |                           |
     |<--- Gửi Thông điệp 2 ---- |
     |                           |
     |<--- Gửi Thông điệp 3 ---- |
     |                           |
     |   ... (và tiếp tục) ...   |
     |                           |
     |<--- Kết thúc Luồng ----   |
     |                           |
```

#### **2. Tầm quan trọng trong các hệ thống thực tế**
RPC truyền-luồng-từ-server cho phép giao tiếp hiệu quả và có độ phản hồi cao cho các ứng dụng chuyên sâu về dữ liệu hoặc thời gian thực.

*   **Thông báo & Đăng ký theo thời gian thực (Real-Time Notifications & Subscriptions)**: Một máy khách đăng ký vào một nguồn cấp tin (ví dụ: "các bình luận mới trên một bài đăng"). Máy chủ giữ kết nối mở và truyền một thông điệp mới mỗi khi có một bình luận mới được thêm vào. Điều này hiệu quả hơn nhiều so với việc máy khách liên tục gửi yêu cầu để kiểm tra cập nhật (polling) - cách tiếp cận của REST.
*   **Truy xuất tập dữ liệu lớn**: Hãy tưởng tượng một yêu cầu trả về một triệu bản ghi từ cơ sở dữ liệu. Một RPC Unary sẽ yêu cầu máy chủ tải tất cả một triệu bản ghi vào bộ nhớ, tuần tự hóa chúng thành một phản hồi khổng lồ, và gửi đi. Quá trình này chậm và tốn nhiều bộ nhớ. Với server-streaming, máy chủ có thể lấy các bản ghi theo từng lô và truyền chúng từng cái một hoặc theo từng khối nhỏ, giữ cho việc sử dụng bộ nhớ ở mức thấp trên cả máy chủ và máy khách.
*   **Báo cáo tiến độ cho các tác vụ chạy dài**: Một máy khách có thể khởi tạo một hoạt động chạy dài như một công việc phân tích dữ liệu hoặc xuất video. Máy chủ sau đó có thể truyền về các bản cập nhật tiến độ (ví dụ: `{"status": "processing", "percent_complete": 10}`, `{"status": "processing", "percent_complete": 20}`, v.v.) cho đến khi công việc hoàn thành.

#### **3. Ví dụ với Spring Boot (Java)**

**1. Định nghĩa RPC Server-Streaming trong tệp `.proto` (`src/main/proto/notification_service.proto`):**
Từ khóa `stream` trên kiểu dữ liệu *phản hồi* `Notification` cho biết đây là một RPC server-streaming.

```protobuf
syntax = "proto3";

package com.example.grpc;

option java_package = "com.example.grpc.notification";
option java_multiple_files = true;

service NotificationService {
  // Client đăng ký nhận thông báo và server truyền-luồng chúng trở lại.
  rpc Subscribe(SubscriptionRequest) returns (stream Notification);
}

message SubscriptionRequest {
  // Ví dụ: có thể chỉ định các chủ đề quan tâm
  string client_id = 1;
}

message Notification {
  string notification_id = 1;
  string message_text = 2;
  int64 timestamp = 3;
}
```

**2. Triển khai phía Máy chủ (`NotificationServiceImpl.java`):**
Phương thức triển khai sẽ lặp hoặc chờ các sự kiện, gửi một thông điệp đến máy khách cho mỗi sự kiện bằng cách sử dụng `responseObserver.onNext()`. Khi không còn thông điệp nào để gửi, nó sẽ gọi `responseObserver.onCompleted()`.

```java
package com.example.grpc.server;

import com.example.grpc.notification.Notification;
import com.example.grpc.notification.NotificationServiceGrpc;
import com.example.grpc.notification.SubscriptionRequest;
import io.grpc.stub.StreamObserver;
import net.devh.boot.grpc.server.service.GrpcService;

@GrpcService
public class NotificationServiceImpl extends NotificationServiceGrpc.NotificationServiceImplBase {

    @Override
    public void subscribe(SubscriptionRequest request, StreamObserver<Notification> responseObserver) {
        System.out.println("Server đã nhận yêu cầu đăng ký từ: " + request.getClientId());

        try {
            // Giả lập việc gửi một luồng gồm 5 thông báo
            for (int i = 1; i <= 5; i++) {
                Notification notification = Notification.newBuilder()
                        .setNotificationId("id-" + i)
                        .setMessageText("Đây là thông báo số " + i)
                        .setTimestamp(System.currentTimeMillis())
                        .build();

                // Gửi thông báo đến luồng của client
                responseObserver.onNext(notification);
                System.out.println("Server đã gửi thông báo #" + i);

                // Chờ một giây để giả lập một luồng thời gian thực
                Thread.sleep(1000);
            }
        } catch (InterruptedException e) {
            // Nếu có lỗi xảy ra, thông báo cho client
            responseObserver.onError(e);
        }

        // Sau khi gửi tất cả các thông điệp, đánh dấu luồng là đã hoàn tất.
        System.out.println("Server đã kết thúc việc truyền-luồng thông báo.");
        responseObserver.onCompleted();
    }
}
```

**3. Triển khai phía Máy khách (`NotificationClientService.java`):**
Cách đơn giản nhất để tiêu thụ một luồng từ server là với một **stub blocking**. Cuộc gọi RPC trả về một `Iterator`, nó sẽ chặn (block) tại `hasNext()` cho đến khi có một thông điệp đến hoặc luồng được đóng.

```java
package com.example.grpc.client;

import com.example.grpc.notification.Notification;
import com.example.grpc.notification.NotificationServiceGrpc;
import com.example.grpc.notification.SubscriptionRequest;
import io.grpc.StatusRuntimeException;
import net.devh.boot.grpc.client.inject.GrpcClient;
import org.springframework.stereotype.Service;
import java.util.Iterator;

@Service
public class NotificationClientService {

    @GrpcClient("notification-service")
    private NotificationServiceGrpc.NotificationServiceBlockingStub blockingStub;

    public void receiveNotifications() {
        System.out.println("Client đang đăng ký nhận thông báo...");
        SubscriptionRequest request = SubscriptionRequest.newBuilder()
                .setClientId("client-abc-123")
                .build();

        Iterator<Notification> responseIterator;
        try {
            // Lệnh gọi này khởi tạo RPC và trả về một iterator cho luồng.
            responseIterator = blockingStub.subscribe(request);

            // Lệnh gọi hasNext() sẽ chặn luồng cho đến khi một thông điệp được nhận hoặc luồng được đóng.
            while (responseIterator.hasNext()) {
                Notification notification = responseIterator.next();
                System.out.println("Client đã nhận thông báo: " + notification.getMessageText());
            }
            System.out.println("Client: Luồng đã kết thúc.");

        } catch (StatusRuntimeException e) {
            System.err.println("gRPC thất bại: " + e.getStatus());
        }
    }
}
```

#### **5. Những cạm bẫy thường gặp**
*   **Máy chủ quên gọi `onCompleted`**: Nếu máy chủ gửi xong dữ liệu nhưng không bao giờ gọi `onCompleted()`, máy khách (đặc biệt là một máy khách blocking) sẽ bị treo vô thời hạn, chờ đợi thông điệp tiếp theo. Đây là một nguyên nhân phổ biến gây rò rỉ tài nguyên.
*   **Chặn luồng gRPC trên Máy chủ**: Phần triển khai của máy chủ không nên thực hiện các hoạt động chặn, chạy lâu bên trong chính phương thức `subscribe` trước khi bắt đầu truyền-luồng. Điều này sẽ làm trì hoãn toàn bộ phản hồi. Nếu cần công việc thiết lập, nó nên được thực hiện một cách bất đồng bộ để tránh chặn luồng xử lý của gRPC.
*   **Bỏ qua việc Hủy bỏ từ Máy khách**: Một máy khách có thể quyết định hủy RPC giữa chừng trong một luồng (ví dụ: người dùng điều hướng đến một trang khác). Một máy chủ được thiết kế tốt nên phát hiện việc hủy bỏ này (gRPC cho phép điều này thông qua API context của nó) và dừng công việc của mình (ví dụ: ngừng truy vấn cơ sở dữ liệu) để tiết kiệm tài nguyên.
*   **Truyền-luồng dữ liệu không giới hạn**: Nếu máy chủ đang truyền-luồng từ một nguồn có số lượng mục rất lớn (như một bảng cơ sở dữ liệu với hàng triệu hàng), nó nên triển khai một số hình thức kiểm soát luồng (flow control) hoặc chuẩn bị cho việc máy khách hủy luồng. Chỉ đơn giản là truyền mọi thứ không có giới hạn có thể làm quá tải máy khách và mạng.

#### **6. Câu hỏi phỏng vấn**

**Q1: RPC Server-Streaming là gì và bạn có thể đưa ra một ví dụ thực tế nơi nó phù hợp hơn một RPC Unary không?**
**A:** RPC Server-Streaming là một mô hình nơi một máy khách gửi một yêu cầu duy nhất và máy chủ phản hồi bằng một luồng gồm nhiều thông điệp. Nó hoàn hảo cho một ứng dụng "bảng giá chứng khoán trực tiếp". Với một RPC Unary, máy khách sẽ phải liên tục hỏi máy chủ mỗi giây, tạo ra chi phí hoạt động rất lớn. Với server-streaming, máy khách gửi một yêu cầu `SubscribeToStock("AAPL")`, và máy chủ giữ kết nối mở, đẩy một thông điệp `StockPriceResponse` mới xuống luồng mỗi khi giá của cổ phiếu AAPL thay đổi. Điều này hiệu quả hơn nhiều và cung cấp các bản cập nhật thực sự theo thời gian thực.

**Q2: Trong một gRPC client bằng Java, sự khác biệt trong cách một stub blocking và một stub async xử lý một luồng từ server là gì?**
**A:**
*   Một **stub blocking** làm cho mọi thứ trở nên đơn giản và đồng bộ. Lời gọi đến phương thức RPC trả về một `Iterator<ResponseMessage>`. Sau đó, máy khách có thể sử dụng một vòng lặp `while(iterator.hasNext())` tiêu chuẩn. Phương thức `hasNext()` sẽ chặn luồng cho đến khi thông điệp tiếp theo đến từ máy chủ hoặc luồng được đóng.
*   Một **stub async** là non-blocking và dựa trên callback. Khi bạn khởi tạo cuộc gọi, bạn phải cung cấp một triển khai của `StreamObserver<ResponseMessage>`. Client gRPC sau đó sẽ gọi phương thức `onNext()` của observer của bạn cho mỗi thông điệp đến, `onError()` nếu có lỗi xảy ra, và `onCompleted()` khi luồng kết thúc. Điều này phù hợp cho các ứng dụng dựa trên sự kiện hoặc có giao diện người dùng (UI) nơi bạn không thể chặn luồng chính.

**Q3: Nếu một máy chủ đang truyền-luồng dữ liệu, điều gì sẽ xảy ra nếu nó gặp lỗi giữa chừng? Nó nên thông báo cho máy khách như thế nào?**
**A:** Nếu máy chủ gặp phải một lỗi nghiêm trọng sau khi đã gửi một số thông điệp, nó không nên gọi `onCompleted()`. Thay vào đó, nó phải gọi `responseObserver.onError(throwable)`. Điều này ngay lập tức chấm dứt luồng và gửi một trạng thái lỗi đến máy khách. Máy khách sẽ không nhận thêm bất kỳ thông điệp nào nữa, và `Iterator` của nó (nếu là blocking) sẽ ném ra một ngoại lệ, hoặc phương thức `StreamObserver.onError()` của nó (nếu là async) sẽ được gọi, cho phép nó xử lý sự cố một cách mượt mà.