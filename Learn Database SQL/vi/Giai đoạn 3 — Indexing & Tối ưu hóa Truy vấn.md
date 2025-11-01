#### **1. Clustered Index vs Non-Clustered Index (Trực quan hóa B-Tree)**

**Lý thuyết**

**Tại sao chúng ta cần Index (Chỉ mục)?**
Hãy tưởng tượng bạn có một cuốn từ điển dày 1000 trang không được sắp xếp theo thứ tự bảng chữ cái. Để tìm một từ, bạn sẽ phải lật từng trang một từ đầu đến cuối. Đây gọi là **Full Table Scan (Quét toàn bộ bảng)**, và nó cực kỳ chậm.

**Index** hoạt động giống như mục lục của một cuốn sách hoặc thứ tự sắp xếp của cuốn từ điển. Nó là một cấu trúc dữ liệu riêng biệt (thường là **B-Tree**) cho phép CSDL tìm đến vị trí của dữ liệu một cách nhanh chóng mà không cần phải đọc toàn bộ bảng.

**Cấu trúc B-Tree (Cây B):**
Hãy hình dung một cây lộn ngược:
*   **Root Node (Nút gốc):** Điểm bắt đầu của việc tìm kiếm.
*   **Branch Nodes (Nút nhánh):** Các nút ở giữa, chứa các khoảng giá trị để điều hướng tìm kiếm. Ví dụ: "Các từ bắt đầu từ A-M đi sang trái, N-Z đi sang phải".
*   **Leaf Nodes (Nút lá):** Tầng dưới cùng của cây, chứa các giá trị thực tế và một con trỏ chỉ đến vị trí của hàng dữ liệu trên đĩa.

Nhờ cấu trúc này, việc tìm kiếm một hàng trong hàng triệu hàng chỉ mất một vài bước nhảy (độ phức tạp O(log n)), thay vì phải duyệt qua tất cả (O(n)).

**Clustered Index (Chỉ mục Phân cụm)**

*   **Tương tự:** Một cuốn **từ điển**.
*   **Cách hoạt động:** Clustered index không chỉ sắp xếp các khóa chỉ mục, nó còn **sắp xếp thứ tự vật lý của các hàng dữ liệu trong bảng** theo chính thứ tự của chỉ mục đó. Các nút lá (leaf nodes) của B-Tree **chính là các trang dữ liệu (data pages)** chứa toàn bộ hàng.
*   **Đặc điểm quan trọng:**
    *   Vì dữ liệu chỉ có thể được sắp xếp vật lý theo một cách duy nhất, mỗi bảng chỉ có thể có **TỐI ĐA MỘT** Clustered Index.
    *   Khi bạn tạo một `PRIMARY KEY` trên một bảng, hầu hết các hệ CSDL (như SQL Server, MySQL InnoDB) sẽ tự động tạo một Clustered Index trên cột đó.

**Non-Clustered Index (Chỉ mục Không phân cụm)**

*   **Tương tự:** Phần **mục lục ở cuối một cuốn sách**.
*   **Cách hoạt động:** Non-clustered index là một cấu trúc dữ liệu hoàn toàn tách biệt với dữ liệu của bảng. Dữ liệu trong bảng vẫn được lưu trữ theo một trật tự khác (thường là theo clustered index hoặc theo thứ tự chèn vào).
    *   Các nút lá của B-Tree trong non-clustered index chứa giá trị của cột được đánh chỉ mục và một **"con trỏ" (pointer)**. Con trỏ này chỉ đến vị trí thực tế của hàng dữ liệu. Con trỏ này có thể là **RowID** (địa chỉ vật lý) hoặc là **giá trị của khóa clustered index**.
*   **Đặc điểm quan trọng:**
    *   Vì nó là một cấu trúc riêng biệt, một bảng có thể có **NHIỀU** Non-Clustered Index.

| Đặc điểm                  | Clustered Index                                     | Non-Clustered Index                                          |
| ------------------------- | --------------------------------------------------- | ------------------------------------------------------------ |
| **Số lượng mỗi bảng**     | Chỉ một                                             | Nhiều                                                        |
| **Sắp xếp dữ liệu vật lý** | Có, bảng được sắp xếp theo chỉ mục này.             | Không, đây là cấu trúc riêng.                                |
| **Nút lá (Leaf Node)**    | Chứa toàn bộ dữ liệu của hàng.                      | Chứa giá trị chỉ mục và một con trỏ đến hàng dữ liệu.        |
| **Kích thước**             | Thường nhỏ hơn (chỉ có cấu trúc B-Tree).            | Lớn hơn, vì nó lưu trữ cả giá trị cột và con trỏ.            |
| **Tốc độ truy vấn**       | Rất nhanh cho truy vấn theo khoảng (range query).    | Nhanh cho truy vấn điểm (point query), nhưng có thể cần thêm một bước "Key Lookup" để lấy dữ liệu còn lại, làm chậm hơn một chút. |

