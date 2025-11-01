### **Module 2: Protocol Buffers**

#### **1. Giải thích**
Protocol Buffers (Protobuf) là một định dạng dữ liệu đa nền tảng, miễn phí và mã nguồn mở được sử dụng để tuần tự hóa (serialize) dữ liệu có cấu trúc. Nó được Google phát triển để sử dụng nội bộ và sau đó được công bố rộng rãi. Hãy coi nó giống như XML hay JSON, nhưng nhỏ hơn, nhanh hơn và đơn giản hơn.

Bạn định nghĩa cấu trúc dữ liệu của mình một lần trong một tệp `.proto` bằng một ngôn ngữ có mục đích đặc biệt. Tệp này đóng vai trò là nguồn định nghĩa duy nhất và chính xác nhất cho mô hình dữ liệu của bạn. Từ tệp này, trình biên dịch Protocol Buffer, `protoc`, sẽ tạo ra mã nguồn bằng ngôn ngữ bạn mong muốn (ví dụ: Java, Go, Python, C++). Mã nguồn được tạo ra này cung cấp các phương thức truy cập đơn giản cho mỗi trường (ví dụ: `getName()`, `setName()`) và các phương thức để tuần tự hóa/giải tuần tự hóa (serialize/deserialize) toàn bộ cấu trúc thành dữ liệu nhị phân thô và ngược lại.

**Các khái niệm chính trong tệp `.proto` (sử dụng cú pháp `proto3`):**

*   **`message`**: Định nghĩa một kiểu dữ liệu có cấu trúc, tương tự như một `class` trong Java hay một `struct` trong Go.
*   **Fields (Trường)**: Mỗi message có các trường được đặt tên và có kiểu dữ liệu (ví dụ: `string`, `int32`, `bool`).
*   **Field Numbers (Số hiệu trường)**: Mỗi trường được gán một số nguyên dương duy nhất (ví dụ: `string name = 1;`). Các số này được sử dụng để xác định các trường trong định dạng mã hóa nhị phân. **Một khi đã được sử dụng, các số hiệu này không bao giờ được thay đổi.**
*   **Data Types (Kiểu dữ liệu)**: Protobuf hỗ trợ một loạt các kiểu dữ liệu vô hướng (`int32`, `float`, `string`, `bytes`), enum, và các kiểu dữ liệu phức hợp (sử dụng các `message` khác làm kiểu dữ liệu cho trường).
*   **`service`**: Định nghĩa giao diện dịch vụ RPC, chỉ định các phương thức, kiểu `message` yêu cầu và kiểu `message` phản hồi của chúng.

**Ví dụ về tệp `.proto`:**
```protobuf
syntax = "proto3";

package user.v1;

option java_package = "com.example.user.v1";
option java_multiple_files = true;
option go_package = "github.com/my-org/user-service/gen/go/user/v1";

enum Role {
  ROLE_UNSPECIFIED = 0;
  ROLE_MEMBER = 1;
  ROLE_ADMIN = 2;
}

message UserProfile {
  string user_id = 1;
  string display_name = 2;
  string email = 3;
}

message GetUserRequest {
  string user_id = 1;
}

message GetUserResponse {
  UserProfile profile = 1;
  Role role = 2;
  int64 created_timestamp = 3;
}

service UserService {
  rpc GetUser(GetUserRequest) returns (GetUserResponse);
}
```

#### **2. Tầm quan trọng trong các hệ thống thực tế**
*   **Hiệu suất & Hiệu quả**: Lý do chính mà Protobuf được sử dụng trong các hệ thống hiệu năng cao là hiệu quả của nó. Định dạng tuần tự hóa nhị phân cực kỳ nhỏ gọn, dẫn đến các gói tin (payloads) nhỏ hơn. Điều này làm giảm mức tiêu thụ băng thông mạng và độ trễ. Việc phân tích cú pháp định dạng nhị phân này cũng ít tốn tài nguyên tính toán hơn nhiều (sử dụng ít CPU hơn) so với việc phân tích các định dạng dựa trên văn bản như JSON.
*   **Hợp đồng chặt chẽ & An toàn kiểu dữ liệu**: Tệp `.proto` định nghĩa một hợp đồng chính thức, không phụ thuộc vào ngôn ngữ. Khi bạn tạo mã nguồn, bạn sẽ nhận được các đối tượng dữ liệu được định kiểu mạnh mẽ. Điều này loại bỏ hoàn toàn một lớp các lỗi lúc chạy chương trình gây ra bởi kiểu dữ liệu không khớp hoặc tên trường sai chính tả có thể xảy ra với các định dạng không có lược đồ (schema-less) như JSON.
*   **Sự phát triển của Lược đồ (Phiên bản API)**: Protobuf hỗ trợ rất tốt cho khả năng tương thích tiến (forward compatibility) và tương thích ngược (backward compatibility). Bạn có thể thêm các trường mới vào một message mà không làm hỏng các máy khách hoặc máy chủ cũ hơn. Miễn là bạn không thay đổi số hiệu của các trường hiện có, mã nguồn cũ chỉ đơn giản là bỏ qua các trường mới khi giải tuần tự hóa. Điều này cho phép sự phát triển dần dần, độc lập của các dịch vụ trong kiến trúc microservices.
*   **Tạo mã nguồn tự động**: Việc tự động tạo ra các lớp truy cập dữ liệu bằng nhiều ngôn ngữ giúp tiết kiệm đáng kể thời gian phát triển và giảm thiểu mã nguồn lặp đi lặp lại (boilerplate code). Các nhà phát triển có thể tập trung vào logic nghiệp vụ thay vì viết mã để xử lý việc tuần tự hóa và giải tuần tự hóa dữ liệu.

