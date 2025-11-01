### **Mục Lục Lộ Trình Học Kafka**

#### **Chương I: Nền Tảng Cơ Bản**

*   **[Phần 1: Nhập Môn Kafka - Các Khái Niệm Cốt Lõi](#phần-1-nhập-môn-kafka---nền-tảng-vững-chắc)** - *Hiểu Kafka là gì, tại sao nó tồn tại, và làm quen với các thuật ngữ quan trọng nhất.*
    *   Event / Message (Sự kiện / Tin nhắn)
    *   Producer & Consumer (Nhà sản xuất & Người tiêu dùng)
    *   Broker & Cluster (Máy chủ & Cụm)
    *   Topic & Partition (Chủ đề & Phân vùng)
    *   Offset (Vị trí)
    *   Consumer Group (Nhóm người tiêu dùng)
    *   Kiến trúc hoạt động tổng quan

#### **Chương II: Thực Hành Đầu Tiên**

*   **[Phần 2: Xây Dựng Producer & Consumer Đầu Tiên với Spring Boot](#phần-2-thực-hành-đầu-tiên-với-kafka-và-spring-boot)** - *Biến lý thuyết thành hành động, gửi và nhận những tin nhắn đầu tiên.*
    *   Cài đặt Kafka và Zookeeper bằng Docker
    *   Khởi tạo và cấu hình dự án Spring Boot
    *   Sử dụng `KafkaTemplate` để gửi tin nhắn (Producer)
    *   Sử dụng `@KafkaListener` để nhận tin nhắn (Consumer)

#### **Chương III: Các Khái Niệm Trung Cấp - Mở Rộng và Đảm Bảo Dữ Liệu**

*   **[Phần 3: Sức Mạnh Của Partitions và Message Keys](#phần-3-hiểu-sâu-về-topic-partition-và-key)** - *Hiểu cách Kafka đạt được hiệu năng cao và đảm bảo thứ tự tin nhắn.*
    *   Vai trò của Partition trong việc xử lý song song và mở rộng
    *   Tầm quan trọng của Message Key
    *   Cách Kafka đảm bảo các tin nhắn cùng key vào cùng partition
    *   Thực hành gửi tin nhắn có key với Spring Boot
    *   Ví dụ Producer với Go


* **[Phần 4: Consumer Groups, Rebalancing và Quản Lý Offset](#phần-4-sức-mạnh-của-consumer-group-rebalancing-và-quản-lý-offset)** - *Khám phá cách các consumer làm việc cùng nhau một cách hiệu quả và đáng tin cậy.*
    *   Cân bằng tải và chịu lỗi với Consumer Group
    *   Quá trình Rebalance (Tái cân bằng)
    *   Cơ chế Commit Offset để tránh mất dữ liệu
    *   Delivery Semantics: At-least-once
    *   Thực hành mở rộng consumer (scaling out)

#### **Chương IV: Kỹ Thuật Nâng Cao và Vận Hành Production**

*   **[Phần 5: Định Dạng Dữ Liệu và Schema Registry](#phần-5-định-dạng-dữ-liệu-và-schema-registry---hợp-đồng-bất-biến)** - *Vượt ra khỏi các chuỗi (String) đơn giản để làm việc với dữ liệu có cấu trúc một cách an toàn.*
    *   Hạn chế của JSON và String
    *   Giới thiệu các định dạng nhị phân: Avro, Protobuf
    *   Schema Registry là gì và tại sao nó quan trọng để tránh lỗi tương thích
    *   Ví dụ với Avro và Spring Boot.


*   **[Phần 6: Xử Lý Lỗi Nâng Cao - Dead Letter Topic (DLT)](#phần-6-xử-lý-lỗi-nâng-cao---dead-letter-topic-dlt)** - *Xây dựng hệ thống bền bỉ có thể xử lý các tin nhắn "độc" (poison pill) mà không bị dừng.*
    *   Vấn đề của các tin nhắn không thể xử lý
    *   Mô hình Dead Letter Topic (DLT)
    *   Cách triển khai DLT trong Spring Kafka
    *   Chiến lược xử lý lại (retry) và giám sát DLT.


*   **[Phần 7: Tinh Chỉnh Cấu Hình Producer & Consumer](#phần-7-tinh-chỉnh-cấu-hình-producer--consumer)** - *Tối ưu hóa hiệu suất, độ trễ và độ tin cậy bằng cách điều chỉnh các thông số quan trọng.*
    *   Producer: `acks`, `retries`, `batch.size`, `linger.ms`, `compression.type`.
    *   Consumer: `fetch.min.bytes`, `fetch.max.wait.ms`, `max.poll.records`, `auto.offset.reset`.

#### **Chương V: Hệ Sinh Thái Kafka**

*   **[Phần 8: Giới Thiệu Kafka Connect](#phần-8-giới-thiệu-kafka-connect---cổng-nối-dữ-liệu)** - *Tích hợp Kafka với các hệ thống khác (như database, S3) một cách dễ dàng mà không cần viết code.*
    *   Kafka Connect là gì?
    *   Source Connectors vs. Sink Connectors
    *   Chạy thử một connector đơn giản.


*   **[Phần 9: Giới Thiệu Kafka Streams](#phần-9-giới-thiệu-kafka-streams---xử-lý-dữ-liệu-thời-gian-thực)** - Thực hiện xử lý dữ liệu theo thời gian thực (stream processing) trực tiếp bằng Kafka.*
    *   Kafka Streams là gì?
    *   So sánh với mô hình Consumer truyền thống
    *   Xây dựng một ứng dụng xử lý luồng đơn giản (ví dụ: đếm từ).

---

### **Phần 1: Nhập Môn Kafka - Nền Tảng Vững Chắc**

Trước khi viết bất kỳ dòng code nào, điều quan trọng nhất là phải hiểu rõ "Kafka là gì?" và các thành phần cốt lõi của nó. Hãy tưởng tượng Kafka như một hệ thống bưu điện kỹ thuật số siêu hiệu quả.

#### **1. Kafka là gì?**

Về bản chất, Apache Kafka là một nền tảng truyền tải sự kiện (event streaming) phân tán, mã nguồn mở. Nó được thiết kế để xử lý khối lượng lớn dữ liệu trong thời gian thực. Bạn có thể hình dung nó như một trung tâm trung chuyển, nơi các ứng dụng có thể gửi (publish) và nhận (subscribe) các luồng dữ liệu, được gọi là "sự kiện" hay "tin nhắn".

**Tại sao lại cần Kafka?**

Trong các hệ thống hiện đại, rất nhiều sự kiện xảy ra liên tục: người dùng click chuột, đơn hàng được tạo, cảm biến IoT gửi dữ liệu, logs từ các dịch vụ... Việc kết nối trực tiếp mọi hệ thống với nhau sẽ tạo ra một mớ hỗn độn phức tạp. Kafka giải quyết vấn đề này bằng cách hoạt động như một "nhật ký sự kiện" trung tâm:

*   **Các ứng dụng gửi (Producer)** chỉ cần gửi sự kiện đến Kafka.
*   **Các ứng dụng nhận (Consumer)** sẽ đọc các sự kiện đó một cách độc lập, theo tốc độ của riêng mình.

Điều này giúp **tách biệt (decouple)** các thành phần trong hệ thống của bạn, giúp chúng dễ dàng mở rộng và quản lý hơn.

#### **2. Các Khái Niệm Cốt Lõi Của Kafka**

Để hiểu cách Kafka hoạt động, chúng ta cần nắm vững các thuật ngữ cơ bản sau:

| Thuật Ngữ | Giải Thích Đơn Giản | Vai Trò |
| --- | --- | --- |
| **Event / Message (Sự kiện / Tin nhắn)** | Một mẩu dữ liệu được gửi qua Kafka. Ví dụ: một dòng log, một thông tin về hành động của người dùng. Mỗi tin nhắn có thể có Key (khóa) và Value (giá trị). | Đơn vị dữ liệu cơ bản nhất trong Kafka. |
| **Producer (Nhà sản xuất)** | Là một ứng dụng gửi hoặc "xuất bản" tin nhắn đến Kafka. | Chịu trách nhiệm tạo ra và gửi dữ liệu vào hệ thống. |
| **Consumer (Người tiêu dùng)** | Là một ứng dụng đọc hoặc "đăng ký" để nhận tin nhắn từ Kafka. | Chịu trách nhiệm xử lý dữ liệu được gửi đến. |
| **Broker (Máy chủ môi giới)** | Một máy chủ Kafka. Đây là nơi các tin nhắn được lưu trữ và quản lý. | Là trái tim của hệ thống Kafka, xử lý việc nhận, lưu trữ và gửi tin nhắn. |
| **Cluster (Cụm)** | Một nhóm các Broker làm việc cùng nhau. | Đảm bảo tính chịu lỗi (fault tolerance) và khả năng mở rộng (scalability) cao. Nếu một Broker gặp sự cố, các Broker khác sẽ thay thế. |
| **Topic (Chủ đề)** | Một danh mục hoặc một kênh nơi các tin nhắn được xuất bản. Giống như một bảng trong cơ sở dữ liệu. Ví dụ: `user_clicks`, `order_updates`. | Phân loại và tổ chức các luồng dữ liệu. |
| **Partition (Phân vùng)** | Mỗi Topic được chia thành một hoặc nhiều Partition. Đây là cách Kafka cho phép xử lý dữ liệu song song và mở rộng quy mô. Mỗi partition là một chuỗi tin nhắn có thứ tự và bất biến. | Đơn vị của sự song song hóa. Nhiều consumer có thể đọc từ các partition khác nhau của cùng một topic cùng một lúc. |
| **Offset** | Một số nguyên duy nhất định danh cho mỗi tin nhắn trong một partition. | Giúp Consumer theo dõi tin nhắn nào nó đã đọc. |
| **Consumer Group (Nhóm người tiêu dùng)** | Một nhóm các Consumer cùng làm việc để xử lý tin nhắn từ một Topic. | Cho phép cân bằng tải (load balancing) và khả năng chịu lỗi. Kafka đảm bảo rằng mỗi tin nhắn trong một partition chỉ được gửi đến một consumer duy nhất trong nhóm. |

#### **3. Kiến Trúc Hoạt Động Của Kafka**

Bây giờ, hãy kết nối các khái niệm trên lại với nhau:

1.  **Producer** muốn gửi một tin nhắn (ví dụ: "người dùng A đã thêm sản phẩm B vào giỏ hàng").
2.  Producer chỉ định tin nhắn này thuộc **Topic** nào (ví dụ: `cart_events`).
3.  Kafka **Broker** nhận tin nhắn này. Nó sẽ quyết định đưa tin nhắn vào **Partition** nào của topic `cart_events`. Quyết định này có thể dựa vào key của tin nhắn hoặc theo cơ chế round-robin (xoay vòng).
4.  Tin nhắn được lưu trữ trong partition đó và được gán một **Offset** duy nhất.
5.  Một **Consumer** thuộc một **Consumer Group** (ví dụ: `inventory_service_group`) đã đăng ký nhận tin nhắn từ topic `cart_events`.
6.  Broker sẽ gửi tin nhắn từ một partition cho một consumer trong group đó. Consumer sẽ xử lý tin nhắn (ví dụ: cập nhật số lượng tồn kho).
7.  Consumer sẽ lưu lại **Offset** của tin nhắn cuối cùng nó đã xử lý. Nếu consumer này bị lỗi, một consumer khác trong cùng group có thể tiếp tục xử lý từ offset đó, đảm bảo không bị mất dữ liệu.

![Hình 1.png](images/H%C3%ACnh%201.png)*Sơ đồ minh họa kiến trúc Kafka với các thành phần chính.*

---

**Tạm kết Phần 1**

Đến đây, bạn đã có một cái nhìn tổng quan và nền tảng về Kafka. Bạn đã hiểu được:

*   Kafka là gì và tại sao nó quan trọng.
*   Các thành phần cốt lõi như Producer, Consumer, Broker, Topic, Partition, và Consumer Group.
*   Luồng hoạt động cơ bản của một tin nhắn trong hệ thống Kafka.

Trong phần tiếp theo, chúng ta sẽ bắt đầu cài đặt Kafka trên máy của bạn và viết những dòng code đầu tiên để gửi và nhận tin nhắn bằng Java Spring Boot.

---

### **Phần 2: Thực Hành Đầu Tiên với Kafka và Spring Boot**

Mục tiêu của phần này là gửi một tin nhắn từ một điểm cuối API (Producer) và thấy nó được xử lý bởi một dịch vụ khác (Consumer) trong cùng một ứng dụng.

#### **A. Cài Đặt Kafka và Zookeeper Bằng Docker**

Cách dễ dàng và nhanh chóng nhất để chạy Kafka trên máy của bạn là sử dụng Docker. Kafka cần Zookeeper để quản lý trạng thái của cụm (cluster), vì vậy chúng ta sẽ khởi chạy cả hai.

1.  **Tạo file `docker-compose.yml`:**
    Tạo một file mới tên là `docker-compose.yml` và sao chép nội dung dưới đây vào:

    ```yaml
    version: '3.8'
    services:
      zookeeper:
        image: 'bitnami/zookeeper:latest'
        ports:
          - '2181:2181'
        environment:
          - ALLOW_ANONYMOUS_LOGIN=yes
      kafka:
        image: 'bitnami/kafka:latest'
        ports:
          - '9092:9092'
        environment:
          - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181
          - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
          - ALLOW_PLAINTEXT_LISTENER=yes
        depends_on:
          - zookeeper
    ```

    **Giải thích nhanh:**
    *   **zookeeper:** Dịch vụ Zookeeper, chạy trên cổng `2181`.
    *   **kafka:** Dịch vụ Kafka Broker, chạy trên cổng `9092`.
    *   `KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181`: Cho Kafka biết nơi để tìm Zookeeper (chính là dịch vụ `zookeeper` mà Docker Compose đã tạo).
    *   `KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092`: Đây là địa chỉ mà các ứng dụng bên ngoài (như ứng dụng Spring Boot của chúng ta) sẽ sử dụng để kết nối tới Kafka.

2.  **Khởi chạy Kafka:**
    Mở terminal hoặc command prompt trong thư mục chứa file `docker-compose.yml` và chạy lệnh:

    ```bash
    docker-compose up -d
    ```

    Lệnh này sẽ tải các image cần thiết và khởi chạy các container ở chế độ nền. Bạn có thể kiểm tra xem chúng có đang chạy không bằng lệnh `docker ps`.

#### **B. Xây Dựng Ứng Dụng Spring Boot**

1.  **Khởi tạo dự án:**
    Truy cập [Spring Initializr](https://start.spring.io/).
    *   **Project:** Maven Project
    *   **Language:** Java
    *   **Spring Boot:** Chọn một phiên bản ổn định (ví dụ: 3.x.x)
    *   **Group, Artifact:** Đặt tên theo ý bạn (ví dụ: `com.example`, `kafka-demo`)
    *   **Dependencies:** Thêm `Spring Web` và `Spring for Apache Kafka`.

    Nhấn "Generate" để tải về file zip, sau đó giải nén và mở bằng IDE yêu thích của bạn (IntelliJ, Eclipse, VS Code).

2.  **Cấu hình kết nối Kafka:**
    Mở file `src/main/resources/application.properties` và thêm các cấu hình sau:

    ```properties
    # Cấu hình máy chủ Kafka
    spring.kafka.bootstrap-servers=localhost:9092

    # Cấu hình Producer
    spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
    spring.kafka.producer.value-serializer=org.apache.kafka.common.serialization.StringSerializer

    # Cấu hình Consumer
    spring.kafka.consumer.group-id=my-group
    spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
    spring.kafka.consumer.value-deserializer=org.apache.kafka.common.serialization.StringDeserializer
    ```

    **Giải thích:**
    *   `bootstrap-servers`: Địa chỉ của Kafka Broker mà chúng ta đã chạy bằng Docker.
    *   `key/value-serializer`: Producer cần biết cách chuyển đổi key và value (trong ví dụ này là kiểu `String`) thành dạng byte để gửi đi.
    *   `group-id`: Tên định danh cho Consumer Group. Tất cả consumer có cùng `group-id` sẽ cùng nhau xử lý các tin nhắn từ một topic.
    *   `key/value-deserializer`: Consumer cần biết cách chuyển đổi dữ liệu byte nhận về thành các đối tượng (kiểu `String`).

3.  **Tạo Producer:**

    Chúng ta sẽ tạo một service và một controller để gửi tin nhắn.

    *   **`KafkaProducerService.java`**
        ```java
        package com.example.kafkademo;

        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.kafka.core.KafkaTemplate;
        import org.springframework.stereotype.Service;

        @Service
        public class KafkaProducerService {

            // Spring Boot tự động cấu hình KafkaTemplate cho chúng ta
            @Autowired
            private KafkaTemplate<String, String> kafkaTemplate;

            private static final String TOPIC = "my-topic";

            public void sendMessage(String message) {
                System.out.println("Đang gửi tin nhắn: " + message);
                // Gửi tin nhắn đến topic có tên là "my-topic"
                kafkaTemplate.send(TOPIC, message);
            }
        }
        ```

    *   **`MessageController.java`**
        ```java
        package com.example.kafkademo;

        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.web.bind.annotation.PostMapping;
        import org.springframework.web.bind.annotation.RequestBody;
        import org.springframework.web.bind.annotation.RequestMapping;
        import org.springframework.web.bind.annotation.RestController;

        @RestController
        @RequestMapping("/api/messages")
        public class MessageController {

            @Autowired
            private KafkaProducerService producerService;

            @PostMapping
            public String sendMessageToKafka(@RequestBody String message) {
                producerService.sendMessage(message);
                return "Tin nhắn đã được gửi tới Kafka!";
            }
        }
        ```

4.  **Tạo Consumer:**

    Bây giờ chúng ta sẽ tạo một service để lắng nghe và xử lý các tin nhắn từ Kafka.

    *   **`KafkaConsumerService.java`**
        ```java
        package com.example.kafkademo;

        import org.springframework.kafka.annotation.KafkaListener;
        import org.springframework.stereotype.Service;

        @Service
        public class KafkaConsumerService {

            private static final String TOPIC = "my-topic";

            // Annotation @KafkaListener sẽ tự động lắng nghe tin nhắn từ topic được chỉ định
            @KafkaListener(topics = TOPIC, groupId = "my-group")
            public void consume(String message) {
                System.out.println("Đã nhận được tin nhắn: " + message);
                // Tại đây bạn có thể thêm logic xử lý tin nhắn, ví dụ: lưu vào database
            }
        }
        ```
    **Điều kỳ diệu ở `@KafkaListener`:** Annotation này của Spring for Kafka đã làm tất cả công việc nặng nhọc: tự động kết nối, lấy tin nhắn, quản lý offset. Bạn chỉ cần viết logic xử lý tin nhắn thôi.

#### **C. Chạy Thử và Kiểm Tra**

1.  **Chạy ứng dụng Spring Boot:**
    Chạy class chính `KafkaDemoApplication.java` từ IDE của bạn.

2.  **Gửi tin nhắn:**
    Sử dụng một công cụ như Postman, `curl`, hoặc bất kỳ trình duyệt nào có thể gửi request POST.
    *   **URL:** `http://localhost:8080/api/messages`
    *   **Method:** `POST`
    *   **Body (raw, text):** `Xin chào Kafka!`

3.  **Quan sát kết quả:**
    Ngay sau khi bạn gửi request, hãy nhìn vào console của ứng dụng Spring Boot. Bạn sẽ thấy hai dòng log xuất hiện gần như đồng thời:

    ```
    Đang gửi tin nhắn: Xin chào Kafka!
    ...
    Đã nhận được tin nhắn: Xin chào Kafka!
    ```

    Điều này chứng tỏ:
    *   `MessageController` đã nhận request và gọi `KafkaProducerService`.
    *   Producer đã gửi thành công tin nhắn `"Xin chào Kafka!"` đến topic `my-topic`.
    *   `KafkaConsumerService` đã ngay lập tức nhận được tin nhắn từ topic đó và in ra console.

---

**Tạm kết Phần 2**

Xin chúc mừng! Bạn vừa hoàn thành một chu trình gửi-nhận tin nhắn hoàn chỉnh qua Kafka bằng Spring Boot. Bạn đã học được cách:

*   Thiết lập một môi trường Kafka đơn giản với Docker.
*   Cấu hình Spring Boot để kết nối và giao tiếp với Kafka.
*   Sử dụng `KafkaTemplate` để gửi tin nhắn (Producer).
*   Sử dụng `@KafkaListener` để nhận tin nhắn một cách dễ dàng (Consumer).

---
### **Phần 3: Hiểu Sâu Về Topic, Partition và Key**

Phần này sẽ giải thích tại sao Partition là khái niệm cốt lõi cho khả năng mở rộng của Kafka và vai trò của Key trong việc kiểm soát dữ liệu.

#### **A. Đi Sâu Về Topic và Partition**

Hãy nghĩ về một Topic không chỉ như một kênh, mà là một **sổ cái (log) phân tán**. Sổ cái này lại được chia nhỏ thành các **Partition**.

*   **Partition là gì?** Mỗi partition là một chuỗi tin nhắn có thứ tự và **bất biến (immutable)**. Một khi tin nhắn đã được ghi vào một partition, nó không thể thay đổi. Mỗi tin nhắn trong partition được gán một ID tuần tự gọi là `offset`.
*   **Tại sao cần Partition?**
    1.  **Khả năng mở rộng (Scalability):** Đây là lý do quan trọng nhất. Thay vì chỉ có một máy (broker) xử lý toàn bộ tin nhắn cho một topic, Kafka có thể phân phối các partition của topic đó trên nhiều broker khác nhau trong cụm. Điều này cho phép hệ thống của bạn xử lý một lượng lớn dữ liệu đọc/ghi cùng lúc.
    2.  **Tính song song (Parallelism):** Nhiều consumer từ cùng một consumer group có thể đọc dữ liệu từ các partition khác nhau của cùng một topic *cùng một lúc*. Nếu một topic có 4 partition, bạn có thể có tới 4 consumer trong một group hoạt động song song, mỗi consumer xử lý một partition. Điều này tăng tốc độ xử lý lên đáng kể.

    Hãy tưởng tượng một con đường cao tốc (Topic). Thay vì chỉ có một làn đường (1 partition), nó có nhiều làn đường (nhiều partitions). Càng nhiều làn đường, càng nhiều xe (consumers) có thể lưu thông cùng lúc.

    ![Hình 2.png](images/H%C3%ACnh%202.png)*Sơ đồ minh họa một Topic có 3 Partitions và một Consumer Group có 3 Consumers, mỗi consumer xử lý một partition.*

#### **B. Sức Mạnh Của Message Key**

Khi một Producer gửi tin nhắn, nó có thể chỉ định một **Key** (khóa). Key này đóng vai trò cực kỳ quan trọng trong việc quyết định tin nhắn sẽ đi vào partition nào.

*   **Nếu không có Key (Key = null):** Producer sẽ phân phối tin nhắn vào các partition một cách luân phiên (round-robin). Ví dụ: tin nhắn 1 vào partition 0, tin nhắn 2 vào partition 1, tin nhắn 3 vào partition 0, v.v. Điều này giúp cân bằng tải đều trên các partition.
*   **Nếu có Key:** Kafka sẽ sử dụng một công thức băm (hashing) để tính toán partition đích dựa trên key. Công thức cơ bản là: `hash(key) % số_lượng_partitions`.
    *   **Đảm bảo quan trọng:** **Tất cả các tin nhắn có cùng một key sẽ LUÔN LUÔN được gửi đến cùng một partition.**

**Tại sao điều này lại quan trọng đến vậy?**

Hãy xem xét một ví dụ về xử lý đơn hàng:
Một đơn hàng có thể có nhiều sự kiện: `ORDER_CREATED`, `PAYMENT_PROCESSED`, `ORDER_SHIPPED`. Để xử lý chính xác, bạn phải đảm bảo các sự kiện của *cùng một đơn hàng* được xử lý theo đúng thứ tự.

Nếu bạn sử dụng `order_id` làm **key** cho tất cả các sự kiện liên quan đến đơn hàng đó:
*   Sự kiện `ORDER_CREATED` cho `order_id=123` sẽ đi vào partition 2.
*   Sự kiện `PAYMENT_PROCESSED` cho `order_id=123` cũng sẽ đi vào partition 2.
*   Sự kiện `ORDER_SHIPPED` cho `order_id=123` cũng sẽ đi vào partition 2.

Vì thứ tự tin nhắn được đảm bảo *bên trong một partition*, consumer xử lý partition 2 sẽ nhận được các sự kiện này theo đúng trình tự chúng được gửi đi. Điều này ngăn chặn các tình huống lỗi như xử lý "giao hàng" trước khi xử lý "thanh toán".

#### **C. Thực Hành: Nâng Cấp Ví Dụ Spring Boot**

Bây giờ, chúng ta sẽ tạo một topic mới có nhiều partition và sửa đổi code để gửi tin nhắn có key.

1.  **Tạo Topic với nhiều Partition:**
    Chúng ta sẽ sử dụng công cụ dòng lệnh của Kafka bên trong container Docker.
    Mở một terminal mới và chạy lệnh sau để truy cập vào shell của container kafka:

    ```bash
    docker exec -it <tên_container_kafka> /bin/bash
    ```
    (Bạn có thể tìm `<tên_container_kafka>` bằng lệnh `docker ps`).

    Bên trong container, chạy lệnh sau để tạo một topic mới tên là `user-events` với 3 partitions:
    ```bash
    kafka-topics.sh --create \
    --topic user-events \
    --partitions 3 \
    --replication-factor 1 \
    --bootstrap-server localhost:9092
    ```
    *   `--partitions 3`: Chỉ định số lượng partition.
    *   `--replication-factor 1`: Chỉ định số bản sao của mỗi partition. Trong môi trường production, bạn nên đặt > 1 để đảm bảo tính chịu lỗi. Vì chúng ta chỉ có một broker nên đặt là 1.

    Bạn có thể kiểm tra lại bằng lệnh: `kafka-topics.sh --describe --topic user-events --bootstrap-server localhost:9092`.

2.  **Sửa đổi Producer (Spring Boot):**
    Chúng ta sẽ sửa `KafkaProducerService` và `MessageController` để gửi tin nhắn với key.

    *   **`MessageController.java`**
        Sửa đổi để nhận một key và một message.
        ```java
        package com.example.kafkademo;
        // ... imports
        import org.springframework.web.bind.annotation.RequestParam;

        @RestController
        @RequestMapping("/api/messages")
        public class MessageController {

            @Autowired
            private KafkaProducerService producerService;

            // POST /api/messages?key=user1
            // Body: "Logged in"
            @PostMapping
            public String sendMessageToKafka(
                @RequestParam("key") String key,
                @RequestBody String message) {
                producerService.sendMessage(key, message);
                return "Tin nhắn với key '" + key + "' đã được gửi!";
            }
        }
        ```
    *   **`KafkaProducerService.java`**
        Sửa đổi phương thức `sendMessage` để chấp nhận và sử dụng key.
        ```java
        package com.example.kafkademo;
        // ... imports

        @Service
        public class KafkaProducerService {

            @Autowired
            private KafkaTemplate<String, String> kafkaTemplate;

            private static final String TOPIC = "user-events"; // Đổi sang topic mới

            public void sendMessage(String key, String message) {
                System.out.println("Đang gửi tin nhắn với key '" + key + "': " + message);
                // Phương thức send bây giờ có 3 tham số: topic, key, value
                kafkaTemplate.send(TOPIC, key, message);
            }
        }
        ```

3.  **Sửa đổi Consumer (Spring Boot):**
    Cập nhật consumer để lắng nghe topic mới và in ra cả key và partition nhận được.

    *   **`KafkaConsumerService.java`**
        ```java
        package com.example.kafkademo;

        import org.springframework.kafka.annotation.KafkaListener;
        import org.springframework.kafka.support.KafkaHeaders;
        import org.springframework.messaging.handler.annotation.Header;
        import org.springframework.messaging.handler.annotation.Payload;
        import org.springframework.stereotype.Service;

        @Service
        public class KafkaConsumerService {

            // Lắng nghe trên topic "user-events"
            @KafkaListener(topics = "user-events", groupId = "my-group")
            public void consume(
                @Payload String message,
                @Header(KafkaHeaders.RECEIVED_KEY) String key,
                @Header(KafkaHeaders.RECEIVED_PARTITION) int partition) {

                System.out.println(String.format(
                    "Đã nhận tin nhắn: [Key: %s, Message: %s, Partition: %d]",
                    key, message, partition
                ));
            }
        }
        ```

4.  **Chạy và Thử nghiệm:**
    *   Khởi động lại ứng dụng Spring Boot của bạn.
    *   Sử dụng Postman, gửi vài request POST đến `http://localhost:8080/api/messages?key=user1` với các nội dung body khác nhau ("Logged in", "Viewed page X", "Logged out").
    *   Tiếp tục gửi vài request khác với `key=user2`.

    **Quan sát console:**
    Bạn sẽ thấy rằng tất cả các tin nhắn có `key=user1` đều được xử lý trên cùng một partition. Tương tự, tất cả các tin nhắn có `key=user2` cũng sẽ nhất quán trên một partition khác.

    ```
    Đang gửi tin nhắn với key 'user1': Logged in
    Đang gửi tin nhắn với key 'user2': Clicked button A
    Đang gửi tin nhắn với key 'user1': Viewed page X
    ...
    Đã nhận tin nhắn: [Key: user1, Message: Logged in, Partition: 1]
    Đã nhận tin nhắn: [Key: user2, Message: Clicked button A, Partition: 0]
    Đã nhận tin nhắn: [Key: user1, Message: Viewed page X, Partition: 1]
    ```

#### **D. Giới Thiệu Kafka với Go (Ví dụ Producer)**

Để bạn có cái nhìn đa dạng, đây là một ví dụ về một Producer đơn giản bằng Go sử dụng thư viện `confluent-kafka-go`.

1.  **Cài đặt thư viện:**
    ```bash
    go get github.com/confluentinc/confluent-kafka-go/v2/kafka
    ```
2.  **Code Producer (`main.go`):**
    ```go
    package main

    import (
        "fmt"
        "github.com/confluentinc/confluent-kafka-go/v2/kafka"
        "time"
    )

    func main() {
        // Cấu hình Producer
        p, err := kafka.NewProducer(&kafka.ConfigMap{"bootstrap.servers": "localhost:9092"})
        if err != nil {
            panic(err)
        }
        defer p.Close()

        // Kênh để nhận các báo cáo gửi tin nhắn (thành công hay thất bại)
        go func() {
            for e := range p.Events() {
                switch ev := e.(type) {
                case *kafka.Message:
                    if ev.TopicPartition.Error != nil {
                        fmt.Printf("Gửi tin nhắn thất bại: %v\n", ev.TopicPartition.Error)
                    } else {
                        fmt.Printf("Đã gửi tin nhắn tới topic %s [partition %d] @ offset %v\n",
                            *ev.TopicPartition.Topic, ev.TopicPartition.Partition, ev.TopicPartition.Offset)
                    }
                }
            }
        }()

        topic := "user-events"
        users := []string{"user-go-1", "user-go-2", "user-go-1"}

        for _, user := range users {
            message := "Event generated at " + time.Now().String()
            p.Produce(&kafka.Message{
                TopicPartition: kafka.TopicPartition{Topic: &topic, Partition: kafka.PartitionAny},
                Key:            []byte(user),
                Value:          []byte(message),
            }, nil)
        }

        // Đợi tất cả tin nhắn được gửi đi
        p.Flush(15 * 1000)
        fmt.Println("Đã gửi xong!")
    }
    ```
    Khi bạn chạy chương trình Go này, nó sẽ gửi 3 tin nhắn đến topic `user-events`. Hãy quan sát console của ứng dụng Spring Boot, bạn sẽ thấy Consumer cũng nhận và xử lý các tin nhắn này, và hai tin nhắn có key `user-go-1` sẽ vào cùng một partition.

---

**Tạm kết Phần 3**

Bạn đã đi sâu vào trái tim của Kafka. Giờ đây bạn đã hiểu:

*   Topic được chia thành các partition để đạt được khả năng mở rộng và xử lý song song.
*   Thứ tự tin nhắn chỉ được đảm bảo *bên trong một partition*.
*   Sử dụng **Key** là cách để đảm bảo các tin nhắn liên quan được xử lý theo đúng thứ tự bằng cách đưa chúng vào cùng một partition.
*   Bạn đã thực hành điều này với Spring Boot và xem qua một ví dụ Producer bằng Go.

---
### **Phần 4: Sức Mạnh Của Consumer Group, Rebalancing và Quản Lý Offset**

Đây là phần cốt lõi giúp Kafka trở thành một hệ thống đáng tin cậy và có khả năng mở rộng cao ở phía người tiêu dùng.

#### **A. Consumer Group - Đội Quân Xử Lý Dữ Liệu**

Như đã giới thiệu ở Phần 1, **Consumer Group** là một nhóm các consumer cùng chia sẻ một `group.id`. Hãy coi chúng như một đội công nhân được giao nhiệm vụ xử lý công việc từ một topic. Kafka hoạt động với hai quy tắc vàng sau:

1.  **Một partition chỉ được gán cho TỐI ĐA một consumer trong cùng một group tại một thời điểm.**
2.  Nếu số consumer trong một group nhiều hơn số partition của topic, các consumer thừa sẽ ở trạng thái **nhàn rỗi (idle)** và không nhận được bất kỳ tin nhắn nào.

**Điều này mang lại hai lợi ích chính:**

*   **Cân bằng tải (Load Balancing):** Kafka tự động phân chia các partition cho các consumer trong group. Nếu topic `user-events` của chúng ta có 3 partition và group `my-group` có 3 consumer, mỗi consumer sẽ xử lý một partition. Công việc được chia đều.
*   **Tính chịu lỗi (Fault Tolerance):** Nếu một consumer trong group bị lỗi hoặc tắt đi, Kafka sẽ phát hiện điều này và tự động gán lại partition của consumer đó cho một consumer còn lại trong group. Quá trình xử lý vẫn tiếp tục mà không bị gián đoạn.

    ![Hình 3.png](images/H%C3%ACnh%203.png)*Sơ đồ minh họa: (Trái) 3 partitions được chia đều cho 3 consumers. (Phải) Khi Consumer 2 lỗi, partition của nó được gán lại cho Consumer 3.*

#### **B. Rebalancing - Khi "Đội Hình" Thay Đổi**

Quá trình Kafka tự động gán lại các partition cho các consumer trong một group được gọi là **Rebalance** (Tái cân bằng). Một cuộc Rebalance sẽ xảy ra khi:

*   Một consumer mới **tham gia** vào group (Scaling out - Mở rộng quy mô).
*   Một consumer **rời khỏi** group (do tắt đi, bị lỗi, hoặc bị ngắt kết nối quá lâu).
*   Số lượng partition của topic thay đổi.

**Rebalancing là một quá trình cực kỳ mạnh mẽ, nhưng cũng có mặt trái:** Trong suốt quá trình Rebalance, tất cả các consumer trong group sẽ tạm dừng xử lý tin nhắn. Điều này được gọi là "stop-the-world". Vì vậy, trong các hệ thống yêu cầu độ trễ cực thấp, việc Rebalance diễn ra thường xuyên là điều cần tránh.

#### **C. Quản Lý Offset - "Đánh Dấu Trang Sách"**

Câu hỏi quan trọng là: Làm thế nào một consumer biết nó đã đọc đến đâu trong một partition? Đặc biệt là sau một cuộc Rebalance, làm thế nào consumer mới được gán partition biết phải bắt đầu đọc từ đâu?

Câu trả lời là **Offset Committing**.

*   **Offset** là vị trí của tin nhắn trong partition.
*   **Commit Offset** là hành động consumer thông báo cho Kafka: "Tôi đã xử lý thành công tất cả các tin nhắn cho đến offset này".

Kafka lưu trữ offset đã được commit của mỗi consumer group cho mỗi partition vào một topic nội bộ đặc biệt tên là `__consumer_offsets`. Khi một consumer khởi động hoặc được gán một partition mới, nó sẽ hỏi Kafka: "Offset cuối cùng mà group của tôi đã commit cho partition này là gì?" và bắt đầu đọc từ đó.

**Delivery Semantics (Ngữ nghĩa phân phối):**

Cách bạn quyết định *khi nào* commit offset sẽ quyết định đến độ tin cậy của hệ thống. Có ba ngữ nghĩa chính:

1.  **At most once (Tối đa một lần):** Commit offset ngay khi nhận được tin nhắn, *trước khi* xử lý nó. Nếu consumer bị lỗi trong lúc xử lý, tin nhắn đó sẽ bị mất vì Kafka nghĩ rằng nó đã được xử lý rồi. (Nhanh nhưng không an toàn).
2.  **At least once (Ít nhất một lần):** Xử lý tin nhắn trước, *sau đó* mới commit offset. Nếu consumer bị lỗi sau khi xử lý xong nhưng trước khi commit, khi khởi động lại, nó sẽ nhận lại và xử lý lại tin nhắn đó. Điều này có thể dẫn đến việc xử lý trùng lặp. (An toàn, là **mặc định** của Kafka và phổ biến nhất).
3.  **Exactly once (Chính xác một lần):** Đảm bảo mỗi tin nhắn được xử lý chính xác một lần. Đây là một cơ chế phức tạp hơn, thường liên quan đến Kafka Streams hoặc sử dụng các producer/consumer giao tác (transactional).

#### **D. Thực Hành: Mở Rộng Consumer và Quản Lý Offset trong Spring Boot**

Spring for Kafka làm cho việc quản lý offset trở nên rất đơn giản. Mặc định, nó sử dụng cơ chế "at-least-once".

1.  **Cơ chế Commit mặc định của Spring:**
    Khi bạn dùng `@KafkaListener`, Spring Boot sẽ tự động commit offset cho bạn sau khi phương thức listener thực thi thành công mà không ném ra ngoại lệ. Cấu hình mặc định là `enable.auto.commit=false` ở cấp độ client Kafka, nhưng Listener Container của Spring sẽ xử lý việc commit một cách đồng bộ sau khi xử lý batch tin nhắn. Điều này mang lại sự an toàn của "at-least-once".

2.  **Thực hành Scaling Out:**
    Bây giờ, hãy chứng kiến sự kỳ diệu của Rebalancing. Chúng ta sẽ chạy hai phiên bản của cùng một ứng dụng consumer để xem chúng chia sẻ công việc như thế nào.

    *   **Bước 1:** Đảm bảo ứng dụng Spring Boot của bạn đang chạy.
    *   **Bước 2:** Mở terminal, điều hướng đến thư mục gốc của dự án và chạy lệnh sau để build file jar:
        ```bash
        ./mvnw clean package
        ```
    *   **Bước 3:** Chạy phiên bản thứ hai của ứng dụng trên một cổng khác để tránh xung đột.
        ```bash
        java -jar target/kafka-demo-0.0.1-SNAPSHOT.jar --server.port=8081
        ```
        (Nhớ thay `kafka-demo-0.0.1-SNAPSHOT.jar` bằng tên file jar thực tế của bạn).

    Bây giờ bạn đang có **2 instances** của ứng dụng chạy cùng lúc. Cả hai đều có cùng `group.id` là `my-group`. Ngay khi instance thứ hai khởi động xong, bạn sẽ thấy một số log về Rebalancing trong console của cả hai.

    *   **Bước 4:** Sử dụng Postman để gửi nhiều tin nhắn liên tục với các key khác nhau (ví dụ: `user1`, `user2`, `user3`, `user4`...) đến topic `user-events`.

    **Quan sát kết quả:**
    Bạn sẽ thấy rằng các tin nhắn bây giờ được xử lý xen kẽ bởi cả hai ứng dụng! Ví dụ, console của ứng dụng chạy trên cổng 8080 có thể in ra:
    ```
    Đã nhận tin nhắn: [Key: user1, Message: ..., Partition: 1]
    Đã nhận tin nhắn: [Key: user4, Message: ..., Partition: 2]
    ```
    Trong khi đó, console của ứng dụng chạy trên cổng 8081 lại in ra:
    ```
    Đã nhận tin nhắn: [Key: user2, Message: ..., Partition: 0]
    Đã nhận tin nhắn: [Key: user3, Message: ..., Partition: 0]
    ```
    Kafka đã tự động phân chia 3 partitions cho 2 consumers. Một consumer có thể nhận 1 partition và consumer còn lại nhận 2 partitions. Nếu bạn khởi động instance thứ ba, mỗi consumer sẽ nhận đúng một partition.

3.  **(Nâng cao) Commit Offset Thủ Công:**
    Trong một số trường hợp, bạn muốn kiểm soát chính xác thời điểm commit offset (ví dụ: sau khi đã lưu thành công vào database). Spring Kafka hỗ trợ điều này.

    *   **Cấu hình:**
        Trong `application.properties`, tắt chế độ commit tự động của container:
        ```properties
        spring.kafka.listener.ack-mode=MANUAL_IMMEDIATE
        ```
    *   **Sửa đổi Consumer:**
        Thay đổi `KafkaConsumerService` để nhận đối tượng `Acknowledgment`.
        ```java
        package com.example.kafkademo;

        import org.springframework.kafka.annotation.KafkaListener;
        import org.springframework.kafka.support.Acknowledgment; // Import
        import org.springframework.kafka.support.KafkaHeaders;
        import org.springframework.messaging.handler.annotation.Header;
        import org.springframework.messaging.handler.annotation.Payload;
        import org.springframework.stereotype.Service;

        @Service
        public class KafkaConsumerService {

            @KafkaListener(topics = "user-events", groupId = "my-group")
            public void consume(
                @Payload String message,
                @Header(KafkaHeaders.RECEIVED_PARTITION) int partition,
                Acknowledgment acknowledgment) { // Thêm Acknowledgment

                System.out.println(String.format(
                    "Đã nhận tin nhắn: [%s] từ partition %d", message, partition
                ));

                // Logic xử lý quan trọng ở đây, ví dụ: lưu vào DB
                // ...

                // Chỉ commit offset sau khi đã xử lý xong
                acknowledgment.acknowledge();
                System.out.println("Đã commit offset.");
            }
        }
        ```
    Với cách này, bạn có toàn quyền kiểm soát. Nếu có lỗi xảy ra trước khi `acknowledgment.acknowledge()` được gọi, offset sẽ không được commit, và tin nhắn sẽ được gửi lại trong lần poll tiếp theo.

---

**Tạm kết Phần 4**

Bạn đã nắm vững các khái niệm vận hành quan trọng nhất của Kafka consumer:

*   **Consumer Groups** cho phép mở rộng quy mô và khả năng chịu lỗi bằng cách phân chia các partition.
*   **Rebalancing** là cơ chế tự động giúp các group thích ứng với sự thay đổi về số lượng consumer.
*   **Offset Committing** là chìa khóa cho việc xử lý dữ liệu đáng tin cậy, đảm bảo không bỏ sót tin nhắn.
*   Bạn đã thực hành việc mở rộng consumer với Spring Boot và tìm hiểu về cách commit offset thủ công để tăng cường độ tin cậy.

---
### **Phần 5: Định Dạng Dữ Liệu và Schema Registry - Hợp Đồng Bất Biến**

#### **A. Vấn Đề Của Việc Dùng String và JSON**

Hãy tưởng tượng Producer gửi một tin nhắn JSON: `{"user_id": 123, "product_name": "Laptop", "quantity": 1}`.

1.  **Thiếu An Toàn Kiểu (No Type Safety):** Consumer nhận được chuỗi này và phải tự mình phân tích (parse). Điều gì xảy ra nếu một ngày, team Producer đổi tên trường `user_id` thành `userId`? Consumer sẽ không thể parse được và gây ra lỗi runtime. Hệ thống của bạn rất **dễ vỡ (brittle)**.
2.  **Thiếu Ràng Buộc (No Contract):** Không có một "hợp đồng" chính thức nào quy định cấu trúc của tin nhắn. Producer có thể thêm, bớt, hoặc đổi kiểu dữ liệu của các trường một cách tùy ý, và Consumer sẽ không biết được cho đến khi quá muộn.
3.  **Kém Hiệu Quả:** JSON là định dạng văn bản, nó dài dòng và tốn nhiều tài nguyên (CPU, băng thông mạng, không gian lưu trữ) để xử lý hơn so với các định dạng nhị phân.

#### **B. Giải Pháp: Định Dạng Nhị Phân và "Bản Hợp Đồng" Schema**

Để giải quyết vấn đề này, cộng đồng Kafka thường sử dụng các định dạng dữ liệu nhị phân có schema đi kèm. Hai định dạng phổ biến nhất là **Avro** và **Protobuf**. Chúng ta sẽ tập trung vào **Apache Avro** vì nó được tích hợp rất chặt chẽ với hệ sinh thái Kafka.

*   **Avro là gì?** Avro là một hệ thống tuần tự hóa dữ liệu (data serialization). Nó định nghĩa dữ liệu bằng một **schema** (thường là file JSON). Dữ liệu sau đó được tuần tự hóa thành dạng nhị phân nhỏ gọn.
*   **Schema là gì?** Schema chính là bản "hợp đồng" mà chúng ta nói đến. Nó định nghĩa rõ ràng các trường, kiểu dữ liệu của chúng, và có thể cả giá trị mặc định.

Nhưng nếu chỉ có schema thì vẫn chưa đủ. Nếu Producer dùng schema phiên bản 2, trong khi Consumer vẫn đang dùng schema phiên bản 1 thì sao? Đây là lúc **Schema Registry** tỏa sáng.

*   **Schema Registry là gì?** Schema Registry là một dịch vụ độc lập, hoạt động như một kho lưu trữ trung tâm cho tất cả các schema của bạn.
    *   Producer, trước khi gửi tin nhắn, sẽ đăng ký schema của dữ liệu với Schema Registry. Registry sẽ trả về một ID duy nhất cho schema đó. Producer sẽ đính kèm ID này vào tin nhắn (chỉ là một con số nhỏ) và gửi đi.
    *   Consumer, khi nhận tin nhắn, sẽ đọc ID đó. Nếu nó chưa có schema tương ứng, nó sẽ hỏi Schema Registry: "Schema có ID là X là gì?" và dùng schema đó để giải mã dữ liệu.

**Lợi ích vàng của Schema Registry:** Nó thực thi các **quy tắc tương thích (compatibility rules)**. Ví dụ, khi bạn cố gắng đăng ký một schema phiên bản mới, Schema Registry có thể kiểm tra xem nó có tương thích ngược (backward compatible) với phiên bản cũ hay không. Điều này ngăn chặn việc triển khai các thay đổi có thể làm sập các consumer đang chạy.

#### **C. Thực Hành: Tích Hợp Avro và Schema Registry vào Dự Án Spring Boot**

1.  **Cập nhật `docker-compose.yml`:**
    Thêm dịch vụ `schema-registry` vào file của bạn.

    ```yaml
    version: '3.8'
    services:
      zookeeper:
        image: 'bitnami/zookeeper:latest'
        ports:
          - '2181:2181'
        environment:
          - ALLOW_ANONYMOUS_LOGIN=yes
      kafka:
        image: 'bitnami/kafka:latest'
        ports:
          - '9092:9092'
        environment:
          - KAFKA_CFG_ZOOKEEPER_CONNECT=zookeeper:2181
          - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://localhost:9092
          - ALLOW_PLAINTEXT_LISTENER=yes
        depends_on:
          - zookeeper
      # DỊCH VỤ MỚI
      schema-registry:
        image: 'bitnami/schema-registry:latest'
        ports:
          - '8085:8085' # Cổng mặc định của Schema Registry
        environment:
          - SCHEMA_REGISTRY_KAFKA_BROKERS=PLAINTEXT://kafka:9092
        depends_on:
          - kafka
    ```
    Dừng các container cũ (`docker-compose down`) và khởi động lại (`docker-compose up -d`).

2.  **Định nghĩa Schema Avro:**
    Trong dự án Spring Boot, tạo một thư mục `src/main/resources/avro`. Bên trong đó, tạo file `order.avsc`:

    ```json
    {
      "namespace": "com.example.kafkademo.avro",
      "type": "record",
      "name": "Order",
      "fields": [
        { "name": "id", "type": "string" },
        { "name": "product", "type": "string" },
        { "name": "quantity", "type": "int" }
      ]
    }
    ```

3.  **Cấu hình Maven để tự động tạo Class Java:**
    Mở file `pom.xml` của bạn. Thêm thư viện của Confluent và Avro, sau đó thêm plugin `avro-maven-plugin`.

    ```xml
    <!-- Thêm vào thẻ <properties> -->
    <confluent.version>7.3.0</confluent.version>
    <avro.version>1.11.1</avro.version>

    <!-- Thêm vào thẻ <dependencies> -->
    <dependency>
        <groupId>org.apache.avro</groupId>
        <artifactId>avro</artifactId>
        <version>${avro.version}</version>
    </dependency>
    <dependency>
        <groupId>io.confluent</groupId>
        <artifactId>kafka-avro-serializer</artifactId>
        <version>${confluent.version}</version>
    </dependency>

    <!-- Thêm vào thẻ <build><plugins> -->
    <plugin>
        <groupId>org.apache.avro</groupId>
        <artifactId>avro-maven-plugin</artifactId>
        <version>${avro.version}</version>
        <executions>
            <execution>
                <phase>generate-sources</phase>
                <goals>
                    <goal>schema</goal>
                </goals>
                <configuration>
                    <sourceDirectory>${project.basedir}/src/main/resources/avro/</sourceDirectory>
                    <outputDirectory>${project.basedir}/src/main/java/</outputDirectory>
                </configuration>
            </execution>
        </executions>
    </plugin>
    ```
    **QUAN TRỌNG:** Sau khi thêm vào `pom.xml`, hãy chạy `mvn clean install` từ terminal hoặc refresh Maven project trong IDE của bạn. Plugin sẽ tự động tạo ra class `com.example.kafkademo.avro.Order.java`.

4.  **Cập nhật `application.properties`:**
    Thay đổi serializer/deserializer và thêm URL của Schema Registry.

    ```properties
    spring.kafka.bootstrap-servers=localhost:9092

    # Đổi serializer/deserializer sang của Avro
    spring.kafka.producer.key-serializer=org.apache.kafka.common.serialization.StringSerializer
    spring.kafka.producer.value-serializer=io.confluent.kafka.serializers.KafkaAvroSerializer
    spring.kafka.consumer.key-deserializer=org.apache.kafka.common.serialization.StringDeserializer
    spring.kafka.consumer.value-deserializer=io.confluent.kafka.serializers.KafkaAvroDeserializer

    spring.kafka.consumer.group-id=my-group
    # Bắt buộc phải có: chỉ cho client biết Schema Registry ở đâu
    spring.kafka.properties.schema.registry.url=http://localhost:8085
    # Cấu hình này giúp Deserializer biết được class cụ thể để map dữ liệu vào
    spring.kafka.consumer.properties.specific.avro.reader=true
    ```

5.  **Sửa đổi Code:**
    *   **`KafkaProducerService.java`:**
        Thay đổi `KafkaTemplate` và phương thức `sendMessage` để làm việc với đối tượng `Order`.
        ```java
        package com.example.kafkademo;

        import com.example.kafkademo.avro.Order; // Import class vừa được tạo
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.kafka.core.KafkaTemplate;
        import org.springframework.stereotype.Service;

        @Service
        public class KafkaProducerService {

            // Thay đổi kiểu dữ liệu của value thành Order
            @Autowired
            private KafkaTemplate<String, Order> kafkaTemplate;

            private static final String TOPIC = "orders"; // Dùng một topic mới cho rõ ràng

            public void sendOrder(Order order) {
                System.out.println("Đang gửi đơn hàng: " + order.toString());
                kafkaTemplate.send(TOPIC, order.getId().toString(), order);
            }
        }
        ```
    *   **`MessageController.java` (Sửa lại cho phù hợp):**
        ```java
        package com.example.kafkademo;

        import com.example.kafkademo.avro.Order;
        import org.springframework.beans.factory.annotation.Autowired;
        import org.springframework.web.bind.annotation.PostMapping;
        import org.springframework.web.bind.annotation.RequestMapping;
        import org.springframework.web.bind.annotation.RestController;
        import java.util.UUID;

        @RestController
        @RequestMapping("/api/orders")
        public class OrderController { // Đổi tên cho phù hợp

            @Autowired
            private KafkaProducerService producerService;

            @PostMapping
            public String sendRandomOrder() {
                Order order = Order.newBuilder()
                    .setId(UUID.randomUUID().toString())
                    .setProduct("Product-" + (int)(Math.random() * 100))
                    .setQuantity((int)(Math.random() * 10) + 1)
                    .build();

                producerService.sendOrder(order);
                return "Đơn hàng đã được gửi!";
            }
        }
        ```
    *   **`KafkaConsumerService.java`:**
        Cập nhật `@KafkaListener` để nhận trực tiếp đối tượng `Order`.
        ```java
        package com.example.kafkademo;

        import com.example.kafkademo.avro.Order; // Import
        import org.springframework.kafka.annotation.KafkaListener;
        import org.springframework.stereotype.Service;

        @Service
        public class KafkaConsumerService {

            // Lắng nghe trên topic "orders" và nhận đối tượng Order
            @KafkaListener(topics = "orders", groupId = "my-group")
            public void consume(Order order) {
                System.out.println("Đã nhận được đơn hàng:");
                System.out.println("  ID: " + order.getId());
                System.out.println("  Product: " + order.getProduct());
                System.out.println("  Quantity: " + order.getQuantity());
            }
        }
        ```

6.  **Chạy và Kiểm tra:**
    *   Khởi động lại ứng dụng Spring Boot.
    *   Dùng Postman gửi một request `POST` đến `http://localhost:8080/api/orders`.
    *   Quan sát console: Bạn sẽ thấy log gửi đi là một đối tượng `Order` và log nhận được cũng là một đối tượng `Order` đã được parse hoàn chỉnh, an toàn.

---

**Tạm kết Phần 5**

Bạn vừa thực hiện một bước tiến khổng lồ trong việc xây dựng một hệ thống Kafka bền vững và an toàn. Giờ đây bạn đã hiểu và biết cách:

*   Nhận ra những rủi ro của việc sử dụng các định dạng dữ liệu không có cấu trúc như String/JSON.
*   Sử dụng **Avro** để định nghĩa một "hợp đồng" dữ liệu rõ ràng.
*   Thiết lập và sử dụng **Schema Registry** để quản lý các "hợp đồng" đó và đảm bảo tính tương thích.
*   Tích hợp toàn bộ quy trình này vào một ứng dụng Spring Boot, từ việc tự động tạo code từ schema cho đến việc gửi và nhận các đối tượng được định kiểu mạnh.

---
### **Phần 6: Xử Lý Lỗi Nâng Cao - Dead Letter Topic (DLT)**

#### **A. Vấn Đề: Tin Nhắn "Độc" (Poison Pill)**

Hãy tưởng tượng kịch bản sau:
1.  Consumer của bạn nhận một tin nhắn `Order`.
2.  Khi xử lý, nó cố gắng gọi một dịch vụ bên ngoài để kiểm tra tồn kho, nhưng dịch vụ đó bị lỗi. Hoặc tệ hơn, dữ liệu trong tin nhắn bị sai (ví dụ: `quantity = -1`) khiến code của bạn ném ra một `Exception`.
3.  Vì có lỗi, consumer không commit offset của tin nhắn này.
4.  Ở lần poll tiếp theo, Kafka thấy rằng offset chưa được commit và **gửi lại chính tin nhắn đó**.
5.  Quá trình lặp lại: Consumer nhận tin nhắn -> lỗi -> không commit -> Kafka gửi lại.

Kết quả là consumer của bạn bị kẹt trong một vòng lặp vô tận, cố gắng xử lý một tin nhắn không thể xử lý được. Tin nhắn này được gọi là "poison pill". Điều tồi tệ nhất là nó làm **chặn toàn bộ partition**. Không một tin nhắn nào đến sau trong partition đó có thể được xử lý. Hệ thống của bạn bị đình trệ.

#### **B. Giải Pháp: Mô Hình Retry và Dead Letter Topic (DLT)**

Giải pháp không phải là bỏ qua tin nhắn lỗi, mà là xử lý nó một cách thông minh. Mô hình này bao gồm hai phần:

1.  **Retry (Thử lại):** Không phải lỗi nào cũng là vĩnh viễn. Có thể do kết nối mạng tạm thời chập chờn. Vì vậy, chiến lược tốt là thử xử lý lại tin nhắn một vài lần với một khoảng thời gian chờ giữa các lần thử.
2.  **Dead Letter Topic (DLT):** Nếu sau một số lần thử lại mà tin nhắn vẫn không thể xử lý được, chúng ta kết luận rằng nó có vấn đề thực sự. Thay vì bị kẹt, consumer sẽ **gửi tin nhắn đó đến một topic đặc biệt gọi là "Dead Letter Topic"**.

Sau khi gửi tin nhắn "độc" sang DLT, consumer sẽ commit offset của nó và tiếp tục xử lý các tin nhắn tiếp theo trong partition. Dòng chảy dữ liệu chính không còn bị chặn nữa.

![Hình 4.png](images/H%C3%ACnh%204.png)*Sơ đồ minh họa: Consumer thử lại 3 lần, nếu vẫn lỗi, nó sẽ gửi tin nhắn đến! DLT và tiếp tục xử lý tin nhắn tiếp theo.*

#### **C. Triển Khai DLT và Retries với Spring Kafka**

Spring for Kafka cung cấp sự hỗ trợ tuyệt vời cho mô hình này mà không cần quá nhiều code phức tạp.

1.  **Cập nhật `application.properties`:**
    Chúng ta cần một vài điều chỉnh nhỏ.
    ```properties
    # ... các cấu hình cũ ...

    # Đảm bảo listener container quản lý ack để retry hoạt động chính xác
    spring.kafka.listener.ack-mode=RECORD

    # (Tùy chọn nhưng khuyến khích) Tăng số lượng consumer thread
    # để xử lý song song các partition.
    spring.kafka.listener.concurrency=3
    ```

2.  **Tạo Lớp Cấu Hình (`KafkaConfig.java`):**
    Đây là nơi chúng ta sẽ định nghĩa logic retry và DLT. Tạo một class mới:
    ```java
    package com.example.kafkademo.config;

    import org.apache.kafka.clients.producer.ProducerRecord;
    import org.apache.kafka.common.header.internals.RecordHeader;
    import org.springframework.beans.factory.annotation.Value;
    import org.springframework.context.annotation.Bean;
    import org.springframework.context.annotation.Configuration;
    import org.springframework.kafka.core.KafkaTemplate;
    import org.springframework.kafka.listener.DeadLetterPublishingRecoverer;
    import org.springframework.kafka.listener.DefaultErrorHandler;
    import org.springframework.util.backoff.FixedBackOff;

    @Configuration
    public class KafkaConfig {

        @Value("${spring.kafka.bootstrap-servers}")
        private String bootstrapServers;

        // Định nghĩa Error Handler
        @Bean
        public DefaultErrorHandler errorHandler(KafkaTemplate<String, Object> template) {
            // Logic để gửi tin nhắn lỗi đến DLT
            DeadLetterPublishingRecoverer recoverer = new DeadLetterPublishingRecoverer(template,
                // Function này quyết định topic DLT dựa trên record gốc
                (record, ex) -> {
                    // Thêm header để biết lý do lỗi và nguồn gốc
                    record.headers().add(new RecordHeader("X-Exception-Message", ex.getMessage().getBytes()));
                    record.headers().add(new RecordHeader("X-Original-Topic", record.topic().getBytes()));
                    return new ProducerRecord<>(record.topic() + ".DLT", record.key(), record.value());
                }
            );

            // Cấu hình retry: thử lại 3 lần, mỗi lần cách nhau 1 giây
            FixedBackOff backOff = new FixedBackOff(1000L, 2); // 1s interval, max 2 retries => total 3 attempts
            return new DefaultErrorHandler(recoverer, backOff);
        }
    }
    ```
    **Giải thích:**
    *   Chúng ta tạo một `DefaultErrorHandler`. Spring Kafka sẽ tự động áp dụng bean này cho tất cả các `@KafkaListener`.
    *   `DeadLetterPublishingRecoverer`: Đây là "người phục hồi" sẽ được gọi sau khi tất cả các lần thử lại thất bại. Nó sử dụng một `KafkaTemplate` để gửi tin nhắn đến một topic khác.
    *   `record.topic() + ".DLT"`: Chúng ta định nghĩa một quy ước đặt tên đơn giản: DLT của một topic sẽ có tên là `tên-topic-gốc.DLT`.
    *   `FixedBackOff(1000L, 2)`: Cấu hình chính sách retry. `1000L` là 1000ms (1 giây) chờ giữa các lần thử. `2` là số lần thử lại tối đa. Tổng cộng sẽ có 1 lần thử ban đầu + 2 lần thử lại = 3 lần thử.

3.  **Simulate Lỗi trong Consumer:**
    Sửa đổi `KafkaConsumerService` để giả lập một lỗi có thể xảy ra.
    ```java
    package com.example.kafkademo;

    import com.example.kafkademo.avro.Order;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.kafka.annotation.KafkaListener;
    import org.springframework.stereotype.Service;

    @Service
    public class KafkaConsumerService {

        private static final Logger logger = LoggerFactory.getLogger(KafkaConsumerService.class);

        @KafkaListener(topics = "orders", groupId = "my-group")
        public void consume(Order order) {
            logger.info("Đang xử lý đơn hàng: {}", order.getId());

            // GIẢ LẬP LỖI: Nếu tên sản phẩm chứa từ "FAIL", ném ra exception
            if (order.getProduct().toString().contains("FAIL")) {
                logger.error("Lỗi cố ý! Không thể xử lý đơn hàng cho sản phẩm: {}", order.getProduct());
                throw new RuntimeException("Sản phẩm không hợp lệ!");
            }

            logger.info("Xử lý thành công đơn hàng: {}", order.getId());
        }
    }
    ```

4.  **Cập nhật Controller để gửi tin nhắn lỗi:**
    Sửa `OrderController` để có thể gửi cả tin nhắn hợp lệ và tin nhắn lỗi.
    ```java
    package com.example.kafkademo;
    // ... imports

    @RestController
    @RequestMapping("/api/orders")
    public class OrderController {
        @Autowired private KafkaProducerService producerService;

        @PostMapping("/good") // Endpoint cho tin nhắn hợp lệ
        public String sendGoodOrder() {
            Order order = Order.newBuilder()
                .setId(UUID.randomUUID().toString())
                .setProduct("Macbook Pro")
                .setQuantity(1)
                .build();
            producerService.sendOrder(order);
            return "Đã gửi đơn hàng hợp lệ!";
        }

        @PostMapping("/bad") // Endpoint cho tin nhắn lỗi
        public String sendBadOrder() {
            Order order = Order.newBuilder()
                .setId(UUID.randomUUID().toString())
                .setProduct("Product-FAIL-123") // Tên sản phẩm chứa "FAIL"
                .setQuantity(-1)
                .build();
            producerService.sendOrder(order);
            return "Đã gửi đơn hàng lỗi!";
        }
    }
    ```

#### **D. Chạy và Quan Sát**

1.  **Khởi động lại ứng dụng Spring Boot.**
2.  **Gửi một tin nhắn lỗi:** `POST http://localhost:8080/api/orders/bad`
3.  **Quan sát console:** Bạn sẽ thấy một chuỗi các sự kiện:
    ```
    INFO  c.e.k.KafkaConsumerService : Đang xử lý đơn hàng: <uuid>
    ERROR c.e.k.KafkaConsumerService : Lỗi cố ý! Không thể xử lý đơn hàng cho sản phẩm: Product-FAIL-123
    ... (stacktrace) ...
    // Sau 1 giây
    INFO  c.e.k.KafkaConsumerService : Đang xử lý đơn hàng: <uuid>
    ERROR c.e.k.KafkaConsumerService : Lỗi cố ý! Không thể xử lý đơn hàng cho sản phẩm: Product-FAIL-123
    ... (stacktrace) ...
    // Sau 1 giây nữa
    INFO  c.e.k.KafkaConsumerService : Đang xử lý đơn hàng: <uuid>
    ERROR c.e.k.KafkaConsumerService : Lỗi cố ý! Không thể xử lý đơn hàng cho sản phẩm: Product-FAIL-123
    ... (stacktrace) ...
    // Sau lần lỗi cuối cùng, bạn sẽ thấy log của DeadLetterPublishingRecoverer
    INFO  o.s.k.l.DeadLetterPublishingRecoverer : Sending failed record to topic orders.DLT
    ```
4.  **Gửi một tin nhắn hợp lệ:** `POST http://localhost:8080/api/orders/good`
5.  **Quan sát console:** Bạn sẽ thấy tin nhắn này được xử lý ngay lập tức. Điều này chứng tỏ partition không còn bị chặn nữa!

6.  **Kiểm tra DLT:**
    Mở terminal và chạy `docker exec` vào container Kafka để dùng `kafka-console-consumer`.
    ```bash
    docker exec -it <tên_container_kafka> /bin/bash

    # Lắng nghe trên topic DLT
    kafka-console-consumer.sh \
    --bootstrap-server localhost:9092 \
    --topic orders.DLT \
    --from-beginning \
    --property print.key=true \
    --property print.headers=true
    ```
    Bạn sẽ thấy tin nhắn lỗi nằm trong đó, cùng với các header mà chúng ta đã thêm vào.

#### **E. Quản Lý DLT**

Việc đưa tin nhắn vào DLT chỉ là một nửa câu chuyện. Trong môi trường production, bạn cần có một chiến lược để xử lý các tin nhắn này:
*   **Giám sát (Monitoring):** Cài đặt cảnh báo khi có tin nhắn mới trong DLT.
*   **Phân tích (Analysis):** Kiểm tra các tin nhắn lỗi để tìm ra nguyên nhân gốc rễ (bug trong code, dữ liệu sai từ hệ thống nguồn...).
*   **Xử lý lại (Reprocessing):** Sau khi đã sửa lỗi, bạn có thể viết một công cụ hoặc một consumer đặc biệt để đọc các tin nhắn từ DLT, sửa đổi chúng nếu cần, và gửi lại vào topic ban đầu để xử lý lại.

---

**Tạm kết Phần 6**

Bạn đã trang bị cho ứng dụng của mình một cơ chế phòng thủ cực kỳ mạnh mẽ. Giờ đây bạn đã biết:

*   Hiểm họa của tin nhắn "poison pill" và cách nó có thể làm tê liệt hệ thống.
*   Mô hình kết hợp **Retry** và **Dead Letter Topic (DLT)** để xử lý lỗi một cách an toàn.
*   Cách triển khai mô hình này một cách thanh lịch trong Spring Boot bằng cách sử dụng `DefaultErrorHandler`.
*   Tầm quan trọng của việc giám sát và quản lý DLT sau khi tin nhắn đã được chuyển vào đó.

---
### **Phần 7: Tinh Chỉnh Cấu Hình Producer & Consumer**

Phần này sẽ tập trung vào các tham số cấu hình quan trọng nhất, giải thích chúng làm gì và khi nào bạn nên thay đổi chúng. Đây là sự cân bằng giữa ba yếu tố: **Thông lượng (Throughput)**, **Độ trễ (Latency)**, và **Độ bền (Durability)**.

#### **A. Tinh Chỉnh Producer - Gửi Dữ Liệu Hiệu Quả và An Toàn**

Các cấu hình này quyết định cách producer gửi tin nhắn đến broker.

**1. `acks` (Xác nhận)**

Đây là cấu hình quan trọng nhất quyết định **độ bền** của dữ liệu. Nó cho biết producer cần bao nhiêu broker xác nhận đã nhận tin nhắn trước khi coi việc gửi là thành công.

*   `acks=0`: **Fire and Forget.** Producer gửi tin nhắn và không chờ bất kỳ xác nhận nào.
    *   **Ưu điểm:** Độ trễ thấp nhất, thông lượng cao nhất.
    *   **Nhược điểm:** Dữ liệu có thể bị mất nếu broker gặp sự cố ngay sau khi nhận. Không có cơ chế thử lại.
    *   **Khi nào dùng:** Khi việc mất một vài tin nhắn là chấp nhận được (ví dụ: thu thập logs, metrics không quan trọng).

*   `acks=1`: **(Mặc định)** Leader xác nhận. Producer chỉ chờ broker leader của partition xác nhận đã ghi tin nhắn vào log của nó.
    *   **Ưu điểm:** Cân bằng tốt giữa độ bền và hiệu suất.
    *   **Nhược điểm:** Dữ liệu có thể bị mất trong trường hợp đặc biệt: leader ghi xong nhưng bị lỗi *trước khi* các follower kịp sao chép.
    *   **Khi nào dùng:** Hầu hết các trường hợp sử dụng thông thường.

*   `acks=all` (hoặc `-1`): **Tất cả In-sync Replicas xác nhận.** Producer sẽ chờ cho đến khi broker leader và tất cả các follower trong danh sách `in-sync replicas` (ISR) xác nhận đã nhận tin nhắn.
    *   **Ưu điểm:** **Độ bền cao nhất.** Đảm bảo không mất dữ liệu miễn là còn ít nhất một replica hoạt động.
    *   **Nhược điểm:** Độ trễ cao nhất vì phải chờ phản hồi từ nhiều broker.
    *   **Khi nào dùng:** Các hệ thống quan trọng không thể để mất dữ liệu (ví dụ: giao dịch tài chính, xử lý đơn hàng).

**2. `compression.type` (Kiểu nén)**

Nén dữ liệu trước khi gửi đi để giảm tải mạng và dung lượng lưu trữ trên broker.

*   **Các giá trị:** `none` (mặc định), `gzip`, `snappy`, `lz4`, `zstd`.
*   **Ưu điểm:** Cải thiện đáng kể thông lượng, đặc biệt với dữ liệu dạng text (JSON, logs).
*   **Nhược điểm:** Tốn thêm một chút CPU ở cả producer (để nén) và consumer (để giải nén).
*   **Khi nào dùng:** **Hầu như luôn luôn!** `snappy` hoặc `lz4` là lựa chọn tốt cho sự cân bằng giữa tốc độ và tỷ lệ nén. `zstd` là một lựa chọn hiện đại và rất hiệu quả.

**3. Batching: `batch.size` và `linger.ms`**

Để tăng thông lượng, producer không gửi từng tin nhắn một mà nhóm chúng lại thành một **lô (batch)** rồi mới gửi.

*   `batch.size`: Kích thước tối đa của một lô tin nhắn (tính bằng byte). Producer sẽ giữ các tin nhắn trong bộ đệm cho đến khi lô này đầy.
*   `linger.ms`: Thời gian chờ tối đa (tính bằng mili giây) trước khi gửi một lô, ngay cả khi nó chưa đầy.

Hãy tưởng tượng một chiếc xe buýt (`batch.size` là số ghế, `linger.ms` là thời gian tối đa xe chờ ở bến).
*   Tăng `batch.size` và `linger.ms` -> **Tăng thông lượng** (chở được nhiều khách hơn trong một chuyến) nhưng **tăng độ trễ** (hành khách đầu tiên phải chờ lâu hơn).
*   Giảm chúng -> **Giảm độ trễ** nhưng **giảm thông lượng** (xe buýt chạy liên tục dù chỉ có vài khách).

**4. `retries` (Số lần thử lại)**

Nếu việc gửi tin nhắn gặp lỗi tạm thời (ví dụ: lỗi mạng, leader re-election), producer có thể tự động thử lại. Đặt giá trị > 0 là rất quan trọng để xây dựng hệ thống bền bỉ.

---

#### **B. Tinh Chỉnh Consumer - Đọc Dữ Liệu Hiệu Quả**

Các cấu hình này ảnh hưởng đến cách consumer lấy và xử lý dữ liệu.

**1. `fetch.min.bytes` và `fetch.max.wait.ms`**

Đây là các tham số tương ứng với batching ở phía consumer. Chúng kiểm soát cách consumer yêu cầu dữ liệu từ broker.

*   `fetch.min.bytes`: Yêu cầu broker chỉ trả lời khi đã có đủ dữ liệu (tính bằng byte) để gửi. Điều này giảm số lượng các yêu cầu mạng không cần thiết.
*   `fetch.max.wait.ms`: Thời gian tối đa mà consumer sẽ chờ broker thu thập đủ `fetch.min.bytes` dữ liệu.

*   Tăng các giá trị này -> **Tăng thông lượng** (mỗi lần lấy được nhiều dữ liệu hơn) nhưng **tăng độ trễ** (consumer phải chờ lâu hơn để nhận được tin nhắn đầu tiên).

**2. `max.poll.records`**

Số lượng tin nhắn tối đa được trả về trong một lần gọi `poll()`.

*   **Ảnh hưởng:** Nếu logic xử lý mỗi tin nhắn của bạn tốn nhiều thời gian (gọi API, truy vấn DB), bạn nên đặt giá trị này **nhỏ** lại. Nếu bạn lấy quá nhiều tin nhắn và xử lý không kịp, bạn có thể vi phạm `session.timeout.ms`, khiến broker nghĩ rằng consumer của bạn đã chết và kích hoạt một cuộc Rebalance.

**3. `session.timeout.ms` và `heartbeat.interval.ms`**

Cách Kafka phát hiện một consumer đã "chết".

*   `heartbeat.interval.ms`: Tần suất consumer gửi một "nhịp tim" đến broker để báo rằng "tôi vẫn còn sống".
*   `session.timeout.ms`: Nếu broker không nhận được nhịp tim nào trong khoảng thời gian này, nó sẽ coi consumer đã chết, xóa nó khỏi group và kích hoạt Rebalance.
*   **Quy tắc:** `session.timeout.ms` nên lớn hơn `heartbeat.interval.ms` (thường là gấp 3 lần). Quan trọng hơn, **tổng thời gian xử lý một lô tin nhắn (`max.poll.records`) phải nhỏ hơn `session.timeout.ms`**.

**4. `auto.offset.reset`**

Chỉ áp dụng khi một consumer group mới được tạo hoặc khi offset đã commit không còn tồn tại trên broker.

*   `latest`: **(Mặc định)** Consumer sẽ bắt đầu đọc từ những tin nhắn mới nhất được gửi đến partition. Nó sẽ bỏ qua tất cả các tin nhắn đã có sẵn. (Giống như bật TV lên và xem chương trình đang chiếu).
*   `earliest`: Consumer sẽ bắt đầu đọc từ đầu partition, xử lý lại tất cả các tin nhắn từ trước đến nay. (Giống như xem một series phim từ tập đầu tiên).

#### **C. Cách Áp Dụng trong `application.properties`**

Bạn có thể dễ dàng áp dụng các cấu hình này trong file `application.properties` của Spring Boot:

```properties
# === Producer Properties ===
# Tăng độ bền
spring.kafka.producer.properties.acks=all
# Bật nén để tăng thông lượng
spring.kafka.producer.properties.compression.type=snappy
# Tăng batching để tối ưu cho thông lượng
spring.kafka.producer.properties.batch.size=16384
spring.kafka.producer.properties.linger.ms=5

# === Consumer Properties ===
# Tăng thông lượng bằng cách fetch nhiều dữ liệu hơn mỗi lần
spring.kafka.consumer.properties.fetch.min.bytes=1024
spring.kafka.consumer.properties.fetch.max.wait.ms=500
# Giới hạn số record mỗi lần poll để xử lý không bị quá tải
spring.kafka.consumer.properties.max.poll.records=100
# Bắt đầu đọc từ đầu nếu là consumer group mới
spring.kafka.consumer.properties.auto.offset.reset=earliest
```

---

**Tạm kết Phần 7**

Việc tinh chỉnh Kafka không có một "công thức vàng" nào cả. Nó luôn là một sự đánh đổi. Bằng cách hiểu ý nghĩa của các tham số cốt lõi này, bạn có thể:

*   **Tối ưu cho độ trễ thấp:** Giảm `linger.ms`, `fetch.max.wait.ms`.
*   **Tối ưu cho thông lượng cao:** Tăng `batch.size`, `linger.ms`, `fetch.min.bytes` và sử dụng nén.
*   **Tối ưu cho độ bền tối đa:** Sử dụng `acks=all`.

Công việc của bạn là xác định yêu cầu của từng ứng dụng và điều chỉnh các thông số cho phù hợp.

---
### **Phần 8: Giới Thiệu Kafka Connect - Cổng Nối Dữ Liệu**

#### **A. Kafka Connect Là Gì? Tại Sao Phải Quan Tâm?**

**Vấn đề:** Việc tích hợp Kafka với các hệ thống khác (databases, S3, Elasticsearch, HDFS...) thường có rất nhiều code lặp đi lặp lại (boilerplate): kết nối, lấy dữ liệu, xử lý lỗi, chuyển đổi định dạng...

**Giải pháp:** Kafka Connect là một **framework** để xây dựng và chạy các **đường ống dữ liệu (data pipelines)** một cách đáng tin cậy và có khả năng mở rộng. Nó giúp di chuyển một lượng lớn dữ liệu vào và ra khỏi Kafka.

Hãy tưởng tượng Kafka Connect như một bộ sưu tập các **adapter (bộ chuyển đổi)** đã được làm sẵn. Bạn chỉ cần **cấu hình** chúng thay vì phải **viết code** từ đầu.

#### **B. Các Khái Niệm Cốt Lõi của Kafka Connect**

1.  **Connector (Bộ kết nối):** Đây là trái tim của Kafka Connect. Một connector định nghĩa cách di chuyển dữ liệu giữa một hệ thống bên ngoài và Kafka. Có hai loại:
    *   **Source Connector:** Lấy dữ liệu từ một hệ thống nguồn (ví dụ: database, file log) và đẩy vào Kafka topic.
    *   **Sink Connector:** Đọc dữ liệu từ Kafka topic và ghi vào một hệ thống đích (ví dụ: Elasticsearch, S3, HDFS).

2.  **Worker:** Là một tiến trình (process) Java chạy các connector và các task của chúng. Worker xử lý việc lưu trữ trạng thái, offset, và tái cân bằng công việc. Chúng có thể chạy ở hai chế độ:
    *   **Standalone Mode:** Một worker duy nhất chạy trên một máy. Dùng cho việc phát triển, thử nghiệm hoặc các công việc nhỏ.
    *   **Distributed Mode (Phân tán):** Nhiều worker chạy trên nhiều máy khác nhau, tạo thành một cụm Kafka Connect. Chế độ này có khả năng chịu lỗi và mở rộng cao, được dùng cho môi trường production.

3.  **Task (Nhiệm vụ):** Worker sẽ chia công việc của một connector thành các `Task`. Một Task là đơn vị thực thi chính. Ví dụ, một source connector kết nối tới database có thể tạo ra nhiều task, mỗi task chịu trách nhiệm đọc một tập hợp các bảng khác nhau. Điều này cho phép xử lý song song.

4.  **Converter (Bộ chuyển đổi):** Kafka Connect di chuyển dữ liệu dưới dạng byte. Converter sẽ xử lý việc tuần tự hóa (serialize) và giải tuần tự hóa (deserialize) dữ liệu giữa Kafka Connect và Kafka. Nó đảm bảo dữ liệu trong Kafka có định dạng bạn muốn (ví dụ: JSON, Avro).

![Hình 5.png](images/H%C3%ACnh%205.png)*Sơ đồ minh họa Source Connector đọc từ Database và Sink Connector ghi vào Elasticsearch.*

#### **C. Thực Hành: Chạy Pipeline Đơn Giản (File-to-File)**

Chúng ta sẽ xây dựng một pipeline đơn giản nhất: đọc dữ liệu từ một file văn bản (source), đẩy vào Kafka, sau đó đọc từ Kafka và ghi ra một file văn bản khác (sink). Điều này cho phép chúng ta hiểu cơ chế hoạt động mà không cần cài thêm database.

1.  **Cập nhật `docker-compose.yml`:**
    Thêm dịch vụ `connect` vào file của bạn. Chúng ta sẽ chạy ở chế độ Distributed.

    ```yaml
    # ... zookeeper, kafka, schema-registry ...

    # DỊCH VỤ MỚI
    connect:
      image: 'bitnami/kafka-connect:latest'
      ports:
        - '8083:8083' # Cổng REST API của Kafka Connect
      environment:
        # Máy chủ Kafka mà Connect sẽ kết nối tới
        - CONNECT_BOOTSTRAP_SERVERS=kafka:9092
        # Tên group.id cho cụm Connect
        - CONNECT_GROUP_ID=my_connect_cluster
        # Các topic nội bộ Kafka Connect dùng để lưu trữ cấu hình, offset, và trạng thái
        - CONNECT_CONFIG_STORAGE_TOPIC=my_connect_configs
        - CONNECT_OFFSET_STORAGE_TOPIC=my_connect_offsets
        - CONNECT_STATUS_STORAGE_TOPIC=my_connect_statuses
        # Số partition cho các topic nội bộ (nên > 1 trong production)
        - CONNECT_CONFIG_STORAGE_REPLICATION_FACTOR=1
        - CONNECT_OFFSET_STORAGE_REPLICATION_FACTOR=1
        - CONNECT_STATUS_STORAGE_REPLICATION_FACTOR=1
        # Converter mặc định cho key và value
        - CONNECT_KEY_CONVERTER=org.apache.kafka.connect.storage.StringConverter
        - CONNECT_VALUE_CONVERTER=org.apache.kafka.connect.storage.StringConverter
      depends_on:
        - kafka
        - schema-registry
    ```
    Dừng và khởi động lại toàn bộ stack: `docker-compose down && docker-compose up -d`.

2.  **Tạo file nguồn:**
    Mở terminal và tạo một file để làm đầu vào.

    ```bash
    # Tạo một file input
    echo "Xin chào Kafka Connect" > /tmp/test.source.txt
    echo "Đây là dòng thứ hai" >> /tmp/test.source.txt
    ```

3.  **Tạo Connector thông qua REST API:**
    Kafka Connect được quản lý thông qua một REST API trên cổng `8083`. Chúng ta sẽ gửi request POST để tạo các connector.

    *   **Tạo Source Connector:**
        Gửi request sau bằng `curl`. Nó sẽ yêu cầu Kafka Connect sử dụng `FileStreamSourceConnector` để đọc file `/tmp/test.source.txt` và đẩy mỗi dòng vào topic `connect-test`.

        ```bash
        curl -X POST -H "Content-Type: application/json" --data '{
          "name": "file-source-connector",
          "config": {
            "connector.class": "org.apache.kafka.connect.file.FileStreamSourceConnector",
            "tasks.max": "1",
            "file": "/tmp/test.source.txt",
            "topic": "connect-test"
            }
          }' http://localhost:8083/connectors
        ```

    *   **Kiểm tra dữ liệu trong Kafka:**
        Mở một terminal khác, `exec` vào container Kafka và dùng console consumer để xem dữ liệu đã vào topic `connect-test` chưa.

        ```bash
        docker exec -it <tên_container_kafka> /bin/bash
        kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic connect-test --from-beginning
        ```
        Bạn sẽ thấy hai dòng chữ từ file nguồn xuất hiện.

    *   **Tạo Sink Connector:**
        Bây giờ, gửi request này để tạo một sink connector. Nó sẽ đọc từ topic `connect-test` và ghi vào file `/tmp/test.sink.txt`.

        ```bash
        curl -X POST -H "Content-Type: application/json" --data '{
          "name": "file-sink-connector",
          "config": {
            "connector.class": "org.apache.kafka.connect.file.FileStreamSinkConnector",
            "tasks.max": "1",
            "file": "/tmp/test.sink.txt",
            "topics": "connect-test"
            }
          }' http://localhost:8083/connectors
        ```

4.  **Kiểm tra kết quả cuối cùng:**
    Quay lại terminal đầu tiên và kiểm tra nội dung của file sink.

    ```bash
    cat /tmp/test.sink.txt
    ```
    Bạn sẽ thấy nội dung của file nguồn đã được sao chép sang đây.

5.  **Thử nghiệm thời gian thực:**
    Thêm một dòng mới vào file nguồn.

    ```bash
    echo "Dòng này được thêm sau" >> /tmp/test.source.txt
    ```
    Chờ một vài giây và kiểm tra lại file sink: `cat /tmp/test.sink.txt`. Dòng mới sẽ tự động xuất hiện! Bạn đã tạo ra một pipeline dữ liệu thời gian thực mà **không cần viết một dòng code Java nào**.

---

**Tạm kết Phần 8**

Bạn vừa được giới thiệu một công cụ cực kỳ mạnh mẽ trong hệ sinh thái Kafka. Giờ đây bạn đã hiểu:

*   **Kafka Connect** giải quyết vấn đề tích hợp dữ liệu bằng cách cung cấp một framework dựa trên cấu hình.
*   Các khái niệm chính: **Source/Sink Connector**, **Worker**, và **Task**.
*   Cách triển khai và quản lý các connector thông qua **REST API** của Kafka Connect.
*   Sức mạnh của việc xây dựng pipeline dữ liệu mà không cần viết code producer/consumer thủ công.

Trong môi trường thực tế, bạn sẽ sử dụng các connector phức tạp hơn như JDBC Connector (để đọc từ database), Debezium (cho Change Data Capture - CDC), S3 Sink Connector, Elasticsearch Sink Connector, v.v. Nhưng nguyên tắc hoạt động vẫn hoàn toàn tương tự.

---
### **Phần 9: Giới Thiệu Kafka Streams - Xử Lý Dữ Liệu Thời Gian Thực**

#### **A. Tại Sao Cần Đến Kafka Streams?**

Hãy tưởng tượng bạn muốn xây dựng một ứng dụng "Bảng xếp hạng game thủ theo thời gian thực". Với mô hình consumer thông thường, bạn sẽ phải:

1.  Viết một consumer để đọc điểm số của người chơi từ một topic.
2.  Để tính tổng điểm, bạn cần một nơi để lưu trữ trạng thái (tổng điểm hiện tại của mỗi người chơi). Bạn có thể dùng một database ngoài như Redis hoặc PostgreSQL.
3.  Mỗi khi nhận tin nhắn mới, consumer sẽ: đọc trạng thái hiện tại từ DB, cộng điểm mới, rồi ghi lại trạng thái mới vào DB.
4.  Việc này tạo ra nhiều độ trễ (do gọi mạng tới DB), phức tạp trong việc quản lý (phải duy trì một DB riêng), và khó mở rộng.

**Kafka Streams** giải quyết chính xác vấn đề này. Nó là một **thư viện client** (không phải một hệ thống cluster riêng như Spark hay Flink) cho phép bạn xây dựng các ứng dụng xử lý luồng một cách mạnh mẽ. Ứng dụng Kafka Streams của bạn thực chất là một ứng dụng Java/Scala thông thường mà bạn có thể đóng gói và chạy ở bất cứ đâu.

**Điểm cốt lõi:** Kafka Streams đọc dữ liệu từ các topic đầu vào, thực hiện các phép biến đổi (lọc, nhóm, tổng hợp, nối...), và ghi kết quả ra các topic đầu ra. Nó quản lý trạng thái (state) một cách hiệu quả ngay tại local (sử dụng RocksDB) và sao lưu trạng thái đó vào Kafka để đảm bảo tính chịu lỗi.

#### **B. Các Khái Niệm Cốt Lõi của Kafka Streams**

1.  **Topology (Cấu trúc liên kết):** Là một bản thiết kế hoặc một đồ thị về luồng xử lý của bạn. Bạn định nghĩa các node xử lý (processor) và cách chúng được kết nối với nhau. Ví dụ: "đọc từ topic A" -> "lọc các tin nhắn" -> "nhóm theo key" -> "đếm số lượng" -> "ghi ra topic B".

2.  **KStream:** Đại diện cho một **luồng sự kiện vô hạn (unbounded stream)**. Mỗi record trong KStream là một sự kiện độc lập. Ví dụ: một luồng các cú click chuột, các giao dịch thẻ tín dụng. Bạn không thể sửa đổi một sự kiện đã xảy ra.

3.  **KTable:** Đại diện cho một **bảng trạng thái (changelog stream)**. Nó thể hiện trạng thái mới nhất của một key. Hãy nghĩ về nó như một bảng trong database được cập nhật liên tục. Mỗi record mới với cùng một key sẽ **cập nhật (update)** giá trị trước đó. Ví dụ: một bảng lưu trữ vị trí hiện tại của mỗi tài xế, hoặc tổng điểm hiện tại của mỗi người chơi.

4.  **State Store (Kho lưu trữ trạng thái):** Nơi Kafka Streams lưu trữ trạng thái cho các phép toán stateful như `count()` hay `aggregate()`. Mặc định, nó sử dụng một database nhúng siêu nhanh là RocksDB.

#### **C. Thực Hành: Xây Dựng Ứng Dụng Đếm Từ (Word Count)**

Đây là ví dụ kinh điển của xử lý luồng. Chúng ta sẽ xây dựng một ứng dụng:
*   Đọc các câu từ topic `streams-plaintext-input`.
*   Tách các câu thành từng từ.
*   Đếm số lần xuất hiện của mỗi từ.
*   Ghi kết quả (từ và số đếm) ra topic `streams-wordcount-output`.

1.  **Cập nhật `pom.xml`:**
    Thêm thư viện Kafka Streams vào dự án Spring Boot của bạn.

    ```xml
    <!-- Thêm vào thẻ <dependencies> -->
    <dependency>
        <groupId>org.apache.kafka</groupId>
        <artifactId>kafka-streams</artifactId>
    </dependency>
    ```

2.  **Cập nhật `application.properties`:**
    Kafka Streams cần một `application.id` để định danh. Nó sẽ sử dụng ID này làm `group.id` cho các consumer nội bộ và để tạo thư mục lưu trữ trạng thái.

    ```properties
    # ... các cấu hình Kafka cũ ...

    # Cấu hình cho Kafka Streams
    spring.kafka.streams.application-id=wordcount-app
    # Chỉ định các broker cho streams (nếu chưa có)
    spring.kafka.streams.bootstrap-servers=localhost:9092
    ```

3.  **Viết Code Xử Lý Luồng:**
    Tạo một class mới để định nghĩa Topology xử lý.
    ```java
    package com.example.kafkademo;

    import org.apache.kafka.common.serialization.Serdes;
    import org.apache.kafka.streams.KeyValue;
    import org.apache.kafka.streams.StreamsBuilder;
    import org.apache.kafka.streams.kstream.KStream;
    import org.apache.kafka.streams.kstream.Produced;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.stereotype.Component;

    import java.util.Arrays;
    import java.util.Locale;

    @Component
    public class WordCountProcessor {

        private static final String INPUT_TOPIC = "streams-plaintext-input";
        private static final String OUTPUT_TOPIC = "streams-wordcount-output";

        // Spring sẽ tự động inject StreamsBuilder
        @Autowired
        void buildPipeline(StreamsBuilder streamsBuilder) {
            // 1. Đọc luồng dữ liệu từ topic đầu vào
            KStream<String, String> messageStream = streamsBuilder.stream(INPUT_TOPIC);

            messageStream
                // 2. Tách mỗi dòng thành các từ riêng lẻ (flatMapValues)
                // "hello world" -> "hello", "world"
                .flatMapValues(value -> Arrays.asList(value.toLowerCase(Locale.getDefault()).split("\\W+")))

                // 3. Nhóm các từ giống nhau lại (groupBy)
                // ("hello", "world", "hello") -> ("hello", ["hello", "hello"]), ("world", ["world"])
                .groupBy((key, value) -> value)

                // 4. Đếm số lần xuất hiện của mỗi từ trong nhóm (count)
                // -> KTable: ("hello", 2), ("world", 1)
                .count()

                // 5. Chuyển KTable kết quả thành một KStream để ghi ra ngoài
                .toStream()

                // 6. Ghi kết quả ra topic đầu ra
                .to(OUTPUT_TOPIC, Produced.with(Serdes.String(), Serdes.Long()));

            // Topology đã được xây dựng. Spring Boot và Spring Kafka sẽ lo việc khởi chạy nó.
        }
    }
    ```

4.  **Chạy và Kiểm Tra:**
    *   **Bước 1:** Khởi động lại ứng dụng Spring Boot của bạn. Ngay khi khởi động, ứng dụng sẽ bắt đầu luồng xử lý Kafka Streams.
    *   **Bước 2:** Mở một terminal và `exec` vào container Kafka. Chúng ta sẽ dùng `kafka-console-producer` để gửi dữ liệu vào topic đầu vào.
        ```bash
        docker exec -it <tên_container_kafka> /bin/bash

        kafka-console-producer.sh \
        --bootstrap-server localhost:9092 \
        --topic streams-plaintext-input
        ```
        Sau khi chạy lệnh, bạn có thể bắt đầu gõ các câu và nhấn Enter.
        ```
        >xin chao kafka streams
        >kafka streams rat manh me
        >xin chao the gioi
        ```

    *   **Bước 3:** Mở một terminal thứ hai và cũng `exec` vào container Kafka. Lần này dùng `kafka-console-consumer` để đọc kết quả từ topic đầu ra.

        ```bash
        docker exec -it <tên_container_kafka> /bin/bash

        kafka-console-consumer.sh \
        --bootstrap-server localhost:9092 \
        --topic streams-wordcount-output \
        --from-beginning \
        --property print.key=true \
        --property print.value=true \
        --key-deserializer org.apache.kafka.common.serialization.StringDeserializer \
        --value-deserializer org.apache.kafka.common.serialization.LongDeserializer
        ```
    *   **Kết quả bạn sẽ thấy:**
        ```
        xin     1
        chao    1
        kafka   1
        streams 1
        kafka   2
        streams 2
        rat     1
        manh    1
        me      1
        xin     2
        chao    2
        the     1
        gioi    1
        ```
        Bạn có thể thấy số đếm được cập nhật liên tục mỗi khi một từ mới xuất hiện. Ứng dụng của bạn đang xử lý dữ liệu một cách stateful và theo thời gian thực!

---

### **Tổng Kết Toàn Bộ Lộ Trình**

Xin chúc mừng! Bạn đã hoàn thành một hành trình dài và toàn diện, từ một người chưa biết gì về Kafka đến việc có thể xây dựng và vận hành các ứng dụng phức tạp. Hãy cùng nhìn lại:

*   **Phần 1-4:** Bạn đã xây dựng nền tảng vững chắc về **các khái niệm cốt lõi**, học cách viết **Producer/Consumer** với Spring Boot, và hiểu sâu về **Partitions, Keys, và Consumer Groups** để đảm bảo khả năng mở rộng và độ tin cậy.
*   **Phần 5-7:** Bạn đã nâng cấp ứng dụng của mình lên tầm production bằng cách sử dụng **Schema Registry và Avro** để đảm bảo an toàn dữ liệu, triển khai mô hình **Dead Letter Topic** để xử lý lỗi, và học cách **tinh chỉnh các cấu hình** quan trọng để tối ưu hóa hiệu suất.
*   **Phần 8-9:** Bạn đã khám phá hệ sinh thái Kafka rộng lớn hơn, học cách dùng **Kafka Connect** để tích hợp dữ liệu mà không cần code, và sử dụng **Kafka Streams** để xây dựng các ứng dụng xử lý luồng dữ liệu thời gian thực mạnh mẽ.