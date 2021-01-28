# Luồng đi của ứng dụng Spring MVC

[TOC]

![](D:\data\source\Hoc lap trinh web\SpringMVC\Learn Spring MVC\Learn 01\img\feature_springmvc.png)

## **1. Luồng đi của ứng dụng MVC qua các tầng**

**Trước hết mình đi qua hình ảnh về luồng đi của ứng dụng** **spring mvc** gồm có các thành phần nào.

![](D:\data\source\Hoc lap trinh web\SpringMVC\Learn Spring MVC\Learn 01\img\springmvcflow.jpg)

1. Người dùng điền vào tên website mình cần truy cập trên browser sau đó bấm enter. Lúc này mình gửi 1 request lên server nơi mà mình triển khai ứng dụng Spring.
2. Thành phần DispatcherServlet của Spring MVC sẽ nhận được request (yêu cầu) của người dùng ở bước 1. Dispatcher là thành phần quan trọng nhất trong springmvc. Nó sẽ là nơi đầu tiên nhận request từ client sau đó sẽ chuyển request đó tới các controller tương ứng, đồng thời sẽ là chốt chặn cuối cùng trả về kết quả cho client.
3. Sau khi nhận được request(yêu cầu) DispatcherServlet sẽ chuyển yêu cầu đó tới Controller bằng các cơ chế mapping mà ta khai báo trong Handler Mapping. Có 4 cách chúng ta có thể dùng để mapping một request vào controller tương ứng

1- Cách 1 : Dùng BeanNameUrlHandlerMapping

Đây là cơ chế mapping mặc định. Dựa vào tên của URL mà nó sẽ mapping tới controller tương ứng. Anh lấy ví dụ http://localhost/hello thì nó sẽ mapping vào đúng controller HelloController vì nó trùng tên là Hello.

Sử dụng Java configure

```java
@Configuration
	public class BeanNameUrlHandlerMappingConfig {
	    @Bean
	    BeanNameUrlHandlerMappingConfig beanNameUrlHandlerMapping() {
	        return new BeanNameUrlHandlerMapping();
	    }
	 
	    @Bean("/HelloUrl")
	    public WelcomeController hello() {
	        return new HelloController();
	    }
	}
```

Sử dụng XML Configure

```xml
<bean class="org.springframework.web.servlet.handler.BeanNameUrlHandlerMapping" />
	<bean name="/hello" class="com.levuguyen.HelloController" />
```

2- Cách 2 : Dùng SimpleUrlHandlerMapping

**SimpleUrlHandlerMapping** thì uyển chuyển hơn **BeanNameUrlHandlerMapping**. Chúng ta có thể name hoặc url để mapping tới controller tương ứng.

Ví dụ sử dụng Java Confiure.

```java
@Configuration
	public class SimpleUrlHandlerMappingConfig {
	 
	    @Bean
	    public SimpleUrlHandlerMapping simpleUrlHandlerMapping() {
	        SimpleUrlHandlerMapping simpleUrlHandlerMapping = new SimpleUrlHandlerMapping();
	        
	        Map<String, Object> urlMap = new HashMap<>();
	        urlMap.put("/simpleUrlWelcome", hello());
	        simpleUrlHandlerMapping.setUrlMap(urlMap);  
	        return simpleUrlHandlerMapping;
	    }
	 
	    @Bean
	    public HelloController hello() {
	        return new HelloController();
	    }
	}
```

- Ví dụ sử dụng XML Configure

```xml
<bean class="org.springframework.web.servlet.handler.SimpleUrlHandlerMapping">
	    <property name="mappings">
	        <value>
	            /simpleUrlWelcome=hello
	            /*/simpleUrlWelcome=hello
	        </value>
	    </property>
	</bean>
	<bean id="hello" class="com.levunguyen.HelloController" />
```

3- Cách 3 : ControllerClassNameHandlerMapping

ControllerClassNameHandlerMapping hiện nay không được hỗ trợ trong version Spring 5.

Chúng ta hoàn toàn có thể cấu hình cách mà URL mapping vào controller theo tên hay theo đường dẩn để mapping vào controller tương ứng.

1. Sau khi vào controller tương ứng thì từ controller ta gọi services, service gọi repository, repository sẽ sử dung tầng persisten để thao tác với database lấy dữ liệu . và chuyển hoá dữ liệu trong database thành model và trả ngược lại cho controller.
2. Controller sẽ trả về tên view (tên trang web ) và model cho Dispatchervleter.
3. DispatcherServlet sẽ dự vào tên view mà controler trả về . Nó sẽ đi tìm trang view (thymeleaf,jsp) tương ứng dựa vào việc ta cấu hình View Reolver mà Dispatcher biết phải tìm thấy trang view ở đâu đồng thời truyền model để trang view hiểu thị dữ liệu. Kết quả cuối cùng là ta có một trang website hoàn chỉnh có HTML và dữ liệu. Chúng ta có thể cấu hình tầng view có thể trả về dạng html, jsp , hoặc xml hoặc json như sau

```java
@Bean
	public ViewResolver internalResourceViewResolver() {
	    InternalResourceViewResolver bean = new InternalResourceViewResolver();
	    bean.setViewClass(JstlView.class);
	    bean.setPrefix("/WEB-INF/view/");
	    bean.setSuffix(".jsp");
	    return bean;
	}
```

Chúng ta sử dụng **internalResourceViewResolver** để cấu hình nơi nào chúng ta đặt các cái view (/WEB-INF/view/). Trang kết quả trả về là html,xml, hoạc jsp (bean.setSuffix(“.jsp”).

1. Cuối cùng **DispatchServlet** gửi lại kết quả trang web cho client. Như vậy chúng ta thấy trang web được sinh ra ở phía server sau đó nó mới được gửi lại cho người dùng.