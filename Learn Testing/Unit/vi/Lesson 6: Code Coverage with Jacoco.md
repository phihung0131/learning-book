### **Giai đoạn 1, Bài 6: Độ bao phủ Mã nguồn với Jacoco**

**Câu hỏi cốt lõi:** Bạn đã viết rất nhiều bài test. Nhưng làm thế nào bạn biết mình đã bỏ sót việc kiểm thử một câu lệnh `if` quan trọng hay một điều kiện lỗi cụ thể? Liệu các bài test của bạn có đang thực thi tất cả các phần của mã nguồn, hay chỉ là các luồng thành công (happy path)?

Đây là vấn đề mà phân tích độ bao phủ mã nguồn giải quyết.

### **1. Độ bao phủ có nghĩa là gì? (Dòng, Nhánh, Phương thức, Lớp)**

**Độ bao phủ mã nguồn (Code coverage)** là một chỉ số đo lường tỷ lệ phần trăm mã nguồn chính của bạn được thực thi bởi các bài test tự động. Nó **không phải** là thước đo chất lượng của bài test, nhưng nó là một công cụ rất hữu ích để xác định các phần chưa được kiểm thử trong ứng dụng của bạn.

**Jacoco** (Java Code Coverage) là công cụ tiêu chuẩn trong hệ sinh thái Java để đo lường điều này. Nó cung cấp một số chỉ số chính:

*   **Độ bao phủ dòng (Line Coverage):** Bao nhiêu phần trăm các dòng mã có thể thực thi đã được chạy trong quá trình test? Đây là chỉ số phổ biến và trực quan nhất.
*   **Độ bao phủ nhánh (Branch Coverage):** Đây được cho là chỉ số **quan trọng nhất**. Đối với mỗi câu lệnh `if`, `switch`, hoặc `while`, nó kiểm tra xem các bài test của bạn đã thực thi *tất cả* các nhánh có thể có hay chưa (ví dụ: cả khối `if` và `else`). Một bài test chỉ bao phủ phần `if` sẽ dẫn đến độ bao phủ nhánh là 50% cho câu lệnh đó.
*   **Độ bao phủ phương thức (Method Coverage):** Bao nhiêu phần trăm các phương thức đã được gọi bởi các bài test của bạn?
*   **Độ bao phủ lớp (Class Coverage):** Bao nhiêu phần trăm các lớp đã có ít nhất một phương thức được gọi?

Độ bao phủ nhánh rất quan trọng vì độ bao phủ dòng 100% có thể gây hiểu lầm. Bạn có thể thực thi mọi dòng bên trong một khối `if` nhưng lại bỏ lỡ hoàn toàn khối `else`, nơi một lỗi nghiêm trọng có thể đang ẩn náu.

### **2. Cách Cài đặt và Cấu hình Jacoco Plugin trong Maven**

Để sử dụng Jacoco, bạn thêm plugin Maven của nó vào phần `<build><plugins>` trong `pom.xml` của mình. Cấu hình này bảo Jacoco làm hai việc:

1.  **`prepare-agent`**: Mục tiêu này chạy trước các bài test. Nó đính kèm một "Java agent" vào JVM để theo dõi những dòng mã nguồn chính nào được thực thi khi các bài test chạy.
2.  **`report`**: Mục tiêu này chạy sau các bài test và sử dụng dữ liệu được thu thập bởi agent để tạo ra một báo cáo HTML thân thiện với người dùng.

Đây là một cấu hình tiêu chuẩn cho `pom.xml` của bạn:

```xml
<build>
    <plugins>
        <!-- Các plugin khác như maven-surefire-plugin -->
        <plugin>
            <groupId>org.jacoco</groupId>
            <artifactId>jacoco-maven-plugin</artifactId>
            <version>0.8.11</version> <!-- Sử dụng phiên bản mới nhất -->
            <executions>
                <execution>
                    <goals>
                        <goal>prepare-agent</goal> <!-- Chạy trước các bài test -->
                    </goals>
                </execution>
                <execution>
                    <id>report</id>
                    <phase>prepare-package</phase> <!-- Tạo báo cáo sau các bài test -->
                    <goals>
                        <goal>report</goal>
                    </goals>
                </execution>
            </executions>
        </plugin>
    </plugins>
</build>
```

