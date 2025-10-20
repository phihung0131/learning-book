### **Giai đoạn 1, Bài 1: Tư duy Kiểm thử & Nền tảng**

### **1. Kiểm thử là gì, và Mục đích của Unit Test là gì?**

#### **Kiểm thử Phần mềm là gì?**

Về cốt lõi, **kiểm thử phần mềm** là quá trình đánh giá một ứng dụng phần mềm để xác minh rằng nó đáp ứng các yêu cầu đã chỉ định và để xác định bất kỳ khiếm khuyết nào. Hãy coi nó như một quy trình đảm bảo chất lượng. Mục tiêu chính không chỉ là tìm lỗi, mà còn là đảm bảo phần mềm hoạt động chính xác như mong đợi, mang lại sản phẩm chất lượng cao cho người dùng cuối.

#### **Unit Test là gì?**

**Unit test** là cấp độ kiểm thử cơ bản và chi tiết nhất. Mục đích của nó là xác minh tính đúng đắn của một đoạn mã nhỏ, bị cô lập được gọi là "unit" (đơn vị).

*   **"Unit" là gì?** Trong Java, một unit thường là một phương thức duy nhất trong một lớp. Mục tiêu là kiểm thử phần nhỏ nhất có thể kiểm thử của ứng dụng một cách hoàn toàn cô lập.
*   **Cô lập là Chìa khóa:** Nguyên tắc xác định của một unit test là **sự cô lập**. Điều này có nghĩa là bài kiểm thử không nên phụ thuộc vào các yếu tố bên ngoài như cơ sở dữ liệu, kết nối mạng hoặc các lớp khác. Chúng ta sử dụng các kỹ thuật như mocking (mà chúng ta sẽ đề cập với Mockito) để đạt được sự cô lập này.

#### **Tại sao chúng ta viết Unit Test?**

Viết unit test là một sự đầu tư quan trọng mang lại nhiều lợi ích:

*   **Tìm lỗi sớm:** Unit test giúp bạn phát hiện các khiếm khuyết sớm trong chu kỳ phát triển, rất lâu trước khi chúng đến tay người dùng. Lỗi càng được tìm thấy sớm thì việc sửa chữa càng dễ dàng và ít tốn kém hơn.
*   **Cải thiện Chất lượng & Thiết kế Mã nguồn:** Nếu một phương thức khó kiểm thử, đó thường là dấu hiệu của một thiết kế tồi. Viết test khuyến khích bạn xây dựng mã nguồn theo kiểu mô-đun, liên kết lỏng lẻo và dễ bảo trì hơn.
*   **Cung cấp Lưới an toàn cho việc Tái cấu trúc (Refactoring):** Tái cấu trúc có nghĩa là thay đổi cấu trúc bên trong của mã nguồn mà không làm thay đổi hành vi bên ngoài của nó. Một bộ unit test toàn diện hoạt động như một lưới an toàn, giúp bạn tự tin cải thiện mã nguồn của mình. Nếu một thay đổi làm hỏng chức năng hiện có, một bài test thất bại sẽ ngay lập tức cảnh báo bạn.
*   **Đóng vai trò như Tài liệu sống:** Các unit test được đặt tên tốt hoạt động như một tài liệu có thể thực thi. Một nhà phát triển mới có thể đọc các bài test cho một lớp và nhanh chóng hiểu được mục đích, đầu vào và đầu ra mong đợi của nó.
*   **Tăng tốc độ Phát triển:** Bằng cách phát hiện các vấn đề sớm và cung cấp một lưới an toàn, unit test giảm thời gian dành cho việc kiểm thử thủ công và gỡ lỗi, cho phép phát hành nhanh hơn và thường xuyên hơn.

### **2. Sự khác biệt giữa Kiểm thử Unit, Tích hợp, Hệ thống và Chấp nhận**

Điều quan trọng là phải hiểu rằng unit test chỉ là một phần của chiến lược kiểm thử hoàn chỉnh. Các loại kiểm thử khác nhau có mục tiêu và phạm vi khác nhau. Hãy xem xét những loại phổ biến nhất.