---

**Ví dụ SQL**

```sql
CREATE TABLE Users (
    user_id INT PRIMARY KEY, -- Mặc định, InnoDB sẽ tạo CLUSTERED INDEX trên cột này
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    created_at DATETIME
);

-- Tạo một NON-CLUSTERED INDEX trên cột email để tăng tốc độ tìm kiếm khi người dùng đăng nhập
CREATE UNIQUE INDEX idx_users_email ON Users(email);
```

---

**Bài tập nhỏ**

Bạn có một bảng `Orders` với các cột `order_id` (PK, INT, auto-increment), `customer_id` (INT), `order_date` (DATETIME), `total_amount` (DECIMAL).

1.  Hệ CSDL có thể sẽ tạo loại Index nào trên cột `order_id`? Tại sao?
2.  Bạn muốn tối ưu hóa việc tìm kiếm tất cả các đơn hàng của một khách hàng cụ thể. Bạn nên tạo thêm loại Index nào và trên cột nào?

**Giải pháp**

1.  Hệ CSDL sẽ tạo một **Clustered Index** trên cột `order_id` vì nó là Khóa chính. Đây là lựa chọn tốt vì `order_id` là một giá trị duy nhất, hẹp (kiểu INT) và tăng dần, giúp tránh phân mảnh dữ liệu khi chèn bản ghi mới.
2.  Bạn nên tạo một **Non-Clustered Index** trên cột `customer_id`. Điều này sẽ giúp câu lệnh `WHERE customer_id = ?` tìm kiếm cực kỳ nhanh chóng mà không cần quét toàn bộ bảng.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt cơ bản nhất giữa Clustered và Non-Clustered Index là gì?"
    *   **Trả lời:** Sự khác biệt cơ bản nhất nằm ở cách chúng lưu trữ dữ liệu. Clustered Index sắp xếp lại thứ tự vật lý của chính các hàng dữ liệu trong bảng, giống như một cuốn từ điển. Vì vậy mỗi bảng chỉ có một. Non-Clustered Index là một cấu trúc riêng biệt, giống như mục lục sách, nó chỉ chứa con trỏ đến dữ liệu thật, vì vậy một bảng có thể có nhiều.

*   **Câu hỏi:** "Tại sao một bảng chỉ có thể có một Clustered Index?"
    *   **Trả lời:** Bởi vì Clustered Index quy định thứ tự sắp xếp vật lý của dữ liệu trên đĩa. Dữ liệu không thể được sắp xếp vật lý theo hai hay nhiều cách cùng một lúc.

*   **Câu hỏi:** "Việc tra cứu bằng Non-Clustered Index đôi khi cần một bước gọi là 'Key Lookup' hoặc 'Bookmark Lookup'. Đó là gì?"
    *   **Trả lời:** Khi bạn tìm kiếm trên một non-clustered index và câu truy vấn của bạn yêu cầu các cột không có trong index đó, CSDL sẽ thực hiện 2 bước: 1) Tìm giá trị trên non-clustered index để lấy con trỏ (ví dụ: lấy `user_id`). 2) Dùng con trỏ đó để tìm đến vị trí của hàng trong clustered index và lấy nốt các cột còn lại (`username`, `created_at`). Bước thứ hai này chính là Key Lookup và nó làm phát sinh thêm chi phí I/O.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Chọn một cột **hẹp (narrow)**, **tĩnh (static)**, và **tăng dần (ever-increasing)** làm khóa cho Clustered Index (ví dụ: `INT AUTO_INCREMENT`). Điều này giúp các bản ghi mới luôn được chèn vào cuối bảng, giảm thiểu việc "page splits" (tách trang dữ liệu) và giữ cho cấu trúc bảng ngăn nắp.
*   **Sai lầm thường gặp:** Chọn một cột **rộng (wide)**, **ngẫu nhiên** hoặc **thường xuyên bị cập nhật** làm khóa cho Clustered Index. Ví dụ, dùng một chuỗi UUID làm khóa chính. Khi chèn một UUID mới, nó có thể nằm ở bất kỳ đâu, buộc CSDL phải liên tục sắp xếp lại các trang dữ liệu, gây ra sự phân mảnh và làm giảm hiệu năng nghiêm trọng.

