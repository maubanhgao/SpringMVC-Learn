# Sử dụng Spring AOP Annotation trong Spring

[TOC]

## 1. Spring AOP Annotation là gì 

Trong bài AOP advise chúng ta sử dụng xml để cấu hình cho advise, pointcut và around. Hôm nay chúng ta sẽ sử dụng annotation để làm AOP như @Before, @After, @AfterReturing, @AfterThrowing và @Around

## 2. Cấu hình file pom 

Chúng ta thêm các thư viện AspectJrt và aspecttools vào trong dự án để sử dụng aop

```xml
<project xmlns="https://maven.apache.org/POM/4.0.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="https://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>org.springframework.samples</groupId>
	<artifactId>SpringAOPExample</artifactId>
	<version>0.0.1-SNAPSHOT</version>

	<properties>

		<!-- Generic properties -->
		<java.version>1.6</java.version>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>

		<!-- Spring -->
		<spring-framework.version>4.0.2.RELEASE</spring-framework.version>

		<!-- Logging -->
		<logback.version>1.0.13</logback.version>
		<slf4j.version>1.7.5</slf4j.version>

		<!-- Test -->
		<junit.version>4.11</junit.version>

		<!-- AspectJ -->
		<aspectj.version>1.7.4</aspectj.version>

	</properties>

	<dependencies>
		<!-- Spring and Transactions -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${spring-framework.version}</version>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-tx</artifactId>
			<version>${spring-framework.version}</version>
		</dependency>

		<!-- Logging with SLF4J & LogBack -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${slf4j.version}</version>
			<scope>compile</scope>
		</dependency>
		<dependency>
			<groupId>ch.qos.logback</groupId>
			<artifactId>logback-classic</artifactId>
			<version>${logback.version}</version>
			<scope>runtime</scope>
		</dependency>

		<!-- AspectJ dependencies -->
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>${aspectj.version}</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjtools</artifactId>
			<version>${aspectj.version}</version>
		</dependency>
	</dependencies>
</project>
```

## 3. Tạo Model 

Trong ví dụ hôm nay chúng ta sẽ làm việc với Employee. Chúng ta có lớp Employee như sau

```java
package com.levunguyen;

public class Employee {

	private String name;
	
	public String getName() {
		return name;
	}

	@Loggable
	public void setName(String nm) {
		this.name=nm;
	}
	
	public void throwException(){
		throw new RuntimeException("Dummy Exception");
	}	
} 
```

Chúng ta thấy có annotation là @Loggable thì cái annotation này dùng ghi log và do chúng ta tự định nghĩa ra. Phần này anh sẽ nói rõ hơn trong phần 10 dưới đây.

## 4. Tạo Service 

Chúng ta sẽ tạo Class EmployeeService để lấy Employee như sau

```java
import com.journaldev.spring.model.Employee;

public class EmployeeService {

	private Employee employee;
	
	public Employee getEmployee(){
		return this.employee;
	}
	
	public void setEmployee(Employee e){
		this.employee=e;
	}
}
```

## 5. Chúng ta tạo cấu hình bean AOP 

```xml
<beans xmlns="https://www.springframework.org/schema/beans"
	xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
	xmlns:aop="https://www.springframework.org/schema/aop"
	xsi:schemaLocation="https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans-4.0.xsd
		https://www.springframework.org/schema/aop https://www.springframework.org/schema/aop/spring-aop-4.0.xsd">

<!-- Enable AspectJ style of Spring AOP -->
<aop:aspectj-autoproxy />

<!-- Configure Employee Bean and initialize it -->
<bean name="employee" class="com.levunguyen.Employee">
	<property name="name" value="Le Vu Nguyen"></property>
</bean>

<!-- Configure EmployeeService bean -->
<bean name="employeeService" class="com.levunguyen.EmployeeService">
	<property name="employee" ref="employee"></property>
</bean>

<!-- Configure Aspect Beans, without this Aspects advices wont execute -->
<bean name="employeeAspect" class="com.levunguyen.EmployeeAspect" />
<bean name="employeeAspectPointcut" class="com.levunguyen.EmployeeAspectPointcut" />
<bean name="employeeAspectJoinPoint" class="com.levunguyen.EmployeeAspectJoinPoint" />
<bean name="employeeAfterAspect" class="com.levunguyen.EmployeeAfterAspect" />
<bean name="employeeAroundAspect" class="com.levunguyen.EmployeeAroundAspect" />
<bean name="employeeAnnotationAspect" class="com.levunguyen.EmployeeAnnotationAspect" />

</beans>
```

- Đầu tiên chúng ta bật chức năng aop lên

```xml
<aop:aspectj-autoproxy />
```

- Tiếp đến chúng ta tạo các bean Employee

```xml
<!-- Configure Employee Bean and initialize it -->
<bean name="employee" class="com.levunguyen.Employee">
	<property name="name" value="Le Vu Nguyen"></property>
</bean>

<!-- Configure EmployeeService bean -->
<bean name="employeeService" class="com.levunguyen.EmployeeService">
	<property name="employee" ref="employee"></property>
</bean>
```