#### **3. Ví dụ với Spring Boot (Java)**

**1. Cấu hình `pom.xml` để tạo mã nguồn:**
Bạn cần `protobuf-maven-plugin` để biên dịch các tệp `.proto` và tạo ra các tệp nguồn Java trước giai đoạn biên dịch chính.

```xml
<build>
    <extensions>
        <extension>
            <groupId>kr.motd.maven</groupId>
            <artifactId>os-maven-plugin</artifactId>
            <version>1.7.0</version>
        </extension>
    </extensions>
    <plugins>
        <plugin>
            <groupId>org.xolstice.maven.plugins</groupId>
            <artifactId>protobuf-maven-plugin</artifactId>
            <version>0.6.1</version>
            <configuration>
                <protocArtifact>com.google.protobuf:protoc:3.21.7:exe:${os.detected.classifier}</protocArtifact>
                <pluginId>grpc-java</pluginId>
                <pluginArtifact>io.grpc:protoc-gen-grpc-java:1.50.2:exe:${os.detected.classifier}</pluginArtifact>
            </configuration>
            <executions>
                <execution>
                    <goals>
                        <goal>compile</goal>
                        <goal>compile-custom</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

**2. Sử dụng mã nguồn Java được tạo ra:**
Sau khi chạy `mvn clean install`, plugin sẽ tạo ra các tệp Java từ định nghĩa `.proto`. Sau đó, bạn có thể sử dụng mẫu thiết kế "builder" được cung cấp để xây dựng các đối tượng message.

```java
// Xây dựng một đối tượng request
GetUserRequest request = GetUserRequest.newBuilder()
    .setUserId("user-123-xyz")
    .build();

// Tạo một đối tượng response trong phần triển khai dịch vụ
UserProfile profile = UserProfile.newBuilder()
    .setUserId("user-123-xyz")
    .setDisplayName("Alice")
    .setEmail("alice@example.com")
    .build();

GetUserResponse response = GetUserResponse.newBuilder()
    .setProfile(profile)
    .setRole(Role.ROLE_ADMIN)
    .setCreatedTimestamp(System.currentTimeMillis())
    .build();

// Truy cập các trường
System.out.println("User ID: " + response.getProfile().getUserId());
System.out.println("User Role: " + response.getRole());
```

#### **4. Ví dụ với Go**

**1. Cấu trúc thư mục:**
Thông lệ tốt là giữ các tệp `.proto` trong một thư mục riêng.
```
my-project/
├── protos/
│   └── user/v1/
│       └── user.proto
├── gen/
│   └── go/
│       └── user/v1/
│           ├── user.pb.go       (Được tạo tự động)
│           └── user_grpc.pb.go  (Được tạo tự động)
└── main.go
```

**2. Lệnh tạo mã nguồn:**
Bạn chạy `protoc` từ thư mục gốc của dự án.

```shell
protoc --go_out=./gen/go --go_opt=paths=source_relative \
    --go-grpc_out=./gen/go --go-grpc_opt=paths=source_relative \
    protos/user/v1/user.proto
```

**3. Sử dụng các struct Go được tạo ra:**
Tệp `.pb.go` được tạo ra sẽ chứa các struct của Go tương ứng với các message của bạn.

```go
import (
	pb "github.com/my-org/user-service/gen/go/user/v1" // Nhập package được tạo
	"time"
	"fmt"
)

