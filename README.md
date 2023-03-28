# java-spring-answer-note-for-junior
인프런 강의 - Java/Spring 주니어 개발자를 위한 오답노트 정리

# 오리엔테이션
Q001. 순차 지향 프로그래밍과 절차 지향 프로그래밍의 차이?
순차 지향 언어 - 어셈블러
절차 지향 언어 - C언어
순차 지향 프로그래밍 - Sequential oriented programming
절차 지향 프로그래밍 - Procedure oriented programming
절차 지향 프로그래밍은 함수 지향 프로그래밍

# 실천할 수 있는 컨벤션 교정
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
