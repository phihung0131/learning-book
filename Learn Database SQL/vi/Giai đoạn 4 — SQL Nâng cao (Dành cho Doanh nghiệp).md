#### **1. Subqueries (Truy vấn con): Correlated vs Non-correlated**

**Lý thuyết**

**Subquery (hay inner query)** là một câu lệnh `SELECT` được lồng bên trong một câu lệnh SQL khác (`SELECT`, `INSERT`, `UPDATE`, hoặc `DELETE`, hoặc thậm chí bên trong một subquery khác).

*   **Tương tự:** Hãy tưởng tượng sếp yêu cầu bạn: "Tìm cho tôi những nhân viên có mức lương cao hơn mức lương trung bình của toàn công ty."
    *   Để trả lời, bạn phải làm 2 bước:
        1.  Tính mức lương trung bình của công ty. (Đây là subquery)
        2.  Lấy danh sách nhân viên có lương > con số vừa tính được. (Đây là outer query)
    *   Subquery cho phép bạn thực hiện hai bước này trong cùng một câu lệnh.

**Non-Correlated Subquery (Truy vấn con không tương quan)**

*   **Định nghĩa:** Là một truy vấn con **độc lập**. Nó có thể tự chạy một mình mà không cần bất kỳ thông tin nào từ truy vấn bên ngoài. CSDL sẽ thực thi truy vấn con này **một lần duy nhất**, lấy kết quả của nó, và sau đó sử dụng kết quả đó cho truy vấn bên ngoài.
*   **Ví dụ (bài toán lương trung bình):**
    ```sql
    SELECT employee_name, salary
    FROM employees
    WHERE salary > (SELECT AVG(salary) FROM employees); -- Subquery này có thể tự chạy
    ```
    *   **Cách hoạt động:**
        1.  CSDL chạy `(SELECT AVG(salary) FROM employees)` trước, giả sử kết quả là `55000`.
        2.  Câu lệnh bên ngoài trở thành `SELECT ... WHERE salary > 55000;` và được thực thi.

**Correlated Subquery (Truy vấn con tương quan)**

*   **Định nghĩa:** Là một truy vấn con **phụ thuộc** vào truy vấn bên ngoài. Nó sử dụng các giá trị từ hàng mà truy vấn bên ngoài đang xử lý. Do đó, truy vấn con này sẽ được **thực thi lặp lại, một lần cho mỗi hàng** của truy vấn bên ngoài.
*   **Tương tự:** Sếp lại yêu cầu: "Tìm cho tôi những nhân viên có mức lương cao nhất trong *chính phòng ban của họ*."
    *   Với mỗi nhân viên, bạn phải:
        1.  Xem nhân viên đó thuộc phòng ban nào.
        2.  Tìm mức lương cao nhất trong phòng ban đó. (Đây là correlated subquery)
        3.  So sánh lương của nhân viên hiện tại với con số vừa tìm được.

*   **Ví dụ:**
    ```sql
    SELECT employee_name, salary, department_id
    FROM employees AS e1
    WHERE salary = (
        SELECT MAX(salary)
        FROM employees AS e2
        WHERE e2.department_id = e1.department_id -- Phụ thuộc vào e1 của truy vấn ngoài
    );
    ```
    *   **Cách hoạt động:**
        1.  Truy vấn ngoài lấy hàng đầu tiên, ví dụ nhân viên An, phòng ban `D1`.
        2.  Subquery được chạy với `WHERE e2.department_id = 'D1'`, tìm ra lương max của phòng D1.
        3.  So sánh lương của An với lương max đó.
        4.  Truy vấn ngoài lấy hàng tiếp theo, ví dụ nhân viên Bình, phòng ban `D2`.
        5.  Subquery lại được chạy với `WHERE e2.department_id = 'D2'`, tìm ra lương max của phòng D2.
        6.  ... và cứ thế cho đến hết bảng.

**Vị trí sử dụng Subquery:**
*   Trong `SELECT`: Subquery trả về một giá trị duy nhất (scalar subquery).
*   Trong `FROM`: Subquery trả về một bảng tạm (derived table).
*   Trong `WHERE`: Phổ biến nhất, thường dùng với các toán tử `IN`, `NOT IN`, `ANY`, `ALL`, `EXISTS`.

---

**Ví dụ SQL**

**Non-Correlated:**
```sql
-- Tìm các khách hàng chưa từng đặt đơn hàng nào
SELECT customer_name
FROM customers
WHERE customer_id NOT IN (SELECT DISTINCT customer_id FROM orders);
```

**Correlated (với `EXISTS`):**
`EXISTS` là toán tử hiệu quả để kiểm tra sự tồn tại. Nó trả về `TRUE` nếu subquery trả về ít nhất một hàng.
```sql
-- Tìm tất cả các phòng ban có ít nhất một nhân viên
SELECT d.department_name
FROM departments d
WHERE EXISTS (
    SELECT 1
    FROM employees e
    WHERE e.department_id = d.department_id -- Tương quan
);
```
*   **Giải thích:** Với mỗi phòng ban `d`, CSDL sẽ chạy subquery để xem có nhân viên `e` nào khớp `department_id` không. Ngay khi tìm thấy một người, `EXISTS` trả về `TRUE` và CSDL dừng subquery ngay lập tức, rất hiệu quả.

---

**Bài tập nhỏ**

Sử dụng bảng `employees` (id, name, department_id, salary), hãy viết một truy vấn con (subquery) để tìm tên của các nhân viên làm việc trong phòng ban có tên là 'Sales'. Giả sử bạn có bảng `departments` (id, name).

**Giải pháp**

```sql
SELECT name
FROM employees
WHERE department_id = (
    SELECT id
    FROM departments
    WHERE name = 'Sales'
);
```
*   **Phân loại:** Đây là một **non-correlated subquery**. Subquery `(SELECT id FROM departments WHERE name = 'Sales')` được chạy một lần duy nhất để lấy ID của phòng 'Sales', sau đó kết quả được dùng để lọc bảng `employees`.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt chính giữa Correlated và Non-correlated Subquery là gì? Cái nào thường nhanh hơn?"
    *   **Trả lời:** Non-correlated subquery là truy vấn độc lập, được thực thi một lần duy nhất trước truy vấn bên ngoài. Correlated subquery phụ thuộc vào truy vấn ngoài và được thực thi lặp lại cho mỗi hàng của truy vấn ngoài. Do đó, **non-correlated subquery thường nhanh hơn đáng kể**. Correlated subquery có thể gây ra các vấn đề nghiêm trọng về hiệu năng trên các bảng lớn vì nó hoạt động giống như một vòng lặp lồng nhau (nested loop).

*   **Câu hỏi:** "Khi nào bạn sẽ dùng `JOIN` thay vì `Subquery`?"
    *   **Trả lời:** Trong nhiều trường hợp, một subquery (đặc biệt là non-correlated) có thể được viết lại bằng `JOIN`, và ngược lại. Hầu hết các bộ tối ưu hóa truy vấn hiện đại (query optimizer) đủ thông minh để chuyển đổi chúng qua lại và chọn ra kế hoạch thực thi tốt nhất. Tuy nhiên, theo quy ước:
        *   **`JOIN` thường được ưu tiên** vì nó thường dễ đọc và diễn đạt mối quan hệ giữa các bảng một cách rõ ràng hơn.
        *   **Subquery** rất hữu ích khi bạn cần tính toán một giá trị tổng hợp trước (như `AVG`, `MAX`) để so sánh, hoặc khi logic phức tạp và việc tách nó ra thành một subquery giúp câu lệnh dễ hiểu hơn.
        *   `NOT IN` với subquery có thể hoạt động không như mong đợi nếu subquery trả về giá trị `NULL`. Trong trường hợp này, `LEFT JOIN ... WHERE ... IS NULL` hoặc `NOT EXISTS` là những lựa chọn an toàn và hiệu quả hơn.

