#### **1. Database, DBMS, và Mô hình Quan hệ**

**Lý thuyết**

*   **Database (Cơ sở dữ liệu - CSDL):** Hãy tưởng tượng CSDL như một tủ hồ sơ kỹ thuật số khổng lồ, được tổ chức một cách khoa học để lưu trữ thông tin. Thay vì giấy tờ, nó chứa dữ liệu. Ví dụ, CSDL của một trang thương mại điện tử chứa thông tin về người dùng, sản phẩm, đơn hàng, v.v. Mục đích chính là để lưu trữ, truy xuất và quản lý dữ liệu một cách hiệu quả.

*   **DBMS (Database Management System - Hệ quản trị CSDL):** Nếu CSDL là tủ hồ sơ, thì DBMS chính là người thủ thư quản lý chiếc tủ đó. DBMS là phần mềm cho phép chúng ta tương tác với CSDL: thêm dữ liệu mới, đọc, cập nhật hoặc xóa dữ liệu. Các DBMS phổ biến bao gồm: MySQL, PostgreSQL, SQL Server, Oracle. Chúng ta ra lệnh cho người thủ thư (DBMS) bằng một ngôn ngữ gọi là SQL.

*   **Mô hình Quan hệ (Relational Model):** Đây là cách tổ chức dữ liệu phổ biến nhất. Trong mô hình này, dữ liệu được sắp xếp vào các bảng (tables), giống như các trang tính trong Excel.

    *   **Table (Bảng):** Đại diện cho một loại đối tượng cụ thể. Ví dụ: một bảng `SINHVIEN`, một bảng `SANPHAM`. Mỗi bảng chứa dữ liệu về một chủ thể duy nhất.
    *   **Column (Cột, hay Trường - Field):** Đại diện cho một thuộc tính của đối tượng. Trong bảng `SINHVIEN`, các cột có thể là `ma_sinh_vien`, `ho_ten`, `ngay_sinh`. Mỗi cột có một kiểu dữ liệu xác định (ví dụ: `ho_ten` là văn bản, `ngay_sinh` là kiểu ngày tháng).
    *   **Row (Hàng, hay Bản ghi - Record):** Đại diện cho một đối tượng cụ thể. Mỗi hàng trong bảng `SINHVIEN` là thông tin của một sinh viên duy nhất.

**Ví dụ trực quan**

Hãy xem xét bảng `Users` (Người dùng):

| id  | user_name | email                  | creation_date |
| --- | --------- | ---------------------- | ------------- |
| 1   | an_nguyen | an.nguyen@example.com  | 2024-10-25    |
| 2   | binh_le   | binh.le@example.com    | 2024-10-26    |
| 3   | chi_tran  | chi.tran@example.com   | 2024-10-27    |

*   **Bảng:** `Users`
*   **Cột:** `id`, `user_name`, `email`, `creation_date`
*   **Hàng:** Hàng có `id` = 1 là một bản ghi, chứa toàn bộ thông tin về người dùng "an_nguyen".

---

**Bài tập nhỏ**

Bạn đang thiết kế CSDL cho một blog đơn giản. Hãy xác định:
1.  Các **bảng** chính có thể có là gì?
2.  Với mỗi bảng, hãy liệt kê một vài **cột** khả dĩ.

**Giải pháp**

1.  Các bảng chính có thể là: `authors` (để lưu thông tin tác giả) và `posts` (để lưu các bài viết).
2.  Bảng `authors`: `id`, `author_name`, `email`.
    Bảng `posts`: `id`, `title`, `content`, `publication_date`, `author_id` (để liên kết với bảng tác giả).

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt giữa Database và DBMS là gì?"
    *   **Trả lời:** Database là nơi chứa dữ liệu, giống như một nhà kho. DBMS là hệ thống phần mềm quản lý nhà kho đó, cho phép chúng ta đưa hàng vào, lấy hàng ra, và sắp xếp hàng hóa. Chúng ta không trực tiếp làm việc với Database mà thông qua DBMS.

*   **Câu hỏi:** "Giải thích các khái niệm: Bảng, Cột, và Hàng."
    *   **Trả lời:** Trong mô hình CSDL quan hệ, dữ liệu được tổ chức trong các Bảng. Mỗi Bảng đại diện cho một loại đối tượng (ví dụ: Sản phẩm). Mỗi Cột là một thuộc tính của đối tượng đó (ví dụ: Tên sản phẩm, Giá). Mỗi Hàng là một thực thể cụ thể của đối tượng đó (ví dụ: một chiếc iPhone 15 Pro Max).

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Luôn đặt tên bảng và cột một cách nhất quán. Quy tắc phổ biến là:
    *   Tên bảng dùng danh từ số nhiều (ví dụ: `users`, `products`).
    *   Tên cột dùng `snake_case` (ví dụ: `user_name`, `creation_date`).
    *   Điều này giúp code SQL dễ đọc và dễ bảo trì hơn.

*   **Sai lầm thường gặp:** Dùng dấu cách hoặc ký tự đặc biệt trong tên bảng/cột. Điều này sẽ buộc bạn phải luôn đặt tên trong dấu ngoặc kép hoặc dấu nháy ngược, gây ra sự bất tiện và dễ lỗi. Ví dụ, thay vì `Tên Người Dùng`, hãy dùng `ten_nguoi_dung` hoặc `user_name`.

---

#### **2. Các loại câu lệnh SQL: DDL, DML, DQL, DCL, TCL**

**Lý thuyết**

SQL có 5 nhóm lệnh chính, mỗi nhóm có một mục đích riêng:

1.  **DQL (Data Query Language - Ngôn ngữ Truy vấn Dữ liệu):** Dùng để "hỏi" và lấy dữ liệu ra khỏi CSDL. Đây là nhóm lệnh bạn sẽ dùng nhiều nhất.
    *   Lệnh chính: `SELECT`.
    *   Tương tự như: "Này người thủ thư, cho tôi xem danh sách tất cả các cuốn sách."

2.  **DML (Data Manipulation Language - Ngôn ngữ Thao tác Dữ liệu):** Dùng để thay đổi dữ liệu *bên trong* các bảng.
    *   Các lệnh: `INSERT` (thêm mới), `UPDATE` (cập nhật), `DELETE` (xóa).
    *   Tương tự như: Thêm một cuốn sách mới vào kệ, sửa tên một cuốn sách, hoặc vứt một cuốn sách đi.

3.  **DDL (Data Definition Language - Ngôn ngữ Định nghĩa Dữ liệu):** Dùng để định nghĩa và quản lý *cấu trúc* của CSDL.
    *   Các lệnh: `CREATE` (tạo bảng, CSDL), `ALTER` (sửa đổi cấu trúc bảng), `DROP` (xóa bảng, CSDL), `TRUNCATE` (xóa toàn bộ dữ liệu trong bảng).
    *   Tương tự như: Xây dựng các kệ sách mới, thay đổi kích thước của kệ, hoặc phá bỏ hoàn toàn một dãy kệ.

4.  **DCL (Data Control Language - Ngôn ngữ Điều khiển Dữ liệu):** Dùng để quản lý quyền truy cập của người dùng.
    *   Các lệnh: `GRANT` (cấp quyền), `REVOKE` (thu hồi quyền).
    *   Tương tự như: Cấp cho ai đó thẻ thư viện và quy định họ được đọc sách ở khu vực nào.

