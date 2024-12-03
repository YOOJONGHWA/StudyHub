## Overriding hashCode

#### 왜 hashCode()를 재정의해야 할까?

equals()와 hashCode()는 동등성을 비교할 때 중요한 역할을 합니다. 만약 equals()를 재정의했다면, 동일한 값의 객체가 같은 해시코드를 반환해야 합니다. 그렇지 않으면 HashMap, HashSet과 같은 컬렉션에서 문제가 발생할 수 있습니다.

#### 규칙

- 동등한 객체는 동일한 hashCode() 값을 가져야 한다.

- 다른 객체는 동일한 hashCode() 값을 가질 필요는 없지만, 해시 충돌을 줄이기 위해 다르게 반환하는 것이 좋다.

- equals()가 true인 두 객체는 반드시 동일한 hashCode() 값을 반환해야 한다.

#### 예시 코드

- PhoneNumber 클래스에서 equals()만 구현하고 hashCode()를 구현하지 않으면 문제 발생

```java

import java.util.HashMap;
import java.util.Map;

public final class PhoneNumber {
    private final short areaCode, prefix, lineNum;

    public PhoneNumber(short areaCode, short prefix, short lineNum) {
        this.areaCode = areaCode;
        this.prefix = prefix;
        this.lineNum = lineNum;
    }

    @Override
    public boolean equals(Object o) {
        if (o == this) return true;
        if (!(o instanceof PhoneNumber)) return false;
        PhoneNumber pn = (PhoneNumber) o;
        return pn.lineNum == lineNum && pn.prefix == prefix && pn.areaCode == areaCode;
    }

    // `hashCode()`를 구현하지 않으면 HashMap에서 문제가 생깁니다.
}

public class Main {
    public static void main(String[] args) {
        Map<PhoneNumber, String> map = new HashMap<>();
        map.put(new PhoneNumber((short) 707, (short) 867, (short) 5309), "Jenny");
        System.out.println(map.get(new PhoneNumber((short) 707, (short) 867, (short) 5309))); // null
    }
}

```

이 경우, equals()는 두 객체가 같은지 비교할 때 잘 작동하지만, hashCode()를 재정의하지 않으면 HashMap에서 동일한 객체를 찾지 못합니다. 이는 PhoneNumber 객체들이 동일하지만 다른 해시코드를 반환하기 때문입니다.

#### hashCode()를 재정의한 올바른 예시

```java

@Override
public int hashCode() {
    int result = Short.hashCode(areaCode);
    result = 31 * result + Short.hashCode(prefix);
    result = 31 * result + Short.hashCode(lineNum);
    return result;
}

```

이제 hashCode()를 재정의했기 때문에, equals()가 동일하다고 판단한 두 객체는 동일한 해시코드를 반환하게 됩니다.

#### 결과 확인

```java

public class Main {
    public static void main(String[] args) {
        Map<PhoneNumber, String> map = new HashMap<>();
        map.put(new PhoneNumber((short) 707, (short) 867, (short) 5309), "Jenny");
        System.out.println(map.get(new PhoneNumber((short) 707, (short) 867, (short) 5309))); // Jenny
    }
}

```

위 코드에서 hashCode()를 재정의한 후, 동일한 PhoneNumber 객체는 이제 HashMap에서 제대로 찾아집니다.

### 추가 사항

- 캐싱: hashCode() 계산이 복잡한 경우, 성능을 개선하기 위해 hashCode를 캐싱할 수 있습니다.

```java

private int hashCode;

@Override
public int hashCode() {
    if (hashCode == 0) {
        int result = Short.hashCode(areaCode);
        result = 31 * result + Short.hashCode(prefix);
        result = 31 * result + Short.hashCode(lineNum);
        hashCode = result;
    }
    return hashCode;
}

```

- 배열 처리: 배열을 필드로 가질 경우, Arrays.hashCode()를 사용하여 배열의 해시코드를 계산할 수 있습니다.

```java

@Override
public int hashCode() {
    return Arrays.hashCode(new short[] {areaCode, prefix, lineNum});
}

```

이렇게 hashCode()와 equals()를 적절히 구현하면, 컬렉션에서 객체를 제대로 관리하고, 성능 문제를 방지할 수 있습니다.
