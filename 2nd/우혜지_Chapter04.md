# Chapter04 설계 품질과 트레이드오프

## 1. 데이터 중심의 영화 예매 시스템

- 객체지향 설계에서 시스템을 객체로 분할할 수 있는 두 가지 방법
    - 상태를 분할의 중심축으로 삼는 방법
    - 책임을 분할의 중심축으로 삼는 방법
- 일반적으로 객체의 상태는 객체가 저장해야 하는 데이터의 집합을 의미하기 때문에 여기서는 ‘상태’와 ‘데이터’를 동일한 의미로 사용
- 데이터 중심의 관점에서 객체는 자신이 포함하고 있는 데이터를 조작하는 데 필요한 오퍼레이션을 정의한다.
- 책임 중심의 관점에서 객체는 다른 객체가 요청할 수 있는 오퍼레이션을 위해 필요한 상태를 보관한다.
- 데이터 중심의 관점은 객체의 상태에 초점을 맞추고 책임 중심의 관점은 객체의 행동에 초점을 맞춘다.
- 전자(데이터 중심)는 객체를 독립된 데이터 덩어리로 바라보고 
후자(책임 중심)는 객체를 협력하는 공동체의 일원으로 바라본다.

### 데이터를 준비하자

- 데이터 중심의 설계란 객체 내부에 저장되는 데이터를 기반으로 시스템을 분할하는 방법이다.
- 책임 중심의 설계가 ‘책임이 무엇인가’를 묻는 것으로 시작한다면 데이터 중심의 설계는 객체가 내부에 저장해야 하는 ‘데이터가 무엇인가’를 묻는 것으로 시작한다.

```java
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions;
    
    private MovieType movieType;
    private Money discountAmount;
    private double discountPercent;
}
```

```java
public enum MovieType {
    AMOUNT_DISCOUNT, // 금액 할인 정책
    PERCENT_DISCOUNT, // 비율 할인 정책
    NONE_DISCOUNT // 미적용
}
```

- 위의 방식은 말 그대로 데이터 중심의 접근 방법이다.

- 데이터 중심의 설계에서는 객체가 포함해야 하는 데이터에 집중한다.
- 객체지향의 가장 중요한 원칙은 캡슐화이므로 내부 데이터가 객체의 엷은 막을 빠져나가 외부의 다른 객체들을 오염시키는 것을 막아야 한다.
- 이를 달성할 수 있는 가장 간단한 방법은 내부의 데이터를 반환하는 접근자(accessor)와 데이터를 변경하는 수정자(mutator)를 추가하는 것이다.

```java
public class Movie {
    public MovieType getMovieType() {
        return movieType;
    }
    
    public void setMovieType(MovieType movieType) {
        this.movieType = movieType;
    }
    
    public Money getFee() {
        return fee;
    }
    
    public void setFee(Money fee) {
        this.fee = fee;
    }
    
    public List<DiscountCondition> getDiscountConditions() {
        return discountConditions;
    }
    
    public void setDiscountAmount(List<DiscountCondition> discountConditions) {
        this.discountConditions = discountConditions;
    }
    
    public Money getDiscountAmount() {
        return discountAmount;
    }
    
    public void setDiscountAmount(Money discountAmount) {
        this.discountAmount = discountAmount;
    }
    
    public double getDiscountPercent() {
        return discountPercent;
    }
    
    public void setDiscountPercent(double discountPercent) {
        this.discountPercent = discountPercent;
    }
}
```

- Movie를 구현하는 데 필요한 데이터를 결정하고 메서드를 이용해 내부 데이터를 캡슐화하는 데도 성공했다.

```java
public enum DiscountConditionType {
    SEQUENCE, // 순번 조건
    PERIOD // 기간 조건
}
```

```java
public class DiscountCondition {
    private DiscountConditionType type;

    private int sequence;

    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;
}
```

- 캡슐화의 원칙에 따라 이 속성들을 클래스 외부로 노출해서는 안 된다.

```java
public class DiscountCondition {
    public DiscountConditionType getType() {
        return type;
    }
    
    public void setType(DiscountConditionType type) {
        this.type = type;
    }
    
    public DayOfWeek getDayOfWeek() {
        return dayOfWeek;
    }
    
    public void setDayOfWeek(DayOfWeek dayOfWeek) {
        this.dayOfWeek = dayOfWeek;
    }
    
    public LocalTime getStartTime() {
        return startTime;
    }
    
    public void setStartTime(LocalTime startTime) {
        this.startTime = startTime;
    }
    
    public LocalTime getEndTime() {
        return endTime;
    }
    
    public void setEndTime(LocalTime endTime) {
        this.endTime = endTime;
    }
    
    public int getSequence() {
        return sequence;
    }
    
    public void setSequence(int sequence) {
        this.sequence = sequence;
    }
}
```