5.  **TCL (Transaction Control Language - Ngôn ngữ Điều khiển Giao dịch):** Dùng để quản lý các "giao dịch" (một nhóm các thao tác).
    *   Các lệnh: `COMMIT` (lưu vĩnh viễn các thay đổi), `ROLLBACK` (hủy bỏ các thay đổi), `SAVEPOINT` (tạo điểm lưu tạm).
    *   Tương tự như: Khi bạn mua nhiều món hàng, bạn gom chúng lại. `COMMIT` giống như thanh toán thành công cho tất cả món hàng. `ROLLBACK` giống như hủy toàn bộ giỏ hàng nếu thẻ tín dụng của bạn bị lỗi.

---

**Bài tập nhỏ**

Hãy phân loại các câu lệnh SQL sau vào các nhóm DDL, DML, DQL, DCL:
1.  `UPDATE products SET price = 500;`
2.  `ALTER TABLE users ADD COLUMN age INT;`
3.  `GRANT SELECT ON customers TO new_employee;`
4.  `SELECT first_name, last_name FROM employees;`

**Giải pháp**

1.  `UPDATE`: DML (Thao tác dữ liệu)
2.  `ALTER TABLE`: DDL (Định nghĩa dữ liệu)
3.  `GRANT`: DCL (Điều khiển dữ liệu)
4.  `SELECT`: DQL (Truy vấn dữ liệu)

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt giữa `DELETE`, `TRUNCATE`, và `DROP` là gì?"
    *   **Trả lời:** Đây là một câu hỏi kinh điển.
        *   `DELETE FROM table_name;`: Là một lệnh DML. Nó xóa các hàng ra khỏi bảng, có thể xóa một vài hàng hoặc tất cả (nếu không có `WHERE`). Nó ghi lại từng lần xóa vào nhật ký giao dịch (transaction log), nên chạy chậm và có thể `ROLLBACK` (hoàn tác) được.
        *   `TRUNCATE TABLE table_name;`: Là một lệnh DDL. Nó xóa *tất cả* dữ liệu trong bảng một cách nhanh chóng bằng cách giải phóng các trang dữ liệu (data pages). Nó không ghi lại từng hàng bị xóa, nên chạy rất nhanh và thường không thể `ROLLBACK`. Nó cũng reset lại các giá trị tự tăng (auto-increment).
        *   `DROP TABLE table_name;`: Là một lệnh DDL. Nó xóa toàn bộ bảng, bao gồm cả cấu trúc và dữ liệu. Bảng đó sẽ không còn tồn tại.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Cẩn trọng tuyệt đối khi dùng các lệnh DDL (`ALTER`, `DROP`) trên CSDL sản phẩm (production) vì chúng có thể gây mất dữ liệu hoặc khóa bảng trong thời gian dài. Luôn sao lưu (backup) trước khi thực hiện các thay đổi lớn.
*   **Sai lầm thường gặp:** Dùng `DELETE` không có `WHERE` để xóa toàn bộ dữ liệu thay vì `TRUNCATE`. `TRUNCATE` hiệu quả hơn rất nhiều cho mục đích này.

---

#### **3. Thao tác CRUD: `SELECT`, `INSERT`, `UPDATE`, `DELETE`**

CRUD là viết tắt của 4 hoạt động cơ bản trong quản lý dữ liệu: **C**reate (Tạo), **R**ead (Đọc), **U**pdate (Cập nhật), **D**elete (Xóa). Trong SQL, chúng tương ứng với các lệnh sau:

*   **C**reate -> `INSERT`
*   **R**ead -> `SELECT`
*   **U**pdate -> `UPDATE`
*   **D**elete -> `DELETE`

Chúng ta sẽ thực hành trên một bảng `products` có cấu trúc như sau:
`id` (số, tự động tăng), `name` (văn bản), `price` (số thập phân), `quantity` (số nguyên).

```sql
-- DDL: Lệnh tạo bảng để thực hành
CREATE TABLE products (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(255) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    quantity INT DEFAULT 0
);
```

**Ví dụ**

1.  **`INSERT` (Tạo mới dữ liệu)**

    ```sql
    -- Chèn một sản phẩm mới
    INSERT INTO products (name, price, quantity)
    VALUES ('Laptop Dell XPS 15', 38500000.00, 50);

    -- Chèn nhiều sản phẩm cùng lúc
    INSERT INTO products (name, price, quantity)
    VALUES
        ('Bàn phím cơ Keychron K2', 2150000.00, 120),
        ('Chuột Logitech MX Master 3S', 2490000.00, 200);
    ```
    *   **Giải thích:** Chúng ta chỉ định các cột cần chèn dữ liệu vào, sau đó cung cấp các giá trị tương ứng trong mệnh đề `VALUES`. Thứ tự các giá trị phải khớp với thứ tự các cột đã liệt kê.

2.  **`SELECT` (Đọc dữ liệu)**

    ```sql
    -- Lấy tất cả thông tin của tất cả sản phẩm
    SELECT * FROM products;

    -- Chỉ lấy tên và giá của sản phẩm
    SELECT name, price FROM products;
    ```
    *   **Giải thích:** `SELECT *` là cách lấy tất cả các cột. Để tối ưu hiệu suất, ta chỉ nên lấy những cột thực sự cần thiết.

3.  **`UPDATE` (Cập nhật dữ liệu)**

    ```sql
    -- Cập nhật giá cho sản phẩm có id = 1
    UPDATE products
    SET price = 39990000.00
    WHERE id = 1;

    -- Giảm 10% giá cho tất cả bàn phím
    UPDATE products
    SET price = price * 0.9
    WHERE name LIKE 'Bàn phím%';
    ```
    *   **Giải thích:** Mệnh đề `SET` chỉ định cột nào cần cập nhật và giá trị mới của nó. **Cực kỳ quan trọng:** Mệnh đề `WHERE` dùng để lọc ra những hàng cần được cập nhật. **Nếu không có `WHERE`, tất cả các hàng trong bảng sẽ bị cập nhật!**

4.  **`DELETE` (Xóa dữ liệu)**

    ```sql
    -- Xóa sản phẩm có id = 3
    DELETE FROM products
    WHERE id = 3;
    ```
    *   **Giải thích:** Tương tự như `UPDATE`, `WHERE` là mệnh đề tối quan trọng để xác định hàng nào cần xóa. **Không có `WHERE` sẽ xóa sạch dữ liệu của cả bảng!**

---

**Bài tập nhỏ**

Cho bảng `products` ở trên, hãy viết các câu lệnh SQL để:
1.  Thêm một sản phẩm mới: 'Màn hình LG UltraGear', giá 8500000, số lượng 75.
2.  Tăng số lượng của 'Laptop Dell XPS 15' lên 55.
3.  Đọc tên và số lượng của tất cả các sản phẩm có giá dưới 3,000,000.

**Giải pháp**

1.  `INSERT INTO products (name, price, quantity) VALUES ('Màn hình LG UltraGear', 8500000, 75);`
2.  `UPDATE products SET quantity = 55 WHERE name = 'Laptop Dell XPS 15';`
3.  `SELECT name, quantity FROM products WHERE price < 3000000;`

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Hậu quả của việc chạy lệnh `UPDATE` hoặc `DELETE` mà không có mệnh đề `WHERE` là gì?"
    *   **Trả lời:** Nếu không có `WHERE`, lệnh sẽ được áp dụng cho tất cả các hàng trong bảng. `UPDATE` sẽ cập nhật toàn bộ bảng với cùng một giá trị, và `DELETE` sẽ xóa sạch toàn bộ dữ liệu của bảng. Đây là một trong những sai lầm nguy hiểm nhất khi làm việc với SQL.

