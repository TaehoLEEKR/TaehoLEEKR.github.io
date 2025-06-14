---
title: 코틀린 싱글톤 패턴 Object
date: 2025-06-14 19:00:00 +09:00
categories: [Spring Boot, Kotlin]
tags: [Kotlin, Spring boot]
---

! 코틀린으로 스프링부트 시스템 설계를 공부 중에 자바에서와 다르게 싱글톤 패턴을 Object 로 구현하여 사용하는 것을 확인 하여 학습함.

---

### 💡 Singleton 패턴과 `object` 키워드를 활용한 Kotlin 유틸리티 클래스 설계

---

### 1. **핵심 개념**

- `object` 키워드는 Kotlin에서 클래스의 단일 인스턴스를 제공하는 **Singleton 패턴**을 간단하게 구현할 수 있게 해준다.
- Singleton 패턴을 활용하면 전역적으로 관리가 필요한 객체를 효율적으로 사용할 수 있다.

---

### 2. **코드 설명**

```
object JsonUtil {

    private val json = Json {
        prettyPrint = true
        ignoreUnknownKeys = true
    }

    fun <T> encodeToJson(v: T, serializer: KSerializer<T>): String {
        return json.encodeToString(serializer, v)
    }

    fun <T> decodeToJson(v: String, serializer: KSerializer<T>): T {
        return json.decodeFromString(serializer, v)
    }
}

```

- `JsonUtil`은 JSON 직렬화(`encodeToJson`) 및 역직렬화(`decodeToJson`) 기능을 제공한다.
- 내부적으로 `kotlinx.serialization`의 `Json` 객체를 사용하며, 직렬화와 역직렬화 관련된 옵션(`prettyPrint`, `ignoreUnknownKeys`)을 설정한다.
- 반복적으로 객체를 생성하지 않아도 되도록 Singleton 패턴을 적용해 유틸리티 클래스를 설계했다.

---

### 3. **`object` 키워드를 사용한 이유**

1. **불필요한 인스턴스 생성 방지**
    - `object`는 Single Instance(싱글 인스턴스)를 보장해준다.
    - `JsonUtil`처럼 상태를 가지지 않는 클래스는 매번 객체를 생성할 필요가 없기 때문에 `object` 키워드가 적합하다.
2. **공통 설정의 재사용**
    - 동일한 JSON 직렬화 설정(prettyPrint, ignoreUnknownKeys)을 프로젝트 전역에서 일관되게 사용할 수 있다.
    - 이를 통해 중복된 설정 코드 작성을 방지할 수 있다.
3. **Kotlin 스타일의 유틸리티 클래스**
    - Kotlin은 Java의 `static` 메서드 대신 `object`를 활용한 정적 클래스 형태를 제공한다.
    - 유틸리티 함수들을 별도의 객체 생성 없이 호출할 수 있다.
4. **사용성의 편의성**
    - `object`로 구현하면 추가 인스턴스 생성 작업 없이 바로 호출할 수 있다.

```
val jsonString = JsonUtil.encodeToJson(myObject, MySerializer)
     val myObject = JsonUtil.decodeToJson(jsonString, MySerializer)

```

- 위 코드는 `object`가 아닌 일반 클래스로 구현했을 경우 객체를 생성해야 하므로 복잡해진다.

```
val jsonUtil = JsonUtil()
     val jsonString = jsonUtil.encodeToJson(myObject, MySerializer)
     val myObject = jsonUtil.decodeToJson(jsonString, MySerializer)

```

1. **Thread-Safe Singleton**
    - `object` 키워드는 Kotlin 컴파일러에 의해 **Thread-Safe Singleton**으로 보장된다.
    - 여러 스레드에서 동시에 호출하더라도 인스턴스가 하나만 생성되어 안전하다.

---

### 4. **왜 `class`가 아닌가?**

- `JsonUtil`은 상태를 가지지 않고, 단순히 JSON 직렬화 및 역직렬화와 같은 기능을 제공한다.
- 이런 경우 추가적인 인스턴스를 생성할 필요가 없다.
- `object`를 사용하면 더 간결하고 안전하며 효율적으로 구현할 수 있다.

---

### 5. **`JsonUtil`과 관련된 장점 정리**

- **Singleton**: 메모리 낭비를 줄이고, 단일 인스턴스를 활용할 수 있다.
- **재사용성**: JSON 설정을 반복하지 않고 프로젝트 전역에서 재사용할 수 있다.
- **Thread-Safe**: 동기화 문제 없이 안전하게 사용할 수 있다.
- **간결성**: 추가적인 코드 없이 바로 호출할 수 있다.

---

### 6. **결론**

`JsonUtil`을 `object`로 구현한 것은 최적화된 Singleton 패턴 활용과 유틸리티 클래스로서의 역할을 명확하게 하기 위해 설계되었다. Kotlin의 `object` 키워드는 간결하면서도 직관적인 방식으로 활용할 수 있어 매우 효율적인 설계 방식이다 .