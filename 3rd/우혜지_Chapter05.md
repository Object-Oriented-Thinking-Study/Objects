# Chapter05 책임 할당하기

## 1. 책임 주도 설계를 향해

데이터 중심의 설계에서 책임 중심의 설계로 전환하기 위해 따라야 하는 두 가지 원칙

- 데이터보다 행동을 먼저 결정하라
- 협력이라는 문맥 안에서 책임을 결정하라

> 두 원칙의 핵심은 설계를 진행하는 동안 데이터가 아니라 객체의 책임과 협력에 초점을 맞추라는 것이다.
> 

### 데이터보다 행동을 먼저 결정하라

- 객체에게 중요한 것은 데이터가 아니라 외부에 제공하는 행동이다
- 객체는 협력에 참여하기 위해 존재하며 협력 안에서 수행하는 책임이 객체의 존재가치를 증명한다.
- 데이터는 객체가 책임을 수행하는 데 필요한 재료를 제공할 뿐이다.
- 책임 중심의 설계에서는 객체의 행동, 즉 책임을 먼저 결정한 후에 객체의 상태를 결정한다.

### 협력이라는 문맥 안에서 책임을 결정하라

- 객체의 입장에서는 책임이 조금 어색해 보이더라도 협력에 적합하다면 그 책임은 좋은 것이다.
- 협력을 시작하는 주체는 메시지 전송자이기 때문에 협력에 적합한 책임이란 메시지 수신자가 아니라 **메시지 전송자**에게 적합한 책임을 의미한다.
    - 다시 말해서 메시지를 전송하는 클라이언트의 의도에 적합한 책임을 할당해야 한다.
- 협력에 적합한 책임을 수확하기 위해서는 객체를 결정한 후에 메시지를 선택하는 것이 아니라 메시지를 결정한 후에 객체를 선택해야 한다.
- 메시지를 먼저 결정하기 때문에 메시지 송신자는 메시지 수신자에 대한 어떠한 가정도 할 수 없고, 이것은 메시지 전송자의 관점에서 메시지 수신자가 깔끔하게 캡슐화되는 것이다.
    - 이처럼 처음부터 데이터에 집중하는 데이터 중심의 설계가 캡슐화에 취약한 반면 협력이라는 문맥 안에서 메시지에 집중하는 책임 중심의 설계는 캡슐화의 원리를 지키기가 훨씬 쉬워진다.

### 책임 주도 설계

**[책임 주도 설계의 흐름]**

- 시스템이 사용자에게 제공해야 하는 기능인 시스템 책임을 파악한다.
- 시스템 책임을 더 작은 책임으로 분할한다.
- 분할된 책임을 수행할 수 있는 적절한 객체 또는 역할을 찾아 책임을 할당한다.
- 객체가 책임을 수행하는 도중 다른 객체의 도움이 필요한 경우 이를 책임질 적절한 객체 또는 역할을 찾는다.
- 해당 객체 또는 역할에게 책임을 할당함으로써 두 객체가 협력하게 된다.

<br>

## 2. 책임 할당을 위한 GRASP 패턴

- GRASP (General Responsibility Assignment Software Pattern)
    - 일반적인 책임 할당을 위한 소프트웨어 패턴
    - 객체에게 책임을 할당할 때 지침으로 삼을 수 있는 원칙들의 집합을 패턴 형식으로 정리

### 도메인 개념에서 출발하기

- 도메인 안에는 많은 개념들이 존재하며 이 도메인 개념들을 책임 할당의 대상으로 사용하면 코드에 도메인의 모습을 투영하기가 좀 더 수월해진다.

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/d20edc91-68da-4aea-886c-5070ee092079/e84df15f-36ad-4f52-b4a2-89011f78f389/Untitled.png)

### 정보 전문가에게 책임을 할당하라

- 책임 주도 설계 방식의 첫 단계는 애플리케이션이 제공해야 하는 기능을 애플리케이션의 책임으로 생각하는 것이다.
- 책임을 애플리케이션에 대해 전송된 메시지로 간주하고 이 메시지를 책임질 첫 번째 객체를 선택하는 것으로 설계를 시작한다.
- 메시지는 메시지를 수신할 객체가 아니라 메시지를 전송할 객체의 의도를 반영해서 결정해야 한다.

