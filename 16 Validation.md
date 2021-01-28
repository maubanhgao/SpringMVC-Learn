# Sử dụng Validation trong Spring

[TOC]

## 1. Tại sao cần kiểm tra và ràng buộc dữ liệu 

Cái quý giá và quan trọng nhất đối với một phần mềm đó chính là dữ liệu, thông tin về dữ liệu. Như các e thấy facebook hay google họ nắm dữ một lượng người dùng khá lớn , dự vào nguồn dữ liệu đó họ sẽ phát triển các kế hoạch dài hạn và tăng doanh thu cho công ty . Chính vì vậy nắm được dữ liệu sẽ là yếu tố quyết định cho sự thành công. Để đảm bảo dữ liệu phải nhập đúng định dạng ví dụ như ngày tháng năm phải theo chuẩn la dd/MM/YYYY hoặc trường dữ liệu bắt buộc người dùng nhập vào thì ta sử dụng **Spring Validation** để làm việc đó. Khi người dùng nhập sai định dạnh mình yêu cầu thì mình thông báo lỗi để người dùng nhập lại.

## 2. Hướng dẫn cách làm 

- Bước 1 : Nhúng thư viện vào trong file pom

```xml
<dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency> 
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency> 
```

- Bước 2 : Thiết ràng buộc dữ liệu trong Entiry

```java
@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;

    @NotBlank(message = "Name is mandatory")
    private String name;

    @NotBlank(message = "Email is mandatory")
    private String email;

    // standard constructors / setters / getters / toString

}
```

Như ta thấy mình sử dụng các annotaion có sẳng như **@NotBlank** để ràng buộc không được phép rỗng cho giá trị name.

- Bước 3 : Sử dụng trong controller

Trong ví dụ sau anh sẽ sử dụng Restful Webservice .

```java
@RestController
public class UserController {

    @PostMapping("/users")
    ResponseEntity<String> addUser(@Valid @RequestBody User user) {
        // persisting the user
        return ResponseEntity.ok("User is valid");
    }

    // standard constructors / other methods

}
```

Như ta thấy trong đoạn code trên ta sử dụng **@Valid** để kiểm tra dữ liệu người dùng truyền lên có thảo mảng điều kiện ta thiết lập trong Entiry User không ? Khi tham số trong controller có annotation @Valid nó sẽ tự động bật chế độ kiểm tra dữ liệu theo chuẩn JSR 380 cái mà cài đặt chứa năng kiểm tra trong thư viện Hibernate Validator để kiểm tra giá trị.


Chúng ta có thể sử dụng annotation **@ExceptionHandler** cho phép chúng ta bắt lỗi dữ liệu cho từng method.

```java
@ResponseStatus(HttpStatus.BAD_REQUEST)
@ExceptionHandler(MethodArgumentNotValidException.class)
public Map<String, String> handleValidationExceptions(
  MethodArgumentNotValidException ex) {
    Map<String, String> errors = new HashMap<>();
    ex.getBindingResult().getAllErrors().forEach((error) -> {
        String fieldName = ((FieldError) error).getField();
        String errorMessage = error.getDefaultMessage();
        errors.put(fieldName, errorMessage);
    });
    return errors;
}
```

## 4. Một số annotaion thường dùng để kiểm tra dữ liệu 

- @NotNull – kiểm tra giá trị null
- @AssertTrue – kiểm tra giá trị thuộc tính là true
- @Size – kiểm tra độ dài min and max
- @Min – kiểm tra giá trị nhỏ nhất
- @Max – Kiểm tra giá trị lớn nhất
- @Email – kiểm tra email có hợp lệ
- @NotEmpty – kiểm tra không được trống và empty
- @NotBlank – kiểm tra giá trị không được null hoặc khoảng trắng
- @Positive and @PositiveOrZero – kiểm tra chỉ được phép là số nguyên dương từ 0 trở đi
- @Negative and @NegativeOrZero – kiểm tra số âm
- @Past and @PastOrPresent – kiểm tra ngày từ quá khứ đến hiện tại.
- @Future and @FutureOrPresent – kiểm tra ngày từ hiện tại đến tương lai

## 5. Tự định nghĩa annotation riêng 

Trong ví dụ sau anh sẽ tạo một annotation riêng tên @Author là để kiểm tra trường name phải nhập vào giá trị tên mà anh mong muốn nến trường name chứa đựng giá trị anh không mong muốn thì sẽ báo lỗi như sau.

```java
@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;

    @NotBlank(message = "Name is mandatory")
    @Author
    private String name;

    @NotBlank(message = "Email is mandatory")
    private String email;

}
```

Tiếp đến anh định nghĩa của @Author như sau

```java
@Target({FIELD})
@Retention(RUNTIME)
@Constraint(validatedBy = AuthorValidator.class)
@Documented
public @interface Author {

    String message() default "Author is not allowed.";

    Class<?>[] groups() default {};

    Class<? extends Payload>[] payload() default {};

}
```

Và cuối cùng anh định nghĩa thế nào là hợp lệ là đúng. Như ví dụ dưới đây anh định nghĩa trường tên tác giả truyền vào phải nằm trong danh sách mà anh định nghĩa là “Santideva”, “Marie Kondo”, “Martin Fowler”, “levunguyen”. Nếu tên tác giả không nằm trong danh sách này thì xem như không hợp lệ và trả về lỗi

```java
public class AuthorValidator implements ConstraintValidator<Author, String> {

    List<String> authors = Arrays.asList("Santideva", "Marie Kondo", "Martin Fowler", "levunguyen");

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {

        return authors.contains(value);

    }
}
```

## 6. Tổng kết

Chúng ta có thể sử dụng các annotaion sẳn có hoặc có thể tự định nghĩa một cái cho phù hợp với ứng dụng của mình.

