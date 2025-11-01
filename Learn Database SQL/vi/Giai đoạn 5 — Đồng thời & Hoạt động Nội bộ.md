#### **1. MVCC (Multi-Version Concurrency Control)**

**Lý thuyết**

Hãy quay lại với các mức độ cô lập. Làm thế nào mà CSDL có thể cho phép Transaction A đọc dữ liệu trong khi Transaction B đang sửa nó, mà không cần phải khóa (lock) mọi thứ lại? Câu trả lời trong hầu hết các CSDL hiện đại (PostgreSQL, MySQL InnoDB, Oracle) là **MVCC - Kiểm soát Đồng thời Đa phiên bản**.

**Ý tưởng cốt lõi của MVCC:**
Thay vì chỉ lưu trữ một phiên bản duy nhất (phiên bản mới nhất) của mỗi hàng, CSDL sẽ lưu trữ **nhiều phiên bản** của hàng đó. Khi một hàng được `UPDATE`, CSDL không ghi đè lên dữ liệu cũ. Thay vào đó, nó làm hai việc:
1.  Đánh dấu phiên bản cũ của hàng là "hết hạn" (expired) tại một thời điểm nào đó (ví dụ, theo ID của transaction đã sửa nó).
2.  Tạo ra một phiên bản mới của hàng đó với dữ liệu đã được cập nhật.

*   **Tương tự:** Hãy tưởng tượng một tài liệu Google Docs. Khi bạn sửa một câu, hệ thống không xóa câu cũ đi. Nó giữ lại lịch sử chỉnh sửa. Bất kỳ ai cũng có thể xem tài liệu ở một thời điểm cụ thể trong quá khứ. MVCC hoạt động tương tự cho mỗi hàng dữ liệu.

**MVCC giải quyết vấn đề "Đọc-Ghi" như thế nào?**
Nguyên tắc vàng của MVCC là: **"Writers don't block readers, and readers don't block writers" (Bên ghi không khóa bên đọc, và bên đọc không khóa bên ghi)**.

Khi một transaction bắt đầu, nó được cấp một "ảnh chụp" (snapshot) của CSDL tại thời điểm đó. Khi transaction này muốn đọc một hàng:
*   CSDL sẽ nhìn vào tất cả các phiên bản của hàng đó.
*   Nó sẽ tìm và trả về phiên bản **mới nhất** mà **"có thể nhìn thấy được"** đối với snapshot của transaction này. (Tức là, phiên bản đó phải được tạo bởi một transaction đã `COMMIT` *trước khi* transaction hiện tại bắt đầu).

**Ví dụ:**
*   **10:00:00 AM:** Hàng `Product A` có `price = 100`.
*   **10:01:00 AM:** Transaction T1 bắt đầu. Nó nhận một snapshot của CSDL tại thời điểm này.
*   **10:02:00 AM:** Transaction T2 bắt đầu, `UPDATE Product A SET price = 120` và **`COMMIT`**. Bây giờ, trong CSDL có 2 phiên bản của `Product A`: `(price=100, expired)` và `(price=120, current)`.
*   **10:03:00 AM:** Transaction T1 chạy `SELECT price FROM Product A`. Mặc dù phiên bản mới nhất là 120, nhưng nó được tạo *sau khi* snapshot của T1 được tạo. Do đó, CSDL sẽ bỏ qua phiên bản này và trả về phiên bản cũ hơn, có thể nhìn thấy được: `price = 100`.

=> Nhờ đó, T1 có một cái nhìn nhất quán về dữ liệu (như trong mức `REPEATABLE READ`) mà không cần phải khóa hàng `Product A` lại, cho phép T2 thực hiện việc cập nhật một cách tự do.