*   **Câu hỏi:** "`EXISTS` vs `IN`: Bạn chọn cái nào và tại sao?"
    *   **Trả lời:**
        *   `EXISTS` chỉ kiểm tra sự tồn tại. Ngay khi tìm thấy một hàng khớp, nó sẽ dừng lại và trả về `TRUE`. Nó không quan tâm có bao nhiêu hàng khớp.
        *   `IN` so sánh giá trị với một danh sách. Nó phải thu thập tất cả kết quả từ subquery, sau đó mới thực hiện so sánh.
        *   **Quy tắc chung:** `EXISTS` thường hoạt động hiệu quả hơn `IN` khi subquery trả về một tập kết quả rất lớn, vì `EXISTS` có thể "thoát sớm". `IN` có thể hiệu quả hơn nếu kết quả của subquery rất nhỏ.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Luôn ưu tiên viết lại một correlated subquery bằng `JOIN` nếu có thể. Hiệu năng thường sẽ tốt hơn đáng kể.
    *   Ví dụ: Thay vì `WHERE salary = (SELECT MAX(salary) FROM ... WHERE ...)` có thể dùng Window Function (sẽ học ở phần sau) hoặc `JOIN`.
*   **Thực tiễn tốt nhất:** Khi sử dụng subquery trả về một bảng (`FROM (SELECT ...)`), luôn đặt cho nó một bí danh (alias). Ví dụ: `... FROM (SELECT ...) AS derived_table;`.
*   **Sai lầm thường gặp:** Viết một subquery trong mệnh đề `SELECT` mà có thể trả về nhiều hơn một hàng. Điều này sẽ gây ra lỗi runtime. Subquery ở vị trí này phải luôn là scalar (trả về 1 hàng, 1 cột).
*   **Sai lầm thường gặp:** Sử dụng `NOT IN` với một subquery có thể chứa giá trị `NULL`.
    *   Ví dụ: `WHERE id NOT IN (1, 2, NULL)`. Câu lệnh này sẽ không trả về bất cứ hàng nào, vì logic của SQL cho rằng `id = NULL` là không xác định (`UNKNOWN`), do đó `id != NULL` cũng là `UNKNOWN`.
    *   Hãy dùng `NOT EXISTS` hoặc `LEFT JOIN` để thay thế, chúng xử lý `NULL` một cách an toàn.

---

#### **2. Window Functions: `ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LAG()`, `LEAD()`**

**Lý thuyết**

**Window Functions (Hàm Cửa sổ)** là một trong những tính năng mạnh mẽ nhất của SQL hiện đại. Nó cho phép bạn thực hiện các phép tính trên một tập hợp các hàng (một "cửa sổ") có liên quan đến hàng hiện tại, nhưng không giống như `GROUP BY`, nó **không gộp các hàng đó lại**. Kết quả của hàm cửa sổ được trả về như một cột mới cho mỗi hàng.

*   **Tương tự:** Hãy tưởng tượng bạn có danh sách điểm thi của học sinh, đã sắp xếp theo điểm từ cao đến thấp.
    *   `GROUP BY` giống như việc bạn tính "điểm trung bình của cả lớp" -> chỉ ra 1 con số duy nhất.
    *   **Window Function** giống như việc bạn đi dọc theo danh sách và làm những việc sau:
        *   "Đánh số thứ tự từ 1 đến hết" -> `ROW_NUMBER()`
        *   "Xếp hạng, nếu có người bằng điểm thì cùng hạng, hạng tiếp theo bị nhảy cóc" -> `RANK()`
        *   "So sánh điểm của học sinh này với điểm của học sinh *ngay phía trước* trong danh sách" -> `LAG()`

**Cú pháp chung:**
`FUNCTION_NAME() OVER ( [PARTITION BY ...] [ORDER BY ...] )`

*   `FUNCTION_NAME()`: Tên của hàm cửa sổ (`ROW_NUMBER`, `SUM`, `AVG`...).
*   `OVER()`: Từ khóa báo hiệu đây là một hàm cửa sổ.
*   `PARTITION BY column_name`: Chia các hàng thành các "phân vùng" (nhóm) riêng biệt. Hàm cửa sổ sẽ được tính toán lại từ đầu cho mỗi phân vùng. Giống như `GROUP BY` nhưng không gộp hàng.
*   `ORDER BY column_name`: Sắp xếp các hàng bên trong mỗi phân vùng. Điều này rất quan trọng đối với các hàm xếp hạng và so sánh vị trí.

**Các hàm phổ biến:**

1.  **Hàm Xếp hạng (Ranking):**
    *   `ROW_NUMBER()`: Đánh số thứ tự duy nhất cho mỗi hàng (1, 2, 3, 4, ...). Không có hai hàng nào có cùng số thứ tự.
    *   `RANK()`: Xếp hạng các hàng. Nếu có các hàng bằng nhau, chúng sẽ có cùng hạng, và hạng tiếp theo sẽ bị bỏ qua. (1, 2, 2, 4, ...)
    *   `DENSE_RANK()`: Tương tự `RANK()`, nhưng hạng tiếp theo không bị bỏ qua. (1, 2, 2, 3, ...)

2.  **Hàm Vị trí (Positional):**
    *   `LAG(column, offset, default_value)`: Lấy giá trị của `column` từ hàng đứng *trước* hàng hiện tại `offset` vị trí.
    *   `LEAD(column, offset, default_value)`: Lấy giá trị của `column` từ hàng đứng *sau* hàng hiện tại `offset` vị trí.

---

**Ví dụ SQL**

Hãy dùng lại bảng `employees` có thêm dữ liệu điểm bán hàng `sales_score`.

| id  | name     | department | salary |
| --- | -------- | ---------- | ------ |
| 1   | An       | Sales      | 70000  |
| 2   | Bình     | Sales      | 80000  |
| 3   | Chi      | Sales      | 70000  |
| 4   | Dũng     | Engineering| 90000  |
| 5   | Giang    | Engineering| 120000 |
| 6   | Hương    | Engineering| 90000  |

**Ví dụ 1: Xếp hạng lương trong mỗi phòng ban**
Tìm 2 người có lương cao nhất trong mỗi phòng ban.
```sql
WITH RankedEmployees AS (
    SELECT
        name,
        department,
        salary,
        RANK() OVER (PARTITION BY department ORDER BY salary DESC) as salary_rank
    FROM employees
)
SELECT name, department, salary, salary_rank
FROM RankedEmployees
WHERE salary_rank <= 2;
```
*   **Phân tích:**
    1.  `PARTITION BY department`: Chia nhân viên thành 2 nhóm 'Sales' và 'Engineering'.
    2.  `ORDER BY salary DESC`: Sắp xếp trong mỗi nhóm theo lương giảm dần.
    3.  `RANK()`: Tính toán thứ hạng.
    4.  Truy vấn bên ngoài (sử dụng CTE, sẽ học ở phần sau) lọc lấy những người có hạng 1 hoặc 2.
*   **Kết quả:**

    | name     | department | salary | salary_rank |
    | -------- | ---------- | ------ | ----------- |
    | Giang    | Engineering| 120000 | 1           |
    | Dũng     | Engineering| 90000  | 2           |
    | Hương    | Engineering| 90000  | 2           |
    | Bình     | Sales      | 80000  | 1           |
    | An       | Sales      | 70000  | 2           |
    | Chi      | Sales      | 70000  | 2           |

**So sánh `ROW_NUMBER`, `RANK`, `DENSE_RANK`**
```sql
SELECT
    name,
    salary,
    ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,
    RANK() OVER (ORDER BY salary DESC) AS rnk,
    DENSE_RANK() OVER (ORDER BY salary DESC) AS dense_rnk
FROM employees;
```
*   **Kết quả:**

    | name     | salary | row_num | rnk | dense_rnk |
    | -------- | ------ | ------- | --- | --------- |
    | Giang    | 120000 | 1       | 1   | 1         |
    | Dũng     | 90000  | 2       | 2   | 2         |
    | Hương    | 90000  | 3       | 2   | 2         |
    | Bình     | 80000  | 4       | 4   | 3         |
    | An       | 70000  | 5       | 5   | 4         |
    | Chi      | 70000  | 6       | 5   | 4         |

**Ví dụ 2: So sánh lương với nhân viên trước đó**
```sql
SELECT
    name,
    department,
    salary,
    LAG(salary, 1, 0) OVER (PARTITION BY department ORDER BY salary) AS previous_salary
FROM employees;
```
*   **Phân tích:** So sánh lương của mỗi người với người có lương thấp hơn liền kề *trong cùng một phòng ban*.
*   **Kết quả:**

    | name     | department | salary | previous_salary |
    | -------- | ---------- | ------ | --------------- |
    | Dũng     | Engineering| 90000  | 0               |
    | Hương    | Engineering| 90000  | 90000           |
    | Giang    | Engineering| 120000 | 90000           |
    | An       | Sales      | 70000  | 0               |
    | Chi      | Sales      | 70000  | 70000           |
    | Bình     | Sales      | 80000  | 70000           |

