#### **1. Các Ràng buộc (Constraints): Primary Key, Foreign Key, Unique, Not Null, Default**

**Lý thuyết**

Khi thiết kế CSDL, chúng ta không chỉ tạo ra các bảng và cột; chúng ta còn phải đặt ra các "luật lệ" để đảm bảo tính toàn vẹn, chính xác và nhất quán của dữ liệu. Những luật lệ này được gọi là **Ràng buộc (Constraints)**.

*   **`PRIMARY KEY` (Khóa chính):**
    *   **Mục đích:** Là định danh duy nhất cho mỗi hàng trong một bảng.
    *   **Tương tự:** Giống như số Căn cước Công dân (CCCD) của mỗi người. Mỗi người chỉ có một số CCCD duy nhất và không ai được phép không có số này.
    *   **Đặc tính:**
        1.  Phải là **duy nhất** (`UNIQUE`).
        2.  Không được phép **rỗng** (`NOT NULL`).
        3.  Mỗi bảng chỉ nên có **một** khóa chính.
    *   Thường là một cột số nguyên tự động tăng (`AUTO_INCREMENT` hoặc `SERIAL`).

*   **`FOREIGN KEY` (Khóa ngoại):**
    *   **Mục đích:** Tạo một liên kết giữa hai bảng để đảm bảo tính toàn vẹn tham chiếu.
    *   **Tương tự:** Trong bảng `SinhVien` có cột `ma_lop`. Giá trị `ma_lop` này phải tồn tại trong bảng `LopHoc`. Khóa ngoại giống như một "tham chiếu" đảm bảo rằng không sinh viên nào có thể được gán vào một lớp học "ma".
    *   **Đặc tính:** Một cột (hoặc nhiều cột) trong một bảng tham chiếu đến `PRIMARY KEY` của một bảng khác.

*   **`UNIQUE` (Duy nhất):**
    *   **Mục đích:** Đảm bảo tất cả các giá trị trong một cột là khác nhau.
    *   **Tương tự:** Trong khi số CCCD là khóa chính, email hoặc số điện thoại cũng phải là duy nhất cho mỗi người. Một bảng có thể có nhiều ràng buộc `UNIQUE`.
    *   **Khác biệt với Khóa chính:** Cho phép một giá trị `NULL` (hầu hết các hệ CSDL).

*   **`NOT NULL` (Không rỗng):**
    *   **Mục đích:** Bắt buộc một cột phải có giá trị, không thể để trống.
    *   **Tương tự:** Khi bạn đăng ký một tài khoản, bạn bắt buộc phải nhập email và mật khẩu. Những trường đó không thể để trống.

*   **`DEFAULT` (Mặc định):**
    *   **Mục đích:** Cung cấp một giá trị mặc định cho cột khi không có giá trị nào được chỉ định lúc chèn dữ liệu.
    *   **Tương tự:** Khi một người dùng mới đăng ký, trạng thái tài khoản của họ có thể được đặt mặc định là `'pending_activation'`.

---

**Ví dụ SQL**

Hãy thiết kế hai bảng `faculties` (khoa) và `students` (sinh viên) áp dụng các ràng buộc này.

```sql
-- Bảng này phải được tạo trước vì bảng students sẽ tham chiếu đến nó.
CREATE TABLE faculties (
    faculty_id INT PRIMARY KEY AUTO_INCREMENT, -- Khóa chính, tự tăng
    faculty_name VARCHAR(100) NOT NULL UNIQUE -- Tên khoa phải có và là duy nhất
);

CREATE TABLE students (
    student_id INT PRIMARY KEY AUTO_INCREMENT, -- Khóa chính
    full_name VARCHAR(100) NOT NULL, -- Tên phải có
    email VARCHAR(100) NOT NULL UNIQUE, -- Email phải có và là duy nhất
    date_of_birth DATE,
    gpa DECIMAL(3, 2) DEFAULT 0.0, -- Điểm trung bình mặc định là 0.0
    faculty_id INT, -- Cột này sẽ là khóa ngoại

    -- Đặt tên rõ ràng cho các ràng buộc là một thực hành tốt
    CONSTRAINT fk_student_faculty
    FOREIGN KEY (faculty_id) REFERENCES faculties(faculty_id)
);
```
*   **Giải thích cách hoạt động:**
    *   Bạn không thể `INSERT` một `student` mà không có `full_name` hoặc `email`.
    *   Bạn không thể `INSERT` hai `student` có cùng `email`.
    *   Nếu bạn `INSERT` một `student` mà không cung cấp `gpa`, nó sẽ tự động nhận giá trị `0.0`.
    *   Quan trọng nhất: Bạn không thể `INSERT` một `student` với `faculty_id = 99` nếu không có khoa nào có `faculty_id = 99` trong bảng `faculties`. CSDL sẽ báo lỗi, đảm bảo dữ liệu luôn nhất quán.

---

**Bài tập nhỏ**

Bạn được yêu cầu thiết kế một bảng `products`. Bảng này cần các thông tin sau:
*   Mã sản phẩm (phải là định danh duy nhất).
*   Mã vạch sản phẩm (cũng phải là duy nhất).
*   Tên sản phẩm (bắt buộc phải có).
*   Số lượng tồn kho (mặc định là 0 khi thêm mới).
*   Trạng thái sản phẩm (ví dụ: 'active', 'discontinued'), mặc định là 'active'.

Hãy viết câu lệnh `CREATE TABLE` cho bảng `products` sử dụng các ràng buộc đã học.

**Giải pháp**

```sql
CREATE TABLE products (
    product_id INT PRIMARY KEY AUTO_INCREMENT,
    barcode VARCHAR(50) UNIQUE NOT NULL, -- Vừa duy nhất vừa bắt buộc
    product_name VARCHAR(255) NOT NULL,
    stock_quantity INT DEFAULT 0,
    status VARCHAR(20) DEFAULT 'active'
);
```

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Sự khác biệt chính giữa Khóa chính (`PRIMARY KEY`) và Ràng buộc duy nhất (`UNIQUE`) là gì?"
    *   **Trả lời:** Có ba điểm khác biệt chính:
        1.  **Số lượng:** Một bảng chỉ có thể có một Khóa chính, nhưng có thể có nhiều ràng buộc `UNIQUE`.
        2.  **Giá trị NULL:** Khóa chính không bao giờ chấp nhận giá trị `NULL`. Ràng buộc `UNIQUE` có thể chấp nhận một giá trị `NULL`.
        3.  **Mục đích:** Khóa chính được dùng làm định danh chính cho hàng, thường được các Khóa ngoại tham chiếu tới. `UNIQUE` chỉ đơn giản là để đảm bảo tính duy nhất của dữ liệu trong một cột không phải là định danh chính.

