# Chapter02 객체지향 프로그래밍

## 1. 영화 예매 시스템

### 요구사항 살펴보기

- 온라인 영화 예매 시스템을 구현하려고 한다.
- 영화는 영화에 대한 기본 정보를 표현한다.
    - 제목, 상영 시간, 가격 정보 등
- 상영은 실제로 관객이 영화를 관람하는 사건을 표현한다.
    - 상영 일자, 시간, 순번 등
- 사람들은 영화를 예매한다고 하지만, 사실 특정 **상영**되는 영화를 예매한다고 해야 옳다.
- **할인 조건(discount condition)**
    - 가격의 할인 여부
    - 순서 조건: 상영 순번을 이용해 할인 여부를 결정
    - 기간 조건: 영화 상영 시작 시간을 이용해 할인 여부를 결정
- **할인 정책(discount policy)**
    - 할인 요금을 결정
    - 금액 할인 정책 : 예매 요금에서 일정 금액을 할인
    - 비율 할인 정책 : 정가에서 일정 비율의 요금을 할인
- 영화 별로 하나의 할인 정책을 할당하거나 아예 안 할 수 있고, 할인 조건은 다수의 할인 조건을 섞을 수 있다.
- 할인 정책이 적용되어 있지만 할인 조건을 만족하지 못하거나, 할인 정책이 적용되어 있지 않은 경우에는 요금을 할인하지 않는다.

<br>

## 2. 객체지향 프로그래밍을 향해

### 협력, 객체, 클래스

- 객체 지향은 객체를 지향하는 것이다.
    - 클래스를 결정한 후에 클래스에 어떤 속성과 메서드가 필요한지 고민하지 말아야 한다.
    - 어떤 객체들이 어떤 상태와 행동을 가지는지 결정해야 한다.
    - 객체를 독립적인 존재가 아니라, 협력하는 공동체의 일원으로 봐야 한다.

### 도메인의 구조를 따르는 프로그램 구조

- **도메인(domain)** 은 문제를 해결하기 위해 사용자가 프로그램을 사용하는 분야를 말한다.

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/2ce4fa5f-7862-4ec9-8727-d106b8a9df1e)

- 일반적으로 클래스의 이름은 대응되는 도메인 개념의 이름과 동일하거나 적어도 유사하게 지어야 한다.
- 클래스 사이의 관계도 최대한 도메인 개념 사이에 맺어진 관계와 유사하게 만들어서 프로그램의 구조를 이해하고 예상하기 쉽게 만들어야 한다.