---

**Bài tập nhỏ**

Cho bảng `monthly_sales` (month, revenue). Viết một truy vấn để hiển thị doanh thu của mỗi tháng, và một cột mới `growth_percentage` cho biết tỷ lệ tăng trưởng so với tháng trước đó.

**Giải pháp**
```sql
WITH PreviousMonthSales AS (
    SELECT
        month,
        revenue,
        LAG(revenue, 1, 0) OVER (ORDER BY month) AS previous_revenue
    FROM monthly_sales
)
SELECT
    month,
    revenue,
    ( (revenue - previous_revenue) / previous_revenue ) * 100 AS growth_percentage
FROM PreviousMonthSales
WHERE previous_revenue > 0;
```
*   **Phân tích:** Dùng `LAG` để lấy doanh thu tháng trước. Sau đó dùng một truy vấn bên ngoài (hoặc CTE) để thực hiện phép tính phần trăm.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt giữa `RANK()` và `DENSE_RANK()` là gì? Cho ví dụ."
    *   **Trả lời:** Cả hai đều dùng để xếp hạng và sẽ gán cùng một hạng cho các giá trị bằng nhau. Sự khác biệt nằm ở hạng tiếp theo. `RANK` sẽ bỏ qua các hạng tiếp theo (1, 2, 2, 4), trong khi `DENSE_RANK` thì không (1, 2, 2, 3). Ví dụ, nếu xếp hạng vận động viên, 2 người cùng về nhì, `RANK` sẽ xếp người tiếp theo hạng tư (vì không có hạng ba), còn `DENSE_RANK` sẽ xếp người tiếp theo hạng ba.

*   **Câu hỏi:** "Mục đích của `PARTITION BY` trong một hàm cửa sổ là gì? Nó khác gì với `GROUP BY`?"
    *   **Trả lời:** `PARTITION BY` chia tập dữ liệu thành các nhóm nhỏ, và hàm cửa sổ sẽ được áp dụng riêng biệt trên từng nhóm đó. Nó giống `GROUP BY` ở chỗ đều chia nhóm dữ liệu, nhưng khác biệt cơ bản là `PARTITION BY` **không làm giảm số lượng hàng trả về**. Nó giữ lại tất cả các hàng ban đầu và chỉ thêm một cột mới chứa kết quả của hàm cửa sổ cho mỗi hàng.

*   **Câu hỏi:** "Bạn sẽ giải quyết bài toán "tìm N mục hàng đầu cho mỗi nhóm" (Top-N-per-Group) như thế nào trong SQL?"
    *   **Trả lời:** Đây là bài toán kinh điển để sử dụng Window Functions. Tôi sẽ sử dụng một hàm xếp hạng như `RANK()` hoặc `ROW_NUMBER()` với `PARTITION BY` trên cột nhóm và `ORDER BY` trên cột cần xếp hạng. Sau đó, tôi sẽ dùng một truy vấn bên ngoài hoặc CTE để lọc lấy các hàng có thứ hạng nhỏ hơn hoặc bằng N.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Khi giải quyết các bài toán liên quan đến xếp hạng, so sánh giữa các hàng, hoặc tính toán lũy kế (running total), hãy nghĩ đến Window Functions đầu tiên. Chúng thường cung cấp một giải pháp gọn gàng và hiệu quả hơn nhiều so với việc dùng self-join hoặc correlated subquery.
*   **Thực tiễn tốt nhất:** `PARTITION BY` có thể làm việc trên nhiều cột, ví dụ `PARTITION BY department, job_title`.
*   **Sai lầm thường gặp:** Quên `ORDER BY` trong các hàm xếp hạng. `ORDER BY` là bắt buộc đối với `RANK`, `ROW_NUMBER`... vì chúng cần biết dựa trên tiêu chí nào để xếp hạng.
*   **Sai lầm thường gặp:** Cố gắng đặt điều kiện `WHERE` trực tiếp lên kết quả của hàm cửa sổ trong cùng một câu `SELECT`.
    *   **SAI:** `SELECT ..., RANK() OVER (...) AS rnk FROM ... WHERE rnk <= 5;`
    *   **Lý do:** Mệnh đề `WHERE` được xử lý *trước khi* các hàm cửa sổ được tính toán.
    *   **ĐÚNG:** Phải sử dụng một subquery hoặc CTE như trong ví dụ.

#### **3. CTE (Common Table Expression): `WITH` clause**

**Lý thuyết**

**CTE (Common Table Expression)** là một **bảng kết quả tạm thời**, được đặt tên, mà bạn có thể tham chiếu đến trong các câu lệnh `SELECT`, `INSERT`, `UPDATE`, hoặc `DELETE` tiếp theo. Nó được định nghĩa bằng mệnh đề `WITH`.

*   **Tương tự:** Hãy tưởng tượng bạn đang giải một bài toán phức tạp. Thay vì viết tất cả các bước tính toán lồng vào nhau trong một công thức khổng lồ duy nhất, bạn sẽ chia nhỏ bài toán:
    1.  Bước 1: Tính giá trị A. (Đây là CTE đầu tiên)
    2.  Bước 2: Dùng giá trị A để tính giá trị B. (Đây là CTE thứ hai, sử dụng CTE đầu tiên)
    3.  Bước 3: Dùng giá trị B để ra kết quả cuối cùng. (Đây là câu `SELECT` chính)
*   CTE giúp làm điều tương tự trong SQL: chia một truy vấn phức tạp thành các bước logic, dễ đọc và dễ quản lý.

**Tại sao nên dùng CTE?**

1.  **Cải thiện tính dễ đọc (Readability):** Đây là lợi ích lớn nhất. CTE cho phép bạn đặt tên cho các truy vấn con, giúp người khác (và chính bạn trong tương lai) hiểu được logic của câu truy vấn phức tạp.
2.  **Tái sử dụng (Reusability):** Bạn có thể định nghĩa một CTE một lần và tham chiếu đến nó nhiều lần trong câu truy vấn chính.
3.  **Hỗ trợ truy vấn đệ quy (Recursive Queries):** Đây là khả năng độc đáo và mạnh mẽ nhất của CTE, cho phép bạn xử lý các cấu trúc dữ liệu dạng cây hoặc phân cấp (ví dụ: sơ đồ tổ chức công ty, danh mục sản phẩm lồng nhau).

**Cú pháp cơ bản:**
```sql
WITH ten_cte_1 AS (
    -- Câu lệnh SELECT để định nghĩa CTE thứ nhất
),
ten_cte_2 AS (
    -- Câu lệnh SELECT khác, có thể tham chiếu đến ten_cte_1
)
-- Câu lệnh SELECT/UPDATE/DELETE chính, có thể tham chiếu đến cả hai CTE
SELECT * FROM ten_cte_2;
```

---

**Ví dụ 1: CTE thông thường (Non-recursive)**

Hãy quay lại bài toán "tìm 2 nhân viên có lương cao nhất trong mỗi phòng ban". Ở phần trước, chúng ta đã dùng CTE để giải quyết. Bây giờ hãy phân tích kỹ hơn.

```sql
-- Bước 1: Tạo CTE để xếp hạng nhân viên
WITH RankedEmployees AS (
    SELECT
        name,
        department,
        salary,
        ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as rn
    FROM employees
)
-- Bước 2: Sử dụng CTE đó để lọc ra kết quả cuối cùng
SELECT name, department, salary
FROM RankedEmployees
WHERE rn <= 2;
```
*   **Không dùng CTE:** Bạn sẽ phải viết một truy vấn con lồng nhau, rất khó đọc:
    ```sql
    SELECT name, department, salary
    FROM (
        SELECT
            name,
            department,
            salary,
            ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) as rn
        FROM employees
    ) AS Subquery
    WHERE rn <= 2;
    ```
*   Rõ ràng, phiên bản dùng CTE sạch sẽ và dễ hiểu hơn nhiều.

---

**Ví dụ 2: CTE Đệ quy (Recursive CTE)**

