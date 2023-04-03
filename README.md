# study-java-spring-answer-note-for-junior
인프런 강의 - Java/Spring 주니어 개발자를 위한 오답노트 정리

# 오리엔테이션
Q001. 순차 지향 프로그래밍과 절차 지향 프로그래밍의 차이?
순차 지향 언어 - 어셈블러
절차 지향 언어 - C언어
순차 지향 프로그래밍 - Sequential oriented programming
절차 지향 프로그래밍 - Procedure oriented programming
절차 지향 프로그래밍은 함수 지향 프로그래밍

# 주니어 개발자가 많이 하는 개발 실수
## 실천할 수 있는 컨벤션 교정
### 1. 이름    
변수이름: camelCase    
함수이름: camelCase    
클래스이름: PascalCase    
패키지이름: alllowercase    
상수: UPPER_SNAKE_CASE    

메소드 이름은 동사로 시작해야함!    
줄여쓰기 - 가능하면 풀어서 쓰기(줄여 쓰지 말기)    
축약어 - 대문자로 표현하지X ex) memberDTO (x) -> memberDto    

### 2. 동사
- get vs find    
get -> return type이 T인 경우(항상 인스턴스를 돌려받을 것을 가정)    
find -> return type이 Optional<T>인 경우(null인 경우가 있음)

- get 남발하지 않기!
일반적으로 get은 내가 갖고있는 멤버변수를 그대로 돌려준다는 의미가 강함.    
ex) 모든 값을 더해서 가져오는 것 : getSumPrice(x) -> sumPrice(o)
	
### 3. 롬복
- getter setter를 남발하지 마라 : 캡슐화를 망치는 주범, 객체를 수동적이게 만듦.
수동적인 예)
```java
@Getter
@Setter
class User {
	enum Status{ACTIVE, INACTIVE}
	
	private long id;
	private String name;
	private Status status;
	private long lastLoginTimestamp;
}

class UserManager {
	public void doSomething(User user) {
		user.setStatus(Status.ACTIVE);
		user.setLastLoginTimestamp(Clock.systemUTC().millis());
	}
}
```

능동적인 예)
```java
class User {
	enum Status{ACTIVE, INACTIVE}
	
	private long id;
	private String name;
	private Status status;
	private long lastLoginTimestamp;
	
	public void inactive() {
		this.status = Status.INACTIVE;
	}
	
	public void login() {
		this.lastLoginTimestamp = clock.millis();
	}
}

class UserManager {
	public void doSomething(User user) {
		user.inactive();
		user.login(Clock.systemUTC().millis());
	}
}
```
	
### 4. 가독성
- 주석은 정말 필요할 때만 사용, 메소드 추출하기
- Optional 자주 사용하기
- Map 남발하지 않기

### 5. 관습
- start(포함) end(미포함)
	

더 알아보기
1. 검증이 필요할 때 무엇을 쓸까? verify vs validate vs check vs is     
2. 코드 스타일 - 구글 코드 스타일 가이드    
3. 클래스 생성 시 단어 조합은 3개 이하로 권장    

## 객체 지향적인 코드 짜기(1): 객체의 종류, 행동
### 1. 객체의 종류
- VO(Value Object) : 모든 변수들을 final로 선언해서 상태를 변경할 수 없는 객체, 항상 불변해야 하며 어떤 메소드를 호출해도 값이 항상 일관됨(불변성)
```java
class UserInfo {
	private final long id;
	private final String username;
	private final String email;
	
	public UserInfo(long id, String username, String email) {
		assert id > 0;
		assert StringUtils.isNotEmpty(username);
		assert EmailValidator.isValid(email);
		this.id = id;
		this.username = username;
		this.email = email;
	}
}
```
번외) 생성자의 역할     
값을 검증, 값을 할당하는 두 역할만 한다.    
- DTO(Data Transfer Object) : 데이터 전송 객체, 메소드, 클래스, 프로세스 간에 데이터를 주고 받을 때 쓰는 객체, 모든 속성을 노출함(모든 멤버변수가 public)
- Entity : 보통 데이터 베이스에 저장함
	+ 유일한 식별자가 있고,
	+ 수명 주기가 있으며, 
	+ 쓰기 모델 저장소에 저장함으로써 지속성을 가지며 나중에 저장소에 불러올 수 있다.