*   **Câu hỏi:** "Khái niệm 'Toàn vẹn tham chiếu' (Referential Integrity) là gì và Khóa ngoại giúp thực thi nó như thế nào?"
    *   **Trả lời:** Toàn vẹn tham chiếu là một quy tắc đảm bảo rằng các mối quan hệ giữa các bảng luôn nhất quán. Khóa ngoại thực thi điều này bằng cách ngăn chặn các hành động có thể phá vỡ liên kết. Ví dụ, nó sẽ không cho phép bạn:
        1.  Thêm một bản ghi vào bảng con (ví dụ: `students`) nếu giá trị khóa ngoại của nó không tồn tại trong bảng cha (ví dụ: `faculties`).
        2.  Xóa một bản ghi trong bảng cha (xóa một `faculty`) nếu vẫn còn các bản ghi trong bảng con đang tham chiếu đến nó (vẫn còn `students` thuộc `faculty` đó). (Hành vi này có thể được thay đổi bằng `ON DELETE CASCADE` hoặc `ON DELETE SET NULL`).

*   **Câu hỏi:** "Điều gì xảy ra khi tôi cố gắng xóa một hàng trong bảng cha đang được tham chiếu bởi một hàng trong bảng con?"
    *   **Trả lời:** Tùy thuộc vào cách khóa ngoại được định nghĩa:
        *   `ON DELETE RESTRICT` (hoặc `NO ACTION` - mặc định ở hầu hết CSDL): CSDL sẽ từ chối hành động xóa và báo lỗi. Đây là hành vi an toàn nhất.
        *   `ON DELETE CASCADE`: Nếu hàng ở bảng cha bị xóa, tất cả các hàng tương ứng ở bảng con cũng sẽ tự động bị xóa theo. Rất mạnh mẽ nhưng cũng nguy hiểm nếu không cẩn thận.
        *   `ON DELETE SET NULL`: Nếu hàng ở bảng cha bị xóa, giá trị khóa ngoại ở các hàng tương ứng trong bảng con sẽ được cập nhật thành `NULL`. Điều này chỉ hoạt động nếu cột khóa ngoại cho phép giá trị `NULL`.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Sử dụng "khóa thay thế" (surrogate key) làm khóa chính. Tức là dùng một cột số nguyên, tự tăng, không có ý nghĩa nghiệp vụ (`id`, `product_id`). Đừng dùng "khóa tự nhiên" (natural key) như email, số CCCD làm khóa chính, vì những giá trị này có thể thay đổi trong thực tế, và việc cập nhật một khóa chính là một thao tác rất phức tạp và tốn kém.
*   **Thực tiễn tốt nhất:** Luôn đặt tên cho các ràng buộc của bạn (ví dụ: `CONSTRAINT fk_student_faculty ...`). Khi CSDL báo lỗi liên quan đến ràng buộc, nó sẽ hiển thị tên bạn đã đặt, giúp việc gỡ lỗi nhanh hơn rất nhiều so với một cái tên do hệ thống tự tạo ra.
*   **Sai lầm thường gặp:** Cho phép `NULL` trên các cột khóa ngoại. Mặc dù đôi khi điều này là cần thiết (ví dụ: nhân viên chưa thuộc phòng ban nào), nhưng nếu theo logic nghiệp vụ một `student` *luôn phải* thuộc về một `faculty`, thì cột `faculty_id` nên được đặt là `NOT NULL`.
*   **Sai lầm thường gặp:** Không tạo chỉ mục (index) trên các cột khóa ngoại. Hầu hết các hệ quản trị CSDL hiện đại sẽ tự động làm điều này, nhưng không phải tất cả. Nếu không có chỉ mục, các thao tác `JOIN` hoặc kiểm tra ràng buộc khi `DELETE` từ bảng cha có thể trở nên cực kỳ chậm trên các bảng lớn.

#### **2. Mô hình ERD, Thực thể, và các Mối quan hệ (1-1, 1-n, n-n)**

**Lý thuyết**

Trước khi viết bất kỳ câu lệnh `CREATE TABLE` nào, các nhà phát triển và kiến trúc sư CSDL thường vẽ ra một "bản thiết kế". Bản thiết kế này được gọi là **Mô hình Quan hệ Thực thể (Entity-Relationship Diagram - ERD)**. Nó giúp chúng ta hình dung cấu trúc CSDL một cách trực quan.

*   **Tương tự:** ERD giống hệt như bản vẽ kiến trúc của một ngôi nhà. Bạn phải thiết kế phòng khách, phòng ngủ, nhà bếp và lối đi kết nối chúng trên giấy *trước khi* bắt đầu xây dựng. Việc này giúp phát hiện các vấn đề thiết kế từ sớm, tránh phải đập đi xây lại tốn kém.

ERD bao gồm 3 thành phần chính:

1.  **Entity (Thực thể):**
    *   Là một đối tượng hoặc khái niệm trong thế giới thực mà chúng ta muốn lưu trữ thông tin.
    *   **Ví dụ:** `Sinh Viên`, `Lớp Học`, `Sản Phẩm`, `Đơn Hàng`.
    *   Trong ERD, thực thể thường được biểu diễn bằng một hình chữ nhật. Mỗi thực thể sẽ trở thành một **bảng (table)** trong CSDL.

2.  **Attribute (Thuộc tính):**
    *   Là các đặc tính hoặc thông tin mô tả cho một thực thể.
    *   **Ví dụ:** Thực thể `Sinh Viên` có các thuộc tính như `mã_sinh_viên`, `họ_tên`, `ngày_sinh`.
    *   Mỗi thuộc tính sẽ trở thành một **cột (column)** trong bảng.

3.  **Relationship (Mối quan hệ):**
    *   Mô tả cách các thực thể tương tác hoặc liên kết với nhau.
    *   **Ví dụ:** Một `Sinh Viên` *đăng ký* một `Lớp Học`. Một `Tác Giả` *viết* nhiều `Bài Viết`.
    *   Mối quan hệ này được thực thi trong CSDL bằng **khóa ngoại (foreign keys)**.

**Các loại Mối quan hệ (Cardinality)**

Đây là phần quan trọng nhất, xác định "số lượng" liên kết giữa các thực thể.

1.  **Quan hệ Một-Một (One-to-One / 1-1):**
    *   **Định nghĩa:** Mỗi bản ghi trong Bảng A chỉ có thể liên kết với **tối đa một** bản ghi trong Bảng B, và ngược lại.
    *   **Ví dụ thực tế:** Một `Người Dùng` (`User`) có **một** `Hồ Sơ Người Dùng` (`UserProfile`). Một `UserProfile` chỉ thuộc về **một** `User`.
    *   **Khi nào dùng:** Loại quan hệ này khá hiếm. Thường được dùng để:
        *   Tách một bảng rất lớn thành hai bảng nhỏ hơn để cải thiện hiệu năng (ví dụ, tách thông tin ít truy cập ra riêng).
        *   Vì lý do bảo mật, tách các thông tin nhạy cảm (như thông tin cá nhân) ra một bảng riêng với quyền truy cập chặt chẽ hơn.