**Vấn đề dọn dẹp (Vacuuming/Purging):**
Tất nhiên, việc lưu nhiều phiên bản sẽ làm CSDL phình to. Do đó, các CSDL sử dụng MVCC đều có một tiến trình chạy ngầm (ví dụ: `VACUUM` trong PostgreSQL) để dọn dẹp các phiên bản hàng cũ mà không còn transaction nào có thể nhìn thấy được nữa.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "MVCC là gì và nó mang lại lợi ích gì cho việc kiểm soát đồng thời?"
    *   **Trả lời:** MVCC, hay Multi-Version Concurrency Control, là một cơ chế mà CSDL duy trì nhiều phiên bản của cùng một hàng dữ liệu để xử lý các truy cập đồng thời. Lợi ích lớn nhất của nó là nó cho phép các thao tác đọc và ghi diễn ra cùng lúc mà không xung đột với nhau. Người đọc sẽ thấy một "ảnh chụp" nhất quán của dữ liệu tại một thời điểm, trong khi người ghi có thể tạo ra các phiên bản mới. Điều này làm tăng đáng kể mức độ đồng thời và hiệu năng của hệ thống so với các cơ chế chỉ dựa trên khóa (locking).

*   **Câu hỏi:** "Nếu MVCC tốt như vậy, tại sao chúng ta vẫn cần khóa (locking)?"
    *   **Trả lời:** MVCC giải quyết xung đột đọc-ghi rất tốt. Tuy nhiên, nó không giải quyết được xung đột **ghi-ghi (write-write)**. Nếu hai transaction cùng cố gắng `UPDATE` **cùng một hàng** tại cùng một thời điểm, CSDL vẫn phải dùng đến cơ chế khóa. Transaction nào đến trước sẽ lấy được khóa ghi (write lock) trên hàng đó, và transaction còn lại sẽ phải chờ cho đến khi transaction đầu tiên `COMMIT` hoặc `ROLLBACK`.

*   **Câu hỏi:** "Tiến trình `VACUUM` trong PostgreSQL có vai trò gì liên quan đến MVCC?"
    *   **Trả lời:** MVCC tạo ra rất nhiều phiên bản hàng "chết" (dead tuples/rows). `VACUUM` có hai vai trò chính: 1) Thu hồi lại không gian lưu trữ bị chiếm bởi các phiên bản chết này để có thể tái sử dụng. 2) Ngăn chặn một vấn đề gọi là "Transaction ID Wraparound", đảm bảo CSDL có thể tiếp tục hoạt động sau khi đã sử dụng hết các ID giao dịch.

---

#### **2. Các Cơ chế Khóa (Locking Mechanisms)**

**Lý thuyết**

Khi MVCC không đủ (chủ yếu cho xung đột ghi-ghi), CSDL phải dùng đến **khóa (lock)**. Khóa là một cơ chế để đảm bảo rằng chỉ một transaction có thể truy cập hoặc sửa đổi một tài nguyên dữ liệu tại một thời điểm, nhằm tránh xung đột.

**Các loại khóa chính:**

1.  **Shared Lock (Khóa chia sẻ - S Lock):**
    *   Còn gọi là **Read Lock (Khóa đọc)**.
    *   Nhiều transaction có thể cùng nắm giữ một S Lock trên cùng một tài nguyên.
    *   Nếu một transaction đang giữ S Lock, các transaction khác có thể **đọc** (lấy S Lock khác) nhưng không thể **ghi** (không thể lấy X Lock).
    *   **Tương tự:** Nhiều người có thể cùng đọc một cuốn sách (S Lock), nhưng khi có ai đó đang đọc, không ai được phép viết thêm vào cuốn sách đó (X Lock).

2.  **Exclusive Lock (Khóa độc quyền - X Lock):**
    *   Còn gọi là **Write Lock (Khóa ghi)**.
    *   Chỉ **một** transaction duy nhất có thể giữ X Lock trên một tài nguyên tại một thời điểm.
    *   Nếu một transaction đang giữ X Lock, không có transaction nào khác có thể lấy bất kỳ loại khóa nào (cả S Lock và X Lock) trên tài nguyên đó. Chúng phải chờ.
    *   **Tương tự:** Khi một người đang viết vào cuốn sách (X Lock), không ai khác có thể đọc hay viết vào nó.

**Phạm vi của Khóa (Lock Granularity):**