### 클래스 구현하기

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
}
```

- 클래스는 내부와 외부로 구분되며 훌륭한 클래스를 설계하기 위해서는 어떤 부분을 외부에 공개하고, 어떤 부분을 감출지 결정해야 한다.
    - 객체의 속성은 private로 막고, 내부 상태를 변경하기 위해 필요한 메서드를 public으로 열어 둔다.
- 클래스의 내부와 외부를 구분해야 객체의 자율성을 보장함으로써 프로그래머에게 구현의 자유를 제공한다.

**자율적인 객체**

- 객체는 상태와 행동을 가진 자율적인 객체여야 한다.
- 데이터와 기능을 객체 내부로 함께 묶는 것을 **캡슐화**라고 한다.
- 접근 제어를 통해 외부의 간섭을 줄여야 객체는 스스로 자기의 행동을 결정할 수 있게 된다.
- 인터페이스와 구현의 분리 원칙은 객체 지향 프로그래밍을 위해 따라야 하는 주요 원칙이다.
    - **퍼블릭 인터페이스(public interface)** : 외부에서 접근 가능한 부분
    - **구현(implementation)** : 내부에서만 접근 가능한 부분

**프로그래머의 자유**

- 프로그래머의 역할은 **클래스 작성자(class creator)** 와 **클라이언트 프로그래머(client programmer)** 로 구분된다.
    - 클래스 작성자는 데이터 타입을 새로 추가
    - 클라이언트 프로그래머는 클래스 작성자가 추가한 데이터 타입을 사용
- **구현 은닉 (implementation hiding)**
    - 클래스 작성자는 필요한 부분만 클라이언트 프로그래머에게 제공하여 내부 구현을 감출 수 있다.
    - 클라이언트 프로그래머는 인터페이스만 알면 되므로 지식의 양을 줄일 수 있다.

### 협력하는 객체들의 공동체

- 돈을 표현할 때 단순히 Long 타입의 변수를 선언하기 보다는, Money와 같이 객체로 포장해 주는 것이 좋다. 객체를 사용하면 의미 전달이 보다 잘 되고, 중복된 연산 처리를 한 곳에서 수행할 수 있기 때문이다.
- 시스템의 어떤 기능을 구현하기 위해 객체들 사이에 이뤄지는 상호 작용을 협력이라고 부른다.

### 협력에 관한 짧은 이야기

- 객체는 다른 객체의 인터페이스에 공개된 행동을 수행하도록 **요청(request)**할 수 있다.
- 요청을 받은 객체는 자율적인 방법에 따라 요청을 처리한 후 **응답(response)**한다.
- 객체가 다른 객체와 상호 작용할 수 있는 유일한 방법은 메시지를 전송하거나 수신하는 것이다.
- 수신된 메시지를 처리하기 위한 자신만의 방법을 **메서드(method)**라고 부른다.
- 메시지와 메서드를 구분하는 것은 중요하며, 여기서부터 **다형성(polymorphism)**의 개념이 출발한다.

<br>

## 3. 할인 요금 구하기

### 할인 요금 계산을 위한 협력 시작하기

- Movie는 제목(title)과 상영시간(runningTime), 기본 요금(fee), 할인 정책(discountPolicy)을 속성으로 가진다.
    
    ```java
    public class Movie {
    		private String title;
    		private Duration runningTime;
    		private Money fee;
    		private DiscountPolicy discountPolicy;
    
    		public Movie(String title, Duration runningTime, Money fee, DicountPolicy discountPolicy) {
    				this.title = title;
    				this.runningTime = runningTime;
    				this.fee = fee;
    				this.discountPolicy = discountPolicy;
    		}
    
    		public Money getFee() {
    				return fee;
    		}
    
    		public Money calculateMovieFee(Screening screening) {
    				return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    		}
    }
    ```
    
- 이 코드에는 상속(inheritance)과 다형성의 개념이 숨겨져 있다.
    - 그 기반에는 **추상화(abstraction)** 라는 원리가 숨겨져 있다.

### 할인 정책과 할인 조건

할인 정책은 금액 할인 정책(`AmountDiscountPolicy` 클래스)과 비율 할인 정책(`PercentDiscountPolicy` 클래스)으로 구분된다.
두 클래스는 대부분의 코드가 유사하고 할인 요금을 계산하는 방식만 조금 다르다.
따라서 두 클래스 사이의 중복 코드를 제거하기 위해 공통 코드를 보관할 장소가 필요하다.

```java
// 실제 애플리케이션에서는 DiscountPolicy의 인스턴스를 생성할 필요가 없기 때문에
// 추상 클래스(abstract class)
public abstract class DiscountPolicy {
		private List<DiscountCondition> conditions = new ArrayList<>();

		public DiscountPolicy(DiscountCondition ... conditions) {
				this.conditions = Arrays.asList(conditions);
		}

		public Money calculateDiscountAmount(Screening screening) {
				for(DiscountCondition each : conditions) {
					if(each.isSatisfiedBy(screening)) {
						return getDiscountAmount(screening);
					}
				}

				return Money.ZERO;
		}

		abstract protected Money getDiscountAmount(Screening Screening);
}
```

- `DiscountPolicy` 는 할인 여부와 요금 계산에 필요한 전체적인 흐름은 정의하지만 실제로 요금을 계산하는 부분은 추상 메서드인 `getDiscountAmount` 메서드에게 위임한다.
- 실제로는 `DiscountPolicy` 를 상속받은 자식 클래스에서 오버라이딩한 메서드가 실행될 것이다.
- 이처럼 부모 클래스에 기본적인 알고리즘의 흐름을 구현하고 중간에 필요한 처리를 자식 클래스에게 위임하는 디자인 패턴을 **TEMPLATE METHOD** 패턴이라고 부른다.

`DiscountCondition` 은 자바의 인터페이스를 이용해 선언돼 있다.
`isSatisfiedBy` 오퍼레이션은 인자로 전달된 `screening` 이 할인이 가능한 경우 `true` 를 반환하고 할인이 불가능한 경우에는 `false` 를 반환한다.

```java
public interface DiscountCondition {
		boolean isSatisfiedBy(Screening screening);
}
```

```java
// 할인 여부를 판단하기 위해 사용할 순번(sequence)을 인스턴스 변수로 포함
public class SequenceCondition implements DiscountCondition {
    private int sequence;
    
