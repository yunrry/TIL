
# MSA와 REST의 관계 및 Spring을 이용한 REST 서비스 구현


### MSA

- Microservice Architecture
- 대규모 어플리케이션을 작은 독립적인 서비스들(마이크로서비스)로 분리하여 개발하는 아키텍처

- 특징
	- 독립적으로 개발,배포, 유지보수가 가능(독립성)
	- 작고 명확한 역할을 가짐
	- 분산 시스템
	- 기술적 다양성 

### REST

- Representational State Transfer
- HTTP 프로토콜을 이용하여 리소스를 주고 받는 웹 아키텍처 스타일
- 클라이언트 - 서버 간의 통신에서 상태를 유지 않으며(Stateless)
- 각 리소스는 고유한 URI로 식별되고
- 다양한 HTTP 메서드(GET, POST, PUT, DELETE)를 사용여 조작됩니다.
    
- 특징
	- HTTP 기반
	- 무상태성: 서버는 클라이언트의 상태를 저장하지 않고, 모든 필요한 정보는 각 요청에 포함되어야함
	- API 호출 규약이 일관되고 직관적이어야 함
	- HTTP 응답을 클라이언트가 캐싱할 수 있음
    
- REST API 설계시 고려할 사항
	- 자원 명명(Naming Resources)
		- 자원(Resource)은 명사로 표현하고, 소문자와 복수형으로 작성 
		- 예: /users (사용자 리스트) , /orders (주문목록)    
	- 버전 관리
		- API의 버전을 명시. 클라이언트와의 호환성을 유지
		- 예: /api/v1/users (버전1 사용자 API)
	- 상태 코드
		- 요청에 대한 응답으로 HTTP 상태 코드를 활용해 클라이언트에 처리 결과를 알립니다.
			- 200  OK : 성공적으로 요청 처리
			- 201  Created : 새로운 리소스가 성공적으로 생성됨
			- 400  Bad Request : 잘못된 요청
			- 401  Unauthorized
			- 404  Not Found 

- REST와 RESTful API의 차이
	- REST는 아키텍처 스타일을 말하며, 이를 따르는 API를 RESTful API라고 부름
	- RESTful API는 REST의 원칙을 준수하여 설계된 API를 의미
    
- MSA에서 REST의 역할
	- 독립된 서비스 간 통신
	- 인터페이스 표준화 : HTTP 기반 표준 인터페이스 제공공
	- 분산 시스템에서의 유연성
  
- Spring을 이용한 REST API 구현
	- REST 서비스가 스프링과 유일하게 다른 점은 스프링 MVC 디스패처 서블릿이 뷰를 찾지 않는다는 것==(뷰 리졸버가 없다)==
	- 서버는 컨트롤러의 액션이 반환하는 것을 ==클라언트에 대한 HTTP응답으로 직접 전송==한다.
	- 스프링을 사용하면 REST 서비스의 구현이 간단해짐.

- Spring에서 REST 엔드포인트 구현
	- 1. Controller에서 REST API의 엔드포인트를 정의
	- 2. HTTP 메서드를 매핑 : 애너테이션 사용
	- 3. Request/Response Body 처리 : 
		- @RequestBody  : 클라이언트에서 전송한 JSON데이터를 자바 객체로 반환
		- @ResponseBody : 자바 객체를 JSON 형식으로 변환해 클라이언트에 반환
	
	- [실습] “고양이 백과 사전” REST API
		- 클라이언트가 고양이의 이름을 요청하면, 고양이의 정보(나이, 종류, 털 색깔)을 제공하는 간단한 RESTful 서비스를 제공
		- 새로운 고양이를 추가할 수 있음
		- Spring Boot
