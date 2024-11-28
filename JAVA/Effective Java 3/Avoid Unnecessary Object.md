#### 불필요한 객체 생성

1. 불필요한 객체 생성의 문제점

- 성능 저하: 반복적으로 생성되는 객체는 CPU와 메모리 자원을 낭비하고, 애플리케이션 속도를 느리게 만든다

- 자원 낭비: 객체 생성/ 삭제 과정에서 GC(가비 컬렉션)가 자주 발생해 프로그램이 더 많은 전력을 소모하고 응답 속도가 느려질 수 있다.

---

2. 자주 발생하는 예시와 개선 방법

- String 객체 반복 생성

```java

// ❌ 나쁜 예 - 매번 새로운 String 객체 생성
String s = new String("bad example");

// ✅ 좋은 예 - 하나의 인스턴스 재사용
String s = "good example";


```

- "문자열" 리터럴은 JVM 내부 캐시를 통해 재사용됩니다.

- new String()은 매번 새로운 객체를 생성하므로 피하세요.

---

- 정규 표현식(Pattern) 객체 생성

```java

// ❌ 나쁜 예 - String.matches()는 매번 새로운 Pattern 객체 생성
boolean isValid = "XV".matches("^(?=.)M*(C[MD]|D?C{0,3})$");

// ✅ 좋은 예 - Pattern 객체를 미리 생성해 재사용
private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})$");

boolean isValid = ROMAN.matcher("XV").matches();

```

- Pattern은 내부적으로 복잡한 구조를 가지므로 캐싱하여 재사용하세요.

- 반복적으로 호출되는 메서드에서 큰 성능 차이를 만듭니다.

---

- 박싱된 기본 타입 사용

```java

// ❌ 나쁜 예 - 박싱된 타입(Long) 사용으로 불필요한 객체 생성
Long sum = 0L;
for (long i = 0; i < Integer.MAX_VALUE; i++) {
    sum += i; // 매번 Long 객체 생성
}

// ✅ 좋은 예 - 기본 타입(long) 사용
long sum = 0L;
for (long i = 0; i < Integer.MAX_VALUE; i++) {
    sum += i; // 객체 생성 없음
}

```

- Long 같은 박싱 타입 대신 long 같은 기본 타입을 사용하세요.

- 박싱된 타입은 내부적으로 새로운 객체를 생성하기 때문에 성능 저하가 발생합니다.

---

- 기타 유용한 팁

- 정적 팩토리 메서드 사용

객체를 매번 생성하는 대신, 미리 만들어둔 인스턴스를 반환하는 메서드를 활용

```java

// ✅ Boolean.valueOf() 사용 예
Boolean isTrue = Boolean.valueOf("true"); // 기존 객체 재사용

```

- 방어적 복사는 필요할 때만

  - 데이터 안전을 위해 객체를 복사해야 하는 경우가 있지만, 복사 자체가 불필요하면 성능이 떨어집니다.

- 객체 풀(pool)은 신중하게 사용

  - JVM의 가비지 컬렉터가 이미 최적화되어 있기 때문에 직접 객체 풀을 관리하는 경우는 드뭅니다.

  - 객체 풀은 메모리 사용량과 코드 복잡도를 증가시킬 수 있습니다.
