# 쓰레드 로컬 (ThreadLocal) 정리

## 1. 쓰레드 로컬의 개념

쓰레드 로컬(ThreadLocal)은 **각 쓰레드마다 별도의 저장소**를 제공하여, **다른 쓰레드와 데이터를 안전하게 구분**하는 기능을 제공합니다. 각 쓰레드는 독립적으로 데이터를 저장하고 조회할 수 있으며, 다른 쓰레드와 공유되지 않습니다.

### 예시:

**물건 보관 창구**:
여러 사람이 같은 물건 보관 창구를 사용하더라도, 창구 직원은 각 사용자마다 구분하여 물건을 보관합니다.  
쓰레드 로컬은 각 쓰레드마다 데이터를 저장하는 보관소를 제공하는 것과 비슷합니다.

---

## 2. 일반 변수 필드 vs. 쓰레드 로컬

### 일반 변수 필드:

- 여러 쓰레드가 같은 변수에 접근하면 **후속 쓰레드가 이전 쓰레드의 데이터를 덮어쓸** 위험이 있습니다.
- 예시: `thread-A`가 `userA`를 저장하고, `thread-B`가 `userB`를 저장하면 `userA` 값이 사라집니다.

### 쓰레드 로컬:

- 각 쓰레드는 **자기만의 내부 저장소**를 가지므로, 다른 쓰레드의 데이터와 **격리**됩니다.
- 예시: `thread-A`는 `userA`를, `thread-B`는 `userB`를 각각 안전하게 저장하고 조회할 수 있습니다.

---

## 3. 쓰레드 로컬 사용법

- **값 저장**: `ThreadLocal.set(xxx)`
- **값 조회**: `ThreadLocal.get()`
- **값 제거**: `ThreadLocal.remove()`

---

## 4. 쓰레드 로컬 사용 시 주의사항

### 문제 발생 예시:

#### 사용자A 저장 요청:

1. 사용자가 HTTP 요청을 보내면, **WAS(Web Application Server)**는 쓰레드 풀에서 하나의 쓰레드를 할당합니다.
2. `thread-A`가 할당되고, `userA` 데이터를 쓰레드 로컬에 저장합니다.

#### 사용자A 요청 종료:

1. HTTP 응답이 완료되면, `thread-A`는 쓰레드 풀에 반환됩니다.
2. 그러나 `thread-A`의 쓰레드 로컬에 있는 `userA` 데이터는 제거되지 않으면 여전히 남아 있습니다.

#### 사용자B 요청:

1. 새로 요청이 온 `userB`는 쓰레드 풀에서 `thread-A`를 할당받을 수 있습니다.
2. `thread-A`는 이전에 저장된 `userA` 데이터를 여전히 가지고 있으므로, `userB`가 조회할 때 잘못된 `userA` 데이터가 반환될 수 있습니다.

### 예방 방법:

- **`ThreadLocal.remove()`**를 사용하여 요청이 끝날 때 쓰레드 로컬에 저장된 데이터를 반드시 제거해야 합니다.
- 이를 통해 쓰레드가 재사용될 때 이전 데이터가 남아 있지 않도록 할 수 있습니다.

---

## 5. 결론

쓰레드 로컬은 쓰레드마다 **독립적인 저장소**를 제공하여, 다수의 쓰레드가 동시에 실행되는 환경에서도 데이터를 안전하게 다룰 수 있도록 도와줍니다.  
그러나 사용 후 반드시 **`remove()`**를 호출하여 데이터를 제거하지 않으면, 쓰레드 풀이 재사용되는 과정에서 이전 쓰레드의 데이터가 다른 쓰레드에 영향을 미치는 **심각한 문제가** 발생할 수 있습니다.
