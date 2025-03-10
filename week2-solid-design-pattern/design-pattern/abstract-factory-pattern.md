# Abstract Factory Pattern
- 추상 팩토리 패턴은 연관성이 있는 객체 군이 여러개 있을 경우 이들을 묶어 추상화하고, 필요할 때 이 객체 군을 생성하게 만들어주는 생성 패턴이다.
- 클라이언트에서 특정 객체을 사용할때 이 팩토리 클래스만 참조하고, 특정 객체에 대한 구현부를 감추어 역할과 구현을 분리시킬 수 있다.
- 즉, 추상 팩토리의 핵심은 제품 '군' 집합을 타입 별로 찍어낼수 있다는 점이 핵심이다.
    - ex. 모니터, 마우스, 키보드를 묶은 전자 제품군이 있고, 이런 제품 군이 브랜드 등의 정보도 표현하며 여러 갈래로 나뉘게 되는 상황
    - 복잡하게 묶이는 제품 군들의 관리와 확장을 용이하게 패턴화 한 것이 추상 팩토리이다.


---
# 추상 팩토리 패턴 사용 예시
## 상황
- 비행기를 조정하고 미사일을 발사해서 적을 미사일로 쏴 맞춰 잡는 슈팅 게임을 만든다고 가정해 보자.
- 이런 게임은 적 정보, 장애물 정보 등 State가 각 게임의 단계별로 다르기 때문에, 단계 별로 다른 시기에 장애물이나 보스를 생성해야 한다.
- 그러나, 해당 생성 정보나 규칙을 매 단계별로 구현하기에 어려움이 있다.

## 문제
- 해당 상황을 무작정 구현하려고 하면, 단계 별로 적기, 보스, 장애물을 생성하는 규칙을 계속 수정해야 한다.
- 예를 들어, 새로운 적 클래스가 추가되거나 각 단계의 보스 종류가 바뀔 때 코드를 일일히 수정해 주어야 한다.
- 또한, 중첩되거나 연속된 조건문으로 인해 코드가 복잡해지고 가독성이 떨어질 수 있다.


## 해결 방법
- 이를 해결하기 위해 Stage 클래스로부터 객체 생성 책임을(적, 장애물 등의 객체 생성) 분리함으로써 이 문제를 해소할 수 있다.
- 추상 팩토리 패턴을 사용해 관련된 객체 군마다 타입을 분리하고, 객체 생성 책임을 준다.


```java
public abstract class EnemyFactory {
    public static EnemyFactory getFactory(int level) {
        if(level == 1) return EasyStageEnemyFactory();
        else return HardEnemyFactory();
    }

    // 객체 생성을 위한 팩토리 메서드
    // 여러 개의 팩토리 메서드를 포함하여, 서로 관련된 객체군 생성
    public abstract Boss createBoss();
    public abstract SmallFlight createSmallFlight();
    public abstract Obstacle createObstacle();
}

public class EasyStageEnemyFactory extends EnemyFactory {

    @Override
    public Boss createBoss() {
        return new StrongAttackBoss();
    }

    @Override
    public SmallFlight createSmallFlight() {
        return new DashSmallFlight;
    }

    @Override
    public Obstacle createObstacle() {
        return new RockObstracle();
    }
}
```

## Abstract Factory vs Factory Method 비교
- 팩토리 메서드 패턴(Factory Method Pattern)과 추상 팩토리 패턴(Abstract Factory Pattern)은 모두 객체 생성의 캡슐화를 목표로 하나, 적용되는 범위와 목적이 다름
- 팩토리 메서드 패턴: 단일 객체를 생성하는 데 초점
- 추상 팩토리 패턴: 연관된 여러 객체 군을 생성하는 것이 목적

### 공통점
- 객체 생성 과정을 캡슐화하여 구체적인 타입을 감춘다.
- 느슨한 결합 구조를 제공하여 유지보수성을 높인다.
- 팩토리 객체를 통해 객체 생성을 관리한다.


### 차이점
| **구분** | **Factory Method Pattern** | **Abstract Factory Pattern** |
|---------|--------------------|---------------------|
| **목적** | **객체 생성 과정을 하위(구체적인) 클래스로 옮김** | **관련 있는 여러 객체(Product Family)를 생성** |
| **적용 범위** | **단일 객체** 생성에 집중 | **연관된 객체(제품군) 전체를 생성** |
| **구성 요소** | - Creator (인터페이스/추상 클래스) <br> - Concrete Creator (구체적 팩토리) <br> - Product (인터페이스/추상 클래스) <br> - Concrete Product (구체적 객체) | - Abstract Factory (인터페이스/추상 클래스) <br> - Concrete Factory (구체적 팩토리) <br> - 여러 개의 Product 인터페이스 <br> - 여러 개의 Concrete Product |
| **팩토리 역할** | **하나의 Factory 클래스가 한 종류의 객체만 생성** | **하나의 Factory에서 여러 개의 관련 객체를 생성** |
| **포커스** | **메서드 레벨에서 객체 생성을 추상화** | **클래스(팩토리) 레벨에서 객체 생성을 추상화** |


---

ref.
- [abstract-factory-pattern-1](https://incheol-jung.gitbook.io/docs/study/undefined/undefined-2/undefined-8)
- [abstract-factory-pattern-2](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%B6%94%EC%83%81-%ED%8C%A9%ED%86%A0%EB%A6%ACAbstract-Factory-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90)