*   **Câu hỏi:** "Trong lệnh `INSERT`, nếu tôi không chỉ định danh sách cột, điều gì sẽ xảy ra?"
    *   **Trả lời:** Nếu không chỉ định danh sách cột (ví dụ: `INSERT INTO products VALUES (...)`), bạn phải cung cấp giá trị cho *tất cả* các cột của bảng và theo đúng thứ tự mà chúng được định nghĩa trong cấu trúc bảng. Cách viết này không được khuyến khích vì nó rất dễ vỡ (brittle). Nếu sau này có ai đó thêm một cột mới vào bảng, câu lệnh `INSERT` đó sẽ bị lỗi ngay lập tức.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Trước khi chạy một lệnh `UPDATE` hoặc `DELETE` quan trọng, hãy viết một lệnh `SELECT` với cùng điều kiện `WHERE` trước. Ví dụ, trước khi chạy `DELETE FROM products WHERE price > 40000000;`, hãy chạy `SELECT * FROM products WHERE price > 40000000;` để xem chính xác những hàng nào sắp bị ảnh hưởng.
*   **Sai lầm thường gặp:**
    *   Quên `WHERE` trong `UPDATE`/`DELETE`.
    *   Sai thứ tự giá trị trong `INSERT` khi không liệt kê tên cột.
    *   Dùng `SELECT *` trong code ứng dụng. Việc này làm tăng tải cho CSDL và mạng, đồng thời có thể gây lỗi nếu cấu trúc bảng thay đổi. Hãy luôn chỉ định rõ các cột bạn cần.

#### **4. Lọc (WHERE), Sắp xếp (ORDER BY), và Phân trang (LIMIT / OFFSET)**

**Lý thuyết**

Sau khi đã biết cách lấy dữ liệu bằng `SELECT`, bước tiếp theo là kiểm soát *dữ liệu nào* được lấy, *theo thứ tự nào*, và *với số lượng bao nhiêu*.

*   **`WHERE` (Lọc):** Mệnh đề `WHERE` hoạt động như một bộ lọc. Bạn đặt ra các điều kiện, và chỉ những hàng nào thỏa mãn tất cả các điều kiện đó mới được trả về.
    *   **Tương tự:** Khi bạn vào thư viện, thay vì yêu cầu "lấy tất cả sách", bạn nói "lấy những cuốn sách của tác giả X, xuất bản sau năm 2020". `tác giả = X` và `năm_xuất_bản > 2020` chính là các điều kiện trong mệnh đề `WHERE`.
    *   Các toán tử phổ biến: `=`, `!=` (hoặc `<>`), `>`, `<`, `>=`, `<=`, `AND`, `OR`, `NOT`.

*   **`ORDER BY` (Sắp xếp):** Dùng để sắp xếp kết quả trả về theo một hoặc nhiều cột.
    *   **Tương tự:** Sau khi thủ thư tìm được sách cho bạn, bạn yêu cầu họ "xếp những cuốn sách này lên bàn theo thứ tự tên sách từ A-Z".
    *   `ASC` (Ascending): Sắp xếp tăng dần (mặc định, không cần ghi rõ).
    *   `DESC` (Descending): Sắp xếp giảm dần.

*   **`LIMIT` / `OFFSET` (Phân trang):** Dùng để giới hạn số lượng kết quả và lấy ra các "trang" dữ liệu. Đây là kỹ thuật cực kỳ quan trọng trong các ứng dụng web để không phải tải hàng ngàn bản ghi cùng lúc.
    *   `LIMIT N`: Chỉ lấy `N` hàng đầu tiên.
    *   `OFFSET M`: Bỏ qua `M` hàng đầu tiên rồi mới bắt đầu lấy.
    *   **Tương tự:** Bạn yêu cầu thủ thư: "Trong số sách đã sắp xếp, chỉ đưa tôi xem 5 cuốn đầu tiên" (`LIMIT 5`). Lần sau bạn quay lại và nói: "Bỏ qua 5 cuốn đầu tôi đã xem, đưa tôi 5 cuốn tiếp theo" (`LIMIT 5 OFFSET 5`).

**Ví dụ SQL**

Chúng ta tiếp tục với bảng `products`. Giả sử bảng đang có dữ liệu sau:

| id  | name                       | price      | quantity |
| --- | -------------------------- | ---------- | -------- |
| 1   | Laptop Dell XPS 15         | 39990000   | 55       |
| 2   | Bàn phím cơ Keychron K2    | 2150000    | 120      |
| 4   | Màn hình LG UltraGear      | 8500000    | 75       |
| 5   | Chuột không dây Logitech   | 750000     | 300      |
| 6   | Ổ cứng SSD Samsung 1TB     | 2800000    | 150      |
| 7   | Laptop Macbook Pro M3      | 52500000   | 30       |

1.  **Lọc với `WHERE`**

    ```sql
    -- Lấy các sản phẩm có giá trên 10,000,000
    SELECT name, price FROM products WHERE price > 10000000;
    ```
    **Kết quả:**
    ```
    name                  | price
    ----------------------+-----------
    Laptop Dell XPS 15    | 39990000
    Laptop Macbook Pro M3 | 52500000
    ```

    ```sql
    -- Lấy các sản phẩm là 'Laptop' VÀ có số lượng dưới 50
    SELECT name, quantity FROM products WHERE name LIKE 'Laptop%' AND quantity < 50;
    ```
    **Kết quả:**
    ```
    name                  | quantity
    ----------------------+----------
    Laptop Macbook Pro M3 | 30
    ```

2.  **Sắp xếp với `ORDER BY`**

    ```sql
    -- Lấy tất cả sản phẩm, sắp xếp theo giá giảm dần
    SELECT name, price FROM products ORDER BY price DESC;
    ```
    **Kết quả:**
    ```
    name                       | price
    ---------------------------+-----------
    Laptop Macbook Pro M3      | 52500000
    Laptop Dell XPS 15         | 39990000
    Màn hình LG UltraGear      | 8500000
    Ổ cứng SSD Samsung 1TB     | 2800000
    Bàn phím cơ Keychron K2    | 2150000
    Chuột không dây Logitech   | 750000
    ```

3.  **Phân trang với `LIMIT` và `OFFSET`**

    ```sql
    -- Lấy 3 sản phẩm có giá cao nhất
    SELECT name, price
    FROM products
    ORDER BY price DESC
    LIMIT 3;
    ```
    **Kết quả:**
    ```
    name                       | price
    ---------------------------+-----------
    Laptop Macbook Pro M3      | 52500000
    Laptop Dell XPS 15         | 39990000
    Màn hình LG UltraGear      | 8500000
    ```

    ```sql
    -- Lấy trang thứ 2 của danh sách sản phẩm, mỗi trang có 2 sản phẩm, sắp xếp theo tên A-Z
    -- Trang 1: LIMIT 2 OFFSET 0
    -- Trang 2: LIMIT 2 OFFSET 2
    SELECT id, name
    FROM products
    ORDER BY name ASC
    LIMIT 2 OFFSET 2;
    ```
    **Kết quả:** (Sau khi sắp xếp theo tên, 2 sản phẩm đầu là Bàn phím và Chuột)
    ```
    id  | name
    ----+-----------------------
    1   | Laptop Dell XPS 15
    7   | Laptop Macbook Pro M3
    ```

---

**Bài tập nhỏ**

Sử dụng bảng `products` ở trên, hãy viết một câu lệnh SQL duy nhất để:
*   Tìm 2 sản phẩm có số lượng tồn kho (`quantity`) nhiều nhất.
*   Chỉ xét những sản phẩm có giá dưới 3,000,000.
*   Hiển thị tên, giá và số lượng của chúng.