    public SequenceCondition(int sequence) {
        this.sequence = sequence;
    }
    
    public boolean isSatisfiedBy(Screening screening) {
        return screening.isSequence(sequence);
    }
}

// 상영 시작 시간이 특정한 기간 안에 포함되는지 여부를 판단해 할인 여부를 결정
public class PeriodCondition implements DiscountCondition {
    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;

    public PeriodCondition(DayOfWeek dayOfWeek, LocalTime startTime, LocalTime endTime) {
        this.dayOfWeek = dayOfWeek;
        this.startTime = startTime;
        this.endTime = endTime;
    }

    public boolean isSatisfiedBy(Screening screening) {
        return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
                startTime.compareTo(screening.getStartTime().toLocalTime()) <= 0 &&
                endTime.compareTo(screening.getStartTime().toLocalTime()) >= 0;
    }
}
```

```java
// 할인 조건을 만족할 경우 일정한 금액을 할인해주는 금액 할인 정책 구현
public class AmountDiscountPolicy extends DiscountPolicy {
    private Money discountAmount;
    
    public AmountDiscountPolicy(Money discountAmount, DiscountCondition ... conditions) {
        super(conditions);
        this.discountAmount = discountAmount;
    }

		@Override
    protected Money getDiscountAmount(Screening screening) {
        return discountAmount;
    }
}

// 고정 금액이 아닌 일정 비율을 차감하는 할인 정책 구현
public class PercentDiscountPolicy extends DiscountPolicy {
    private double percent;
    
    public PercentDiscountPolicy(double percent, DiscountCondition ... conditions) {
        super(conditions);
        this.percent = percent;
    } 
    
    @Override
    protected Money getDiscountAmount(Screening screening) {
        return screening.getMovieFee().times(percent);
    }
}
```

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/8b82e592-1ba5-46a8-898a-18b56722edbb)

**오버라이딩과 오버로딩**

- **오버라이딩(overriding)**
    - 부모 클래스에 정의된 같은 이름, 같은 파라미터 목록을 가진 메서드를 자식 클래스에서 재정의하는 경우를 가리킨다.
    - 자식 클래스의 메서드는 오버라이딩한 부모 클래스의 메서드를 가리기 때문에 외부에서는 부모 클래스의 메서드가 보이지 않는다.
- **오버로딩(overloading)**
    - 메서드의 이름은 같지만 제공되는 파라미터의 목록이 다르다.
    - 오버로딩한 메서드는 원래의 메서드를 가리지 않기 때문에 이 메서드들은 사이 좋게 공존한다.
    
    ```java
    public class Money {
    		public Money plus(Money amount) {
    				return new Money(this.amount.add(amount.amount));
    		}
    
    		public Money plus(long amount) {
    				return new Money(this.amount.add(BigDecimal.valueOf(amount)));
    		}
    }
    ```
    

### 할인 정책 구성하기

하나의 영화에 대해 단 하나의 할인 정책만 설정할 수 있지만 할인 조건의 경우에는 여러 개를 적용할 수 있다.

```java
public class Movie {
		public Movie(String title, Duration runningTime, Money fee, DiscountPolicy discountPolicy) {
			...
			this.discountPolicy = discountPolicy;
		}
}

public abstract class DiscountPolicy {
		public DiscountPolicy(DiscountCondition ... conditions) {
			this.conditions = Arrays.asList(conditions);
		}
}
```

<br>

## 4. 상속과 다형성

### 컴파일 시간 의존성과 실행 시간 의존성

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/5120060e-4aaa-4042-8d33-382c2e4c2e30)

- 어떤 클래스가 다른 클래스에 접근할 수 있는 경로를 가지거나 해당 클래스의 객체의 메서드를 호출할 경우 두 클래스 사이에 의존성이 존재한다고 말한다.
- `Movie` 클래스는 `DiscountPolicy` 가 아닌 `AmountDiscountPolicy` 와 `PercentDiscountPolicy` 인스턴스가 필요하지만, 추상 클래스인 `DiscountPolicy` 에만 의존하고 있다.

- 만약 영화 요금을 계산하기 위해 금액 할인 정책을 적용하고 싶다면 Movie의 인스턴스를 생성할 때 인자로 AmountDiscountPolicy의 인스턴스를 전달하면 된다.
- 실행 시 Movie의 인스턴스는 AmountDiscountPolicy 클래스의 인스턴스에 의존하게 될 것이다.

```java
Movie avatar = new Movie("아바타",
		Duration.ofMinutes(120),
		Money.wons(10000),
		new AmountDiscountPolicy(Money.wons(800), ...));