2.  **Quan hệ Một-Nhiều (One-to-Many / 1-n):**
    *   **Định nghĩa:** Một bản ghi trong Bảng A có thể liên kết với **nhiều** bản ghi trong Bảng B, nhưng mỗi bản ghi trong Bảng B chỉ liên kết với **duy nhất một** bản ghi trong Bảng A.
    *   **Ví dụ thực tế:** Một `Tác Giả` có thể viết **nhiều** `Bài Viết`, nhưng mỗi `Bài Viết` chỉ được viết bởi **một** `Tác Giả`.
    *   **Triển khai:** Khóa ngoại được đặt ở bảng "Nhiều" (Bảng B). Tức là bảng `BaiViet` sẽ có một cột `tac_gia_id` tham chiếu đến khóa chính của bảng `TacGia`.

3.  **Quan hệ Nhiều-Nhiều (Many-to-Many / n-n):**
    *   **Định nghĩa:** Một bản ghi trong Bảng A có thể liên kết với **nhiều** bản ghi trong Bảng B, và một bản ghi trong Bảng B cũng có thể liên kết với **nhiều** bản ghi trong Bảng A.
    *   **Ví dụ thực tế:** Một `Bài Viết` có thể có **nhiều** `Thẻ` (tag). Một `Thẻ` có thể được gán cho **nhiều** `Bài Viết`.
    *   **Triển khai:** Chúng ta **không thể** tạo mối quan hệ này trực tiếp. Thay vào đó, chúng ta phải tạo một bảng thứ ba gọi là **bảng trung gian (junction table / linking table)**.
        *   Bảng trung gian này sẽ chứa ít nhất hai cột: một khóa ngoại tham chiếu đến Bảng A và một khóa ngoại tham chiếu đến Bảng B.

---

**Ví dụ SQL**

Hãy mô hình hóa một blog đơn giản với `authors`, `posts`, và `tags`.

**1. Quan hệ 1-n: `authors` và `posts`**
Một tác giả (`authors`) có thể viết nhiều bài viết (`posts`).

```sql
-- Bảng "Một"
CREATE TABLE authors (
    author_id INT PRIMARY KEY AUTO_INCREMENT,
    author_name VARCHAR(100) NOT NULL
);

-- Bảng "Nhiều"
CREATE TABLE posts (
    post_id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    content TEXT,
    author_id INT, -- Khóa ngoại đặt ở đây

    CONSTRAINT fk_post_author
    FOREIGN KEY (author_id) REFERENCES authors(author_id)
);
```

**2. Quan hệ n-n: `posts` và `tags`**
Một bài viết có thể có nhiều tag, một tag có thể thuộc nhiều bài viết.

```sql
-- Bảng thực thể 1
CREATE TABLE tags (
    tag_id INT PRIMARY KEY AUTO_INCREMENT,
    tag_name VARCHAR(50) UNIQUE NOT NULL
);

-- Bảng trung gian (Junction Table)
CREATE TABLE post_tags (
    post_id INT,
    tag_id INT,

    -- Khóa chính phức hợp để đảm bảo một bài viết không bị gán cùng một tag hai lần
    PRIMARY KEY (post_id, tag_id),

    -- Hai khóa ngoại tham chiếu đến hai bảng chính
    CONSTRAINT fk_pt_post FOREIGN KEY (post_id) REFERENCES posts(post_id),
    CONSTRAINT fk_pt_tag FOREIGN KEY (tag_id) REFERENCES tags(tag_id)
);
```
*   
* **Giải thích bảng `post_tags`:** Nếu một bài viết có `post_id = 1` được gán 2 tag có `tag_id = 5` và `tag_id = 8`, bảng `post_tags` sẽ có 2 hàng: `(1, 5)` và `(1, 8)`.

---

**Bài tập nhỏ**

Bạn đang thiết kế CSDL cho một trang thương mại điện tử đơn giản. Có 3 thực thể chính: `Customers` (Khách hàng), `Orders` (Đơn hàng), và `Products` (Sản phẩm).
1.  Xác định loại mối quan hệ (1-1, 1-n, n-n) giữa `Customers` và `Orders`.
2.  Xác định loại mối quan hệ giữa `Orders` và `Products`.
3.  Làm thế nào để bạn triển khai mối quan hệ ở câu 2 bằng các bảng và khóa ngoại?

**Giải pháp**

1.  **`Customers` và `Orders` là quan hệ 1-n:** Một khách hàng có thể có nhiều đơn hàng, nhưng một đơn hàng chỉ thuộc về một khách hàng duy nhất.
2.  **`Orders` và `Products` là quan hệ n-n:** Một đơn hàng có thể chứa nhiều sản phẩm, và một sản phẩm có thể xuất hiện trong nhiều đơn hàng khác nhau.
3.  **Triển khai:**
    *   Chúng ta cần một bảng trung gian, thường được gọi là `order_details` hoặc `order_items`.
    *   Bảng này sẽ có các cột như: `order_id` (khóa ngoại tới `Orders`), `product_id` (khóa ngoại tới `Products`), `quantity` (số lượng sản phẩm trong đơn hàng đó), `price_at_purchase` (giá sản phẩm tại thời điểm mua).

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Làm thế nào để bạn triển khai một mối quan hệ nhiều-nhiều (many-to-many) trong CSDL quan hệ?"
    *   **Trả lời:** Bắt buộc phải sử dụng một bảng thứ ba gọi là bảng trung gian (junction table). Bảng này chứa hai cột khóa ngoại, mỗi cột tham chiếu đến khóa chính của một trong hai bảng trong mối quan hệ. Thông thường, khóa chính của bảng trung gian này là một khóa chính phức hợp (composite primary key) bao gồm cả hai cột khóa ngoại đó để đảm bảo tính duy nhất của cặp quan hệ.

*   **Câu hỏi:** "Khi nào bạn sẽ cân nhắc sử dụng mối quan hệ một-một (one-to-one)?"
    *   **Trả lời:** Có hai lý do chính. Thứ nhất là vì hiệu năng: nếu một bảng có quá nhiều cột, và một số cột (ví dụ: `user_bio`, `profile_picture_url`) ít được truy cập hơn các cột khác (như `username`, `password`), việc tách chúng ra một bảng riêng có thể làm tăng tốc độ truy vấn trên bảng chính. Thứ hai là vì bảo mật: tách dữ liệu nhạy cảm ra một bảng riêng để có thể áp dụng các quy tắc truy cập chặt chẽ hơn.

*   **Câu hỏi:** "Khóa chính của bảng trung gian trong quan hệ n-n nên là gì?"
    *   **Trả lời:** Cách tốt nhất là sử dụng một khóa chính phức hợp bao gồm cả hai cột khóa ngoại. Ví dụ, trong bảng `post_tags`, `PRIMARY KEY(post_id, tag_id)`. Điều này không chỉ định danh duy nhất cho mỗi hàng mà còn thực thi một quy tắc nghiệp vụ quan trọng: một bài viết không thể được gán cùng một tag nhiều hơn một lần. Một cách khác là thêm một cột `id` tự tăng làm khóa chính, nhưng vẫn cần phải có một ràng buộc `UNIQUE` trên cặp `(post_id, tag_id)` để đảm bảo tính toàn vẹn dữ liệu.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Đặt tên bảng trung gian bằng cách kết hợp tên của hai bảng mà nó liên kết. Ví dụ: `posts` và `tags` -> `post_tags`.
