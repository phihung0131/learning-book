### **Module 1: gRPC là gì và Tại sao nên sử dụng gRPC thay vì REST?**

#### **1. Giải thích**
gRPC (gRPC Remote Procedure Call) là một framework RPC (Gọi thủ tục từ xa) hiện đại, mã nguồn mở, và hiệu năng cao do Google phát triển, có thể chạy trong mọi môi trường. Nó cho phép một ứng dụng máy khách (client) có thể gọi trực tiếp các phương thức trên một ứng dụng máy chủ (server) ở một máy tính khác như thể nó là một đối tượng cục bộ.

Về cốt lõi, gRPC được xây dựng trên hai công nghệ nền tảng:
*   **HTTP/2**: Không giống như REST thường sử dụng HTTP/1.1, gRPC tận dụng HTTP/2. Điều này mang lại những lợi ích đáng kể về hiệu suất như ghép kênh (multiplexing - gửi nhiều yêu cầu trên một kết nối duy nhất), đẩy dữ liệu từ máy chủ (server push), và nén tiêu đề (header compression).
*   **Protocol Buffers (Protobuf)**: Theo mặc định, gRPC sử dụng Protocol Buffers làm Ngôn ngữ Định nghĩa Giao diện (IDL) và định dạng trao đổi thông điệp. Protobuf là một cơ chế để tuần tự hóa (serializing) dữ liệu có cấu trúc thành một định dạng nhị phân nhỏ gọn, giúp dữ liệu nhỏ hơn và phân tích cú pháp nhanh hơn nhiều so với các định dạng dựa trên văn bản như JSON được sử dụng trong REST.

Quy trình làm việc rất đơn giản: bạn định nghĩa một `service` (dịch vụ) trong một tệp `.proto`, chỉ định các phương thức có thể được gọi từ xa cùng với các tham số và kiểu dữ liệu trả về của chúng. Bộ công cụ gRPC sau đó sẽ tự động tạo ra mã nguồn cho máy khách và máy chủ (được gọi là stubs) trong bất kỳ ngôn ngữ nào mà gRPC hỗ trợ. Điều này tạo ra một "hợp đồng" (contract) được định kiểu mạnh mẽ và rõ ràng giữa máy khách và máy chủ.

<br>

**Sơ đồ: So sánh gRPC và REST**

```ascii
     gRPC                                     REST
┌──────────────┐                       ┌──────────────┐
│  Ứng dụng   │                       │  Ứng dụng   │
│   Client     │                       │   Client     │
└───────┬──────┘                       └───────┬──────┘
        │                                      │
        │ Gọi phương thức (VD: GetUser)       │ HTTP GET /users/123
        │                                      │
┌───────▼──────┐                       ┌───────▼──────┐
│  Client Stub │ (Mã nguồn được tạo)   │ Thư viện HTTP│ (VD: Axios, Fetch)
└───────┬──────┘                       └───────┬──────┘
        │                                      │
        │ Protobuf (Nhị phân) qua HTTP/2       │ JSON (Văn bản) qua HTTP/1.1
        │                                      │
┌───────▼──────┐                       ┌───────▼──────┐
│  gRPC Server │                       │   REST API   │
└──────────────┘                       └──────────────┘
```

#### **2. Tầm quan trọng trong các hệ thống thực tế**
Việc chọn gRPC thay vì REST là một quyết định kiến trúc quan trọng, xuất phát từ nhu cầu về hiệu suất, hiệu quả và các hợp đồng API chặt chẽ, đặc biệt là trong các hệ thống phân tán phức tạp.

*   **Kiến trúc Microservices hiệu năng cao:** Trong kiến trúc microservices, các dịch vụ giao tiếp với nhau rất thường xuyên. Chi phí phát sinh từ các gói tin JSON dạng văn bản và các kết nối HTTP/1.1 của REST có thể tạo ra độ trễ đáng kể. Việc gRPC sử dụng Protocol Buffers dạng nhị phân và cơ chế ghép kênh kết nối của HTTP/2 giúp giảm đáng kể độ trễ mạng và mức sử dụng CPU, làm cho giao tiếp giữa các dịch vụ nhanh hơn và hiệu quả hơn.
*   **Truyền dữ liệu thời gian thực (Real-Time Streaming):** gRPC hỗ trợ truyền dữ liệu hai chiều (bidirectional streaming) một cách hoàn hảo, nơi cả máy khách và máy chủ đều có thể gửi một chuỗi các thông điệp trên một kết nối duy nhất. Điều này lý tưởng cho các ứng dụng như cung cấp dữ liệu tài chính trực tiếp, theo dõi vị trí theo thời gian thực trong các ứng dụng gọi xe, hoặc các dịch vụ trò chuyện tương tác, những tính năng vốn rất khó và phức tạp để triển khai với mô hình yêu cầu-phản hồi (request-response) của REST.
*   **Môi trường đa ngôn ngữ (Polyglot):** Trong các doanh nghiệp lớn, các dịch vụ khác nhau thường được viết bằng các ngôn ngữ lập trình khác nhau. Khả năng tạo mã nguồn tự động của gRPC giúp tạo ra các client stubs và server stubs phù hợp với từng ngôn ngữ từ một tệp `.proto` duy nhất, đảm bảo khả năng tương tác liền mạch.
*   **Thiết bị di động và IoT:** Đối với các thiết bị bị hạn chế về pin và băng thông mạng, hiệu quả là yếu tố cực kỳ quan trọng. Các gói tin nhị phân nhỏ hơn của gRPC tiêu thụ ít băng thông hơn và đòi hỏi ít CPU hơn để xử lý so với JSON, khiến nó trở thành một lựa chọn tuyệt vời cho các máy khách di động và thiết bị IoT.

