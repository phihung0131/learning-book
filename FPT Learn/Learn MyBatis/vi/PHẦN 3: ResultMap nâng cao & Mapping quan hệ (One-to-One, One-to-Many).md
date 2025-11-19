Chào bạn, chúng ta tiếp tục với **PHẦN 3**. Đây là phần thể hiện sức mạnh thực sự của MyBatis so với JDBC thuần.

Khi làm việc thực tế, hiếm khi chúng ta chỉ `SELECT *` từ một bảng đơn lẻ. Chúng ta thường phải **JOIN** nhiều bảng (User kèm Address, User kèm danh sách Orders...).

MyBatis giải quyết việc này thông qua thẻ **`<resultMap>`**.

---

# PHẦN 3: RESULTMAP NÂNG CAO & MAPPING QUAN HỆ

### 1. Tại sao cần `<resultMap>`?

Ở phần trước, chúng ta dựa vào auto-mapping (tên cột trùng tên biến). Tuy nhiên, khi JOIN bảng, tên cột có thể trùng nhau (cả bảng User và Order đều có cột `id` và `name`), hoặc cấu trúc Object lồng nhau.

**Giả sử mô hình dữ liệu:**
1.  **User** (id, name)
2.  **Address** (id, city, user_id) -> Một User có **1** Address (**One-to-One**).
3.  **Order** (id, product_name, user_id) -> Một User có **Nhiều** Order (**One-to-Many**).

**Java Class:**
```java
public class User {
    private Integer id;
    private String name;
    
    // Quan hệ 1-1
    private Address address; 
    
    // Quan hệ 1-N
    private List<Order> orders;
}
```

---

### 2. Quan hệ 1-1 (One-to-One) với `<association>`

Để map object con (`Address`) vào trong object cha (`User`), ta dùng thẻ `<association>`.

Có 2 cách làm: **Nested Select** (Select lồng nhau) và **Nested Results** (Dùng Join). Mình sẽ hướng dẫn cách **Nested Results (Dùng Join)** vì nó hiệu năng tốt hơn (chỉ tốn 1 câu query).

#### Câu SQL mong muốn (Join 2 bảng)
```sql
SELECT 
    u.id as user_id, u.name as user_name,
    a.id as addr_id, a.city as addr_city
FROM users u
LEFT JOIN address a ON u.id = a.user_id
WHERE u.id = #{id}
```

#### Cấu hình trong Mapper XML
```xml
<mapper namespace="com.example.mapper.UserMapper">

    <!-- Định nghĩa ResultMap -->
    <resultMap id="UserWithAddressMap" type="com.example.User">
        <!-- Map cho bảng User -->
        <id property="id" column="user_id"/>
        <result property="name" column="user_name"/>

        <!-- Map cho object Address (Quan hệ 1-1) -->
        <!-- property: tên biến trong class User -->
        <!-- javaType: kiểu dữ liệu của biến đó -->
        <association property="address" javaType="com.example.Address">
            <id property="id" column="addr_id"/>
            <result property="city" column="addr_city"/>
        </association>
    </resultMap>

    <!-- Sử dụng ResultMap trong câu Select -->
    <select id="getUserWithAddress" resultMap="UserWithAddressMap">
        SELECT 
            u.id as user_id, u.name as user_name,
            a.id as addr_id, a.city as addr_city
        FROM users u
        LEFT JOIN address a ON u.id = a.user_id
        WHERE u.id = #{id}
    </select>

</mapper>
```

**Lưu ý:** Việc đặt alias (as `user_id`, `addr_id`) là rất quan trọng để MyBatis biết cột nào thuộc về bảng nào.

---

### 3. Quan hệ 1-N (One-to-Many) với `<collection>`

Để map một danh sách (`List<Order>`) vào User, ta dùng thẻ `<collection>`.

#### Câu SQL mong muốn (Join bảng User và Order)
Khi join 1-N, SQL sẽ trả về nhiều dòng dữ liệu trùng lặp thông tin User, nhưng khác thông tin Order. MyBatis sẽ tự động gộp (group) chúng lại thành 1 User duy nhất chứa List Order.

