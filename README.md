# 주요 디자인 패턴 (1)

## 디자인 패턴이란

- 객체 지향 설계는 변화에 유연하게 대응하기 위해 특정 상황에 맞는 패턴을 이용하게 되는데 이를 디자인 패턴이라고 한다. 여기서 특정 상황이란 클래스, 객체의 구성, 객체 간 메시지 흐름에서 알맞는 설계를 하는 것이다.

- 디자인 패턴을 배우면서 느낄 수 있는 장점은 아래와 같다.
    1. 상황에 맞는 설계를 빠르게 적용시킬 수 있다.
    2. 각 패턴의 장단점을 통해 어떻게 설계할 것인지에 대해 도움을 받을 수 있다.
    3. 설계한 패턴에 이름을 붙임으로써 문서화, 이해, 유지보수에 도움을 얻을 수 있다.

### 전략 패턴 (Strategy Pattern)

- 전략 패턴은 전략(Strategy)과 컨텍스트(Context)라는 이름으로 나뉘어지는데, 여기서 전략이란 어떤 기능을 수행하는데 필요한 알고리즘을 직접 구현한 객체가 되며 콘텍스트는 이를 사용하는 책임을 갖고 있는 것을 말한다.

- 따라서 특정 컨텍스트에서 어떤 기능을 수행하는데 필요한 알고리즘을 따로 분리해내 컨텍스트에서는 어떤 알고리즘을 사용하는지 알 필요가 없다.

- 전략 패턴을 사용했을때와 사용하지 않았을 때의 코드를 비교해자면 아래와 같다.

    1. **사용하지 않았을 때**

    ```kotlin
    data class Item(val price: Int)

    class Coupon {
        fun calculatePrice(item: Item, isFirst: Boolean): Int {
            if (isFirst) {
                return item.price - (item.price * 0.8).toInt()
            } else {
                return item.price - (item.price * 0.5).toInt()
            }
        }
    }
    ```
    
        - 첫 번째 주문일 경우 80퍼센트의 할인을 해주고 그렇지 않다면 50퍼센트를 할인해주는 코드이다.
        - 만약 첫 번째 주문이 아닌 오랜만에 주문했을 경우의 코드를 추가한다고 했을 때, if ~ else 구문과 메소드의 매개변수들은 점점 늘어나게 되고 관리가 힘들어지게 된다.

    2. **사용했을 때**

    ```kotlin
    interface DiscountStrategy {
        val ratio: Double
        fun getDiscountPrice(price: Int): Int
    }

    class FirstOrderDiscount : DiscountStrategy {
        override val ratio: Double = 0.8
        override fun getDiscountPrice(price: Int): Int {
            return price - (price * ratio).toInt()
        }
    }

    class OrderDiscount : DiscountStrategy {
        override val ratio: Double = 0.5
        override fun getDiscountPrice(price: Int): Int {
            return price - (price * ratio).toInt()
        }
    }

    data class Item(val price: Int)

    class Coupon(private val discountStrategy: DiscountStrategy) {
        fun calculatePrice(item: Item): Int {
            return discountStrategy.getDiscountPrice(item.price)
        }
    }
    ```

        - 사용하지 않았을 때와 동일한 값을 반환하지만 새로운 할인 전략이 발생했을 때 `DiscountStrategy`를 상속받는 클래스를 이용해 정의하고 사용할 수 있게 된다.
        - 이렇게 되면 객체 지향 설계 중 하나인 `OCP원칙`을 지킬 수 있게 된다.

---

### 템플릿 메서드 패턴 (Template Method)

- 프로그램을 구현하다 보면, 완전히 동일한 절차를 가진 코드를 작성하게 될 수 있다. 즉, 일부 과정의 구현만 다를 뿐 나머지 구현은 완전히 같을 수 있는데, 그 예는 아래와 같다.

    ```kotlin
    class DbAuthenticator {
        fun authenticate(id: String, pw: String): Auth {
            val user = userDao.selectById(id)
            auth = user.equalPassword(pw)
            if (!auth) {
                throw Exception()
            }
            return new Auth(id, user.getName());
        }
    }

    class LdapAuthenticator {
        fun authenticate(id: String, pw: String): Auth {
            auth = ldapClient.authenticate(id, pw)
            if (!aut) {
                throw Exception()
            }
            ctx = ldapClient.find(id)
            return new Auth(id, ctx.getAttribute("name))
        }
    }
    ```

- 위 코드는 단순히 DB와 LDAP를 이용해 인증하는 구조인데, 몇 부분을 제외하고는 완전히 동일한 형태를 띄게 된다.
- 지금은 DB, LDAP만 있지만 또다른 인증 방법이 나오게 된다면 코드의 중복이 많이 발생하게 됨으로써 유지보수가 어려워지게 된다.
    - 코드의 중복이 많아지면 로직 중 하나를 수정할 때 모든 관련된 코드를 수정해야하는 단점이 존재한다.
- 템플릿 패턴은 이러한 코드의 중복을 줄여주도록 아래와 같이 구성된다.
    - 실행 과정을 구현한 상위 클래스
    - 실행 과정의 일부 단계를 구현한 하위 클래스

- 상위 클래스는 추상 메서드를 이용해 호출하며 하위 클래스에서는 이러한 추상 메서드를 직접 구현함으로써 코드의 중복을 줄일 수 있다. 그 예는 아래와 같다.

    ```kotlin
    abstract class Authenticator {
        fun authenticate(id: String, pw: String) {
            if (!doAuthenticate(id, pw)) {
                throw Exception()
            }

            return createAuth(id)
        }

        protected abstract fun doAuthenticate(id: String, pw: String): Boolean
        protected abstract fun createAuth(id: String): Auth
    }

    class DbAuthenticator : Authenticator() {
        override fun doAuthenticate(id: String, pw: String): Boolean {
            ...

            return true
        }

        override fun createAuth(id: String): Auth {
            ...

            return Auth(id)
        }
    }
    ```

    - 위와 같이 구현하게 되면 `Authenticator`를 상속받는 클래스에서는 단순히 상위 클래스의 추상 메서드들을 재정의해면 된다.

- 일반적으로 상속은 하위 클래스가 상위 클래스의 기능을 재사용할지 여부를 확인하지만 템플릿 메서드 패턴에서는 상위 클래스가 흐름을 제어하고 그 흐름에 하위 클래스가 끼어들어가게 되는 것이다.

- 또한, 템플릿 메서드 패턴에 훅(Hook) 메서드라는 것을 이용해 하위 클래스에서 확장할 수도 있는데 그 구조는 아래와 같다.

    ```kotlin
    abstract class Authenticator {
        fun authenticate(id: String, pw: String) {
            if (!doAuthenticate(id, pw)) {
                throw Exception()
            }
            doSomthing()

            return createAuth(id)
        }

        protected abstract fun doAuthenticate(id: String, pw: String): Boolean
        protected abstract fun createAuth(id: String): Auth
        protected fun doSomthing() {
            ...
        }
    }
    ```

    - 위 코드에서 `doSomething`메서드를 `protected`로 선언함으로써 인증이 끝나고 어떤 작업을 해야할 때 그 작업을 하위 클래스에서 확장하도록 할 수 있다.

---