**Giải pháp**

```sql
SELECT name, price, quantity
FROM products
WHERE price < 3000000
ORDER BY quantity DESC
LIMIT 2;
```
*   **Phân tích:**
    1.  `FROM products`: Xác định làm việc trên bảng `products`.
    2.  `WHERE price < 3000000`: Lọc ra chỉ những sản phẩm thỏa mãn điều kiện giá.
    3.  `ORDER BY quantity DESC`: Sắp xếp các sản phẩm đã lọc theo số lượng giảm dần.
    4.  `LIMIT 2`: Lấy 2 hàng đầu tiên từ kết quả đã sắp xếp.
    5.  `SELECT name, price, quantity`: Chọn các cột để hiển thị.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Thứ tự thực thi logic của một câu lệnh `SELECT` chứa `WHERE`, `ORDER BY`, `LIMIT` là gì?"
    *   **Trả lời:** Mặc dù chúng ta viết `SELECT` đầu tiên, nhưng CSDL không thực thi theo thứ tự đó. Thứ tự logic là:
        1.  `FROM`: Xác định bảng nguồn.
        2.  `WHERE`: Lọc các hàng.
        3.  `GROUP BY`: (Sẽ học sau) Gom nhóm các hàng.
        4.  `HAVING`: (Sẽ học sau) Lọc các nhóm.
        5.  `SELECT`: Chọn ra các cột (hoặc tính toán giá trị mới).
        6.  `ORDER BY`: Sắp xếp kết quả cuối cùng.
        7.  `LIMIT` / `OFFSET`: Lấy một phần của kết quả đã sắp xếp.
    *   Hiểu điều này rất quan trọng để tối ưu hóa câu truy vấn. Ví dụ, bạn biết rằng `WHERE` được áp dụng trước `ORDER BY`, nên việc lọc hiệu quả sẽ giúp giảm số lượng hàng cần phải sắp xếp, làm tăng tốc độ truy vấn.

*   **Câu hỏi:** "Tại sao việc phân trang bằng `OFFSET` có thể trở nên rất chậm với các trang ở xa (ví dụ: trang 1000)?"
    *   **Trả lời:** Khi bạn dùng `LIMIT 10 OFFSET 10000`, CSDL vẫn phải tìm và sắp xếp tất cả 10010 hàng đầu tiên, sau đó vứt bỏ 10000 hàng đầu và chỉ trả về 10 hàng cuối. Với `OFFSET` lớn, việc "vứt bỏ" này rất tốn tài nguyên. Một giải pháp thay thế hiệu quả hơn là "keyset pagination" (hay "cursor-based pagination"), tức là dựa vào giá trị của trang cuối cùng để lấy trang tiếp theo. Ví dụ: `WHERE id > [id_cuối_cùng_của_trang_trước] ORDER BY id ASC LIMIT 10`.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Luôn luôn sử dụng `ORDER BY` khi dùng `LIMIT` / `OFFSET`. Nếu không có `ORDER BY`, thứ tự các hàng không được đảm bảo. CSDL có thể trả về các kết quả khác nhau cho cùng một câu lệnh trong những lần chạy khác nhau, khiến việc phân trang của bạn trở nên vô nghĩa và không nhất quán.
*   **Thực tiễn tốt nhất:** Sử dụng ngoặc đơn `()` khi kết hợp `AND` và `OR` trong mệnh đề `WHERE` để đảm bảo logic được thực thi đúng theo ý muốn, tránh sự mơ hồ. Ví dụ: `WHERE (category = 'Laptop' AND price > 30000000) OR (quantity = 0)`.
*   **Sai lầm thường gặp:** Tính toán sai giá trị `OFFSET`. Công thức chung cho trang `p` (bắt đầu từ 1) với `n` mục mỗi trang là: `OFFSET (p - 1) * n`. Ví dụ, để lấy trang 3 với 20 mục/trang, `OFFSET` là `(3 - 1) * 20 = 40`.
*   **Sai lầm thường gặp:** Áp dụng hàm hoặc phép toán lên cột trong mệnh đề `WHERE` (ví dụ: `WHERE YEAR(order_date) = 2024`). Điều này thường ngăn CSDL sử dụng index trên cột đó, làm chậm truy vấn. Cách tốt hơn là: `WHERE order_date >= '2024-01-01' AND order_date < '2025-01-01'`.

#### **5. Các hàm và toán tử thường dùng: `COUNT`, `SUM`, `AVG`, `MIN`, `MAX`, `LIKE`, `BETWEEN`, `IN`, `DISTINCT`**

**Lý thuyết**

Phần này giới thiệu các công cụ giúp bạn xử lý và tóm tắt dữ liệu, cũng như các cách lọc dữ liệu mạnh mẽ hơn. Chúng ta có thể chia chúng thành hai nhóm:

**1. Các hàm tổng hợp (Aggregate Functions):**
Đây là những hàm hoạt động trên một tập hợp các hàng và trả về một giá trị duy nhất.
*   `COUNT()`: Đếm số lượng hàng.
*   `SUM()`: Tính tổng các giá trị trong một cột (chỉ dùng cho cột số).
*   `AVG()`: Tính giá trị trung bình (chỉ dùng cho cột số).
*   `MIN()`: Tìm giá trị nhỏ nhất.
*   `MAX()`: Tìm giá trị lớn nhất.

**Tương tự:** Hãy tưởng tượng bạn là giáo viên xem sổ điểm của cả lớp. Các hàm tổng hợp giống như việc bạn tính:
*   Sĩ số lớp là bao nhiêu? (`COUNT`)
*   Tổng số điểm của cả lớp là bao nhiêu? (`SUM`)
*   Điểm trung bình của cả lớp là bao nhiêu? (`AVG`)
*   Ai có điểm thấp nhất? (`MIN`)
*   Ai có điểm cao nhất? (`MAX`)

**2. Các toán tử và từ khóa lọc nâng cao:**
Đây là các công cụ dùng trong mệnh đề `WHERE` để tạo ra các điều kiện lọc phức tạp hơn.
*   `LIKE`: So khớp một chuỗi ký tự với một mẫu (pattern). Rất hữu ích khi tìm kiếm văn bản.
*   `BETWEEN`: Kiểm tra xem một giá trị có nằm trong một khoảng cho trước hay không (bao gồm cả hai giá trị biên).
*   `IN`: Kiểm tra xem một giá trị có khớp với bất kỳ giá trị nào trong một danh sách hay không.
*   `DISTINCT`: Loại bỏ các giá trị trùng lặp khỏi kết quả trả về.

---

**Ví dụ SQL**

Hãy tạo một bảng `orders` (đơn hàng) để thực hành:
```sql
CREATE TABLE orders (
    order_id INT PRIMARY KEY,
    customer_name VARCHAR(100),
    product_category VARCHAR(50),
    total_amount DECIMAL(12, 2),
    order_date DATE
);

INSERT INTO orders VALUES
(1, 'Nguyễn Văn A', 'Điện tử', 1500000.00, '2024-10-01'),
(2, 'Trần Thị B', 'Thời trang', 850000.00, '2024-10-03'),
(3, 'Lê Văn C', 'Điện tử', 3200000.00, '2024-10-05'),
(4, 'Phạm Thị D', 'Gia dụng', 550000.00, '2024-10-05'),
(5, 'Nguyễn Văn A', 'Gia dụng', 1200000.00, '2024-10-08'),
(6, 'Hoàng Văn E', 'Điện tử', 7800000.00, '2024-10-12');
```

**Ví dụ về Hàm tổng hợp:**

