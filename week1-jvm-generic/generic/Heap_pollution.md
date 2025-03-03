# 힙오염

JVM의 힙 메모리 영역에 저장되어있는 특정 변수(객체)가 불량 데이터를 참조함으로써 힙에서 데이터를 가져올 때 런타임 에러가 발생할 수 있는 상태를 말한다.

→ 제네릭으로 인해 많이 발생된다.



## 문제 원인

하위 호환성을 위해서 제네릭을 컴파일 시점까지 유효하고 컴파일이 되면 타입 파라미터를 Object로 변환하거나 제거한다. 그런데 **타입 소거**와 **컴파일러 특성**이 합쳐지며 힙 오염이 발생할 수 있게 된다.



## 문제 상황

- 원시 타입과 매개변수 타입을 동시에 사용할 때
- 확인되지 않은 형변환을 수행할 때

예제)

컴파일 특성

```java
ArrayList<String> list1 = new ArrayList<>();
list1.add("qwer");
list1.add("asdf");

Object obj = list1;

ArrayList<Double> list2 = (ArrayList<Double>) obj;
list2.add(1.0); // Heap 오염 발생
list2.add(2.0);

for (double n : list2) {
	System.out.println(n); // <- ClassCastException
}
```

컴파일러는 형변환 대상 객체에 대해서 검사하지 않고 **캐스팅할 때 대입되는 변수에 저장할 수 있는지 여부만 판단**한다.

(즉 list2변수에 (ArrayList<Double>)로 캐스팅된 obj가 들어갈 수 있는지만 판단)

→ ArrayList<String> → Object → ArrayList<Double> 로 이상하게 캐스팅 되더다도 알 수 없다.

제네릭 타입 소거

```java
ArrayList list1 = new ArrayList();
list1.add("qwer");
list1.add("asdf");

Object obj = list1;

ArrayList list2 = (ArrayList) obj;
list2.add(1.0); 
list2.add(2.0);
```

컴파일이 되면 제네릭 타입 파라미터는 Object로 변환, 또는 삭제된다. 따라서 리스트에 String, Double값이 공존할 수 있게 된다.



## 해결방법

`Collections.checkList()` 를 통해 해결할 수 있다. 객체에 의도하지 않은 타입이 들어가면 예외를 발생시킨다.

```java
List<String> list1 = Collections.checkList(new ArrayList<>(), String.class);

list1.add("qwer");
list1.add("asdf");

Object obj = list1;

ArrayList<Double> list2 = (ArrayList<Double>) obj;
list2.add(1.0); // 여기서 힙 오염을 미리 방지.
list2.add(2.0);

for (double n : list2) {
	System.out.println(n); // 기존 예외 발생 지점.
}
```



## 참고 자료

* [https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A0%9C%EB%84%A4%EB%A6%AD-%ED%9E%99-%EC%98%A4%EC%97%BC-Heap-Pollution-%EC%9D%B4%EB%9E%80](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%A0%9C%EB%84%A4%EB%A6%AD-%ED%9E%99-%EC%98%A4%EC%97%BC-Heap-Pollution-%EC%9D%B4%EB%9E%80)