Đây là phần nâng cao nhưng cực kỳ hữu ích. Một CTE đệ quy là một CTE tự tham chiếu đến chính nó. Nó phải có cấu trúc gồm 2 phần được nối với nhau bởi `UNION ALL`.

1.  **Anchor Member (Thành viên neo):** Một câu lệnh `SELECT` không đệ quy, định nghĩa tập kết quả ban đầu (điểm bắt đầu của đệ quy).
2.  **Recursive Member (Thành viên đệ quy):** Một câu lệnh `SELECT` tham chiếu lại đến chính CTE đó, và `JOIN` với một bảng khác để tạo ra tập kết quả tiếp theo.

**Bài toán kinh điển:** Hiển thị cây phân cấp nhân viên. Giả sử ta có bảng `staff` với các cột: `staff_id`, `staff_name`, `manager_id` (tham chiếu đến `staff_id` của người quản lý).

| staff_id | staff_name | manager_id |
| -------- | ---------- | ---------- |
| 1        | CEO        | NULL       |
| 2        | Giám đốc A | 1          |
| 3        | Giám đốc B | 1          |
| 4        | Trưởng P. A1 | 2        |
| 5        | Nhân viên A1.1 | 4      |

```sql
WITH RECURSIVE EmployeeHierarchy AS (
    -- 1. Anchor Member: Tìm điểm gốc (CEO, người không có quản lý)
    SELECT
        staff_id,
        staff_name,
        manager_id,
        0 AS level -- Thêm một cột để biết cấp bậc
    FROM staff
    WHERE manager_id IS NULL

    UNION ALL

    -- 2. Recursive Member: Tìm nhân viên cấp dưới
    SELECT
        s.staff_id,
        s.staff_name,
        s.manager_id,
        eh.level + 1 -- Tăng cấp bậc lên 1
    FROM staff AS s
    -- JOIN với chính CTE để tìm nhân viên mà manager_id của họ
    -- bằng với staff_id của cấp trên (kết quả từ vòng lặp trước)
    INNER JOIN EmployeeHierarchy AS eh ON s.manager_id = eh.staff_id
)
-- Truy vấn cuối cùng để hiển thị kết quả
SELECT * FROM EmployeeHierarchy;
```
*   **Cách hoạt động:**
    1.  **Vòng 0:** Anchor member chạy, tìm ra CEO (`staff_id = 1`, `level = 0`). Kết quả tạm thời là `{(1, 'CEO', NULL, 0)}`.
    2.  **Vòng 1:** Recursive member chạy, nó `JOIN` bảng `staff` với kết quả tạm thời ở trên (`eh.staff_id = 1`). Nó tìm ra những người có `manager_id = 1` (Giám đốc A và B). Kết quả tạm thời được thêm vào: `{(2, 'GĐ A', 1, 1), (3, 'GĐ B', 1, 1)}`.
    3.  **Vòng 2:** Recursive member lại chạy, `JOIN` bảng `staff` với kết quả mới nhất (`eh.staff_id IN (2, 3)`). Nó tìm ra Trưởng phòng A1. Kết quả tạm thời được thêm vào: `{(4, 'TP A1', 2, 2)}`.
    4.  Quá trình này tiếp tục cho đến khi Recursive Member không tìm thêm được hàng nào nữa.
*   **Kết quả:**

    | staff_id | staff_name   | manager_id | level |
    | -------- | ------------ | ---------- | ----- |
    | 1        | CEO          | NULL       | 0     |
    | 2        | Giám đốc A   | 1          | 1     |
    | 3        | Giám đốc B   | 1          | 1     |
    | 4        | Trưởng P. A1 | 2          | 2     |
    | 5        | Nhân viên A1.1 | 4        | 3     |

---

**Bài tập nhỏ**

Sử dụng CTE, viết một truy vấn để tìm tất cả các phòng ban (`departments`) có tổng lương (`salary` từ bảng `employees`) cao hơn mức lương trung bình của tất cả các phòng ban.

**Giải pháp**

```sql
-- Bước 1: CTE để tính tổng lương cho mỗi phòng ban
WITH DepartmentSalaries AS (
    SELECT
        department_id,
        SUM(salary) AS total_salary
    FROM employees
    GROUP BY department_id
),
-- Bước 2: CTE để tính lương trung bình của tất cả các phòng ban
AverageSalary AS (
    SELECT AVG(total_salary) AS avg_total_salary
    FROM DepartmentSalaries
)
-- Bước 3: Truy vấn chính để so sánh và lấy kết quả
SELECT d.department_id, ds.total_salary
FROM DepartmentSalaries AS ds
JOIN AverageSalary AS avgs ON ds.total_salary > avgs.avg_total_salary;
```
*   **Phân tích:** Chúng ta đã chia bài toán thành các bước logic rõ ràng. CTE `DepartmentSalaries` tính toán trước tổng lương, sau đó CTE `AverageSalary` sử dụng kết quả đó để tính trung bình. Cuối cùng, truy vấn chính thực hiện việc so sánh.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Lợi ích chính của việc sử dụng CTE so với việc dùng subquery lồng nhau (derived tables) là gì?"
    *   **Trả lời:** Lợi ích chính là **tính dễ đọc và bảo trì**. CTE cho phép chúng ta đặt tên cho các khối logic, giống như đặt tên cho các hàm trong lập trình, làm cho các truy vấn phức tạp trở nên dễ hiểu hơn nhiều. Ngoài ra, CTE có thể được tham chiếu nhiều lần trong cùng một truy vấn, trong khi một derived table thì không. Và quan trọng nhất, chỉ CTE mới hỗ trợ các truy vấn đệ quy.

*   **Câu hỏi:** "CTE có cải thiện hiệu năng không?"
    *   **Trả lời:** Không hẳn. Trong hầu hết các trường hợp, một CTE thông thường chỉ là một "syntactic sugar" - một cách viết khác cho subquery. Bộ tối ưu hóa truy vấn thường sẽ tạo ra cùng một kế hoạch thực thi cho cả hai. Do đó, CTE chủ yếu cải thiện khả năng đọc của code chứ không phải hiệu năng thực thi. Tuy nhiên, trong một số trường hợp phức tạp, việc chia nhỏ logic có thể giúp bộ tối ưu hóa tìm ra một kế hoạch tốt hơn, nhưng đây không phải là mục đích chính.

*   **Câu hỏi:** "Giải thích cách hoạt động của một CTE đệ quy."
    *   **Trả lời:** Một CTE đệ quy hoạt động bằng cách bắt đầu với một tập kết quả gốc (Anchor Member), sau đó lặp đi lặp lại việc kết hợp (thường là `JOIN`) tập kết quả hiện tại với dữ liệu gốc để tạo ra tập kết quả mới cho vòng lặp tiếp theo (Recursive Member). Quá trình này dừng lại khi vòng lặp không tạo ra thêm bất kỳ hàng mới nào. Nó rất hữu ích để duyệt qua các cấu trúc dữ liệu phân cấp như sơ đồ tổ chức hoặc cây danh mục.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Sử dụng CTE bất cứ khi nào bạn có một truy vấn con phức tạp hoặc cần tham chiếu đến cùng một tập kết quả trung gian nhiều lần.
*   **Thực tiễn tốt nhất:** Khi viết CTE đệ quy, hãy luôn có một điều kiện dừng trong Recursive Member (thường là `JOIN` không tìm thấy hàng mới) để tránh vòng lặp vô hạn. Một số hệ CSDL cung cấp tùy chọn `MAXRECURSION` để phòng ngừa.
*   **Sai lầm thường gặp:** Cho rằng CTE là một bảng tạm được "materialized" (lưu vật lý). Hầu hết các CSDL sẽ thực thi CTE như một macro, tức là "dán" code của CTE vào nơi nó được gọi. Nó không được tính toán một lần và lưu lại (trừ một số trường hợp đặc biệt).
*   **Sai lầm thường gặp:** Đặt các câu lệnh khác giữa các định nghĩa CTE hoặc giữa CTE và câu lệnh chính. Toàn bộ khối `WITH ... SELECT` phải là một câu lệnh SQL duy nhất.

#### **4. Views (Bảng ảo)**

**Lý thuyết**

**View** là một **câu lệnh `SELECT` đã được lưu trữ** trong CSDL và được đặt cho một cái tên, hoạt động giống như một **bảng ảo (virtual table)**.