- PO(Persistence Object) : DB의 Entity

### 2. 디미터 법칙
최소 지식의 법칙
: 모듈은 자신이 조작하는 객체의 속사정을 몰라야 한다.    
TDA 원칙 (Tell Don't Ask) 물어보지 말고 일을 시키라

### 3. 행동
struct와 class는 다르다.
데이터 위주의 사고 < 행동 위주의 사고
일반적으로 행동 위주의 사고가 객체지향일 확률이 높다.
- duck typing : 어떤 새가 오리처럼 걷고, 날고, 오리처럼 꽥꽥거린다면 오리라고 부르겠다. (행동이 같다면 같은 클래스로 부르겠다.)    
ex) 자동차 클래스를 만든다고 했을 때 단순히 필드만 나열하는 것은 데이터 위주의 사고, 자동차가 하는 일을 method로 정의하면 행동 위주의 사고.

### 4. 순환 참조
순환참조, 양방향 참조 만들지 말기.
1. 순환 의존성 자체가 결합도를 높이는 원인이 된다.
2. 순환참조 때문에 serialize가 불가해진다.
- 간접 참조로 해결하기
- 컴포넌트 분리(중간에 한 클래스를 생성)

더 알아보기
1. 항상 하면 좋은 고민
- final이어야 할까?
- 변수명
2. VO의 변경자
- 새로운 VO를 반환한다.
- VO의 변경자 이름(ex. changePassword < withNewPasswird)

## 설계(1) : 의존성이란 무엇인지?(DI vs DIP)
### 1. SOLID
- Single reponse (단일 책임 원칙) : 클래스는 하나의 목적만을 가져야 한다. (응집도와 연관이 있는 원칙)
- Open-Closed (개방-폐쇄의 원칙) : 확장에 대해 열려 있어야 하고, 수정에 대해서는 닫혀 있어야 한다. => 추상화(인터페이스화)가 부족한 경우
- Liskov substitution (리스코프 치환 원칙) : 상위 클래스와 하위 클래스 사이의 계약이 깨지지 않아야 한다.
public 메소드는 다른 말로 인터페이스라고 한다. (인터페이스 == 계약)
- Interface-Segregation (인터페이스 분리의 원칙) : 클라이언트가 자신이 이용하지 않는 메서드에 의존하지 않아야 한다는 원칙 => 인터페이스를 분리해서 조립하듯이 개발하지 않을 경우    
인터페이스란? : 이 기능을 사용하고 싶다면 이 방법을 사용하라고 알려주는 것    
API도 큰 범주에서는 인터페이스다.    
- Dependency inversion (의존성 역전 원칙) : 상위 모듈은 하위 모듈에 의존해서는 안된다. 추상화에 의존해야 한다. 추상화는 세부 사항에 의존해서는 안된다. 세부사항이 추상화에 의존해야 한다.
	
### 2. 의존성
의존성이란 무엇인가?     
의존성은 결합도와 같은 말이고 다른 객체나 함수를 사용하는 상태이다. A객체가 B객체를 사용하면 A는 B에 의존한다고 볼 수 있다.
- 의존성 주입 : 필요한 값을 외부에서 의존성을 넣어주면 의존성 주입.
1. 파라미터 주입
2. 필드 주입
3. 생성자 주입
의존성 주입을 받는다고 의존성이 사라진 것은 아님. => 의존성을 약하게 하는 역할.
	
Dependency Injection은 의존성 주입(DI)    
Dependency Inversion은 의존성 역전(SOILD-DIP)    
- 의존성 역전 : 의존성 역전은 화살표의 방향을 반대로 바꾸는 기술    
ex) MacDonald -> HamburgerChef 를    
 MacDonald -> (interface) Chef <- HamburgerChef 로 바꾸는 것
- 의존성 주입과 책임 : 생성자 의존성 주입이 7개 이상 넘어간다면, 파라미터 의존성 주입이 4개 이상 넘어간다면 클래스 분할이나 메소드 분할을 고려해야 한다.
				  