*   **Thực tiễn tốt nhất:** Đặt tên khóa ngoại một cách nhất quán, thường là `ten_bang_tham_chieu_dang_so_it_id`. Ví dụ: trong bảng `posts`, khóa ngoại tham chiếu đến `authors` nên được đặt là `author_id`.
*   **Sai lầm thường gặp (CỰC KỲ NGHIÊM TRỌNG):** Cố gắng triển khai quan hệ nhiều-nhiều bằng cách lưu một danh sách các ID được phân tách bằng dấu phẩy trong một cột văn bản. Ví dụ, trong bảng `posts` có một cột `tag_ids` chứa giá trị như `"1,5,12"`.
    *   **Tại sao sai:**
        1.  Vi phạm các quy tắc chuẩn hóa CSDL (1NF).
        2.  Không thể thực thi tính toàn vẹn tham chiếu bằng khóa ngoại.
        3.  Việc truy vấn (`JOIN`, tìm kiếm) trở nên cực kỳ phức tạp, chậm chạp và gần như không thể.
        4.  Việc cập nhật và xóa trở thành một cơn ác mộng.
    *   **Đây là một anti-pattern kinh điển và là một dấu hiệu đỏ lớn trong thiết kế CSDL.**

#### **3. Các Dạng Chuẩn: 1NF → 2NF → 3NF → BCNF**

**Lý thuyết**

**Chuẩn hóa CSDL (Database Normalization)** là một quy trình có hệ thống nhằm tổ chức các cột và bảng trong CSDL quan hệ để giảm thiểu sự **trùng lặp dữ liệu (data redundancy)** và cải thiện **tính toàn vẹn dữ liệu (data integrity)**.

*   **Tương tự:** Hãy tưởng tượng bạn có một cuốn sổ ghi chép mọi thứ. Ban đầu, bạn viết tất cả thông tin vào cùng một trang. Dần dần, bạn thấy tên và địa chỉ của một người được lặp lại mỗi khi họ mua hàng. Nếu người đó chuyển nhà, bạn phải tìm và sửa ở *tất cả* các dòng, rất dễ sai sót. Chuẩn hóa giống như việc bạn quyết định tách cuốn sổ đó ra thành nhiều cuốn chuyên biệt: một cuốn chỉ ghi thông tin khách hàng, một cuốn ghi thông tin sản phẩm, và một cuốn ghi nhận đơn hàng (chỉ tham chiếu đến khách hàng và sản phẩm).

**Mục tiêu chính của chuẩn hóa:**
1.  **Loại bỏ dữ liệu dư thừa:** Mỗi mẩu thông tin chỉ được lưu trữ ở một nơi duy nhất.
2.  **Loại bỏ các bất thường khi cập nhật dữ liệu (Anomalies):**
    *   **Insert Anomaly:** Không thể thêm thông tin về một đối tượng nếu thiếu thông tin về đối tượng khác. (Ví dụ: Không thể thêm một khóa học mới vào hệ thống nếu chưa có sinh viên nào đăng ký).
    *   **Update Anomaly:** Cập nhật một thông tin phải thay đổi ở nhiều nơi, dễ gây ra sự không nhất quán.
    *   **Delete Anomaly:** Xóa một bản ghi vô tình làm mất thông tin hữu ích khác. (Ví dụ: Xóa bản ghi đăng ký môn học cuối cùng của một sinh viên, vô tình xóa luôn thông tin của sinh viên đó khỏi CSDL).

Chúng ta sẽ đi qua các dạng chuẩn phổ biến nhất, từ 1NF đến BCNF. Mỗi dạng chuẩn là một bộ quy tắc chặt chẽ hơn dạng chuẩn trước đó.

---

**Ví dụ thực hành: Từ hỗn loạn đến trật tự**

Hãy bắt đầu với một bảng chưa được chuẩn hóa (đôi khi gọi là 0NF) dùng để quản lý việc đăng ký môn học của sinh viên. Đây là thứ bạn thường thấy trong một file Excel:

**Bảng `Student_Registrations_0NF`**

| student_id | student_name | student_major | courses                                          |
| ---------- | ------------ | ------------- | ------------------------------------------------ |
| 101        | An Nguyễn    | Công nghệ TT   | CS101: Nhập môn Lập trình, MA203: Giải tích 2    |
| 102        | Bình Lê      | Kinh tế       | EC101: Kinh tế Vi mô                             |
| 103        | Chi Trần     | Công nghệ TT   | CS101: Nhập môn Lập trình, CS305: Cấu trúc dữ liệu |

Bảng này chứa rất nhiều vấn đề.

---

#### **Dạng Chuẩn Thứ Nhất (First Normal Form - 1NF)**

**Lý thuyết**

*   **Quy tắc:**
    1.  Mỗi ô (cell) trong bảng phải chứa một giá trị duy nhất, không thể phân chia được (atomic). Không được chứa danh sách hay tập hợp.
    2.  Mỗi hàng phải là duy nhất.

*   **Vấn đề của bảng 0NF:** Cột `courses` vi phạm quy tắc 1. Nó chứa một danh sách các môn học. Điều này làm cho việc truy vấn trở nên bất khả thi. Ví dụ: Làm sao để đếm có bao nhiêu sinh viên học môn "CS101"?

**Chuyển đổi sang 1NF**

Chúng ta phải "làm phẳng" bảng bằng cách tách mỗi môn học của sinh viên ra thành một hàng riêng.

**Bảng `Student_Registrations_1NF`**

| student_id | student_name | student_major | course_id | course_name            | instructor_name | instructor_office |
| ---------- | ------------ | ------------- | --------- | ---------------------- | --------------- | ----------------- |
| 101        | An Nguyễn    | Công nghệ TT   | CS101     | Nhập môn Lập trình      | Giáo sư X       | A101              |
| 101        | An Nguyễn    | Công nghệ TT   | MA203     | Giải tích 2            | Giáo sư Y       | B203              |
| 102        | Bình Lê      | Kinh tế       | EC101     | Kinh tế Vi mô          | Giáo sư Z       | C305              |
| 103        | Chi Trần     | Công nghệ TT   | CS101     | Nhập môn Lập trình      | Giáo sư X       | A101              |
| 103        | Chi Trần     | Công nghệ TT   | CS305     | Cấu trúc dữ liệu       | Giáo sư X       | A101              |