#### **3. Ví dụ với Spring Boot (Java)**
Trong Spring Boot, việc tích hợp gRPC thường được thực hiện bằng một thư viện của bên thứ ba, vì chưa có starter chính thức. Một lựa chọn phổ biến là `grpc-spring-boot-starter`.

**1. Khai báo dependencies trong `pom.xml`:**
Bạn cần thêm các dependency cho gRPC, Protocol Buffers, và starter.

```xml
<dependency>
    <groupId>net.devh</groupId>
    <artifactId>grpc-server-spring-boot-starter</artifactId>
    <version>2.15.0.RELEASE</version>
</dependency>
```

**2. Định nghĩa Dịch vụ trong tệp `.proto` (`src/main/proto/greeting.proto`):**

```protobuf
syntax = "proto3";
option java_multiple_files = true;
package com.example.grpc;

// Định nghĩa dịch vụ chào hỏi.
service Greeter {
  // Gửi một lời chào
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

// Thông điệp yêu cầu chứa tên của người dùng.
message HelloRequest {
  string name = 1;
}

// Thông điệp phản hồi chứa lời chào
message HelloReply {
  string message = 1;
}
```

**3. Triển khai Dịch vụ:**
Quá trình build (sử dụng một plugin của Maven) sẽ tạo ra các lớp Java từ tệp `.proto`. Sau đó, bạn triển khai logic phía máy chủ bằng cách kế thừa lớp cơ sở đã được tạo và chú thích nó bằng `@GrpcService`.

```java
import net.devh.boot.grpc.server.service.GrpcService;
import io.grpc.stub.StreamObserver;

@GrpcService
public class GreeterService extends GreeterGrpc.GreeterImplBase {

    @Override
    public void sayHello(HelloRequest req, StreamObserver<HelloReply> responseObserver) {
        HelloReply reply = HelloReply.newBuilder()
                .setMessage("Xin chào ==> " + req.getName())
                .build();
        responseObserver.onNext(reply);
        responseObserver.onCompleted();
    }
}
```

#### **4. Ví dụ với Go**
Go có sự hỗ trợ xuất sắc và chính thức cho gRPC.

**1. Tải các gói gRPC cho Go:**
```shell
go get google.golang.org/grpc
go get google.golang.org/protobuf/cmd/protoc-gen-go
go get google.golang.org/grpc/cmd/protoc-gen-go-grpc
```

**2. Định nghĩa Dịch vụ trong tệp `.proto` (`protos/greeting.proto`):**
(Sử dụng cùng tệp `.proto` như ví dụ Java, nhưng với tùy chọn package cho Go)
```protobuf
syntax = "proto3";
option go_package = "github.com/your-repo/project/protos";
package protos;

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply) {}
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}
```

**3. Tạo mã nguồn Go:**
Chạy trình biên dịch `protoc` để tạo ra các tệp Go cần thiết.
```shell
protoc --go_out=. --go_opt=paths=source_relative \
    --go-grpc_out=. --go-grpc_opt=paths=source_relative \
    protos/greeting.proto
```
Lệnh này sẽ tạo ra hai tệp: `greeting.pb.go` và `greeting_grpc.pb.go`.

**4. Triển khai và Chạy Server:**