*   **Tương tự:** Hãy tưởng tượng bạn là một nhà phân tích kinh doanh và bạn thường xuyên phải chạy một câu lệnh `JOIN` 5 bảng rất phức tạp để lấy ra báo cáo doanh thu theo vùng miền. Thay vì gõ lại hoặc sao chép/dán câu lệnh dài ngoằng đó mỗi ngày, bạn có thể lưu nó lại dưới một cái tên đơn giản như `BaoCaoDoanhThuVungMien`. Từ đó về sau, bạn chỉ cần chạy `SELECT * FROM BaoCaoDoanhThuVungMien;`.
*   Bản thân View không lưu trữ dữ liệu. Mỗi khi bạn truy vấn một View, CSDL sẽ thực thi lại câu lệnh `SELECT` gốc đã định nghĩa nên nó để tạo ra kết quả.

**Lợi ích của việc sử dụng View:**

1.  **Đơn giản hóa (Simplicity):** Che giấu sự phức tạp của các câu lệnh `JOIN` và logic tính toán. Người dùng cuối chỉ cần tương tác với một "bảng" đơn giản.
2.  **Bảo mật (Security):** Bạn có thể tạo View để chỉ hiển thị một số cột hoặc hàng nhất định từ các bảng gốc. Thay vì cấp quyền cho người dùng truy cập trực tiếp vào các bảng nhạy cảm (như `employees` có cột `salary`), bạn có thể cấp cho họ quyền `SELECT` trên một View chỉ hiển thị `employee_name` và `department`.
3.  **Tính nhất quán (Consistency):** Đảm bảo rằng mọi người đều sử dụng cùng một logic nghiệp vụ phức tạp. Nếu logic tính toán doanh thu thay đổi, bạn chỉ cần cập nhật định nghĩa của View ở một nơi, và tất cả các báo cáo sử dụng View đó sẽ tự động được cập nhật.
4.  **Độc lập logic-vật lý (Logical-Physical Independence):** Nếu bạn tái cấu trúc (refactor) các bảng cơ sở (ví dụ, tách một bảng thành hai), bạn có thể giữ nguyên View cũ. Ứng dụng client vẫn truy vấn View đó mà không cần biết rằng cấu trúc vật lý bên dưới đã thay đổi.

**Cú pháp:**
```sql
-- Tạo một View
CREATE VIEW ViewName AS
SELECT column1, column2
FROM table_name
WHERE condition;

-- Sử dụng View
SELECT * FROM ViewName;

-- Xóa một View
DROP VIEW ViewName;
```

---

**Ví dụ**

Giả sử chúng ta có các bảng `employees`, `departments`, và `salaries`. Chúng ta muốn tạo một View để hiển thị thông tin tổng hợp về nhân viên mà không để lộ mức lương cụ thể, thay vào đó là bậc lương (cao, trung bình, thấp).

```sql
CREATE VIEW EmployeeSummary AS
SELECT
    e.employee_id,
    e.full_name,
    d.department_name,
    CASE
        WHEN s.amount > 100000 THEN 'Cao'
        WHEN s.amount > 60000 THEN 'Trung bình'
        ELSE 'Thấp'
    END AS salary_level
FROM
    employees AS e
JOIN
    departments AS d ON e.department_id = d.department_id
JOIN
    salaries AS s ON e.employee_id = s.employee_id
WHERE
    e.is_active = TRUE;
```
*   **Sử dụng:** Bây giờ, thay vì viết lại toàn bộ câu lệnh trên, phòng nhân sự hoặc các nhà phân tích chỉ cần chạy:
    ```sql
    SELECT * FROM EmployeeSummary WHERE department_name = 'Kỹ thuật';
    ```
*   Họ không thể thấy được cột `salary` gốc. Họ cũng không cần quan tâm đến logic `JOIN` phức tạp.

---

**Bài tập nhỏ**

Dựa trên các bảng `posts` và `comments` (một bài viết có nhiều bình luận), hãy tạo một View có tên `PostCommentCounts` để hiển thị `post_id`, `post_title` và một cột mới là `number_of_comments` (tổng số bình luận của bài viết đó).

**Giải pháp**

```sql
CREATE VIEW PostCommentCounts AS
SELECT
    p.post_id,
    p.title AS post_title,
    COUNT(c.comment_id) AS number_of_comments
FROM
    posts AS p
LEFT JOIN -- Dùng LEFT JOIN để hiển thị cả những bài chưa có comment
    comments AS c ON p.post_id = c.post_id
GROUP BY
    p.post_id, p.title;
```
*   **Sử dụng:** `SELECT * FROM PostCommentCounts ORDER BY number_of_comments DESC;`

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt giữa View và CTE là gì?"
    *   **Trả lời:**
        *   **Vòng đời (Lifecycle):** View là một đối tượng CSDL vĩnh viễn, nó được lưu trữ trong schema cho đến khi bị `DROP`. CTE chỉ tồn tại trong phạm vi của một câu lệnh SQL duy nhất mà nó được định nghĩa.
        *   **Mục đích:** View được tạo ra để tái sử dụng và kiểm soát truy cập một cách lâu dài. CTE được dùng để chia nhỏ và làm rõ logic của một câu lệnh phức tạp tại thời điểm viết.
        *   **Lưu trữ:** Cả hai đều không lưu trữ dữ liệu (trừ trường hợp đặc biệt là Materialized View).

*   **Câu hỏi:** "View có thể làm chậm hiệu năng không? Tại sao?"
    *   **Trả lời:** Có, View có thể làm chậm hiệu năng. Vì View chỉ là một câu lệnh `SELECT` được lưu lại, nên nếu câu lệnh đó vốn đã không hiệu quả (ví dụ: `JOIN` nhiều bảng lớn không có index), thì việc truy vấn View đó cũng sẽ không hiệu quả. Đặc biệt, việc lồng nhiều View vào nhau (một View truy vấn một View khác) có thể khiến bộ tối ưu hóa của CSDL khó tạo ra một kế hoạch thực thi tốt.

*   **Câu hỏi:** "Materialized View là gì và nó khác gì so với View thông thường?"
    *   **Trả lời:** Đây là một câu hỏi nâng cao. Khác với View thông thường (bảng ảo), **Materialized View** là một View mà kết quả của nó được **lưu trữ vật lý** trên đĩa, giống như một bảng thực sự. Nó không chạy lại câu `SELECT` gốc mỗi lần được truy vấn. Thay vào đó, nó trả về dữ liệu đã được tính toán trước. Điều này làm cho việc đọc dữ liệu từ Materialized View cực kỳ nhanh. Cái giá phải trả là dữ liệu có thể bị lỗi thời (stale) và cần một cơ chế để "làm mới" (refresh) định kỳ. Nó thường được sử dụng trong các hệ thống kho dữ liệu (data warehouse).

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Sử dụng View để cung cấp một API ổn định cho tầng ứng dụng. Ứng dụng tương tác với View, còn bạn có thể tự do thay đổi cấu trúc bảng bên dưới mà không làm ảnh hưởng đến ứng dụng.
*   **Thực tiễn tốt nhất:** Giữ cho logic của View tương đối đơn giản. Tránh lồng quá nhiều View vào nhau. Nếu một View trở nên quá phức tạp, hãy cân nhắc xem đó có phải là dấu hiệu của một thiết kế CSDL tồi hay không.
*   **Sai lầm thường gặp:** Cho rằng View luôn nhanh. Hiệu năng của View phụ thuộc hoàn toàn vào hiệu năng của câu lệnh `SELECT` định nghĩa nên nó. Luôn chạy `EXPLAIN` trên các truy vấn vào View để kiểm tra hiệu năng.
*   **Sai lầm thường gặp:** Cố gắng `INSERT`, `UPDATE`, `DELETE` trên các View phức tạp. Mặc dù một số View đơn giản (thường là chỉ dựa trên một bảng) có thể được cập nhật, nhưng các View có `JOIN`, `GROUP BY`, hàm tổng hợp... thì thường không thể. Việc này có thể dẫn đến các hành vi không mong muốn và lỗi.

---

#### **5. Stored Procedures, Functions, Triggers (Khi nào KHÔNG nên dùng)**

**Lý thuyết**