---

#### **2. Cách Index cải thiện/làm tệ đi hiệu năng**

**Lý thuyết**

Index là một con dao hai lưỡi. Sử dụng đúng cách, nó có thể tăng tốc độ truy vấn lên hàng ngàn lần. Lạm dụng nó, hệ thống của bạn sẽ chậm đi đáng kể.

**Index Cải thiện Hiệu năng ĐỌC (`SELECT`) như thế nào?**

Index tăng tốc đáng kể các hoạt động sau:
1.  **Mệnh đề `WHERE`:** Đây là trường hợp sử dụng phổ biến nhất. Thay vì quét toàn bộ bảng để tìm các hàng thỏa mãn điều kiện, CSDL sử dụng Index Seek để nhảy thẳng đến các hàng cần thiết.
2.  **Mệnh đề `JOIN`:** Khi bạn `JOIN` hai bảng dựa trên các cột `tableA.id = tableB.a_id`, nếu cột `tableB.a_id` được đánh index, CSDL có thể tìm các hàng khớp trong `tableB` một cách cực kỳ hiệu quả. Nếu không, nó có thể phải quét toàn bộ `tableB` cho mỗi hàng trong `tableA`.
3.  **Mệnh đề `ORDER BY`:** Nếu bạn `ORDER BY` một cột đã được đánh index, CSDL có thể không cần phải thực hiện một thao tác sắp xếp riêng biệt tốn kém. Nó chỉ cần duyệt qua các nút lá của index theo thứ tự là đã có kết quả được sắp xếp.
4.  **Hàm tổng hợp `MIN()` và `MAX()`:** Tìm giá trị nhỏ nhất hoặc lớn nhất trên một cột có index chỉ đơn giản là đi đến nút lá đầu tiên hoặc cuối cùng của B-Tree, một thao tác gần như tức thời.

**Index Làm Tệ đi Hiệu năng GHI (`INSERT`, `UPDATE`, `DELETE`) như thế nào?**

Đây là cái giá phải trả cho việc đọc nhanh.
1.  **`INSERT`:** Khi bạn chèn một hàng mới, CSDL không chỉ phải ghi dữ liệu vào bảng mà còn phải cập nhật **tất cả các index** có trên bảng đó. Mỗi index phải được cập nhật để bao gồm giá trị mới ở đúng vị trí trong cấu trúc B-Tree của nó. Càng nhiều index, `INSERT` càng chậm.
2.  **`DELETE`:** Tương tự, khi xóa một hàng, CSDL phải xóa dữ liệu khỏi bảng và cũng phải xóa các mục tương ứng khỏi tất cả các index.
3.  **`UPDATE`:** Đây là trường hợp tệ nhất. Nếu bạn cập nhật một cột **được đánh index**, CSDL thực chất phải làm hai việc: một thao tác `DELETE` (cho giá trị cũ) và một thao tác `INSERT` (cho giá trị mới) bên trong cấu trúc B-Tree của index đó.

**Các trường hợp khác Index hoạt động không hiệu quả:**

*   **Các cột có Lượng giá trị riêng biệt thấp (Low Cardinality):** Đánh index trên cột `gender` (chỉ có vài giá trị 'Nam', 'Nữ', 'Khác') gần như vô dụng. Khi bạn truy vấn `WHERE gender = 'Nam'`, CSDL ước tính rằng nó phải đọc khoảng 50% bảng. Trong trường hợp này, việc quét toàn bộ bảng có thể còn nhanh hơn là đọc index rồi lại nhảy đến dữ liệu.
*   **Các bảng quá nhỏ:** Với các bảng chỉ có vài trăm hàng, chi phí để quét toàn bộ bảng là rất nhỏ. Việc sử dụng index có thể còn chậm hơn vì CSDL phải đọc cả cấu trúc index và bảng.
*   **Lưu trữ (Storage):** Mỗi index đều chiếm dung lượng trên đĩa. Với các bảng rất lớn, các index cũng có thể trở nên rất lớn.

---

**Bài tập nhỏ**

Một trang web thương mại điện tử có bảng `Products`. Bảng này có các đặc điểm sau:
*   Giá sản phẩm (`price`) được cập nhật hàng ngày do chương trình khuyến mãi.
*   Tên sản phẩm (`product_name`) rất ít khi thay đổi.
*   Người dùng thường xuyên tìm kiếm sản phẩm theo tên.
*   Hệ thống có một job chạy ngầm để phân tích và cập nhật số liệu tồn kho (`stock_quantity`) liên tục.