- 다음과 같이 Screening, Reservation, Customer 클래스를 추가하면 다음과 같다.

```java
public class Screening {
    private Movie movie;
    private int sequence;
    private LocalDateTime whenScreened;
    
    public Movie getMovie() {
        return movie;
    }
    
    public void setMovie(Movie movie) {
        this.movie = movie;
    }
    
    public LocalDateTime getWhenScreened() {
        return whenScreened;
    }
    
    public void setWhenScreened(LocalDateTime whenScreened) {
        this.whenScreened = whenScreened;
    }
    
    public int getSequence() {
        return sequence;
    }
    
    public void setSequence(int sequence) {
        this.sequence = sequence;
    }
}

...
```

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/122d3049-ba77-413a-bca4-b9d01fadfc37)

### 영화를 예매하자

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
        Movie movie = screening.getMovie();
        
        boolean discountable = false;
        for (DiscountCondition condition : movie.getDiscountConditions()) {
            if (condition.getType() == DiscountConditionType.PERIOD) {
                discountable = screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek()) &&
                        condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                        condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
            } else {
                discountable = condition.getSequence() == screening.getSequence();
            }
            
            if (discountable) {
                break;
            }
        }
        
        Money fee;
        if (discountable) {
            Money discountAmount = Money.ZERO;
            switch (movie.getMovieType()) {
                case AMOUNT_DISCOUNT:
                    discountAmount = movie.getDiscountAmount();
                    break;
                case PERCENT_DISCOUNT:
                    discountAmount = movie.getFee().times(movie.getDiscountPercent());
                    break;
                case NONE_DISCOUNT:
                    discountAmount = Money.ZERO;
                    break;
            }
            
            fee = movie.getFee().minus(discountAmount);
        } else {
            fee = movie.getFee();
        }
        
        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```

<br>

## 2. 설계 트레이드오프

- 데이터 중심 설계와 책임 중심 설계의 장단점을 비교하기 위해 **캡슐화, 응집도, 결합도**를 사용

### 캡슐화

- 상태와 행동을 하나의 객체 안에 모으는 이유는 객체의 내부 구현을 외부로부터 감추기 위해서다.
- 변경될 가능성이 높은 부분을 **구현**이라고 부르고 상대적으로 안정적인 부분을 **인터페이스**라고 부른다.
- 객체를 설계하기 위한 가장 기본적인 아이디어는 변경의 정도에 따라 구현과 인터페이스를 분리하고 외부에서는 인터페이스에만 의존하도록 관계를 조절하는 것이다.
- **캡슐화**는 외부에서 알 필요가 없는 부분을 감춤으로써 대상을 단순화하는 추상화의 한 종류이다.
- 설계가 필요한 이유는 **요구사항이 변경**되기 때문이고, 캡슐화가 중요한 이유는 **불안정한 부분과 안정적인 부분을 분리**해서 **변경의 영향을 통제할 수 있기** 때문이다.
- 정리하면 캡슐화란 변경 가능성이 높은 부분을 객체 내부로 숨기는 추상화 기법이다.

### 응집도와 결합도

- **응집도** : 모듈에 포함된 내부 요소들이 연관돼 있는 정도
    - 모듈 내의 요소들이 하나의 목적을 위해 긴밀하게 협력한다면 높은 응집도를 가진다.
    - 모듈 내의 요소들이 서로 다른 목적을 추구한다면 낮은 응집도를 가진다.
- **결합도** : 의존성의 정도를 나타내며 다른 모듈에 대해 얼마나 많은 지식을 갖고 있는지를 나타내는 척도
    - 어떤 모듈이 다른 모듈에 대해 너무 자세한 부분까지 알고 있다면 높은 결합도를 가진다.
    - 어떤 모듈이 다른 모듈에 대해 꼭 필요한 지식만 알고 있다면 낮은 결합도를 가진다.
- 일반적으로 좋은 설계란 **높은 응집도**와 **낮은 결합도**를 가진 모듈로 구성된 설계를 의미한다.
- 높은 응집도와 낮은 결합도를 가진 설계를 추구해야 하는 이유는 설계를 변경하기 쉽게 만들기 때문이다.

- 변경의 관점에서 응집도란 **변경이 발생할 때 모듈 내부에서 발생하는 변경의 정도**로 측정할 수 있다.
- 응집도가 높은 설계에서는 하나의 요구사항 변경을 반영하기 위해 오직 하나의 모듈만 수정하면 되지만,
응집도가 낮은 설계에서는 하나의 원인에 의해 변경해야 하는 부분이 다수의 모듈에 분산돼 있기 때문에 여러 모듈을 동시에 수정해야 한다.

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/b191c56a-5fe6-4b7c-8eee-0ff11623b72b)

- 변경의 관점에서 결합도는 **한 모듈이 변경되기 위해서 다른 모듈의 변경을 요구하는 정도**로 측정할 수 있다.
- 낮은 결합도를 가진 설계에서는 모듈 A를 변경했을 때 오직 하나의 모듈만 영향을 받지만, 
높은 결합도를 가진 설계에서는 모듈 A를 변경했을 때 4개의 모듈을 동시에 변경해야 한다.

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/4350247c-1cd9-47a3-b182-82080357478e)

- 내부 구현을 변경했을 때 이것이 다른 모듈에 영향을 미치는 경우에는 두 모듈 사이의 결합도가 높다고 표현한다.
- 반면 퍼블릭 인터페이스를 수정했을 때만 다른 모듈에 영향을 미치는 경우에는 결합도가 낮다고 표현한다.
- 따라서 클래스의 구현이 아닌 인터페이스에 의존하도록 코드를 작성해야 낮은 결합도를 얻을 수 있다.
    - “인터페이스에 대해 프로그래밍하라[GOF94]”
- 결합도가 높아도 상관 없는 경우도 있는데, 일반적으로 변경될 확률이 매우 적은 안정적인 모듈에 의존하는 것은 아무런 문제도 되지 않는다.
    - 표준 라이브러리에 포함된 모듈이나 성숙 단계에 접어든 프레임워크에 의존하는 경우가 여기에 속함
- 캡슐화의 정도가 응집도와 결합도에 영향을 미치는데, 캡슐화를 지키면 모듈 안의 응집도는 높아지고 모듈 사이의 결합도는 낮아진다.

<br>

## 3. 데이터 중심의 영화 예매 시스템의 문제점

**데이터 중심의 설계가 가진 대표적인 문제점**

- 캡슐화 위반
- 높은 결합도
- 낮은 응집도

### 캡슐화 위반

```java
public class Movie {
		private Money fee;

