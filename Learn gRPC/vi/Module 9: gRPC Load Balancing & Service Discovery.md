### **Module 9: Cân bằng tải & Khám phá dịch vụ trong gRPC**

#### **1. Giải thích**

Trong một hệ thống phân tán, bạn hiếm khi chỉ có một phiên bản (instance) của một dịch vụ. Để đảm bảo khả năng mở rộng và tính sẵn sàng cao, bạn sẽ chạy nhiều phiên bản giống hệt nhau. **Cân bằng tải (Load balancing)** là quá trình phân phối các yêu cầu đến trên nhóm các phiên bản máy chủ này. **Khám phá dịch vụ (Service discovery)** là quá trình tự động tìm kiếm vị trí mạng (địa chỉ IP và cổng) của các phiên bản này.

**Cách tiếp cận của gRPC: Cân bằng tải phía Client (Client-Side Load Balancing)**

Không giống như các ứng dụng web truyền thống nơi một proxy trung tâm (như Nginx hoặc AWS Elastic Load Balancer) xử lý việc cân bằng tải (cân bằng tải phía máy chủ), gRPC lại ưa chuộng mạnh mẽ **cân bằng tải phía máy khách**.

Trong mô hình này:
1.  **Khám phá Dịch vụ**: Client gRPC truy vấn một cơ chế khám phá dịch vụ (như Kubernetes DNS, Consul, hoặc etcd) để lấy danh sách tất cả các địa chỉ IP có sẵn cho một dịch vụ mục tiêu. Việc này được xử lý bởi một **Bộ phân giải tên (Name Resolver)**.
2.  **Cân bằng tải**: Client sau đó sử dụng một **Chính sách Cân bằng tải (Load Balancing Policy)** (ví dụ: `round_robin`) để chọn một trong các máy chủ backend từ danh sách cho mỗi RPC. Nó duy trì kết nối đến nhiều backend và phân phối các yêu cầu đi giữa chúng.

Cách tiếp cận này loại bỏ nhu cầu về một proxy trung tâm, vốn là một điểm lỗi duy nhất (single point of failure) và một nút thắt cổ chai tiềm tàng.

**Tại sao điều này quan trọng đối với gRPC?**

gRPC sử dụng HTTP/2, vốn duy trì một kết nối TCP duy nhất, tồn tại lâu dài giữa một client và một server. Nhiều RPC được ghép kênh (multiplexed) trên một kết nối này. Nếu bạn đặt một bộ cân bằng tải TCP (Lớp 4) đơn giản giữa các client và server, bộ cân bằng tải sẽ thiết lập một kết nối từ một client đến *một* máy chủ backend cụ thể. Kết nối sau đó sẽ bị "ghim chặt" vào backend đó. Tất cả các RPC tiếp theo từ client đó sẽ đi đến cùng một phiên bản máy chủ, hoàn toàn phá vỡ mục đích của việc cân bằng tải.

```ascii
// Vấn đề: Cân bằng tải L4 với gRPC
// Client A bị ghim vĩnh viễn vào Server 1, Client B vào Server 2. Không có cân bằng tải.
Client A ----+                             +---> Server 1
             |---> Cân bằng tải TCP ---> |
Client B ----+                             +---> Server 2
                                           |
                                           +---> Server 3

// Giải pháp: Cân bằng tải phía Client
// Client A nhận biết tất cả các server và tự phân phối các RPC của mình.
             +------------------------------> Server 1
             |
Client A ----+------------ Round Robin ----> Server 2
(Thông minh) |
             +------------------------------> Server 3
```

#### **2. Tầm quan trọng trong các hệ thống thực tế**

*   **Khả năng mở rộng & Hiệu suất**: Bằng cách phân phối các yêu cầu một cách đồng đều, cân bằng tải phía client đảm bảo rằng không có phiên bản máy chủ nào trở thành nút thắt cổ chai. Điều này cho phép hệ thống mở rộng theo chiều ngang bằng cách chỉ cần thêm nhiều phiên bản máy chủ hơn.
*   **Tính sẵn sàng cao & Khả năng phục hồi**: Client nhận biết tất cả các phiên bản backend đang hoạt động tốt. Nếu một máy chủ gặp sự cố, bộ phân giải tên của client sẽ nhận được một danh sách cập nhật từ hệ thống khám phá dịch vụ. Client sau đó sẽ tự động ngừng gửi lưu lượng truy cập đến phiên bản bị lỗi và định tuyến nó đến những phiên bản còn lại đang hoạt động tốt, cung cấp khả năng chuyển đổi dự phòng (failover) một cách liền mạch.
*   **Môi trường Cloud-Native Năng động**: Trong các môi trường như Kubernetes, các pod là tạm thời (ephemeral)—chúng được tạo, phá hủy, và thay đổi quy mô thường xuyên, và IP của chúng cũng thay đổi. Việc gán cứng địa chỉ IP là không thể. Khám phá dịch vụ là cơ chế thiết yếu cho phép các client tìm thấy các backend của chúng trong một bối cảnh năng động như vậy.
*   **Giảm độ phức tạp của Cơ sở hạ tầng**: Cân bằng tải phía client có thể loại bỏ nhu cầu về một lớp phần cứng hoặc phần mềm cân bằng tải chuyên dụng, giúp đơn giản hóa cấu trúc liên kết mạng và giảm chi phí.

