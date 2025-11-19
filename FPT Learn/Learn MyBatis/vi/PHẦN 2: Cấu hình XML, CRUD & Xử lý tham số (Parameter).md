Tuyệt vời, chúng ta sẽ tiếp tục ngay với **PHẦN 2**.

Ở phần này, chúng ta sẽ đi sâu vào cách thao tác dữ liệu thực tế (CRUD), cách truyền tham số đúng chuẩn và phân biệt hai khái niệm cực kỳ quan trọng trong bảo mật SQL của MyBatis.

---

# PHẦN 2: CẤU HÌNH XML, CRUD & XỬ LÝ THAM SỐ

### 1. Thực hiện CRUD (Create, Read, Update, Delete)

Trong MyBatis, ngoài thẻ `<select>`, chúng ta có `<insert>`, `<update>`, và `<delete>`.

**Lưu ý quan trọng về Transaction:**
Mặc định `SqlSession` trong MyBatis **không tự động commit**. Khi bạn thực hiện Insert/Update/Delete, bạn phải gọi `session.commit()` thủ công, nếu không dữ liệu sẽ không được lưu vào DB.

#### A. Ví dụ Mapper Interface
```java
public interface UserMapper {
    // Thêm mới (Trả về int là số dòng bị ảnh hưởng)
    int insertUser(User user);

    // Cập nhật
    int updateUser(User user);

    // Xóa
    int deleteUser(Integer id);
}
```

#### B. Ví dụ Mapper XML
```xml
<mapper namespace="com.example.mapper.UserMapper">

    <!-- 1. INSERT -->
    <!-- useGeneratedKeys="true": Bảo MyBatis lấy ID tự tăng từ DB -->
    <!-- keyProperty="id": Gán ID đó ngược lại vào field 'id' của object User -->
    <insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
        INSERT INTO users (name, email)
        VALUES (#{name}, #{email})
    </insert>

    <!-- 2. UPDATE -->
    <update id="updateUser">
        UPDATE users
        SET name = #{name},
            email = #{email}
        WHERE id = #{id}
    </update>

    <!-- 3. DELETE -->
    <delete id="deleteUser">
        DELETE FROM users WHERE id = #{id}
    </delete>

</mapper>
```

#### C. Cách gọi trong Java
```java
try (SqlSession session = sqlSessionFactory.openSession()) {
    UserMapper mapper = session.getMapper(UserMapper.class);

    // Thêm mới
    User newUser = new User();
    newUser.setName("Nguyen Van A");
    newUser.setEmail("a@gmail.com");
    
    mapper.insertUser(newUser);
    // QUAN TRỌNG: Phải commit
    session.commit(); 
    
    // Lúc này newUser.getId() sẽ có giá trị (ví dụ: 10) nhờ useGeneratedKeys
    System.out.println("New ID: " + newUser.getId());
}
```

---

### 2. Xử lý tham số (Parameter Handling) - Rất quan trọng

Làm sao để truyền dữ liệu từ Java vào câu SQL?

#### Trường hợp 1: Chỉ có 1 tham số đơn (int, String...)
MyBatis không quan tâm tên tham số.
*   Interface: `User getUser(Integer id);`
*   XML: `WHERE id = #{batKyTenGiCungDuoc}` (Nhưng nên đặt trùng tên cho dễ hiểu).

#### Trường hợp 2: Tham số là một Object (POJO)
MyBatis sẽ tìm các **getter** trong object đó.
*   Interface: `int insertUser(User user);`
*   XML: `VALUES (#{name}, #{email})` -> Nó sẽ gọi `user.getName()` và `user.getEmail()`.

#### Trường hợp 3: Nhiều tham số (Multiple Parameters)
Đây là chỗ người mới hay lỗi. Ví dụ: tìm user theo tên VÀ email.
*   Interface: `User findBy(String name, String email);` -> **SAI** nếu dùng trực tiếp trong XML.

**Cách giải quyết chuẩn (Dùng Annotation `@Param`):**
```java
// Interface
User findBy(@Param("userName") String name, @Param("userEmail") String email);
```

```xml
<!-- XML -->
<select id="findBy" resultType="User">
    SELECT * FROM users 
    WHERE name = #{userName} AND email = #{userEmail}
</select>
```
*MyBatis sẽ dùng key trong `@Param` để map vào `#{}`.*

---

### 3. Sự khác biệt "chết người" giữa `#{}` và `${}`

Đây là câu hỏi phỏng vấn kinh điển và là kiến thức bảo mật bắt buộc.

#### `#{}` (Nên dùng 99% trường hợp)
*   **Cơ chế:** MyBatis tạo ra **PreparedStatement**.
*   **SQL thực tế:** `SELECT * FROM users WHERE id = ?`
*   **An toàn:** Chống được **SQL Injection**. Giá trị được escape tự động.

#### `${}` (String Substitution - Chỉ dùng khi thật sự cần)
*   **Cơ chế:** MyBatis nối chuỗi trực tiếp (Text replacement).
*   **SQL thực tế:** Nếu id là "1", SQL thành `SELECT * FROM users WHERE id = 1`.
*   **Nguy hiểm:** Nếu tham số là `1; DROP TABLE users;`, lệnh xóa bảng sẽ được chạy ngay lập tức.
*   **Khi nào dùng?** Khi bạn muốn động tên bảng hoặc tên cột (thứ mà PreparedStatement không hỗ trợ).
    *   Ví dụ: `ORDER BY ${columnName}` (Sắp xếp động theo cột người dùng chọn).

---

### 4. Xử lý chênh lệch tên cột (CamelCase vs Snake_case)

Database thường đặt tên kiểu `user_id`, `created_at`.
Java thường đặt tên kiểu `userId`, `createdAt`.

Mặc định, MyBatis sẽ không map được `user_id` vào `userId` -> Giá trị sẽ bị `null`.

**Cách khắc phục:**

#### Cách 1: Dùng Alias trong SQL (Thủ công)
```sql
SELECT user_id AS userId, created_at AS createdAt FROM users...
```
*(Cách này mệt mỏi nếu bảng có nhiều cột)*

#### Cách 2: Bật cấu hình `mapUnderscoreToCamelCase` (Khuyên dùng)
Trong file `mybatis-config.xml`:
```xml
<configuration>
    <settings>
        <!-- Tự động map user_id -> userId -->
        <setting name="mapUnderscoreToCamelCase" value="true"/>
    </settings>
    <!-- ... -->
</configuration>
```
Chỉ cần bật dòng này, bạn không cần lo lắng về việc đặt tên nữa, miễn là tuân thủ chuẩn đặt tên.

#### Cách 3: Dùng ResultMap (Mạnh mẽ nhất)
Sẽ được giới thiệu chi tiết ở Phần 3. Dùng khi tên cột và tên biến khác nhau hoàn toàn (ví dụ DB là `u_name` mà Java là `fullName`).

---

### TỔNG KẾT PHẦN 2
Bạn đã nắm được:
1.  Cách viết INSERT, UPDATE, DELETE và lấy ID tự tăng (`useGeneratedKeys`).
2.  Nhớ phải `commit` transaction.
3.  Cách dùng `@Param` khi truyền nhiều tham số.
4.  Tuyệt đối dùng `#{}` để tránh hack, chỉ dùng `${}` để sort hoặc chọn bảng động.
5.  Cấu hình `mapUnderscoreToCamelCase` để code nhàn hơn.