*   **Kết quả:** Bây giờ mỗi ô đều chứa giá trị nguyên tử. Chúng ta đã đạt 1NF.
*   **Vấn đề mới:** Dữ liệu bị lặp lại rất nhiều. `student_name` và `student_major` lặp lại cho mỗi môn học mà sinh viên đăng ký. `course_name`, `instructor_name`, `instructor_office` lặp lại cho mỗi sinh viên đăng ký cùng một môn. Điều này dẫn đến các bất thường khi cập nhật.

---

#### **Dạng Chuẩn Thứ Hai (Second Normal Form - 2NF)**

**Lý thuyết**

*   **Điều kiện:** Bảng phải đang ở dạng 1NF.
*   **Quy tắc:** Mọi thuộc tính không phải là khóa đều phải **phụ thuộc hoàn toàn** vào **toàn bộ khóa chính**. Không được có sự phụ thuộc riêng lẻ vào một phần của khóa chính (no partial dependencies). Quy tắc này chỉ có ý nghĩa đối với các bảng có **khóa chính phức hợp (composite primary key)**.

*   **Phân tích bảng 1NF:**
    *   Khóa chính của bảng `Student_Registrations_1NF` là `(student_id, course_id)`. Cần cả hai để xác định duy nhất một hàng.
    *   **Phát hiện Phụ thuộc riêng lẻ (Partial Dependency):**
        *   `student_name` và `student_major` chỉ phụ thuộc vào `student_id`. Chúng không liên quan gì đến `course_id`.
        *   `course_name`, `instructor_name`, `instructor_office` chỉ phụ thuộc vào `course_id`. Chúng không liên quan gì đến `student_id`.

**Chuyển đổi sang 2NF**

Chúng ta tách các nhóm phụ thuộc riêng lẻ ra thành các bảng riêng.

1.  **Bảng `Students`:**

    | student_id | student_name | student_major |
    | ---------- | ------------ | ------------- |
    | 101        | An Nguyễn    | Công nghệ TT   |
    | 102        | Bình Lê      | Kinh tế       |
    | 103        | Chi Trần     | Công nghệ TT   |

2.  **Bảng `Courses`:**

    | course_id | course_name            | instructor_name | instructor_office |
    | --------- | ---------------------- | --------------- | ----------------- |
    | CS101     | Nhập môn Lập trình      | Giáo sư X       | A101              |
    | MA203     | Giải tích 2            | Giáo sư Y       | B203              |
    | EC101     | Kinh tế Vi mô          | Giáo sư Z       | C305              |
    | CS305     | Cấu trúc dữ liệu       | Giáo sư X       | A101              |

3.  **Bảng `Registrations` (bảng trung gian):**

    | student_id | course_id |
    | ---------- | --------- |
    | 101        | CS101     |
    | 101        | MA203     |
    | 102        | EC101     |
    | 103        | CS101     |
    | 103        | CS305     |

*   **Kết quả:** Chúng ta đã loại bỏ được rất nhiều dữ liệu lặp lại. Nếu 'An Nguyễn' đổi chuyên ngành, ta chỉ cần sửa ở 1 nơi trong bảng `Students`. Chúng ta đã đạt 2NF.
*   **Vấn đề mới:** Nhìn vào bảng `Courses`. Giả sử 'Giáo sư X' chuyển văn phòng. Chúng ta vẫn phải cập nhật ở 2 dòng (cho CS101 và CS305). Vấn đề này là do "phụ thuộc bắc cầu".

---

#### **Dạng Chuẩn Thứ Ba (Third Normal Form - 3NF)**

**Lý thuyết**

*   **Điều kiện:** Bảng phải đang ở dạng 2NF.
*   **Quy tắc:** Không được tồn tại **phụ thuộc bắc cầu (transitive dependency)**. Tức là một thuộc tính không phải khóa không được phụ thuộc vào một thuộc tính không phải khóa khác.

*   **Phân tích bảng `Courses` (từ 2NF):**
    *   Khóa chính là `course_id`.
    *   `course_name`, `instructor_name`, `instructor_office` đều phụ thuộc vào `course_id`.
    *   **Phát hiện Phụ thuộc bắc cầu:** Cột `instructor_office` thực chất không phụ thuộc trực tiếp vào `course_id`. Nó phụ thuộc vào `instructor_name`. Ta có chuỗi phụ thuộc: `course_id` → `instructor_name` → `instructor_office`. Đây chính là một phụ thuộc bắc cầu.

**Chuyển đổi sang 3NF**

Chúng ta tách phụ thuộc bắc cầu ra một bảng mới.

1.  **Bảng `Instructors`:**

    | instructor_id | instructor_name | instructor_office |
    | ------------- | --------------- | ----------------- |
    | 901           | Giáo sư X       | A101              |
    | 902           | Giáo sư Y       | B203              |
    | 903           | Giáo sư Z       | C305              |
    *(Chúng ta thêm `instructor_id` làm khóa chính thay thế)*

2.  **Bảng `Courses` (đã cập nhật):**

    | course_id | course_name            | instructor_id |
    | --------- | ---------------------- | ------------- |
    | CS101     | Nhập môn Lập trình      | 901           |
    | MA203     | Giải tích 2            | 902           |
    | EC101     | Kinh tế Vi mô          | 903           |
    | CS305     | Cấu trúc dữ liệu       | 901           |

**Hệ thống hoàn chỉnh ở dạng 3NF:**
*   `Students` (student_id, student_name, student_major)
*   `Instructors` (instructor_id, instructor_name, instructor_office)
*   `Courses` (course_id, course_name, instructor_id)
*   `Registrations` (student_id, course_id)

*   **Kết quả:** Bây giờ mỗi mẩu thông tin (về sinh viên, giảng viên, môn học) đều được lưu trữ ở một nơi duy nhất. Nếu 'Giáo sư X' chuyển văn phòng, ta chỉ cần cập nhật 1 hàng trong bảng `Instructors`. Hệ thống đã rất gọn gàng và toàn vẹn.

---

#### **Dạng Chuẩn Boyce-Codd (Boyce-Codd Normal Form - BCNF)**

**Lý thuyết**

*   **Điều kiện:** Bảng phải đang ở dạng 3NF.
*   **Định nghĩa:** BCNF là một phiên bản chặt chẽ hơn của 3NF. Nó xử lý một số trường hợp hiếm gặp mà 3NF bỏ qua.
*   **Quy tắc:** Đối với mọi phụ thuộc hàm `X → Y` không tầm thường, `X` phải là một **siêu khóa (superkey)**. (Siêu khóa là một tập hợp các thuộc tính mà có thể xác định duy nhất một hàng, khóa chính là một trường hợp tối thiểu của siêu khóa).

*   **Khi nào BCNF quan trọng?** Khi một bảng có:
    1.  Nhiều khóa ứng viên (candidate keys).
    2.  Các khóa ứng viên đó là khóa phức hợp.
    3.  Các khóa ứng viên đó có chung thuộc tính.

**Ví dụ về vi phạm BCNF:**