#### **3. Ví dụ với Spring Boot (Java)**

Hãy cấu hình một gRPC client Spring Boot để thực hiện khám phá dịch vụ và cân bằng tải round-robin trong môi trường Kubernetes.

**Kịch bản**:
*   Một gRPC server được triển khai trong Kubernetes với 3 bản sao (replicas/pods).
*   Một `Service` Kubernetes có tên `user-service-k8s` được tạo để nhóm các pod này lại.
*   Một gRPC client (cũng trong Kubernetes) cần kết nối đến `user-service-k8s` và có các yêu cầu của nó được cân bằng trên cả 3 pod.

**1. Dependencies trong `pom.xml`:**
Bạn cần `grpc-netty-shaded` (thường đã được bao gồm) và các starter cốt lõi. Không cần dependency đặc biệt nào cho việc phân giải DNS vì nó đã được tích hợp sẵn trong `grpc-java`.

**2. Cấu hình địa chỉ Client trong `application.properties`:**
Điểm mấu chốt là sử dụng scheme `dns:///`. Điều này báo cho `grpc-java` sử dụng bộ phân giải tên DNS tích hợp sẵn của nó. Địa chỉ phải là tên miền đủ điều kiện (FQDN) của service Kubernetes.

```properties
# application.properties
grpc.client.user-service.address=dns:///user-service-k8s.default.svc.cluster.local:9090
grpc.client.user-service.negotiationType=PLAINTEXT
```
*   `user-service-k8s`: Tên của `Service` Kubernetes.
*   `default`: Namespace Kubernetes nơi dịch vụ đang cư trú.
*   `svc.cluster.local`: Hậu tố tên miền cluster tiêu chuẩn trong Kubernetes.
*   Bộ phân giải DNS sẽ tra cứu địa chỉ này và nhận lại một danh sách các địa chỉ IP của các pod.

**3. Cấu hình Chính sách Cân bằng tải:**
Theo mặc định, gRPC sử dụng chính sách `pick_first`, đây không phải là điều chúng ta muốn để cân bằng tải. Chúng ta cần thay đổi nó thành `round_robin`. Trong `grpc-spring-boot-starter`, cách làm chuẩn mực để làm điều này là cung cấp một bean `GrpcChannelConfigurer`.

```java
package com.example.grpc.client.config;

import io.grpc.ManagedChannelBuilder;
import net.devh.boot.grpc.client.channelfactory.GrpcChannelConfigurer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class GrpcClientLoadBalancingConfig {

    @Bean
    public GrpcChannelConfigurer grpcChannelConfigurer() {
        return (channelBuilder, name) -> {
            // Bộ cấu hình này sẽ được áp dụng cho tất cả các channel được tạo bởi factory.
            // Bạn có thể thêm logic để chỉ áp dụng cho các channel cụ thể bằng cách kiểm tra 'name'.
            if ("user-service".equals(name)) {
                System.out.println("Áp dụng chính sách cân bằng tải round_robin cho channel: " + name);
                
                // Thiết lập chính sách cân bằng tải mặc định cho channel
                channelBuilder.defaultLoadBalancingPolicy("round_robin");
            }
        };
    }
}
```
Với cấu hình này, khi stub client `user-service` được tạo, channel của nó sẽ được cấu hình để:
1.  Sử dụng bộ phân giải DNS để lấy các IP cho `user-service-k8s.default.svc.cluster.local`.
2.  Áp dụng chính sách `round_robin` để phân phối các RPC trên danh sách các IP đó.

#### **5. Những cạm bẫy thường gặp**
*   **Sử dụng Cân bằng tải L4 một cách vô tình**: Triển khai lên một môi trường đám mây nơi một bộ cân bằng tải mạng TCP được đặt trước các máy chủ gRPC theo mặc định. Điều này sẽ vô hiệu hóa việc cân bằng tải phía client và ghim kết nối. Bạn phải đảm bảo các client có thể phân giải và kết nối trực tiếp đến các địa chỉ IP của từng pod/VM.
*   **Quên không thiết lập Chính sách Cân bằng tải**: Chính sách mặc định là `pick_first`. Một nhà phát triển thiết lập khám phá dịch vụ DNS, thấy rằng client kết nối được, và cho rằng mọi thứ đang hoạt động. Trên thực tế, tất cả lưu lượng truy cập đang đi đến một phiên bản máy chủ duy nhất cho đến khi nó thất bại. Bạn phải cấu hình `round_robin` một cách tường minh để đạt được sự phân phối tải.
*   **Độ trễ của Khám phá Dịch vụ**: Khi một phiên bản máy chủ gặp sự cố hoặc một phiên bản mới được thêm vào, có một độ trễ trước khi hệ thống khám phá dịch vụ cập nhật bản ghi của mình và bộ phân giải của client nhận được danh sách mới. Trong khoảng thời gian này, các client có thể vẫn cố gắng kết nối đến một máy chủ đã chết.
*   **Dịch vụ Headless và ClusterIP trong Kubernetes**: Để cân bằng tải phía client, bạn thường muốn sử dụng một **Headless Service** trong Kubernetes. DNS của một headless service sẽ phân giải thành danh sách các địa chỉ IP của pod, đây chính xác là những gì client cần. DNS của một `ClusterIP` Service thông thường sẽ phân giải thành một địa chỉ IP ảo duy nhất, sau đó nó sẽ tự thực hiện proxy L4—tái tạo lại vấn đề của cân bằng tải L4.