### **3. Cách Chạy Test và Tạo Báo cáo qua CLI**

Với plugin đã được cấu hình, quá trình rất đơn giản. Bạn sử dụng các lệnh Maven tiêu chuẩn:

1.  **Chạy các bài test:** Lệnh này bây giờ sẽ tự động đính kèm agent của Jacoco.
    ```bash
    mvn clean test
    ```
2.  **Tạo báo cáo:** Mặc dù pha `prepare-package` thường chạy cùng với `test`, bạn có thể tạo báo cáo một cách tường minh.
    ```bash
    mvn jacoco:report
    ```

Sau khi các lệnh này thành công, bạn sẽ tìm thấy báo cáo đầy đủ tại **`target/site/jacoco/index.html`**. Bạn có thể mở tệp này trong trình duyệt web của mình.

### **4. Cách Diễn giải Báo cáo HTML của Jacoco**

Báo cáo của Jacoco có tính tương tác và được mã hóa màu sắc, giúp dễ dàng phát hiện những khoảng trống trong việc kiểm thử của bạn.

*   **Xanh lá:** Mã được bao phủ hoàn toàn.
*   **Vàng:** Mã được bao phủ một phần. Điều này phổ biến nhất đối với độ bao phủ nhánh.
*   **Đỏ:** Mã hoàn toàn không được bao phủ bởi bất kỳ bài test nào.

Khi bạn mở báo cáo, bạn có thể đi sâu từ các gói xuống các lớp và cuối cùng là các phương thức riêng lẻ.

Bên trong một giao diện xem lớp, bạn sẽ thấy mã nguồn của mình được chú thích bằng các màu này.
*   Một **dòng màu đỏ** có nghĩa là dòng đó chưa bao giờ được thực thi.
*   Một **hình thoi màu vàng** bên cạnh câu lệnh `if` có nghĩa là chỉ một số nhánh được kiểm thử. Nếu bạn di chuột qua nó, nó sẽ cho bạn biết điều gì đó như "2 trong 4 nhánh đã bị bỏ lỡ." Đây là tín hiệu để bạn viết một trường hợp kiểm thử mới buộc thực thi đi theo con đường còn lại (ví dụ: truyền một giá trị `null` để kích hoạt kiểm tra `if (input == null)`).

### **5. Đặt Ngưỡng Độ bao phủ (Làm cho Build Thất bại)**

Sức mạnh thực sự của Jacoco đến từ việc sử dụng nó như một cổng chất lượng tự động. Bạn có thể cấu hình plugin để **làm cho build thất bại** nếu độ bao phủ mã nguồn của bạn giảm xuống dưới một ngưỡng nhất định. Điều này ngăn chặn mã không có đủ độ bao phủ test được hợp nhất vào nhánh chính.

Bạn thêm một execution `check` vào cấu hình plugin:

```xml
<plugin>
    <groupId>org.jacoco</groupId>
    <artifactId>jacoco-maven-plugin</artifactId>
    <!-- ... phiên bản và các execution khác ... -->
    <executions>
        <!-- ... các execution prepare-agent và report ... -->
        <execution>
            <id>check</id>
            <goals>
                <goal>check</goal>
            </goals>
            <configuration>
                <rules>
                    <rule>
                        <element>BUNDLE</element> <!-- Có thể là BUNDLE, PACKAGE, CLASS, METHOD -->
                        <limits>
                            <!-- Đặt độ bao phủ NHÁNH tối thiểu là 80% -->
                            <limit>
                                <counter>BRANCH</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.80</minimum>
                            </limit>
                            <!-- Đặt độ bao phủ DÒNG tối thiểu là 85% -->
                             <limit>
                                <counter>LINE</counter>
                                <value>COVEREDRATIO</value>
                                <minimum>0.85</minimum>
                            </limit>
                        </limits>
                    </rule>
                </rules>
            </configuration>
        </execution>
    </executions>
</plugin>
```
Bây giờ, nếu bạn chạy `mvn verify`, quá trình build sẽ thất bại nếu độ bao phủ nhánh tổng thể của bạn thấp hơn 80% hoặc độ bao phủ dòng thấp hơn 85%.

