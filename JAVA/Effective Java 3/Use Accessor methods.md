## ITEM 16: Use Accessor Methods

### 잘못된 예제: 필드 직접 노출

```java
class Point {
    public double x;
    public double y;
}

```

### 문제점

- 캡슐화 제공하지 않음

  - 필드를 외부에서 직접 접근 가능:

    ```java

    Point p = new Point();
    p.x = -10; // 유효하지 않은 값
    p.y = -20;

    ```

- 내부 표현 변경 시 API 수정 필

  - 예를 들어, x와 y 대신 다른 데이터 표현 방식으로 변경하면 전체 코드 수정 필요.

- 불변식 보장 불가능

  - 필드에 유효하지 않은 값이 들어가도 제한할 방법 없음.

- 부수 작업 수행 불가능

  - 값을 읽거나 설정할 때 로그 기록 또는 추가 작업 불가.

---

### 개선된 예제: 접근자 방식

```java

class Point {
private double x;
private double y;

    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }

    public double getX() {
        return x;
    }

    public void setX(double x) {
        this.x = x;
    }

    public double getY() {
        return y;
    }

    public void setY(double y) {
        this.y = y;
    }

}

```

### 장점

- 캡슐화 제공

  - 외부에서 필드 직접 접근 불가, 메서드를 통해 관리 가능:

    ```java

    Point p = new Point(10, 20);
    p.setX(15); // 유효성 검사 등 추가 가능
    System.out.println(p.getX()); // 값 읽기

    ```

- 내부 표현 변경 유연성

  - 필드 표현 방식을 변경해도 외부 API는 유지 가능:

    ```java
    public double getDistance() {
    return Math.sqrt(x _ x + y _ y); // 내부 표현 방식과 무관
    }
    ```

- 불변식 보장 가능

  - 필드 설정 시 유효성 검사를 추가 가능:

    ```java

    public void setX(double x) {
    if (x < 0) {
    throw new IllegalArgumentException("X는 음수가 될 수 없습니다.");
    }
    this.x = x;
    }

    ```

- 부수 작업 수행 가능

  - 필드 설정 시 로그 기록 또는 이벤트 트리거 추가 가능:

    ```java

    public void setY(double y) {
    System.out.println("Y 값 설정: " + y);
    this.y = y;
    }

    ```

---

### 불변 필드 예제

```java

  public final class Time {
  private static final int HOURS_PER_DAY = 24;
  private static final int MINUTES_PER_HOUR = 60;

      public final int hour;
      public final int minute;

      public Time(int hour, int minute) {
          if (hour < 0 || hour >= HOURS_PER_DAY) {
              throw new IllegalArgumentException("시간은 0~23 사이여야 합니다: " + hour);
          }
          if (minute < 0 || minute >= MINUTES_PER_HOUR) {
              throw new IllegalArgumentException("분은 0~59 사이여야 합니다: " + minute);
          }
          this.hour = hour;
          this.minute = minute;
      }

}

```

### 불변 클래스의 장점

- 불변식 보장

  - 필드가 final이므로 변경 불가:

    ```java

    Time time = new Time(10, 30);
    // time.hour = 12; // 컴파일 에러 발생

    ```

- 스레드 안전성

  - 필드 값이 변경되지 않으므로 동시 접근에도 안전.

### 단점

- 내부 표현 변경 어려움

  - 내부 필드를 직접 노출하므로, 표현 방식 변경 시 API 수정 필요.

- 부수 작업 불가

  - 필드 값을 읽을 때 추가 작업(로그 기록 등) 수행 불가.

### 요약

- 필드 직접 노출은 캡슐화, 유효성 검증, 부수 작업 등을 어렵게 만듦.

- 접근자 사용을 통해 캡슐화, 내부 표현 변경 유연성, 부수 작업 가능.

- 불변 클래스는 값의 안정성과 스레드 안전성 제공하지만, 내부 표현 변경과 부수 작업에 제약이 있음.