Dựa trên thông tin này, việc đánh index trên cột `price` có phải là ý tưởng tốt không? Tại sao?

**Giải pháp**

Không, việc đánh index trên cột `price` có thể không phải là ý tưởng tốt.
*   **Lý do:** Cột `price` bị cập nhật rất thường xuyên (hàng ngày). Mỗi lần `UPDATE`, CSDL sẽ phải tốn thêm chi phí để cập nhật lại cấu trúc B-Tree của index trên cột `price`. Mặc dù index có thể giúp tìm kiếm theo giá nhanh hơn, nhưng chi phí phải trả cho việc ghi/cập nhật liên tục có thể lớn hơn lợi ích đó, làm chậm toàn bộ hệ thống. Ngược lại, đánh index trên `product_name` là một ý tưởng tuyệt vời vì nó được đọc thường xuyên và ít khi bị thay đổi.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Bạn có một bảng với rất nhiều thao tác `INSERT` mỗi giây (ví dụ: bảng logs). Bạn sẽ áp dụng chiến lược đánh index như thế nào cho bảng này?"
    *   **Trả lời:** Tôi sẽ rất hạn chế số lượng index trên bảng này. Mỗi index sẽ làm chậm đáng kể mỗi thao tác `INSERT`. Tôi sẽ chỉ tạo các index thực sự cần thiết cho các truy vấn đọc quan trọng, và chấp nhận rằng các truy vấn đọc khác có thể không được tối ưu. Nếu có nhuá cầu phân tích dữ liệu log, tôi sẽ xem xét chuyển dữ liệu định kỳ sang một bảng khác (data warehouse) được tối ưu cho việc đọc với nhiều index hơn.

*   **Câu hole:** "Tại sao một câu lệnh `SELECT` trông có vẻ đơn giản nhưng lại chạy rất chậm?"
    *   **Trả lời:** Có vài lý do phổ biến:
        1.  **Thiếu Index:** Cột trong mệnh đề `WHERE` hoặc `JOIN` không được đánh index, dẫn đến Full Table Scan trên một bảng lớn.
        2.  **Index không được sử dụng:** Có index nhưng CSDL không dùng được, ví dụ như áp dụng một hàm lên cột trong `WHERE` (ví dụ: `WHERE YEAR(order_date) = 2024`).
        3.  **Dữ liệu lớn và Cardinality thấp:** CSDL quyết định rằng quét bảng sẽ hiệu quả hơn là dùng index.
        4.  **Locking:** Câu lệnh đang phải chờ một transaction khác nhả khóa (lock) trên các hàng mà nó muốn đọc.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** **Quy tắc Vàng về Index:** Đánh index trên các cột được dùng thường xuyên trong mệnh đề `WHERE`, `JOIN`, và `ORDER BY`.
*   **Thực tiễn tốt nhất:** Sử dụng **Composite Index (Chỉ mục Phức hợp)** cho các truy vấn lọc trên nhiều cột. Ví dụ, nếu bạn thường xuyên chạy `WHERE lastname = ? AND firstname = ?`, hãy tạo một index duy nhất trên cả hai cột: `CREATE INDEX idx_name ON Users(lastname, firstname)`. Thứ tự các cột trong composite index rất quan trọng.
*   **Sai lầm thường gặp:** **Đánh index trên mọi cột.** Đây là một sai lầm kinh điển của người mới bắt đầu. Nó làm chậm đáng kể hiệu năng ghi và tốn rất nhiều dung lượng đĩa.
*   **Sai lầm thường gặp:** **Tạo các index riêng lẻ thay vì composite index.** Nếu bạn có một index trên `lastname` và một index khác trên `firstname`, CSDL có thể chỉ sử dụng một trong hai (hoặc kết hợp chúng một cách không hiệu quả). Một composite index trên `(lastname, firstname)` sẽ hiệu quả hơn nhiều cho truy vấn lọc trên cả hai cột.

#### **3. `EXPLAIN` / `EXPLAIN ANALYZE` (Cách CSDL thực thi truy vấn)**

**Lý thuyết**

`EXPLAIN` là một trong những công cụ mạnh mẽ và quan trọng nhất để tối ưu hóa CSDL. Nó không thực sự chạy câu truy vấn của bạn, thay vào đó, nó yêu cầu CSDL cho bạn xem **kế hoạch thực thi (execution plan)** mà CSDL đã vạch ra để thực hiện câu truy vấn đó.

