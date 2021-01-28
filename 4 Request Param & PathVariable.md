# Phân biệt Request Param và PathVariable trong SpringMVC

[TOC]

## 1. Request Param 

Chúng ta sử dụng **Request Param** ở controller để lấy giá trị người dùng nhập trên trình duyệt. Ví dụ khi người dùng gõ vào đường link như sau để gửi 2 giá trị 10 và 20 lên server .

http://localhost:8080/springmvc/hello/101?param1=10&param2=20

Phía Controller ta sẽ dùng **@RequestParam** để bắt lại 2 giá trị 10 và 20 như sau :

```java
public String getDetails(@RequestParam(value="param1", required=true) String param1, @RequestParam(value="param2", required=false) String param2){

}
```

- @RequestParam : chúng ta sử dụng annotation @RequestParam để khai báo là sẽ sử dụng nó để lấy các giá trị trên url
- Value=”param1” : Chúng ta khai báo để lấy giá trị tên là “param1” trên trình duyệt. Như vậy ứng với giá trị số 10 trên trình duyệt sẽ gán vào giá trị String param1
- Value=”param2” : Thì nó tương tự mục đích ở trên , chúng ta khai báo để lấy giá trị tên param2. Như vậy nó sẽ lấy giá trị 20 và gán vào biên String param2
- require = true : Thì chúng ta bắt buột là trên url phải có tham số param1

## 2. Path Variable 

Sử dụng **Path Variable** ở Controller để lấy giá trị người dùng nhập trên trình duyệt. Nhưng ở đây mình sẽ không dùng theo định dạng key và value như ?param1=10&param2=20. Mà thay vào đó chúng ta sẽ sử dụng định dạng khác là /param/10.

Ví dụ khi người dùng nhập vào url sau và muốn truyền 1234 lên Controller thì bên Controller ta sử lý như sau . http://localhost:8080/MyApp/user/1234

```java
@RequestMapping(value="/user/{userId}", method = RequestMethod.GET)
public List<Invoice> listUsersInvoices(
            @PathVariable("userId") int user) {
  ...
}
```

- @RequestMapping(value=”/user/{userId}” : chúng ta khai báo định dạng là /user/{userId}. Như vậy nó sẽ map với trình duyệt có định dạnh là user/1234
- @PathVariable chúng ta sẽ lấy số 1234 từ trình duyệt và gắn vào biên int user.

## 3. Kết hợp cả 2 trong 1 request

Về mặt kỷ thuật chúng ta có thể sử dụng cả 2 phương pháp PathVariable và RequestParam trong một Controller để lấy giá trị từ url như sau.

http://localhost:8080/MyApp/user/1234/invoices?date=12-05-2013

```java
@RequestMapping(value="/user/{userId}/invoices", method = RequestMethod.GET)
public List<Invoice> listUsersInvoices(
            @PathVariable("userId") int user,
            @RequestParam(value = "date", required = false) Date dateOrNull) {

}
```

Trong đó PathVariable sẽ sử dụng định dạng là /user/{userId}. Trong khi đó @RequestParam sẽ sử dụng định dạng key và value date (key) =12-05-2013 (value).

## 4. Kết luận

Cả 2 cách trên đều thực hiện chung một nhiệm vụ là lấy các tham số từ người dùng truyền lên. Bạn sử dụng cái nào cũng làm được mục đích của mình . Tuy nhiên tuỳ vào thiết kế của một hệ thống mà lựa chọn Request Param hoặc Path Variable để sử dụng mới đem lại hiệu quả cao được . Lấy ví dụ mình viết Restfull Web Service thì chắc chắn mình phải dùng Path Variable . Còn thường Request Param khi ta chỉ muống query data trên URL.