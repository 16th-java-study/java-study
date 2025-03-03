# 제네릭 컴파일 타입 소거 (Erasure)
- 이전 자바 버전의 코드 호환성을 위해, 제네릭 타입은 컴파일 시 사라진다.

  → 클래스 파일 `.class` 에는 제네릭 타입에 대한 정보가 존재하지 않음


  → 컴파일 타임에만 타입 제약 조건을 정의하고, 런타임에는 타입을 제거하기 때문에, 잘못 설계하면 힙 오염 문제에 빠질 수 있다.


  ![img1](/image/generic-type-erasure.png)
    

## Reifiable & Non-Reifiable
- 실체화 타입
    - 컴파일 단계에서 타입 소거에 의해 지워지지 않는 타입 정보
    - 원시 타입 (`int`, `double`, `float`..)
    - 일반 클래스와 인터페이스 타입 (`Number`, `Integer`..)
    - 자체 Raw Type (`List`, `ArrayList`, `Map`..)
    - List<?>, ArrayList<?> 등 비한정 와일드카드가 포함된 매개변수화 타입
        
        → 비한정적 와일드카드 <?>는 애초에 타입 정보를 명시하지 않은 것이므로, 컴파일시에 타입 소거를 한다고 해도 잃을 정보가 없기 때문에 실체화 타입이라 볼 수 있다. (컴파일 시점에 Object로 변환)
        
    
- 비실체화 타입
    - 컴파일 이후, 타입 소거에 의해 지워지는 타입 정보
    - 타입 소거에 의해 지워지기 때문에 배열 생성이 불가하다.
    - 제네릭 타입 파라미터는 모두 제거된다고 보면 된다.
        - ex. 제네릭 클래스 (`List<T>`, `Map<K, V>`, `Optional<T>` ..)
        - ex. 제네릭 인터페이스 (`Comparable<T>`, `Function<T, R>` ..)


## 제네릭 타입 소거 과정
- 제네릭 클래스 & 메서드
    1. 제네릭 타입의 경계를 제거한다.
    2. 제네릭 타입 제거 후, 타입이 일치하지 않는 곳에 형변환을 추가한다.

    ```java
      // 치환 전
      class Box<T extends Number> {
          List<T> list = new ArrayList<>();

          void add(T item) {
              list.add(item);
          }

          T getValue(int i) {
              return list.get(i);
          }
      }

      // 1번 동작 후
      // <T extends Number> 하위의 T는 Number로 치환되고, <T> 는 Object로 치환된다.
      class Box {
          List list = new ArrayList(); // Object

          void add(Number item) {
              list.add(item);
          }

          Number getValue(int i) {
              return list.get(i);
          }
      }

      /* 2번 동작 후 */
      class Box {
          List list = new ArrayList(); // Object

          void add(Number item) {
              list.add(item);
          }

          Number getValue(int i) {
              return (Number) list.get(i); // Object List이므로 타입 일치하지 않는 곳에 캐스팅 연산자 추가
          }
      }
    ```
  
- **타입 한정 (`extends`)이 있는 경우**
    
    → `extends`를 사용하여 특정 타입을 제한한 경우, 컴파일러는 해당 타입으로 변환한다.


## Bridge 메서드
- 제네릭과 다형성이 함께 사용될 때, 컴파일러가 자동 생성하는 메서드이다. (제네릭 타입이 소거되면서 발생하는 타입 불일치 해결)
  - 제네릭과 다형성이 충돌하지 않도록 한다. Bridge 메서드가 없다면, 제네릭을 사용한 코드에서 다형성이 깨지고 `ClassCastException` 같은 오류가 발생할 수 있다.
- **브릿지 메서드가 생성되는 원리**
    - 제네릭이 사용된 부모 클래스를 일반 클래스가 상속하면서 타입을 구체화할 때 생성
    - 자식 클래스의 오버라이딩 메서드가 부모 클래스와 시그니처(메서드 정의)가 달라지는 경우 발생
    - 타입 소거(Type Erasure)로 인해 부모 클래스의 메서드는 Raw Type으로 변경되지만, 자식 클래스의 오버라이딩 메서드는 원래 타입을 유지함 → **타입 불일치**
    - 이 때 컴파일러가 브릿지 메서드를 자동 생성하여,부모 클래스의 Raw Type 메서드와 자식 클래스의 구체화된 메서드 사이의 연결을 유지한다.

      ```java
      // 1. 원래 코드
      class Node<T> {
          protected T data;

          public void setData(T data) {
              System.out.println("Node 클래스의 메서드 호출");
              this.data = data;
          }
      }

      class IntegerNode extends Node<Integer> {
          public IntegerNode(Integer data) {
              super.data = data;
          }

          // Integer 타입으로 오버라이딩
          @Override
          public void setData(Integer data) {
              System.out.println("IntegerNode 클래스의 메서드 호출");
              this.data = data + 1000;
          }
      }

      // 2. main에서 호출
      public class Main {
          public static void main(String[] args) {
              Node<Integer> node = new IntegerNode(10);
              node.setData(20);  // "IntegerNode 클래스의 메서드 호출"
          }
      }


      // 3. 브릿지 메서드 생성 후 IntergerNode 상태
      class IntegerNode extends Node {
          public IntegerNode(Integer data) {
              super(data);
          }

          // Bridge Method
          //   상위 Node class가 타입 소거된 Raw Type 상태여서
          //   setData(Object data) 이런식으로 변경되어 있는데 여기서 브릿지 메서드가 타입을 맞춰준다.
          public void setData(Object data) {
              this.setData((Integer) data);
          }

          public void setData(Integer data) {
              System.out.println("IntegerNode 클래스의 메서드 호출");
              this.data = data + 1000;
          }
      }
      ```

      
---
ref.

- [pic1-compile-type-erasure](https://jindory.tistory.com/entry/Java-Generic%EC%9D%98-%EC%BB%B4%ED%8C%8C%EC%9D%BC-%ED%83%80%EC%9E%84%EC%97%90-%EB%8F%99%EC%9E%91-%EB%B0%A9%EC%8B%9D)
- [generic-compile-time-erasure-process](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A0%9C%EB%84%A4%EB%A6%AD-%ED%83%80%EC%9E%85-%EC%86%8C%EA%B1%B0-%EC%BB%B4%ED%8C%8C%EC%9D%BC-%EA%B3%BC%EC%A0%95-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)
- [how-generics-works-internally](https://www.devinline.com/2014/02/how-generics-works-internally-in-java.html)