Khóa có thể được áp dụng ở nhiều cấp độ khác nhau. Có một sự đánh đổi giữa mức độ đồng thời và chi phí quản lý khóa.

*   **Row-level Lock (Khóa mức hàng):**
    *   Khóa chỉ được áp dụng trên từng hàng riêng lẻ bị ảnh hưởng.
    *   **Ưu điểm:** Cung cấp mức độ đồng thời cao nhất. Các transaction thao tác trên các hàng khác nhau trong cùng một bảng có thể chạy song song.
    *   **Nhược điểm:** Tốn nhiều bộ nhớ và chi phí quản lý nếu một transaction phải khóa hàng triệu hàng.
    *   Đây là cơ chế mặc định cho các CSDL hiện đại như InnoDB và PostgreSQL.

*   **Page-level Lock (Khóa mức trang):**
    *   Khóa được áp dụng trên một "trang" dữ liệu (thường là 8KB hoặc 16KB) trên đĩa.
    *   Là một sự cân bằng giữa khóa hàng và khóa bảng.

*   **Table-level Lock (Khóa mức bảng):**
    *   Toàn bộ bảng bị khóa.
    *   **Ưu điểm:** Chi phí quản lý rất thấp.
    *   **Nhược điểm:** Giết chết tính đồng thời. Khi một transaction khóa cả bảng, không có transaction nào khác có thể truy cập vào bảng đó.
    *   Thường được sử dụng cho các thao tác DDL (`ALTER TABLE`) hoặc khi một transaction cập nhật một phần rất lớn của bảng.

*   **Lock Escalation (Leo thang khóa):**
    *   Là một cơ chế tự động của CSDL. Khi một transaction lấy quá nhiều khóa ở mức độ chi tiết (ví dụ, hàng ngàn row lock), CSDL có thể quyết định "leo thang" nó thành một khóa ở mức độ cao hơn (ví dụ, table lock) để tiết kiệm tài nguyên quản lý.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Hãy so sánh giữa Shared Lock và Exclusive Lock."
    *   **Trả lời:** Shared Lock (S Lock) cho phép nhiều transaction cùng đọc một tài nguyên, nhưng ngăn chặn việc ghi. Nó tương thích với các S Lock khác. Exclusive Lock (X Lock) chỉ cho phép một transaction duy nhất ghi vào tài nguyên và ngăn chặn cả việc đọc và ghi từ các transaction khác. Nó không tương thích với bất kỳ loại khóa nào khác.

*   **Câu hỏi:** "Tại sao khóa mức hàng (row-level lock) lại được ưa chuộng trong các hệ thống OLTP (Online Transaction Processing)?"
    *   **Trả lời:** Các hệ thống OLTP (như trang thương mại điện tử, hệ thống ngân hàng) có đặc điểm là có rất nhiều transaction nhỏ, ngắn, chạy đồng thời và thường chỉ thao tác trên một vài hàng dữ liệu (ví dụ: cập nhật một đơn hàng, kiểm tra số dư một tài khoản). Khóa mức hàng cho phép các transaction này chạy song song mà không ảnh hưởng lẫn nhau miễn là chúng không đụng vào cùng một hàng. Điều này tối đa hóa thông lượng (throughput) và khả năng đáp ứng của hệ thống.

*   **Câu hỏi:** "Câu lệnh `SELECT ... FOR UPDATE` làm gì?"
    *   **Trả lời:** Đây là một câu lệnh rất quan trọng. Một câu `SELECT` thông thường trong MVCC sẽ không lấy khóa ghi. `SELECT ... FOR UPDATE` sẽ thực hiện việc đọc dữ liệu, nhưng đồng thời nó sẽ đặt một **Exclusive Lock (X Lock)** trên các hàng mà nó đọc được. Mục đích là để "khóa" các hàng đó lại, ngăn không cho transaction nào khác thay đổi chúng, trước khi transaction hiện tại thực hiện một thao tác `UPDATE` trên chính các hàng đó sau này. Nó thường được dùng trong các kịch bản "đọc-sửa-ghi" để tránh race condition.

