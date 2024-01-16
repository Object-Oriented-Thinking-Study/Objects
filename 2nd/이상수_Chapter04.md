> 객체지향 설계란 올바른 책임을 할당하면서 낮은 결합도와 높은 응집도를 가진 구조를 창조하는 활동 - Evers
> 
- 객체지향 설계의 핵심이 책임
- 책임을 할당하는 작업이 응집도와 결합도 같은 설계 품질과 깊은 연관

⇒ 설계는 변경을 위해 존재하고 변경에는 비용이 발생하며, 훌륭한 설계는 합리적인 비용 안에서 변경을 수용할 수 있는 구조를 만드는 것이다.

이번 장은 영화 예매 시스템을 상태를 표현하는 데이터 중심의 설계(Data-Driven Design) 형태로 살펴본다

# 데이터 중심의 영화 예매 시스템

객체지향 설계에서는 두 가지 방법을 이용해 시스템을 객체로 분할할 수 있다.

- 상태(데이터)를 분할의 중심축으로 삼는 방법
- 책임을 분할의 중심축으로 삼는 방법

데이터 중심 관점에서의 객체는 자신이 포함하고 있는 데이터를 조작하는 데 필요한 명령을 정의한다.

⇒ 데이터 중심 관점은 객체를 독립된 데이터 덩어리로 바라보고, 상태 중심 관점에서는 객체를 협력하는 공동체의 일원으로 바라본다

객체의 상태(데이터)는 구현에 속한다. 구현은 불안정하기 때문에 변하기 쉽다. 상태를 객체 분할의 중심축으로 삼으면 구현에 관한 세부사항이 객체의 인터페이스에 스며들게 되어 캡슐화의 원칙이 무너진다.

⇒ 상태 변경은 인터페이스의 변경을 초래하며, 인터페이스에 의존하는 모든 객체에게 변경의 영향이 퍼진다(변경에 취약)

RDD(Responsibility-Driven Design)가 아닌 DDD(Data-Driven Design)으로 설계하며 비교해보자

## 데이터 준비

데이터 중심 설계란 객체 내부에 저장되는 데이터를 기반으로 시스템을 분할한다.

- Movie에 저장될 데이터를 결정
    
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
    
- 첫 설계부터 discountCondition이 바깥으로 노출
- discount의 정책인 Amount,Percent 둘 다 Movie에서 직접 정의
- movieType을 통해 discount 정책을 설정
- 객체의 막을 빠져나가는 것을 막기 위해 접근자(accessor)와 수정자(mutator)를 추가(캡슐화)

… 데이터 준비를 마친 이후

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/063bb2d1-ee4b-4cdf-a8cb-3612352a6b48/58827f73-d458-4a48-bd95-026a81c67860/Untitled.png)

위와 같이 클래스가 구현되었다.

## 영화 예매

```java
public Reservation reserve(Screening screening, Customer customer,
                               int audienceCount) {
        Movie movie = screening.getMovie();

        boolean discountable = false;
        for(DiscountCondition condition : movie.getDiscountConditions()) {
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
            switch(movie.getMovieType()) {
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

            fee = movie.getFee().minus(discountAmount).times(audienceCount);
        } else {
            fee = movie.getFee().times(audienceCount);
        }

        return new Reservation(customer, screening, fee, audienceCount);
    }
```

---

# 설계 트레이드오프

## 캡슐화

캡슐화를 지키는 이유는 내부 구현을 외부로부터 감춰 세부 사항 변경이 있을 때 파급력이 적게 하기 위해 캡슐화 기법을 사용한다.

- **구현**
    - 변경될 가능성이 높은 세부 사항
- **인터페이스**
    - 상대적으로 안정적인 부분
- 캡슐화의 범위
    - `변경의 가능성이 있는 모든 부분`

## 응집도와 결합도

응집도와 결합도는 구조적 설계 방법이 주도하는 시대에 생긴 기준이지만, 객체지향 시대에서도 동일하게 사용된다.