### **6. Tại sao Độ bao phủ 100% ≠ Mã không có lỗi**

Đây là một khái niệm quan trọng cho các cuộc phỏng vấn và thực hành chuyên nghiệp.

Việc nhắm đến độ bao phủ 100% có thể phản tác dụng. Nó **không** đảm bảo mã của bạn không có lỗi.

*   **Độ bao phủ chỉ đo lường việc thực thi, không phải chất lượng của assertion.** Một bài test có thể chạy một phương thức, cho nó độ bao phủ dòng 100%, nhưng nếu nó không có assertion (hoặc assertion tồi), bài test đó là vô dụng. Nó không thực sự *xác minh* rằng mã đã tạo ra kết quả chính xác.
*   **Nó không bao phủ tất cả các đầu vào.** Bạn có thể có độ bao phủ nhánh 100% cho `a + b`, nhưng bạn có thể chưa kiểm thử trường hợp `a` và `b` là `Integer.MAX_VALUE`, gây ra tràn số.
*   **10-20% cuối cùng thường rất khó để đạt được.** Điều này bao gồm những thứ như kiểm thử các constructor ngoại lệ ít được sử dụng hoặc các getter và setter đơn giản, điều này mang lại rất ít giá trị so với công sức bỏ ra.

**Mục tiêu:** Sử dụng độ bao phủ mã nguồn như một **hướng dẫn** để tìm ra những gì bạn đã bỏ lỡ. Đừng coi 100% là mục tiêu cuối cùng. Một dự án lành mạnh, được kiểm thử tốt thường có **độ bao phủ nhánh từ 80-90%**. Con số này là một phương tiện để đạt được mục đích cuối cùng: **sự tự tin vào mã nguồn của bạn.**

---

### **Tóm tắt Mục tiêu Cuối cùng & Kết luận Khóa học**

Chúc mừng! Bạn đã chính thức hoàn thành Giai đoạn 1: Nền tảng Kiểm thử cho Java.

Hãy tóm tắt lại tất cả những gì bạn đã đạt được so với các mục tiêu ban đầu của chúng ta:

*   **Tư duy Kiểm thử:** Bạn biết unit test là gì, tại sao chúng ta kiểm thử, các loại test khác nhau, và Kim tự tháp Kiểm thử. Bạn có thể thiết kế các trường hợp kiểm thử cho tất cả các kịch bản và hiểu các phương pháp như TDD.
*   **JUnit 5:** Bạn hoàn toàn có khả năng viết các bài test dễ bảo trì bằng cách sử dụng tất cả các annotation cốt lõi, assertion, test tham số hóa, và cấu trúc lồng nhau. Bạn có thể xử lý tất cả các trường hợp biên phổ biến.
*   **Mockito:** Bạn có thể mock các phụ thuộc một cách chính xác bằng `@Mock` và `@InjectMocks`. Bạn có thể stub hành vi với `when/then` và xác minh các tương tác với `verify()` và `ArgumentCaptor`.
*   **AssertJ:** Bạn thành thạo trong việc viết các assertion dễ đọc, có thể nối chuỗi bằng `assertThat()` và có thể xử lý các đối tượng và tập hợp phức tạp một cách dễ dàng.
*   **Cấu trúc Dự án:** Bạn biết cách tổ chức một dự án test trong Maven, quản lý các phụ thuộc, và sử dụng các tính năng như `@Tag` để chạy test có chọn lọc.
*   **Jacoco:** Bạn hiểu độ bao phủ mã nguồn có nghĩa là gì, cách cấu hình Jacoco, diễn giải báo cáo của nó, và đặt các cổng chất lượng để thực thi các tiêu chuẩn kiểm thử.