Hãy xem xét một bảng `Student_Tutor_Subject` nơi:
*   Mỗi sinh viên có thể học nhiều môn.
*   Với một môn học cụ thể, một sinh viên chỉ có một gia sư.
*   Mỗi gia sư chỉ dạy một môn.

| student_id | subject | tutor_name |
| ---------- | ------- | ---------- |
| 101        | Toán    | Thầy An    |
| 101        | Lý      | Cô Bình    |
| 102        | Toán    | Thầy An    |
| 103        | Hóa     | Thầy Cường |

*   **Phân tích khóa:**
    *   `(student_id, subject)` là một khóa ứng viên (xác định duy nhất một gia sư).
    *   `(student_id, tutor_name)` cũng là một khóa ứng viên (xác định duy nhất một môn học).
*   **Phân tích phụ thuộc:**
    *   Ta có phụ thuộc `tutor_name` → `subject` (vì mỗi gia sư chỉ dạy một môn).
*   **Vi phạm BCNF:** Phụ thuộc `tutor_name` → `subject` vi phạm BCNF vì `tutor_name` không phải là một siêu khóa.

**Chuyển đổi sang BCNF**

Tách bảng ra làm hai:

1.  **Bảng `Student_Tutors`:**

    | student_id | tutor_name |
    | ---------- | ---------- |
    | 101        | Thầy An    |
    | 101        | Cô Bình    |
    | 102        | Thầy An    |
    | 103        | Thầy Cường |

2.  **Bảng `Tutor_Subjects`:**

    | tutor_name | subject |
    | ---------- | ------- |
    | Thầy An    | Toán    |
    | Cô Bình    | Lý      |
    | Thầy Cường | Hóa     |

*   **Kết quả:** Cả hai bảng mới đều ở dạng BCNF.

---

**Bài tập nhỏ**

Cho một bảng `Project_Assignments` như sau, hãy chuẩn hóa nó về dạng 3NF. Bảng này theo dõi nhân viên nào làm việc cho dự án nào, tên dự án và trưởng dự án là ai.

| emp_id | project_id | emp_name | project_name       | project_leader_name |
| ------ | ---------- | -------- | ------------------ | ------------------- |
| E1     | P1         | Nam      | Xây dựng App      | Nga                 |
| E1     | P2         | Nam      | Nâng cấp CSDL      | Trung               |
| E2     | P1         | Mai      | Xây dựng App      | Nga                 |
| E3     | P2         | Huy      | Nâng cấp CSDL      | Trung               |

**Giải pháp**

1.  **1NF:** Bảng đã ở dạng 1NF. Khóa chính là `(emp_id, project_id)`.
2.  **Phân tích 2NF (Phụ thuộc riêng lẻ):**
    *   `emp_name` chỉ phụ thuộc vào `emp_id`.
    *   `project_name` và `project_leader_name` chỉ phụ thuộc vào `project_id`.
    *   Tách ra:
        *   `Employees` (emp_id, emp_name)
        *   `Projects` (project_id, project_name, project_leader_name)
        *   `Assignments` (emp_id, project_id)
3.  **Phân tích 3NF (Phụ thuộc bắc cầu):**
    *   Nhìn vào bảng `Projects` mới tạo: `project_leader_name` phụ thuộc vào `project_id`. Giả sử tên trưởng dự án chỉ là một thuộc tính của dự án. Nếu `project_leader_name` phụ thuộc vào `project_leader_id` (không có ở đây), thì mới có phụ thuộc bắc cầu. Trong trường hợp này, bảng `Projects` đã ở dạng 3NF.
4.  **Kết quả cuối cùng (3NF):**
    *   **Bảng `Employees`:** `(emp_id (PK), emp_name)`
    *   **Bảng `Projects`:** `(project_id (PK), project_name, project_leader_name)`
    *   **Bảng `Assignments`:** `(emp_id (FK), project_id (FK))` (PK là `(emp_id, project_id)`)

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Giải thích mục đích của việc chuẩn hóa CSDL bằng ngôn ngữ đơn giản."
    *   **Trả lời:** Chuẩn hóa là quá trình dọn dẹp và sắp xếp lại dữ liệu trong CSDL. Mục đích là để mỗi thông tin chỉ được lưu ở một nơi duy nhất. Việc này giúp tiết kiệm dung lượng, tránh sai sót khi cập nhật (chỉ cần sửa một chỗ), và đảm bảo dữ liệu luôn nhất quán và đáng tin cậy.

*   **Câu hỏi:** "Sự khác biệt chính giữa 2NF và 3NF là gì?"
    *   **Trả lời:** Cả hai đều nhằm mục đích giảm sự trùng lặp dữ liệu. 2NF giải quyết vấn đề "phụ thuộc riêng lẻ", nơi một cột không khóa phụ thuộc vào chỉ một phần của khóa chính phức hợp. 3NF giải quyết vấn đề "phụ thuộc bắc cầu", nơi một cột không khóa phụ thuộc vào một cột không khóa khác, thay vì phụ thuộc trực tiếp vào khóa chính.

*   **Câu hỏi:** "Có phải lúc nào chúng ta cũng nên chuẩn hóa CSDL đến mức cao nhất có thể không? Tại sao?"
    *   **Trả lời:** Không hẳn. Mặc dù chuẩn hóa cao (3NF, BCNF) mang lại sự toàn vẹn dữ liệu tốt nhất, nó cũng có một nhược điểm: làm tăng số lượng bảng. Điều này có nghĩa là các truy vấn phức tạp sẽ cần `JOIN` nhiều bảng hơn, có thể làm giảm hiệu năng đọc dữ liệu. Trong một số trường hợp, đặc biệt là trong các hệ thống báo cáo (data warehousing) hoặc các ứng dụng yêu cầu tốc độ đọc cực nhanh, người ta có thể chấp nhận một mức độ trùng lặp dữ liệu có kiểm soát để tránh các phép `JOIN` tốn kém. Quá trình này được gọi là "phi chuẩn hóa" (denormalization).

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Mục tiêu thiết kế CSDL cho các hệ thống giao dịch trực tuyến (OLTP) thường là đạt đến **dạng chuẩn thứ ba (3NF)**. Đây được coi là điểm cân bằng tốt giữa tính toàn vẹn dữ liệu và hiệu năng.
*   **Thực tiễn tốt nhất:** Hiểu rõ nghiệp vụ trước khi chuẩn hóa. Việc xác định các phụ thuộc hàm (functional dependencies) đòi hỏi bạn phải hiểu dữ liệu và các quy tắc của nó.
*   **Sai lầm thường gặp:** **Chuẩn hóa quá mức (Over-normalization).** Tách các bảng ra một cách không cần thiết, dẫn đến các câu truy vấn phức tạp vô ích. Ví dụ, tách cột `zip_code` và `city` ra thành một bảng riêng trong khi chúng luôn đi cùng nhau và ít khi thay đổi.
*   **Sai lầm thường gặp:** **Không hiểu về phi chuẩn hóa (Denormalization).** Cho rằng chuẩn hóa là quy tắc tuyệt đối. Trong thực tế, phi chuẩn hóa là một kỹ thuật tối ưu hóa quan trọng, nhưng nó phải được thực hiện một cách có chủ đích và hiểu rõ các đánh đổi.