---

#### **3. Deadlock: Cách phát hiện và ngăn chặn**

**Lý thuyết**

**Deadlock (Bế tắc)** là một tình huống xảy ra khi hai (hoặc nhiều) transaction đang chờ đợi lẫn nhau để nhả khóa, và không transaction nào có thể tiếp tục.

*   **Tương tự kinh điển:**
    1.  Giao dịch A khóa Hàng 1 và đang cố gắng lấy khóa trên Hàng 2.
    2.  Giao dịch B khóa Hàng 2 và đang cố gắng lấy khóa trên Hàng 1.
*   A không thể tiếp tục vì B đang giữ Hàng 2.
*   B không thể tiếp tục vì A đang giữ Hàng 1.
*   Cả hai sẽ chờ đợi nhau mãi mãi.

**Làm thế nào CSDL xử lý Deadlock?**
CSDL không để chúng chờ mãi. Hầu hết các CSDL đều có một **tiến trình phát hiện deadlock** chạy ngầm định kỳ.
1.  Nó xây dựng một "đồ thị chờ" (waits-for graph) của các transaction và các khóa mà chúng đang yêu cầu.
2.  Nếu nó phát hiện ra một **chu trình (cycle)** trong đồ thị (A chờ B, B chờ A), nó xác định rằng đã có deadlock.
3.  Nó sẽ chọn một trong các transaction làm **"nạn nhân" (victim)**.
4.  Nó sẽ **tự động `ROLLBACK`** toàn bộ transaction nạn nhân, giải phóng các khóa mà nó đang giữ.
5.  Điều này cho phép transaction còn lại tiếp tục.
6.  Ứng dụng client của transaction nạn nhân sẽ nhận được một thông báo lỗi deadlock.

**Cách ngăn chặn Deadlock (từ phía ứng dụng):**

Phòng bệnh hơn chữa bệnh. Dưới đây là các kỹ thuật phổ biến nhất:

1.  **Truy cập tài nguyên theo một thứ tự nhất quán:**
    *   Đây là cách hiệu quả nhất. Hãy quy ước rằng tất cả các transaction trong ứng dụng của bạn phải khóa các tài nguyên (bảng, hàng) theo cùng một thứ tự.
    *   Ví dụ: Luôn khóa bảng `Accounts` trước rồi mới đến bảng `Transactions`. Nếu A và B đều tuân thủ quy tắc này, deadlock sẽ không bao giờ xảy ra. A sẽ khóa `Accounts` trước, B sẽ phải chờ. A sẽ tiếp tục khóa `Transactions` và hoàn thành công việc.

2.  **Giữ transaction ngắn và nhanh:**
    *   Transaction càng kéo dài, nó càng giữ khóa lâu, và khả năng xảy ra xung đột và deadlock càng cao.

3.  **Khóa ở mức độ chi tiết nhất có thể:**
    *   Sử dụng row-level lock thay vì table-level lock nếu được. Khóa ít tài nguyên hơn sẽ giảm khả năng xung đột.

4.  **Sử dụng mức cô lập thấp hơn (nếu có thể):**
    *   Mức cô lập thấp hơn (như `READ COMMITTED`) sử dụng ít khóa hơn và giữ chúng trong thời gian ngắn hơn, làm giảm nguy cơ deadlock.

5.  **Sử dụng `SELECT ... FOR UPDATE` với `NOWAIT` hoặc `SKIP LOCKED`:**
    *   **`NOWAIT`:** Nếu hàng đã bị khóa, thay vì chờ, câu lệnh sẽ báo lỗi ngay lập tức. Ứng dụng có thể bắt lỗi này và thử lại sau.
    *   **`SKIP LOCKED`:** Bỏ qua các hàng đã bị khóa và chỉ xử lý các hàng còn lại. Rất hữu ích trong các kịch bản xử lý hàng đợi (queue processing) nơi nhiều worker cùng lấy việc từ một bảng.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Deadlock là gì? Hãy cho một ví dụ."
    *   **Trả lời:** (Sử dụng định nghĩa và ví dụ kinh điển ở trên về Giao dịch A và B cùng khóa Hàng 1 và Hàng 2).

