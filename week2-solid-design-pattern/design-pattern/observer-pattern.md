# Observer Pattern
- 옵저버(관찰자)들이 관찰하고 있는 대상자(Subject)의 상태가 변화가 있을 때마다 대상자는 직접 목록의 각 관찰자들에게 통지하고, 관찰자들은 알림을 받아 조치를 취하는 행동 패턴
- 옵저버 패턴은 여타 다른 디자인 패턴들과 다르게 일대다(one-to-many) 의존성을 가진다. (ex. 분산 이벤트 핸들링 시스템을 구현하는 데 사용)


## 옵저버 패턴 사용 시기
- 앱이 한정된 시간, 특정한 케이스에만 다른 객체를 관찰해야 하는 경우
- 대상 객체의 상태가 변경될 때마다 다른 객체의 동작을 트리거해야 하는 경우
- 한 객체의 상태가 변경되면 다른 객체도 변경해야 하는 경우


## 해당 패턴의 장점
- Subject의 상태 변경을 주기적으로 조회하지 않고 자동으로 감지할 수 있다.
- 발행자의 코드를 변경하지 않고도 새 구독자 클래스를 도입할 수 있어 개방 폐쇄 원칙(OCP)를 준수한다
- 런타임 시점에서에 발행자와 구독 알림 관계를 맺을 수 있다.
- (느슨한 결합 상태) 상태를 변경하는 객체(Subject)와 변경을 감지하는 객체(Observer)의 관계를 느슨하게 유지할 수 있다.


## 해당 패턴의 단점
- 구독자는 알림 순서를 제어할수 없고, 무작위 순서로 알림을 받는다.
- 다수의 옵저버 객체를 등록한 이후, 해지하지 않으면 메모리 누수 발생 위험이 있다.

---

# 옵저버 패턴 사용 예시


## 상황
- 웹 사이트의 상태를 확인해서 응답 속도가 느리거나 연결이 안 되면 모니터링 담당자에게 이메일로 통지해 주는 시스템을 만들고자 한다.

## 문제
- StatusChecker는 시스템의 상태가 불안정해지면 이 사실을 EmailSender, SmsSender, Messenger 객체에게 변경 사실을 알려준다는 점이다.
- 이렇게 한 객체의 상태 변화를 정해지지 않은 여러 다른 객체에 통지하고 싶다.

## 해결 방법
- 옵저버 패턴에는 크게 대상(subject) 객체와 옵저버(observer) 객체가 등장하는데, 대상 객체는 다음의 두 가지 책임을 갖는다.
#### 1. 옵저버 목록을 관리하고, 옵저버를 등록하고 제거할 수 있는 메서드를 제공한다. add() 메서드와 remove() 메서드가 각각 옵저버 목록에 등록하고 삭제하는 기능을 제공한다.

#### 2. 상태의 변경이 발생하면 등록된 옵저버에 변경 내역을 알린다. notifyStatus() 메서드가 등록된 옵저버 객체의 onAbnormalStatus() 메서드를 호출한다.


```java
public class StatusChecker extends StatusSubject {
    
    public void check() {
        Status status = loadStatus();
        if(status.isNotNormal()){
            super.notifyStatus(status);
        }
    }
    
    private Status loadStatus(){
        // ...
    }
} 

public abstract class StatusSubject {
    private List<StatusObserver> observers = new Arraylist<StatusObserver>();

    public void add(StatusObserver observer){
        observers.add(observer);
    }

    public void remove(StatusObserver observer){
        observers.remove(observer);
    }

    public void notifyStatus(Status status){
        for(StatusObserver observer : observers){
            observer.onAbnormalStatus(status);
        }
    }
}

public interface StatusObserver {
    void onAbnormalStatus(Status status);
}

public class StatusEmailSender implements StatusObserver {

    @Override
    public void onAbnormalStatus(Status status) {
        sendEmail(status);
    }
    
    private void sendEmail(Status status){
        // 이메일 전송 코드
    }
}
```

## 옵저버 패턴 구현 시 고려 사항
- Subject 객체의 통지 기능을 실행하는 주체를 잘 정해야 한다. (Subject 객체를 사용하는 코드에서 통지할 지, Subject 객체 자체에서 직접 통지 기능을 구현할 지?)
- Button 기능 처럼 Subject 객체의 상태가 바뀔 때마다 옵저버에게 통지를 해야 하는 상황일 때, Subject 객체에서 직접 통지 기능을 실행하는 것이 구현에 유리하다.
- 왜냐하면, Subject 객체를 사용하는 코드에서 통지 기능을 실행하면 상태를 변경하는 모든 코드에서 일일히 통지 기능을 호출해야하기 때문이다.

  
  -> Human Error 발생 위험 높음! (ex. 통지 기능을 호출해줘야 하는 상황에서 개발자의 실수로 호출 못하는 경우)





---
ref.
- Book-객체지향과 디자인 패턴(최범균 저)
- [observer-pattern-1](https://incheol-jung.gitbook.io/docs/study/undefined/undefined-2/undefined-6)
- [observer-pattern-2](https://inpa.tistory.com/entry/GOF-%F0%9F%92%A0-%EC%98%B5%EC%A0%80%EB%B2%84Observer-%ED%8C%A8%ED%84%B4-%EC%A0%9C%EB%8C%80%EB%A1%9C-%EB%B0%B0%EC%9B%8C%EB%B3%B4%EC%9E%90)

