### 이번 장에서는

GRASP 패턴을 통해 응집도와 결합도, 캡슐화 같은 기준에 따라 책임을 할당하는 어려움을 해결하기 위한 방법을 알아볼 예정

# 책임 주도 설계를 향해

데이터 중심 설계 → 책임 중심 설계에 필요한 원칙

- 데이터보다 행동을 먼저 결정하라
- 협력이라는 문맥 안에서 책임을 결정하라

⇒ 설계를 할 때 데이터가 아니라 객체의 책임과 협력에 초점을 맞추는 것

### 객체에게 어떤 책임을 할당해야 하는가

객체지향 설계에서 중요한 것은 적절한 객체에게 적절한 책임을 할당하는 것이다.

- **협력이라는 문맥 안에서 책임을 결정**
    - 책임은 객체의 입장이 아니라 **객체가 참여하는 협력**에 적합해야 한다
    - 메시지를 전송하는 클라이언트의 의도에 적합한 책임을 할당해야 한다
    - 객체가 메시지를 선택하는 것이 아니라 메시지가 객체를 선택하게 해야 한다
    
    ⇒ **실제 설계할 때 인터페이스를 잘 설계해야 한다**
    
- **책임 주도 설계 (Responsibility-Driven Design)**
    - 책임 주도 설계의 흐름
        - 시스템이 사용자에게 제공해야 하는 기능인 **시스템 책임**을 파악
        - 시스템 책임을 **더 작은 책임으로 분할**
        - 분할된 책임을 수행할 수 있는 적절한 객체 또는 역할을 찾아 책임을 할당
        - 객체가 책임을 수행하는  도중 다른 객체의 도움이 필요한 경우 이를 책임질 적절한 객체 또는 역할을 찾기
        - 해당 객체 또는 역할에게 책임을 할당함으로써 두 객체가 협력하게 한다.
    - 즉 객체의 책임은 **거시적**으로 봐야한다.

# 책임 할당을 위한 GRASP 패턴

책임 할당 기법중 크레이그 라만이 패턴 형식으로 제안한 `GRASP 패턴`이 가장 널리 알려져 있다.

GRASP는 “General Responsibility Assignment Software Pattern(일반적인 책임 할당을 위한 소프트웨어 패턴)”의 약자로 객체에게 책임을 할당할 때 지침으로 삼을 수 있는 원칙들의 집합을 패턴 형식으로 정리한 것

이전에 했던 영화 예매 시스템을 책임 중심으로 설계하는 과정을 따라가보기

### 도메인 개념에서 출발하기

- **도메인 개념**을 책임 할당의 대상으로 사용한다면 코드에 도메인의 모습을 투영하기가 수월해진다.
- 설계를 시작하는 단계에서는 개념들의 의미와 관계가 정확하거나 완벽할 필요가 없다.
- 올바른 도메인 모델이란 존재하지 않는다.
    - 할인 정책
        - 독립적인 할인 정책
        - 영화의 종류
        
        ⇒ 올바른 구현을 이끌어낼 수 있다면, 둘 다 옳은 모델
        

### 정보 전문가에게 책임을 할당하라

책임 주도 설계의 첫 단계는 애플리케이션이 제공해야 하는 기능을 애플리케이션의 책임으로 생각하는 것

**영화 예매 시나리오**

- **메시지를 전송할 객체는 무엇을 원하는가? (“예매하라”)**
    - 협력 시작 객체는 모르지만, 원하는 것은 영화를 예매하는 것
- **메시지를 수신할 적합한 객체는 누구인가?**
    - 객체는 상태와 행동을 통합한 캡슐화의 단위
    
    ⇒ 객체의 책임과 책임을 수행하는 데 필요한 상태는 동일한 객체 안에 존재해야 한다
    
    - 책임을 수행할 정보를 알고 있는 객체에게 책임을 할당하기 ****
        - **INFORMATION EXPERT(정보 전문가) 패턴**
    - 정보 전문가가 정보를 `‘알고 있다’`고 해서 ****그 정보를 `‘저장’` 하고 있을 필요는 없다
