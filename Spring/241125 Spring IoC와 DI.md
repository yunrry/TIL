
# IoC와 DI 개념

#### 리팩토링
- 외부 기능에는 변화가 없지만 내부적으로 효율적으로 시스템을 재구성하는 것
- 관심사에 맞게 프로그램을 효율적으로 구성하고
- Spring 원칙에 부합할 수 있게 어플리케이션 구성 가능
    

#### 관심사와 제어권

1. 관심사 파악
	- 이메일을 발송하는 함수 예제

```java
public void sendEmail(@RequestParam("to") List<String> to, @RequestParam("cc") List<String> cc){  
String subject = generateSubject(); // 제목 생성  
String body = generateBody(); // 내용 생성  
Content subjectContent = new Content().withData(subject);  
Body bodyContent = new Body().withHtml(new Content().withData(body));  
Message message = new Message().withSubject(subjectContent).withBody(bodyContent);  
Destination destination = new Destination(); //받는 사람 구성  
if(CollectionUtils.isNotEmpty(to)) {  
destination.withToAddresses(to);  
}  
if(CollectionUtils.isNotEmpty(cc)){  
destination.withCcAddress(cc);  
}  
SendEmailRequest sendEmailRequest = new SendEmailRequest().withSource(source).withDestination(destination).withMessage(message);  
EmailServiceClient emailServiceClient = new EmailServiceClient();  
emailServiceClient.sendEmail(sendEmailRequest); // 실제 이메일 발송  
}
```


```java
Public void sendEmail(SendEmailRequest sendEmailRequest){  
EmailServiceClient emailServiceClient = new EmailServiceClient();  
      emailServiceClient.sendEmail(sendEmailRequest);  
}
```


---

  
# 객체의 제어권과 제어 역전


**객체의 제어권**
: 어떠한 객체가 다른 객체를 생성, 관리하는 권한

-[실습] sendEmail함수를 실제 동작하는 Spring MVC Controller로 옮겨 봄

  

#### 제어의 역전(IoC)

1. 제어권 이전
	- 제어권리라고 하는 관심사 -> 관심사도 리팩토링 하려면 분리할 필요가 있음
	- EmailController에서 ‘관계 설정 기능”의 분리 필요
		- EmailController의 sendEmail은 이메일 전송이라는 본연의 기능에만 집중
		- EmailServiceClient 클래스 객체의 선택/생성 불필요
		- emailServiceClient의 sendEmail 메서드만 남기고, emailServiceClient의 객체를 생성하는 부분을 어딘가로 분리할 필요가 있음
    
**제어권 역전(IoC, Inversion of Control)**
- spring이 지향하는 바는 이런 제어권에 해당하는 것은 모두 다른 객체(상위 객체)에게 위임
	1. 임의의 객체는 자신이 사용할 다른 객체를 선택/생성하지 않음
	2. 객체 스스로가 향후 어떻게 만들어지고 어디서 사용될지 알 수 없음
	3. 모든 제어 권한은 다른 객체(상위)에게 위임
		- 내가 제어권을 가질 필요 없이 위에서 부터 전달되는 객체를 그대로 수동적으로 사용만 하겠다. => 객체를 프로그래머가 아니라, 스프링 프레임워크 이 관리
	    - 객체에 대한 제어권이 수동적으로 역전이 되었다. 라는 뜻
- 장점
	- 프로그램 소스 코드의 유연성 및 확장성 증가
	- IoC는 스프링에서 제공하는 모든 기능의 기초가 되는 기반 기술
    