> 메시지를 전송할 객체는 무엇을 원하는가?
메시지를 수신할 적합한 객체는 누구인가?
> 
- 질문에 답하기 위해서는 객체가 상태와 행동을 통합한 캡슐화의 단위라는 사실에 집중해야 한다.
- 객체에게 책임을 할당하는 첫 번째 원칙은 책임을 수행할 정보를 알고 있는 객체에게 책임을 할당하는 것이다.
    - GRASP에서는 이를 **INFORMATION EXPERT(정보 전문가)** 패턴이라고 부른다.
- INFORMATION EXPERT 패턴은 객체가 자신이 소유하고 있는 정보와 관련된 작업을 수행한다는 일반적인 직관을 표현한 것이다.
    - 여기에서 이야기하는 정보는 데이터와 다르다.
- 객체는 해당 정보를 제공할 수 있는 다른 객체를 알고 있거나 필요한 정보를 계산해서 제공할 수도 있다.

[]()

### 높은 응집도와 낮은 결합도

- 설계는 트레이드오프 활동이라는 것을 감안하여, 동일한 기능을 구현할 수 있는 무수히 많은 설계가 존재하고 그 중 한 가지를 선택해야 하는 경우가 발생한다.

[]()

- 높은 응집도와 낮은 결합도는 객체에 책임을 할당할 때 항상 고려해야 하는 기본 원리다.
- 협력 패턴 중에서 높은 응집도와 낮은 결합도를 얻을 수 있는 설계가 있다면 그 설계를 선택해야 한다는 것이다.
- GRASP에서는 이를 LOW COUPLING(낮은 결합도) 패턴과 HIGH COHESION(높은 응집도) 패턴이라고 부른다.

- **LOW COUPLING 패턴**
    - 어떻게 하면 의존성을 낮추고 변화의 영향을 줄이며 재사용성을 증가시킬 수 있을지, 전체적인 결합도가 낮게 유지되도록 책임을 할당해라.
    - 낮은 결합도는 설계 결정을 평가할 때 적용할 수 있는 평가원리다.

- **HIGH COHESION 패턴**
    - 어떻게 복잡성을 관리할 수 있는 수준으로 유지할 수 있을지, 즉 높은 응집도를 유지할 수 있도록 책임을 할당해라
    - 낮은 결합도와 마찬가지로 높은 응집도 역시 설계 결정을 평가할 때 적용할 수 있는 평가원리다.

### 창조자에게 객체 생성 책임을 할당하라

- GRASP의 CREATOR(창조자) 패턴은 책임 할당 패턴으로서 객체를 생성할 책임을 어떤 객체에게 할당할지에 대한 지침을 제공한다.

- **CREATOR 패턴**
    - 객체 A를 생성해야 할 때 어떤 객체에게 객체 생성 책임을 할당해야 하는가?
        - B가 A 객체를 포함하거나 참조한다.
        - B가 A 객체를 기록한다.
        - B가 A 객체를 긴밀하게 사용한다.
        - B가 A 객체를 초기화하는 데 필요한 데이터를 가지고 있다
    - CREATOR 패턴의 의도는 어떤 방식으로든 생성되는 객체와 연결되거나 관련될 필요가 있는 객체에 해당 객체를 생성할 책임을 맡기는 것이다.
    - 생성될 객체에 대해 잘 알고 있어야 하거나 그 객체를 사용해야 하는 객체는 어떤 방식으로든 생성될 객체와 연결될 것이다.
    - 이미 결합돼 있는 객체에게 생성 책임을 할당하는 것은 설계의 전체적인 결합도에 영향을 미치지 않는다.
    - CREATOR 패턴은 이미 존재하는 객체 사이의 관계를 이용하기 때문에 설계가 낮은 결합도를 유지할 수 있게 한다.

<br>

## 3. 구현을 통한 검증