*   **Tương tự:** Hãy tưởng tượng bạn yêu cầu Google Maps chỉ đường từ điểm A đến điểm B. `EXPLAIN` giống như việc bạn xem trước lộ trình chi tiết: "đi thẳng đường X 5km, rẽ trái vào đường Y, đi tiếp 2km...". Bạn có thể xem lộ trình này để biết nó có hợp lý không (ví dụ: tại sao nó lại chọn đi qua một con đường đang tắc nghẽn?).

**Kế hoạch thực thi cho bạn biết những gì?**
*   Thứ tự các bảng được truy cập.
*   Phương pháp truy cập dữ liệu cho mỗi bảng (`Seq Scan` hay `Index Scan`).
*   Loại `JOIN` được sử dụng (`Nested Loop`, `Hash Join`, `Merge Join`).
*   Ước tính số hàng sẽ được xử lý ở mỗi bước (`rows`).
*   Chi phí ước tính (`cost`) của mỗi thao tác.

**`EXPLAIN ANALYZE` là gì?**
`EXPLAIN ANALYZE` đi một bước xa hơn. Nó **thực sự chạy** câu truy vấn, sau đó hiển thị kế hoạch thực thi *kèm theo thời gian thực tế* và *số hàng thực tế* ở mỗi bước.

*   **Tương tự:** `EXPLAIN ANALYZE` giống như việc bạn lái xe theo lộ trình mà Google Maps đã vạch ra, đồng thời ghi lại thời gian thực tế bạn mất ở mỗi đoạn đường. Điều này cho phép bạn so sánh giữa "ước tính" và "thực tế", giúp phát hiện ra những điểm mà CSDL đã ước tính sai, thường là nơi ẩn chứa vấn đề về hiệu năng.

**Các thuật ngữ quan trọng trong kế hoạch thực thi:**

*   **`Sequential Scan` (hoặc `Seq Scan`, `Table Scan`):** Quét toàn bộ bảng từ đầu đến cuối. Đây là "kẻ thù" số một trên các bảng lớn.
*   **`Index Scan`:** Duyệt qua một phần của index để tìm các hàng thỏa mãn. Hiệu quả hơn `Seq Scan` rất nhiều.
*   **`Index Only Scan`:** Một dạng `Index Scan` siêu tối ưu. Tất cả các cột mà câu truy vấn yêu cầu đều có sẵn trong chính index đó, nên CSDL không cần phải vào bảng để lấy dữ liệu (tránh được "Key Lookup").
*   **`Nested Loop Join`:** Với mỗi hàng ở bảng ngoài (outer table), nó quét toàn bộ bảng trong (inner table) để tìm các hàng khớp. Rất hiệu quả nếu bảng ngoài nhỏ và có index tốt trên cột join của bảng trong.
*   **`Hash Join`:** Xây dựng một bảng băm (hash table) trong bộ nhớ cho bảng nhỏ hơn, sau đó quét bảng lớn hơn và tìm các hàng khớp trong bảng băm. Hiệu quả với các tập dữ liệu lớn khi không có index phù hợp.
*   **`Merge Join`:** Yêu cầu cả hai bảng đầu vào phải được sắp xếp theo cột join. Sau đó nó duyệt qua cả hai bảng cùng lúc để ghép nối. Rất hiệu quả nếu dữ liệu đã được sắp xếp sẵn (ví dụ: từ một `Index Scan`).

---

**Ví dụ thực hành**

Hãy tạo một bảng lớn để xem sự khác biệt.
```sql
CREATE TABLE employees (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100),
    department_id INT,
    salary INT
);

-- Chèn 1 triệu bản ghi (đây là code giả định, trong thực tế bạn sẽ dùng tool để generate)
INSERT INTO employees (name, department_id, salary)
SELECT 'Employee ' || i, (random() * 100)::INT, (random() * 50000 + 30000)::INT
FROM generate_series(1, 1000000) s(i);
```

**Kịch bản 1: Không có Index**
```sql
EXPLAIN ANALYZE
SELECT * FROM employees WHERE id = 500000;
```
**Kết quả (ví dụ):**
```
                                                 QUERY PLAN
-------------------------------------------------------------------------------------------------------------
 Seq Scan on employees  (cost=0.00..18334.00 rows=1 width=23) (actual time=25.123..49.215 rows=1 loops=1)
   Filter: (id = 500000)
   Rows Removed by Filter: 999999
 Planning Time: 0.075 ms
 Execution Time: 49.231 ms
```
*   **Phân tích:**
    *   **`Seq Scan`**: CSDL phải quét toàn bộ bảng.
    *   **`cost=0.00..18334.00`**: Chi phí ước tính là rất lớn.
    *   **`actual time=...49.215`**: Mất 49ms để thực thi.
    *   **`Rows Removed by Filter: 999999`**: Nó đã phải đọc 1 triệu hàng và loại bỏ 999,999 hàng.