		public Money getFee() {
			return fee;
		}

		public void setFee(Money fee) {
			this.fee = fee;
		}
}
```

- getFee 메서드와 setFee 메서드는 Movie 내부에 Money 타입의 fee라는 이름의 인스턴스 변수가 존재한다는 사실을 퍼블릭 인터페이스에 노골적으로 드러낸다.
- 캡슐화의 원칙을 어기게 된 근본적인 원인은 객체가 수행할 책임이 아니라 내부에 저장할 데이터에 초점을 맞췄기 때문이다.
- 이처럼 접근자와 수정자에 과도하게 의존하는 설계 방식을 **추측에 의한 설계 전략(design-by-guessing strategy)**이라고 부른다. (앨런 홀럽)

### 높은 결합도

```java
public class ReservationAgency {
		public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
			...
			Money fee;
			if (discountable) {
				...
				fee = movie.getFee().minus(discountedAmount).times(audienceCount);
			} else {
				fee = movie.getFee();
			}
			...
		}
}
```

- fee의 타입 변경으로 인해 협력하는 클래스가 변경되기 때문에 getFee 메서드는 fee를 정상적으로 캡슐화하지 못한다.
- 결합도 측면에서 데이터 중심 설계가 가지는 또 다른 단점은 여러 데이터 객체들을 사용하는 제어 로직이 특정 객체 안에 집중되기 때문에 하나의 제어 객체가 다수의 데이터 객체에 강하게 결합된다는 것이다.

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/d4885d00-23c3-4696-a36a-7673863ecaca)

### 낮은 응집도

- 서로 다른 이유로 변경되는 코드가 하나의 모듈 안에 공존할 때 모듈의 응집도가 낮다고 말한다.
- ReservationAgency를 예로 들었을 때, 다음과 같은 수정사항이 발생하는 경우에 코드를 수정해야 할 것이다.
    - 할인 정책이 추가될 경우
    - 할인 정책별로 할인 요금을 계산하는 방법이 변경될 경우
    - 할인 조건이 추가되는 경우
    - 할인 조건별로 할인 여부를 판단하는 방법이 변경될 경우
    - 예매 요금을 계산하는 방법이 변경될 경우
- 낮은 응집도는 두 가지 측면에서 설계에 문제를 일으킨다.
    1. 변경의 원인이 다른 코드들이 하나의 모듈 안에 뭉쳐있어 변경과 아무 상관 없는 코드들이 영향을 받는다.
    2. 하나의 요구사항 변경을 위해 여러 모듈을 동시에 수정해야 한다.
- 어떤 요구사항 변경을 수용하기 위해 하나 이상의 클래스를 수정해야 하는 것은 설계의 응집도가 낮다는 증거다.

<br>

## 4. 자율적인 객체를 향해

### 캡슐화를 지켜라

- 데이터 중심의 설계가 낮은 응집도와 높은 결합도라는 문제로 몸살을 앓게 된 근본적인 원인은 바로 캡슐화의 원칙을 위반했기 때문이다.
- 객체는 자신이 어떤 데이터를 가지고 있는지를 내부에 캡슐화하고 외부에 공개해서는 안된다.
- 객체는 스스로의 상태를 책임져야 하며 외부에서는 인터페이스에 정의된 메서드를 통해서만 상태에 접근할 수 있어야 한다.

### 스스로 자신의 데이터를 책임지는 객체

- 우리가 상태와 행동을 객체라는 하나의 단위로 묶는 이유는 객체 스스로 자신의 상태를 처리할 수 있게 하기 위해서다.
- 객체는 단순한 데이터 제공자가 아니다. 객체 내부에 저장되는 데이터보다 객체가 협력에 참여하면서 수행할 책임을 정의하는 오퍼레이션이 더 중요하다.
- “이 객체가 어떤 데이터를 포함해야 하는가?”라는 질문은 다음과 같은 두 개의 개별적인 질문으로 분리된다.
    - 이 객체가 어떤 데이터를 포함해야 하는가?
    - 이 객체가 데이터에 대해 수행해야 하는 오퍼레이션은 무엇인가?

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/ae26a486-be45-4a3c-adbf-e02a53b7c0a9)

<br>

## 5. 하지만 여전히 부족하다

### 캡슐화 위반

- 내부 구현의 변경이 외부로 퍼져나가는 파급 효과(ripple effect)는 캡슐화가 부족하다는 명백한 증거다.
- 따라서 변경 후의 설계는 자기 자신을 스스로 처리한다는 점에서 이전의 설계보다 분명히 개선됐지만 여전히 내부의 구현을 캡슐화하는 데는 실패한 것이다.

**캡슐화의 진정한 의미**

사실 캡슐화는 변경될 수 있는 어떤 것이라도 감추는 것을 의미한다.

내부 속성을 외부로부터 감추는 것은 ‘데이터 캡슐화’라고 불리는 캡슐화의 한 종류일 뿐이다.

속성의 타입이건, 할인 정책의 종류건 상관 없이 내부 구현의 변경으로 인해 외부의 객체가 영향을 받는다면 캡슐화를 위반한 것이다. 

설계에서 변하는 것이 무엇인지 고려하고 변하는 개념을 캡슐화해야 한다.

<br>

## 6. 데이터 중심 설계의 문제점

- 두 번째 설계가 변경에 유연하지 못한 이유는 캡슐화를 위반했기 때문이다.
- 캡슐화를 위반한 설계를 구성하는 요소들이 높은 응집도와 낮은 결합도를 가질 확률은 극히 낮다.
- 데이터 중심의 설계가 변경에 취약한 이유는 두 가지이다.
    - 본질적으로 너무 이른 시기에 데이터에 관해 결정하도록 강요한다.
    - 협력이라는 문맥을 고려하지 않고 객체를 고립시킨 채 오퍼레이션을 결정한다.

### 데이터 중심 설계는 객체의 행동보다는 상태에 초점을 맞춘다

- 데이터 중심의 설계를 시작할 때 던졌던 첫 번째 질문은 “이 객체가 포함해야 하는 데이터가 무엇인가?”다.
- 데이터 주도 설계는 설계를 시작하는 처음부터 데이터에 관해 결정하도록 강요하기 때문에 너무 이른 시기에 내부 구현에 초점을 맞추게 한다.
- 데이터 중심의 관점에서 객체는 그저 단순한 데이터의 집약체일 뿐이기에, 이로 인해 접근자와 수정자를 과도하게 추가하게 되고 이 데이터 객체를 사용하는 절차를 분리된 별도의 객체 안에 구현하게 된다.
- 비록 데이터를 처리하는 작업과 데이터를 같은 객체 안에 두더라도 데이터에 초점이 맞춰져 있다면 만족스러운 캡슐화를 얻기 어렵다.

### 데이터 중심 설계는 객체를 고립시킨 채 오퍼레이션을 정의하도록 만든다

- 올바른 객체지향 설계의 무게 중심은 항상 객체의 내부가 아니라 외부에 맞춰져 있어야 한다.
- 객체가 내부에 어떤 상태를 가지고 그 상태를 어떻게 관리하는가는 부가적인 문제다.
- 데이터 중심 설계에서 초점은 객체의 외부가 아니라 내부로 향한다.
- 실행 문맥에 대한 깊이 있는 고민 없이 객체가 관리할 데이터의 세부 정보를 먼저 결정한다.
- 객체의 구현이 이미 결정된 상태에서 다른 객체와의 협력 방법을 고민하기 때문에 이미 구현된 객체의 인터페이스를 억지로 끼워맞출 수밖에 없다.
