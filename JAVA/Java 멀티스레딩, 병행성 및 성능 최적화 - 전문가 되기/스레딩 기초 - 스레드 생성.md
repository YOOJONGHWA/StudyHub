#### 쓰레드 기본 예시

- 애플리케이션에서 여러 개의 쓰레드를 사용하는 이유는 무엇일까?

  - 여러 개의 쓰레드를 사용하면 동시에 여랴 개의 관련 작업을 실행할 수 있어서, 애플리케이션 반응을 향상시 킬수 있기 때문, 또한 여러 개의 작업을 동시에 실행하여 더 뛰어난 성능을 달성할 수 있다.

  - 즉 밀티쓰레딩은 병행성과 병렬성을 활용할 수 있게 해주는것

- 그럼 운영 체제는 어떤 방식으로 스케쥴링할 쓰레드를 설계할까?

  - 운영 체제는 각 스레드에 대한 동적 우선 순위를 가진 쓰레드를 우선시하고 시스템상 특정 쓰레드가 기아 상태가 되는 것을 방지

---

#### 쓰레드 출력 순서

```
Thread thread1 = new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("I'm going for a walk");
    }
});

Thread thread2 = new Thread(new Runnable() {
    @Override
    public void run() {
        System.out.println("I'm going to swim");
    }
});

thread1.start();
thread2.start();
System.out.println("I'm going home");


```

출력 순서는 항상 코드 작성대로 보장받지는 않는다.

---

#### 기본 적인 쓰레드 설계 방식

```

1. 상속 활용
public class Main {
    public static void main(String [] args) {
        Thread thread = new TaskThread1();
        thread.start();
    }

    public static class TaskThread1 extends Thread {
        @Override
        public void run(){
            System.out.println("Hello from new thread");
        }
    }
}


2. Runnable 활용
public class Main {
    public static void main(String [] args) {
        Thread thread = new Thread(new Task2());
        thread.start();
    }

    public static class Task2 implements Runnable {
        @Override
        public void run(){
            System.out.println("Hello from new thread");
        }
    }

```