**Kịch bản 2: Có Index (Khóa chính)**
Vì `id` là `PRIMARY KEY`, PostgreSQL tự động tạo một B-Tree index trên nó.
```sql
EXPLAIN ANALYZE
SELECT * FROM employees WHERE id = 500000;
```
**Kết quả (ví dụ):**
```
                                                              QUERY PLAN
---------------------------------------------------------------------------------------------------------------------------------------
 Index Scan using employees_pkey on employees  (cost=0.43..8.45 rows=1 width=23) (actual time=0.021..0.022 rows=1 loops=1)
   Index Cond: (id = 500000)
 Planning Time: 0.098 ms
 Execution Time: 0.038 ms
```
*   **Phân tích:**
    *   **`Index Scan`**: CSDL đã sử dụng index.
    *   **`cost=0.43..8.45`**: Chi phí ước tính cực kỳ thấp.
    *   **`actual time=...0.022`**: Thời gian thực thi chỉ còn **0.022ms**, nhanh hơn **hơn 2000 lần**!

**Kịch bản 3: Tối ưu với Index Only Scan**
Hãy tạo index trên `department_id` và `salary`.
```sql
CREATE INDEX idx_emp_dept_salary ON employees(department_id, salary);
```
Bây giờ, hãy chạy một truy vấn chỉ yêu cầu hai cột này.
```sql
EXPLAIN ANALYZE
SELECT department_id, salary FROM employees WHERE department_id = 50;
```
**Kết quả (ví dụ):**
```
                                                          QUERY PLAN
-------------------------------------------------------------------------------------------------------------------------------
 Index Only Scan using idx_emp_dept_salary on employees  (cost=0.43..355.85 rows=9989 width=8) (actual time=0.031..2.147 rows=10012 loops=1)
   Index Cond: (department_id = 50)
   Heap Fetches: 0
 Planning Time: 0.152 ms
 Execution Time: 2.388 ms
```
*   **Phân tích:**
    *   **`Index Only Scan`**: CSDL đã thực hiện quét chỉ trên index.
    *   **`Heap Fetches: 0`**: Dòng này cực kỳ quan trọng. Nó có nghĩa là CSDL **không cần** phải truy cập vào bảng chính ("heap") để lấy dữ liệu, vì tất cả thông tin cần thiết (`department_id`, `salary`) đều đã có trong index. Đây là loại truy vấn đọc nhanh nhất.

---

**Bài tập nhỏ**

Bạn có một câu lệnh sau đang chạy chậm:
`SELECT id, name FROM employees WHERE SUBSTRING(name, 1, 8) = 'Employee';`
Hãy dùng `EXPLAIN` để phỏng đoán tại sao nó chạy chậm và đề xuất cách sửa.

**Giải pháp**

1.  **Chạy `EXPLAIN`:** Kết quả gần như chắc chắn sẽ cho thấy một **`Seq Scan`**.
2.  **Phỏng đoán:** Nó chạy chậm vì CSDL không thể sử dụng index trên cột `name` (nếu có). Lý do là vì chúng ta đã áp dụng một hàm (`SUBSTRING`) lên cột đó. Việc này buộc CSDL phải tính toán giá trị của hàm cho từng hàng trong bảng trước khi so sánh.
3.  **Cách sửa:** Viết lại câu lệnh để tránh dùng hàm trên cột. Trong trường hợp này, có thể dùng `LIKE`:
    `SELECT id, name FROM employees WHERE name LIKE 'Employee%';`
    Câu lệnh `LIKE` với ký tự đại diện ở cuối có thể tận dụng được B-Tree index, chuyển `Seq Scan` thành `Index Scan` và tăng tốc độ đáng kể.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Bạn sẽ làm gì đầu tiên khi nhận được yêu cầu tối ưu một câu truy vấn SQL đang chạy chậm?"
    *   **Trả lời:** Bước đầu tiên của tôi luôn là chạy `EXPLAIN ANALYZE` trên câu truy vấn đó trong môi trường staging (hoặc production nếu cẩn thận) để lấy kế hoạch thực thi và thời gian thực tế. Kế hoạch này sẽ cho tôi biết CSDL đang làm gì "dưới mui xe". Tôi sẽ tìm kiếm các dấu hiệu bất thường như `Seq Scan` trên các bảng lớn, các phép `JOIN` không hiệu quả, hoặc sự chênh lệch lớn giữa số hàng ước tính và số hàng thực tế.

