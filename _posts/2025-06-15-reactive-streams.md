---
title: 리액티브 스트림즈
date: 2025-06-15 17:20:00 +09:00
categories: [Spring Boot, Spring WebFlux, Java , Kotlin]
tags: [Reactiv Programming]
---

## 🤔 Reactive Streams ?

리액티브 스트림즈 란? 앞서 [리액티브 프로그래밍](https://www.notion.so/1bfc0f395cc580319999e03b42e2b41b?pvs=21) 에 설명한 이벤트나 상황이 발생했을 때 반응을 잘하는 시스템을 말하는데 , 이 리액티브한 코드를 작성하기 위해 코드 구성을 용이 하게 해주는 표준 사양을 정의해 놓은 것을 말한다.

리엑티브 스트림즈를 구현한 구현체로는 

- RxJava
- Reactor
- Akka Streams
- Java 9 Flow API

있다.

살펴 볼 구현체는 Spring Framework 와 가장 궁합이 잘 맞는 Reactor 에 대해 정리 할 예정이다.

---

## 리액티브 스트림즈 구성요소

- 리액티브 스트림즈를  구현해야 되는 API 컴포넌트에는 Publisher, Subscriber, Subscription, Processor가 있다.
- Publisher
    - 데이터를 생성하고 통지하는 역할을 한다.
- Subscriber
    - 구독한 Publisher로부터 통지된 데이터를 전달받아 처리하는 역할
- Subscription
    - Publisher에 요청할 데이터의 개수를 지정하고, 데이터의 구독을 취소하는 역할
- Processor
    - Publisher, Subscriber의 기능 모두를 가지고 있습니다. Subscriber로서 다른 Publisher를 구독할 수 있고, Publisher로서 다른 Subscriber가 구독할 수 있다.

![학습용 이미지.png](attachment:1539079f-dfc0-4378-ae2f-21a0de9fda1f:학습용_이미지.png)

[스프링으로 시작하는 리액티브 프로그래밍 : 알라딘](https://www.aladin.co.kr/shop/wproduct.aspx?ItemId=314200532&start=pgooglemc)

1. 먼저 Subscriber(구독자) 전달받을 데이터를 구독합니다 (**subscribe**)
2. 다음으로 Publisher(발행자)는 데이터를 통지(발행 , 게시, 방출…)할 준비가 되었음을 Subscriber 에 알립니다(**onSubScribe**)
3. Publisher(발행자)가 데이터를 통지할 준비가 되었다는 알림을 받은 SubScriber는 전달 받기를 원하는 데이터의 개수를 Publisher에게 요청합니다.(**Subscription.request**)
4. 다음으로는 Publisher는 Subscriber 로부터 요청받은 만큼의 데이터를 통지합니다.(onNext)
5. 이렇게 Publisher와 Subscriber 간에 데이터 통지, 데이터 수신, 데이터 요청의 과정을 반복하다가 Publisher가 모든 데이터를 통지하게 되면 마지막으로 데이터 전송이 완료되었음을  Subscriber 에게 알립니다. (**onComplete**)
    1.  만약에 Publisher가 데이터를 처리하는 과정에서 에러가 발생하면 에러가 발생했음을 Subscriber 에게 알립니다. (**onError**) 

⭐ Subscriber 가 Subscription.request 를 통해 왜 굳이 데이터를 요청 개수를 지정하는가? 

그림 상으로는 마치 스레드에 동기적으로 상호작용 하는 것처럼 보이지만 실제로는 다른 스레드에서 비동기적으로 상호작용 하는 경우가 대부분이다.

Publisher 가 통지하는 속도보다 통지 된 데이터를 Subscriber가 처리 하는 속도보다 빠르면 처리를 기다리는 데이터는 쌓이게 되고,

 이는 결과적으로 시스템 부하가 커지는 결과를 낳게 되어 개수를 제어하는 것이다.!

---

## 리액티브 스트림즈 컴포넌트

```kotlin
public interface Publisher<T> {
    public void subscribe(Subscriber<? super T> s);
}
```

Publisher 인터페이스는 간단하다.
 subscribe 메서드는 파라미터로 전달받은 Subscriber를 등록하는 역할을 한다.

- 왜 Subscriber 가 Publisher 에 subscribe가 정의 되어 있는가???
    
    
    ~~아주 짧게 kafka 에 대해 찍먹… 해본 사람으로서 책에 나온 내용에 대해 의문점에 대해 생각 하게 되었다.~~
    
    kafka 같은 메시지 기반의 시스템에서 Pub/Sub 모델을 이해하고 있다면 의문이 드는게 당연하다. 리액티브 스트림즈에서 Publisher/Subscriber 와 Kafka에서의 의미가 조금 다르다
    
    Kafak의 경우 Publisher 와 Subscriber 중간에 메시지 브로커가 있고 이 브로커 내에 여러 개의 토픽(Topic) 이 존재 하는데 , Publisher 와 Subscriber 는 브로커에 있는 특정 토픽을 바라보는 구조로 이루어 져 있다. Kafka 에서의 각각 브로커 내의 특정 토픽만 바라보면되기 때문에 데이터를 전송하기만 하면 되고, Sub 는 특정 토픽을 구독하고 해당 토픽에 전달되는 메시지 데이터를 전달 받기만 하면된다. 이는 Publisher / Subscriber 의 느슨한 결합 구조라고 볼 수 있다.
    

```kotlin
public interface Subscriber<T> {
    public void onSubscribe(Subscription s);
    public void onNext(T t);
    public void onError(Throwable t);
    public void onComplete();
}
```

- onSubscribe 메서드는 구독 시작 시점에 어떤 처리를 하는 역할을 합니다. 처리는 Publisher에게 요청할 데이터의 개수를 지정하거나 구독을 해지하는 것을 의미하는데, 이것은 onSubscribe 메서드의 파라미터로 전달된 Subscription 객체를 통해서 이루어집니다.
- onNext 메서드는 Publisher가 통지한 데이터를 처리하는 역할을 합니다.
- onError 메서드는 Publisher가 데이터 통지를 위한 처리 과정에서 에러가 발생했을 때 해당 에러를 처리하는 역할을 합니다.
- onComplete 메서드는 Publisher가 데이터 통지를 완료했음을 알릴 때 호출되는 메서드입니다.

```
public interface Subscription {
    public void request(long n);
    public void cancel();
}
```

- Subscription 인터페이스의 코드는 Subscriber 가 구독한 데이터의 개수를 요청하거나 또는 데이터 요청의 취소 즉 구독 해지를 의미한다.

```
public interface Processor<T, R> extends Subscriber<T>, Publisher<R> {
}
```

- Processor 는 Publisher 와 Subscriber 의 기능을 모두 가지고 있는 상속 인터페이스 이다.

---

## 리액티브 스트림즈 관련 용어

- Signal
    - Pusblisher 와 Subscriber 간에 주고받는 상호작용
    - `onSubscribe`, `onNext`, `onComplete`, `onError`, `request`, `cancel` 이 Subscriber 가 Publisher 에게 보내는 Signal 이라고 볼 수 있다.
- Demand
    - Demand 는 사전적으로 ‘수요 , 요구’ 등을 의미하는데 리액티브 스트림즈에서도 이와 비슷하다.
    - Publisher가 아직 Subscriber에게 전달하지 않은 데이터 중 Subscriber가 요청한 데이터를 말한다.
- Emit
    - Emit 은 사전적으로 ‘통지, 발행 , 게시 , 방출 ‘의 의미로 데이터를 내보내다. 라고 이해 할 수 있다.
    - 데이터를 내보내는 의미
- Upstream/Downstream
    - Upstream은 위로 흐르는 것을, Downstream은 아래로 흐르는 것을 의미합니다. 흐르는 주체는 데이터입니다.
    
    ```kotlin
    Flux.just(1, 2, 3, 4, 5, 6)
        .filter(n -> n % 2 == 0)
        .map(n -> n * 2)
        .subscribe(System.out::println);
    ```
    
    리액티브 프로그래밍 코드를 살 펴 봤을 때, 
    
    Flux.just () 메서드 호출을 통해 반환된 Flux를 기준으로 Flux.just().filter() 메서드 호출을 통해 반환된 Flux는 하위이므로 Downstram 이다.
    
     반대로 Flux.just().filter() 메서드 호출을 통해 반환된 Flux를 기준으로 하면 Flux.just () 메서드 호출을 통해 반환된 Flux는 Upstream 이다.
    
    이때, 메서드가 하나로 연결된 것처럼 보이는 것을 메서드 체인 이라고 한다.
    
- Sequence
    - Sequence는 Publisher가 emit하는 데이터의 연속적인 흐름을 정의해 놓은 것 자체를 의미합니다.
    - Sequence는 emit 하고 filter 메서드를 통해서 필터링 한 후, map 메서드를 통해 변환하는 이러한 과정을 Sequence 라고 부른다.
- Operator
    - 연산자를 의미하는데,위 코드에 체이닝 메서드(just,filter,map) 을 리액티브 프로그래밍에서는 연산자라고 부릅니다.
- Source
    - 리액티브 스트림즈 또는 리액티브 프로그래밍 관련 문서에서 Source라는 용어는 대부분 '최초의' 라는 의미로 사용됩니다. 비슷한 의미로 Original 이라는 용어도 사용된다.

---

## 리액티브 스트림즈의 구현 규칙

| 번호 | 규칙 |
| --- | --- |
| 1 | **Publihser 가 Subscriber 에게 보내는 onNext signal 의 총 개수는 항상 해당 Subscriver 의 구독을 통해 요청된 데이터의 총 개수보다 더 작거나 같아야한다.** |
| 2 | **Publihsersms는 요청된 것보다 적은 수의 onNext signal을 보내고 onComplete 또는 onError를 호출하여 구독을 종료할 수 있다.** |
| 3 | **Publihser의 데이터 처리가 실패하면 onError signal 을 보내야한다.** |
| 4 | **Publihser의 데이터 처리가 성공적으로 종료되면 onComplete signal 을 보내야한다.** |
| 5 | **Publihser가 Subscriber에게 onError 또는 onComplete signal 을 보내는 경우 해당 Subscriber의 구독은 취소된 것으로 간주 되어야한다.** |
| 6  | 일단 종료 상태 signal을 받으면(onError, onComplte) 더 이상 signal이 발생 되면 안된다. |
| 7 | 구독이 취소되면, Subscriber는 결국 signal을 받는 것을 중지 되어야 한다. |