```sql
-- Có tổng cộng bao nhiêu đơn hàng?
SELECT COUNT(*) FROM orders;
-- Kết quả: 6

-- Tổng doanh thu từ tất cả các đơn hàng là bao nhiêu?
SELECT SUM(total_amount) FROM orders;
-- Kết quả: 14900000.00

-- Doanh thu trung bình của một đơn hàng là bao nhiêu?
SELECT AVG(total_amount) FROM orders;
-- Kết quả: 2483333.33

-- Đơn hàng có giá trị cao nhất và thấp nhất là bao nhiêu?
SELECT MAX(total_amount), MIN(total_amount) FROM orders;
-- Kết quả: 7800000.00, 550000.00
```

**Ví dụ về Toán tử lọc và `DISTINCT`:**

```sql
-- Tìm tất cả các khách hàng có họ 'Nguyễn'
SELECT DISTINCT customer_name FROM orders WHERE customer_name LIKE 'Nguyễn%';
```
*   **Giải thích:** `LIKE 'Nguyễn%'` tìm tất cả các chuỗi bắt đầu bằng 'Nguyễn'. Dấu `%` đại diện cho 0 hoặc nhiều ký tự bất kỳ. `DISTINCT` đảm bảo rằng 'Nguyễn Văn A' chỉ xuất hiện một lần dù ông có 2 đơn hàng.
*   **Kết quả:**
    ```
    customer_name
    -------------
    Nguyễn Văn A
    ```

```sql
-- Tìm các đơn hàng được đặt trong khoảng từ 01/10/2024 đến 05/10/2024
SELECT order_id, total_amount, order_date
FROM orders
WHERE order_date BETWEEN '2024-10-01' AND '2024-10-05';
```
*   **Giải thích:** `BETWEEN` bao gồm cả ngày bắt đầu và ngày kết thúc.
*   **Kết quả:** Sẽ trả về các đơn hàng có `order_id` là 1, 2, 3, 4.

```sql
-- Tìm các đơn hàng thuộc danh mục 'Điện tử' hoặc 'Gia dụng'
SELECT order_id, product_category
FROM orders
WHERE product_category IN ('Điện tử', 'Gia dụng');
```
*   **Giải thích:** `IN` là cách viết ngắn gọn và hiệu quả hơn so với `WHERE product_category = 'Điện tử' OR product_category = 'Gia dụng'`.
*   **Kết quả:** Sẽ trả về các đơn hàng có `order_id` là 1, 3, 4, 5, 6.

---

**Bài tập nhỏ**

Sử dụng bảng `orders` ở trên, hãy viết một câu lệnh SQL để:
*   Tính tổng doanh thu (`total_amount`) của riêng các đơn hàng thuộc danh mục 'Điện tử' được đặt trong tháng 10 năm 2024.

**Giải pháp**

```sql
SELECT SUM(total_amount)
FROM orders
WHERE
    product_category = 'Điện tử'
    AND order_date BETWEEN '2024-10-01' AND '2024-10-31';
```
*   **Phân tích:**
    1.  `FROM orders`: Làm việc trên bảng `orders`.
    2.  `WHERE product_category = 'Điện tử'`: Lọc chỉ những đơn hàng 'Điện tử'.
    3.  `AND order_date BETWEEN ...`: Lọc tiếp những đơn hàng trong tháng 10.
    4.  `SELECT SUM(total_amount)`: Tính tổng `total_amount` của những hàng đã được lọc.
*   **Kết quả:** `1500000 + 3200000 + 7800000 = 12500000.00`

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt giữa `COUNT(*)` và `COUNT(column_name)` là gì?"
    *   **Trả lời:** `COUNT(*)` đếm tất cả các hàng trong kết quả, bất kể giá trị các cột. `COUNT(column_name)` chỉ đếm các hàng mà cột `column_name` có giá trị khác `NULL`. Nếu một cột có thể có giá trị `NULL`, hai cách đếm này có thể cho ra kết quả khác nhau.

*   **Câu hỏi:** "Khi nào bạn sẽ dùng `IN` thay vì nhiều điều kiện `OR`?"
    *   **Trả lời:** Tôi sẽ luôn ưu tiên dùng `IN` khi kiểm tra một cột với một danh sách các giá trị tĩnh. `IN` làm cho câu lệnh SQL ngắn gọn, dễ đọc hơn rất nhiều. Về mặt hiệu năng, hầu hết các hệ quản trị CSDL hiện đại đều tối ưu hóa `IN` rất tốt, thường là tương đương hoặc tốt hơn nhiều điều kiện `OR`.

*   **Câu hỏi:** "Làm thế nào để tìm tất cả sản phẩm có tên chứa từ 'SSD'?"
    *   **Trả lời:** Dùng toán tử `LIKE` với dấu `%` ở cả hai bên: `WHERE product_name LIKE '%SSD%'`. Dấu `%` ở đầu cho phép có ký tự bất kỳ trước 'SSD', và dấu `%` ở cuối cho phép có ký tự bất kỳ sau 'SSD'.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Sử dụng `IN` thay vì nhiều `OR` để tăng tính dễ đọc.
*   **Thực tiễn tốt nhất:** `COUNT(1)` thường được cho là tương đương `COUNT(*)` về hiệu năng. Cả hai đều có ý nghĩa là "đếm số hàng". Dùng `COUNT(*)` thường rõ ràng hơn về mặt ngữ nghĩa.
*   **Sai lầm thường gặp:** Sử dụng `LIKE` với ký tự đại diện (`%` hoặc `_`) ở đầu chuỗi tìm kiếm (ví dụ: `'%search_term'`). Điều này ngăn CSDL sử dụng các chỉ mục (index) thông thường trên cột đó, dẫn đến việc phải quét toàn bộ bảng (full table scan), làm giảm hiệu năng nghiêm trọng trên các bảng lớn.
*   **Sai lầm thường gặp:** Nhầm lẫn về `BETWEEN`. Hãy nhớ rằng `BETWEEN` là bao gồm cả hai đầu mút (`inclusive`). Đối với kiểu dữ liệu `DATETIME`, điều này có thể gây ra kết quả không mong muốn. Ví dụ, `BETWEEN '2024-10-01' AND '2024-10-05'` sẽ bao gồm cả những giao dịch lúc 00:00:00 của ngày 5, nhưng bỏ sót những giao dịch sau đó trong ngày. Một cách an toàn hơn là dùng: `WHERE date_column >= '2024-10-01' AND date_column < '2024-10-06'`.

#### **6. `GROUP BY` + `HAVING` (Các trường hợp sử dụng trong kinh doanh)**

**Lý thuyết**

*   **`GROUP BY` (Gom nhóm):** Mệnh đề này cho phép bạn gom nhiều hàng có cùng giá trị ở một hoặc nhiều cột thành một hàng tóm tắt duy nhất. `GROUP BY` gần như luôn luôn được sử dụng cùng với các hàm tổng hợp (`COUNT`, `SUM`, `AVG`, v.v.) để thực hiện tính toán trên mỗi nhóm.

    *   **Tương tự trong kinh doanh:** Hãy tưởng tượng bạn là quản lý một chuỗi cửa hàng. Bảng dữ liệu của bạn chứa hàng ngàn giao dịch riêng lẻ từ tất cả các chi nhánh. Sếp của bạn không muốn xem từng giao dịch. Thay vào đó, ông ấy muốn biết: "Tổng doanh thu của *từng chi nhánh* là bao nhiêu?"
        *   Hành động "gom tất cả giao dịch của từng chi nhánh lại" chính là `GROUP BY chi_nhanh`.
        *   Hành động "tính tổng doanh thu" chính là `SUM(doanh_thu)`.