Đây là các khối mã lệnh (thường viết bằng ngôn ngữ thủ tục của CSDL như PL/pgSQL, T-SQL) được lưu trữ và thực thi ngay tại CSDL server. Chúng cho phép bạn đóng gói logic nghiệp vụ phức tạp vào tầng CSDL.

*   **Stored Procedure (Thủ tục lưu trữ):**
    *   **Là gì:** Một tập hợp các câu lệnh SQL và logic điều khiển được đặt tên và lưu lại. Nó có thể nhận tham số đầu vào, đầu ra và thực hiện các thao tác phức tạp (thậm chí là các giao dịch bao gồm nhiều `INSERT`, `UPDATE`, `DELETE`).
    *   **Tương tự:** Một "hàm" API ngay trong CSDL. Ứng dụng chỉ cần gọi tên thủ tục và truyền tham số, thay vì phải gửi một loạt các câu lệnh SQL.

*   **Function (Hàm do người dùng định nghĩa - UDF):**
    *   **Là gì:** Tương tự như Stored Procedure nhưng có một khác biệt chính: **luôn phải trả về một giá trị duy nhất** (hoặc một bảng).
    *   **Tương tự:** Một hàm thuần túy trong lập trình. Nó thường được dùng bên trong các câu lệnh `SELECT` để thực hiện các phép tính toán tùy chỉnh.

*   **Trigger (Trình kích hoạt):**
    *   **Là gì:** Một Stored Procedure đặc biệt, được tự động thực thi khi một sự kiện DML (`INSERT`, `UPDATE`, `DELETE`) xảy ra trên một bảng cụ thể.
    *   **Tương tự:** Một "event listener". Ví dụ: "Mỗi khi một hàng mới được chèn vào bảng `orders`, hãy tự động cập nhật số lượng tồn kho trong bảng `products`."

**Ưu điểm:**
*   **Giảm lưu lượng mạng:** Thay vì gửi nhiều câu lệnh SQL qua lại, ứng dụng chỉ cần gửi một lệnh `CALL` duy nhất.
*   **Tái sử dụng và đóng gói logic:** Logic nghiệp vụ được định nghĩa một lần và có thể được gọi bởi nhiều ứng dụng khác nhau.
*   **Bảo mật:** Bạn có thể cấp quyền `EXECUTE` trên một Stored Procedure mà không cần cấp quyền trên các bảng cơ sở mà nó thao tác.
*   **Hiệu năng (đôi khi):** Kế hoạch thực thi có thể được CSDL cache lại, giúp các lần gọi sau nhanh hơn.

**Nhược điểm và KHI KHÔNG NÊN DÙNG:**
Đây là phần quan trọng nhất trong thực tế hiện đại.

1.  **Khó bảo trì và gỡ lỗi:** Viết và debug code PL/SQL thường khó hơn nhiều so với các ngôn ngữ lập trình hiện đại (Python, Java, Go...). Các công cụ hỗ trợ cũng kém hơn.
2.  **Khó kiểm soát phiên bản (Version Control):** Việc đưa code CSDL vào các hệ thống như Git và quản lý các thay đổi, di chuyển (migration) phức tạp hơn nhiều so với code ứng dụng.
3.  **Khó mở rộng quy mô (Scalability):** Logic nghiệp vụ đặt trong CSDL sẽ tiêu tốn tài nguyên (CPU, memory) của CSDL server. CSDL thường là thành phần khó mở rộng quy mô nhất trong một hệ thống. Việc đẩy logic tính toán nặng về phía các server ứng dụng (application server) - vốn dễ dàng mở rộng theo chiều ngang - thường là một kiến trúc tốt hơn.
4.  **Phụ thuộc vào nhà cung cấp (Vendor Lock-in):** Code T-SQL của SQL Server sẽ không chạy được trên PostgreSQL (PL/pgSQL) và ngược lại. Nếu bạn muốn đổi CSDL, bạn sẽ phải viết lại toàn bộ logic này.
5.  **"Logic ma thuật" (Magic/Hidden Logic):** Đặc biệt là với `Trigger`. Một lập trình viên mới nhìn vào code ứng dụng sẽ không thể hiểu tại sao khi `INSERT` một bản ghi thì một bảng khác lại tự động thay đổi. Trigger làm cho luồng dữ liệu trở nên khó đoán và khó theo dõi.

**Kết luận thực tế:**
Trong phát triển ứng dụng hiện đại, xu hướng là **giữ cho CSDL "câm" (dumb database)**. Tức là CSDL chỉ nên tập trung vào việc lưu trữ và truy xuất dữ liệu một cách hiệu quả. Hầu hết (hoặc toàn bộ) logic nghiệp vụ nên được xử lý ở **tầng ứng dụng**.

**Chỉ nên sử dụng chúng khi:**
*   Logic đó thực sự, thực sự phải được thực thi ngay tại CSDL để đảm bảo tính toàn vẹn dữ liệu tuyệt đối mà không thể tin tưởng vào client. (Ví dụ: một Trigger để cập nhật cột `updated_at`).
*   Đối với các công việc của DBA (Quản trị CSDL) hoặc các tác vụ ETL (trích xuất, chuyển đổi, tải dữ liệu) nội bộ.
*   Khi hiệu năng mạng là một vấn đề cực kỳ nghiêm trọng và một Stored Procedure có thể giảm đáng kể số lần round-trip.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt chính giữa Stored Procedure và Function là gì?"
    *   **Trả lời:** Sự khác biệt chính là Function bắt buộc phải trả về một giá trị và thường được sử dụng bên trong một câu lệnh `SELECT` như một phần của biểu thức. Stored Procedure không bắt buộc phải trả về giá trị, nó có thể thực hiện một chuỗi các hành động và thường được gọi như một câu lệnh độc lập (`CALL` hoặc `EXECUTE`).

*   **Câu hỏi:** "Tại sao Trigger lại bị coi là nguy hiểm hoặc là một 'anti-pattern' trong nhiều trường hợp?"
    *   **Trả lời:** Trigger bị coi là nguy hiểm vì chúng tạo ra các "tác dụng phụ" ẩn. Logic thực thi một cách tự động và không tường minh trong code ứng dụng. Điều này làm cho hệ thống rất khó để lý giải và gỡ lỗi. Một thay đổi nhỏ có thể gây ra một chuỗi các trigger kích hoạt lẫn nhau (cascading triggers), dẫn đến các vấn đề về hiệu năng khó lường và thậm chí là deadlock.

*   **Câu hỏi:** "Trong kiến trúc microservices, tại sao việc đặt logic nghiệp vụ vào Stored Procedures lại là một ý tưởng tồi?"
    *   **Trả lời:** Kiến trúc microservices khuyến khích mỗi service sở hữu và quản lý dữ liệu của riêng mình. Nếu nhiều service cùng gọi vào một Stored Procedure trong một CSDL dùng chung, nó sẽ tạo ra một điểm **khớp nối chặt (tight coupling)** ngay tại tầng dữ liệu, đi ngược lại hoàn toàn với nguyên tắc của microservices. Nó phá vỡ sự độc lập và khả năng triển khai riêng lẻ của các service.

#### **6. Transactions, ACID, Rollback**

**Lý thuyết**

**Transaction (Giao dịch)** là một **đơn vị công việc logic** bao gồm một hoặc nhiều câu lệnh SQL. Điểm mấu chốt của transaction là nó tuân thủ nguyên tắc **"hoặc tất cả, hoặc không có gì" (all-or-nothing)**.

*   **Tương tự trong đời thực:** Giao dịch chuyển tiền từ tài khoản A sang tài khoản B. Công việc này bao gồm hai bước không thể tách rời:
    1.  Trừ tiền từ tài khoản A.
    2.  Cộng tiền vào tài khoản B.
*   Hãy tưởng tượng hệ thống trừ tiền của A xong thì bị sập nguồn. Nếu không có transaction, tiền của A sẽ bị mất và B không bao giờ nhận được. Một transaction sẽ bao bọc cả hai hành động này lại, đảm bảo rằng nếu bước 2 thất bại, bước 1 cũng sẽ được **hủy bỏ (rollback)**, đưa tài khoản A trở về trạng thái ban đầu.

**Các lệnh điều khiển Transaction (TCL):**