### 3. 의존성 조언
- 의존성을 드러내라
의존성이 숨겨진 예시 
```java
class User {
	private long lastLoginTimestamp;
								  
	public void login() {
		this.lastLoginTimestamp = Clock.systemUTC().millis();
	}							  
	
}
```
외부에서 보면 login은 분명 Clock에 의존적임.    
개발자들이 의존성을 실수로 숨기게 되는 흔한 케이스 : 시간, 랜덤(실행할 때마다 "변하는 값")    
- 변하는 값은 외부에서 주입받아라.    
테스트를 강조하는 이유 : 의존성이 제대로 풀려있지 않다면 테스트가 힘들기 때문.    
- 변하는 값을 추상화시켜라.    
런타임 의존성과 컴파일 타임 의존성을 다르게 하라.
**의존성을 추상화 시키는 방식 중요!!!**

### 4. CQRS
Command and Query Responsebility Segregation
명령과 질의의 책임 분리    
: 하나의 메소드는 명령이나 쿼리여야하며, 두 가지 기능을 모두 가져서는 안된다. 명령은 객체의 상태를 변경할 수 있지만, 값을 반환하지 않는다. 쿼리는 값을 반환하지만 객체를 변경하지 않는다.
- Command    
명령메소드는 객체의 상태를 변경시키고 return 값을 가지지 않는다.    
- Query     
질의 : 상태를 물어보는 메소드, 상태를 변경해서는 안된다.

더 알아보기
1. 정답이 없다 (개발자는 계속 trade-off를 해야한다.)
2. 리팩토링 Working Effectively with Legacy code 책 추천
3. 다양한 설계 조언
				  
#### 기타 팁   
프로그래머 vs 소프트웨어 엔지니어    
시간, 규모, 트레이드 오프를 고려할 줄 아는 사람이 엔지니어다.

객체지향 생활 체조 9가지 원칙    
오늘날 더 나은 소프트웨어를 향한 9단계
1. 한 메서드에 오직 한 단계의 들여쓰기(indent)만 한다.
2. else 예약어를 쓰지 않는다.
3. 모든 원시값과 문자열을 포장한다.
4. 한 줄에 점을 하나만 찍는다.
5. 줄여쓰지 않는다.(축약 금지)
6. 모든 엔티티를 작게 유지한다.
7. 3개 이상의 인스턴스 변수를 가진 클래스를 쓰지 않는다.
8. 일급 컬렉션을 쓴다.
9. getter/setter/property를 쓰지 않는다.


### 습관
- StringUtils - String의 null체크 시 코드가독성이 좋다.
- 상속을 지양하고 Composition을 지향할 것.
- 테스트를 먼저 생각하기. -> 테스트하기 쉬운 코드가 잘 설계된 코드
- 블락이 생긴다면 메소드로 분할을 고려하기.
- 들여쓰기가 2개 이상 된다면 메소드 분할을 고려하기. ex) if문과 for문으로 들여써질 때.

# 스프링을 OOP스럽게 사용하기
## 스프링에서 OOP와 안티 패턴 : Transaction script
안티패턴 두 가지 : smart UI(Controller), Transaction script
### 1. smart UI
- 스마트 컨트롤러를 피하기 => 컨트롤러의 역할은 어떤 서비스를 실행할지 선택하는 정도만 하기.
- 레이어 아키텍쳐 : 바로 하위의 레이어만 의존하도록 하는 방식
- DDD 레이어 : Contoller -> service -> Domain model -> Repository    
"소프트웨어가 수행할 작업을 정의하고 표현력있는 **도메인 객체가 문제를 해결하게 하는 레이어**, 다른 시스템의 응용 계층과 상호작용하는데 필요한 것들. **이 계층은 최대한 얇게 유지되어야 한다.** 업무 규칙이니ㅏ 지식이 포함되지 않아야 한다. **오직 작업을 조정하고 아래에 위치한 계층에 도메인 객체의 협력자에게 작업을 위임한다.**"    
서비스 컴포넌트는 도메인 객체에 작업을 위임한다.    
도메인 = 비즈니스 모델