*   **`HAVING` (Lọc nhóm):** Nếu `WHERE` là bộ lọc cho các hàng riêng lẻ *trước khi* gom nhóm, thì `HAVING` là bộ lọc cho các nhóm *sau khi* đã được tạo ra bởi `GROUP BY`. Bạn chỉ có thể dùng `HAVING` với các điều kiện dựa trên kết quả của các hàm tổng hợp.

    *   **Tương tự trong kinh doanh:** Sau khi bạn đã có báo cáo tổng doanh thu của từng chi nhánh, sếp bạn lại yêu cầu: "Chỉ cho tôi xem những chi nhánh có tổng doanh thu *trên 1 tỷ đồng*."
        *   Bạn không thể dùng `WHERE` ở đây, vì "tổng doanh thu" không phải là một cột có sẵn trong bảng giao dịch ban đầu. Nó là một giá trị được tính toán sau khi gom nhóm.
        *   Lúc này, bạn phải dùng `HAVING SUM(doanh_thu) > 1000000000` để lọc ra những nhóm (chi nhánh) thỏa mãn điều kiện.

**Thứ tự thực thi logic:** `FROM` -> `WHERE` -> `GROUP BY` -> `HAVING` -> `SELECT` -> `ORDER BY`.

---

**Ví dụ SQL**

Chúng ta tiếp tục sử dụng bảng `orders` đã tạo.

| order_id | customer_name | product_category | total_amount | order_date |
| -------- | ------------- | ---------------- | ------------ | ---------- |
| 1        | Nguyễn Văn A  | Điện tử          | 1500000.00   | 2024-10-01 |
| 2        | Trần Thị B    | Thời trang       | 850000.00    | 2024-10-03 |
| 3        | Lê Văn C      | Điện tử          | 3200000.00   | 2024-10-05 |
| 4        | Phạm Thị D    | Gia dụng         | 550000.00    | 2024-10-05 |
| 5        | Nguyễn Văn A  | Gia dụng         | 1200000.00   | 2024-10-08 |
| 6        | Hoàng Văn E   | Điện tử          | 7800000.00   | 2024-10-12 |

1.  **Ví dụ `GROUP BY` cơ bản:** Đếm số lượng đơn hàng cho mỗi danh mục sản phẩm.

    ```sql
    SELECT product_category, COUNT(order_id) AS so_luong_don_hang
    FROM orders
    GROUP BY product_category;
    ```
    *   **Giải thích:** CSDL sẽ quét bảng `orders`, gom các hàng có cùng `product_category` lại. Sau đó, với mỗi nhóm, nó sẽ thực hiện hàm `COUNT` để đếm số đơn hàng.
    *   **Kết quả:**
        ```
        product_category | so_luong_don_hang
        -----------------+--------------------
        Điện tử          | 3
        Thời trang       | 1
        Gia dụng         | 2
        ```

2.  **Ví dụ `GROUP BY` với nhiều hàm tổng hợp:** Tính tổng doanh thu và doanh thu trung bình cho mỗi danh mục.

    ```sql
    SELECT
        product_category,
        SUM(total_amount) AS tong_doanh_thu,
        AVG(total_amount) AS doanh_thu_trung_binh
    FROM orders
    GROUP BY product_category;
    ```
    *   **Kết quả:**
        ```
        product_category | tong_doanh_thu | doanh_thu_trung_binh
        -----------------+----------------+----------------------
        Điện tử          | 12500000.00    | 4166666.67
        Thời trang       | 850000.00      | 850000.00
        Gia dụng         | 1750000.00     | 875000.00
        ```

3.  **Ví dụ `GROUP BY` và `HAVING`:** Tìm các danh mục có tổng doanh thu trên 2,000,000.

    ```sql
    SELECT product_category, SUM(total_amount) AS tong_doanh_thu
    FROM orders
    GROUP BY product_category
    HAVING SUM(total_amount) > 2000000;
    ```
    *   **Giải thích:** Câu lệnh này hoạt động y hệt ví dụ 2, nhưng sau khi các nhóm được tạo và tính toán xong, mệnh đề `HAVING` sẽ lọc và chỉ giữ lại những nhóm có `tong_doanh_thu` lớn hơn 2,000,000.
    *   **Kết quả:**
        ```
        product_category | tong_doanh_thu
        -----------------+----------------
        Điện tử          | 12500000.00
        ```

4.  **Ví dụ kết hợp `WHERE` và `HAVING`:** Tính tổng chi tiêu cho mỗi khách hàng, nhưng chỉ tính các đơn hàng có giá trị trên 1,000,000, và cuối cùng chỉ hiển thị những khách hàng có tổng chi tiêu (sau khi lọc) lớn hơn 5,000,000.

    ```sql
    SELECT customer_name, SUM(total_amount) AS tong_chi_tieu
    FROM orders
    WHERE total_amount > 1000000 -- Lọc các hàng riêng lẻ TRƯỚC KHI gom nhóm
    GROUP BY customer_name
    HAVING SUM(total_amount) > 5000000; -- Lọc các nhóm SAU KHI gom nhóm
    ```
    *   **Kết quả:**
        ```
        customer_name | tong_chi_tieu
        --------------+---------------
        Hoàng Văn E   | 7800000.00
        ```
    *   **Phân tích logic:**
        1.  `WHERE total_amount > 1000000` loại bỏ các đơn hàng của 'Trần Thị B' (850k) và 'Phạm Thị D' (550k).
        2.  Các hàng còn lại được `GROUP BY customer_name`.
        3.  `SUM` được tính trên các nhóm này: A (1.5M + 1.2M = 2.7M), C (3.2M), E (7.8M).
        4.  `HAVING SUM(...) > 5000000` lọc và chỉ giữ lại nhóm của 'Hoàng Văn E'.

---

**Bài tập nhỏ**

Sử dụng bảng `orders`, viết câu lệnh SQL để:
1.  Tìm ra những khách hàng đã mua hàng nhiều hơn một lần.
2.  Hiển thị tên khách hàng và số lần mua hàng của họ.

**Giải pháp**

```sql
SELECT customer_name, COUNT(order_id) AS so_lan_mua
FROM orders
GROUP BY customer_name
HAVING COUNT(order_id) > 1;
```
*   **Phân tích:**
    1.  `GROUP BY customer_name`: Gom tất cả đơn hàng của mỗi khách hàng vào một nhóm.
    2.  `COUNT(order_id)`: Đếm số đơn hàng trong mỗi nhóm.
    3.  `HAVING COUNT(order_id) > 1`: Lọc ra những nhóm (khách hàng) có số đếm lớn hơn 1.
*   **Kết quả:**
    ```
    customer_name | so_lan_mua
    --------------+------------
    Nguyễn Văn A  | 2
    ```

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt cốt lõi giữa `WHERE` và `HAVING` là gì?"
    *   **Trả lời:**
        1.  **Thời điểm lọc:** `WHERE` lọc dữ liệu ở cấp độ hàng (row-level) *trước khi* bất kỳ hoạt động gom nhóm nào diễn ra. `HAVING` lọc dữ liệu ở cấp độ nhóm (group-level) *sau khi* các hàng đã được gom lại bởi `GROUP BY`.
        2.  **Đối tượng lọc:** `WHERE` làm việc với các cột thông thường. `HAVING` làm việc với các hàm tổng hợp (`SUM`, `COUNT`, v.v.). Bạn không thể viết `WHERE COUNT(*) > 10`, mà phải viết `HAVING COUNT(*) > 10`.

