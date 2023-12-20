# Chapter01 객체, 설계

## step01. 구현

- 수동적이지 않은 객체
    - 객체의 메서드가 모두 열려있음 - public
    - 하나의 객체(Theater)가 모든 것을 조종함 - Processing(절차지향적)
- 변경에 취약한 코드
    - 요구사항이 변경 될 가능성
    - 구현된 클래스가 다른 클래스에 의존되어 구현된다.
    - 결합도(coupling)가 높음

<aside>
💡 모듈의 세가지 목적 - 로버트 마틴 -

- 제대로 실행되어야 함
- 변경이 용이해야 함
- 이해하기 쉬워야 함
</aside>

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
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

## Step02. 설계 개선

- 자율적인 존재로 만들기
    - 기존 : 티켓 판매를 티켓 판매원이 아닌 Theater가 담당함
    - 변경 : 티켓 판매 로직을 티켓 판매원에게 양도
        
        ```java
        TicketSeller{
        ...
        
        public void sellTo(Audience audience){
        	if(audienct.getBag().hasInvitation()){
        		Ticket ticket = ticketOffice.getTicket();
        		audience.getBag().setTicket(ticket);
        	}else{
        		Ticket ticket = ticketOffice.getTicket();
        		audience.getBag().minusAmount(ticket.getFee());
        		ticketOffice.plusAmount(ticket.getFee90);
        		audience.getBag().setTicket(ticket);
        	}
        }
        ...
        }
        
        ->
        public class Theater {
        ...
        	public void enter(Audience audience){
        		ticketSeller.sellTo(audience);
        	}
        ...
        }
        
        ```
        
- 기존 : bag을 getter로 불러옴
- 변경 : bag을 private화 + getter삭제 + 공용 인터페이스인 buy()를 bag에 선언
    
    ```java
    public class Audience{
    	public Bag bag;
    	->
    	private Bag bag;
    
    	public Bag getBag() {
    		return bag;
    	}
    	->
    	public Long buy(Ticket ticket){
    		if( bag.hasInvitation()) {
    		bag.setTicket(ticket);
    		return 0L;
    	} else {
    		bag.setTicket(ticket);
    		bag.minusAmount(ticket.getFee());
    		return ticket.getFee();
    	}
    }
    ```
    
- 변화점 : 각 객체들이 맡은 책임에 맞게 일을 행하도록 구현되었다.
- Theater의 경우를 예로 들었을 때 객체는 다른 객체에게 이 일을 행해달라고 전달했을 뿐이다.

### 캡슐화와 응집도

- 객체지향의 핵심은 객체 내부의 상태를 캡슐화하고 객체 간에 오직 공용 인터페이스(메시지)를 통해서만 상호작용하도록 만드는 것
- 객체가 밀접하게 연관된 작업만을 수행하고 연관성이 없는 작업은 다른 객체에게 위임하는 것이 응집도를 높이는 방법이다.
- 자신의 데이터를 책임지며 자신의 데이터를 가공하는 것이 객체의 응집도를 높이는 핵심

### 절차지향과 객체지향

- **절차적 프로그래밍**
Step01에서는 Theater가 절차지향적인 코드였으며, Process역할을 했었다. 이렇게 Step01의 Theater처럼 별도 모듈에 위치시키는 방식
- **객체지향 프로그래밍**
Step02에서는 프로세스가 각 객체 안에서 동작하도록 구성했다. `ex)class Audience`
- **책임의 이동(shift of responsibility)**
두 방식의 근본적인 차이는 기능을 처리할 때 Theater가 직접 처리를 하는 방식과, Theater가 말을하면 Audience와 TicketSeller가 스스로 처리를 하는 방식으로 구분을 할 수 있다.
    - 책임 집중 : Theater
    - 책임 분산 : buy → Audience, SellTo → TicketSeller

## Step03. 추가 개선

### TicketOffice

- 기존 : getTicket, getFee를 통해 티켓 자체를 Office에서 꺼내옴
- 변경 : sellTicketTo를 통해 기존의 티켓을 꺼내오는 방식 변경
    
    ```java
    class TicketOffice{
    ...
    	public Ticket getTicket(){
    		...
    	}
    
    	public void plusAmount(){
    		...
    	}
    ...
    }
    
    ->
    class TicketOffice{
    ...
    	public void sellTicketTo(Audience audience){
    		plusAmount(audience.buy(getTicket()));
    	}
    
    	private Ticket getTicket(){
    		...
    	}
    
    	private void plusAmount(){
    		...
    	}
    ...
    }
    
    public TicketOffice getTicketOffice() {
            return ticketOffice;
        }
    
    ->
    
    public class TicketSeller{
    	public void sellTo(Audience audience){
    		ticketOffice.sellTicketTo(audience);
    	}
    }
    ```
    
- 인터페이스에만 의존하게하는 형태로 변경됨
- `but` TicketSeller는 audience를 넘겨주며 서로 의존하게 만듦

### 트레이드오프

- 인터페이스에만 의존하는 Audience결합도가 중요한지, audience를 넘겨주며 의존성이 생겨난 것이 더 좋을지 개발자는 트레이드 오프를 해야 할 순간이 온다.
- 두가지 이슈를 통해 (책에서의)개발팀은 TicketOffice의 자율성을 지켜 의존성 제거를 택했다.

### 의인화

Theater, Bag, TicketOffice는 실세계에서 자율적인 존재가 아니다. 하지만 객체지향 패러다임에서는 생물과 동일하게 객체로 다뤘다. 이렇게 모든 객체는 능동적이고 자율적인 존재로 바뀌도록 설계하는 원칙을 **의인화**라고 부른다

## 설계

> 설계란 코드를 배치하는 것이다 - Metz12 -
> 
- 요구사항은 항상 변경된다. → 코드는 항상 변경된다.
- 설계는 코드를 잘 배치하는 것 → 변경될 때 코드의 변경을 최소화하는 것
- 코드의 변경을 최소화하려면 → 객체 사이의 의존성을 적절하게 관리하는 것