#### **6. Câu hỏi phỏng vấn**

**Q1: Tại sao một bộ cân bằng tải TCP (L4) truyền thống có thể không hiệu quả cho gRPC? Cách tiếp cận tốt hơn là gì?**
**A:** Một bộ cân bằng tải TCP truyền thống không hiệu quả vì gRPC sử dụng một kết nối HTTP/2 duy nhất, tồn tại lâu dài cho mỗi backend. Bộ cân bằng tải L4 sẽ chuyển tiếp kết nối TCP của một client đến một phiên bản máy chủ cụ thể và nó sẽ bị ghim ở đó. Tất cả các yêu cầu gRPC tiếp theo, được ghép kênh trên kết nối duy nhất đó, sẽ đều đến cùng một máy chủ, phá vỡ mục đích phân phối tải. Cách tiếp cận tốt hơn là cân bằng tải phía client, nơi client đủ thông minh. Nó tự khám phá tất cả các địa chỉ IP của máy chủ backend và phân phối các RPC đi của mình trên chúng bằng một chính sách như round-robin.

**Q2: Giải thích vai trò của "Name Resolver" và "Load Balancing Policy" trong cơ chế cân bằng tải phía client của gRPC. Cho một ví dụ.**
**A:**
*   **Name Resolver (Bộ phân giải tên)**: Nhiệm vụ của nó là khám phá dịch vụ. Nó lấy một tên logic cho một dịch vụ (ví dụ: `dns:///my-service:8080`) và phân giải nó thành một danh sách thực tế các địa chỉ IP và cổng của máy chủ. Nó cũng theo dõi các thay đổi và cung cấp cập nhật cho client. Một ví dụ là một bộ phân giải DNS truy vấn Kubernetes để lấy IP của các pod.
*   **Load Balancing Policy (Chính sách Cân bằng tải)**: Nhiệm vụ của nó là lấy danh sách các địa chỉ được cung cấp bởi bộ phân giải tên và quyết định địa chỉ nào sẽ được sử dụng cho RPC tiếp theo. Một ví dụ là chính sách `round_robin`, nó lặp qua danh sách các máy chủ có sẵn cho mỗi yêu cầu mới.

**Q3: Bạn có một gRPC client và nhiều phiên bản của một gRPC server đang chạy trong Kubernetes. Bạn sẽ cấu hình client như thế nào để khám phá và cân bằng các yêu cầu trên tất cả các phiên bản máy chủ?**
**A:** Tôi sẽ cấu hình nó để cân bằng tải phía client:
1.  **Phía Server**: Cung cấp các pod của server bằng một **Headless Kubernetes Service**. Điều này đảm bảo tên DNS của nó phân giải trực tiếp thành danh sách các địa chỉ IP của từng pod.
2.  **Địa chỉ phía Client**: Cấu hình địa chỉ channel của gRPC client để sử dụng scheme `dns:///` với tên DNS nội bộ đầy đủ của headless service (ví dụ: `dns:///my-service.my-namespace.svc.cluster.local:9090`).
3.  **Chính sách phía Client**: Cấu hình một cách tường minh channel gRPC của client để sử dụng chính sách cân bằng tải `round_robin`. Chính sách mặc định, `pick_first`, sẽ không phân phối tải.

**Q4: Sự khác biệt giữa chính sách cân bằng tải `pick_first` và `round_robin` là gì? Khi nào bạn sẽ sử dụng mỗi loại?**
**A:**
*   **`pick_first` (mặc định)**: Client phân giải danh sách các địa chỉ, cố gắng kết nối đến địa chỉ đầu tiên, và nếu thành công, nó sẽ gửi *tất cả* các RPC đến máy chủ duy nhất đó. Nó sẽ chỉ thử địa chỉ tiếp theo trong danh sách nếu địa chỉ đầu tiên thất bại. Đây là một chiến lược cho việc **chuyển đổi dự phòng (failover)**, không phải để cân bằng tải. Bạn sẽ sử dụng nó trong một thiết lập active-passive đơn giản.
*   **`round_robin`**: Client kết nối đến tất cả các địa chỉ có thể phân giải được. Đối với mỗi RPC mới, nó lặp qua danh sách các kết nối đang hoạt động và gửi yêu cầu đến máy chủ tiếp theo trong hàng. Đây là một chiến lược để **phân phối tải**. Bạn nên sử dụng nó khi muốn trải đều lưu lượng truy cập trên nhiều phiên bản máy chủ đang hoạt động để cải thiện khả năng mở rộng.