### 2. Transaction script
- 서비스 컴포넌트에 모든 업무 규칙과 지식들이 들어가 있는 경우
- 절차지향적, 수동적인 도메인
- 사실상 서비스 계층이 트랜잭션을 수행하는 스크립트 역할이기 때문    
=> 서비스는 작업을 조정하고, 비즈니스 로직을 도메인에게 위임해야 한다. 비즈니스 로직을 도메인이 들고있어야 한다.    
서비스가 가지고있던 과한 책임을 객체로 위임해야 함.

- 도메인 서비스 : "가격 계산"같이 객체로 표현하기 애매하고, 논리 로직 자체가 목적인 행위자를 도메인 서비스라고 한다.
- 애플리케이션 서비스는 비즈니스 결정을 내리지 않아야 함.
- 애플리케이션 서비스는 요청을 도메인과 도메인 서비스에 위임한다.

 더 알아보기
 DDD (Domain Driven Design)
 Event Driven Design
 OOP (Object Oriented Programming)
 
## 어디까지 추상화해야 하는가?
### 1. 추상화
추상화 : 모듈을 격리하고 인터페이스를 만드는 과정, 책임을 선별하는 과정
- 응용 프로그램 서비스는 구체다.    
Controller, Service, Entity, VO는 구현체로 구현되어도 상관없다.    
서비스와 컨트롤러는 한번 생성으로 영원히 같은 일을 할 수 있는 객체여야 한다.