```

- 영화 요금을 계산하기 위해 비율 할인 정책을 적용하고 싶다면 AmountDiscountPolicy 대신 PercentDiscountPolicy의 인스턴스를 전달하면 된다.
- 이 경우 Movie의 인스턴스는 PercentDiscountPolicy의 인스턴스에 의존하게 된다.

```java
Movie avatar = new Movie("아바타",
		Duration.ofMinutes(120),
		Money.wons(10000),
		new PercentDiscountPolicy(0.1, ...));
```

- 코드의 의존성과 실행 시점의 의존성이 서로 다를 수 있다.
- 클래스 사이의 의존성과 객체 사이의 의존성은 동일하지 않을 수 있다.
- 유연하고, 쉽게 재사용할 수 있으며, 확장 가능한 객체지향 설계가 가지는 특징은 코드의 의존성과 실행 시점의 의존성이 다르다는 것이다.
- **코드의 의존성과 실행 시점의 의존성이 다르면 다를수록 코드를 이해하기 어려워진다.**
- 반면 코드의 의존성과 실행 시점의 의존성이 다르면 다를수록 코드는 더 유연해지고 확장 가능해진다.

> **설계가 유연해질수록 코드를 이해하고 디버깅하기는 점점 더 어려워진다.
반면 유연성을 억제하면 코드를 이해하고 디버깅하기는 쉬워지지만 재사용성과 확장 가능성은 낮아진다.
무조건 유연한 설계도, 무조건 읽기 쉬운 코드도 정답이 아니다.**
> 

### 차이에 의한 프로그래밍

클래스를 하나 추가하고 싶은데 그 클래스가 기존의 어떤 클래스와 매우 흡사하다면, 그 클래스의 코드를 가져와 약간만 추가하거나 수정해서 새로운 클래스를 만들 수 있다면 좋을 것이다. 
더 좋은 방법은 그 클래스의 코드를 전혀 수정하지 않고도 재사용하는 것일 것이다.
이를 가능하게 해주는 방법이 바로 **상속**이다.

- ************************상속 :************************ 객체지향에서 코드를 재사용하기 위해 가장 널리 사용되는 방법
    - 상속을 이용하면 클래스 사이에 관계를 설정하는 것만으로 기존 클래스가 가지고 있는 모든 속성과 행동을 새로운 클래스에 포함시킬 수 있다.
    - 상속은 기존 클래스를 기반으로 새로운 클래스를 쉽고 빠르게 추가할 수 있는 간편한 방법을 제공한다.
    - 상속을 이용하면 부모 클래스의 구현은 공유하면서도 행동이 다른 자식 클래스를 쉽게 추가할 수 있다.
- 부모 클래스와 다른 부분만을 추가해서 새로운 클래스를 쉽고 빠르게 만드는 방법을 **차이에 의한 프로그래밍(programming by difference)**이라고 부른다.

### 상속과 인터페이스

상속이 가치 있는 이유는 부모 클래스가 제공하는 모든 인터페이스를 자식 클래스가 물려받을 수 있기 때문이다.

- 인터페이스는 객체가 이해할 수 있는 메시지의 목록을 정의한다.
- 상속을 통해 자식 클래스는 자신의 인터페이스에 부모 클래스의 인터페이스를 포함하게 된다.
- 결과적으로 자식 클래스는 부모 클래스가 수신할 수 있는 모든 메시지를 수신할 수 있기 때문에 외부 객체는 자식 클래스를 부모 클래스와 동일한 타입으로 간주할 수 있다.

```java
public class Movie {
		public Money calculateMovieFee(Screening screening) {
        return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    }
}
```

- 자식 클래스는 상속을 통해 부모 클래스의 인터페이스를 물려받기 때문에 부모 클래스 대신 사용될 수 있다.
- 자식 클래스가 부모 클래스를 대신하는 것을 **업캐스팅(upcasting)**이라고 부른다.

### 다형성

메시지와 메서드는 다른 개념이다.

Movie는 동일한 메시지를 전송하지만 실제로 어떤 메서드가 실행될 것인지는 메시지를 수신하는 객체의 클래스가 무엇이냐에 따라 달라진다.
이를 **다형성**이라고 부른다.

- 다형성은 객체지향 프로그램의 컴파일 시간 의존성과 실행 시간 의존성이 다를 수 있다는 사실을 기반으로 한다.
- 다형성은 컴파일 시간 의존성과 실행 시간 의존성을 다르게 만들 수 있는 객체지향의 특성을 이용해 서로 다른 메서드를 실행할 수 있게 한다.
- 다형성이란 동일한 메시지를 수신했을 때 객체의 타입에 따라 다르게 응답할 수 있는 능력을 의미한다.
따라서 다형적인 협력에 참여하는 객체들은 모두 같은 메시지를 이해할 수 있어야 한다.

- 다형성을 구현하는 방법은 매우 다양하지만 **메시지에 응답하기 위해 실행될 메서드**를 컴파일 시점이 아닌 **실행 시점에 결정**한다는 공통점이 있다.
- 다시 말해 메시지와 메서드를 실행 시점에 바인딩한다는 것이다.
- 이를 **지연 바인딩(lazy binding)** 또는 **동적 바인딩(dynamic binding)** 이라고 부른다.
- 전통적인 함수 호출처럼 컴파일 시점에 실행될 함수나 프로시저를 결정하는 것을 **초기 바인딩(early binding)** 또는 **정적 바인딩(static binding)** 이라고 부른다.

### 인터페이스와 다형성

- 구현은 공유할 필요가 없고 순수하게 인터페이스만 공유하고 싶을 때가 있는데, 이를 위해 C#과 자바에서는 **인터페이스**라는 프로그래밍 요소를 제공한다.
- 자바의 인터페이스는 말 그대로 구현에 대한 고려 없이 다형적인 협력에 참여하는 클래스들이 공유 가능한 외부 인터페이스를 정의한 것이다.
- C++의 경우 **추상 기반 클래스(Abstract Base Class, ABC)** 를 통해 자바의 인터페이스 개념을 구현할 수 있다.

<br>

## 5. 추상화와 유연성

### 추상화의 힘

지금까지 살펴본 것처럼 할인 정책은 구체적인 금액 할인 정책과 비율 할인 정책을 포괄하는 추상적인 개념이다.
할인 조건 역시 더 구체적인 순번 조건과 기간 조건을 포괄하는 추상적인 개념이다.

**추상화를 사용할 경우 두 가지 장점**

1. 추상화의 계층만 따로 떼어 놓고 살펴보면 요구사항의 정책을 높은 수준에서 서술할 수 있다.
2. 추상화를 이용하면 설계가 좀 더 유연해진다.

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/7f2db110-7ed6-4fad-9868-440f75bfb459)

- 추상화를 사용하면 세부적인 내용을 무시한 채 상위 정책을 쉽고 간단하게 표현할 수 있다.
- 추상화의 이런 특징은 세부사항에 억눌리지 않고 상위 개념만으로도 도메인의 중요한 개념을 설명할 수 있게 한다.
- 추상화를 이용해 상위 정책을 기술한다는 것은 기본적인 애플리케이션의 협력 흐름을 기술한다는 것을 의미한다.
- 재사용 가능한 설계의 기본을 이루는 **디자인 패턴(design pattern)** 이나 **프레임워크(framework)** 모두 추상화를 이용해 상위 정책을 정의하는 객체지향의 메커니즘을 활용하고 있기 때문이다.
- 추상화를 이용해 상위 정책을 표현하면 기존 구조를 수정하지 않고도 새로운 기능을 쉽게 추가하고 확장할 수 있다. 다시 말해 설계를 유연하게 만들 수 있다.

### 유연한 설계

```java
public class Movie {
		public Money calculateMovieFee(Screening screening) {
			if (discountPolicy == null) {
				return fee;
			}

			return fee.minus(discountPolicy.calculateDiscountAmount(screening));
		}
}
```

- 위의 방식의 문제점은 할인 정책이 없는 경우를 예외 케이스로 취급하기 때문에 지금까지 일관성 있던 협력 방식이 무너지게 된다는 것이다.
- 책임의 위치를 결정하기 위해 조건문을 사용하는 것은 협력의 설계 측면에서 대부분의 경우 좋지 않은 선택이다.
- 항상 예외 케이스를 최소화하고 일관성을 유지할 수 있는 방법을 선택하라.

```java
public class NoneDiscountPolicy extends DiscountPolicy {
		@Override
		protected Money getDiscountAmount(Screening screening) {
			return Money.ZERO;
		}
}
```

다음 클래스를 통해 할인되지 않는 영화를 생성할 수 있게 된다.

```java
Movie starWars = new Movie("스타워즈",
		Duration.ofMinutes(210),
		Money.wons(10000),
		new NoneDiscountPolicy());
