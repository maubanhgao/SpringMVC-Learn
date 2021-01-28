# Sử dụng @OneToOne trong spring jpa

[TOC]

## 1. One To Many annotation 

Một nhân viên chỉ có một địa chỉ duy nhất. Như vậy khi ta thực hiện câu query, nếu ta lấy được nhân viên thì sẽ lấy được địa chỉ của nhân viên đó.

Nếu ta thiết kết database thì ta có 2 bảng là user và address như sau.

```mysql
CREATE TABLE `address` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  PRIMARY KEY (`cart_id`)
  'street' varchar(45),
  'city' varchar(45)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8;

CREATE TABLE `user` (
  `id` int(11) unsigned NOT NULL AUTO_INCREMENT,
  `phone` int(11),
  'name' varchar(45)
  KEY `adress_id` (`user_address`),
  CONSTRAINT `usser_address` FOREIGN KEY (`id`) REFERENCES `Address` (`address_id`)
) ENGINE=InnoDB AUTO_INCREMENT=7 DEFAULT CHARSET=utf8;
```

Ta tạo 2 tables là user và address. Trong đó ta có trường addressid trong table user là khoá phụ liên kết đến bảng Address

## 2. Triển khai trong Java

**Đầu tiên mình tạo Class User và sử dụng annotation** **@OneToOne** để nói rằng. Một user chỉ có một địa chỉ duy nhất.

```java
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id")
    private Long id;
    //...

    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "address_id", referencedColumnName = "id")
    private Address address; // biến address này sẽ trùng  với giá trị  mappedBy trong Class User 

    // ... getters and setters
}
```

**@Id** : dùng để chỉ ra đây chính là khoá chính, như vậy khoá chính trong table User sẽ được ánh xạ vào biên Long id.

**@GeneratedValue**(strategy = GenerationType.AUTO) : Đây là annotation để mình tăng tự động thứ tự các dòng trong table. Ví dụ như trong table dòng số 1 là id = 1. Khi ta insert thêm một dòng dữ liệu nữa thì id sẽ tự động tăng lên = 2. Có rất nhiều cách để tăng tự động giá trị của khoá chính.

Các em có thể tham khảo thêm các cách **tăng giá trị ở khoá chính** khác nhau tại [đây](https://levunguyen.com/laptrinhspring/2020/04/25/generation-identifier/)

Như các em thấy ở trên ta sử dụng annotation **@OneToOne** để nói rằng một user chỉ có 1 đối tượng Address .

Tiếp đến **cascade = CascadeType.ALL** nghĩa là khi xoá một dòng dữ liệu trong table Address. Thì bên bản User cũng sẽ bị xoá 1 đòng tương ứng với dòng bị xoá bên table User . Như vậy dữ liệu ở 2 table User và Address dữ liệu sẽ giống nhau. Mục đích của Casecade là để toàn vẹn dữ liệu, dữ liệu sẽ thống nhất ở 2 bảng,tránh thừa dữ liệu không cần thiết.

Điều gì sẽ xảy ra nếu ta không dùng cascade. Anh lấy ví dụ ta có các bảng ghi sau trong table User

| id   | name         | phone      | adress_id |
| ---- | ------------ | ---------- | --------- |
| 1    | Nguyễn Văn A | 0905500505 | 1         |
| 2    | Trần Văn B   | 0905500506 | 2         |
| 3    | Tôn Đức C    | 0905500507 | 3         |
| 4    | Quang Viet   | 0905500508 | 4         |

Anh lấy ví dụ ta có các bảng ghi sau trong table Adress

| id   | name          | city    |
| ---- | ------------- | ------- |
| 1    | Lê Lợi        | Da Nẵng |
| 2    | Trần Phú      | Da Nẵng |
| 3    | Tôn Đức Thắng | Da Nẵng |
| 4    | Quang Trung   | Da Nẵng |

Nếu không có sử dụng cascade thì khi anh xoá 1 dòng dữ liệu trong bảng Address ví dụ như anh xoá dòng 1 (Lê Lợi) thì dòng dữ liệu Nguyễn Văn A trong bản User vẫn tồn tại. Nhưng lúc này giá trị là vô nghĩa vì dữ liệu address (Lê Lợi) không có tồn tại nữa trong cơ sở dữ liệu, lúc này dữ liệu mình bị dư thừa.

Nếu anh sử dụng cascade. Thì khi anh xoá dòng 1 (Lê Lợi) bên table Adress thì tự động dữ liệu Nguyễn Văn A cũng sẽ bị xoá đi. Như vậy giúp mình đồng bộ dữ liệu giữa 2 tables. Khi nào dữ liệu Adress bị xoá thì nó sẽ xoá luôn các dữ liệu bên bảng User (nếu dòng dữ liệu bên bảng User có liên kết với address bị xoá đi).

Chúng ta phải thiết lập cascade trong code java và trong mysql thì lúc đó dữ liệu sẽ được toàn vẹn và không bị dư thừa dữ liệu

Chúng ta sử dụng **@JoinColumn** để cấu hình cho biến address là tìm kiếm trong column nào trong database mà map vào (nó chính là foregin key) .Biến address này được khai báo trong Class Address dưới đây. Như vậy chúng ta sử dụng @JoinColumn giống như một cầu nối nơi 2 tables User và Address. Nó được dùng để khai báo 2 tables sẽ kết nối mối quan hệ với nhau thông qua column nào.

```java
@Entity
@Table(name = "address")
public class Address {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    @Column(name = "id")
    private Long id;
    //...

    @OneToOne(mappedBy = "address")
    private User user;

    //... getters and setters
}
```

#### Test ứng dụng

Chúng ta sẽ lưu xuống database theo cách sau.

```java
Address address = new Address ("Address 1");

 User user  = new User();
 user.setAddress(address);
 user.save(); // như vậy ta sẽ lưu dữ liệu xuống 2 bảng User và Address  
```

