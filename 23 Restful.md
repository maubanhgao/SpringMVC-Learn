# Sử dụng Restful trong SpringBoot

[TOC]

![](D:\data\source\Hoc lap trinh web\SpringMVC\Learn Spring MVC\Learn 01\img\feature_webservice.png)

## 1. Website là gì ? 

**Trước tiên chúng ta sẽ tìm hiểu** **webstie** là gì? Anh ví dụ khi mọi người nhập vào đường link https://lazada.com mình sẽ nhận được website như sau:

![](D:\data\source\Hoc lap trinh web\SpringMVC\Learn Spring MVC\Learn 01\img\lazada.png)

Để thấy được trang **web** lazada với đầy đủ nội dung và hình ảnh như vậy thì mình phải trải qua các bước sau

- Client (website) gửi một yêu cầu (request) lên con server. Trong trường hợp này người dùng gõ vào trình duyệt là https://lazada.com và yêu cầu server sẽ trả về website bao gồm html,css,ảnh, và dữ liệu)
- Khi server nhận được request từ phía client . Nó sẽ chuẩn bị resource (trong trường hợp này là html và dữ liệu ) đúng như yêu cầu client. Sau khi client nhận được resouce (html,css,js,data) thì lúc đó hiển thị lên cho người dùng thấy được trang home hoàn chỉnh.
- Đây chính là luồng đi của ứng dụng website . Client yêu cầu (request) server trả về một resouce mà mình mong muốn. Resource ở đây có thể là html , một cái ảnh hay một cái file.

## 2. Webservice là gì ? 

Cũng là ví dụ trên nhưng giờ người dùng (client) không dùng **website** nữa mà thay vào đó là ứng dụng trên điện thoại di động. Người dùng mở điện thoại và bật ứng dụng lazada lên. Như các em thấy trên ứng dụng di động chúng ta không thể trả về **html,css** được. Mà ta chỉ muốn server trả về **dữ liệu** (data) sau đó mình sẽ dùng ngôn ngữ lập trình của mobile để hiển thị dữ liệu.

Như vậy chúng ta không thể áp dụng nguyên lý của **website** vào đây. Mà thay vào đó chúng ta sẽ sử dụng một công nghệ gọi là **webservice**. Ở đây chúng ta yêu cầu server trả về **dữ liệu (data)** thôi và sau đó tuỳ thuộc vào **frontend** đang dùng ngôn ngữ gì mà mình hiển thị dư liệu lên.

Thông thường server sẽ trả dữ liệu dựa trên 2 dạng là **XML** và **JSON** về cho client.

Đây là dạng dữ liệu XML

```xml
<?xml version="1.0"?>
<user>
 <name>Smith</name>
</user>
```

Đây là dạng JSON

```json
{
 "date": "2016-08-27",
 "location": "Chicago",
 "info": "Hot"
}
```

## 3. Restfull webservice là gì? 

**REST** là viết tắt của từ (REpresentational State Transfer ). Anh lấy ví dụ về lazada . Khi mình vào click vô xem chi tiết của một sản phẩm thì mình sẽ thấy thông tin của nó gồm mô tả , giá , số lượng. Như vậy khi client gửi request (yêu cầu) lên server để lấy thông tin về sản phẩm. Ví dụ backend là mình viết bằng Spring (java) thì lúc đó Controller sẽ gọi các services để lấy dữ liệu và kết quả của các service trả về là một đối tượng Product (có thuộc tính mô tả, giá , số lượng). Tuy nhiên ta sẽ không trả về đối tượng Product cho client ngay mà đối tượng Product đó sẽ được chuyển đổi thành dạng Json hoặc XML rồi gửi về cho client.

Sự chuyển đổi đó gọi là REpresentational State Transfer. Representational có nghĩa là hiển thị kiểu json hay xml. State có nghĩa là trạng thái của dữ liệu từ Object Java mình chuyển sang trạng thái khác để truyền đi. Transfer nghĩa là động tác chuyển đổi dữ liệu Object sang kiểu định dạng mới là json hay xml . Nói tóm lại ta chuyển đổi trạng thái dữ liệu từ object sang kiểu json hay xml để client có thể nhận được data.

**Restfull Webservice** là một dạng webservice viết theo chuẩn REST. REST quy định các quy tắc để bạn làm ra một webservice . Nó chú trọng vào việc lấy resouce (tài nguyên) như là data, image , files từ server trả về client thông qua protocal http.

Bất kỳ một ứng dụng nào cũng thực hiện thao tác CRUD (tạo,đọc,sửa,xoá) dữ liệu. Rest đặt ra một quy tắc mà các lập trình viên muốn xát định rõ ý định của mình thông qua các phương thức http.

- Khi lấy dữ liệu, đọc dữ liệu từ server thì phương thức request mình dụng là **Get**
- Khi tạo mới một resource thì phương thức request là **Post**
- Khi cập nhật giá trị thì phương thức request là **Update**
- Khi xoá một giá trị thì phương thức request là **Delete**

Ví dụ sau là một Restfull của Spring . Ta định nghĩa các **@GetMapping** tương ứng với method request là GET. **@PostMapping** ứng với request là http Post. **@DeleteMapping** ứng với request có http là DELETE. Và **@PutMapping** ứng với request Update.

```java
@RestController
@RequestMapping("/api/books")
public class BookController {

    @Autowired
    private BookRepository bookRepository;

    @GetMapping
    public Iterable findAll() {
        return bookRepository.findAll();
    }

    @GetMapping("/title/{bookTitle}")
    public List<Book> findByTitle(@PathVariable String bookTitle) {
        return bookRepository.findByTitle(bookTitle);
    }

/*    @GetMapping("/{id}")
    public Book findOne(@PathVariable Long id) {
       Book book1 = new Book();
       return book1;
    }*/


    @GetMapping("/{id}")
    public Book findOne(@PathVariable Long id) {
        return bookRepository.findById(id).orElseThrow(BookNotFoundException::new);
    }



    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    public Book create(@RequestBody Book book) {
        Book books = bookRepository.save(book);
        return books;
    }

    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) {
        bookRepository.findById(id)
                .orElseThrow(BookNotFoundException::new);
        bookRepository.deleteById(id);
    }

    @PutMapping("/{id}")
    public Book updateBook(@RequestBody Book book, @PathVariable Long id) {
        if (book.getId() != id) {
            throw new BookNotFoundException();
        }
        bookRepository.findById(id)
                .orElseThrow(BookNotFoundException::new);
        return bookRepository.save(book);
    }
```

Ở ví dụ trên nếu phương thức **request** là Post sẽ gọi hàm create. Nếu phương thức request là Delete sẽ gọi hàm delete. Như vậy Rest quy định rất rõ ràng từng phương thức tương ứng với hành động CRUD

## 4. Kết luận?

Ngày nay thì đa số các ứng dụng webservice đều là Restfull cả . Vì nó có nhiều ưu điểm hơn các loại khác như Web Service dựa trên SOAP và WSDL. Đồng thời REST để bảo trì và mở rộng.