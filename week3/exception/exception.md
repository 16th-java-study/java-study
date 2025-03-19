# 예외 처리

## 예외 계층 구조

![img.png](../image/Jinsu/exception-inheritance-relation.png)

자바에서 예외는 에러(Error)와 예외(Exception)로 나뉜다. </br>

에러는 메모리 부족과 같이 시스템이 비정상적인 상황에 사용된다. 주로 JVM에서 발생하기 때문에 애플리케이션 코드에서 잡아서도 안되고 잡더라도 대응할 수 있는 방법도 없다. 때문에 애플리케이션 개발자가 신경쓸 부분은 딱히 없다.


## 예외의 종류
Exception은 체크 예외(Check Exception)와 언체크 예외(Uncheck Exception)으로 나뉜다. </br>


### 체크 예외 (Check Exception)
RuntimeException을 상속받지 않은 예외 클래스를 말한다. 체크 예외는 복구 가능성이 있는 예외로 반드시 예외를 잡아서 처리하거나 던져야 한다. 별도의 처리를 하지 않으면 컴파일 에러가 발생한다.

#### 장점
* 개발자가 실수로 예외 처리를 누락하는 것을 방지한다.
  * 예전에는 이 방식을 많이 사용했다고 하는데 현재는 언체크 예외를 훨씬 많이(대부분) 사용한다.

#### 단점
* 예외 처리를 개발자가 매번 다 해줘야 하고 신경쓰고 싶지 않은 예외까지 처리해야 한다.

### 언체크 예외
RuntimeException 클래스를 상속받는 클래스들로 복구 가능성이 없는 예외들이라 컴파일러가 예외 처리를 강제하지 않는다. </br>
Error와 마찬가지로 예외 처리를 하지 않아도 컴파일 에러가 발생하지 않는다.

#### 장점
* 신경쓰고 싶지 않은 언체크 예외를 다 무시할 수 있어서 편리하다.

#### 단점
* 컴파일러가 예외를 잡아주지 않기 때문에 개발자가 실수로 누락할 수 있는 단점이 있다.

* 스프링 프레임워크에서 제공하는 @Transactional안에서 에러 발생 시 체크 예외는 롤백되지 않고 언체크 예외는 롤백된다.
  * 설정 변경이 가능하지만 기본으로 어떻게 동작하는지 반드시 알아야 한다.


## 예외 처리 방법

### 예외 복구
문제를 정상 상태로 돌려놓는 것을 말한다. 특정 작업이 처리가 불가능하면 다른 방법으로 처리를 유도함으로서 예외를 처리한다. </br>
ex) API 호출 실패 시 일정 횟수 Retry 하여 복구를 시도할 수 있다.

### 예외 처리 회피
예외를 처리하지 않고 호출한 곳으로 예외를 던지는 방식이다. 무분별하게 예외를 던지면 안되고 상황에 따라 적절하게 처리해야 한다.

### 예외 전환
예외를 복구할 수 없을 때 사용하며 회피와는 다르게 예외를 변환하여 던진다.

* 의미 있는 예외로 바꿔서 던지기

ex1) </br>
발생 예외를 그대로 던지는 것이 적절한 의미 부여를 못할 때 의미 있는 예외로 바꿔서 던질 수 있다. </br>
예를 들어서 IOException이 발생했는데 이를 그대로 던지면 어떤 외부 콜을 호출할 때 예외가 발생한 것인지 파악하기 어려울 수 있다. </br>

ex2) </br>
Spring 애플리케이션을 사용할 때 다양한 DB를 설정해서 사용할 수 있다. 기존에 PostgreSQL을 사용하다가 MySQL로 전환할 경우 PostgreSQL의 에러를 MySQL 에러로 전환해야 한다. </br>

@Repository 에는 각기 다른 DB의 에러를 DataAccessException으로 전환해준다. 이를 통해 DB에 종속되지 않은 개발이 가능해진다.



* 체크 예외를 언체크 예외로 변환해서 던지기 (많이 사용함)

체크 예외로 인해 에러 처리가 많아지거나 복구하지 못하는 상황에 불필요하게 체크할 필요가 없다. </br>
이런 경우 언체크 예외로 변환해서 던지면 유용하다.

### 예외 처리 주의점
* 무분별하게 외부로 예외를 던지는 건 좋지 못하다.
  * 나를 호출하는 메소드가 있는 클래스에도 해당 예외에 대한 의존성이 생김.
  * 이유없이 책임을 다른 클래스로 전가하는 것은 부적절
* try-catch로 예외를 잡고 아무 처리도 하지 않으면 위험할 수 있다.
  * 의도가 있어서 아무것도 안하게 하는 것이 아니라면 반드시 피해야 한다.
  * 메로리 누수나 비정상 동작이 발생할 수 있다.

## Spring Transactional 예외 동작