*   **Câu hỏi:** "Làm thế nào bạn có thể giảm thiểu khả năng xảy ra deadlock trong ứng dụng của mình?"
    *   **Trả lời:** Cách quan trọng nhất là đảm bảo các transaction truy cập tài nguyên theo một thứ tự nhất quán. Ví dụ, nếu cập nhật tài khoản người dùng và sản phẩm, hãy luôn cập nhật bảng `users` trước rồi mới đến bảng `products`. Ngoài ra, tôi sẽ cố gắng giữ cho các transaction càng ngắn càng tốt, chỉ thực hiện các thao tác cần thiết bên trong khối transaction, và sử dụng `SELECT ... FOR UPDATE` một cách cẩn thận để khóa các hàng cần thiết ngay từ đầu.

*   **Câu hỏi:** "Khi ứng dụng của bạn nhận được một lỗi deadlock từ CSDL, nó nên làm gì?"
    *   **Trả lời:** Lỗi deadlock là một loại lỗi tạm thời (transient error). Ứng dụng nên được thiết kế để có thể bắt được loại lỗi này và tự động **thử lại (retry)** toàn bộ transaction sau một khoảng thời gian chờ ngẫu nhiên ngắn (exponential backoff). Vì CSDL đã rollback transaction "nạn nhân", nên việc thử lại có khả năng thành công ở lần sau.

---

#### **4. Mở rộng quy mô CSDL (Database Scaling)**

**Lý thuyết**

Khi ứng dụng của bạn phát triển, một CSDL server duy nhất sẽ không thể xử lý hết tải. Có hai chiến lược chính để mở rộng quy mô.

**1. Vertical Scaling (Mở rộng theo chiều dọc - Scale Up):**
*   **Là gì:** Tăng cường sức mạnh cho server hiện tại.
*   **Hành động:** Nâng cấp CPU, thêm RAM, sử dụng ổ cứng nhanh hơn (SSD/NVMe).
*   **Ưu điểm:**
    *   Dễ dàng thực hiện. Không cần thay đổi code ứng dụng.
*   **Nhược điểm:**
    *   **Có giới hạn:** Sẽ đến lúc bạn không thể nâng cấp phần cứng được nữa (đã mua con server mạnh nhất).
    *   **Đắt đỏ:** Chi phí tăng theo cấp số nhân.
    *   **Điểm lỗi duy nhất (Single Point of Failure):** Nếu server này bị sập, toàn bộ hệ thống ngừng hoạt động.

**2. Horizontal Scaling (Mở rộng theo chiều ngang - Scale Out):**
*   **Là gì:** Thêm nhiều server hơn vào hệ thống.
*   **Hành động:** Thay vì một con server "khổng lồ", bạn có nhiều con server "bình thường" cùng làm việc.
*   **Ưu điểm:**
    *   **Khả năng mở rộng gần như vô hạn:** Cần thêm năng lực? Chỉ cần thêm server mới.
    *   **Hiệu quả về chi phí:** Các server thông thường rẻ hơn nhiều.
    *   **Tính sẵn sàng cao (High Availability):** Nếu một server bị sập, các server khác vẫn hoạt động, hệ thống không bị gián đoạn hoàn toàn.
*   **Nhược điểm:**
    *   **Phức tạp:** Đòi hỏi phải thay đổi kiến trúc hệ thống và ứng dụng.

**Các kỹ thuật Horizontal Scaling cho CSDL:**

*   **Read Replicas (Bản sao chỉ đọc):**
    *   **Cách hoạt động:** Tạo ra nhiều bản sao của CSDL chính (master). Tất cả các thao tác **ghi (`INSERT`, `UPDATE`, `DELETE`)** đều đi vào master. Master sau đó sẽ sao chép (replicate) dữ liệu sang các bản sao (replicas). Các thao tác **đọc (`SELECT`)** sẽ được phân bổ đều cho các replicas.
    *   **Khi nào dùng:** Rất hiệu quả cho các ứng dụng có tỷ lệ đọc cao hơn nhiều so với ghi (ví dụ: blog, trang tin tức, trang thương mại điện tử).
    *   **Nhược điểm:** Có một độ trễ sao chép (replication lag). Dữ liệu trên replica có thể hơi cũ hơn so với master.