#### **4. Khi nào KHÔNG nên Chuẩn hóa (Đánh đổi của việc Phi chuẩn hóa)**

**Lý thuyết**

Sau khi tìm hiểu về tầm quan trọng của việc chuẩn hóa, chúng ta sẽ khám phá một khái niệm nâng cao và thực tế hơn: **Phi chuẩn hóa (Denormalization)**.

**Phi chuẩn hóa là gì?**
Đó là quá trình **cố tình** vi phạm một hoặc nhiều quy tắc chuẩn hóa, chấp nhận sự trùng lặp dữ liệu để đạt được một mục tiêu khác, thường là **cải thiện hiệu năng đọc (read performance)**.

*   **Tương tự:** Chuẩn hóa giống như việc bạn tổ chức thư viện của mình một cách hoàn hảo: sách Lịch sử ở kệ A, sách Khoa học ở kệ B, thông tin tác giả ở một sổ riêng. Khi bạn muốn tìm hiểu "tất cả các cuốn sách khoa học của tác giả X", bạn phải đi đến sổ tác giả, tìm ID của ông X, sau đó đến kệ B để tìm sách. Việc này rất có trật tự nhưng mất nhiều bước (`JOIN`).
*   **Phi chuẩn hóa** giống như việc bạn tạo ra một "kệ trưng bày đặc biệt" cho các chủ đề hot. Trên kệ này, bạn đặt sẵn những cuốn sách khoa học bán chạy nhất, và dán kèm một mẩu giấy ghi rõ tên tác giả ngay trên bìa sách. Bây giờ, việc tìm kiếm nhanh hơn rất nhiều (chỉ cần nhìn vào một chỗ), nhưng bạn phải chấp nhận rằng:
    1.  Tên tác giả bị lặp lại ở cả sổ chính và trên mẩu giấy.
    2.  Nếu tác giả đổi bút danh, bạn phải nhớ cập nhật ở cả hai nơi.

**Sự Đánh Đổi Cốt Lõi**

| Khía cạnh          | Chuẩn hóa (Normalization)                                     | Phi chuẩn hóa (Denormalization)                                |
| ------------------ | ------------------------------------------------------------- | -------------------------------------------------------------- |
| **Tối ưu cho**     | **Ghi dữ liệu (`INSERT`, `UPDATE`, `DELETE`)**                 | **Đọc dữ liệu (`SELECT`)**                                     |
| **Dữ liệu**        | Không trùng lặp, tính toàn vẹn và nhất quán cao.               | Chấp nhận trùng lặp, tiềm ẩn nguy cơ không nhất quán.           |
| **Cập nhật**       | Dễ dàng, chỉ cần thay đổi dữ liệu ở một nơi duy nhất.          | Phức tạp, phải cập nhật dữ liệu ở nhiều nơi, tốn kém hơn.      |
| **Truy vấn đọc**   | Có thể yêu cầu `JOIN` nhiều bảng, chậm hơn với các truy vấn phức tạp. | Thường chỉ cần truy vấn trên một bảng, rất nhanh.              |

**Khi nào nên cân nhắc Phi chuẩn hóa?**

Bạn chỉ nên phi chuẩn hóa khi có lý do chính đáng và đã xác định được một nút thắt cổ chai về hiệu năng.
1.  **Hệ thống Báo cáo và Phân tích (Reporting & Data Warehousing):** Đây là trường hợp phổ biến nhất. Các hệ thống này cần thực hiện các truy vấn tổng hợp trên một lượng dữ liệu khổng lồ. Việc `JOIN` liên tục là không khả thi. Người ta thường tạo ra các bảng "dữ liệu phẳng" (flat tables) được phi chuẩn hóa để các nhà phân tích có thể truy vấn nhanh chóng.
2.  **Tăng tốc các truy vấn đọc thường xuyên:** Trong một ứng dụng web, có những trang được truy cập liên tục (ví dụ: trang chủ, trang chi tiết sản phẩm, feed mạng xã hội). Để hiển thị thông tin trên các trang này, thay vì `JOIN` 5-6 bảng mỗi lần tải trang, người ta có thể tạo một bảng được phi chuẩn hóa chứa sẵn tất cả thông tin cần thiết.
3.  **Tính toán trước các giá trị tổng hợp:** Thay vì chạy `COUNT(*)` hoặc `SUM()` mỗi lần, bạn có thể lưu trữ kết quả của các phép tính này trong một cột riêng và chỉ cập nhật nó khi có sự kiện liên quan xảy ra.

---

**Ví dụ: Từ Chuẩn hóa sang Phi chuẩn hóa**

Hãy xem xét một hệ thống blog đơn giản. Chúng ta muốn hiển thị một danh sách các bài viết, mỗi bài viết kèm theo tên tác giả và tổng số bình luận.

**Thiết kế Chuẩn hóa (3NF)**

Chúng ta có 3 bảng:
1.  `authors` (`author_id`, `author_name`)
2.  `posts` (`post_id`, `title`, `author_id`)
3.  `comments` (`comment_id`, `comment_text`, `post_id`)

Để lấy thông tin cần thiết, chúng ta phải viết một câu lệnh `JOIN` phức tạp:
```sql
SELECT
    p.post_id,
    p.title,
    a.author_name,
    COUNT(c.comment_id) AS comment_count
FROM
    posts AS p
INNER JOIN
    authors AS a ON p.author_id = a.author_id
LEFT JOIN -- Dùng LEFT JOIN phòng trường hợp bài viết chưa có comment nào
    comments AS c ON p.post_id = c.post_id
GROUP BY
    p.post_id, p.title, a.author_name
ORDER BY
    p.post_id DESC;
```
Câu lệnh này hoạt động tốt, nhưng nếu bảng `posts` và `comments` có hàng triệu bản ghi, nó sẽ trở nên rất chậm vì phải thực hiện `JOIN` và `GROUP BY` trên dữ liệu lớn mỗi khi có người dùng truy cập.

**Thiết kế Phi chuẩn hóa**

Chúng ta nhận thấy rằng tên tác giả và số lượng bình luận là thông tin được đọc rất thường xuyên. Chúng ta quyết định phi chuẩn hóa bằng cách thêm trực tiếp các thông tin này vào bảng `posts`.

**Bảng `posts` mới:**
`post_id` (PK), `title`, `author_id` (FK), `author_name` (dữ liệu dư thừa), `comment_count` (dữ liệu tính toán trước).

Bây giờ, câu lệnh để lấy danh sách bài viết trở nên cực kỳ đơn giản và nhanh chóng:
```sql
SELECT post_id, title, author_name, comment_count
FROM posts
ORDER BY post_id DESC;
```
Không cần `JOIN`, không cần `GROUP BY`. Tốc độ đọc được cải thiện đáng kể.

