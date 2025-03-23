# 모니터
***
## 모니터란?
스레드가 "mutual exclusion"와 "cooperation"을 가질 수 있도록 하는 메커니즘이다.
- Mutual exclusion : 여러 프로세스나 스레드가 동시에 공유 자원에 접근하지 못하게 함
- cooperation : 여러 프로세스나 스레드가 공유 자원을 함께 사용하면서 상호작용하는 방식
***
## 특징
- 객체마다 하나의 모니터를 가짐. => 이 객체의 모니터를 획득한 스레드만 상호배제적으로 접근할 수 있다.
- 다른 스레드는 대기 상태로 존재.
- 블록이나 메서드가 끝나면 자동으로 락 해제.
***
## 작동 과정
- 구성
  - mutex : mutual의 "mut"와 exclusion의 "ex"의 합성어로 스레드가 critical section으로 진입 시 필요하다.
  - condition variable : 대기 중인 스레드들이 있는 곳.
    - entry queue : synchronized 블록에 들어가려 하지만, 락을 획득하지 못한 스레드가 대기하는 곳.
    - wait queue : wait()을 호출한 후 락을 반납한 스레드들이 대기하는 곳.
      - wait() : 현재 점유중인 스레드를 해제하고 대기시키는 명령어.

1. 스레드가 mutex 락을 획득하고 critical section 진입.
    - 나머지 스레드들은 큐에 들어가서 await 상태로 존재.
2. 스레드가 mutex 락을 반환하고, signal/broadcast를 통해 대기 중이던 스레드들을 깨운다.
   - signal : 스레드 하나 => notify()
   - broadcast : 모든 스레드 => notifyAll()
   - 이때 JVM 스케줄러는 우선순위 알고리즘으로 작동되며, 같을 경우에는 FIFO로 작동한다고 함.
***
## 생산자 소비자 예제 코드
~~~java
class ProducerAndConsumer {
    private int data;
    private boolean available = false;

    // synchronized를 한 번에 하나의 스레드만 특정 블록을 실행할 수 있게 해준다.
    public synchronized void produce(int value) throws InterruptedException {
        while (available) {  
            wait();  // 데이터가 있으면 모니터 락 해제 후 대기
        }
        data = value;
        available = true;
        notify();  // 대기 중인 소비자 깨우기 => 아래로 이동
    }

    public synchronized int consume() throws InterruptedException {
        while (!available) { 
            wait();  // 사용할 데이터가 없으면 Wait Queue에서 대기
        }
        available = false; // 데이터를 소비
        notify();  // 생산자 깨우기
        return data;
    }
}
~~~
***
## 주의사항
notify()도 결국 하나의 스레드만 호출하는 방법이다. 그러면 결국 호출이 안되는 친구가 발생하는 기아 현상이 일어날 수도 있다.<br>
그러면 notifyAll()을 쓰면? 위의 예제를 통해 보면 만약 큐가 꽉 차있는 상태 일시, 생산자와 소비자가 구분없이 깨어나게 된다. 이는 경쟁 상태로 이어진다.
***
### ※ 참고자료
- https://www.baeldung.com/cs/monitor
- https://www.youtube.com/watch?v=Dms1oBmRAlo