*   **Câu hỏi:** "Giải thích sự khác biệt giữa `Index Scan` và `Index Only Scan`."
    *   **Trả lời:** Cả hai đều sử dụng index để tìm dữ liệu. Tuy nhiên, với `Index Scan`, sau khi tìm thấy mục trong index, CSDL vẫn phải thực hiện một bước nữa là truy cập vào bảng chính để lấy các cột khác không nằm trong index. Với `Index Only Scan`, tất cả các cột mà câu `SELECT` yêu cầu đều đã có sẵn trong index, do đó CSDL không cần truy cập vào bảng chính nữa. `Index Only Scan` nhanh hơn vì nó tiết kiệm được một lượng lớn I/O.

*   **Câu hỏi:** "`cost` trong `EXPLAIN` có đơn vị là gì? Nó có phải là thời gian không?"
    *   **Trả lời:** Không, `cost` không phải là thời gian. Nó là một đơn vị đo lường trừu tượng, do CSDL tự định nghĩa, đại diện cho chi phí tổng hợp để thực hiện một thao tác (bao gồm cả I/O và CPU). Con số tuyệt đối của `cost` không quá quan trọng, điều quan trọng là so sánh `cost` giữa các kế hoạch thực thi khác nhau để chọn ra kế hoạch có `cost` thấp nhất.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Luôn đọc kế hoạch thực thi từ trong ra ngoài, từ trên xuống dưới (theo cấu trúc thụt lề). Các thao tác ở mức thụt lề sâu nhất sẽ được thực hiện trước.
*   **Thực tiễn tốt nhất:** Chú ý đến sự chênh lệch giữa `rows` (ước tính) và `actual rows` (thực tế) trong `EXPLAIN ANALYZE`. Nếu chênh lệch quá lớn, điều đó có nghĩa là thống kê của CSDL về bảng đó đã cũ hoặc không chính xác. Chạy lệnh `ANALYZE table_name;` để cập nhật lại thống kê có thể giúp CSDL tạo ra một kế hoạch thực thi tốt hơn.
*   **Sai lầm thường gặp:** Chỉ nhìn vào `EXPLAIN` mà không có `ANALYZE`. `EXPLAIN` chỉ cho bạn biết CSDL *nghĩ* nó sẽ làm gì, có thể không giống với thực tế. `ANALYZE` cung cấp các con số thực tế, là nguồn thông tin đáng tin cậy hơn nhiều.
*   **Sai lầm thường gặp:** Tin tưởng tuyệt đối vào `cost`. Đôi khi CSDL có thể chọn một kế hoạch có `cost` cao hơn một chút nhưng lại chạy nhanh hơn trong thực tế do các yếu tố như caching. Luôn ưu tiên nhìn vào `actual time` của `EXPLAIN ANALYZE`.

---

#### **4. Các Mẫu truy vấn chậm & Kỹ thuật Tối ưu hóa**

**Lý thuyết**

Sau khi đã biết cách dùng `EXPLAIN` để chẩn đoán, đây là một số "bệnh" thường gặp và cách "chữa trị" chúng.

