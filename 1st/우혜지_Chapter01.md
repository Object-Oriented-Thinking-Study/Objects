# Chapter01 객체, 설계

## 1. 티켓 판매 애플리케이션 구현하기

어플리케이션에서 필요한 각각의 도메인 클래스를 생성한다. (Audience, Bag, Ticket, Invitation(이벤트 당첨), TicketOffice, TicketSeller, Theater)

초대장은 무료입장권을 의미하며 몇일자의 무료입장권인지만을 보여준다.

```java
public class Invitation {
    private LocalDateTime when;
}
```

초대장과 관계없이 모든 관객은 티켓을 가지고 있어야 들어갈 수 있다. 초대장이 있는 경우 티켓을 무료 배부한다.

```java
public class Ticket {
    private Long fee;

    public Long getFee() {
        return fee;
    }
}
```

가방에는 현금과 초대장 그리고 티켓을 가지고 있다.

```java
public class Bag {
    private Long amount;
    private Invitation invitation;
    private Ticket ticket;

    public boolean hasInvitation() {
        return invitation != null;
    }

    public boolean hasTicket() {
        return ticket != null;
    }

    public void setTicket(Ticket ticket) {
        this.ticket = ticket;
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

Bag의 인스턴스의 상태는 현금과 초대장을 함께 보관하거나, 초대장 없이 현금만 보관하는 두 가지 중 하나일 것이다. Bag의 인스턴스를 생성하는 시점에 이 제약을 강제할 수 있도록 생성자를 추가한다.

```java
public class Bag {
		public Bag(long amount) {
        this(null, amount);
    }
    
    public Bag(Invitation invitation, long amount) {
        this.invitation = invitation;
        this.amount = amount;
    }
}
```

입장하고자 하는 관객을 담는 클래스로 각각의 관객은 가방을 가지고 있다.

```java
class Audience {
    private Bag bag;
    
    public Audience(Bag bag) {
        this.bag = bag;
    }
    
    public Bag getBag() {
        return bag;
    }
}
```

티켓오피스에서는 티켓을 가지고 있고 티켓이 판매되면 티켓을 줄이고 가격을 높이는 등의 기능을 제공한다.

```java
public class TicketOffice {
    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();
    
    public TicketOffice(Long amount, Ticket ... tickets) {
        this.amount = amount;
        this.tickets.addAll(Arrays.asList(tickets));
    }
    
    public Ticket getTicket() {
        return tickets.remove(0);
    }
    
    public void minusAmount(Long amount) {
        this.amount -= amount;
    }
    