- “**예매하라”**에 어울리는 객체는 **‘상영’이라는 도메인 개념** (Screening에 책임 할당)
- **Screening은 외부 객체에게 도움을 요청해 데이터를 얻어와야 한다 (가격을 계산하라 Movie)**
    - 예매하라 메시지를 완료하기 위해서는 영화 한 편의 가격을 알아야 한다.
    - Screening은 가격을 계산하는 데 필요한 정보를 모르기에, 외부에게 메시지를 보내 가격을 받아온다.
- Movie는 할인 여부를 판단할 정보가 필요 (DiscountCondition)
- 객체는 상태와 행동을 함게 가지는 단위라는 객체지향의 원리를 책임 할당의 관점에서 표현하는 위와 같은 방식이 `INFORMATION EXPERT 패턴`

## 높은 응집도와 낮은 결합도

- 트레이드 오프를 하기 위해, 이전에 진행했던 INFORMATION EXPERT 패턴 이외의 다른 책임 할당 패턴도 고민해 봐야 한다
    - Screening이 직접 DiscountCondition과 협력하는 방법
    
   ![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/75903442/756dc935-2db5-4fff-aff8-b37be620362c)

    
    - 기능적으로는 동일
    - 응집도와 결합도에 차이가 있는데, 다양한 대안이 존재한다면 응집도와 결합도 측면에서 바라보고 좋은 대안으로 선택해야 한다
    - GRASP에서는 이를 LOW COUPLING(낮은 결합도) 패턴과 HIGH COHESION(높은 응집도) 패턴이라고 부른다.
- **LOW COUPLING** 패턴
    - **의존성을 낮추고 변화의 영향을 줄이며 재사용성을 증가**시킬 **결합도**를 낮게 유지되도록 책임 할당하기
    - Movie가 DiscountCondition과 협력하는 것이 더 나은 설계 대안
- **HIGH COHESION** 패턴
    - **복잡성을 관리할 수 있는 수준으로 유지하기 위한 응집도**를 높게 책임 할당하기
    - Movie가 DiscountCondition과 협력하는 것이 더 나은 설계 대안
- 창조자에게 객체 생성 책임을 할당(**CREATOR 패턴**)
    - 이미 결합돼 있는 객체에게 생성 책임을 할당하기 때문에 전체 결합도에 영향을 미치지 않음
    ⇒ **낮은 결합도 유지**
    - 객체 A를 생성할 때 아래의 조건을 최대한 많이 만족하는 B에게 객체 생성 책임을 할당하기
        - B가 A 객체를 포함하거나 참조한다
        - B가 A 객체를 기록한다
        - B가 A 객체를 긴밀하게 사용한다
        - B가 A 객체를 초기화하는 데 필요한 데이터를 가지고 있다(B는 A에 대한 정보 전문가)
    - Screening은 예매 정보를 생성하는 데 필요한 영화, 상영 시간, 상영 순번 등의 정보에 대한 전문가이며, Movie 정보도 알고있다.
        
        ⇒ Screening을 Reservation의 CREATOR로 선택
        
       ![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/75903442/c078d5cb-2e58-4ba2-b287-af593cef8376)

        

# 구현을 통한 검증

- Screening에서 create를 통해 Reservation을 구현
    
    ```java
    public class Screening{
    	private Movie movie;
      private int sequence;
      private LocalDateTime whenScreened;
    
      public Reservation reserve(Customer customer, int audienceCount) {
          return new Reservation(
    				customer, 
    				this, 
    				calculateFee(audienceCount), 
    				audienceCount
    			);
      }
    
    	private Money calculateFee(int audienceCount) {
          return movie.calculateMovieFee(this).times(audienceCount);
      }
    }
    ```
    
    - 책임이 결정됨
    - Screening과 Movie를 연결하는 유일한 연결 고리는 메시지(calculateMovieFee)뿐
    - 나머지 세부 사항은 Movie 객체에 맡기면 됨