### 2. Overview
![image](https://user-images.githubusercontent.com/104114755/228725677-09ba654f-5671-4280-b256-bc79c6496647.png)

## 서비스란 무엇인가?
### 1. 서비스
애플리케이션 서비스는 도메인과 도메인 서비스에게 책임을 위임하는 Facade 패턴의 일종
- 자신의 본거지를 Entity나 Value Object에서 찾지 못하는 중요한 도메인 연산이 있다.
- 이름 끝에 Manager와 같은 것이 붙는다.
- 중요한 것 : 풍부한 도메인을 만들고 서비스는 가능한 적고 얇게 만들어라.

### 2. 서비스에 관한 조언
서비스는 한번 생성하면 특정 작업을 영원히 실행할 수 있는 객체    
(서비스의 불변성)    
- 서비스를 얇게 유
- 서비스의 멤버변수는 모두 final로 선언되어야 한다.
- 서비스에 setter가 존재한다면 지우기
- 반드시 생성자 주입으로 사용.

## 기타 꿀팁
### 1. JPA
- JPA는 기술 명세이고 Hibernate는 구현체이다. 
![image](https://user-images.githubusercontent.com/104114755/228727426-a7064513-0100-45f4-9287-e60e047ac3f9.png)

연관 관계의 주인은 외래키이다. 
- n + 1문제    
해결법 : 1. EntityGraph 2. Fetch join
	
### 2. 기타

# 테스트는 왜 해야하는가?
## 테스트를 해야하는 이유와 테스트의 분류
### 1. 필요성
- 레거시 코드 : 테스트 루틴이 없는 코드 (-레거시 코드 활용 전략 중)
- Regression : 회귀 - 기능이 동작하지 않는 과거로 회귀했다. 회귀 테스트가 완벽한 테스트는 개발이 쉽다.

### 2. 테스트의 종류
- 인수 테스트 : 체크리스트로 관리하면서 테스트 항목을 하나하나 사람이 검사하는 것
- 자동 테스트 : 소프트웨어를 이용해서 자동으로 테스트하는 것
- 테스트의 3분류 : API테스트, 통합 테스트, 단위 테스트 / 소형, 중형, 대형 테스트
	+ 소형 테스트 : 단일 서버, 단일 프로세스, 단일 스레드에서 돌아가는 테스트, Disk IO가 있어서는 안되고 Blocking call이 있어서도 안됨. 
	+ 중형 테스트 : 멀티 프로세스와 멀티 스레드 사용 가능 (h2같은 DB 사용 가능)
	+ 대형 테스트 : 멀티 서버 사용 가능, 보통 End to End 테스트를 의미함. 
우리가 집중해야 하는 것은 소형 테스트이다. 

### 3. 안티 패턴
- 아이스크림 패턴 : API 테스트만 늘리는 경우
- 모래 시계 패턴

더 알아보기    
테스트와 CI/CD
	
## 테스트에 필요한 개념
- SUT : System under test(테스트 하려는 대상)
- TDD : 테스트 주도 개발 
1. 깨지는 테스트를 먼저 작성한다.
2. 깨지는 테스트를 성공시킨다.
3. 리팩토링한다.	
- BDD : Behaviour driven developer (given - when - then), 메소드 위주의 테스트 코드보다 시나리오에 기반한 테스트를 하는 방식
- 불규칙한 테스트(flaky) 깨지기 쉬운 테스트(brittle)
- 상호작용 테스트보다는 상태를 테스트하는 게 좋다.
- 테스트 더블 : 가짜 객체로 테스트 하는 것
	+ Dummy : 아무런 동작하지 않는 객체
	+ Fake : 가짜 객체, 자체 로직을 가지고 있음
	+ Stub : 외부 연동하는 컴포넌트들에 많이 사용
	+ Mock : 사실상 테스트 더블과 동일한 의미로 사용됨.
	+ Spy : 모든 메소드를 호출을 기록하고 있는 객체 
- 도구 
	+ Mock 프레임워크

더 알아보기    
1. BDD
2. PARAMETERIZED TEST
3. MOCKIST | CLASSICIST
4. TFD : 테스트 우선개발

## 테스트 기법 소개
### 1. 조언
- private 메소드 : private 메소드는 테스트하지 않는다.
- fianl 메소드 : final 메소드를 stub해야하는 상황이 생긴다면 설계가 잘못된 것이다.
- DRY < DAMP : Don't Repeat Yourself(반복하지 않기) , 테스트 시에는 중복된 코드가 더 깔끔할 수 있다.
- 논리 : 테스트에 논리(+,-,for,if를 넣지 말)
	    
### 2. 기법
- 의존성 추상화
의존되는 것을 인터페이스로 분리하여 테스트와 실제 실행 시의 클래스를 구분
- 이벤트 기록
내부에 기록한 이벤트를 사용해 변경 가능 객체의 변경을 확인한다.

더 알아보기    
1. 오버 엔지니어링
2. 추상화는 대체적으로 좋다
3. 테스트는 행동 위주의 사고를 도와준다.
4. TDD의 한계	

# 실무에 필요한 개념 정리
## 네트워크 용어 정리(1) : DNS, BD, FQDN 등
### 1. 도메인, DNS, VIP
- 도메인, 도메인을 실제 서버와 연결시켜 주는 것을 DNS 서버라고 한다. ex)Cloudflare	
- VIP : 가상 IP
### 2. LB
- LB(Load Balancing) 로드밸런싱 : 부하 분산, 트래픽 부하를 하위 장비들에 분산하는 것 
	+ 라운드 로빈 
### 3. SLB, GSLB
- SLB : 서버 로드밸런싱	
- GSLB : 지역을 넘나드는 서버 로드밸런싱

### 4. FQDN
- Full domain -> 서브 도메인을 포함해서 말하는 것

더 알아보기    
1. 포트포워딩
LB 구성 시)    
2. DSR 구성
3. Inline 구성

## 네트워크 용어 정리(2) : CDN, ACL, Proxy, SSL 등
### 1. CDN
- 콘텐츠를 효율적으로 송,수신하기 위해 만들어진 네트워크
	
### 2. ACL 
- Access Control List의 약자, 네트워크 망에 접근을 허가하는 IP 리스트를 의미.
사내 망에서 외부와 연결하기 위한 통로
	
### 3. Proxy
- 프록시 서버 : 클라이언트가 자신을 통해서 다른 네트워크 서비스에 간접적으로 접속할 수 있게 해주는 컴퓨터 시스템
프록시를 사용하는 이유
1. Cache
2. Access control
3. 보안 (데이터 유출 보호)
4. 사용률 파악
포워드 프록시 (일반적인 프록시를 지칭) - 인터넷 앞에 있는 것
리버스 프록시 - 인터넷 뒤에 있는 것
	
### 4. SSL
HTTP를 HTTPS로 만들어주는 보안 프로토콜

