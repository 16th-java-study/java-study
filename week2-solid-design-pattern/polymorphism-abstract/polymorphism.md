# 다형성
***
## 다형성이란?
한 객체가 여러 가지 모습을 갖는 것.<br>
=> 즉 한 객체가 여러 가지 "타입"을 가질 수 있다는 것을 말한다.

## 어떻게 구현?
자바와 같은 정적 언어에서는 타입 상속을 통해서 다형성을 구현한다. (오버라이딩, 오버로딩...)

### 인터페이스 상속
순전히 타입 정의만을 상속받는 것.<br>
같은 타입(인터페이스)으로 여러 하위 타입(구현 클래스)들을 구현할 수 있다.

~~~java
interface Vehicle {
    void start();
    void stop();
}

interface Car extends Vehicle {
    void honk();
}

class Sedan implements Car {
    @Override
    public void start() {
    }

    @Override
    public void stop() {
    }
}

class Truck implements Car {
    @Override
    public void start() {
    }

    @Override
    public void stop() {
    }

    @Override
    public void honk() {
    }
}
~~~

### 구현 상속
클래스를 상속받는 것.
~~~java
public class Animal{
    public void bark(){
        ~;
    }
}

public class Dog extends animal(){
    public void bark(){
        ~; //=> 새로운 내용 구현 = 오버라이딩
    }
}

Animal dog = new Dog();
dog.bark();
~~~
위 코드에서 dog는 Animal의 메소드를 호출할 것 같지만, 실제 타입인 Dog의 메소드를 호출한다.

***
## 다형성이 가지는 의미
다형성을 이용한 타입 묶음을 통해 개발을 용이하게 할 수 있다.
~~~java
interface Animal {
    void makeSound();
}

class Dog implements Animal {
    @Override
    public void makeSound() {
    }
}

class Cat implements Animal {
    @Override
    public void makeSound() {
    }
}

class Cow implements Animal {
    @Override
    public void makeSound() {
    }
}

public class TypeGroupingExample {
    public static void main(String[] args) {
        Animal[] animals = {new Dog(), new Cat(), new Cow()};
    }
}
~~~
위 코드와 같이 부모클래스를 자식클래스로 할당해 생성한 후 이것을 같은 타입의 형식으로 처리할 수 있다.<br>
=> 이게 안되면 각 타입별 배열을 새로 만들고 각각 처리해야 할 것이다.

### ※ 참고자료
* 개발자가 반드시 정복해야 할 객체 지향과 디자인 패턴, 최범균 저
* https://inpa.tistory.com/entry/OOP-JAVA%EC%9D%98-%EB%8B%A4%ED%98%95%EC%84%B1Polymorphism-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4#%EC%9E%90%EB%A3%8C%ED%98%95_%EB%8B%A4%ED%98%95%EC%84%B1