2. Spring의 IoC
	EmailController가 이미 최상위 클래스인데 EmailServiceClient의 제어권을 상위 객체로 넘기려면 어떻게 해야할까?(EmailController에 대한 제어권은 누가 가지고 있나? 궁금)
	- Object Factory
		- Bean: Spring에서 제어권을 가지고 직접 생성, 관리하는 객체
	- Factory Class
		- org.springframework.beans.factory.BeanFactory를 이용해 객체를 생성, 관리할 수 있음
		- Bean Factory를 상속한 org.springframework.context.ApplicationContext 인터페이스를 통해 어플리케이션 전반에 걸쳐 제어를 담당
	- Application Context의 역할
		- IoC개념을 적용해서 관리할 모든 객체에 대한 제어권 담당
			- 컨테이터 역할을 하는 것이 Application Context. 
			- 모든 객체를 생성해서 담고 있게 됨
		- XML에 Bean 등록 예
		    ```xml
<bean id="emailServiceClient"       class="com.spring.mvcproject.service.EmailServiceClient">
</bean>
		    ```
		보통은 어노테이션으로 Bean 등록해서 사용
			@Controller @Service @Componet
	- Annotation 기반으로 동작하기 때문에 각 Bean들의 등록 빈도 감수
		- 모든 클래스를 일일이 등록해주기는 번거로워서 Annotation 기반으로 등록
		- 앞의 EmailController도 **@Controller** 어노테이션이 붙어서 자동으로 Bean으로 등록된 것
	- Application Context 활용 장점
		- 본연의 기능 및 관심사에 집중 가능
		- Bean의 종합적 관리 자동화(객체관리는 스프링이 자체적으로 알서)
	- Spring에서 관리하는 Bean의 수명
	    1. Bean은 Spring Container의 컨테이너 내에 한 개의 객체로 생성. 이를 Singleton Registry로 관리
	    2. (생성된 Bean은) 강제로 제거하지 않는 한 스프링 컨테이너 내에 계속 유지

---
#### 의존관계 주입 (의존성 주입 : DI) 

1. 의존관계 설정
	- 의존관계 (dependency)
		- ‘A’가 ‘B’에 의존한다. 는 것 방향성이 있음 (A->B)
	- 의존관계 주입(의존성 주입 DI, Dependency Injection)
		- 객체 간의 관계 설정 의도 의도 표현
	- 스프링에서 의존관계 주입 조건
		- 런타임 시점의 의존관계는  Application Context같은 제3의 객체가 결정

2. Annotaion을 이용한 DI
	- @Autowired
		- Spring Framework에서 지원
		- Spring이 자동으로 빈(Bean) 찾아서 주입해 줌.
		- Spring Framework에 종속적(단점)
		- 사용 위치
			- 필드 : 클래스의 멤버 변수에 직접 붙일 수
			- 생성자: 클래스의 생성자에 @Autowired를 붙여 해당 생성자가 호출될 때 의존성을 자동 주입
			- 세터 메서드 
		- 주입하려는 빈의 타입을 필수로 설정하지 않으려면  @Autowired(required=false)
	- @Inject
		- java 표준으로 만들어진 어노테이션
		- 특정 프레임워크에 종속되지 않음

---

순환 의존성 (circular dependency)

- 서로서로 의존하는 관계의 Bean을 생성 순환 의존성이 발생
- Bean 구성할 때 순환 의존성을 피해야 함
- 순환 의존성을 해결하는 방법
	1. 필드 주입
	2. @Lazy 어노테이션 사용

---

#### AOP의 개념

Aspect Oriented Programming
- 관점 지향 프로그래밍
- 어플리케이션의 핵심적인 기능에서 부가적인 기능을 분리하여 별도 모듈로 구분하여 설계하고 개발하는 방법
- 어플리케이션을 다양한 측면에서 독립적으로 모델링하고, 설계하고, 개발할 수 있도록 함.
    
Aspect란?
- 객체지향 기술에서 부가기능 모듈을 부르는 이름
- Aspect 자체로는 어플리케이션의 핵심기능을 담고 있지는 않지만 요소요소마다 공통 관심사항이 될 수는 있음
- 어플리케이션을 구성하는 보조적인 한 가지 측면
    
AOP 주요 용어
- 타겟(Target)
- 어드바이스(Advice) : 타겟에 제공할 부가기능을 담은 모듈
- 조인포인트(Join Point) : 어드바이스가 적용될 수 있는 위치
- 포인트 컷(Pointcut) : 스프링의 포인트 컷은 특정 타입 객체의 특정 메서드를 선정하는 기능

- AspectJ 활용
	- @AspectJ
	- dependency
		- spring-aop
		- aspectjrt
		- aspectjweaver
    
- Pointcut 활용