- **응집도**
    - 모듈에 포함된 내부 요소들이 연관돼 있는 정도를 나타냄
    - 모듈의 요소가 하나의 목적을 위해 긴밀하게 협력하면 해당 모듈은 ‘응집도가 높다’라고 부름
    - 객체지향 관점에서는 객체나 클래스에 얼마나 `관련 높은 책임`을 할당했는지를 나타냄
- **결합도**
    - 의존성의 정도를 나타내며 다른 모듈에 대해 얼마나 많은 지식을 갖고 있는지를 나타내는 척도
    - 다른 모듈에 대해 너무 자세한 정보까지 알고 있다면 ‘결합도가 높다’라고 부름
    - 객체지향 관점에서의 결합도는 협력에 필요한 적절한 수준의 관계만 유지해야 한다
    - 즉 결합도는 낮을수록 좋다.
- 높은 응집도와 낮은 결합도를 추구해야 하는 이유
    - `설계를 변경하기 쉽게 만들기 때문`
    - **변경 관점에서 응집도**
        - 변경이 발생할 때 모듈 내부에서 발생하는 변경의 정도
    - **변경 관점에서의 결합도**
        - 한 모듈이 변경되기위해 다른 모듈의 변경을 요구하는 정도
        - 하나의 모듈을 수정할 때 얼마나 많은 모듈을 함께 수정해야 하는지

# 데이터 중심의 영화 예매 시스템의 문제점

데이터 중심의 설계는 캡슐화를 위반하고 객체의 내부 구현을 인터페이스의 일부로 만든다. 때문에 데이터 중심의 설계가 가진 문제점은 다음과 같다.

- **캡슐화 위반**
    - 데이터에 초점을 맞춘 것이 근본적인 원인
    - 구현을 캡슐화할 수 있는 적절한 책임은 협력이라는 문맥을 고려했을 때 얻을 수 있음
    - **추측에 의한 설계 전략(design-by-guessing strategy)**
        - 접근자와 수정자에 과도하게 의존하는 설계 방식
- **높은 결합도**
    - 객체의 내부 구현을 변경했을 때 다른 객체도 변경해야 함
    - 데이터 중심의 설계는 전체 시스템을 하나의 거대한 의존성 덩어리로 만들게 된다
- **낮은 응집도**
    - ReservationAgency
        - 할인 정책이 추가
        - 할인 정책 별로 할인 요금을 계산하는 방법이 변경
        - 할인 조건이 추가
        - 할인 여부 판단 조건 변경
        - 예매 요금 계산방법 변경
    - 위와 같은 수정사항이 발생했을 때 문제점
        - 변경과 아무 상관이 없는 코드들에게 영향
        - 하나의 요구사항 변경을 반영하기 위해 여러 모듈을 수정
- `단일 책임 원칙`
    - 모듈의 응집도가 변경과 연관이 있다는 사실을 강조하기 위해 소프트웨어 설계 원칙으로 `단일 책임 원칙` 이라는 설계 원칙이 제시됨
    - 이 때 지금까지 살펴본 역할, 책임, 협력에서 이야기하는 책임과는 다르며, 변경과 관련된 큰 개념을 가리킴

# 자율적인 객체를 향해

## 캡슐화를 지켜라

캡슐화는 설계의 제 1원리이다. 객체는 스스로의 상태를 책임져야 하며 외부에서는 인터페이스에 정의된 메서드를 통해서만 상태에 접근할 수 있어야 한다.

### 사각형 클래스

사각형의 너비와 높이를 증가시켜야 할 때 사각형 클래스 안에 설계된 메서드가 아닌, 사각형을 갖고있는 어떠한 클래스가 setRight()등과 같은 메서드로 외부에서 동작시킬 것이다.

**문제점**

- **코드 중복**
    - 다른 클래스에서도 사각형의 너비와 높이를 증가시키는 코드가 필요하다면 그곳에도 동일한 로직을 가진 메서드가 필요
- **변경 취약**
    - top, left, right, bottom을 모두 외부에서 꺼내서 활용하기 때문에 변경이 일어났을 때 외부에서 꺼낸 클래스 모두 변경해야 함
- **해결 방법**