- Movie는 DiscountCondition에 할인 여부를 판단하는 메시지를 전송해야 함
    
    ```java
    public class Movie{
    
    	private List<DiscountCondition> discountConditions;
    	
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
    
    }
    ```
    
    - discountCondition의 정보를 discountCondition 객체에서 받아옴
- DiscountCondition 개선하기
    - DiscountCondition은 변경에 취약한 클래스를 포함하고 있다.
        - 새로운 할인 조건이 들어왔을 때 취약
        - 순번 조건을 판단하는 로직이 변경
        - 기간 조건을 판단하는 로직이 변경되는 경우
        
        ⇒ `응집도가 낮다`
        
    - 변경의 이유에 따라 클래스를 분리
- 함께 초기화되는 속성을 기준으로 코드를 분리해야 한다

### 타입 분리하기

순번 조건과 기간 조건의 타입을 각각 SequenceCondition과 PeriodCondition 클래스로 분리하기

- 클래스를 분리했을 때 문제점이 해결된다.
    
    ```java
    public class SequenceCondition {
        private int sequence;
    
        public SequenceCondition(int sequence) {
            this.sequence = sequence;
        }
    
        public boolean isSatisfiedBy(Screening screening) {
            return sequence == screening.getSequence();
        }
    }
    ```
    
- Movie의 인스턴스가 SequenceCondition과 PeriodCondition이라는 두 클래스 모두와 협력해야 한다 → 응집도가 높아짐
    
    ⇒ Movie 클래스 안에서 SequenceCondition의 목록과 PeriodCondition의 목록을 따로 유지하도록 변경
    
- 위의 방법을 사용 시 응집도는 높아졌지만 변경과 캡슐화 즉 **결합도 관점**에서 설계의 품질이 나빠졌다.

### 다형성을 통해 분리하기

**DiscountCondition의 역할**을 사용하기 시작 큰 개념으로 할인 정책을 담는 DiscountCondition을 사용해 **객체의 타입을 추상화**

```java
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
```

- GRASP에서 이 패턴을 **POYMORPHISM(다형성)** 패턴이라고 부른다.
- 해당 코드는 변경으로부터 보호되는데 각 Condition의 내부 로직이 변경되지 않는 캡슐화가 되어있기 때문이다.
    - 새로운 DiscountCondition 타입이 추가되어도 변경되지 않음

⇒ 변경을 캡술화하도록 책임을 할당하는 **GRASP - PROTECTED VARIATIONS(변경 보호)** 패턴

### MOVIE 클래스 개선

MOVIE 클래스도 동일하게 다형성을 적용해 변경해야 한다.

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/75903442/06a50dfe-93fa-4126-8425-c9eee1c6831e)


- 결국 변경에 강한 코드를 만들기 위해서는 데이터 중심이 아닌 책임을 중심으로 설계해야 한다

### 변경과 유연성

- Movie에서 상속을 이용하기 때문에 유연하지 않다.
- 때문에 DiscountPolicy로 분리하여 Movie에 합성시켜 유연한 설계를 만들어야 한다

# 책임 주도 설계의 대안

설계를 할 때 완벽하게 할 수 없기 때문에 우리 개발자들은 구현 후 리팩터링 작업을 통해 내부 구조를 변경한다.

- **메서드 응집도**
    - reserve 메서드는 상당히 길다. 긴 메서드는 코드를 이해하는데 오랜 시간이 걸리기에 유지보수에 부정적이다.
        - 한눈에 파악하기에 오랜 시간이 걸림
        - 변경이 필요할 때 수정할 부분을 찾기 어려움
        - 메서드의 나머지 부분에서 버그가 발생할 확률이 높음
        - 로직의 일부만 재사용이 불가능
        - 코드의 중복을 초래하기 쉬움
    
    ⇒ 이처럼 긴 메서드를 **몬스터 메서드**라고 부른다
    
- **자율적인 객체**
    - 접근자 메서드를 통해 메서드가 어떤 역할을 하는지 파악할 수 있다
    - 메시지를 통해서만 통신하기에 이에 맞춰 구현한다면 캡슐화와 높은 응집도, 낮은 결합도를 가지는 설계가 완성된다
