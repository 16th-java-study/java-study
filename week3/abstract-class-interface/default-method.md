# 디폴트 메서드(Default Method)
- 인터페이스를 구현하는 클래스는, 인터페이스에서 정의하는 모든 메서드를 구현하거나 부모 클래스의 구현을 상속받아야 한다. 
- 때문에 인터페이스를 바꿔야 할 일이 생기면, 이전에 해당 인터페이스를 구현했던 모든 클래스도 고쳐야 하는 문제가 발생한다.
- Java 8 부터 해당 문제를 해결하기 위해 기본 구현을 포함하는 인터페이스를 정의하는 두 가지 방법을 제공한다. 

1. 인터페이스 내부의 **정적 메서드**를 사용하는 것
2. 인터페이스의 기본 구현을 제공할 수 있도록 **디폴트 메서드** 기능을 사용하는 것


## 디폴트 메서드는 무엇일까?
- 호환성을 유지하면서 API를 바꿀 수 있도록 해주는 기능
- 이름 그대로 인터페이스 자체에서 구현한다.
- 인터페이스를 구현하는 모든 클래스는 디폴트 메서드의 구현도 상속받는다.
  - 즉, 호환성을 유지하며 코드를 수정할 수 있다.


```java
public interface ExampleInterface {
   // 추상 메서드 
    void abstractMethodA();
    void abstractMethodB();

	// default 메서드
    default int defaultMethodA(){
    	// 구현부
    }
}
```

- Default Method가 등장하면서 위에서 다뤘던 문제점을 해결할 수 있다.
- 문제점 : 인터페이스에 추상메서드를 추가하게 되면 모든 구현체에 추가로 구현을 해야한다.
- 해결 방안 : 인터페이스에 Default Method를 사용하면 추가 변경을 막을 수 있다.
- Default Method 사용을 통해 OCP 원칙을 지킬 수 있다. (확장에 개방되어 있고, 변경에 닫혀있음)

## Default Method 간 충돌
- Default Method를 사용하면 크게 2가지 충돌 상황이 발생할 수 있다.

1. 여러 인터페이스의 디폴트 메서드 간의 충돌
2. 디폴트 메서드와 상위 클래스의 메서드 간의 충돌

## 충돌 시, 세 가지 해결 규칙
1. **클래스가 항상 이긴다**. 
- 클래스나 슈퍼클래스에서 정의한 메서드가 디폴트 메서드보다 우선권을 갖는다.
2. 1번 규칙 외의 상황에서는 **서브 인터페이스가 이긴다**. 
- 상속관계를 갖는 인터페이스에서 같은 시그니처를 갖는 메서드를 정의할 때, 서브 인터페이스가 이긴다. 
- 서브 인터페이스(다른 인터페이스를 상속받은 인터페이스) == B가 A를 상속받는다면, B가 A를 이긴다.
3. 여전히 디폴트 메서드의 우선순위가 결정되지 않았다면 여러 인터페이스를 상속받는 **클래스가 명시적으로 디폴트 메서드를 오버라이드하고 호출**해야 한다.


```java
interface A {
    default void hello() {
        System.out.println("Hello from A");
    }
}

interface B extends A { // B가 A를 상속
    default void hello() {
        System.out.println("Hello from B");
    }
}

class MyClass implements A, B {  // A, B 동시에 구현
    // 규칙 2에 의해 B의 hello()가 우선
}

public class Main {
    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.hello(); // "Hello from B"
    }
}

```


## 인터페이스 다중 구현 시 생길 수 있는 다이아몬드 문제
```java
public interface A {
	default void hello() {
		System.out.println("Hello from A");
	}
}
public interface B extends A { }
public interface C extends A { }
public class D implements B, C {
	public static void main(String... args) {
		new D().hello(); //무엇이 출력될까?
	}
}
```


UML 다이어그램이 다이아몬드 형태인 시나리오를 다이아몬드 문제라고 부른다.


1. A만 디폴트 메서드를 정의하고 있는 상황 ⇒ `Hello from A`
2. B에서도 hello를 정의한다면? ⇒ `Hello from B`
3. B와 C에서 모두 hello를 정의한다면? ⇒ `충돌! 명시적으로 호출해야 함`


---
ref.
- [modern-java-in-action-chap-13](https://xrabcde.github.io/modernjava-ch13/)