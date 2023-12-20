# Chapter02 객체지향 프로그래밍

## 영화 예매 시스템

이번 장에서 다루는 예제는 영화 예매 시스템이다.

- 영화관에서 사용하는 할인액을 결정하는 두 가지 규칙
    - 할인 조건
        - 영화별로 다수의 할인 조건 할당 가능
        - 할인 조건 미할당 가능
        - 순서 조건
        - 기간 조건
    - 할인 정책
        - 영화별로 하나의 할인 정책만 할당 가능
        - 금액 할인 정책
        - 비율 할인 정책

### 객체지향 패러다임 작성 고민

- 어떤 클래스가 필요한지 고민하기 이전에 어떤 객체들이 필요한지 고민
    - 클래스의 윤곽을 잡기 위해서는 각 객체가 어떤 상태와 행동을 갖는지를 알아야 함
- 객체는 협력에 참여하는 참여자
- 훌륭한 협력이 훌륭한 객체를 낳고 훌륭한 객체가 훌륭한 클래스를 낳는다.

### 도메인의 구조를 따르는 프로그램 구조

사용자가 문제를 해결하기 위해 프로그램을 사용하는 분야를 **도메인**이라고 부른다.

- 도메인을 구성하는 개념들이 프로그램을 프로그램 객체와 매끄럽게 연결될 수 있다.
- 클래스는 도메인 개념 사이에 맺어진 관계와 유사하게 만들어서 프로그램의 구조를 이해하고 예상하기 쉽게 만들어야 한다.
- 도메인을 구성하는 개념과 관계를 클래스로 유사하게 표현, 설계한 후 실제 구현에 들어간다.

```java
public class Screening {
    private Movie movie;
    private int sequence;
    private LocalDateTime whenScreened;

    public Screening(Movie movie, int sequence, LocalDateTime whenScreened) {
        this.movie = movie;
        this.sequence = sequence;
        this.whenScreened = whenScreened;
    }

    public LocalDateTime getStartTime() {
        return whenScreened;
    }

    public boolean isSequence(int sequence) {
        return this.sequence == sequence;
    }

    public Money getMovieFee() {
        return movie.getFee();
    }

    public Reservation reserve(Customer customer, int audienceCount) {
        return new Reservation(customer, this, calculateFee(audienceCount),
                audienceCount);
    }

    private Money calculateFee(int audienceCount) {
        return movie.calculateMovieFee(this).times(audienceCount);
    }
}
```

### 클래스 구현하기 - 내부, 외부 구분하기

- 인스턴스 변수 : private
    - 객체의 상태를 나타냄
- 메서드 : public
    - 객체에게 메시지를 보내는 용도
- 메서드 : private
    - 객체의 상태를 변경하는 용도
- 내부와 외부를 private와 public으로 구분
    - 객체 지향원칙 `인터페이스와 구현 분리원칙(separation of interface and implementation)`
    - 경계를 명확히 해야 객체의 자율성을 보장하기 때문
    - 프로그래머가 작업하기에 용이

### 클래스(자율적인 객체)

- 상태와 행동을 포함
- 객체가 스스로 판단하고 행동하는 자율적 존재
- 위의 조건을 지키기 위해 데이터와 기능을 객체 내부로 함께 묶는 것 **캡슐화**
- 클래스에는 접근 제어 기능이 있는데, 이를 활용하면 객체를 자율적인 존재로 만들 수 있음
- 객체지향의 핵심은 스스로 상태를 관리, 판단, 행동하는 자율적인 객체들의 공동체를 구성하는 것
- 스스로 관리 판단 행동하도록 하기 위해서는 `외부의 간섭을 최소화해야 함`
- 외부에서 접근가능하도록 하는 `퍼블릭 인터페이스`, 외부에서 접근 불가능한 `구현` 을 통해 객체가 이뤄진다.
- 위의 이야기를 근거로 만들어진 `인터페이스와 구현의 분리 원칙`은 훌륭한 객체지향 프로그램을 만들기 위해 따라야 하는 핵심 원칙이다.
- 실제 객체지향의 구현에서 `private와 public을 통한 내부와 외부`를 잘 구현할 것

### 프로그래머의 자유

프로그래머의 역할을 **클래스 작성자(class creator)**, **클라이언트 프로그래머(client programmer)**로 구분하고 작업을 하기도 하는데, 클래스 작성자는 새로운 데이터 타입을 프로그램에 추가하고, 클라이언트 프로그래머는 클래스 작성자가 추가한 데이터 타입을 사용한다.

### 협력

- 객체의 내부 상태는 외부에서 접근하지 못하도록 감춰야 한다.
- 내부 상태를 접근하는 방법은 `공용 인터페이스`를 통해 처리
- 인터페이스로 통신하는 형식 `요청(request)`과 `응답(response)`

⇒ 객체가 다른 객체와 상호작용할 수 있는 유일한 방법은 `메시지를 전송`(send a message)하는 것
⇒`수신된 메시지를 처리하기 위한 자신만의 private한 방법은 메서드(method)`

메시지와 메서드의 구분에서 `다형성(polymorphism)`의 개념이 출발한다.