```java
public class Screening {
    private Movie movie;
    private int sequence;
    private LocalDateTime whenScreened;

    public Reservation reserve(Customer customer, int audienceCount) {
        return new Reservation(customer, this, calculateFee(audienceCount), audienceCount);
    }

    private Money calculateFee(int audienceCount) {
        return movie.calculateMovieFee(this).times(audienceCount);
    }

    public LocalDateTime getWhenScreened() {
        return whenScreened;
    }

    public int getSequence() {
        return sequence;
    }
}

public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private List<DiscountCondition> discountConditions;

    private MovieType movieType;
    private Money discountAmount;
    private double discountPercent;

    public Money calculateMovieFee(Screening screening) {
        if (isDiscountable(screening)) {
            return fee.minus(calculateDiscountAmount());
        }

        return fee;
    }

    private boolean isDiscountable(Screening screening) {
        return discountConditions.stream()
                .anyMatch(condition -> condition.isSatisfiedBy(screening));
    }

    private Money calculateDiscountAmount() {
        switch(movieType) {
            case AMOUNT_DISCOUNT:
                return calculateAmountDiscountAmount();
            case PERCENT_DISCOUNT:
                return calculatePercentDiscountAmount();
            case NONE_DISCOUNT:
                return calculateNoneDiscountAmount();
        }

        throw new IllegalStateException();
    }

    private Money calculateAmountDiscountAmount() {
        return discountAmount;
    }

    private Money calculatePercentDiscountAmount() {
        return fee.times(discountPercent);
    }

    private Money calculateNoneDiscountAmount() {
        return Money.ZERO;
    }
}

public class DiscountCondition {
    private DiscountConditionType type;
    private int sequence;
    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;

    public boolean isSatisfiedBy(Screening screening) {
        if (type == DiscountConditionType.PERIOD) {
            return isSatisfiedByPeriod(screening);
        }

        return isSatisfiedBySequence(screening);
    }

    private boolean isSatisfiedByPeriod(Screening screening) {
        return dayOfWeek.equals(screening.getWhenScreened().getDayOfWeek()) &&
                startTime.compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                endTime.compareTo(screening.getWhenScreened().toLocalTime()) <= 0;
    }

    private boolean isSatisfiedBySequence(Screening screening) {
        return sequence == screening.getSequence();
    }
}
```

### DiscountCondition 개선하기

- DiscountCondition이 변경될 수 있는 세 가지 이유
    - 새로운 할인 조건 추가
    - 순번 조건을 판단하는 로직 변경
    - 기간 조건을 판단하는 로직이 변경되는 경우
- DiscountCondition은 하나 이상의 변경 이유를 가지기 때문에 응집도가 낮다.
- 낮은 응집도가 초래하는 문제를 해결하기 위해서는 **변경의 이유에 따라 클래스를 분리해야 한다**.

- 클래스 응집도 판단하기
    - 클래스가 하나 이상의 이유로 변경돼야 한다면 응집도가 낮은 것이다.
    변경의 이유를 기준으로 클래스를 분리하라.
    - 클래스의 인스턴스를 초기화하는 시점에 경우에 따라 서로 다른 속성들을 초기화하고 있다면 응집도가 낮은 것이다. 초기화되는 속성의 그룹을 기준으로 클래스를 분리하라.
    - 메서드 그룹이 속성 그룹을 사용하는지 여부로 나뉜다면 응집도가 낮은 것이다.
    이들 그룹을 기준으로 클래스를 분리하라.

### 타입 분리하기

- DiscountCondition의 가장 큰 문제는 순번 조건과 기간 조건이라는 두 개의 독립적인 타입이 하나의 클래스 안에 공존하고 있다는 점이다.
- 이를 해결할 수 있는 방법은 두 타입을 SequenceCondition과 PeriodCondition이라는 두 개의 클래스로 분리하는 것이다.
- 하지만 이후 Movie의 인스턴스는 SequenceCondition과 PeriodCondition 두 개의 서로 다른 클래스의 인스턴스 모두와 협력할 수 있어야 한다는 문제점이 발생한다.

### 다형성을 통해 분리하기

- 역할을 사용하면 객체의 구체적인 타입을 추상화할 수 있다.
- 자바에서는 일반적으로 역할을 구현하기 위해 추상 클래스나 인터페이스를 사용한다.
    - 역할을 대체할 클래스들 사이에서 구현을 공유해야 할 필요가 있다면 추상 클래스를 사용
    - 구현을 공유할 필요 없이 역할을 대체하는 객체들의 책임만 정의하고 싶다면 인터페이스를 사용
- 객체의 암시적인 타입에 따라 행동을 분기해야 한다면 암시적인 타입을 명시적인 클래스로 정의하고 행동을 나눔으로써 응집도 문제를 해결할 수 있다.
- 객체의 타입에 따라 변하는 행동이 있다면 타입을 분리하고 변화하는 행동을 각 타입의 책임으로 할당하라는 것이다.
- GRASP에서는 이를 POLYMORPHISM(다형성) 패턴이라고 부른다.