SSL 인증서 등록 방식(1)
1. 서버에 접속하여 비대칭 키 생성
2. 만들어진 public key를 가지고 CA에 요청
3. CA에서 만들어진 인증서를 서버에 저장

 더 알아보기    
1. nginx
2. OpenSSL
3. CA 기관 - Let's encrypt
	    
## 인프라 용어 정리 : Xaas, 컨테이너 등 
### 1. 장비
### 2. XaaS, 온프레미스, 온디맨드
- IaaS : Infrastructure as a Service, VM을 제공하는 Service
- SaaS : Software as a Service, ex) 구글 드라이브, 슬랙

- 온프레미스 : 전산실 서버에 직접 설치해 운영하는 서비스를 전달하는 방식
- 온디맨드 : 수요가 있을 때 서비스하는 방식
### 3. PM, VM, Container, Docker
- PM : Physical Machine, 컴퓨터
- VM : Virtual Machine, 가상머신
- Container : VM 보다는 조금 더 가벼운 방식의 가상화 기술, cgroups를 제공해야 사용 가능
- Docker : 컨테이너를 독립된 가상환경으 여러 개 띄울 수 있다.

### 4. VPN, VDI
- VPN (Virtual Private Network) : 공중 네트워크를 통해 한 회사나 몇몇 단체가 내용을 바깥 사람에게 드러내지 않고 통신할 목적으로 쓰이는 사설 통신망이다. 

## 쿠버네티스 맛보기
### 1. 쿠버네티스의 개요
전통적인 배포 시대
- 배포를 할 때 서버에 ssh로 붙어서 하나씩 배포 
- 빌드 도구 : Jenkins
- 빌드된 파일 : jar | war
- 배포 도구 : Jenkins | Ansible
	    
가상화된 배포 시대
- 노드에 독립적인 여러 서버를 띄울 수 있게 됨
- 배포를 할 때 서버에 ssh로 붙어서 하나씩 배포
- 빌드 도구 : Jenkins
- 빌드된 파일 : jar | war
- 배포 도구 : Jenkins | Ansible
	    
컨테이너 개발 시대
- 개발자들이 아예 OS까지 포함된 컨테이너까지 지정하여 개발할 수 있게됨
- 빌드 도구 : Jenkins + docker 
- 빌드된 파일 : Docker image
- 배포 도구 : Kubernetes ( + argo cd)

컨테이너 오케스트레이션
쿠버네티스, 도커 스웜

### 2. 실제 구성
nginx -> ingress -> service -> pod
요청이 오는 흐름ingress에 오는 요청을 servic로 보내고, pod로 보낸다. (도커를 여러 개 띄울 수 있는 것 pod)

### 3. LBaaS, Node
- LBaaS : 쿠버네티스의 service단을 ingress 앞에 두는 것

### 4. 빌드/배포
- 개발자 -> github 훅 -> 젠킨스 -> 도커이미지 생성 ->컨테이너 레지스트리에 등록

더 알아보기
1. probe : 쿠버네티스에게 헬스체크를 어떻게 하는지 알려주는 방식
2. resource 

## NoSQL 정리
### 1. 목적에 맞는 다양한 사용
1) 중앙화 된 캐시가 필요한 경우
- 조회수나 좋아요 등 빠르게 변하는 값을 저장 -> Redis를 많이 사용
2) 메시지 큐가 필요한 경우
- 통신하는 데 있어서 데이터 유실이 걱정될 경우 시스템 큐를 둘 수도 있다. -> kafka, RabbitMQ, redis
3) 검색이 필요한 경우
- 검색 기능을 개발자가 일일이 만들지 않고 -> Elasticsearch, Solr 사용 
4) 시계열 차트를 그려야하는 경우
- influxdb, OPENTSDB, druid 등

### 2. Key-value DB
- redis : 서버 자체가 하나의 거대한 Map(or dictionary)으로 구성되어 있음. 이 Map이 메모리에 올라와있어 빠르게 데이터를 가져올 수 있음.     
싱글 스레드, 샤딩과 클러스터 모드, Keys 사용 금지 
	
