### **Module 6: RPC Truyền-luồng-hai-chiều (Bidirectional Streaming RPC)**

#### **1. Giải thích**
RPC Truyền-luồng-hai-chiều (hay "Bidi-streaming") là mô hình linh hoạt nhất trong bốn loại hình giao tiếp của gRPC. Trong chế độ này, cả máy khách (client) và máy chủ (server) đều có thể độc lập gửi một luồng thông điệp cho nhau trên một kết nối gRPC duy nhất và tồn tại lâu dài.

Đặc điểm chính là các luồng của máy khách và máy chủ là độc lập. Không có thứ tự quy định nào; máy khách có thể gửi thông điệp mà không cần chờ phản hồi từ máy chủ, và máy chủ có thể gửi thông điệp mà không cần chờ máy khách. Chúng có thể hoạt động theo kiểu song công toàn phần (full-duplex). Cuộc gọi được khởi tạo bởi máy khách và được chấm dứt khi cả hai bên đã gửi xong thông điệp của mình.

**Luồng hoạt động:**
1.  Máy khách khởi tạo cuộc gọi. Cả máy khách và máy chủ đều nhận được một đối tượng luồng để đọc và ghi dữ liệu.
2.  Máy khách có thể bắt đầu gửi thông điệp đến máy chủ.
3.  Đồng thời, máy chủ cũng có thể bắt đầu gửi thông điệp đến máy khách.
4.  Cuộc hội thoại hai chiều này tiếp tục cho đến khi cần thiết.
5.  Cuối cùng, một bên (ví dụ: máy khách) quyết định kết thúc việc gửi thông điệp và ra tín hiệu `onCompleted()`. Bên kia sẽ được thông báo nhưng vẫn có thể tiếp tục gửi thông điệp của mình.
6.  Cuộc gọi chỉ chính thức chấm dứt khi cả hai bên đã hoàn thành luồng của mình.

```ascii
   Máy khách                   Máy chủ
     |                           |
     |---- Khởi tạo Cuộc gọi --> | (Kết nối được thiết lập)
     |                           |
     |---- Thông điệp Client 1 -->| (Xử lý Thông điệp 1)
     |                           |
     |<--- Thông điệp Server 1 --|
     |                           |
     |<--- Thông điệp Server 2 --|
     |                           |
     |---- Thông điệp Client 2 -->| (Xử lý Thông điệp 2)
     |                           |
     |---- Client Hoàn tất ----> | (Client đã gửi xong)
     |                           |
     |<--- Thông điệp Server 3 --|
     |                           |
     |<--- Server Hoàn tất ----- | (Server đã gửi xong, cuộc gọi kết thúc)
     |                           |
```

#### **2. Tầm quan trọng trong các hệ thống thực tế**
Bidi-streaming cho phép thực hiện các kịch bản tương tác, thời gian thực, phức tạp mà sẽ rất khó để triển khai hiệu quả bằng các giao thức khác.

*   **Ứng dụng trò chuyện thời gian thực (Real-Time Chat)**: Một người dùng (máy khách) gửi một luồng các tin nhắn trò chuyện. Máy chủ nhận chúng và phát (broadcast) đến những người dùng khác. Đồng thời, máy chủ có thể truyền-luồng các tin nhắn đến từ những người dùng khác trở lại cho máy khách này. Mỗi người tham gia trong một phòng trò chuyện sẽ có một RPC bidi-streaming đang mở.
*   **Game tương tác nhiều người chơi**: Một client game truyền-luồng các hành động của người chơi (di chuyển, bắn) đến máy chủ. Máy chủ xử lý các hành động này, cập nhật trạng thái trò chơi, và truyền-luồng trạng thái mới (vị trí của những người chơi khác, các sự kiện trong game) trở lại cho tất cả các client trong thời gian thực.
*   **Bảng trắng/Chỉnh sửa tài liệu cộng tác**: Nhiều máy khách có thể truyền-luồng các lệnh vẽ hoặc các chỉnh sửa văn bản của họ đến một máy chủ trung tâm. Máy chủ sau đó xử lý các sự kiện này và truyền-luồng trạng thái tài liệu/bảng vẽ đã được cập nhật trở lại cho tất cả các máy khách đang kết nối.
*   **Điều khiển và Chỉ huy IoT phức tạp**: Một thiết bị IoT có thể truyền-luồng dữ liệu đo từ xa (telemetry) đến một máy chủ, trong khi máy chủ có thể, bất cứ lúc nào, gửi các lệnh trở lại xuống cùng luồng đó cho thiết bị (ví dụ: "khởi động lại", "cập nhật firmware", "thay đổi tần số cảm biến").

