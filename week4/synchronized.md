# Synchronized
***
## Synchronized란?
자바에서 동기화 영역을 표시하는 키워드.<br>
=> 여러 개의 스레드가 공유 자원에 동시에 접근할 때 한 번에 하나의 스레드만 접근할 수 있도록 보장하는 메커니즘
***
## 사용 방법
~~~java
class SynchronizedMethod {
    private int count = 0;
    
    public synchronized void increment() {
        count++;
    }
}

~~~
첫 번째 사용 방법으로는 위처럼 메서드를 synchronized를 통해 한 번에 하나의 스레드만 해당 메서드를 실행할 수 있도록
할 수 있다.
~~~java
class SynchronizedBlock {
    private int count = 0;
    private final Object lock = new Object();

    public void increment() {
        synchronized (lock) {
            count++;
        }
    }
}
~~~
두 번째로는 필요한 부분만 블록을 synchronized를 통해 성능을 최적화하는 방법이다.
~~~java
class SynchronizedStaticMethod {
    private static int count = 0;

    public static synchronized void increment() {
        count++;
    }
}
~~~
마지막으로는 클래스 전체에서 동일한 자원에 접근하는 모든 스레드를 동기화하는 방법이 있다.
<br>
결국 근데 synchronized를 남발하게 되면 이 block/unblock 처리에도 리소스가 들어가는 작업이니 꼭 필요한 부분만 사용하도록 한다.
***
### ※ 참고자료
- https://parkcheolu.tistory.com/15