| Mẫu truy vấn chậm (Anti-pattern)                                     | Tại sao chậm?                                                                                                       | Kỹ thuật Tối ưu hóa (Optimization Technique)                                                                                  |
| -------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| **1. `SELECT *` trong ứng dụng**                                     | Lấy về các cột không cần thiết, làm tăng I/O trên CSDL và lưu lượng mạng. Gây ra Key Lookup không cần thiết nếu dùng index. | Chỉ `SELECT` những cột bạn thực sự cần. Điều này tạo cơ hội cho `Index Only Scan`.                                             |
| **2. Áp dụng hàm lên cột trong `WHERE`**<br>(`WHERE YEAR(date) = 2024`) | Ngăn chặn CSDL sử dụng index trên cột đó vì nó phải tính toán giá trị hàm cho mỗi hàng.                               | Viết lại điều kiện để cột "trần trụi".<br>Ví dụ: `WHERE date >= '2024-01-01' AND date < '2025-01-01'`.                             |
| **3. `LIKE '%text'` (wildcard ở đầu)**                               | B-Tree index được sắp xếp từ trái sang phải. Ký tự đại diện ở đầu làm cho index trở nên vô dụng, dẫn đến `Seq Scan`.      | - Nếu có thể, hãy tránh wildcard ở đầu.<br>- Sử dụng **Full-Text Search** (PostgreSQL, SQL Server) cho các tìm kiếm văn bản phức tạp.<br>- Cân nhắc **Trigram Index** (PostgreSQL). |
| **4. `OR` trên nhiều cột khác nhau**<br>(`WHERE col_a = 1 OR col_b = 2`) | CSDL thường gặp khó khăn trong việc sử dụng nhiều index khác nhau cho một mệnh đề `OR`.                                  | Chuyển đổi `OR` thành `UNION ALL`. Mỗi `SELECT` trong `UNION` sẽ sử dụng một index riêng, thường hiệu quả hơn.<br>`SELECT ... WHERE col_a = 1 UNION ALL SELECT ... WHERE col_b = 2` |
| **5. Phân trang bằng `OFFSET` lớn**<br>(`LIMIT 10 OFFSET 100000`)      | CSDL vẫn phải lấy và sắp xếp 100,010 hàng rồi mới vứt đi 100,000 hàng đầu. Cực kỳ tốn kém.                             | Sử dụng **Keyset Pagination (Seek Method)**. Dựa vào giá trị của trang cuối để lấy trang tiếp theo.<br>`WHERE id > [last_id_from_previous_page] ORDER BY id LIMIT 10` |
| **6. N+1 Query**                                                     | Đây là vấn đề ở tầng ứng dụng (ORM), không phải CSDL. Lấy 1 danh sách, sau đó lặp qua danh sách và thực hiện 1 truy vấn cho mỗi mục. | - **Eager Loading:** Sử dụng `JOIN` để lấy tất cả dữ liệu cần thiết trong một truy vấn duy nhất.<br>- **Batching:** Lấy danh sách ID, sau đó thực hiện một truy vấn `WHERE id IN (...)` để lấy dữ liệu con. |

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Bạn sẽ tối ưu một truy vấn `LIKE '%search_term%'` như thế nào trên một bảng có hàng triệu bản ghi?"
    *   **Trả lời:** Cách tiếp cận tiêu chuẩn là sử dụng một hệ thống Full-Text Search được tích hợp sẵn trong CSDL như của PostgreSQL hoặc SQL Server. Nó sẽ tạo ra một loại index đặc biệt (ví dụ: GIN hoặc GiST trong PostgreSQL) được tối ưu cho việc tìm kiếm từ hoặc cụm từ bên trong một khối văn bản lớn. Nếu CSDL không hỗ trợ, tôi sẽ xem xét các giải pháp bên ngoài như Elasticsearch.

*   **Câu hỏi:** "Vấn đề 'N+1 Query' là gì và nó thường xảy ra ở đâu?"
    *   **Trả lời:** Vấn đề N+1 Query xảy ra ở tầng ứng dụng, đặc biệt là khi sử dụng các thư viện ORM. Nó xảy ra khi code thực hiện một truy vấn để lấy một danh sách các đối tượng (truy vấn "1"), sau đó lặp qua danh sách này và thực hiện một truy vấn khác cho mỗi đối tượng để lấy dữ liệu liên quan (N truy vấn). Tổng cộng là 1+N truy vấn thay vì chỉ 1 hoặc 2. Cách giải quyết là sử dụng các tính năng như "eager loading" hoặc "batch fetching" mà ORM cung cấp để nó có thể tạo ra các câu lệnh `JOIN` hoặc `IN (...)` hiệu quả hơn.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Tối ưu hóa không phải là việc làm một lần. Hãy thiết lập các công cụ giám sát (monitoring) để theo dõi các truy vấn chạy chậm nhất trong hệ thống của bạn và định kỳ xem xét lại chúng.
*   **Thực tiễn tốt nhất:** Hiểu rõ dữ liệu của bạn. Việc tối ưu hóa phụ thuộc rất nhiều vào đặc điểm của dữ liệu như phân bố (distribution) và cardinality.
*   **Sai lầm thường gặp:** **Tối ưu hóa sớm (Premature Optimization).** Dành quá nhiều thời gian để tối ưu những truy vấn không quan trọng hoặc không phải là nút thắt cổ chai. Hãy tập trung vào những gì thực sự làm hệ thống của bạn chậm đi.
*   **Sai lầm thường gặp:** Đổ lỗi cho CSDL. Thường thì, các vấn đề về hiệu năng đến từ các truy vấn được viết không tốt, thiết kế schema tồi, hoặc thiếu index phù hợp, chứ không phải do bản thân CSDL chậm.