#### **3. Ví dụ với Spring Boot (Java)**

**1. Định nghĩa RPC Bidirectional Streaming trong tệp `.proto` (`src/main/proto/chat_service.proto`):**
Từ khóa `stream` có mặt trên *cả* kiểu dữ liệu yêu cầu và phản hồi.

```protobuf
syntax = "proto3";

package com.example.grpc;

option java_package = "com.example.grpc.chat";
option java_multiple_files = true;

service ChatService {
  // Một luồng hai chiều cho cuộc trò chuyện thời gian thực.
  rpc Chat(stream ChatMessage) returns (stream ChatMessage);
}

message ChatMessage {
  string user_id = 1;
  string text = 2;
  int64 timestamp = 3;
}
```

**2. Triển khai phía Máy chủ (`ChatServiceImpl.java`):**
Giống như trong client-streaming, phương thức triển khai của máy chủ trả về một `StreamObserver` để xử lý luồng đến từ máy khách. Bên trong observer này, nó sử dụng `responseObserver` của chính nó để gửi thông điệp trở lại cho máy khách. Ở đây, chúng ta sẽ tạo một máy chủ "echo" đơn giản, đồng thời thêm một tiền tố vào tin nhắn.

```java
package com.example.grpc.server;

import com.example.grpc.chat.ChatMessage;
import com.example.grpc.chat.ChatServiceGrpc;
import io.grpc.stub.StreamObserver;
import net.devh.boot.grpc.server.service.GrpcService;

@GrpcService
public class ChatServiceImpl extends ChatServiceGrpc.ChatServiceImplBase {

    @Override
    public StreamObserver<ChatMessage> chat(StreamObserver<ChatMessage> responseObserver) {
        // Trả về một StreamObserver để xử lý các thông điệp từ client.
        return new StreamObserver<>() {

            // Được gọi mỗi khi nhận được một thông điệp từ client.
            @Override
            public void onNext(ChatMessage clientMessage) {
                System.out.println("Server đã nhận tin nhắn từ " + clientMessage.getUserId() +
                                   ": " + clientMessage.getText());

                // Tạo một thông điệp phản hồi để gửi lại cho client.
                ChatMessage serverResponse = ChatMessage.newBuilder()
                        .setUserId("Server")
                        .setText("Phản hồi tin nhắn của bạn: " + clientMessage.getText())
                        .setTimestamp(System.currentTimeMillis())
                        .build();

                // Gửi phản hồi trở lại cho client.
                responseObserver.onNext(serverResponse);
            }

            // Được gọi nếu luồng của client có lỗi.
            @Override
            public void onError(Throwable t) {
                System.err.println("Lỗi luồng trò chuyện: " + t.getMessage());
            }

            // Được gọi khi client đã gửi xong thông điệp.
            @Override
            public void onCompleted() {
                System.out.println("Client đã đóng luồng trò chuyện.");
                // Server bây giờ có thể đóng phía luồng của mình.
                responseObserver.onCompleted();
            }
        };
    }
}
```

**3. Triển khai phía Máy khách (`ChatClientService.java`):**
Máy khách phải sử dụng một stub async. Việc triển khai đối xứng với của máy chủ. Máy khách khởi tạo cuộc gọi, cung cấp một `StreamObserver` để xử lý các thông điệp đến từ máy chủ. Cuộc gọi trả về một `StreamObserver` mà máy khách sử dụng để gửi các thông điệp của chính mình.

