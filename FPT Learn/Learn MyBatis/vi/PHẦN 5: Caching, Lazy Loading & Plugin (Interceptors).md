Chào bạn, chúng ta tiếp tục với **PHẦN 5**.

Phần này tập trung vào **Hiệu năng (Performance)** và **Khả năng mở rộng**. Khi hệ thống lớn lên, việc tối ưu số lượng câu query và can thiệp sâu vào quy trình chạy SQL là rất quan trọng.

---

# PHẦN 5: CACHING, LAZY LOADING & PLUGINS

### 1. Caching trong MyBatis (Bộ nhớ đệm)

MyBatis có 2 cấp độ cache để giảm tải cho Database.

#### A. Level 1 Cache (Local Cache) - Mặc định
*   **Phạm vi:** Trong một `SqlSession` (một request/transaction).
*   **Cơ chế:** Mặc định luôn **BẬT**. Bạn không thể tắt nó (trừ khi cấu hình `statement` cụ thể).
*   **Hoạt động:**
    1.  Bạn gọi `userMapper.getById(1)`. MyBatis xuống DB lấy, lưu vào RAM.
    2.  Trong cùng session đó, bạn gọi `userMapper.getById(1)` lần nữa. MyBatis trả về ngay từ RAM, không query DB.
*   **Lưu ý:** Khi bạn gọi `INSERT`, `UPDATE`, `DELETE` hoặc `commit()`, cache cấp 1 sẽ bị xóa sạch để đảm bảo dữ liệu mới nhất.

#### B. Level 2 Cache (Global Cache)
*   **Phạm vi:** Theo **Namespace** (Mapper). Dữ liệu được chia sẻ giữa nhiều `SqlSession` khác nhau.
*   **Cơ chế:** Mặc định **TẮT**.
*   **Khi nào dùng:** Dữ liệu ít thay đổi (như danh mục, tỉnh thành, cấu hình).

**Cách bật L2 Cache:**

1.  Trong `mybatis-config.xml`:
    ```xml
    <settings>
        <setting name="cacheEnabled" value="true"/>
    </settings>
    ```
2.  Trong file Mapper XML (ví dụ `UserMapper.xml`), thêm thẻ `<cache/>`:
    ```xml
    <mapper namespace="...">
        <!-- Bật cache cho mapper này -->
        <cache eviction="FIFO" flushInterval="60000" size="512" readOnly="true"/>
    </mapper>
    ```
3.  **Yêu cầu bắt buộc:** Class POJO (`User`) phải implements `java.io.Serializable`.

> **Lời khuyên thực tế:** Trong các dự án hiện đại (đặc biệt là Microservices), người ta **ít dùng MyBatis L2 Cache**. Thay vào đó, họ dùng Redis hoặc Memcached ở tầng Service để kiểm soát tốt hơn.

---

### 2. Lazy Loading (Tải lười)

Ở Phần 3, mình có nói về việc Query lồng (Nested Select) gây ra vấn đề N+1. Tuy nhiên, Query lồng lại là điều kiện bắt buộc để dùng **Lazy Loading**.

**Kịch bản:**
Bạn load thông tin `User`. User có danh sách `Orders` rất nặng.
*   Nếu dùng **Eager Loading** (mặc định): Load User xong load luôn Orders -> Chậm.
*   Nếu dùng **Lazy Loading**: Load User trước. Khi nào code gọi `user.getOrders()` thì mới chạy câu SQL lấy Order.

**Cấu hình bật Lazy Loading:**
Trong `mybatis-config.xml`:
```xml
<settings>
    <!-- Bật tính năng tải lười -->
    <setting name="lazyLoadingEnabled" value="true"/>
    <!-- aggressiveLazyLoading = false: Chỉ load property được gọi, không load hết cả object -->
    <setting name="aggressiveLazyLoading" value="false"/>
</settings>
```

**Cách dùng trong Mapper:**
Bạn **phải** dùng cách **Select Lồng** (không dùng JOIN) thì Lazy Loading mới hoạt động.

```xml
<!-- 1. Select User -->
<select id="getUser" resultMap="UserMap">
    SELECT * FROM users WHERE id = #{id}
</select>

<!-- 2. Select Orders (Chỉ chạy khi cần) -->
<select id="getOrdersByUserId" resultType="Order">
    SELECT * FROM orders WHERE user_id = #{userId}
</select>

<resultMap id="UserMap" type="User">
    <id property="id" column="id"/>
    <!-- fetchType="lazy" đè cấu hình mặc định -->
    <collection property="orders" column="id" 
                select="getOrdersByUserId" fetchType="lazy"/>
</resultMap>
```

---

### 3. Plugins (Interceptors) - Can thiệp sâu

MyBatis cho phép bạn chặn (intercept) 4 đối tượng cốt lõi để chèn logic riêng (Ví dụ: Log SQL, đo thời gian chạy, mã hóa dữ liệu, phân trang).

4 đối tượng có thể chặn:
1.  **Executor:** Thực thi update/query, commit/rollback.
2.  **ParameterHandler:** Set tham số.
3.  **ResultSetHandler:** Xử lý kết quả trả về.
4.  **StatementHandler:** Chuẩn bị câu lệnh JDBC.

#### Ví dụ: Plugin đo thời gian thực thi SQL
Đây là cách tốt nhất để debug xem câu SQL nào chạy chậm.

```java
import org.apache.ibatis.executor.statement.StatementHandler;
import org.apache.ibatis.plugin.*;
import java.sql.Connection;
import java.util.Properties;

// Khai báo chặn method 'prepare' của StatementHandler
@Intercepts({
    @Signature(type = StatementHandler.class, method = "prepare", args = {Connection.class, Integer.class})
})
public class PerformanceInterceptor implements Interceptor {

    @Override
    public Object intercept(Invocation invocation) throws Throwable {
        long start = System.currentTimeMillis();
        
        // Cho phép MyBatis chạy logic gốc
        Object result = invocation.proceed();
        
        long end = System.currentTimeMillis();
        System.out.println("SQL Time: " + (end - start) + "ms");
        
        return result;
    }

    @Override
    public Object plugin(Object target) {
        return Plugin.wrap(target, this);
    }

    @Override
    public void setProperties(Properties properties) {}
}
```

**Đăng ký Plugin trong `mybatis-config.xml`:**
```xml
<plugins>
    <plugin interceptor="com.example.plugin.PerformanceInterceptor"/>
</plugins>
```

#### Ứng dụng nổi tiếng nhất: PageHelper
Có một thư viện cực nổi tiếng tên là **PageHelper**. Nó dùng cơ chế Plugin này để tự động chèn `LIMIT` và `OFFSET` vào câu SQL của bạn để làm phân trang mà bạn không cần sửa file XML.
*   Code: `PageHelper.startPage(1, 10);`
*   Sau dòng này, câu query tiếp theo tự động bị can thiệp để phân trang.

---

### TỔNG KẾT PHẦN 5
Bạn đã biết:
1.  **L1 Cache**: Tự động, giúp nhanh trong 1 request.
2.  **L2 Cache**: Chia sẻ giữa các request, ít dùng trực tiếp (thường dùng Redis).
3.  **Lazy Loading**: Chỉ dùng khi cần tải dữ liệu quan hệ nặng, yêu cầu phải dùng Nested Select.
4.  **Plugin**: Kỹ thuật nâng cao để viết thư viện tiện ích (Log, Phân trang, Mã hóa).