    public void plusAmount(Long amount) {
        this.amount += amount;
    }
}
```

판매원은 자신이 소속된 티켓오피스를 가지고 있다.

```java
public class TicketSeller {
    private TicketOffice ticketOffice;
    
    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }
    
    public TicketOffice getTicketOffice() {
        return ticketOffice;
    }
}
```

프로그램을 담당하는 극장은 판매원이 있으며 각각의 도메인에서 데이터를 가져와 입장과 관련된 일을 처리한다.

```java
public class Theater {
    private TicketSeller ticketSeller;
    
    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }
    
    public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusTicket(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

<br>

## 2. 무엇이 문제인가

> 모든 소프트웨어 모듈에는 세 가지 목적이 있다.
첫 번째 목적은 **실행 중에 제대로 동작하는 것이다**. 이것은 모듈의 존재 이유라고 할 수 있다.
두 번째 목적은 **변경을 위해 존재하는 것이다**. 대부분의 모듈은 생명주기 동안 변경되기 때문에 간단한 작업만으로도 변경이 가능해야 한다. 변경하기 어려운 모듈은 제대로 동작하더라도 개선해야 한다.
모듈의 세 번째 목적은 **코드를 읽는 사람과 의사소통하는 것이다**. 모듈은 특별한 훈련 없이도 개발자가 쉽게 읽고 이해할 수 있어야 한다. 읽는 사람과 의사소통할 수 없는 모듈은 개선해야 한다.          - 로버트 마틴 (Robert C. Martin)
> 

앞선 티켓 판매 애플리케이션은 제대로 실행한다는 첫 번째 제약은 만족하지만, 변경 용이성과 의사소통이라는 목적은 만족하지 못하고 있다.

### 예상을 빗나가는 코드

문제는 관람객과 판매원이 소극장의 통제를 받는 수동적인 존재라는 점이다.

- Theater 클래스의 `enter()` 메소드가 하는 일
    - 소극장은 관람객의 가방을 열어 그 안에 초대장이 들어 있는지 살펴 본다.
    - 가방 안에 초대장이 들어 있으면 판매원에게 매표소에 보관되어 있는 티켓을 관람객의 가방 안으로 옮기라고 시킨다.
    - 가방 안에 초대장이 들어 있지 않으면 관람객의 가방 안에서 티켓 금액만큼의 현금을 꺼내 티켓을 구매하고, 티켓을 가방 안에 넣는다.
- 관람객 입장에서는 소극장이라는 제 3자가 마음대로 가방을 뒤적이고 돈을 가져가고, 티켓을 넣는 것을 지켜봐야 한다.
- 판매원 입장에서는 소극장이라는 제 3자가 허락도 없이 매표소에 티켓과 현금의 값을 조작하는 것을 지켜봐야 한다.
- 이해 가능한 코드란 동작이 우리의 예상을 크게 벗어나지 않는 코드를 뜻하는데, 위 소극장은 우리의 예상을 벗어난다.
    - 관람객은 스스로 가방 안에서 돈을 꺼내 판매원에게 지불하여 티켓을 받아야 한다.
    - 판매원은 스스로 매표소에 있는 티켓을 직접 꺼내 관람객에게 전달하고, 관람객에게서 돈을 받아 매표소에 보관해야 한다.
- 또한, `enter()` 메소드를 이해하기 위해서는 여러 가지 세부적인 내용을 모두 기억하고 있어야 한다.
    - Audience가 Bag을 가지고 있다.
    - Bag 안에는 현금과 티켓이 있다.
    - TiketSellet가 TicketOffice에서 티켓을 판매하고, TicketOffice 안에 돈과 티켓이 보관되어 있다.

### 변경에 취약한 코드

`enter()` 메소드는 2가지 조건을 가정한다.

- 관람객은 현금과 초대장을 보관하기 위해 항상 가방을 들고 다닌다.
- 판매원은 매표소에서만 티켓을 판매한다.

그렇다면 아래 상황은 어떨까?

- 관람객은 가방이 없을 수 있다.
- 관람객은 현금이 아니라 신용 카드를 사용할 수 있다.
- 판매원이 매표소 밖에서 티켓을 판매할 수 있다.

다른 클래스가 Audience의 내부에 대해 더 많이 알면 알수록 Audience를 변경하기 어려워진다.

- 이것은 객체 사이의 **의존성(dependency)**과 관련된 문제다.
    - 의존성은 변경에 대한 영향을 암시한다.
- 우리의 목표는 애플리케이션의 기능을 구현하는 데 필요한 최소한의 의존성만 유지하고 불필요한 의존성을 제거하는 것이다.
- 객체 사이의 의존성이 과한 경우를 가리켜 **결합도(coupling)**가 높다고 말한다.

<br>

## 3. 설계 개선하기

### 자율성을 높이자

```java
public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public void sellTo(Audience audience) {
        if (audience.getBag().hasTicket()) {
            Ticket ticket = ticketOffice.getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketOffice.getTicket();
            audience.getBag().minusTicket(ticket.getFee());
            ticketOffice.plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}

public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        ticketSeller.sellTo(audience);
    }
}
```

- 이처럼 개념적이나 물리적으로 객체 내부의 세부적인 사항을 감추는 것을 **캡슐화(encapsulation)**라고 부른다.

```java
public class Audience {
    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }

    public Long buy(Ticket ticket) {
        if (bag.hasInvitation()) {
            bag.setTicket(ticket);
            return 0L;
        } else {
            bag.setTicket(ticket);
            bag.minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }
}

public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public void sellTo(Audience audience) {
        ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    }
}
```

![image](https://github.com/Object-Oriented-Thinking-Study/Objects/assets/118504257/fa45f15e-61be-4014-a723-a8a7d2d6145a)


### 캡슐화와 응집도

- 핵심은 객체 내부의 상태를 캡슐화하고 객체 간에 오직 메시지를 통해서만 상호작용하도록 만드는 것이다.
- 밀접하게 연관된 작업만을 수행하고 연관성 없는 작업은 다른 객체에게 위임하는 객체를 가리켜 **응집도(cohesion)**가 높다고 말한다.
- 자신의 데이터를 스스로 처리하는 자율적인 객체를 만들면 결합도를 낮출 수 있을뿐더러 응집도를 높일 수 있다.

### 절차지향과 객체지향

- 수정하기 전의 코드 기준으로 Theater의 enter 메서드는 **프로세스(Process)**이며 
Audience, TicketSeller, Bag, TicketOffice는 **데이터(Data)**다.
- 이처럼 프로세스와 데이터를 별도의 모듈에 위치시키는 방식을 **절차지향적 프로그래밍(Procedural Programming)**이라고 부른다.
- 데이터와 프로세스가 동일한 모듈 내부에 위치하도록 프로그래밍하는 방식을 **객체지향 프로그래밍(Object-Oriented Programming)**이라고 부른다.
- 훌륭한 객체지향 설계의 핵심은 캡슐화를 이용해 의존성을 적절히 관리함으로써 객체 사이의 결합도를 낮추는 것이다.
- 객체지향 코드는 자신의 문제를 스스로 처리해야 한다는 우리의 예상을 만족시켜주기 때문에 이해하기 쉽고, 객체 내부의 변경이 객체 외부에 파급되지 않도록 제어할 수 있기 때문에 변경하기가 수월하다.

### 책임의 이동

- 두 방식 사이에 근본적인 차이를 만드는 것은 **책임의 이동(shift of responsibility)**이다.
- 여기서의 ‘책임’을 기능을 가리키는 객체지향 세계의 용어로 생각해도 무방하다.
- 객체지향 설계에서는 독재자가 존재하지 않고 각 객체에 책임이 적절하게 분배된다.
- 따라서 각 객체는 **자신을 스스로 책임**진다.
- 객체지향 애플리케이션은 스스로 책임을 ㅅ행하는 자율적인 객체들의 공동체를 구성함으로써 완성된다.

> 설계를 어렵게 만드는 것은 **의존성**이다.
해결 방법은 불필요한 의존성을 제거함으로써 객체 사이의 **결합도**를 낮추는 것이다.
예제에서 결합도를 낮추기 위해 선택한 방법은 Theater가 몰라도 되는 세부사항을 Audience와 TicketSeller 내부로 감춰 **캡슐화**하는 것이다.
결과적으로 불필요한 세부사항을 객체 내부로 캡슐화하는 것은 객체의 **자율성**을 높이고 **응집도** 높은 객체들의 공동체를 창조할 수 있게 한다.
> 

### 그래, 거짓말이다!

- 현실에서는 수동적인 존재라고 하더라도 객체 지향의 세계에 들어오면 모든 것이 능동적이고 자율적인 존재로 바뀐다.
- 의인화를 사용하여 수동적인 객체를 마치 웃고, 떠들고 화내는 대상으로 생각하는 것이 좋다.

## 4. 객체지향 설계

### 설계가 왜 필요한가

- 설계란 코드를 배치하는 것이다.
- 좋은 설계란 오늘 요구하는 기능을 온전히 수행하면서 내일의 변경을 매끄럽게 수용할 수 있는 것이다.

### 객체지향 설계

- 변경 가능한 코드란 이해하기 쉬운 코드다.
- 객체 지향 패러다임은 우리가 세상을 바라보는 방식대로 코드를 작성할 수 있게 돕는다.
- 객체는 자신의 데이터를 스스로 책임지는 자율적인 존재다.
- 훌륭한 객체 지향 설계란 협력하는 객체 사이의 의존성을 적절하게 관리하는 설계다.