```

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/51f77b98-bf07-4f0e-bf4a-b59c5f8f6c37)

### 추상 클래스와 인터페이스 트레이드오프

```java
public interface DiscountPolicy {
	Money calculateDiscountAmount(Screening screening);
}

public abstract class DefaultDiscountPolicy implements DiscountPolicy {
	...
}

public class NoneDiscountPolicy implements DiscountPolicy {
		@Override
		public Money calculateDiscountAmount(Screening screening) {
			return Money.ZERO;
		}
}
```

- 구현과 관련된 모든 것들은 트레이드오프의 대상이 될 수 있다.

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/c53975c1-50ed-4a16-a0a0-6733d769e95a)

### 코드 재사용

상속은 코드를 재사용하기 위해 널리 사용되는 방법이다. 그러나 널리 사용되는 방법이라고 해서 가장 좋은 방법인 것은 아니다.

코드 재사용을 위해서는 상속보다는 **합성(composition)** 이 더 좋은 방법이라는 이야기를 많이 들었을 수 있다.

- **합성** : 다른 객체의 인스턴스를 자신의 인스턴스 변수로 포함해서 재사용하는 방법

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/64cb3433-65fe-4726-97ed-ab860df08e48)

### 상속

- 객체지향에서 코드를 재사용하기 위해 널리 사용되는 기법
- 하지만 두 가지 관점에서 설계에 안 좋은 영향을 미친다.
    - 상속이 캡슐화를 위반한다.
    - 설계를 유연하지 못하게 만든다.

**캡슐화를 위반한다**

- 부모 클래스의 구현이 자식 클래스에게 노출되기 때문에 캡슐화가 약화된다.
- 캡슐화의 약화는 자식 클래스가 부모 클래스에 강하게 결합되도록 만들기 때문에 부모 클래스를 변경할 때 자식 클래스도 함께 변경될 확률을 높인다.
- 결과적으로 상속을 과도하게 사용한 코드는 변경하기도 어려워진다.

**설계가 유연하지 않다**

- 상속은 부모 클래스와 자식 클래스 사이의 관계를 컴파일 시점에 결정하기 때문에, 실행 시점에 객체의 종류를 변경하는 것이 불가능하다.
- 상속보다 오히려 인스턴스 변수로 관계를 연결한 원래의 설계가 더 유연하다.

### 합성

- 인터페이스에 정의된 메시지를 통해서만 코드를 재사용하는 방법
- 상속이 부모 클래스의 코드와 자식 클래스의 코드를 컴파일 시점에 하나의 단위로 강하게 결합하는 데 비해 합성을 사용하면 인터페이스를 통해 약하게 결합된다.
- 합성은 상속이 가지는 두 가지 문제점을 모두 해결한다.
    - 인터페이스에 정의된 메시지를 통해서만 재사용이 가능하기 때문에 구현을 효과적으로 캡슐화할 수 있다.
    - 의존하는 인스턴스를 교체하는 것이 비교적 쉽기 때문에 설계를 유연하게 만든다.
- 코드 재사용을 위해서는 상속보다는 합성을 선호하는 것이 더 좋은 방법이다.

```java
public class Movie {
		private DiscountPolicy discountPolicy;

		public void changeDiscountPolicy(DiscountPolicy discountPolicy) {
			this.discountPolicy = discountPolicy;
		}
}

Movie avatar = new Movie("아바타",
		Duration.ofMinutes(120),
		Money.wons(10000),
		new AmountDiscountPolicy(Money.wons(800), ...));

avatar.changeDiscountPolicy(new PercentDiscountPolicy(0.1, ...));
```

> **코드를 재사용하는 경우에는 상속보다 합성을 선호하는 것이 옳지만,
다형성을 위해 인터페이스를 재사용하는 경우에는 상속과 합성을 함께 조합해서 사용할 수 밖에 없다.**
>
