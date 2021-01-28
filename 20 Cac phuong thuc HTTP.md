# Sử dụng các phương thức HTTP trong lập trình web

[TOC]

## 1. Request là gì 

 ví dụ như mình có cái website là tiki bán hàng. Khi mình vào trang web thì mình thấy tất cả các mặt hàng của niki hiện ra. Làm thế nào mà mình thấy được như vậy.

Đầu tiên khi mình vào trang chủ của tiki ví dụ https://tiki.vn khi đó trang web (client) sẽ gửi một yêu cầu (request) lên server để nói server là client cần những thông tin ở trang chủ. Khi server nắm được yêu cầu (request) của client thì nó sẽ trả lại thông tin tương ứng với yêu cầu của client.

Thông thường thì từ client gửi yêu cầu lên server sẽ có nhiều dạnh yêu cầu. Có khi là client yêu cầu danh sách các sản phẩm, có khi là yêu cầu thêm một sản phẩm, hoặc xoá một sản phẩm. Tuỳ vào mong muốn của client mà trong lập trình mình có nhiều loại yêu cầu hỗ trợ khác nhau.

Sau đây là tổng hợp **9 loại yêu cần được sử dụng nhiều trong lập trình**. Thông thường yêu cầu Get dùng để lấy thông tin. Post dùng để tạo/thêm mới. Put là để cập nhật thông tin và Delete dùng để xoá thông tin.

## 2. Tổng hợp các loại request 

Có tất cả 9 loại request.

1. GET: được sử dụng để lấy thông tin từ sever theo URI đã cung cấp.
2. HEAD: giống với GET nhưng response trả về không có body, chỉ có header.
3. POST: gửi thông tin tới sever thông qua các biểu mẫu http( đăng kí chả hạn..).
4. PUT: ghi đè tất cả thông tin của đối tượng với những gì được gửi lên.
5. PATCH: ghi đè các thông tin được thay đổi của đối tượng.
6. DELETE: xóa tài nguyên trên server.
7. CONNECT: thiết lập một kết nối tới server theo URI.
8. OPTIONS: mô tả các tùy chọn giao tiếp cho resource.
9. TRACE: thực hiện một bài test loop - back theo đường dẫn đến resource.

## 3. Một số khái niệm khác 

- SAFE : một method được coi là safe khi nó không làm thay đổi trạng thái “sate” của server. Nói cách khác, an toàn là chỉ đọc mà không làm thay đổi bất kì điều gì. Các method được coi là safe chỉ có: GET, HEAD và OPTIONS.
- Unsafe: PUT, DELETE, POST và PATCH các method này có thể thay đổi trạng thái của server, nó có thể tạo mới , chỉnh sửa hoặc xoá thông tin. -IDEMPOTENT : các method được coi là idempotent khi nó có thể thực hiên n + 1 lần mà vẫn trả lại 1 kết quả như ban đầu.
- Một số lưu ý: header dài tối đa 8kb và cũng phụ thuộc cả vào trình duyệt, body thì limit của nó tùy trình duyệt. Url không dài quá 2 nghìn kí tự (ror).

## 4. GET VS POST 

Một ứng dụng web được thiết kế theo restful thì get chỉ dùng để lấy dữ liệu và post chỉ dùng để đẩy dữ liệu lên. Một chút khác biệt dễ nhận thấy giữa **phương thức get** và **phương thức post** là get thì không có body. Khi dùng get để truyền dữ liệu lên sever chúng ta thấy rằng tất cả các paramater đều bị hiển thị trên url của request, xét về khía cạnh bảo mật thì điều này thật là tệ. Anh lấy ví dụ nếu mình dùng **phương thức get** để thực hiện công việc chuyển tiền từ tài khoản A sang tài khoản B. Thì thông tin số tài khoản sẽ được hiển thị trên trình duyệt (URL). Nếu vô tình có người đứng sau lưng mình thì họ hoàn toàn có thể thấy được các thông tin nhạy cảm trên và đánh cắp thông tin đó để phục vụ cho mục đích xấu.

Post thì khác, nó giấu parameters trong body và mã hóa chúng đi, ngăn cản các phần tử trung gian ăn cắp nội dung. Nhưng post chỉ có tính an toàn đối với client, còn với sever thì lại khác.

Anh cũng lấy ví dụ chuyển tiền trên , nếu mình sử dụng **phương thức post** sẽ an toàn hơn vì thông tin tài khoản không hiển thị trên URL, mà nó nằm trong body. Người dùng không thấy được trên url nên không thể biết được là mình truyền dữ liệu gì

Thông thường mình dùng **phương thức get** để lấy thông tin. Anh ví dụ như lấy danh sách sản phẩm điện thoai trên trang tiki.vn. Thì mình có thể dùng phương thức get và hiển thị thông tin trên trình duyệt như sau : https://tiki.vn/iphone10 hoặc https://tiki.vn/samsungA51. Như vậy chữ iphone10 hay samsungA51 không cần phải bảo mật cao, chủ yếu mình dùng nó để lấy thông tin tất cả các sản phẩm iphone10 và samsunga51 thôi.

Mình dùng **phương thức post** khi các thông tin truyền lên server có tính bảo mật.

## 5. POST/PUT/PATCH 

- Điểm khác biệt giữ **phương thức post** và **phương thức put** đơn giản là put là idempotent còn post thì không, bạn sẽ nhận được thông báo lỗi khi gửi một request post với cùng 1 nội dung 2 lần nhưng put thì không, nó luôn trả về kết quả như nhau.
- Post: tạo mới.
- Put: ghi đè(toàn bộ) hoặc tạo mới 1 resource.
- Patch: cập một 1 phần của resource.

## 6. Kết luận

**Tuỳ vào hành động mà client mong muốn là gì thì mình sẽ chọn phương thức cho đúng. Thông thường mình có thể dùng POST cho cả việc tạo mới, update, delete đều được nhưng không nên khuyến khích dùng. Post chỉ dùng cho tạo , Get dùng cho xem thông tin, PUT dùng cho cập nhật và Delete dùng cho việc xoá. Chỉ cần nắm được như vậy thì mình đã okie rồi.**