- **POLYMORPHISM 패턴**
    - 객체의 타입에 따라 변하는 로직이 있을 때 변하는 로직을 담당할 책임을 어떻게 할당해야 하는가?
    타입을 명시적으로 정의하고 각 타입에 다형적으로 행동하는 책임을 할당하라.
    - 객체의 타입을 검사해서 타입에 따라 여러 대안들을 수행하는 조건적인 논리를 사용하지 말라고 경고하며, 대신 다형성을 이용해 새로운 변화를 다루기 쉽게 확장하라고 권고한다.

### 변경으로부터 보호하기

- 변경을 캡슐화하도록 책임을 할당하는 것을 PROTECTED VARIATIONS(변경 보호) 패턴이라고 부른다.

- **PROTECTED VARIATIONS 패턴**
    - 변화가 예상되는 불안정한 지점들을 식별하고 그 주위에 안정된 인터페이스를 형성하도록 책임을 할당하라.
    - “설계에서 변하는 것이 무엇인지 고려하고 변하는 개념을 캡슐화하라”
    - 우리가 캡슐화해야 하는 것은 변경이다.

### 변경과 유연성

- 설계를 주도하는 것은 변경이다.
- 개발자로서 변경에 대비할 수 있는 두 가지 방법
    - 코드를 이해하고 수정하기 쉽도록 최대한 단순하게 설계하는 것
    - 코드를 수정하지 않고도 변경을 수용할 수 있도록 코드를 더 유연하게 만드는 것
- 대부분 전자가 더 좋은 방법이지만 유사한 변경이 반복적으로 발생하고 있다면 복잡성이 상승하더라도 유연성을 추가하는 두 번째 방법이 더 좋다.
- 새로운 기능이 추가될 때마다 인스턴스를 생성하고, 상태를 복사하고, 식별자를 관리하는 코드를 추가하는 일은 번거롭고 오류가 발생하기 쉽다.
- 이 경우 코드의 복잡성이 높아지더라도 변경을 쉽게 수용할 수 있게 코드를 유연하게 만드는 것이 더 좋은 방법이다.
- 해결 방법은 상속 대신 **합성**을 사용하는 것이다.
- 요소들 사이의 의존성의 정도가 유연성의 정도를 결정한다.
- 유연성의 정도에 따라 결합도를 조절할 수 있어야 한다.

<br>

## 4. 책임 주도 설계의 대안

- **리팩터링(Refactoring)** : 이해하기 쉽고 수정하기 쉬운 소프트웨어로 개선하기 위해 겉으로 보이는 동작은 바꾸지 않은 채 내부 구조를 변경하는 것

### 메서드 응집도

- 긴 메서드가 코드의 유지보수에 미치는 부정적인 영향
    - 어떤 일을 수행하는지 한눈에 파악하기 어렵기 때문에 코드를 전체적으로 이해하는 데 많은 시간이 걸린다.
    - 하나의 메서드 안에서 너무 많은 작업을 처리하기 때문에 변경이 필요할 때 수정해야 할 부분을 찾기 어렵다.
    - 메서드 내부의 일부 로직만 수정하더라도 메서드의 나머지 부분에서 버그가 발생할 확률이 높다.
    - 로직의 일부만 재사용하는 것이 불가능하다.
    - 코드를 재사용하는 유일한 방법은 원하는 코드를 복사해서 붙여넣는 것뿐이므로 코드 중복을 초래하기 쉽다.
- 요약하자면, 긴 메서드는 응집도가 낮기 때문에 이해하기도 어렵고 재사용하기도 어려우며 변경하기도 어렵다.
- 이런 메서드를 **몬스터 메서드(monster method)** 라고 부른다.
- 클래스가 작고, 목적이 명확한 메서드들로 구성돼 있다면 변경을 처리하기 위해 어떤 메서드를 수정해야 하는지를 쉽게 판단할 수 있다.
- 또한 메서드의 크기가 작고 목적이 분명하기 때문에 재사용하기도 쉽다.
- 객체로 책임을 분배할 때 가장 먼저 할 일은 메서드를 응집도 있는 수준으로 분해하는 것이다.

### 객체를 자율적으로 만들자

- 메서드를 이동할 때 캡슐화, 응집도, 결합도의 측면에서 이동시킨 메서드의 적절성을 판단하자.
- 책임 주도 설계 방법이 익숙하지 않다면 일단 데이터 중심으로 구현한 후 이를 리팩터링하더라도 유사한 결과를 얻을 수 있다.