*   **`BEGIN TRANSACTION` (hoặc `START TRANSACTION`):** Đánh dấu điểm bắt đầu của một giao dịch.
*   **`COMMIT`:** Lưu vĩnh viễn tất cả các thay đổi được thực hiện bên trong transaction vào CSDL. Giao dịch kết thúc thành công.
*   **`ROLLBACK`:** Hủy bỏ tất cả các thay đổi đã được thực hiện bên trong transaction kể từ điểm `BEGIN`. Đưa CSDL trở về trạng thái như trước khi giao dịch bắt đầu. Giao dịch kết thúc thất bại.
*   **`SAVEPOINT name`:** Tạo một "điểm lưu" bên trong một transaction.
*   **`ROLLBACK TO SAVEPOINT name`:** Hủy bỏ các thay đổi về lại một `SAVEPOINT` đã tạo trước đó, nhưng không hủy toàn bộ transaction.

**ACID - "Lời thề" của các hệ CSDL quan hệ**

ACID là một bộ bốn thuộc tính đảm bảo rằng các transaction được xử lý một cách đáng tin cậy.

1.  **Atomicity (Tính Nguyên tử):**
    *   Đảm bảo nguyên tắc "hoặc tất cả, hoặc không có gì". Một transaction là một đơn vị không thể chia nhỏ. Nó hoặc là `COMMIT` thành công 100%, hoặc là `ROLLBACK` hoàn toàn.
    *   **Ví dụ:** Giao dịch chuyển tiền.

2.  **Consistency (Tính Nhất quán):**
    *   Đảm bảo rằng một transaction chỉ có thể đưa CSDL từ một trạng thái hợp lệ này sang một trạng thái hợp lệ khác. Mọi ràng buộc (constraints) của CSDL phải được tuân thủ.
    *   **Ví dụ:** Nếu có ràng buộc `balance >= 0` trên tài khoản ngân hàng, một transaction cố gắng trừ tiền khiến `balance` thành số âm sẽ bị thất bại và `ROLLBACK`, đảm bảo CSDL luôn nhất quán.

3.  **Isolation (Tính Cô lập):**
    *   Đảm bảo rằng các transaction diễn ra đồng thời không ảnh hưởng lẫn nhau. Từ góc nhìn của một transaction, nó sẽ cảm thấy như thể nó là transaction duy nhất đang chạy trên hệ thống.
    *   **Ví dụ:** Trong khi giao dịch chuyển tiền từ A sang B đang diễn ra (chưa `COMMIT`), một transaction khác cố gắng đọc số dư của A và B sẽ thấy được số dư *trước khi* giao dịch bắt đầu, hoặc phải chờ đến *sau khi* giao dịch kết thúc, chứ không phải một trạng thái lửng lơ ở giữa. (Mức độ cô lập này có thể được điều chỉnh, sẽ học ở phần sau).

4.  **Durability (Tính Bền vững):**
    *   Đảm bảo rằng một khi transaction đã được `COMMIT`, các thay đổi của nó sẽ được lưu vĩnh viễn và sẽ không bị mất, ngay cả khi hệ thống gặp sự cố (mất điện, crash server...). Điều này thường được thực hiện bằng cách ghi vào các tệp nhật ký giao dịch (transaction logs) trên đĩa trước khi thực sự cập nhật dữ liệu.

---

**Ví dụ SQL**

Hãy mô phỏng lại giao dịch chuyển tiền. Giả sử bảng `accounts` có các cột `account_id` và `balance`.

```sql
START TRANSACTION;

-- Lấy số dư hiện tại của tài khoản người gửi (A) và người nhận (B)
-- Trong ứng dụng thực tế, bạn sẽ SELECT và lưu vào biến
-- SELECT balance FROM accounts WHERE account_id = 'A'; -- Giả sử là 1000
-- SELECT balance FROM accounts WHERE account_id = 'B'; -- Giả sử là 500

-- Giả định số tiền chuyển là 200

-- Bước 1: Trừ tiền từ tài khoản A
UPDATE accounts SET balance = balance - 200 WHERE account_id = 'A';

-- Bước 2: Cộng tiền vào tài khoản B
UPDATE accounts SET balance = balance + 200 WHERE account_id = 'B';

-- Kiểm tra lại điều kiện (ví dụ: số dư của A không được âm)
-- IF (SELECT balance FROM accounts WHERE account_id = 'A') < 0 THEN
--     -- Nếu có lỗi, hủy bỏ tất cả
--     ROLLBACK;
-- ELSE
--     -- Nếu mọi thứ ổn, xác nhận giao dịch
--     COMMIT;
-- END IF;
```
*   **Cách hoạt động:** Nếu có bất kỳ lỗi nào xảy ra giữa `START TRANSACTION` và `COMMIT` (lỗi mạng, lỗi logic, server crash), toàn bộ transaction sẽ tự động được `ROLLBACK` khi CSDL khởi động lại, đảm bảo tiền không bị "lơ lửng". Nếu code ứng dụng phát hiện ra một lỗi logic (như số dư không đủ), nó có thể chủ động gọi `ROLLBACK`.

---

**Bài tập nhỏ**

Bạn đang viết một kịch bản để đăng ký một sinh viên mới vào một khóa học. Việc này bao gồm 2 bước:
1.  Chèn một bản ghi mới vào bảng `registrations` (student_id, course_id).
2.  Cập nhật bảng `courses` bằng cách giảm số lượng chỗ trống (`slots_available`) đi 1.

Hãy viết đoạn mã SQL (dạng giả code) sử dụng transaction để đảm bảo cả hai bước này xảy ra một cách an toàn. Cần xử lý trường hợp không còn chỗ trống (`slots_available <= 0`).

**Giải pháp**

```sql
START TRANSACTION;

-- Lấy số chỗ còn lại
-- available_slots = SELECT slots_available FROM courses WHERE course_id = ?;

IF available_slots > 0 THEN
    -- Bước 1: Giảm số chỗ trống
    UPDATE courses SET slots_available = slots_available - 1 WHERE course_id = ?;

    -- Bước 2: Thêm bản ghi đăng ký
    INSERT INTO registrations (student_id, course_id) VALUES (?, ?);

    -- Nếu cả hai lệnh thành công, xác nhận
    COMMIT;
ELSE
    -- Nếu không còn chỗ, hủy bỏ
    -- (thực tế không có thay đổi nào được thực hiện, nhưng rollback là một thói quen tốt)
    ROLLBACK;
    -- Báo lỗi cho người dùng: "Khóa học đã hết chỗ"
END IF;
```

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Giải thích 4 thuộc tính ACID bằng một ví dụ thực tế."
    *   **Trả lời:** Hãy dùng ví dụ đặt vé xem phim. Bạn chọn 2 ghế và bấm thanh toán.
        *   **Atomicity:** Hoặc là bạn nhận được vé VÀ 2 ghế đó được đánh dấu là "đã bán", hoặc là giao dịch thanh toán của bạn thất bại VÀ 2 ghế đó vẫn ở trạng thái "còn trống". Không có trường hợp lơ lửng.
        *   **Consistency:** Hệ thống không cho phép bạn đặt một ghế đã được bán. Giao dịch của bạn phải tuân thủ quy tắc này.
        *   **Isolation:** Nếu bạn và một người khác cùng lúc bấm thanh toán cho cùng một ghế, tính cô lập đảm bảo rằng chỉ một trong hai giao dịch được thành công. Giao dịch còn lại sẽ thất bại.
        *   **Durability:** Một khi bạn đã nhận được email xác nhận vé, thông tin đặt vé của bạn đã được lưu an toàn. Kể cả rạp chiếu phim có mất điện ngay sau đó, vé của bạn vẫn hợp lệ.

*   **Câu hỏi:** "`COMMIT` và `ROLLBACK` làm gì?"
    *   **Trả lời:** `COMMIT` là lệnh để lưu vĩnh viễn các thay đổi của một transaction. `ROLLBACK` là lệnh để hủy bỏ tất cả các thay đổi đã thực hiện trong transaction đó, đưa CSDL về lại trạng thái trước khi transaction bắt đầu.

