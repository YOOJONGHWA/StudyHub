# 원자적 연산과 Volatile 및 메트릭(Metrics)의 실용 예시

## 1. 원자적 연산 (Atomic Operations)

- **정의**: 원자적 연산은 더 이상 나눠지지 않는 최소 단위의 연산을 의미합니다. 멀티스레드 환경에서 여러 스레드가 동시에 변수를 수정할 때, 원자적 연산을 사용하면 **경쟁 상태(race condition)**를 방지하고, 스레드 간의 안전한 데이터 처리를 보장할 수 있습니다.
- **실용 예시**:

  - **카운터 증가**: 여러 스레드가 동시에 카운터를 증가시키는 경우, `AtomicInteger`를 사용하여 원자적으로 증가시킬 수 있습니다.

  - **예시 코드**:

    ```java
    import java.util.concurrent.atomic.AtomicInteger;

    public class AtomicExample {
        private static AtomicInteger counter = new AtomicInteger(0);

        public static void main(String[] args) {
            Thread thread1 = new Thread(() -> {
                for (int i = 0; i < 1000; i++) {
                    counter.incrementAndGet(); // 원자적 증가
                }
            });
            Thread thread2 = new Thread(() -> {
                for (int i = 0; i < 1000; i++) {
                    counter.incrementAndGet();
                }
            });

            thread1.start();
            thread2.start();

            try {
                thread1.join();
                thread2.join();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            System.out.println("Final counter value: " + counter.get()); // 2000
        }
    }
    ```

  - **결과**: 두 스레드가 동시에 `counter`를 증가시켜도 `AtomicInteger` 덕분에 경쟁 상태 없이 정확하게 2000이 출력됩니다.

---

## 2. Volatile

- **정의**: `volatile` 키워드는 변수의 값을 여러 스레드가 동시에 읽고 쓸 때, **캐시된 값이 아닌 메인 메모리에서 직접 읽고 쓰게** 하여, 변수의 최신 값이 모든 스레드에 즉시 반영되도록 보장합니다.

- **실용 예시**:

  - **플래그 값 체크**: 특정 조건을 만족할 때까지 대기하는 작업에서 `volatile`을 사용하면, 하나의 스레드에서 변경한 값을 다른 스레드가 즉시 반영하도록 할 수 있습니다.

  - **예시 코드**:

    ```java
    public class VolatileExample {
        private static volatile boolean flag = false;

        public static void main(String[] args) {
            Thread thread1 = new Thread(() -> {
                while (!flag) {
                    // busy-waiting
                }
                System.out.println("Flag has been set to true");
            });

            Thread thread2 = new Thread(() -> {
                try {
                    Thread.sleep(1000); // 1초 후 flag 값 변경
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                flag = true; // flag 값을 true로 변경
                System.out.println("Flag set to true");
            });

            thread1.start();
            thread2.start();
        }
    }
    ```

  - **결과**: `volatile`을 사용하여 `flag`의 값이 변경되면 다른 스레드가 즉시 반영할 수 있게 됩니다. 그렇지 않으면, 변경된 값이 다른 스레드에 반영되지 않고 무한 대기할 수 있습니다.

---

## 3. 메트릭(Metrics)

- **정의**: 시스템의 성능, 처리 시간, 리소스 사용량 등의 **데이터를 수집하고 분석**하는 방법입니다. 멀티스레드 환경에서 메트릭을 관리할 때는 데이터의 일관성과 동기화가 필요합니다.

- **실용 예시**:

  - **응답 시간 측정**: 여러 요청의 응답 시간을 측정하고 평균값을 구하는 데 유용합니다. `AtomicLong`을 사용하여 총 응답 시간을 원자적으로 업데이트하고, `volatile`을 사용하여 평균 응답 시간을 모든 스레드가 바로 볼 수 있게 합니다.

  - **예시 코드**:

    ```java
    import java.util.concurrent.atomic.AtomicLong;

    public class MetricsExample {
        private static AtomicLong totalResponseTime = new AtomicLong(0);
        private static volatile double averageResponseTime = 0.0;
        private static long count = 0;

        public static void main(String[] args) {
            Thread thread1 = new Thread(() -> {
                long start = System.currentTimeMillis();
                // 요청 처리 시뮬레이션
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                long end = System.currentTimeMillis();
                long responseTime = end - start;

                totalResponseTime.addAndGet(responseTime); // 원자적 증가
                count++;
                averageResponseTime = totalResponseTime.get() / (double) count; // 평균값 업데이트
                System.out.println("Average Response Time: " + averageResponseTime);
            });

            Thread thread2 = new Thread(() -> {
                long start = System.currentTimeMillis();
                // 요청 처리 시뮬레이션
                try { Thread.sleep(200); } catch (InterruptedException e) {}
                long end = System.currentTimeMillis();
                long responseTime = end - start;

                totalResponseTime.addAndGet(responseTime);
                count++;
                averageResponseTime = totalResponseTime.get() / (double) count;
                System.out.println("Average Response Time: " + averageResponseTime);
            });

            thread1.start();
            thread2.start();
        }
    }
    ```

  - **결과**: 여러 스레드에서 요청 처리 시간을 측정하고, 총 응답 시간을 원자적으로 합산한 후 평균을 `volatile`로 저장하여 모든 스레드가 최신 평균값을 볼 수 있도록 합니다.

---

## 정리

- **원자적 연산(Atomic Operations)**: 멀티스레드 환경에서 변수를 안전하게 변경할 수 있도록 보장합니다. `AtomicInteger`나 `AtomicLong`과 같은 클래스를 사용하여 **경쟁 상태를 방지**할 수 있습니다.
- **Volatile**: `volatile` 키워드를 사용하면 **변수의 값이 모든 스레드에 즉시 반영**되도록 보장할 수 있습니다. 주로 **플래그**와 같은 변수에서 사용됩니다.
- **메트릭(Metrics)**: 시스템의 **성능 측정** 및 **평균 응답 시간 계산**과 같은 작업을 할 때, **원자적 연산**과 **volatile**을 함께 사용하여 **데이터 일관성**을 유지합니다.

이 세 가지 개념은 멀티스레드 프로그래밍에서 **경쟁 상태 방지**, **일관성 있는 데이터 관리** 및 **성능 측정**을 위한 핵심 요소입니다.