```go
package main

import (
    "context"
    "log"
    "net"
    "google.golang.org/grpc"
    pb "github.com/your-repo/project/protos" // Nhập package đã được tạo
)

// server được sử dụng để triển khai dịch vụ Greeter.
type server struct {
    pb.UnimplementedGreeterServer
}

// SayHello triển khai phương thức SayHello của dịch vụ Greeter.
func (s *server) SayHello(ctx context.Context, in *pb.HelloRequest) (*pb.HelloReply, error) {
    log.Printf("Đã nhận: %v", in.GetName())
    return &pb.HelloReply{Message: "Xin chào " + in.GetName()}, nil
}

func main() {
    lis, err := net.Listen("tcp", ":50051")
    if err != nil {
        log.Fatalf("lắng nghe thất bại: %v", err)
    }
    s := grpc.NewServer()
    pb.RegisterGreeterServer(s, &server{})
    log.Printf("server đang lắng nghe tại %v", lis.Addr())
    if err := s.Serve(lis); err != nil {
        log.Fatalf("phục vụ thất bại: %v", err)
    }
}
```

#### **5. Những cạm bẫy thường gặp (Common pitfalls)**
*   **Độ phức tạp khi thiết lập ban đầu:** Yêu cầu phải định nghĩa dịch vụ trong các tệp `.proto` và chạy một bước tạo mã nguồn làm tăng thêm một lớp phức tạp ban đầu so với việc tạo nhanh một endpoint REST.
*   **Hỗ trợ trình duyệt hạn chế:** Trình duyệt không thể gọi trực tiếp một dịch vụ gRPC vì nó phụ thuộc vào các tính năng của HTTP/2 không được cung cấp thông qua các API trình duyệt tiêu chuẩn. Điều này đòi hỏi một lớp proxy trung gian như gRPC-Web với Envoy để chuyển đổi các yêu cầu.
*   **Khó đọc hơn cho con người:** Bản chất nhị phân của Protobuf gây khó khăn cho việc kiểm tra các gói tin trên đường truyền để gỡ lỗi nếu không có các công cụ chuyên dụng (như Wireshark với bộ phân tích Protobuf hoặc các công cụ gỡ lỗi dành riêng cho gRPC), không giống như JSON dạng văn bản thuần túy của REST.
*   **Khớp nối chặt chẽ (Tight Coupling):** Máy khách và máy chủ được liên kết chặt chẽ với nhau thông qua "hợp đồng" trong tệp `.proto`. Mặc dù điều này đảm bảo an toàn về kiểu dữ liệu, nó cũng có nghĩa là những thay đổi trong định nghĩa dịch vụ thường đòi hỏi cả máy khách và máy chủ phải được cập nhật và triển khai lại.

#### **6. Câu hỏi phỏng vấn**

**Q1: Khi nào bạn sẽ chọn gRPC thay vì REST, và tại sao?**
**A:** Tôi sẽ chọn gRPC thay vì REST chủ yếu cho giao tiếp nội bộ hiệu suất cao giữa các microservice. Các lý do chính là: 1) **Hiệu suất:** gRPC sử dụng HTTP/2 và Protocol Buffers dạng nhị phân, dẫn đến độ trễ và mức sử dụng mạng thấp hơn đáng kể so với REST qua HTTP/1.1 với JSON dạng văn bản. 2) **Streaming:** gRPC hỗ trợ truyền dữ liệu hai chiều một cách tự nhiên, điều này rất cần thiết cho các ứng dụng thời gian thực như cập nhật trực tiếp hoặc luồng dữ liệu, một tính năng không có sẵn trong REST tiêu chuẩn. 3) **Hợp đồng API chặt chẽ:** Việc sử dụng Protocol Buffers επιβάλλει một hợp đồng được định kiểu mạnh mẽ giữa các dịch vụ, giúp giảm lỗi trong quá trình chạy và cải thiện độ tin cậy trong một hệ thống phức tạp, đa ngôn ngữ.

**Q2: Những nhược điểm chính của việc sử dụng gRPC là gì?**
**A:** Những nhược điểm chính là: 1) **Hỗ trợ trình duyệt hạn chế:** Giao tiếp trực tiếp từ trình duyệt đến gRPC là không thể nếu không có một proxy như gRPC-Web, điều này làm tăng thêm độ phức tạp về kiến trúc. 2) **Đường cong học tập (Learning Curve):** Quy trình làm việc liên quan đến các tệp `.proto`, tạo mã nguồn, và hiểu các khái niệm của HTTP/2 đòi hỏi một quá trình học tập khó hơn so với REST truyền thống. 3) **Khả năng đọc gói tin:** Việc gỡ lỗi có thể khó khăn hơn vì các gói tin nhị phân không thể đọc được bằng mắt thường nếu không có công cụ chuyên dụng, không giống như JSON. 4) **Khớp nối chặt chẽ:** Hợp đồng nghiêm ngặt có nghĩa là cả máy khách và máy chủ đều phải được cập nhật nếu định nghĩa API thay đổi.