```java
package com.example.grpc.client;

import com.example.grpc.chat.ChatMessage;
import com.example.grpc.chat.ChatServiceGrpc;
import io.grpc.stub.StreamObserver;
import net.devh.boot.grpc.client.inject.GrpcClient;
import org.springframework.stereotype.Service;

import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;

@Service
public class ChatClientService {

    @GrpcClient("chat-service")
    private ChatServiceGrpc.ChatServiceStub asyncStub;

    public void startChat() throws InterruptedException {
        final CountDownLatch finishLatch = new CountDownLatch(1);

        StreamObserver<ChatMessage> requestObserver = asyncStub.chat(new StreamObserver<>() {
            // Observer để xử lý các thông điệp đến TỪ server
            @Override
            public void onNext(ChatMessage serverMessage) {
                System.out.println("Client đã nhận tin nhắn từ " + serverMessage.getUserId() +
                                   ": " + serverMessage.getText());
            }

            @Override
            public void onError(Throwable t) {
                System.err.println("Trò chuyện thất bại: " + t.getMessage());
                finishLatch.countDown();
            }

            @Override
            public void onCompleted() {
                System.out.println("Server đã đóng luồng.");
                finishLatch.countDown();
            }
        });

        try {
            // Gửi một vài thông điệp ĐẾN server
            for (int i = 1; i <= 3; i++) {
                ChatMessage clientMessage = ChatMessage.newBuilder()
                        .setUserId("JavaClient")
                        .setText("Xin chào, đây là tin nhắn #" + i)
                        .build();
                System.out.println("Client đang gửi tin nhắn: " + clientMessage.getText());
                requestObserver.onNext(clientMessage);
                Thread.sleep(1000);
            }
        } catch (RuntimeException e) {
            requestObserver.onError(e);
            throw e;
        }

        // Đánh dấu kết thúc luồng của client
        System.out.println("Client đã gửi xong tin nhắn.");
        requestObserver.onCompleted();

        // Chờ server đóng luồng của nó.
        if (!finishLatch.await(10, TimeUnit.SECONDS)) {
            System.err.println("Hết thời gian chờ phản hồi từ server.");
        }
    }
}
```

#### **5. Những cạm bẫy thường gặp**
*   **Điều kiện tranh đua và các vấn đề về đồng thời (Race Conditions and Concurrency Issues)**: Vì cả máy khách và máy chủ đều có thể gửi và nhận bất cứ lúc nào, mã nguồn về bản chất là đa luồng. Bạn phải cẩn thận khi truy cập trạng thái được chia sẻ bên trong các trình xử lý `StreamObserver`. Ví dụ, nếu một máy chủ đang quản lý nhiều client trò chuyện, nó cần các cấu trúc dữ liệu an toàn với luồng (thread-safe) để xử lý việc phát tin nhắn.
*   **Bế tắc (Deadlocks)**: Một cách triển khai ngây thơ có thể dẫn đến bế tắc. Ví dụ, nếu máy khách đợi một thông điệp cụ thể từ máy chủ trước khi gửi thông điệp tiếp theo của mình, và máy chủ lại đợi thông điệp đó của máy khách trước khi nó gửi phản hồi, thì cả hai bên sẽ không tiến triển được. Các luồng phải được coi là độc lập.
*   **Luồng vô hạn**: Nếu cả máy khách và máy chủ đều không có logic để cuối cùng đóng luồng (`onCompleted`), kết nối có thể vẫn mở vô thời hạn, tiêu tốn tài nguyên. Luôn phải có một điều kiện kết thúc rõ ràng (ví dụ: người dùng đăng xuất, một tin nhắn "exit" được gửi, hoặc một khoảng thời gian không hoạt động được phát hiện).
*   **Thiếu kiểm soát luồng (Flow Control)**: Một bên gửi nhanh có thể làm quá tải một bên nhận chậm. Mặc dù lớp vận chuyển HTTP/2 của gRPC có cơ chế kiểm soát luồng riêng, việc kiểm soát luồng ở cấp ứng dụng vẫn có thể cần thiết. Ví dụ, một máy khách không nên truyền-luồng hàng gigabyte dữ liệu nếu máy chủ đã chỉ ra rằng nó chưa sẵn sàng để xử lý.

