# Sử dụng Optional PathVariable trong SpringMVC

[TOC]

## 1. PathVariable dùng để làm gì 

Như các em đã thấy trong bài **RequestMaping** anh viết lần trước tại [đây](https://levunguyen.com/laptrinhspring/2020/04/15/phan-biet-request-param-va-path-variable/). Chúng ta sử dụng **@PathVariable** để mapping URI mà người dùng nhập trên trình duyệt vào Controller tưng ứng.

Ví dụ anh có một controller có phương thức là getArticle sau.

```java
@RequestMapping(value = {"/article", "/article/{id}"})
public Article getArticle(@PathVariable(name = "id") Integer articleId) {
    if (articleId != null) {
        //...
    } else {
        //...
    }
}
```

Ở đây chúng ta mong muốn khi người dùng gõ vào url là /article hay /article/id . Thì Spring sẽ tự động mapping vô method getArticle trong Controller.

Nếu ta có request là /article/123 thì mình gán giá trị 123 vô tham số articleId

Nếu ta có request là /article thì Spring sẽ trả về lỗi 500

```markdown
org.springframework.web.bind.MissingPathVariableException:
  Missing URI template variable 'id' for method parameter of type Integer
```

Như vậy để làm sao mình không bị lỗi như trên . Giải pháp sẽ là dùng optional

## 2. Sử dụng Optional Parameter

**Cũng ví dụ code về controller trên. Bây giờ ta thêm Optional vào**

```java
@RequestMapping(value = {"/article", "/article/{id}"}")
public Article getArticle(@PathVariable Optional<Integer> optionalArticleId) {
    if (optionalArticleId.isPresent()) {
        Integer articleId = optionalArticleId.get();
        //...
    } else {
        //...
    }
}
```

Ở ví dụ trên ta sử dụng Optional optiontalArticleId để mapping giá trị id từ request url của người dùng

Nếu ta có request là /article/123 thì mình gán giá trị 123 vô tham số optiontalArticleId

Nếu ta có request là /article thì optiontalArticleId sẽ là null. Khi sử dụng Optional ta sẽ cos được các method của Optional như isPresent(), get(), or orElse() để ta có thể truy cập và thao tác và xử lý theo ý ta mong

## **3. Kết luận**

Mình không nên sử dụng @RequestMapping(value = {“/article”, “/article/{id}”}”) cho cùng môt method vì nó dể gây ra nhầm lẫn. Tốt nhất 1 request nên được xử lý bởi môt method. Ta có thể tách ra như sau

```java
@RequestMapping(value = "/article/{id}")
public Article getArticle(@PathVariable(name = "id") Integer articleId) {
    //...        
}

@RequestMapping(value = "/article")
public Article getDefaultArticle() {
    //...
}
```