#### **1. Unit Test**

*   **Phạm vi:** Phần nhỏ nhất có thể kiểm thử của một ứng dụng, thường là một phương thức duy nhất.
*   **Mục đích:** Để xác minh rằng một đoạn mã cụ thể ("unit") hoạt động chính xác trong **sự cô lập**.
*   **Tốc độ:** Cực kỳ nhanh (mili giây).
*   **Ai viết chúng:** Nhà phát triển (Developers).
*   **Ví dụ:** Kiểm thử một phương thức `Calculator.add(2, 3)` để đảm bảo nó trả về `5`. Tất cả các phần khác của hệ thống đều bị bỏ qua.

#### **2. Kiểm thử Tích hợp (Integration Tests)**

*   **Phạm vi:** Hai hoặc nhiều unit/thành phần hoạt động cùng nhau.
*   **Mục đích:** Để xác minh rằng các phần khác nhau của hệ thống tương tác chính xác với nhau. Đây là nơi bạn kiểm thử "chất keo" gắn kết các unit của bạn lại với nhau.
*   **Tốc độ:** Chậm hơn unit test vì chúng liên quan đến nhiều thành phần và có khả năng là các hệ thống bên ngoài như cơ sở dữ liệu hoặc API.
*   **Ai viết chúng:** Nhà phát triển, đôi khi cùng với kỹ sư QA.
*   **Ví dụ:** Kiểm thử xem một `UserService` có thể lưu chính xác một người dùng mới vào `UserRepository`, mà `UserRepository` lại ghi vào cơ sở dữ liệu hay không. Bài kiểm thử này xác minh toàn bộ luồng từ service đến cơ sở dữ liệu.

#### **3. Kiểm thử Hệ thống (System Tests)**

*   **Phạm vi:** Toàn bộ ứng dụng, được tích hợp và triển khai đầy đủ.
*   **Mục đích:** Để kiểm thử hành vi đầu cuối hoàn chỉnh của phần mềm từ góc độ của người dùng. Nó xác nhận rằng toàn bộ hệ thống đáp ứng các yêu cầu chức năng và phi chức năng của nó.
*   **Tốc độ:** Chậm hơn nhiều so với kiểm thử tích hợp, vì chúng mô phỏng các quy trình làm việc thực tế của người dùng.
*   **Ai viết chúng:** Chủ yếu là kỹ sư QA hoặc các kỹ sư tự động hóa kiểm thử chuyên dụng.
*   **Ví dụ:** Một kịch bản tự động mở trình duyệt web, điều hướng đến trang web thương mại điện tử của bạn, đăng nhập, thêm sản phẩm vào giỏ hàng, tiến hành thanh toán và hoàn tất giao dịch mua. Điều này kiểm thử giao diện người dùng, các dịch vụ backend, cơ sở dữ liệu và tích hợp cổng thanh toán cùng một lúc.

#### **4. Kiểm thử Chấp nhận (Acceptance Tests hay UAT - User Acceptance Testing)**

*   **Phạm vi:** Toàn bộ ứng dụng.
*   **Mục đích:** Để xác nhận rằng phần mềm đáp ứng các yêu cầu kinh doanh và sẵn sàng để giao cho khách hàng. Đây là lần kiểm tra cuối cùng để đảm bảo ứng dụng "phù hợp với mục đích" từ quan điểm của khách hàng hoặc người dùng cuối.
*   **Tốc độ:** Thường là một quy trình thủ công, nhưng có thể được tự động hóa. Trọng tâm là tính đúng đắn, không phải tốc độ.
*   **Ai viết chúng:** Thường được thực hiện bởi khách hàng, người dùng cuối hoặc chủ sở hữu sản phẩm (product owners).
*   **Ví dụ:** Một bên liên quan kinh doanh thực hiện theo một kịch bản thủ công để xác minh rằng tính năng "tạo hóa đơn" mới hoạt động chính xác như được chỉ định trong tài liệu yêu cầu dự án.