#### **6. Câu hỏi phỏng vấn**

**Q1: Bidirectional Streaming là gì và nó khác biệt cơ bản như thế nào so với hai RPC client-streaming và server-streaming riêng biệt?**
**A:** Bidirectional streaming cho phép cả máy khách và máy chủ gửi các luồng thông điệp độc lập cho nhau trên một kết nối gRPC duy nhất. Sự khác biệt cơ bản là nó là một kênh giao tiếp song công toàn phần (full-duplex) duy nhất. Hai RPC riêng biệt sẽ yêu cầu hai kết nối riêng biệt và sẽ không được đồng bộ hóa. Một luồng bidi được thiết lập bằng một cuộc gọi RPC duy nhất, hiệu quả hơn về tài nguyên mạng (một kết nối), và có độ trễ thấp hơn cho giao tiếp tương tác qua lại.

**Q2: Mô tả vòng đời của một RPC bidirectional streaming. Khi nào thì cuộc gọi được coi là hoàn tất?**
**A:** Vòng đời bắt đầu khi máy khách gọi RPC, thiết lập kết nối. Tại thời điểm này, cả máy khách và máy chủ đều nhận được các đối tượng ghi luồng để gửi thông điệp và sẵn sàng nhận thông điệp từ phía bên kia. Sau đó, họ có thể gửi thông điệp bất cứ lúc nào. Máy khách có thể báo hiệu rằng mình đã gửi xong bằng cách gọi `onCompleted()`. Máy chủ sẽ được thông báo thông qua callback `onCompleted()` của nó nhưng vẫn có thể gửi thông điệp trở lại cho máy khách. Điều tương tự cũng áp dụng nếu máy chủ kết thúc trước. Toàn bộ cuộc gọi RPC chỉ được coi là hoàn tất hoàn toàn khi *cả* máy khách và máy chủ đều đã gọi `onCompleted()` trên các luồng tương ứng của họ.

**Q3: Giả sử bạn đang xây dựng một ứng dụng trò chuyện. Ở phía máy chủ, bạn sẽ xử lý các thông điệp từ luồng đến của một máy khách và phát chúng đến các máy khách khác như thế nào? Bạn sẽ phải đối mặt với những thách thức về đồng thời nào?**
**A:** Trong phương thức `chat` của máy chủ, đối với mỗi máy khách được kết nối, tôi sẽ nhận được một `StreamObserver` cho các thông điệp đến (`requestObserver`) và một `StreamObserver` để gửi thông điệp đi (`responseObserver`). Tôi sẽ cần một cấu trúc dữ liệu tập trung, an toàn với luồng, như một `ConcurrentHashMap<String, StreamObserver>`, để lưu trữ `responseObserver` cho mỗi người dùng đang hoạt động.
Khi một thông điệp đến trên `onNext` của `requestObserver` của một máy khách, tôi sẽ lặp qua `ConcurrentHashMap` và gọi `onNext()` trên `responseObserver` của mọi máy khách khác.
Thách thức chính về đồng thời là quản lý map một cách an toàn. Tôi cần xử lý việc các máy khách kết nối và ngắt kết nối, tức là thêm và xóa các observer khỏi map. Điều này phải được thực hiện một cách an toàn với luồng để tránh `ConcurrentModificationException` hoặc gửi tin nhắn đến các máy khách đã ngắt kết nối. Ngoài ra, nếu logic phát tin nhắn chậm, nó có thể chặn luồng gRPC, vì vậy tôi có thể cân nhắc sử dụng một nhóm luồng (thread pool) riêng để điều phối các tin nhắn.