```sql
SELECT 
    u.id as user_id, u.name as user_name,
    o.id as order_id, o.product_name as order_product
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE u.id = #{id}
```

#### Cấu hình Mapper XML
```xml
<mapper namespace="com.example.mapper.UserMapper">

    <resultMap id="UserWithOrdersMap" type="com.example.User">
        <!-- ID của User (Rất quan trọng để MyBatis gom nhóm) -->
        <id property="id" column="user_id"/>
        <result property="name" column="user_name"/>

        <!-- Map danh sách Orders (Quan hệ 1-N) -->
        <!-- property: tên biến list trong User -->
        <!-- ofType: kiểu dữ liệu của từng phần tử trong List -->
        <collection property="orders" ofType="com.example.Order">
            <id property="id" column="order_id"/>
            <result property="productName" column="order_product"/>
        </collection>
    </resultMap>

    <select id="getUserWithOrders" resultMap="UserWithOrdersMap">
        SELECT 
            u.id as user_id, u.name as user_name,
            o.id as order_id, o.product_name as order_product
        FROM users u
        LEFT JOIN orders o ON u.id = o.user_id
        WHERE u.id = #{id}
    </select>

</mapper>
```

**Cơ chế hoạt động:**
Khi SQL trả về:
1.  User A - Order 1
2.  User A - Order 2

MyBatis nhìn thấy cùng `user_id`, nó sẽ tạo ra **1 object User A**, và đẩy cả Order 1, Order 2 vào trong `List<Order>` của object đó.

---

### 4. Tổng hợp: Map cả 3 bảng cùng lúc (Complex ResultMap)

Bạn có thể lồng ghép `association` và `collection` trong cùng một ResultMap.

```xml
<resultMap id="FullUserMap" type="com.example.User">
    <id property="id" column="user_id"/>
    <result property="name" column="user_name"/>
    
    <!-- 1-1 -->
    <association property="address" javaType="com.example.Address">
        <id property="id" column="addr_id"/>
        <result property="city" column="addr_city"/>
    </association>
    
    <!-- 1-N -->
    <collection property="orders" ofType="com.example.Order">
        <id property="id" column="order_id"/>
        <result property="productName" column="order_product"/>
    </collection>
</resultMap>
```

---

### 5. Vấn đề N+1 Query (Cảnh báo)

Ở trên, mình dùng cách **JOIN** (1 câu SQL duy nhất). Tuy nhiên, MyBatis hỗ trợ một cách khác gọi là **Nested Select** (Select lồng).

Ví dụ Nested Select (Cách **KHÔNG NÊN DÙNG** cho danh sách lớn):
```xml
<resultMap id="BadWayMap" type="User">
    <!-- Gọi thêm câu SQL khác để lấy orders -->
    <collection property="orders" column="id" select="selectOrdersByUserId"/>
</resultMap>
```

**Tại sao tệ?**
Nếu bạn `SELECT * FROM users` lấy ra 100 user.
1.  Chạy 1 câu lấy danh sách User.
2.  Với mỗi User, MyBatis chạy thêm 1 câu SQL để lấy Order.
    -> Tổng cộng: 1 + 100 = 101 câu SQL. Đây là lỗi **N+1 Problem** làm chậm hệ thống kinh khủng.

**Lời khuyên:** Luôn ưu tiên dùng cách **JOIN (`<collection>` bên trong ResultMap)** như mục 3 để chỉ tốn 1 câu query, trừ khi bạn dùng Lazy Loading (sẽ nói ở Phần 5).

---

### TỔNG KẾT PHẦN 3
Bạn đã nắm được:
1.  **ResultMap** là công cụ mapping thủ công mạnh mẽ.
2.  `<association>` dùng cho quan hệ 1-1 (Java Type).
3.  `<collection>` dùng cho quan hệ 1-N (List - ofType).
4.  Cần dùng **Alias** trong SQL để tránh trùng tên cột.
5.  Nên dùng kỹ thuật **JOIN** thay vì Select lồng nhau để tối ưu hiệu năng.

---
