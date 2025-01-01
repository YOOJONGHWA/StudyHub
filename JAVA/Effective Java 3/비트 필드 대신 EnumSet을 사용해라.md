## ITEM 36: 비트 필드 대신 EnumSet을 사용해라

#### 1. 기존 비트 필드 방식의 문제점

비트 필드의 정의 및 사용 예

- 비트 필드는 여러 상수를 하나의 집합으로 표현하기 위해 비트별 OR 연산자를 사용하여 상수들을 조합하는 방식이다.

- 예제 코드:

```java

public class Text {
    public static final int STYLE_BOLD          = 1 << 0;
    public static final int STYLE_ITALIC        = 1 << 1;
    public static final int STYLE_UNDERLINE     = 1 << 2;
    public static final int STYLE_STRIKETHROUGH = 1 << 3;

    public void applyStyles(int styles) {
        // 비트 필드 처리 로직
    }
}

Text text = new Text();
text.applyStyles(STYLE_BOLD | STYLE_UNDERLINE);

```

#### 비트 필드의 단점

- 가독성 부족

  - 비트 필드 값이 출력되면 해석하기 어려움.
    - 예: STYLE_BOLD | STYLE_UNDERLINE이 숫자로 출력되면 의미를 알기 어려움.

- 유연성 부족

  - 최대 비트 수를 미리 예측해야 하며, API를 수정하지 않으면 확장이 어려움.

- 순회 어려움

  - 비트 필드 내부의 모든 원소를 순회하기 까다로움.

- 정수 열거 상수의 단점을 그대로 계승
  - 타입 안전성을 보장하지 못하며, 상수 값이 변경되면 오류 발생 가능.

#### 2. EnumSet 사용의 장점

EnumSet 정의 및 예제

- EnumSet은 열거 타입 상수 값으로 구성된 집합을 효과적으로 표현하는 클래스.

- 내부적으로 비트 벡터로 구현되어 비트 필드와 동등한 성능을 제공하며, Set 인터페이스를 완벽히 구현.

```java

import java.util.Set;
import java.util.EnumSet;

public class NewText {
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }

    public void applyStyles(Set<Style> styles) {
        // EnumSet 처리 로직
    }
}

NewText text = new NewText();
text.applyStyles(EnumSet.of(NewText.Style.BOLD, NewText.Style.UNDERLINE));

```

#### 장점

- 가독성

  - 열거 타입 상수를 사용하여 값의 의미가 명확함.

    - 예: EnumSet.of(Style.BOLD, Style.UNDERLINE)

- 타입 안전성

  - 잘못된 타입의 값을 넘길 경우 컴파일 단계에서 오류 발생.

- 효율성

  - 내부적으로 비트 연산을 사용해 집합 연산을 효율적으로 처리.

- 유연성
  - Set 인터페이스를 구현하므로 다른 Set 구현체와 호환 가능.

#### EnumSet의 단점

- 불변 EnumSet을 만들 수 없음.

  - 해결 방안:

    - 구글 Guava: ImmutableEnumSet 제공.

    - Java Collections: Collections.unmodifiableSet 사용.

#### 3. EnumSet을 사용한 최적의 구현

- 코드 예제:

```java

import java.util.Collections;
import java.util.EnumSet;
import java.util.Set;

public class NewText {
    public enum Style { BOLD, ITALIC, UNDERLINE, STRIKETHROUGH }

    public void applyStyles(Set<Style> styles) {
        // 처리 로직
    }

    public static Set<Style> immutableStyles(Set<Style> styles) {
        return Collections.unmodifiableSet(EnumSet.copyOf(styles));
    }
}

NewText text = new NewText();
Set<NewText.Style> styles = NewText.immutableStyles(EnumSet.of(NewText.Style.BOLD));
text.applyStyles(styles);

```

#### 4. 요약

- 비트 필드의 단점을 극복하고자 **EnumSet**을 사용하라.

- EnumSet은 비트 필드의 성능을 유지하면서 가독성과 타입 안전성을 보장한다.

- 가능하면 Set 인터페이스로 받는 습관을 들여 다양한 Set 구현체를 수용할 수 있도록 설계하라.

- 불변 집합이 필요하면 Guava 또는 Collections.unmodifiableSet을 활용하라.
