# Sử dụng Spring Data JPA trong SpringBoot

[TOC]

## 1. ORM là gì ? 

**ORM** **là viết tắt của Object Relational Mapping, là một quá trình ánh xạ (chuyển đổi) dữ liệu từ ngôn ngữ** **hướng đối tượng** **sang** **Database** **quan hệ và ngược lại. ORM giúp mình ánh xạ các** **tables,column,kiểu dữ liệu và mối quan hệ** (1-1,1-n,n-n) trong database thành các Class và thuộc tính trong Java. Anh lấy ví dụ. Trong database mình có table (person) và các trường (id kiểu Integer , name kiểu varchar ) như sau.

```mysql
CREATE TABLE persons (
    id integer NOT NULL,
    name varchar(50) NOT NULL, salary float, PRIMARY KEY(id)
);
```

**Như vậy ORM nghĩa là tương ứng với table person trong database mình ánh xạ nó trong Class Java (POJO) như sau cho tương ứng.**

```java
public class Person {
    public String name;
    public float salary;
    public Person(String name) { ... }
}
```

Như vậy trong database có gì, thì Class Java sẽ mô tả lại y chang vậy. Sau đây là bản mapping các kiểu dữ liệu trong mysql tương ứng với kiểu Java

![](D:\data\source\Hoc lap trinh web\SpringMVC\Learn Spring MVC\Learn 01\img\mysql-java.jpg)

## 2. Một số ORM Framework 

Trong **Spring** thì thường mình hay sử trong các dự án Java được cung cấp bởi nhà cung cấp sau.

- JPA
- Hibernate
- OpenJPA
- EclipseLink
- Apache Cayenne

## 3. JPA là gì ? 

**JPA** viết tắc của từ Java Persitent API . Tầng Persistent có nhiệm vụ thao tác với database như query lấy dữ liệu , lưu dữ liệu xuống database . JPA cung cấp cho mình cơ chế ORM mapping các bảng, column , mối quan hệ trong database thành các lớp java và đồng thời cung cấp cho mình các method cần thiết để thao tác dữ liệu trong database.

## 4. Vai trò của tầng Persistent 

![](D:\data\source\Hoc lap trinh web\SpringMVC\Learn Spring MVC\Learn 01\img\persistentlayer.jpg)

1. Như ta thấy ở hình trên, đó chính là luồng đi của một ứng dụng . Bắt đầu khi người dùng gửi request lên server.
2. Khi request vào Dispatcher nó sẽ đưa đến Controller tương ứng để xử lý request
3. Từ Controller nó sẽ gọi xuống Service để thực hiện các nghiệp vụ cần thiết
4. Từ tầng Service nó gọi tầng Persisten (Trong các dự án mình sử dụng JPA) để thực hiện các thao tác xuống database và trả kết quả về

## 5. Hướng dẫn sử dụng JPA thông qua ví dụ

Sau đây mình sẽ làm một ứng dụng đơn giản để lấy dữ liệu từ **database** và trả kết quả về cho người dùng. Và mình sẽ sử dụng thư viện spring-data-jpa để kết nối và thao tác với database. Ngoài ra mọi người có thể xem qua bài viết Hibernate mà anh đã viết để thao tác với database nhé. Source code [tại đây ](https://github.com/codegymdanang/CGDN-SpringBoot-JPA).

Bước 1 - Chuẩn bị dependency trong file pom.xml

```xml
   <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>

         <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <scope>runtime</scope>
         </dependency>
```

Bước 2 - Cấu hình connection kết nối database trong file application.properties

```properties
spring.datasource.driver-class-name=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/company
spring.datasource.username=root
spring.datasource.password=abc  
```

Bước 3 - Chuẩn bị entiry . Mapping table Department trong database thành các class Java

```java
@Data
@Entity
@Table(name = "department")
@NamedQuery( name="findAllCustomersWithName",
        query="SELECT c FROM Customer c WHERE c.name LIKE :custName" )
public class Department implements Serializable {

    @Column(name = "id")
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    @Id
    public int id;

    @Column(name = "name")
    public String name;

    @Column(name = "description")
    public String description;


}
```

#### Bước 4 - Chuẩn bị Controller để mapping request từ client

```java
@Controller
public class CreationQueryController {


    @Autowired
    DepartmentQueryCreationService departmentQueryCreationService;

    @GetMapping("/creationFindbyDepartmentName/{name}")
    public ModelAndView findbyDepartment(@PathVariable("name") String name) {
        ModelAndView modelAndView = new ModelAndView("creation");
        List<Department> departments = departmentQueryCreationService.findByDepartment(name);
        modelAndView.addObject("departments",departments);
        return modelAndView;

    }
}
```

Bước 5 - Tạo file DepartmentQueryCreationService Service

Service có nhiệm vụ thực hiện các nghiệp vụ của ứng dụng . Đồng thời nhúng bean Repository để gọi tầng Persistence.

```java
@Service
public class DepartmentQueryCreationService {

    @Autowired
    DepartmentQueryCreationRepository departmentQueryCreationRepository;

    public List<Department> findByDepartment(String name) {
        return departmentQueryCreationRepository.findByName(name);
    }
}
```

Bước 6 - Tạo file DepartmentAnnotationRepository sử dụng JPA

Tầng này có nhiệm vụ thao tác lấy dữ liệu. Các cách lấy dữ liệu sẽ được giới thiệu riêng ở bài khác.

```java
@Transactional
public interface DepartmentAnnotationRepository extends JpaRepository<Department,Integer> {

    @Query("select department from Department department where department.name = ?1")
    Department findByName(String departmentName);

    @Query("select department from Department department where department.name like %?1")
    List<Department> findByFirstnameEndsWith(String departmentName);

    @Query(value = "select department from Department department where department.name = ?1", nativeQuery = true)
    Department findByName2(String departmentName);
}
```

Bước 7 - Luồng đi của ứng dụng trên như sau

1. Người dùng gõ vào link là http://localhost8080/creationFindbyDepartmentName/java
2. Request trên sẽ được Controller CreationQueryController xử lý nhờ cơ chế mapping
3. Trong CreationQueryController ta nhúng DepartmentQueryCreationService để gọi hàm từ service này
4. DepartmentQueryCreationService có nhiệm vụ thực hiện các nghiệp vụ của chương trình đồng thời nhúng DepartmentQueryCreationRepository để gọi hàm từ DepartmentQueryCreationRepository.
5. DepartmentQueryCreationRepository có nhiệm vụ truy vấn dữ liệu trong database và trả kết quả lại cho Service. Service trả kết quả cho Controller . Cuối cùng từ Controller trả kết quả cho client.

##### Như vậy ta có thể phân ra thành 2 luồng chính

Luồng 1 : Lấy dữ liệu mình bắt đầu từ Client .
Client -> Controller -> Service -> JPA -> Query database .
Luống 2 : Trả dữ liệu từ Database .
Database -> JPA -> Service -> Controller -> Client .

## **6. Kết luận**

Tổng hợp các các cách query xuống database .

1. Sử dụng Query Creation
2. Sử dụng @Query (ở ví dụ trên khi ta dùng @Query)
3. Sử dụng @NameQuery
4. Sử dụng EntityManager

## Tại sao mình cần JPA

1. Chúng ta chỉ tập trung vào viết chức năng của chương trình còn các việc như quản lý connection , cách query thì JPA sẽ lo
2. Khả năng thay đổi database không bị ảnh hưởng . Ví du hôm nay ta dùng Mysql ngày mai ta dùng Postgres thì không ảnh hưởng tới chương trình của mình.