## 할인 요금 구하기

### 할인 요금 계산을 위한 협력 시작하기

**예매 요금을 계산하는 협력**

- Movie클래스의 calculateMovieFee메서드를 통해 할인 정책을 통한 값 책정을`discountPolicy.calculateDiscountAmount` 에게 맡기고 다른 처리는 하지 않는다.
    
    ```java
    public abstract class DiscountPolicy{
    	...
    	abstract protected Money getDiscountAmount(Screeing screening);
    }
    ```
    
    - 추상화
    - 상속
    - 다형성

### 할인 정책과 할인 조건

- AmountDiscountPolicy와 PercentDiscountPolicy는 어떻게 구현해야 할까?

### Case01

**Policy**

- DiscountPolicy라는 추상 클래스 생성
- DiscountPolicy를 상속
- 실제 요금을 계산하는 부분은 추상 메서드인 getDiscountAmount 메서드에 위임
- **Template Method 패턴**

**DiscountCondition**

```java
public interface DiscountCondition{
	boolean isSatisfiedBy(Screening screening);
}
```

- 인터페이스
- isSatisfiedBy(Screening)
    - 순번 할인의 경우는 순번과 일치한지 확인
        
        ```java
        public class SequenceCondition implements DiscountCondition{
        	...
        	public boolean isSatisfiedBy(Screening screening){
        		return screening.isSequence(sequence);
        	}
        	...
        }
        ```
        
    - 시간 할인은 요일, 시작 시간, 종료 시간 등 반영
        
        ```java
        public class SequenceCondition implements DiscountCondition{
        	...
        	public boolean isSatisfiedBy(Screening screening){
        		return screening.getStartTime().equals(dayOfWeek) &&
        		...
        	}
        	...
        }
        ```
        
- 할인 조건을 구한 후 할인 정책 구현 - Amount, Percent
    
    ```java
    public class AmountDiscountPolicy extends DiscountPolicy{
    	private Money discountAmount;
    	
    	public AmountDiscountPolicy(Money discountAmount, DiscountCondition ... conditions){
    		super(conditions);
    		this.discountAmount = discountAmount;
    	}
    
    	@Override
    	protected Money getDiscountAmount(Screening screning){
    		return discountAmount;
    	}
    }
    ```
    

### 할인 정책 구성하기

- 할인 정책이 구성되는 부분은 **생성자**
- 할인 정책은 하나만 들어갈 수 있기 때문에 Movie가 생성될 때 정책을 결정
- Movie 생성자 안에 들어가는 conditions 예시
    
    ```java
    Movie avatar = new Movie("아바타",
    	Duration.ofMinutes(120),
    	Money.wons(10000),
    	new AmountDiscountPolicy(Money.wons(800),
    		new SequenceCondition(1),
    		new SequenceCondition(10),
    		new PeriodCondition(DayOfWeek.MONDAY, LocalTime.of(10, 0), LocalTime.of(11,59)),
    		new PeriodCondition(DayOfWeek.THURSDAY, LocalTime.of(10, 0), LocalTime.of(20,59)));
    ```
    

<aside>
💡 컴파일 단계에서는 의존성이 그저 DiscountPolicy 추상클래스만 바라봄 → 컴파일 의존성
코드가 실행되면 AmountDiscountPolicy라는 친구가 만들어짐 → 실행 시간 의존성
이러한 상태를 컴파일 시간 의존성과 실행 시간 의존성이 다른 상태라고 한다.

</aside>

### 컴파일 시간 의존성과 실행 시간 의존성

- **확장성**
    - 확장성이 높은 객체지향 설계는 코드의 의존성과 실행 시점의 의존성이 다르다.
    - 확장성이 높아진다면 코드를 이해하기 어려워짐 → 해당 클래스만을 보고 바로 직관적으로 판단을 못하기 때문
- **직관성**
    - 유연성을 억제하면 코드를 이해하고 디버깅하기는 쉬워지지만, 재사용성과 확장 가능성은 낮아진다.
- **트레이드오프**
    - 유연성과 가독성 사이에서 항상 고민해야 함

### 차이에 의한 프로그래밍

- 상속은 객체지향에서 코드를 재사용하기 위한 가장 널리 사용되는 방법
- (부모)DiscountPolicy + (자식)AmountDiscountPolicy으로 기존 값을 재사용
- 이를 **차이에 의한 프로그래밍(programming by difference)**이라고 ****부름

### 상속과 인터페이스

- 대부분의 사람들은 상속의 목적이 메서드나 인스턴스 변수를 재사용한다고 생각
    - 이 용도도 맞다 하지만..
- 객체가 이해할 수 있는 메시지의 목록을 정의한다.
    - 상속을 통해 부모 클래스의 인터페이스도 자식이 갖고있게 된다.
    - 자식 클래스는 부모가 수신할 수 있는 모든 메시지를 수신할 수 있기 때문에 외부 객체는 부모와 자식을 동일한 타입으로 간주할 수 있다.

```java
public class Movie {
	public Money calculateMovieFee(Screening screening) {
		return fee.minus(discountPolicy.calculateDiscountAmount(screening));
	}
}
```