Đây là một bảng tóm tắt đơn giản:

| Loại kiểm thử | Phạm vi | Mục tiêu | Đặc tính chính |
| :--- | :--- | :--- | :--- |
| **Unit** | Một Phương thức/Lớp | Tính đúng đắn của mã nguồn | Sự cô lập |
| **Tích hợp**| Nhiều thành phần | Tính đúng đắn của tương tác | "Kiểm thử các mối nối" |
| **Hệ thống** | Toàn bộ ứng dụng | Hành vi đầu cuối | Góc nhìn hộp đen |
| **Chấp nhận**| Toàn bộ ứng dụng | Yêu cầu kinh doanh | Xác nhận bởi người dùng/khách hàng |

### **3. Kim tự tháp Kiểm thử (Quy tắc 70/20/10)**

Kim tự tháp Kiểm thử là một phép ẩn dụ đơn giản nhưng mạnh mẽ giúp bạn cấu trúc chiến lược kiểm thử của mình. Nó hướng dẫn bạn về số lượng bài test của mỗi loại mà bạn nên hướng tới.

Hãy tưởng tượng một kim tự tháp với ba lớp:

![The Testing Pyramid](https://martinfowler.com/bliki/images/testPyramid/test-pyramid.png)

*(Nguồn ảnh: Martin Fowler)*

#### **Lớp 1: Unit Test (Phần đáy)**

*   **Tỷ lệ:** Phần lớn nhất trong bộ test của bạn, tạo thành đáy rộng của kim tự tháp. Một hướng dẫn phổ biến là **~70%** tổng số bài test của bạn.
*   **Tại sao?**
    *   **Nhanh:** Chúng chạy trong mili giây vì chúng bị cô lập và không đụng đến các hệ thống bên ngoài.
    *   **Đáng tin cậy:** Chúng không hay bị lỗi thất thường (flaky). Một unit test thất bại gần như luôn chỉ ra một lỗi thực sự trong mã nguồn đang được kiểm thử, chứ không phải do lỗi mạng hay cơ sở dữ liệu chậm.
    *   **Chính xác:** Khi một unit test thất bại, nó chỉ ra chính xác vị trí của vấn đề (phương thức cụ thể), giúp việc gỡ lỗi cực kỳ hiệu quả.
*   **Mục tiêu:** Đảm bảo các thành phần riêng lẻ hoạt động chính xác trước khi bạn nghĩ đến việc kết nối chúng.

#### **Lớp 2: Kiểm thử Tích hợp (Phần giữa)**

*   **Tỷ lệ:** Lớp giữa, nhỏ hơn phần đáy. Hướng dẫn là **~20%** tổng số bài test của bạn.
*   **Tại sao?**
    *   **Chậm hơn:** Chúng mất nhiều thời gian hơn để chạy vì chúng liên quan đến nhiều thành phần, các hoạt động I/O (như truy cập cơ sở dữ liệu) hoặc các lệnh gọi mạng.
    *   **Kém chính xác hơn:** Một bài kiểm thử tích hợp thất bại cho bạn biết rằng có vấn đề *giữa* các thành phần (ví dụ: service của bạn không thể nói chuyện với cơ sở dữ liệu), nhưng nó không chỉ ra ngay lập tức dòng mã chính xác. Bạn có thể cần phải gỡ lỗi thêm.
*   **Mục tiêu:** Xác minh rằng "hệ thống đường ống" giữa các thành phần của bạn đang hoạt động như mong đợi.

#### **Lớp 3: Kiểm thử Đầu cuối (E2E) / UI (Phần đỉnh)**

*   **Tỷ lệ:** Phần đỉnh của kim tự tháp, chiếm phần nhỏ nhất trong các bài test của bạn. Hướng dẫn là **~10%** tổng số bài test của bạn.
*   **Tại sao?**
    *   **Rất chậm:** Các bài test này cực kỳ chậm vì chúng tự động hóa các luồng công việc của người dùng thông qua toàn bộ hệ thống ứng dụng (ví dụ: khởi chạy trình duyệt, nhấp vào các nút).
    *   **Giòn/Dễ vỡ (Brittle/Flaky):** Chúng dễ bị hỏng vì những lý do không liên quan đến lỗi ứng dụng, chẳng hạn như một phần tử giao diện người dùng thay đổi ID, hết thời gian chờ mạng hoặc bản cập nhật trình duyệt.
    *   **Tốn kém:** Chúng tốn kém để viết, chạy và bảo trì.
*   **Mục tiêu:** Cung cấp một lần kiểm tra tổng thể cuối cùng rằng một vài hành trình quan trọng của người dùng thông qua toàn bộ hệ thống hoạt động chính xác. Bạn không muốn kiểm thử mọi trường hợp đặc biệt (edge case) ở đây; đó là công việc của các unit test.

#### **Quy tắc 70/20/10: Một Hướng dẫn, không phải là Luật**

Tỷ lệ 70/20/10 là một hướng dẫn chung. Tỷ lệ chính xác sẽ phụ thuộc vào kiến trúc ứng dụng của bạn. Nguyên tắc cốt lõi vẫn giữ nguyên: **Viết thật nhiều unit test nhanh, đơn giản và giảm dần số lượng các bài test phức tạp hơn khi bạn đi lên kim tự tháp.**

Một chiến lược kiểm thử không lành mạnh thường trông giống như một **"Hình nón kem"**—nhiều bài test E2E chậm, dễ vỡ, ít bài test tích hợp và gần như không có unit test nào. Điều này dẫn đến một quy trình kiểm thử chậm, không đáng tin cậy và tốn kém mà các nhà phát triển ngại chạy.

### **4. Thiết kế Trường hợp Kiểm thử (Test Case): Kịch bản Đầu vào, Đầu ra mong đợi, Biên và Tiêu cực**

Một **trường hợp kiểm thử (test case)** là một tập hợp các hành động cụ thể được thực hiện để xác minh một tính năng hoặc chức năng cụ thể. Đối với unit test, một test case gồm ba thành phần cốt lõi:

1.  **Given (Cho trước):** Trạng thái ban đầu hoặc thiết lập. (ví dụ: *Cho trước* một đối tượng người dùng có email là null).
2.  **When (Khi):** Hành động hoặc lệnh gọi phương thức bạn đang kiểm thử. (ví dụ: *Khi* phương thức `validate(user)` được gọi).
3.  **Then (Thì):** Kết quả hoặc khẳng định mong đợi. (ví dụ: *Thì* một ngoại lệ `IllegalArgumentException` sẽ được ném ra).

Cấu trúc "Given-When-Then" này là một quy ước phổ biến giúp các bài test trở nên rõ ràng và dễ đọc.

Hãy xem cách thiết kế các test case này. Giả sử chúng ta đang kiểm thử một phương thức đơn giản:

```java
public class RegistrationService {
    /**
     * Xác thực một tên người dùng.
     * Tên người dùng hợp lệ phải có độ dài từ 6 đến 20 ký tự, bao gồm cả hai đầu.
     * @param username Tên người dùng để xác thực.
     * @return true nếu hợp lệ.
     * @throws IllegalArgumentException nếu tên người dùng là null hoặc không hợp lệ.
     */
    public boolean isUsernameValid(String username) {
        if (username == null) {
            throw new IllegalArgumentException("Username cannot be null");
        }
        int length = username.length();
        return length >= 6 && length <= 20;
    }
}
```

Làm thế nào để chúng ta kiểm thử phương thức này một cách kỹ lưỡng? Chúng ta thiết kế các test case cho các kịch bản khác nhau.

#### **A. Kịch bản "Happy Path" (Kịch bản Tích cực)**

Đây là trường hợp rõ ràng nhất: kiểm thử với các đầu vào hợp lệ để đảm bảo phương thức hoạt động như mong đợi trong điều kiện bình thường.

*   **Đầu vào:** `"ValidUsername"` (13 ký tự)
*   **Đầu ra mong đợi:** `true`

Bạn cũng có thể kiểm thử một vài đầu vào hợp lệ khác để chắc chắn, như `"another_valid_one"`.

#### **B. Kịch bản Biên (Boundary Scenarios)**

Lỗi thường ẩn náu ở các cạnh của miền đầu vào của bạn. **Phân tích Giá trị Biên** là kỹ thuật kiểm thử tại chính xác các cạnh này. Đối với phương thức `isUsernameValid` của chúng ta, các biên là độ dài tối thiểu và tối đa cho phép.

Chúng ta phải kiểm thử:
*   **Tại biên dưới:**
    *   Đầu vào: Một tên người dùng có chính xác **6** ký tự (ví dụ: `"abcdef"`)
    *   Đầu ra mong đợi: `true`
*   **Ngay dưới biên dưới:**
    *   Đầu vào: Một tên người dùng có **5** ký tự (ví dụ: `"abcde"`)
    *   Đầu ra mong đợi: `false`
*   **Tại biên trên:**
    *   Đầu vào: Một tên người dùng có chính xác **20** ký tự (ví dụ: `"12345678901234567890"`)
    *   Đầu ra mong đợi: `true`
*   **Ngay trên biên trên:**
    *   Đầu vào: Một tên người dùng có **21** ký tự (ví dụ: `"123456789012345678901"`)
    *   Đầu ra mong đợi: `false`

Kiểm thử các giá trị biên là rất quan trọng. Đây là nơi các lỗi "sai lệch một" (off-by-one errors) (ví dụ: sử dụng `<` thay vì `<=`) bị phát hiện.

#### **C. Kịch bản Tiêu cực (Negative Scenarios)**

Kịch bản tiêu cực kiểm thử cách mã nguồn của bạn xử lý đầu vào không hợp lệ hoặc không mong muốn. Mục tiêu là đảm bảo phương thức của bạn hoạt động một cách ổn thỏa (ví dụ: trả về `false` hoặc ném ra một ngoại lệ cụ thể) thay vì bị sập.

Đối với phương thức của chúng ta, các kịch bản tiêu cực bao gồm:

*   **Đầu vào Null:**
    *   Đầu vào: `null`
    *   Đầu ra mong đợi: Một `IllegalArgumentException` được ném ra.
*   **Đầu vào Rỗng:**
    *   Đầu vào: `""` (0 ký tự, dưới mức biên)
    *   Đầu ra mong đợi: `false`
*   **Đầu vào là Khoảng trắng:**
    *   Đầu vào: `"      "` (một chuỗi các khoảng trắng)
    *   Đầu ra mong đợi: `false` hoặc `true` tùy thuộc vào yêu cầu (mã nguồn hiện tại của chúng ta sẽ trả về `true` nếu độ dài nằm trong khoảng 6 đến 20, điều này có thể là một lỗi! Đây là lý do tại sao kiểm thử rất có giá trị—nó làm rõ các yêu cầu).

Bằng cách thiết kế các test case cho ba loại này—happy path, biên và kịch bản tiêu cực—bạn có thể tự tin rằng phương thức `isUsernameValid` của mình mạnh mẽ và được kiểm thử tốt.

### **5. Khi nào nên viết Test (Phương pháp TDD và Test-After)**

Bạn đã thiết kế các test case của mình, nhưng khi nào bạn thực sự viết mã cho chúng? Có hai trường phái tư tưởng chính về vấn đề này, mỗi trường phái có quy trình làm việc và triết lý riêng.

#### **1. Phương pháp "Test-After" (Kiểm thử sau) truyền thống**

Đây là quy trình làm việc trực quan nhất, đặc biệt đối với những người mới làm quen với kiểm thử.

**Quy trình làm việc:**
1.  **Viết mã nguồn chính (Production Code):** Bạn viết lớp Java và các phương thức của nó trước, dựa trên các yêu cầu của tính năng.
2.  **Viết Unit Test:** Sau khi mã nguồn chính "hoàn thành", bạn viết các bài test để xác minh rằng nó hoạt động như mong đợi. Bạn sẽ tạo các test case cho happy path, biên và các kịch bản tiêu cực mà chúng ta vừa thảo luận.
3.  **Chạy Test & Sửa lỗi:** Bạn chạy các bài test. Nếu có bất kỳ bài nào thất bại, bạn quay lại mã nguồn chính, sửa lỗi và lặp lại cho đến khi tất cả các bài test đều qua.

**Ưu điểm:**
*   Cảm thấy tự nhiên và dễ dàng để bắt đầu.
*   Không yêu cầu một sự thay đổi lớn trong suy nghĩ so với phát triển truyền thống.

**Nhược điểm:**
*   **Kiểm thử như một việc làm sau:** Các bài test có thể bị giảm ưu tiên hoặc làm vội vàng khi gần đến hạn chót.
*   **Thiên vị xác nhận (Confirmation Bias):** Bạn có thể vô thức viết các bài test chỉ để xác nhận mã nguồn hoạt động (happy path) thay vì cố gắng làm hỏng nó.
*   **Mã nguồn không thể kiểm thử:** Bạn có thể thấy rằng mã nguồn bạn viết bị liên kết chặt chẽ và khó kiểm thử, buộc bạn phải tái cấu trúc nó (tốn thời gian) hoặc viết các bài test phức tạp, dễ vỡ.

---

#### **2. Phát triển Hướng Kiểm thử (Test-Driven Development - TDD)**

TDD đảo ngược quy trình truyền thống. Thay vì các bài test xác minh mã nguồn bạn đã viết, **các bài test sẽ định hướng việc phát triển chính mã nguồn đó.** Nó là một phương pháp thiết kế cũng như một phương pháp kiểm thử.

TDD tuân theo một chu trình lặp đi lặp lại đơn giản được gọi là **"Đỏ-Xanh-Tái cấu trúc" (Red-Green-Refactor).**

**Quy trình làm việc:**

1.  **ĐỎ - Viết một bài Test Thất bại:**
    *   Trước khi viết *bất kỳ* mã nguồn chính nào, bạn viết một unit test nhỏ, duy nhất cho chức năng bạn sắp triển khai.
    *   Ví dụ, bạn sẽ viết một bài test có tên `shouldReturnTrueForUsernameWith6Chars()`.
    *   Vì phương thức `isUsernameValid` thậm chí còn chưa tồn tại, bài test này sẽ không thể biên dịch. Đây là trạng thái "đỏ" cuối cùng. Sau đó, bạn tạo lớp và chữ ký phương thức tối thiểu chỉ để nó biên dịch, và nó sẽ thất bại ở phần khẳng định.

2.  **XANH - Làm cho bài Test Vượt qua:**
    *   Viết lượng mã nguồn chính **tối thiểu tuyệt đối** cần thiết để làm cho bài test đang thất bại vượt qua.
    *   Bạn không cố gắng viết mã hoàn hảo, thanh lịch. Mục tiêu đơn giản là để có được một thanh "xanh".
    *   Đối với bài test ở trên, bạn có thể mã hóa cứng giá trị trả về: `return true;`. Điều này có vẻ ngớ ngẩn, nhưng đó là một bước quan trọng. Nó chứng tỏ bài test đang hoạt động và buộc bạn phải viết một bài test khác (ví dụ: cho 5 ký tự) để thúc đẩy một giải pháp tổng quát hơn.

3.  **TÁI CẤU TRÚC - Cải thiện Mã nguồn:**
    *   Bây giờ bạn đã có một bài test vượt qua (một lưới an toàn), bạn có thể dọn dẹp mã nguồn bạn vừa viết.
    *   Bạn có thể tái cấu trúc mã nguồn chính và mã test, loại bỏ sự trùng lặp và cải thiện thiết kế, trong khi liên tục chạy các bài test để đảm bảo chúng vẫn xanh.
    *   Sau khi tái cấu trúc, chu trình lại bắt đầu với một test case mới (ví dụ: `shouldReturnFalseForUsernameWith5Chars()`).

**Ưu điểm của TDD:**
*   **Đảm bảo Khả năng Kiểm thử:** Bằng cách viết bài test trước, bạn buộc mình phải thiết kế mã nguồn vốn đã có thể kiểm thử và liên kết lỏng lẻo.
*   **Độ bao phủ Test cao:** Bạn tự nhiên sẽ có độ bao phủ 100% cho mã nguồn bạn viết thông qua phương pháp này.
*   **Ngăn chặn Việc Thiết kế quá mức (Over-Engineering):** Bạn chỉ viết mã để đáp ứng một bài test thất bại, điều này ngăn bạn thêm các tính năng "chỉ để đề phòng".
*   **Tạo ra một Lưới an toàn:** Bộ test phát triển cùng với mã nguồn, mang lại cho bạn sự tự tin to lớn để tái cấu trúc và thêm các tính năng mới sau này.

**Phương pháp nào tốt hơn?**

Mặc dù phương pháp Test-After là phổ biến, **TDD được nhiều người coi là một thông lệ chuyên nghiệp tốt nhất** dẫn đến phần mềm chất lượng cao hơn, dễ bảo trì hơn. Nó đòi hỏi kỷ luật và ban đầu có thể cảm thấy chậm hơn, nhưng nó mang lại lợi ích to lớn về lâu dài bằng cách giảm lỗi và cải thiện thiết kế mã nguồn.

Bất kể bạn chọn phương pháp nào, điều quan trọng nhất là bạn phải nhất quán và có kỷ luật trong việc viết các bài test kỹ lưỡng.

### **6. Các Anti-Pattern phổ biến trong Test**

Một anti-pattern là một phản ứng phổ biến đối với một vấn đề lặp đi lặp lại nhưng thường không hiệu quả và có nguy cơ phản tác dụng.

#### **1. Kiểm thử Giòn/Dễ vỡ (Brittle Tests)**

Một bài test giòn là một bài test thất bại vì những lý do không liên quan đến tính đúng đắn của unit đang được kiểm thử. Chúng thường bị hỏng khi có những thay đổi nhỏ, hợp lệ trong quá trình tái cấu trúc mã nguồn chính.

*   **Vấn đề:** Bài test quá phụ thuộc chặt chẽ vào *chi tiết triển khai* của phương thức, thay vì *hành vi* của nó.
*   **Ví dụ:** Hãy tưởng tượng một phương thức trả về một thông điệp chào mừng.
    ```java
    public String getWelcomeMessage(String username) {
        String message = "Welcome, " + username + "!";
        // một số logic khác...
        return message;
    }
    ```
    Một **bài test giòn** có thể khẳng định chuỗi chính xác:
    ```java
    // ANTI-PATTERN
    @Test
    void testWelcomeMessage() {
        String result = service.getWelcomeMessage("Alex");
        assertEquals("Welcome, Alex!", result);
    }
    ```
    Nếu một nhà phát triển tái cấu trúc mã nguồn chính để thêm một khoảng trắng (`"Welcome, " + username + " !" `), bài test sẽ thất bại, mặc dù hành vi cốt lõi (chào mừng người dùng) vẫn đúng.
*   **Giải pháp:** Kiểm thử hành vi thiết yếu. Một bài test tốt hơn sẽ linh hoạt hơn:
    ```java
    // TỐT HƠN
    @Test
    void testWelcomeMessage() {
        String result = service.getWelcomeMessage("Alex");
        assertTrue(result.startsWith("Welcome,"));
        assertTrue(result.contains("Alex"));
    }
    ```
    Bài test này xác minh các phần quan trọng của hành vi mà không quá nhạy cảm với những thay đổi định dạng nhỏ. **Hãy kiểm thử "cái gì", chứ không phải "cách nào".**

#### **2. Logic trong Test (Vòng lặp, câu lệnh `if`, v.v.)**

Một unit test nên đơn giản, xác định và dễ đọc. Nó chỉ nên có một lý do duy nhất, rõ ràng để thất bại.

*   **Vấn đề:** Việc đưa logic như các khối `if/else`, vòng lặp `for` hoặc khối `try/catch` vào một bài test làm cho chính bài test đó trở nên phức tạp. Nếu bài test thất bại, bạn phải gỡ lỗi bài test ngoài mã nguồn chính. Một bài test có logic có thể có lỗi của riêng nó.
*   **Ví dụ:**
    ```java
    // ANTI-PATTERN
    @Test
    void testMultipleUsernames() {
        // ... thiết lập
        for (String validName : validUsernames) {
            if (!service.isUsernameValid(validName)) {
                 fail("Đáng lẽ phải hợp lệ: " + validName);
            }
        }
    }
    ```
    Nếu bài test này thất bại, báo cáo chỉ nói "testMultipleUsernames thất bại". Bạn không biết ngay lập tức tên người dùng *nào* đã gây ra lỗi.
*   **Giải pháp:** Sử dụng các công cụ được thiết kế cho những kịch bản này, như **Parameterized Tests**, mà chúng ta sẽ đề cập trong phần JUnit 5. Mỗi đầu vào chạy như một bài test riêng biệt, và báo cáo test sẽ cho bạn biết chính xác đầu vào nào đã thất bại. Một bài test nên có một con đường thẳng, duy nhất từ thiết lập đến khẳng định.

#### **3. Đặc tả Quá chi tiết (Overspecification) hoặc Mocking Quá mức (Over-mocking)**

Đây là một anti-pattern rất phổ biến và tinh vi bạn sẽ gặp khi chúng ta đến với Mockito. Nó xảy ra khi một bài test quá cụ thể về các tương tác nội bộ của unit đang được kiểm thử.

*   **Vấn đề:** Bài test không chỉ xác minh kết quả cuối cùng mà còn xác minh *từng tương tác nội bộ* với các cộng tác viên (các phụ thuộc) của nó. Điều này làm cho bài test cực kỳ giòn. Nếu bạn tái cấu trúc việc triển khai nội bộ của một phương thức (ví dụ: gọi một phương thức trợ giúp hai lần thay vì một lần) mà không thay đổi kết quả cuối cùng của nó, bài test sẽ bị hỏng.
*   **Ví dụ khái niệm:** Hãy tưởng tượng một phương thức `checkout()` gọi `paymentService.charge()` và `emailService.sendReceipt()`. Một bài test cho `checkout()` có thể xác minh rằng cả hai phương thức đó đã được gọi. Nhưng nếu nó *cũng* xác minh rằng `loggingService.logInfo()` được gọi ba lần với các thông điệp cụ thể? Đó là đặc tả quá chi tiết. Việc ghi log là một chi tiết nội bộ; nó không ảnh hưởng đến kết quả hành vi của quy trình thanh toán.
*   **Giải pháp:** Tập trung vào việc xác minh các tương tác thiết yếu xác định hành vi. Việc xác minh `paymentService.charge()` đã được gọi là ổn vì đó là một tác dụng phụ quan trọng. Nhưng hãy tránh xác minh mọi lệnh gọi nội bộ, tầm thường.

---

**Tóm tắt Bài 1:**

*   **Unit Testing** xác minh các đoạn mã nhỏ, bị cô lập (unit).
*   Nó cung cấp một **lưới an toàn**, cải thiện **thiết kế mã nguồn**, và đóng vai trò như **tài liệu sống**.
*   **Kim tự tháp Kiểm thử** hướng dẫn chúng ta có nhiều **Unit** test nhanh (~70%), ít **Integration** test hơn (~20%), và rất ít **End-to-End** test chậm (~10%).
*   **Thiết kế Test Case** phải bao gồm **happy path**, các **biên**, và các **kịch bản tiêu cực**.
*   **TDD (Red-Green-Refactor)** là một phương pháp mạnh mẽ để thiết kế mã nguồn có thể kiểm thử.
*   Chúng ta phải tránh các **anti-pattern** phổ biến như **kiểm thử giòn**, **logic trong test**, và **đặc tả quá chi tiết**.