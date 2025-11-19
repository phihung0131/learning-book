Chào bạn, chúng ta đã đi đến chặng cuối cùng: **PHẦN 6**.

Đây là phần quan trọng nhất để bạn áp dụng kiến thức vào các dự án thực tế đi làm. Hiện nay, hiếm ai cấu hình MyBatis thủ công (như Phần 1), mà 99% sẽ tích hợp vào **Spring Boot**.

Ngoài ra, mình sẽ giới thiệu **MyBatis-Plus** – một thư viện mở rộng "quốc dân" giúp bạn code nhanh như gió (giảm 90% lượng code lặp đi lặp lại).

---

# PHẦN 6: MYBATIS VỚI SPRING BOOT & MYBATIS-PLUS

### A. Tích hợp MyBatis với Spring Boot

Khi dùng Spring Boot, bạn được giải phóng khỏi:
1.  Không cần tạo `SqlSessionFactory` thủ công.
2.  Không cần `openSession()`, `close()`, `commit()`. Spring quản lý Transaction tự động.
3.  Cấu hình cực gọn trong `application.properties`.

#### 1. Cài đặt Dependency
Trong `pom.xml`:

```xml
<!-- MyBatis Starter cho Spring Boot -->
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.3</version> <!-- Chọn version phù hợp Spring Boot -->
</dependency>

<!-- Driver MySQL -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
```

#### 2. Cấu hình (application.properties)
Thay vì file XML dài dòng, ta cấu hình như sau:

```properties
# Kết nối Database
spring.datasource.url=jdbc:mysql://localhost:3306/mydb
spring.datasource.username=root
spring.datasource.password=123456

# MyBatis Config
# 1. Chỉ đường dẫn đến file XML chứa SQL
mybatis.mapper-locations=classpath:mapper/*.xml
# 2. Bật log SQL (để debug)
mybatis.configuration.log-impl=org.apache.ibatis.logging.stdout.StdOutImpl
# 3. Tự động map user_id -> userId
mybatis.configuration.map-underscore-to-camel-case=true
# 4. Alias package (để trong XML chỉ cần ghi resultType="User" thay vì com.example...User)
mybatis.type-aliases-package=com.example.entity
```

#### 3. Cách code Mapper và Service

**Mapper Interface:**
Cần thêm annotation `@Mapper` (hoặc dùng `@MapperScan` ở main class).

```java
@Mapper
public interface UserMapper {
    User findById(Long id); // Tự tìm trong XML
}
```

**Service:**
Sử dụng `@Autowired` để tiêm Mapper vào.

```java
@Service
public class UserService {
    
    @Autowired
    private UserMapper userMapper;

    // Spring tự quản lý Transaction (Tự commit/rollback nếu lỗi)
    @Transactional 
    public User getUser(Long id) {
        return userMapper.findById(id);
    }
}
```

---

### B. MyBatis-Plus (MP) - "Vũ khí tối thượng"

**Vấn đề của MyBatis thường:**
Với mỗi bảng (User, Order...), bạn phải hì hục viết lại các câu SQL cơ bản: `INSERT`, `UPDATE`, `DELETE`, `SELECT *`. Việc này rất nhàm chán và tốn thời gian.

**MyBatis-Plus giải quyết thế nào?**
Nó cung cấp sẵn một Interface tên là `BaseMapper`. Chỉ cần kế thừa nó, bạn có ngay full bộ CRUD mà **không cần viết một dòng SQL nào**.

> *Lưu ý: MyBatis-Plus hoàn toàn tương thích với MyBatis. Bạn vẫn có thể viết SQL phức tạp trong XML như bình thường.*

#### 1. Cài đặt (Thay thế dependency ở trên)
Bỏ `mybatis-spring-boot-starter`, thay bằng:

```xml
<dependency>
    <groupId>com.baomidou</groupId>
    <artifactId>mybatis-plus-boot-starter</artifactId>
    <version>3.5.3.1</version>
</dependency>
```

#### 2. Entity (Mapping với Annotations)

```java
@TableName("users") // Tên bảng trong DB
public class User {
    @TableId(type = IdType.AUTO) // Khóa chính tự tăng
    private Long id;
    
    private String name;
    private String email;
    
    @TableField(exist = false) // Cột này không có trong DB
    private String tempField;
}
```

#### 3. Mapper (Sức mạnh thực sự)
Chỉ cần extends `BaseMapper<T>`:

```java
@Mapper
public interface UserMapper extends BaseMapper<User> {
    // Không cần viết gì cả!
    // Đã có sẵn: insert, deleteById, updateById, selectById, selectList...
}
```

#### 4. Sử dụng Wrapper (Query không cần SQL)
Đây là tính năng "ăn tiền" nhất. Bạn xây dựng câu `WHERE` bằng code Java (Type-safe), không sợ gõ sai tên cột.

```java
@Service
public class UserService {
    @Autowired
    private UserMapper userMapper;

    public void demoMyBatisPlus() {
        // 1. Insert
        User u = new User();
        u.setName("New Name");
        userMapper.insert(u); // Tự sinh SQL Insert

        // 2. Select với điều kiện phức tạp (LambdaQueryWrapper)
        // SQL tương đương: SELECT * FROM users WHERE name LIKE '%Tuan%' AND age > 18
        LambdaQueryWrapper<User> query = new LambdaQueryWrapper<>();
        query.like(User::getName, "Tuan")
             .gt(User::getAge, 18);
             
        List<User> list = userMapper.selectList(query);
    }
}
```

---

### TỔNG KẾT TOÀN BỘ KHÓA HỌC

Chúc mừng bạn! Bạn đã đi qua toàn bộ kiến thức cốt lõi của hệ sinh thái MyBatis.

1.  **MyBatis Core:** Hiểu bản chất `SqlSession`, mapping SQL thủ công.
2.  **XML Mapping:** Thành thạo `resultMap`, tham số `#{}`.
3.  **Dynamic SQL:** Dùng `<if>`, `<where>`, `<foreach>` để tạo SQL linh hoạt.
4.  **Advanced:** Hiểu về N+1, Lazy Loading, Caching.
5.  **Modern Stack:** Tích hợp Spring Boot và sử dụng **MyBatis-Plus** để tăng tốc độ development.

**Lời khuyên cuối cùng:**
*   Nếu dự án cần CRUD đơn giản: Dùng **JPA/Hibernate** hoặc **MyBatis-Plus**.
*   Nếu dự án cần báo cáo phức tạp, SQL cực khó, tối ưu query từng chút một: **MyBatis (XML)** là vô địch.
*   Trong thực tế: Người ta thường dùng kết hợp **MyBatis-Plus** (để làm CRUD nhanh) + **File XML** (để viết các câu query báo cáo khó).