*   **Câu hỏi:** "Tôi có thể sử dụng một cột trong mệnh đề `SELECT` mà không nằm trong `GROUP BY` và cũng không phải là một hàm tổng hợp không? Tại sao?"
    *   **Trả lời:** Theo chuẩn SQL, là không thể. Lý do là vì nó gây ra sự mơ hồ. Khi bạn gom nhiều hàng thành một, CSDL không biết phải hiển thị giá trị nào từ cột đó. Ví dụ, nếu bạn `GROUP BY product_category` và `SELECT customer_name`, trong nhóm 'Điện tử' có 3 khách hàng khác nhau ('A', 'C', 'E'), CSDL không biết phải chọn tên nào để hiển thị. (Lưu ý: Một số hệ CSDL như MySQL có thể có chế độ "nới lỏng" cho phép điều này, nhưng nó thường trả về một giá trị không thể đoán trước từ nhóm và là một thực hành không tốt).

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Luôn ưu tiên lọc bằng `WHERE` nếu có thể. `WHERE` loại bỏ các hàng không cần thiết từ sớm, giúp giảm lượng dữ liệu mà `GROUP BY` phải xử lý, từ đó tăng hiệu suất truy vấn. Chỉ dùng `HAVING` khi bạn cần lọc dựa trên kết quả của một hàm tổng hợp.
*   **Sai lầm thường gặp:** Dùng `WHERE` với hàm tổng hợp. Ví dụ: `WHERE SUM(total_amount) > 100000`. Đây là lỗi cú pháp vì `WHERE` không biết `SUM` là gì, nó chỉ làm việc trên từng hàng.
*   **Sai lầm thường gặp:** Viết một câu `SELECT` có hàm tổng hợp nhưng quên `GROUP BY`. Ví dụ: `SELECT product_category, SUM(total_amount) FROM orders;`. Hầu hết các CSDL sẽ báo lỗi, vì chúng không biết bạn muốn tính tổng theo nhóm nào.
*   **Sai lầm thường gặp:** Đặt các cột trong `SELECT` không nhất quán với `GROUP BY`. Quy tắc vàng là: **Bất kỳ cột nào trong mệnh đề `SELECT` đều phải xuất hiện trong mệnh đề `GROUP BY` hoặc phải được bọc trong một hàm tổng hợp.**

#### **7. Joins: `INNER`, `LEFT`, `RIGHT`, `FULL`, `CROSS` — Khi nào dùng loại nào**

**Lý thuyết**

Trong một CSDL được thiết kế tốt, dữ liệu thường được tách ra thành nhiều bảng khác nhau để tránh lặp lại thông tin (đây gọi là "chuẩn hóa", sẽ học ở Giai đoạn 2). Ví dụ, thay vì lưu tên và địa chỉ của phòng ban trong bảng nhân viên, ta sẽ tạo một bảng `phong_ban` riêng và chỉ lưu `id_phong_ban` trong bảng `nhan_vien`.

`JOIN` là mệnh lệnh cho phép chúng ta "ghép" các bảng này lại với nhau dựa trên một cột chung (thường là khóa chính - khóa ngoại) để tạo ra một bảng kết quả tạm thời chứa thông tin từ tất cả các bảng được ghép.

**Tương tự:** Hãy tưởng tượng bạn có hai danh sách:
1.  Danh sách `SINHVIEN` (mã sinh viên, tên sinh viên, mã lớp).
2.  Danh sách `LOP` (mã lớp, tên lớp, tên giáo viên chủ nhiệm).

`JOIN` giúp bạn trả lời câu hỏi: "Tên giáo viên chủ nhiệm của sinh viên 'Nguyễn Văn A' là ai?" bằng cách kết nối hai danh sách này qua `mã lớp`.

**Các loại JOIN và Sơ đồ Venn**

| Loại JOIN       | Diễn giải                                                              | Sơ đồ Venn (A và B là hai bảng)                                                                                             |
| ---------------- | ---------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **`INNER JOIN`** | Lấy những hàng có khóa khớp ở **cả hai** bảng.                         | Phần giao nhau ở giữa.                                                                                                     |
| **`LEFT JOIN`**  | Lấy **tất cả** hàng từ bảng bên trái và chỉ những hàng khớp từ bảng bên phải. Nếu không khớp, cột của bảng phải sẽ là `NULL`. | Toàn bộ hình tròn A, cộng với phần giao nhau.                                                                              |
| **`RIGHT JOIN`** | Lấy **tất cả** hàng từ bảng bên phải và chỉ những hàng khớp từ bảng bên trái. Nếu không khớp, cột của bảng trái sẽ là `NULL`. | Toàn bộ hình tròn B, cộng với phần giao nhau.                                                                              |
| **`FULL JOIN`**  | Lấy **tất cả** hàng từ cả hai bảng. Ghép nối nếu có thể, nếu không thì điền `NULL` vào các cột còn thiếu. | Toàn bộ cả hai hình tròn A và B.                                                                                             |
| **`CROSS JOIN`** | Lấy mọi sự kết hợp có thể có giữa các hàng từ hai bảng (tích Đề-các). | Không thể hiện bằng sơ đồ Venn. Nếu bảng A có M hàng, B có N hàng, kết quả sẽ có M \* N hàng. |

---

**Ví dụ SQL**

Hãy tạo hai bảng `employees` (nhân viên) và `departments` (phòng ban) để thực hành.

```sql
CREATE TABLE departments (
    department_id INT PRIMARY KEY,
    department_name VARCHAR(50)
);

CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    employee_name VARCHAR(50),
    department_id INT -- Khóa ngoại có thể NULL
);

INSERT INTO departments VALUES
(1, 'Kinh doanh'),
(2, 'Kỹ thuật'),
(3, 'Nhân sự'),
(4, 'Marketing'); -- Phòng ban này chưa có ai

INSERT INTO employees VALUES
(101, 'An Nguyễn', 1),
(102, 'Bình Lê', 2),
(103, 'Chi Trần', 2),
(104, 'Dũng Phạm', 1),
(105, 'Hương Mai', NULL); -- Nhân viên này chưa thuộc phòng ban nào
```

**1. `INNER JOIN` (Chỉ nhân viên có phòng ban)**
*   **Mục đích:** Lấy danh sách nhân viên và tên phòng ban tương ứng của họ. Những nhân viên chưa có phòng ban hoặc phòng ban chưa có nhân viên sẽ bị bỏ qua.

```sql
SELECT e.employee_name, d.department_name
FROM employees AS e
INNER JOIN departments AS d ON e.department_id = d.department_id;
```
*   **Giải thích:** `AS e` và `AS d` là bí danh (alias) giúp viết câu lệnh ngắn gọn hơn. Mệnh đề `ON` chỉ định điều kiện kết nối: chỉ ghép những hàng nào có `department_id` bằng nhau ở cả hai bảng.
*   **Kết quả:**
    ```
    employee_name | department_name
    --------------+-----------------
    An Nguyễn     | Kinh doanh
    Bình Lê       | Kỹ thuật
    Chi Trần      | Kỹ thuật
    Dũng Phạm     | Kinh doanh
    ```

**2. `LEFT JOIN` (Tất cả nhân viên, dù có phòng ban hay không)**
*   **Mục đích:** Lấy danh sách *tất cả* nhân viên và tên phòng ban của họ. Nếu nhân viên nào chưa có phòng ban, cột `department_name` sẽ hiển thị `NULL`.