1. Spring에서 트랜잭션 진행중에 예외가 발생하면 `rollbackOn`에서 언체크 예외인지 확인한다.
2. 언체크 예외이면 `rocessRollback` 부분에서 기본 설정으로 참여중인 트랜잭션을 rollback-only로 마킹한다.

이를 통해 일부 트랜잭션이 실패할 경우 전체 트랜잭션을 롤백한다.(마킹된거 롤백) </br>

### 문제

1. eatPizza()안에서 pizza를 호출하면 새로운 TX가 열리는지?
-> 열리지 않는다.

```kotlin
@Service
class PizzaService {
    fun eatPizza() {
        pizza()
    }

    @Transactional
    fun pizza() {
        // ...
    }
}
``` 

![img.png](../image/Jinsu/problem-1.png)

1번 코드의 경우 첫번째 이미지와 같이 동작한다. </br>
Transactional Spring AOP를 기반으로 동작한다. 내부 호출의 경우 프록시를 거치지 않고 직접 호출되기 때문에 TransactionalInterceptor가 동작하지 않는다. </br>
때문에 새로운 TX가 열리지 않는다. </br>

여기서 `pizza()`에 트랜잭션을 적용하고 싶으면 별도의 클래스로 분리해야 한다. (eatPizza()를 거치는 경우)

2. pizza() 호출할 때 RuntimeException이 발생하면 eatPizza()의 트랜잭션이 롤백되는지?
-> 롤백되지 않는다.

```kotlin
@Service
class PizzaService {
    @Transactional
    fun eatPizza() { // 롤백이 될까?
        try {
            pizza()
        } catch (e: Exception) {
            // error ignore
        }
    }

    @Transactional(propagation = Propagation.REQUIRES_NEW)
    fun pizza() {
        throw RuntimeException("RuntimeException") // Unchecked Exception 발생
    }
}
```

pizza에서 예외가 발생했지만 TransactionInterceptor가 동작하지 않았고 eatPizza에서는 예외를 try catch로 잡는다. </br>
트랜잭션 커밋 시점에 eatPizza의 TransactionInterceptor 동작에서 롤백 마크가 돼있는지 찾는데 트랜잭션 내부에서 예외가 발생하지 않고 예외를 처리했기 때문에 마킹된 롤백이 없다. </br>
예외가 발생한 pizza에서는 TransactionInterceptor 동작이 수행되지 않았고 TransactionInterceptor 동작이 수행되는 eatPizza에서는 예외를 모두 잡고 트랜잭션이 정상적으로 수행된다.

3. PickService의 eatPickle을 호출할 시 RuntimeException이 발생하면 eatPizza의 트랜잭션이 롤백되는지?
-> 롤백된다.

```kotlin
@Service
class PizzaService {
    @Transactional
    fun eatPizza() { // 롤백이 될까?
        pizza()
        try {
            pickleService.eatPickle()
        } catch (e: Exception) {
            // error ignore
        }
    }

    private fun pizza() {
        // ...
    }
}

@Service
class PickleService {
    @Transactional
    fun eatPickle() {
        throw RuntimeException("RuntimeException") // Unchecked Exception 발생
    }
}
```

다른 서비스(PickleService)에서 eatPickle()을 호출하는데 @Transacteional의 기본 전파 속성으로 REQUIRE로 동작한다.
* REQUIRE는 기존 트랜잭션이 없으면 생성, 있으면 참여

때문에 eatPickle() 은 기존 eatPizza() (부모) 트랜잭션에 참여하게 되고 RuntimeException이 발생하면서 롤백 마킹을 해버린다. </br>
-> eatPizza() 트랜잭션도 롤백된다.

4. PickleService의 eatPickle 호출 시 RuntimeException 발생하면 eatPizza 트랜잭션 롤백되는지?
-> 롤백되지 않는다.

```kotlin
@Service
class PizzaService {
    @Transactional
    fun eatPizza() { // 롤백이 될까?
        try {
            pickleService.eatPickle()
            pizza()
        } catch (e: Exception) {
            // error ignore
        }
        pizza()
    }

    private fun pizza() {
        // ...
    }
}

@Service
class PickleService {
    // 문제 3과 달리 @Transactional 생략
    fun eatPickle() {
        throw RuntimeException("RuntimeException") // Unchecked Exception 발생
    }
}
```

2번과 마찬가지로 eatPizza에서 예외를 catch해서 처리했기 때문에 롤백 마크가 찍히지 않는다. </br>

## 참고자료
* https://hyperconnect.github.io/2025/02/10/spring-transactional-rollback.html
* https://mangkyu.tistory.com/152\
* https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%97%90%EB%9F%ACError-%EC%99%80-%EC%98%88%EC%99%B8-%ED%81%B4%EB%9E%98%EC%8A%A4Exception-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC
