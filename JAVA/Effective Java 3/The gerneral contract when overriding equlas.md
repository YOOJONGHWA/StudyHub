## equals 메서드 재정의: 요약과 가이드라인

#### 재정의하지 말아야 할 경우

1. 인스턴스 고유성: 객체가 본질적으로 고유한 경우 (예: Thread).

2. 논리적 동치성 검사 불필요: 동치성 비교가 필요 없는 경우.

3. 상위 클래스의 메서드가 적합한 경우: 상위 클래스의 equals가 이미 적절히 동작하는 경우.

4. private 또는 package-private 클래스: 외부에서 equals 호출이 없는 경우.

---

#### 재정의해야 하는 경우

1. 논리적 동치성 검사 필요:

   - 값 클래스 (예: Integer, String)처럼 객체의 논리적 동등성을 확인해야 할 때.

2. 상위 클래스의 equals가 부적합:

   - 예를 들어, 값 클래스를 작성 중이고 상위 클래스에서 동등성 정의가 없거나 필요에 맞지 않을 때.

---

#### equals 메서드의 규약

equals 메서드는 **동치관계(equivalence relation)**를 만족해야 합니다:

1. 반사성(reflexivity): 모든 객체 x에 대해 x.equals(x)는 true.

2. 대칭성(symmetry): x.equals(y)가 true면, y.equals(x)도 true.

3. 추이성(transitivity): x.equals(y)와 y.equals(z)가 true라면, x.equals(z)도 true.

4. 일관성(consistency): x.equals(y)의 결과는 외부 상태가 변경되지 않는 한 변하지 않음.

5. null-아님: null과 비교 시 항상 false.

#### 주요 설계 사례와 문제

1. 대칭성 위반

   - CaseInsensitiveString에서 String과의 비교 로직이 대칭성을 깨뜨림.

   - 해결: equals를 CaseInsensitiveString 타입에만 적용.

2. 추이성 위반

   - Point와 ColorPoint의 equals를 잘못 구현하면 추이성을 위반.

   - 해결: 상속 대신 컴포지션을 사용.

3. 일관성

   - java.net.URL처럼 외부 상태(네트워크 상태 등)에 따라 결과가 달라지면 규약 위반.

---

#### equals 메서드 구현 방법

1. 자기 자신 참조 확인: if (this == o) return true;

2. 타입 확인: if (!(o instanceof MyType)) return false;

3. 타입 변환: MyType other = (MyType) o;

4. 핵심 필드 비교:

   - 기본 타입: == 사용.

   - 참조 타입: Objects.equals(a, b)로 비교 (null-safe).

   - float와 double: Float.compare 또는 Double.compare 사용.

```java

@Override
public boolean equals(Object o) {
    if (this == o) return true; // 자기 참조 확인
    if (!(o instanceof MyType)) return false; // 타입 확인
    MyType other = (MyType) o; // 타입 변환
    return Objects.equals(this.field1, other.field1) // 핵심 필드 비교
            && this.field2 == other.field2;
}

```

---

#### 주의 사항

1. hashCode 재정의: equals를 재정의하면 반드시 hashCode도 재정의 (Item 11).

2. 객체 불변성: 불변 객체라면 동등성 결과는 한 번 정해지면 영원히 동일.

3. 구체 클래스 비교:

   - getClass()로 타입 비교 시 하위 클래스 확장이 제한될 수 있음.

   - 상속이 필요한 경우 상위 클래스의 equals는 instanceof로 확인.

---

#### 도구 활용

- AutoValue: Google AutoValue를 사용하여 자동으로 equals와 hashCode 생성.

```java

@AutoValue
public abstract class MyValue {
    public abstract String name();
    public abstract int age();

    public static MyValue create(String name, int age) {
        return new AutoValue_MyValue(name, age);
    }
}

```

위 내용을 준수하면 안정적이고 직관적인 equals 메서드를 작성할 수 있습니다.