func main() {
    // Tạo một đối tượng request
    request := &pb.GetUserRequest{
        UserId: "user-123-xyz",
    }

    // Tạo một đối tượng response
    response := &pb.GetUserResponse{
        Profile: &pb.UserProfile{
            UserId:      "user-123-xyz",
            DisplayName: "Alice",
            Email:       "alice@example.com",
        },
        Role:             pb.Role_ROLE_ADMIN,
        CreatedTimestamp: time.Now().Unix(),
    }

    // Truy cập các trường
    fmt.Printf("User ID: %s\n", response.Profile.UserId)
    fmt.Printf("User Role: %s\n", response.Role)
}
```

#### **5. Những cạm bẫy thường gặp**
*   **Thay đổi Số hiệu trường**: Không bao giờ thay đổi thẻ số của một trường hiện có. Điều này sẽ phá vỡ tính tương thích trên đường truyền (wire compatibility) và dẫn đến hỏng dữ liệu hoặc lỗi phân tích cú pháp cho các máy khách và máy chủ hiện tại. Nếu bạn muốn đổi tên một trường, bạn có thể sử dụng từ khóa `reserved` để ngăn thẻ cũ bị tái sử dụng.
*   **Quên các giá trị mặc định của `proto3`**: Trong `proto3`, các trường không có khái niệm `null`. Nếu một trường vô hướng không được thiết lập, nó sẽ mặc định thành một giá trị được xác định trước (0 cho số, chuỗi rỗng cho `string`, `false` cho `bool`). Điều này có thể dẫn đến sự mơ hồ. Ví dụ, bạn không thể biết `age = 0` được thiết lập một cách tường minh hay là do nó bị bỏ qua. Để giải quyết vấn đề này, Google đã giới thiệu các kiểu bao bọc (wrapper types) (`google.protobuf.Int32Value`) hoặc bạn có thể tự định nghĩa bằng từ khóa `optional` trong các phiên bản gần đây.
*   **Không tạo lại mã nguồn**: Một nguồn lỗi phổ biến là sửa đổi một tệp `.proto` nhưng quên chạy trình biên dịch `protoc`. Điều này gây ra sự không khớp giữa logic ứng dụng và mã tuần tự hóa, dẫn đến các lỗi không mong muốn lúc chạy chương trình.
*   **Tái sử dụng Số hiệu trường**: Một khi một trường đã bị xóa, số hiệu của nó nên được `reserved` (dành riêng) để nó không thể vô tình được tái sử dụng trong tương lai cho một trường khác với kiểu dữ liệu khác, điều này sẽ làm hỏng các máy khách cũ hơn.

#### **6. Câu hỏi phỏng vấn**

**Q1: Những ưu điểm chính của Protobuf so với JSON là gì?**
**A:** Ba ưu điểm chính là:
1.  **Hiệu suất:** Protobuf là một định dạng nhị phân nhỏ hơn và nhanh hơn nhiều để tuần tự hóa/giải tuần tự hóa so với JSON dựa trên văn bản, dẫn đến độ trễ và mức sử dụng CPU thấp hơn.
2.  **Thực thi Lược đồ (Schema Enforcement):** Protobuf sử dụng một tệp lược đồ `.proto` để thực thi một hợp đồng dữ liệu nghiêm ngặt. Điều này cung cấp kiểu dữ liệu mạnh và giảm thiểu các lỗi dữ liệu lúc chạy chương trình, không giống như bản chất linh hoạt và dễ gây lỗi hơn của JSON.
3.  **Tương thích ngược/tiến:** Protobuf có các quy tắc được xác định rõ ràng để phát triển một lược đồ, chẳng hạn như thêm các trường mới, mà không làm hỏng các ứng dụng hiện có. Điều này rất quan trọng để duy trì sự ổn định trong kiến trúc microservices.

**Q2: Làm thế nào để bạn xử lý việc phiên bản hóa API hoặc phát triển lược đồ trong Protobuf mà không làm hỏng các máy khách hiện có?**
**A:** Điều cốt yếu là tuân theo các quy tắc sau:
*   **KHÔNG** thay đổi số hiệu của bất kỳ trường nào hiện có.
*   **BẠN CÓ THỂ** thêm các trường mới vào một message. Các máy khách cũ sẽ chỉ đơn giản là bỏ qua chúng khi giải tuần tự hóa.
*   **BẠN CÓ THỂ** xóa các trường. Các máy khách mới sẽ thấy chúng là giá trị mặc định. Tuy nhiên, bạn nên `reserve` số hiệu và tên của trường đã xóa để ngăn việc tái sử dụng trong tương lai.
*   Thay đổi kiểu dữ liệu của một trường là một thay đổi gây phá vỡ (breaking change). Nếu cần thay đổi kiểu dữ liệu, thông lệ tiêu chuẩn là giới thiệu một trường mới với một số hiệu mới cho kiểu dữ liệu mới và đánh dấu trường cũ là `deprecated` (không dùng nữa).

**Q3: Tầm quan trọng của các số hiệu trường (ví dụ: `= 1`, `= 2`) trong tệp `.proto` là gì?**
**A:** Các số hiệu trường là phần quan trọng nhất của định nghĩa message. Chúng được sử dụng làm định danh duy nhất cho mỗi trường trong định dạng nhị phân trên đường truyền (binary wire format). Không giống như JSON sử dụng tên trường (chuỗi ký tự) làm khóa, Protobuf sử dụng các thẻ số nguyên này. Đây là lý do chính cho sự nhỏ gọn và tốc độ của nó—mã hóa một thẻ số nguyên hiệu quả hơn nhiều so với mã hóa một khóa chuỗi ký tự. Vì chúng xác định bố cục nhị phân, chúng phải luôn ổn định trong suốt vòng đời của API để đảm bảo tính tương thích ngược.