- discountPolicy는 부모 but 얘는 추상클래스인데?
- 생성자를 통해 자식을 부모에 넣어줬다.
- **컴파일러는 코드 상에서 부모 클래스가 나오는 모든 장소에서 자식 클래스를 사용하는 것을 허용한다.**
- 이렇게 자식이 부모 클래스를 대신하는 것을 `업캐스팅(upcasting)`이라고 부른다

### 다형성

- 메시지와 메서드는 다른 개념
- Movie → DiscountPolicy.calculateDiscountAmount()
    - AmountDiscountPolicy
        - 정량 할인
    - PercentDiscountPolicy
        - 퍼센트 할인
- 두 객체는 각자 오버라이딩 된 메서드를 통해 각자의 할인 정책 시행
- 이렇게 수신되는 메시지는 같지만, 실행되는 방식은 다른 것이 가능한 특징을 **다형성**이라고 부른다.
- **다형성은 객체지향 프로그램의 컴파일 시간 의존성과 실행 시간 의존성이 다를 수 있다는 사실을 기반**
- 위의 이야기를 기반으로 실행 시점에 바인딩하는 것을 `지연 바인딩(lazy binding)` 또는 `동적 바인딩(dynamic binding)` 이라고 부른다.
- 컴파일 시점에 즉시 실행될 함수나 프로시저를 결정하는 방식을 `초기 바인딩(early binding)` 또는 `정적 바인딩(static binding)`이라고 부른다.
- **상속만이 다형성을 구현할 수 있는 유일한 방법은 아님**

### 구현 상속과 인터페이스 상속

- **구현 상속(서브클래싱)**
    - 순수하게 코드를 재사용하기 위한 목적으로 상속을 하는 것
- **인터페이스 상속(서브타이핑)**
    - 다형적인 협력을 위해 부모와 자식 클래스의 인터페이스 공유가 목적인 것을 인터페이스 상속이라고 부른다.

### 인터페이스와 다형성

- C#과 자바에서는 **인터페이스**라는 프로그래밍 요소를 제공한다.
- 기존의 추상 클래스처럼 업캐스팅이 가능

```java
public interface DiscountCondition{
	public boolean isSatisfiedBy();
}
```

## 추상화

기존의 설계 flow는 아래와 같다

- Movie → AmountDiscountPolicy → DiscountCondition
- Movie → PercentDiscountPolicy → DiscountCondition

추상화 이후의 설계 flow ⇒ Movie → DiscountPolicy → DiscountCondition

- 요구사항의 정책을 높은 수준에서 서술 가능
- 설계가 더 유연해짐
- 자식은 추상화를 통해 만들어진 부모의 흐름을 그대로 이어받음
- 상위 정책을 표현하면 기존 구조를 수정하지 않고 새로운 기능을 쉽게 추가 및 확장이 가능함

### 더 유연한 설계

할인 정책은 Percent, Amount, None까지 할인이 안들어간 상태도 포함되어있었기에 할인정책을 사용하지 않는 경우 예외 발생!!

**예외를 해결하기 위해서**

- DiscountPolicy를 부모로 받는 NoneDiscount도 만들어주자

```java
public class NoneDiscountPolicy extends DiscountPolicy {
	@Override
	protected Money getDiscountAmount(Screening screening){
		return Money.ZERO;
	}
}
```

이후 생성자에 그냥 빈 NonDiscountPolicy를 넣어주면 되는데 이러한 코드 적용이 되는 이유는 Movie는 어느 특정한 할인 정책에도 묶이지 않기 때문에 가능한데, 이를 **컨텍스트 독립성(context independency)**이라고 부른다.

### 추상 클래스와 인터페이스 트레이드오프

DiscountPolicy 클래스를 추상화할 때 사용하는 두 가지 기법

- **인터페이스**
    - DiscountPolicy → 인터페이스
    - 인터페이스 → DefaultDiscountPolicy → 할인 정책
    - 인터페이스 → NoneDiscountPolicy - 기존의 추상 클래스가 갖고 있던 다른 금액 관한 코드를 없앨 수 있음 (혼동 최소화)
- **추상 클래스**
    - 인터페이스로 만들기 위한 노력이 덜 필요(None만을 위한 인터페이스는 과하다)

### 코드 재사용

객체지향 설계는 **상속보다는 합성(composition)**

- 이번 코드에서는 Movie가 DiscountPolicy의 코드를 재사용하는 방법이 합성
- 이번 코드도 상속만으로 구현할 수 있다.
- 그럼에도 불구하고 추상화를 통해 구현한 이유
    - 캡슐화를 위반
    - 설계를 유연하지 못하게 만든다.

## 합성

- 인터페이스에 정의된 메시지를 통해서 코드를 재사용하는 방법
- 컴파일 시점에 인터페이스를 통해 약하게 결합시킴 ⇒ 코드의 재사용을 위해서는 상속보다는 합성
- 꼭 상속이 안좋다는 것은 아님
    - **서브클래싱** 용도로는 상속을 사용해도 무방
    - **서브타이핑** 용도로는 상속은 변경에 취약
