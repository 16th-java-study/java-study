# Composite Pattern (복합체 패턴)
- 복합체 패턴(Composite Pattern)은 복합 객체(Composite) 와 단일 객체(Leaf)를 동일한 컴포넌트로 취급하여, 클라이언트에게 이 둘을 구분하지 않고 동일한 인터페이스를 사용하도록 하는 구조 패턴이다.
- 복합체 패턴은 전체-부분의 관계를 갖는 객체들 사이의 관계를 트리 계층 구조로 정의해야 할때 유용하다.
  - 리눅스의 파일 시스템 구조를 보면 쉽게 이해할 수 있다.
  - 디렉토리 안에는 파일이 들어 있을수도 있고, 다른 디렉토리도 들어있을 수 있다. == 이를 복합적으로 담을수 있어서 Composite 객체라고 한다.
  - 반면 파일은 단일 객체 이기 때문에 이를 Leaf 객체라고 한다. (Leaf는 자식이 없음)

## 장점
- 다형성 재귀를 통해 복잡한 트리 구조를 편리하게 구성할 수 있다.
- 수평적, 수직적 모든 방향으로 객체를 확장할 수 있다.
- 새로운 Leaf 클래스를 추가하더라도 클라이언트는 추상화된 인터페이스 만을 바라보기 때문에 개방 폐쇄 원칙(OCP)을 준수한다. (단일 부분의 확장 용이)


## 단점
- 재귀 호출 특징 상 트리의 depth가 깊어질 수 있어서 디버깅이 어려워질 수 있다.
- 계층형 구조에서 leaf 객체와 composite 객체들을 모두 동일한 인터페이스로 다뤄야 하기 때문에, 공통 인터페이스 설계가 어려울 수 있다.


## 사용 의의
- 이 패턴은 "단일 객체와 복합 객체를 동일하게 다룰 수 있도록 설계하는 패턴"이다.
- 이를 통해 계층적인 구조를 가지는 객체(ex. 폴더와 파일)를 일관된 방식으로 처리할 수 있다.
1. 동일한 타입으로 다룰 수 있도록 구현을 단순화 (동일한 인터페이스 or 추상 클래스화)
2. 재귀 구조를 활용한 계층적 관리 (트리 구조 적용 가능, 재귀적으로 하위 객체들에게 작업 위임)
3. 단일 객체와 복합 객체를 동일한 방식으로 처리 가능하여 클라이언트에서 두 객체를 구분할 필요가 없어진다.

   
   -> 복잡한 계층 구조를 재귀적으로 쉽게 관리, 단일 객체와 복합 객체를 같은 방식으로 다뤄서 코드 일관성 증가


# 컴포지트 패턴 사용 예시
## 상황
- 빌딩에 탑재된 장비들의 전원을 관리하는 제어 프로그램을 개발한다.
- 이 프로그램을 만들기위해 개별 장비의 전원을 켜고 끄는 기능을 제공하는 인터페이스를 정의하고, 장비 별로 알맞은 콘크리트 클래스를 구현했다. 
- 또한, 개별 장비가 아닌 장비들을 하나로 묶어서 관리할 수 있도록 하기 위해 DeviceGroup 클래스를 추가하였다.


```java
public class PowerController {
    public void turnOn(Long deviceId){
        Device device = findDeviceById(Long deviceId);
        device.turnOn();
    }
    
    // turnGroupOn()과 turnOn()은 개별/그룹 차이를 빼면 동일한 기능이다. 
    public void turnGroupOn(Long groupId) {
        DeviceGroup group = findGroupById(Long groupId);
        group.turnAllOn();
    }
}
```

## 문제
- 위 코드의 단점은, 장비나 장비 그룹에 적용하는 동작이 동일함에도 불구하고, 이를 구분해서 처리하고 있다는 점이다.
- PowerController 입장에서 같은 동작임에도 Device와 DeviceGroup을 구분해서 처리해야 한다.
- 이를 Composite Pattern을 사용하여 Device와 DeviceGroup 을 구분하지 않고 처리하게 만들 수 있다.


## 해결 방법
컴포지트 패턴에서 컴포지트는 다음의 책임을 갖는다.


1. 컴포넌트 그룹을 관리한다.
2. 컴포지트에 기능 실행을 요청하면, 컴포지트는 포함하고 있는 컴포넌트들에게 기능 실행 요청을 위임한다.

```java
public class DeviceGroup implements Device {
    private List<Device> devices = new ArrayList<Device>();

    public void addDevice(Device d){
        devices.add(d);
    }

    public void remove(Device d){
        devices.remove(d);
    }

    public void turnOn(){
        for(Device device : devices){
            device.turnOn(); // 관리하는 Device 객체들에게 실행 위임
        }
    }

    public void turnOff(){
        for(Device device : devices){
            device.turnOff(); // 관리하는 Device 객체들에게 실행 위임
        }
    }
}
```

이제 DeviceGroup 클래스는 Device 타입이 되므로, 전원 제어 기능을 제공하는 PowerController 클래스는 Device 타입과 DeviceGroup 타입을 구분할 필요가 없다.


Device 타입만을 이용해서 전원 관리를 할 수 있게 되고, 전체냐 부분이냐에 상관없이 클라이언트가 단일 인터페이스로 기능을 실행할 수 있게 된다.


마지막으로, 해당 패턴을 구현할 때 고려할 점은 컴포넌트를 관리하는 인터페이스를 어디서 구현할지 고민한 후 고려해야 한다. (객체들의 관계 및 트리 구조에서의 관리 방식에 직접적인 영향을 주기 때문)
