Chào bạn, chúng ta tiếp tục với **PHẦN 4**.

Nếu bạn đã từng nối chuỗi SQL trong Java (kiểu `String sql = "SELECT * FROM..." + " WHERE..."`), bạn sẽ hiểu nỗi đau khi phải xử lý dấu phẩy, dấu `AND`, hay kiểm tra null.

**Dynamic SQL** (SQL động) là tính năng được yêu thích nhất của MyBatis, giúp bạn viết các câu SQL linh hoạt ngay trong XML mà không cần code Java lằng nhằng.

---

# PHẦN 4: DYNAMIC SQL (SỨC MẠNH THỰC SỰ)

MyBatis cung cấp các thẻ XML tương tự như các lệnh điều kiện trong lập trình (`if`, `switch`, `for`).

### 1. Thẻ `<if>` - Kiểm tra điều kiện đơn giản

Dùng khi bạn muốn thêm điều kiện vào câu SQL **nếu tham số tồn tại**.

**Ví dụ:** Tìm kiếm User. Nếu nhập `name` thì tìm theo `name`. Nếu không nhập (null) thì bỏ qua.

```xml
<select id="findUser" resultType="User">
    SELECT * FROM users
    WHERE state = 'ACTIVE'
    <!-- Nếu name != null thì mới nối thêm đoạn AND này vào -->
    <if test="name != null">
        AND name like #{name}
    </if>
</select>
```
*   Nếu `name` là "Tuan", SQL thành: `... WHERE state = 'ACTIVE' AND name like 'Tuan'`
*   Nếu `name` là null, SQL thành: `... WHERE state = 'ACTIVE'`

---

### 2. Thẻ `<where>` - Xử lý thông minh mệnh đề WHERE

Vấn đề của thẻ `<if>` ở trên là nếu ta không có điều kiện cố định (`state = 'ACTIVE'`), mà tất cả đều là tùy chọn:

```xml
<!-- SAI lầm thường gặp -->
<select id="findUserBad" resultType="User">
    SELECT * FROM users
    WHERE 
    <if test="name != null">
        AND name = #{name}
    </if>
</select>
```
*   Nếu `name` có dữ liệu -> SQL: `... WHERE AND name = ...` (**Lỗi cú pháp dư chữ AND**)
*   Nếu không có điều kiện nào -> SQL: `... WHERE` (**Lỗi cú pháp thiếu điều kiện**)

**Giải pháp: Dùng thẻ `<where>`**
Thẻ này cực kỳ thông minh:
1.  Tự động thêm từ khóa `WHERE` nếu bên trong có ít nhất 1 điều kiện đúng.
2.  Tự động **cắt bỏ** chữ `AND` hoặc `OR` ở đầu nếu nó bị thừa.

```xml
<select id="findUserSmart" resultType="User">
    SELECT * FROM users
    <where>
        <if test="name != null">
            name = #{name}
        </if>
        <if test="email != null">
            AND email = #{email}
        </if>
    </where>
</select>
```
*   Nếu có `email`, nó tự xóa chữ `AND` ở trước `email` đi -> `WHERE email = ...`
*   Nếu không có gì, nó bỏ luôn chữ `WHERE` -> `SELECT * FROM users`.

---

### 3. Thẻ `<choose>, <when>, <otherwise>` - Giống Switch-Case

Dùng khi bạn chỉ muốn chọn **MỘT** trong nhiều điều kiện (Ưu tiên từ trên xuống dưới).

**Ví dụ:** Tìm theo ID trước, nếu không có ID thì tìm theo Name, nếu không có cả hai thì lấy status là 'GUEST'.

```xml
<select id="findUserByPriority" resultType="User">
    SELECT * FROM users
    <where>
        <choose>
            <when test="id != null">
                id = #{id}
            </when>
            <when test="name != null">
                name = #{name}
            </when>
            <otherwise>
                status = 'GUEST'
            </otherwise>
        </choose>
    </where>
</select>
```

---

### 4. Thẻ `<set>` - Dùng cho câu UPDATE

Khi update, ta thường chỉ muốn update các trường có thay đổi (không null).
Vấn đề gặp phải: Dấu phẩy `,` thừa ở cuối câu lệnh.

**Giải pháp: Thẻ `<set>`**
Nó tự động thêm từ khóa `SET` và **xóa dấu phẩy thừa ở cuối**.

```xml
<update id="updateUserDynamic">
    UPDATE users
    <set>
        <if test="name != null">name = #{name},</if>
        <if test="email != null">email = #{email},</if>
        <if test="password != null">password = #{password},</if>
    </set>
    WHERE id = #{id}
</update>
```
Nếu chỉ update `name`:
SQL sinh ra: `UPDATE users SET name = ? WHERE id = ?` (Dấu phẩy sau `name` tự động bị xóa).

---

### 5. Thẻ `<foreach>` - Dùng cho câu lệnh IN

Đây là thẻ rất mạnh để xử lý List/Array.
Ví dụ: Xóa các user có ID nằm trong danh sách: `DELETE FROM users WHERE id IN (1, 5, 7)`.

*   Input Java: `List<Integer> ids`

```xml
<delete id="deleteByIds">
    DELETE FROM users
    WHERE id IN
    <!-- 
        collection: tên biến danh sách truyền vào
        item: tên biến tạm cho từng phần tử
        open: chuỗi bắt đầu
        close: chuỗi kết thúc
        separator: dấu phân cách giữa các phần tử
    -->
    <foreach item="item" collection="ids" open="(" separator="," close=")">
        #{item}
    </foreach>
</delete>
```

Ngoài ra, `<foreach>` còn dùng để **Bulk Insert** (Insert nhiều dòng 1 lúc) cực nhanh:
```xml
<insert id="insertBatch">
    INSERT INTO users (name, email) VALUES
    <foreach item="u" collection="listUsers" separator=",">
        (#{u.name}, #{u.email})
    </foreach>
</insert>
```

---

### 6. `<trim>` - Thẻ tùy biến (Advanced)

Thực ra `<where>` và `<set>` chỉ là trường hợp con của `<trim>`. Bạn có thể tự định nghĩa quy tắc cắt chuỗi.

Ví dụ: Tự viết lại chức năng của thẻ `<where>` bằng `<trim>`:
```xml
<trim prefix="WHERE" prefixOverrides="AND |OR ">
    ...
</trim>
```
*(Ít khi dùng trực tiếp, nhưng biết để hiểu cơ chế).*

---

### 7. Thẻ `<sql>` và `<include>` - Tái sử dụng code

Nếu bạn thấy mình viết đi viết lại danh sách cột `id, name, email, address...` trong nhiều câu select, hãy tách nó ra.

```xml
<!-- Định nghĩa đoạn SQL chung -->
<sql id="Base_Column_List">
    id, name, email, created_at
</sql>

<!-- Sử dụng lại -->
<select id="findAll" resultType="User">
    SELECT 
    <include refid="Base_Column_List" />
    FROM users
</select>
```

---

### TỔNG KẾT PHẦN 4
Bạn đã nắm được các vũ khí để xử lý logic phức tạp trong SQL:
1.  **`<if>`**: Kiểm tra null.
2.  **`<where>`**: Tự động xử lý từ khóa WHERE và xóa AND/OR thừa (Must use).
3.  **`<set>`**: Tự động xử lý dấu phẩy trong Update (Must use).
4.  **`<choose>`**: Logic if-else if-else.
5.  **`<foreach>`**: Xử lý danh sách cho câu lệnh `IN` hoặc Batch Insert.
6.  **`<sql>`**: Tái sử dụng đoạn mã SQL để code gọn hơn.

---