*   **Sharding (Phân mảnh):**
    *   **Cách hoạt động:** Chia nhỏ dữ liệu của bạn ra và lưu trữ trên nhiều CSDL server khác nhau (gọi là các shard). Mỗi shard chứa một phần dữ liệu.
    *   **Ví dụ:** Shard 1 chứa dữ liệu của người dùng có ID từ 1-1,000,000. Shard 2 chứa dữ liệu của người dùng có ID từ 1,000,001-2,000,000.
    *   **Khi nào dùng:** Khi CSDL của bạn quá lớn để chứa trên một server duy nhất, hoặc khi lưu lượng ghi quá cao không thể xử lý bởi một master. Đây là giải pháp cuối cùng cho các hệ thống quy mô rất lớn.
    *   **Nhược điểm:** Cực kỳ phức tạp để triển khai và quản lý. Các thao tác `JOIN` qua các shard khác nhau rất khó khăn và tốn kém. Cần một tầng định tuyến (routing layer) để biết dữ liệu nằm ở shard nào.

*   **Partitioning (Phân vùng):**
    *   **Lưu ý:** Thường bị nhầm lẫn với Sharding, nhưng chúng khác nhau. Partitioning là việc chia một **bảng lớn** thành nhiều **phần nhỏ hơn** nhưng vẫn nằm **trên cùng một CSDL server**.
    *   **Ví dụ:** Phân vùng bảng `logs` theo tháng. Sẽ có các partition `logs_2024_01`, `logs_2024_02`...
    *   **Lợi ích:** Cải thiện hiệu năng truy vấn (CSDL chỉ cần quét partition liên quan) và dễ dàng quản lý dữ liệu (ví dụ: xóa dữ liệu cũ bằng cách `DROP` cả một partition thay vì `DELETE` hàng triệu hàng).

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "So sánh Vertical Scaling và Horizontal Scaling."
    *   **Trả lời:** Vertical scaling là làm cho một server mạnh hơn. Horizontal scaling là thêm nhiều server hơn. Vertical scaling đơn giản nhưng đắt đỏ và có giới hạn. Horizontal scaling phức tạp hơn nhưng có khả năng mở rộng gần như vô hạn và tăng tính sẵn sàng.

*   **Câu hỏi:** "Replication lag là gì và làm thế nào để xử lý nó trong ứng dụng của bạn?"
    *   **Trả lời:** Replication lag là độ trễ thời gian giữa việc dữ liệu được ghi vào master và việc nó xuất hiện trên read replica. Để xử lý nó, ứng dụng có thể áp dụng chiến lược "read-after-write consistency". Tức là, ngay sau khi một người dùng thực hiện một thao tác ghi (ví dụ: đăng một bài viết), các truy vấn đọc tiếp theo từ chính người dùng đó trong một khoảng thời gian ngắn sẽ được định tuyến thẳng đến master để đảm bảo họ thấy được dữ liệu mới nhất của mình. Các truy vấn từ những người dùng khác vẫn có thể đi đến replica.

*   **Câu hỏi:** "Sự khác biệt giữa Sharding và Partitioning là gì?"
    *   **Trả lời:** Cả hai đều là kỹ thuật chia nhỏ dữ liệu. Điểm khác biệt chính là nơi dữ liệu được lưu trữ. Partitioning chia một bảng thành các phần nhỏ hơn trên **cùng một server CSDL**. Sharding chia dữ liệu của bạn ra và lưu trữ trên **nhiều server CSDL khác nhau**. Sharding là một chiến lược mở rộng theo chiều ngang, trong khi partitioning chủ yếu là một kỹ thuật tối ưu hóa hiệu năng và quản lý trên một server duy nhất.