*   **Câu hỏi:** "Điều gì sẽ xảy ra với một transaction đang mở nếu kết nối tới CSDL bị ngắt đột ngột?"
    *   **Trả lời:** Hầu hết các hệ CSDL sẽ tự động thực hiện `ROLLBACK` cho bất kỳ transaction nào đang mở mà chưa được `COMMIT` khi kết nối của session đó bị ngắt. Đây là một cơ chế an toàn để đảm bảo không có các giao dịch "mồ côi" hay "lơ lửng".

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Giữ cho các transaction càng ngắn gọn và nhanh chóng càng tốt. Một transaction kéo dài sẽ giữ "khóa" (lock) trên các tài nguyên (hàng, bảng) lâu hơn, làm tăng khả năng xảy ra tranh chấp và giảm hiệu năng chung của hệ thống.
*   **Thực tiễn tốt nhất:** Không bao giờ để một transaction ở trạng thái mở trong khi chờ đợi tương tác từ người dùng (ví dụ: chờ người dùng nhập input). Hãy thu thập tất cả thông tin cần thiết trước, sau đó mới bắt đầu và kết thúc transaction một cách nhanh chóng.
*   **Sai lầm thường gặp:** **Tự động commit (Autocommit).** Hầu hết các client SQL mặc định ở chế độ `autocommit = ON`, nghĩa là mỗi câu lệnh SQL bạn gõ sẽ tự động được bọc trong một transaction và `COMMIT` ngay lập tức. Điều này rất tiện lợi cho các truy vấn đơn giản, nhưng lại rất nguy hiểm khi bạn cần thực hiện nhiều thao tác phụ thuộc lẫn nhau. Hãy luôn nhớ tắt `autocommit` hoặc dùng `START TRANSACTION` một cách tường minh khi cần.
*   **Sai lầm thường gặp:** Thực hiện các thao tác DDL (như `CREATE TABLE`, `ALTER TABLE`) bên trong một transaction. Mặc dù một số CSDL hỗ trợ điều này, nhiều CSDL khác (như MySQL) sẽ thực hiện một `COMMIT` ngầm trước khi chạy lệnh DDL, có thể phá vỡ logic transaction của bạn.

---

#### **7. Isolation Levels & Các Vấn đề về Đồng thời**

**Lý thuyết**

**Isolation (Tính Cô lập)** trong ACID không phải là một khái niệm tuyệt đối "có hoặc không". Tiêu chuẩn SQL định nghĩa 4 **mức độ cô lập (Isolation Levels)**, cho phép bạn đánh đổi giữa **tính nhất quán** và **hiệu năng**. Mức cô lập càng cao, dữ liệu càng an toàn và nhất quán, nhưng hiệu năng càng giảm do CSDL phải sử dụng nhiều cơ chế khóa (locking) hơn.

**Các vấn đề về đồng thời (Concurrency Phenomena):**
Khi nhiều transaction chạy cùng lúc, các vấn đề sau có thể xảy ra nếu mức cô lập không đủ cao:

1.  **Dirty Read (Đọc bẩn):**
    *   Transaction A sửa đổi một hàng nhưng **chưa `COMMIT`**. Transaction B đọc hàng đó và thấy được giá trị **chưa được commit** của A. Nếu sau đó A bị `ROLLBACK`, thì B đã đọc phải dữ liệu "rác" không bao giờ tồn tại.
    *   **Ví dụ:** A cập nhật giá sản phẩm lên 120 (chưa commit). B đọc giá 120 và xử lý đơn hàng. A bị lỗi và rollback về giá 100. B đã bán hàng với giá sai.

2.  **Non-Repeatable Read (Đọc không lặp lại):**
    *   Transaction A đọc một hàng. Transaction B sau đó **cập nhật hoặc xóa** hàng đó và `COMMIT`. Nếu Transaction A đọc lại hàng đó lần nữa, nó sẽ thấy một giá trị khác hoặc hàng đó đã biến mất.
    *   **Ví dụ:** A đọc số dư tài khoản là 1000. B chuyển tiền vào và commit, số dư lên 1500. A đọc lại lần nữa và thấy số dư là 1500. Dữ liệu trong cùng một transaction của A đã bị thay đổi.

3.  **Phantom Read (Đọc bóng ma):**
    *   Transaction A chạy một truy vấn có điều kiện `WHERE` (ví dụ: `SELECT COUNT(*) FROM employees WHERE department = 'Sales'`), được kết quả là 20. Transaction B sau đó **chèn thêm** một nhân viên mới vào phòng 'Sales' và `COMMIT`. Nếu Transaction A chạy lại chính xác câu truy vấn `SELECT` đó, nó sẽ thấy kết quả là 21. Một "hàng ma" đã xuất hiện.

**Bốn mức độ cô lập:**

| Isolation Level          | Dirty Read | Non-Repeatable Read | Phantom Read | Hiệu năng |
| ------------------------ | ---------- | ------------------- | ------------ | --------- |
| **READ UNCOMMITTED**     | Cho phép   | Cho phép            | Cho phép     | Cao nhất  |
| **READ COMMITTED**       | Ngăn chặn  | Cho phép            | Cho phép     | Cao       |
| **REPEATABLE READ**      | Ngăn chặn  | Ngăn chặn           | Cho phép     | Trung bình|
| **SERIALIZABLE**         | Ngăn chặn  | Ngăn chặn           | Ngăn chặn    | Thấp nhất |

*   **READ UNCOMMITTED:** Gần như không được sử dụng trong thực tế vì quá nguy hiểm.
*   **READ COMMITTED:** **Mức mặc định** cho hầu hết các CSDL (PostgreSQL, SQL Server, Oracle). Đây là sự cân bằng tốt: bạn không bao giờ đọc phải dữ liệu chưa commit, nhưng dữ liệu có thể thay đổi trong suốt quá trình transaction của bạn.
*   **REPEATABLE READ:** **Mức mặc định** cho MySQL (InnoDB). Đảm bảo rằng nếu bạn đọc lại cùng một hàng, bạn sẽ luôn thấy dữ liệu giống nhau. Tuy nhiên, các hàng mới vẫn có thể được chèn vào.
*   **SERIALIZABLE:** Mức cao nhất. Đảm bảo các transaction chạy đồng thời sẽ có kết quả tương đương với việc chúng chạy tuần tự từng cái một. Nó loại bỏ mọi vấn đề về đồng thời nhưng phải trả giá bằng hiệu năng thấp nhất và khả năng xảy ra lỗi `serialization failure` cao.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Giải thích 3 vấn đề về đồng thời: Dirty Read, Non-Repeatable Read, và Phantom Read."
    *   **Trả lời:** (Sử dụng các định nghĩa và ví dụ đã nêu ở trên).
        *   **Dirty Read:** Đọc dữ liệu chưa commit.
        *   **Non-Repeatable Read:** Dữ liệu bạn đã đọc bị `UPDATE` hoặc `DELETE` bởi người khác.
        *   **Phantom Read:** Các hàng mới (`INSERT`) xuất hiện trong một truy vấn mà bạn chạy lại.

*   **Câu hỏi:** "Mức độ cô lập mặc định của PostgreSQL/MySQL là gì và nó giải quyết được những vấn đề nào?"
    *   **Trả lời:**
        *   **PostgreSQL:** Mặc định là `READ COMMITTED`. Nó ngăn chặn được Dirty Read, nhưng vẫn cho phép Non-Repeatable Read và Phantom Read.
        *   **MySQL (InnoDB):** Mặc định là `REPEATABLE READ`. Nó ngăn chặn được Dirty Read và Non-Repeatable Read, nhưng vẫn có thể gặp Phantom Read (mặc dù InnoDB sử dụng một cơ chế gọi là `next-key locking` để giảm thiểu vấn đề này).

*   **Câu hỏi:** "Khi nào bạn sẽ cần nâng mức độ cô lập lên `SERIALIZABLE`?"
    *   **Trả lời:** Bạn chỉ nên sử dụng `SERIALIZABLE` khi có một yêu cầu nghiệp vụ cực kỳ nghiêm ngặt, đòi hỏi một chuỗi các thao tác phải diễn ra trên một "snapshot" dữ liệu hoàn toàn không thay đổi. Ví dụ: một hệ thống báo cáo phức tạp phải đảm bảo rằng tất cả các số liệu được tính toán dựa trên cùng một bộ dữ liệu chính xác tại một thời điểm, không có bất kỳ sự thay đổi nào (kể cả `INSERT`) xen vào giữa các bước tính toán. Tuy nhiên, ứng dụng phải được thiết kế để có thể thử lại (retry) các transaction bị thất bại do `serialization failure`.