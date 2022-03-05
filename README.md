# -Chapter7-object-oriented-1
## 1 전략 패턴
### 전략
 * 전략이란 무엇일까? 어떤 목적을 달성하기 위해서 일을 수행하는 방식, 비지니스 규칙, 문제를 해결하는 알고리즘
 * 프로그램에서 전략을 실행할 때는 쉽게 전략을 바꿔야할 필요가 있는 경우가 많이 발생한다. 
 * 특히 게임 프로그래밍에서 게임 케릭터가 자신이 처한 상황에 따라 공격이나 행동방식을 바꾸고 싶을 때 전략 패턴은 매우 유용


### 전략 패턴
![image](https://user-images.githubusercontent.com/58967292/156896365-08a8854a-3378-4c92-86fb-f512defc1a63.png)
* Strategy : 인터페이스나 추상 클래스로 외부에서 동일한 방식으로 알고리즘을 호출하는 방법을 명시한다.
* ConcreteStrategy1, ConcreteStrategy2, ConcreteStrategy3 : 전략 패턴에서 명시한 알고리즘을 실제로 구현한 클래스이다.
* Context : 전략 패턴을 이용하는 역할을 수행한다. 필요에 따라 동적으로 구체적인 전략을 바꿀수 있도록 setter()메서드를 제공한다
* 전략 제공자(client) : 전략 사용자에게 실제 전략으로 사용할 전략 콘크리트 클래스를 주입하는 역할

=> 클라이언트가 원하는 전략 객체를 생성하고, 이를 Context 객체에 바인딩하면 Context 객체는 바인딩된 전략 객체의 타입에 따라 적절한 행위를 실행한다.

구체적인 방법? 
* 인터페이스와 해당 인터페이스를 구현한 클래스를 사용하는 것
* 인터페이스를 통해 전략을 추상화 시켜놓은 후, 적재 적소에 필요한 전략을 구현한 Class를 삽입


### 전략 패턴 예시
```
public interface ShuffleStrategy {
    List<Card> shuffle(final List<Card> cards);
}
    
public class RandomShuffleStrategy implements ShuffleStrategy {
    @Override
    public List<Card> shuffle(final List<Card> cards) {
        Collections.shuffle(cards);
        return cards;
    }
}
```
* 전략(strategy) 과 전략 콘크리트(concrete strategy) 
* ShuffleStrategy라는 인터페이스를 만들어 Shuffle 이라는 전략을 추상화 
* 해당 인터페이스를 구현한 전략 콘크리트 클래스는, 각자 의도에 맞는 Shuffle 전략을 작성

```
public static CardDeck make(final ShuffleStrategy shuffleStrategy) {
    final List<Card> shuffledCard = shuffleStrategy.shuffle(cards);
    return new CardDeck(shuffledCard);
}
```
* 전략 사용자(context) 
*  전략을 사용하는 프로그램의 흐름으로, 서로 다른 전략에 따른 코드의 변경이 필요 X
*  context에서는 넘겨 받은 전략 콘크리트의 shuffle 메서드를 통해 cards를 shuffle 
* ShuffleStrategy를 구현한 클래스는 shuffle 이라는 전략을 가지고 있다는 것을 보장
* cards를 섞는 일은 전략 콘크리트 객체에게 위임

```
public void run() {
    final CardDeck cardDeck = CardDeckFactory.make(new RandomShuffleStrategy());
}
```
* 전략 제공자(client) 
* 전략 사용자에게 구체적인 전략 콘크리트 클래스를 주입

### 전략 패턴 장단점

[장점]
  - 전략 사용자(context)의 코드 변경 없이 새로운 전략을 추가 할 수 있다.
     - 이를 통해 if - else 분기를 제거할 수 있다.
     - if - else 분기를 제거하면, 단일 책임 원칙을 준수하기 더 수월해진다. 

  - 확장에 유리한 코드를 작성할 수 있다. 
      - 새롭게 필요한 전략 콘크리트 클래스를 쉽게 만들 수 있다. 
      - 개방 폐쇄 원칙을 준수한 코드 작성이 가능하다. 

  - 런타임에 전략을 변경시킬 수 있다. 

[단점]

  - 어플리케이션에 들어가는 모든 전략을 알고 있어야 한다. 
      - 클래스로 분리한 각 전략들이 어느 상황에 사용되어야 할 지 알고 있어야 한다.
      - 이 같은 특성이 어쩌면 유지보수를 더 힘들게 할 수도 있다. 

  - 전략을 추상화한 인터페이스가 효율적이지 못할 수 있다. 
      - 어떤 전략 콘크리트 객체에서는 사용하지 않는 메서드들 역시 전략 인터페이스에 정의해 주어야 한다. 


## 템플릿 메서드 패턴
### 템플릿 메서드 패턴?
* 템플릿 메서드 패턴(Template Method Pattern)은 전체적으로 동일하면서 부분적으로 다른 구문으로 구성된 메서드의 중복을 최소화할 때 유용한 패턴이다. 
* 동일한 기능을 상위 클래스에서 정의하면서 확장/변화가 필요한 부분만 서브 클래스에서 구현할 수 있도록 한다.

![image](https://user-images.githubusercontent.com/58967292/156896804-8d22681a-73ee-4773-af53-3183eee34ed4.png)
* AbstractClass : 템플릿 메서드를 정의하는 클래스
  * 하위 클래스에서 공통 알고리즘을 정의하고, 하위 클래스에서 구현될 기능을 primitive 또는 hook 메서드로 정의하는 클래스다.
* ConcreteClass : 물려받은 primitive 또는 hook 메서드를 구현하는 클래스
  * 상위 클래스에 구현된 템플릿 메서드의 일반적인 알고리즘에서 하위 클래스에 적합하게 primitive 또는 hook 메서드를 오버라이드하는 클래스다.

![image](https://user-images.githubusercontent.com/58967292/156896957-cad250b9-d0b8-4d12-b546-2d1643fb706d.png)
* Client는 ConcreteClass객체의 templateMethod()를 호출한다.
*  실제로 templateMethod()는 AbstractClass에서 정의되었지만 ConcreteClass는 AbstractClass의 하위 클래스이기 때문에 Client가 호출할 수 있다. 
*  AbstractClass :: templateMethod()에서는 primitiveOperation1()과 primitiveOperation2()를 호출한다. 
*  이 2개의 메서드는 ConcreteClass에서 오버라이드 된 것이다.


### 탬플릿 메서드 패턴 예시 
```
public abstract class Ramen {

    public void makeRamen() {
        boilWater();
        putNoodles();
        putExtra();
        waitForMinutes();
    }

    public void boilWater() {
        System.out.println("물을 끓인다.");
    }

    public void putNoodles() {
        System.out.println("면을 넣는다.");
    }

    public abstract void putExtra();

    public abstract void waitForMinutes();
}
```
```
public class ShinRamen extends Ramen {

    @Override
    public void putExtra() {
        System.out.println("계란을 넣는다.");
    }

    @Override
    public void waitForMinutes() {
        System.out.println("4분 기다린다.");
    }
}
```

### 템플릿 메서드 패턴 장단점
템플릿 메소드 패턴 장단점
[장점]
- 중복코드를 줄일 수 있다.
- 자식 클래스의 역할을 줄여 핵심 로직의 관리가 용이하다.
-  좀더 코드를 객체지향적으로 구성할 수 있다.

단점
- 추상 메소드가 많아지면서 클래스 관리가 복잡해진다.
- 클래스간의 관계와 코드가 꼬여버릴 염려가 있다.


## 상태 패턴
### 상태 패턴이란 무엇인가 
* 실세계의 많은 개체들은 자신이 처한 상황에 따라 일을 다르게 수행한다. 예를 들어 비가 오거나 눈이 오거나 사람이 붐비는 장소에 있거나에 따라 걷는 방식과 말하는 방식이 달라지는 것과 같다.
* 이를 표현하는 가장 직접적이고, 직관적인 방법은 일을 수행할 때 상태에 따라 상태 하나하나를 검사해 일을 다르게 수행하게 끔 하는 것이다. 하지만 이는 분명 복잡한 조건식이 있는 코드를 산출할 것이고, 결과적으로 코드를 이해하거나 수정하기 어렵게 만들 것이다.
* 위의 방식과 달리 스테이트 패턴은 어떤 행위를 수행할 때 상태에 행위를 수행하도록 위임한다. 이를 위해 스테이트 패턴에서는 시스템의 각 상태를 클래스로 분리해 표현하고, 각 클래스에서 수행하는 행위들을 메서드로 구현한다. 그리고 이러한 상태들을 외부로부터 캡슐화하기 위해 인터페이스를 만들어 시스템의 각 상태를 나타내는 클래스로 하여금 실체화하게 한다.

![image](https://user-images.githubusercontent.com/58967292/156897211-16221333-fc10-4d1f-a271-fb93f6a46a64.png)
* State : 시스템의 모든 상태에 공통 인터페이스를 제공한다. 이 인터페이스를 실체화한 어떤 상태 클래스도 기존 상태 클래스를 대신해 교체해서 사용할 수 있다.
* State1, State2, State3 : Context 객체가 요청한 작업을 자신의 방식으로 실제 실행한다. 다음 상태를 결정해 상태 변경을 Context 객체에 요청하는 역할도 수행한다.
* Context : State를 이용하는 역할을 수행한다. 현재 시스템의 상태를 나타내는 상태 변수(state)와 실제 시스템의 상태를 구성하는 여러가지 변수가 있다.
* 또한 각 상태 클래스에서 상태 변경을 요청해 상태를 바꿀 수 있도록하는 메서드(setState())가 제공된다. 
* Client : 상태 보유자에게 특정 기능 수행을 요청


### 상태 패턴 예시
```
public class Laptop {
    private LaptopState laptopState;

    public Laptop() {
        this.laptopState = new Off();
    }

    public void pushPower() {
        laptopState = laptopState.pushPower();
    }

    public void startCoding() {
        laptopState.startCoding();
    }
}
```
* 상태 보유자 (context) 
* Laptop 클래스는 LaptopState를 인스턴스 변수로 가지고 있다
* 처음 Laptop 객체를 생성하면, LaptopState는 Off 객체로 초기화
* Laptop 객체가 pushPower()와 startCoding() 기능 수행 요청을 받으면, LaptopState에게 이를 위임

```
public interface LaptopState {
    LaptopState pushPower();
    void startCoding();
}


public class On implements LaptopState {
    @Override
    public LaptopState pushPower() {
        return new Off();
    }

    @Override
    public void startCoding() {
        System.out.println("재미난 코딩!");
    }
}


public class Off implements LaptopState {
    @Override
    public LaptopState pushPower() {
        return new On();
    }

    @Override
    public void startCoding() {
        System.out.println("먼저 전원을 켜주세요!");
    }
}
```
* 상태 (state) 와 상태 콘크리트 (state concrete)
* 위에서 언급했던 것처럼, LaptopState라는 인터페이스를 만들어 Laptop이 제공해야 할 기능을 추상화 
* 해당 인터페이스를 구현한 상태 콘크리트 클래스는 각각 Laptop 상태에 알맞은 기능을 구현


### 상태 패턴 장단점
[장점]
  - 관련된 코드가 한 곳에 모여있어 안전하고 빠른 구현이 가능하다.
      - 각각의 상태에 대해 유지보수가 용이하다.
      - 상태마다 달라지는 validation 등이 쉬워진다.

  - 상태가 많아지더라도 코드의 복잡도가 증가하지 않는다. 
      - 이를 통해 if - else 분기를 제거할 수 있다. 
      - if - else 분기 제거를 통해, 단일 책임 원칙을 준수하기 더 수월해진다. 

[단점]
  - 상태 보유자가 가질 수 있는 모든 상태에 대해 알아야 한다. 
      - 상태의 구조가 한 눈에 파악이 어렵다면, 오히려 유지보수가 어려워질 수도 있다. 

  - 작성해야 할 코드의 양이 많다. 
      - 추상화 된 모든 메서드를 구현해 줘야 하기 때문에 코드의 양이 많아진다. 
