---
title: Switch~case (Java 12 🔺)
date: 2025-06-07 20:05:00 +09:00
categories: [java, spring boot]
tags: [java]
---

# Switch~case (Java 12 🔺)

---

❓ Java 17 버전과 IntelliJ로 개발을 진행하던 중, 평소 잘 쓰던 Switch 문에서 경고나 조언을 의미하는 노란색 의미심장한 … 밑줄이 생긴 것을 발견했다. 이유를 확인해보니 Java 12 버전 이상부터 Switch~case 문의 문법이 개선되어 알려주는 것이었다.   

늘 잘 쓰던 Switch 문에 개선 방식이라 .. 신선했다…

이참에 switch~case의 람다식 과  yield 방식에 대해 학습하려 한다.

---

## 기존 Switch 문 (JAVA 11 버전까지의 방식)

```java
String day = "MONDAY";

int dayNumber;
switch (day) {
    case "MONDAY":
        dayNumber = 1;
        break;
    case "TUESDAY":
        dayNumber = 2;
        break;
    default:
        dayNumber = -1;
}
System.out.println(dayNumber); 
```

기존 평소대로 switch 문을 사용하여 개발을 할 경우 선언과 break , default 를 잘 선언 하여 사용 해야 했다.

---

## 🤔 [새로운 Switch 표현식 (Java 12 + )](https://www.baeldung.com/java-yield-switch)

```java
String day = "TUESDAY";

int dayNumber = switch (day) {
    case "MONDAY" -> 1;
    case "TUESDAY" -> 2;
    default -> {
        System.out.println("Unknown day");
        yield -1; 
    }
};

System.out.println(dayNumber); 
```

새로운 문법 에서는 → 문법이 도입 되었고 case 를 표현 하는 방식이 간결해 졌다 . 또한 yield 키워드를 통해 return 하는 식으로도 사용 가능 하여 선언식 이 간결해 진 것 같다.

---

## yield 키워드

위 예시 처럼 화살표 문법은 시각적으로 쉽게 이해 할 수 있지만 새로운 키워드 yield 에 대해서는 조금 더 알아봐야 할 것 같다.

예시 1) 

```java
int result = switch (value) {
    case 1 -> 100;          // 간단한 케이스
    case 2 -> 200;          // 한 줄 반환
    default -> 0;           // 기본 값
};
```

예시 2)

```java
int result = switch (value) {
    case 1 -> {
        System.out.println("case 1");
        yield 101; // 101 반환
    }
    case 2 -> 202; // 간단한 반환
    default -> {
        System.out.println("디폴트 케이스");
        yield 0; // 기본값 반환
    }
};
```

기존에 사용하는 switch 문은 int result ~ 식으로 switch 문을 선언 할 수 없었지만  12+ 버전 이상 부터 선언 이후 → 와 yield 로 선언 후 안전하게 리턴을 받을 수 있어 보인다. 

간단하게 생각 해보면 yeild 는 Return 을 처리 해 주는 것으로 보이며 , 복잡한 블럭 처리에서는 yield 로 명시적 으로 사용 처리가 가능해 보인다.

---

## 결론

| Java 11 - | Java 12 +  |
| --- | --- |
| break 를 사용 하여 블럭의 흐름을 종료해야함. | 암묵적인 종료 (→ 함수) |
| fall-through 문제가 발생 할 수 있음 | fall-through 의 문제가 불가능 해지고 , 안전해짐. |
| 문(statement) 중심 | 표현식() 이 가능하여 조합이 가능하다 |
| 코드가 장황 해짐. | 간결성과 가독성이 높아짐 |
| case 에 값을 반환 할 수 없음 | yield 키워드를 사용하여 반환이 가능해짐 |


<br>
<br>


기존에 사용하던 switch 문은 break 를 까먹거나 의도 되지 않은 fall-through 가 발생 할 수 있었다.

이 부분이 가장 큰 장점으로 보이며 또한 리턴이 가능하다는 부분에서 새로운 표현이 가능해 진 것 같다.
