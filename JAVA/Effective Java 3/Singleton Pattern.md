#### 싱글톤 패턴

특정 클래스의 객체를 하나만 생성하고 공유하는 디자인 패턴

데이터 베이스 연결, 설정 관리 등 객체를 하나만 유지해야 하는 상황에서 유용

1. Public Static Final 필드 방식

   - 객체를 클래스 로딩 시점에 한 번만 생성

```java

public class Singleton {
    public static final Singleton INSTANCE = new Singleton();
    private Singleton() { }
}

```

---

2. Static Factory 메서드 방식

   - getInstance() 메서드로 객체 반환.

```java

public class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    private Singleton() { }
    public static Singleton getInstance() { return INSTANCE; }
}


```

---

3. Enum 방식

   - Java에서 가장 안전하며 간단한 방식.

   - Reflection 공격과 직렬화 문제 자동 방어.

```java

public enum Singleton {
    INSTANCE;
}


```

#### Reflection 공격이란?

- Reflection은 Java 프로그램이 실행 중 클래스, 메서드, 생성자 등에 동적으로 접근하도록 해주는 강력한 도구입니다.

- Reflection 공격은 이 기능을 악용하여, private 생성자에 접근하거나 싱글턴 객체를 추가로 생성하는 보안 취약점을 말합니다.

```java

import java.lang.reflect.Constructor;

public class Main {
    public static void main(String[] args) throws Exception {
        Singleton instance1 = Singleton.getInstance();
        Constructor<Singleton> constructor = Singleton.class.getDeclaredConstructor();
        constructor.setAccessible(true);
        Singleton instance2 = constructor.newInstance();
        System.out.println(instance1 == instance2); // false
    }
}


```

---

#### Reflection 공격 방어 방법

1. 생성자에서 방어 코드 추가

   - 기존 객체가 존재하면 예외를 던지도록 설계

```java

public class Singleton {
    private static final Singleton INSTANCE = new Singleton();
    private Singleton() {
        if (INSTANCE != null) throw new RuntimeException("Reflection attack detected!");
    }
    public static Singleton getInstance() { return INSTANCE; }
}


```

2. Enum 사용

   - Enum을 활용하면 Reflection API로 객체를 생성할 수 없습니다.

---

3. final 키워드로 상속 차단

   - 클래스 자체를 final로 선언해 Reflection을 통한 우회 가능성을 낮춤.

```java

public final class Singleton {
    private static final Singleton INSTANCE = new Singleton();
}


```

---

#### 요약

- 싱글턴 패턴 구현 방식:

  - Public Static Final 필드 방식: 간단, 명확.

  - Static Factory 메서드 방식: 유연성 제공.

  - Enum 방식: 추천. 가장 안전하며 Reflection 공격과 직렬화 문제를 자동 방어.

- Reflection 공격:

  - Reflection API로 private 생성자에 접근하여 싱글턴을 깨뜨리는 보안 위협.

  - 방어 방법:

    - 생성자에서 객체 중복 생성 방지.

    - Enum 방식 활용.

    - final 클래스로 설계.

    - Reflection 방어를 위해 Enum 방식을 사용하는 것이 가장 안전하고 권장됩니다.

![alt text](<./img/napkin-selection%20(2).png>)