- Chúng ta cấu hình các aspect bean.

```xml
<bean name="employeeAspect" class="com.levunguyen.EmployeeAspect" />
<bean name="employeeAspectPointcut" class="com.levunguyen.EmployeeAspectPointcut" />
<bean name="employeeAspectJoinPoint" class="com.levunguyen.EmployeeAspectJoinPoint" />
<bean name="employeeAfterAspect" class="com.levunguyen.EmployeeAfterAspect" />
<bean name="employeeAroundAspect" class="com.levunguyen.EmployeeAroundAspect" />
<bean name="employeeAnnotationAspect" class="com.levunguyen.EmployeeAnnotationAspect" />
```

## 6. Ví dụ với Spring AOP Before Aspect 

Ta có EmployeeAspect sử dụng annotation @Aspect trong đó ta định nghĩa phương thức nào cần hook trước khi chạy. Trong ví dụ này chúng ta áp dụng khi chạy phương thức getName

- Một Aspect Class sẽ được đánh dấu băng @Aspect
- Annotation @Before được sử dụng để chạy trước khi method getName được gọi

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class EmployeeAspect {

	@Before("execution(public String getName())")
	public void getNameAdvice(){
		System.out.println("Executing Advice on getName()");
	}
	
	@Before("execution(*.com.levunguyen.*.get*())")
	public void getAllAdvice(){
		System.out.println("Service method getter called");
	}
}
```

## 7. Ví dụ với AOP Pointcut Method 

Chúng ta sử dụng phương thức nào sẽ được chạy AOP hoặc tất cả phương thức được chạy bằng sử dụng within

```java
@Aspect
public class EmployeeAspectPointcut {

	@Before("getNamePointcut()")
	public void loggingAdvice(){
		System.out.println("Executing loggingAdvice on getName()");
	}
	
	@Before("getNamePointcut()")
	public void secondAdvice(){
		System.out.println("Executing secondAdvice on getName()");
	}
	
	@Pointcut("execution(public String getName())")
	public void getNamePointcut(){}
	
	@Before("allMethodsPointcut()")
	public void allServiceMethodsAdvice(){
		System.out.println("Before executing service method");
	}
	
	//Pointcut to execute on all the methods of classes in a package
	@Pointcut("within(com.levunguyen.service.*)")
	public void allMethodsPointcut(){}
	
}
```

## 8. Ví dụ với AOP JoinPoint và Advice 

Chúng ta sử dụng AOP cho các phương thức là set có trong Employee model

```java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class EmployeeAspectJoinPoint {
	
	@Before("execution(public void com.levunguyen.model..set*(*))")
	public void loggingAdvice(JoinPoint joinPoint){
		System.out.println("Before running loggingAdvice on method="+joinPoint.toString());
		
		System.out.println("Agruments Passed=" + Arrays.toString(joinPoint.getArgs()));

	}
	
	//Advice arguments, will be applied to bean methods with single String argument
	@Before("args(name)")
	public void logStringArguments(String name){
		System.out.println("String argument passed="+name);
	}
}
```

## 9. Ví dụ với AOP After Aspect 

Chúng ta sử dụng @After để chạy AOP khi method đã chạy xong

```java
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Aspect;

@Aspect
public class EmployeeAfterAspect {

	@After("args(name)")
	public void logStringArguments(String name){
		System.out.println("Running After Advice. String argument passed="+name);
	}
	
	@AfterThrowing("within(com.levunguyen.Employee)")
	public void logExceptions(JoinPoint joinPoint){
		System.out.println("Exception thrown in Employee Method="+joinPoint.toString());
	}
	
	@AfterReturning(pointcut="execution(* getName())", returning="returnString")
	public void getNameReturningAdvice(String returnString){
		System.out.println("getNameReturningAdvice executed. Returned String="+returnString);
	}
	
}
```

## 10. Ví dụ AOP Around Aspect 

```java
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;

@Aspect
public class EmployeeAroundAspect {

	@Around("execution(* com.journaldev.spring.model.Employee.getName())")
	public Object employeeAroundAdvice(ProceedingJoinPoint proceedingJoinPoint){
		System.out.println("Before invoking getName() method");
		Object value = null;
		try {
			value = proceedingJoinPoint.proceed();
		} catch (Throwable e) {
			e.printStackTrace();
		}
		System.out.println("After invoking getName() method. Return value="+value);
		return value;
	}
}
```

## 11. Tự tạo annotation aop pointcut

Như các em thấy trong Module Employee ta thấy một annotation là @Loggable đây chính là annotation do chúng ta tự tạo ra bằng cách sau

```java
package com.levunguyen;

public @interface Loggable {

}
```

Bây giờ chúng ta chỉ muốn các phương thức nào được đánh dấu bởi annotation @Loggable mới được chạy như sau

```java
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class EmployeeAnnotationAspect {

	@Before("@annotation(com.levunguyen.Loggable)")
	public void myAdvice(){
		System.out.println("Executing myAdvice!!");
	}
}
```