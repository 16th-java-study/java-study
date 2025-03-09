# 추상 타입
***
## 추상화란?
데이터나 프로세스 등을 의미가 비슷한 개념이나 표현으로 정의하는 과정.<br>
=> 타입도 추상화의 대상이 된다.<br>
추상화된 타입은 오퍼레이션의 시그니처만 정의할 뿐 실제 구현을 제공하는 것은 아니다.(인터페이스를 생각하면 된다.)<br>

## 추상 타입
클래스에 추상화를 접목시켜 보다 구조적이게 객체를 설계하고, 프로그램의 유지보수성을 올려주기 위한 것이다.

***

## 추상 클래스
* 하나 이상의 프로퍼티나 함수가 불완전한 클래스
* 참조가 속한 클래스에도 abstract 변경자를 붙여야 한다.
* 직접 인스턴스화가 불가능
* 추상 멤버는 반드시 override

~~~java
abstract public void test(); // => 추상메소드는 구현부가 없으며 중괄호를 포함하지 않는다.
~~~

### 추상 클래스 vs 인터페이스
공통점
* 구현이 있는 함수를 포함할 수 있다.
* 인터페이스 혹은 추상 클래스를 상속받아 구현한 구현체를 통해 인스턴스화를 할 수 있다.
* 인터페이스 혹은 추상 클래스를 상속한 클래스는 추상 멤버를 반드시 구현해야 한다.

차이점
* 추상 클래스는 상태를 가질 수 있지만 인터페이스는 상태를 가질 수 없다.
* 추상 클래스는 생성자를 가질 수 있다.
* 추상 클래스는 접근제어자의 제한이 없다.
* 추상 클래스는 다중 상속이 불가능하다.
  * 추상 클래스는 하위 관계의 클래스는 추상 클래스에 의해 제약이 걸린다.

### 추상 클래스의 역할
기능 및 구현등의 공통 부분을 묶기 위한 역할.<br>
추상 메소드가 포함된 클래스를 상속받는 자식 클래스가 반드시 추상 메소드를 구현하도록 하기 위함이다.<br>
=> 구현을 안하면 인스턴스를 못만듬.

### 예시 코드
~~~java
import java.sql.SQLOutput;

abstract class Vehicle {
    protected String name;

    public Vehicle(String name) {
        this.name = name;
    }

    public abstract void move(); // 모든 차량은 이동해야함. 구현은 알아서

    // 모든 차량은 멈춤. 방식도 동일하게.
    public void stop() {
        System.out.println("stop");
    }
}

class Car extends Vehicle {
    public Car(String name) {
        super(name);
    }

    @Override
    public void move() {
        System.out.println("move car");
    }
}

class Bike extends Vehicle {
    public Bike(String name) {
        super(name);
    }

    @Override
    public void move() {
        System.out.println("move bike");
    }
}

public class test {
    public static void main(String[] args) {
        Vehicle myCar = new Car("Tesla");
        Vehicle myBike = new Bike("Mountain Bike");
    }
}

~~~

### ※ 참고자료
* 개발자가 반드시 정복해야 할 객체 지향과 디자인 패턴, 최범균 저
* https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4Abstract-%EC%9A%A9%EB%8F%84-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0#%EC%B6%94%EC%83%81_%ED%81%B4%EB%9E%98%EC%8A%A4_%EC%99%80_%EC%B6%94%EC%83%81%ED%99%94