```sql
SELECT e.employee_name, d.department_name
FROM employees AS e
LEFT JOIN departments AS d ON e.department_id = d.department_id;
```
*   **Kết quả:**
    ```
    employee_name | department_name
    --------------+-----------------
    An Nguyễn     | Kinh doanh
    Bình Lê       | Kỹ thuật
    Chi Trần      | Kỹ thuật
    Dũng Phạm     | Kinh doanh
    Hương Mai     | NULL
    ```

**3. `RIGHT JOIN` (Tất cả phòng ban, dù có nhân viên hay không)**
*   **Mục đích:** Lấy danh sách *tất cả* phòng ban và các nhân viên trong đó. Nếu phòng ban nào chưa có nhân viên, cột `employee_name` sẽ hiển thị `NULL`.

```sql
SELECT e.employee_name, d.department_name
FROM employees AS e
RIGHT JOIN departments AS d ON e.department_id = d.department_id;
```
*   **Kết quả:**
    ```
    employee_name | department_name
    --------------+-----------------
    An Nguyễn     | Kinh doanh
    Dũng Phạm     | Kinh doanh
    Bình Lê       | Kỹ thuật
    Chi Trần      | Kỹ thuật
    NULL          | Nhân sự
    NULL          | Marketing
    ```

**4. `FULL OUTER JOIN` (Tất cả nhân viên VÀ tất cả phòng ban)**
*   **Mục đích:** Lấy tất cả dữ liệu từ cả hai bảng.

```sql
-- Lưu ý: MySQL không hỗ trợ FULL OUTER JOIN. Bạn có thể giả lập nó bằng cách UNION một LEFT JOIN và một RIGHT JOIN.
-- Với PostgreSQL hoặc SQL Server, bạn có thể chạy:
SELECT e.employee_name, d.department_name
FROM employees AS e
FULL OUTER JOIN departments AS d ON e.department_id = d.department_id;
```
*   **Kết quả:**
    ```
    employee_name | department_name
    --------------+-----------------
    An Nguyễn     | Kinh doanh
    Bình Lê       | Kỹ thuật
    Chi Trần      | Kỹ thuật
    Dũng Phạm     | Kinh doanh
    Hương Mai     | NULL
    NULL          | Nhân sự
    NULL          | Marketing
    ```

---

**Bài tập nhỏ**

Sử dụng hai bảng trên, viết một câu lệnh SQL để tìm ra những phòng ban **chưa có** bất kỳ nhân viên nào.

**Giải pháp**

Đây là một trường hợp kinh điển của việc sử dụng `LEFT JOIN` (hoặc `RIGHT JOIN`) kết hợp với `WHERE`.

```sql
SELECT d.department_name
FROM departments AS d
LEFT JOIN employees AS e ON d.department_id = e.department_id
WHERE e.employee_id IS NULL;
```
*   **Phân tích:**
    1.  `departments AS d LEFT JOIN employees AS e`: Chúng ta lấy tất cả phòng ban (bảng trái) và ghép với các nhân viên tương ứng.
    2.  Với những phòng ban không có nhân viên (như 'Nhân sự', 'Marketing'), tất cả các cột từ bảng `employees` (bao gồm `e.employee_id`) sẽ là `NULL`.
    3.  `WHERE e.employee_id IS NULL`: Chúng ta lọc ra chính những hàng `NULL` đó, đó chính là các phòng ban không có nhân viên.
*   **Kết quả:**
    ```
    department_name
    ---------------
    Nhân sự
    Marketing
    ```

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt chính giữa `INNER JOIN` và `LEFT JOIN` là gì? Hãy cho một ví dụ thực tế khi bạn phải dùng `LEFT JOIN`."
    *   **Trả lời:** `INNER JOIN` chỉ trả về các hàng có sự tương ứng ở cả hai bảng (phần giao). `LEFT JOIN` trả về tất cả các hàng của bảng bên trái, bất kể chúng có tìm thấy sự tương ứng ở bảng bên phải hay không. Một ví dụ kinh điển là tìm "khách hàng chưa từng đặt đơn hàng nào". Bạn sẽ `LEFT JOIN` từ bảng `customers` (khách hàng) sang bảng `orders` (đơn hàng). Những khách hàng có `order_id` là `NULL` chính là những người chưa từng mua hàng.

*   **Câu hỏi:** "Sự khác biệt khi đặt một điều kiện lọc trong mệnh đề `ON` so với trong mệnh đề `WHERE` của một `LEFT JOIN`?"
    *   **Trả lời:** Đây là một câu hỏi nâng cao để kiểm tra sự hiểu biết sâu sắc.
        *   Điều kiện đặt trong `ON`: Được áp dụng *trong quá trình* ghép nối. Đối với `LEFT JOIN`, nó sẽ cố gắng tìm một hàng trong bảng bên phải khớp với cả điều kiện `JOIN` và điều kiện bổ sung này. Nếu không tìm thấy, nó vẫn giữ lại hàng bên trái và điền `NULL` cho các cột bên phải.
        *   Điều kiện đặt trong `WHERE`: Được áp dụng *sau khi* việc ghép nối đã hoàn tất. Nếu bạn đặt điều kiện lọc trên một cột của bảng bên phải trong `WHERE`, nó sẽ loại bỏ tất cả các hàng không thỏa mãn, bao gồm cả những hàng mà `LEFT JOIN` đã tạo ra với giá trị `NULL`. Điều này vô tình biến `LEFT JOIN` thành `INNER JOIN`.

*   **Câu hỏi:** "`RIGHT JOIN` có thực sự cần thiết không?"
    *   **Trả lời:** Về mặt logic, `RIGHT JOIN` không thực sự cần thiết vì bất kỳ câu lệnh `RIGHT JOIN` nào cũng có thể được viết lại thành một câu lệnh `LEFT JOIN` bằng cách đảo ngược thứ tự các bảng. Tuy nhiên, `LEFT JOIN` được sử dụng phổ biến hơn và được coi là dễ đọc hơn theo quy ước.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Luôn sử dụng bí danh (alias) cho các bảng khi `JOIN` nhiều hơn một bảng. Nó làm cho câu lệnh của bạn ngắn hơn, dễ đọc hơn và tránh lỗi mơ hồ khi các bảng có cột trùng tên.
*   **Thực tiễn tốt nhất:** Nên viết rõ `INNER JOIN` thay vì chỉ `JOIN`. Mặc dù `JOIN` mặc định là `INNER JOIN`, việc viết rõ ràng giúp người đọc code hiểu ngay ý định của bạn.
*   **Sai lầm thường gặp:** Ghép nhầm `LEFT JOIN` thành `INNER JOIN` như đã giải thích ở câu hỏi phỏng vấn. Ví dụ, để tìm nhân viên trong phòng 'Kỹ thuật':
    *   **SAI:** `... LEFT JOIN departments d ON ... WHERE d.department_name = 'Kỹ thuật'`. Cách này sẽ loại bỏ nhân viên 'Hương Mai' (vì `department_name` của cô là `NULL`, không bằng 'Kỹ thuật').
    *   **ĐÚNG:** `... LEFT JOIN departments d ON e.department_id = d.department_id AND d.department_name = 'Kỹ thuật'`. Cách này giữ lại 'Hương Mai' và chỉ hiển thị tên phòng ban nếu nó là 'Kỹ thuật'.
*   **Sai lầm thường gặp:** Sử dụng `CROSS JOIN` một cách vô ý. Nếu bạn quên mệnh đề `ON` khi `JOIN` hai bảng lớn, CSDL có thể thực hiện một `CROSS JOIN`, tạo ra một tập kết quả khổng lồ và làm treo hệ thống.