**Cái giá phải trả:**
Sự đánh đổi nằm ở logic ghi dữ liệu:
*   **Khi một bình luận mới được thêm vào:** Ngoài việc `INSERT` vào bảng `comments`, ứng dụng của bạn *phải* thực thi thêm một lệnh `UPDATE`:
    ```sql
    UPDATE posts SET comment_count = comment_count + 1 WHERE post_id = ?;
    ```
*   **Khi một tác giả đổi tên:** Ngoài việc `UPDATE` bảng `authors`, bạn phải tìm và cập nhật tên của họ trong *tất cả* các bài viết mà họ đã viết:
    ```sql
    UPDATE posts SET author_name = 'Tên mới' WHERE author_id = ?;
    ```
Việc này làm tăng độ phức tạp của code phía ứng dụng và tiềm ẩn nguy cơ dữ liệu không nhất quán nếu một trong các bước cập nhật bị lỗi.

---

**Bài tập nhỏ (Bài toán thiết kế)**

Bạn đang thiết kế CSDL cho một trang thương mại điện tử. Trang chi tiết sản phẩm cần hiển thị:
*   Tên sản phẩm, mô tả, giá.
*   Tên của danh mục sản phẩm.
*   Điểm đánh giá trung bình của sản phẩm (tính từ tất cả các review).

Việc tải trang này phải cực nhanh. Hãy đề xuất một thiết kế phi chuẩn hóa cho bảng `products` và thảo luận về những thao tác cập nhật cần thiết để duy trì tính nhất quán.

**Giải pháp**

*   **Thiết kế chuẩn hóa:** Sẽ có các bảng `products`, `categories`, và `reviews`. Để lấy dữ liệu, cần `JOIN` `products` với `categories` và `JOIN` với `reviews` rồi dùng `AVG()` trên điểm số.

*   **Đề xuất thiết kế phi chuẩn hóa:**
    *   Tạo bảng `products` với các cột: `product_id`, `product_name`, `description`, `price`, `category_id` (vẫn giữ FK), `category_name` (dữ liệu dư thừa), `average_rating` (dữ liệu tính toán trước), `review_count` (hữu ích để tính lại trung bình).
    *   **Lợi ích:** Truy vấn để hiển thị trang chi tiết sản phẩm chỉ là `SELECT * FROM products WHERE product_id = ?`. Cực nhanh.

*   **Thao tác cập nhật cần thiết:**
    *   **Khi một review mới được thêm:**
        1.  `INSERT` vào bảng `reviews`.
        2.  Cập nhật lại `average_rating` trong bảng `products`. Công thức: `new_avg = ((old_avg * old_count) + new_score) / (old_count + 1)`. Đồng thời tăng `review_count`.
    *   **Khi một danh mục đổi tên:**
        1.  `UPDATE` tên trong bảng `categories`.
        2.  `UPDATE` `category_name` cho tất cả các sản phẩm thuộc danh mục đó trong bảng `products`.

---

**Câu hỏi phỏng vấn**

*   **Câu hỏi:** "Phi chuẩn hóa là gì và tại sao chúng ta lại cần nó?"
    *   **Trả lời:** Phi chuẩn hóa là hành động cố ý thêm dữ liệu dư thừa vào CSDL để cải thiện hiệu năng đọc. Chúng ta cần nó trong các kịch bản mà tốc độ truy vấn `SELECT` là tối quan trọng, chẳng hạn như trong các hệ thống báo cáo hoặc các trang web có lưu lượng truy cập cao, nơi các phép `JOIN` phức tạp sẽ trở thành nút thắt cổ chai về hiệu năng.

*   **Câu hỏi:** "Rủi ro lớn nhất của việc phi chuẩn hóa là gì và làm thế nào để giảm thiểu nó?"
    *   **Trả lời:** Rủi ro lớn nhất là **dữ liệu không nhất quán (data inconsistency)**, xảy ra khi dữ liệu dư thừa không được cập nhật đồng bộ. Ví dụ, tên tác giả được cập nhật trong bảng `authors` nhưng vẫn là tên cũ trong bảng `posts`. Để giảm thiểu rủi ro này, chúng ta có thể:
        1.  Sử dụng **Transactions** để đảm bảo tất cả các lệnh `UPDATE` liên quan đều thành công hoặc thất bại cùng nhau.
        2.  Sử dụng **Triggers** trong CSDL để tự động cập nhật dữ liệu dư thừa khi dữ liệu gốc thay đổi.
        3.  Xây dựng logic xử lý chặt chẽ ở tầng ứng dụng.
        4.  Chạy các **batch job** định kỳ để quét và đồng bộ lại dữ liệu.

*   **Câu hỏi:** "Hãy đưa ra quy tắc chung của bạn: khi nào thì chuẩn hóa, khi nào thì phi chuẩn hóa?"
    *   **Trả lời:** Quy tắc vàng là: **"Chuẩn hóa trước, phi chuẩn hóa sau khi cần thiết" (Normalize first, denormalize when necessary)**. Luôn bắt đầu với một thiết kế được chuẩn hóa tốt (ít nhất là 3NF). Chỉ sau khi hệ thống đi vào hoạt động và bạn xác định được các truy vấn cụ thể gây ra vấn đề về hiệu năng thông qua các công cụ đo lường (profiling), bạn mới nên xem xét phi chuẩn hóa một cách có chọn lọc cho chính những truy vấn đó.

---

**Thực tiễn tốt nhất & Sai lầm thường gặp**

*   **Thực tiễn tốt nhất:** Phi chuẩn hóa phải là một quyết định có ý thức, không phải là một sai lầm trong thiết kế. Luôn ghi lại lý do tại sao một phần của CSDL được phi chuẩn hóa.
*   **Thực tiễn tốt nhất:** Cân nhắc các giải pháp thay thế trước khi phi chuẩn hóa. Đôi khi, một truy vấn chậm có thể được giải quyết bằng cách thêm **chỉ mục (index)** phù hợp, tối ưu lại câu lệnh SQL, hoặc sử dụng **materialized views** (một dạng bảng cache được CSDL tự động quản lý).
*   **Sai lầm thường gặp:** **Phi chuẩn hóa sớm (Premature Denormalization).** Áp dụng phi chuẩn hóa ngay từ đầu mà không có bằng chứng nào cho thấy hiệu năng sẽ là một vấn đề. Điều này dẫn đến một hệ thống phức tạp và khó bảo trì một cách không cần thiết.
*   **Sai lầm thường gặp:** **Phi chuẩn hóa mà không quản lý cập nhật.** Đây là sai lầm nghiêm trọng nhất, dẫn đến dữ liệu rác và không đáng tin cậy trong hệ thống của bạn. Nếu bạn quyết định phi chuẩn hóa, bạn phải chịu trách nhiệm đảm bảo dữ liệu luôn được đồng bộ.