### 3. Wide-column DB
: 대량의 데이터를 동적인 칼럼을 갖는 테이블에 read/write 하는 DB, 데이터 압축, 분산처리에 특화되어 있음.
- RDBMS와 다르게 relation이 없어 write 성능이 빠르다.
- Cassandra
Consistent hashing(Cassandra에서 데이터를 저장하는 방법), Partition key가 중요, Write 성능이 매우 좋음
- HBase	
	
### 4. Document DB
- MongoDB : 트랜잭션 지원, 전문 검색 기능 제공
- elasticsearch : 인덱스(데이터 스키마), 인덱스가 많아지면 X, update가 없음, 통계내기에 상당히 유용
	
### 5. Graph DB
- neo4j : 사회적 관계망을 표현하는데 사용하기 좋은 DB
	
### 6. Message system
- kafka : topic / partition / consumer group / consumer 4 개의 개념    
Producer가 Topic에 데이터를 publish하면 Topic은 데이터의 key값에 따라서 특정 파티션으로 데이터를 보낸다(분산) -> 파티션을 바라보고 있는 컨슈머들이 데이터를 들고와서 처리    
하나의 파티션에는 하나의 컨슈머만 붙을 수 있다.
	
- RabbitMQ : 자체 관리 페이지 GUI, Topic exchange, Fanout exchange    
솔루션 자체가 하나의 Queue로 동작
	

더 알아보기    
1. Lucene 전문 검색 라이브러리
2. ELK
3. Hadoop
4. Zookeeper : 분산환경의 클러스터 

## 개발 용어 정리 : OAuth, Rest, 불변성 등
### 1. Rest API
1) Uniformb (일관성)
2) Stateless (무상태성)
3) Cacheable (캐시 가능)
4) Self-descriptive (인터페이스만 봐도 이해 가능)
5) Client - Server 구성
6) 계층형 구조
- url에는 리소스를 칭하는 명사(복수형)를 넣는다.
- 리소스를 생성할 때는 post method를 사용
- 하위 데이터에 접근할 때는 /를 이용해 드릴 다운해 나가는 방식 ex) localhost:8080/users/1
- 수정할 때는 PUT or PATCH 사용, but 거의 PUT을 사용한다.
- url에는 명사만 넣는다. 동사를 넣고 싶을 때는 state라는 필드를 넣어주기

### 2. OAuth
- Open Authorization의 약자로 구글, 네이버 등으로 로그인하게 하는 것 (소셜 로그인)
- 인증 vs 인가
- 토큰이 유효하지 않을 때 : 401 (Unauthorized)
- 토큰에 권한이 없을 때 : 403 (Forbbien)
- SSO : 로그인 한 번으로 여러 서비스를 이용할 수 있는 것

### 3. 불변성
- 변하지 않는 성질, 설계를 단순화 시킬 수 있는 핵심 요소
- 객체에 불변성이 적용된 경우 : VO, 불변 객체, Immutable / Java의 String, React의 State
- 함수에 불변성이 적용된 경우 : 순수 함수 / 언제나 같은 결과를 내는 함수
- 함수형 프로그래밍 : 자료 처리를 수학적 함수의 계산으로 취급하고, 상태와 가변 데이터를 멀리하는 프로그래밍 패러다임의 하나.
- 부수 효과 (side effect) : 같은 요청을 했음에도 예상했던 결과와 다르게 나오는 값.

### 4. Library / Framework
- 컴포넌트의 생성과 호출의 주체가 누구인가?
- IoC Container의 유무 (있으면 프레임워크, 없으면 라이브러리)

### 5. CORS / XSS
- CORS : 아무곳에서나 내 서버를 호출하지 못하도록 하는 정책
- XSS (크로스 사이트 스크립팅) : 자바 스크립트 코드를 삽입해 개인정보를 탈취하는 위험성

### 6. 기타
- 멱등성 : 여러 번을 실행해도 실행 결과가 동일하다.
- 공변성 : 서브타입이 슈퍼타입 대신 사용될 수 있다.
- 반공변성 : 슈퍼타입이 서브타입 대신 사용될 수 있다.
- 무공변성 : 슈퍼타입과 서브타입이 아무런 관계도 없다.

더 알아보기
1. Rest API
2. WebSocket
3. 데이터 표현방식
4. LDAP ID (사내 아이디)
