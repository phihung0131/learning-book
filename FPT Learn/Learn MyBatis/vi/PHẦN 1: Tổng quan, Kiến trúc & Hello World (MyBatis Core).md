# PHẦN 1: TỔNG QUAN, KIẾN TRÚC & HELLO WORLD

### 1. MyBatis là gì?

MyBatis là một **Persistence Framework** (khung làm việc với dữ liệu bền vững) cho Java.
*   Nó không phải là một ORM (Object-Relational Mapping) hoàn toàn tự động như Hibernate/JPA.
*   Nó được gọi là **SQL Mapper**: Nó giúp ánh xạ (map) các câu lệnh SQL thủ công vào các Java Object (POJO).

**Sự khác biệt cốt lõi:**
*   **Hibernate:** Bạn thao tác với Java Object, Hibernate tự sinh ra SQL. (Tốt cho CRUD đơn giản).
*   **MyBatis:** Bạn viết SQL, MyBatis giúp bạn chạy và hứng kết quả vào Java Object. (Tốt cho các câu query phức tạp, cần tối ưu hiệu năng cao).

### 2. Tại sao chọn MyBatis?
*   **Kiểm soát hoàn toàn SQL:** Bạn biết chính xác câu lệnh nào được gửi xuống Database.
*   **Dễ học:** Không có quá nhiều "ma thuật" ẩn bên dưới như Hibernate.
*   **Hiệu năng:** Rất nhanh vì không tốn chi phí phân tích object graph phức tạp.
*   **Dễ debug:** Lỗi SQL sai thì sửa SQL, không cần đoán xem Framework đang sinh ra cái gì.

### 3. Kiến trúc cốt lõi (Core Components)

Để chạy MyBatis, bạn cần hiểu vòng đời của 4 thành phần sau:

1.  **SqlSessionFactoryBuilder:**
    *   Dùng để đọc file cấu hình (XML) và tạo ra `SqlSessionFactory`.
    *   *Vòng đời:* Dùng 1 lần rồi vứt bỏ.
2.  **SqlSessionFactory:**
    *   Nhà máy tạo ra các `SqlSession`.
    *   *Vòng đời:* Tồn tại suốt vòng đời ứng dụng (Singleton). Chỉ cần 1 cái cho toàn app.
3.  **SqlSession:**
    *   Đại diện cho một kết nối đến Database. Dùng để thực thi câu lệnh SQL, commit, rollback.
    *   *Vòng đời:* **Mỗi request 1 cái**. Dùng xong **PHẢI ĐÓNG** (close) ngay. Không được share giữa các luồng (Thread-unsafe).
4.  **Mapper (Interface):**
    *   Các interface Java chứa các phương thức tương ứng với SQL.

---

### 4. Ví dụ "Hello World" (Sử dụng MyBatis thuần - Không Spring)

Để hiểu bản chất, chúng ta sẽ code thuần trước khi dùng Spring Boot.

#### Bước 1: Cài đặt thư viện (Maven)
Trong file `pom.xml`:

```xml
<dependencies>
    <!-- MyBatis Core -->
    <dependency>
        <groupId>org.mybatis</groupId>
        <artifactId>mybatis</artifactId>
        <version>3.5.13</version>
    </dependency>
    
    <!-- Database Driver (Ví dụ MySQL) -->
    <dependency>
        <groupId>mysql</groupId>
        <artifactId>mysql-connector-java</artifactId>
        <version>8.0.33</version>
    </dependency>
</dependencies>
```

#### Bước 2: Tạo Database & POJO
Giả sử có bảng `users` (id, name, email).

Class Java:
```java
public class User {
    private Integer id;
    private String name;
    private String email;
    
    // Getter, Setter, toString...
}
```

#### Bước 3: Cấu hình chính (mybatis-config.xml)
Tạo file này trong thư mục `src/main/resources`. Đây là nơi khai báo kết nối DB.

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- Môi trường (Dev, Prod...) -->
    <environments default="development">
        <environment id="development">
            <!-- Quản lý Transaction -->
            <transactionManager type="JDBC"/>
            <!-- Cấu hình DataSource -->
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.cj.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis_demo"/>
                <property name="username" value="root"/>
                <property name="password" value="password"/>
            </dataSource>
        </environment>
    </environments>

    <!-- Đăng ký các file Mapper -->
    <mappers>
        <mapper resource="UserMapper.xml"/>
    </mappers>
</configuration>
```

#### Bước 4: Tạo Mapper (XML + Interface)

**UserMapper.java (Interface):**
```java
public interface UserMapper {
    User getUserById(Integer id);
}
```

**UserMapper.xml (File chứa SQL):** (Đặt trong resources)
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
  PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<!-- namespace phải trỏ đúng đến Interface -->
<mapper namespace="com.example.mapper.UserMapper">

    <!-- id phải trùng tên method trong Interface -->
    <!-- resultType là class Java sẽ nhận dữ liệu -->
    <select id="getUserById" resultType="com.example.User">
        SELECT id, name, email 
        FROM users 
        WHERE id = #{id}
    </select>

</mapper>
```

#### Bước 5: Chạy thử (Main Class)

```java
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import java.io.InputStream;

public class MainApp {
    public static void main(String[] args) throws Exception {
        // 1. Đọc file cấu hình
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);

        // 2. Tạo SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

        // 3. Mở SqlSession (Dùng try-with-resources để tự động đóng session)
        try (SqlSession session = sqlSessionFactory.openSession()) {
            
            // 4. Lấy Mapper Interface
            UserMapper mapper = session.getMapper(UserMapper.class);
            
            // 5. Gọi hàm thực thi SQL
            User user = mapper.getUserById(1);
            
            System.out.println("User Found: " + user.getName());
        }
    }
}
```

---

### TỔNG KẾT PHẦN 1
Bạn đã nắm được:
1.  MyBatis là SQL Mapper.
2.  Cấu hình `mybatis-config.xml` để kết nối DB.
3.  Cách tạo Mapper Interface và file XML chứa SQL.
4.  Cách dùng `SqlSession` để chạy câu lệnh.

**Lưu ý quan trọng:**
*   `#{id}` trong XML: Đây là cách MyBatis binding tham số an toàn (tránh SQL Injection).

