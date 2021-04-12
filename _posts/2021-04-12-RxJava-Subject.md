---
title: "RxJava-Subject"
date: 2021-04-12
categories: RxJava
---

### Subject 

---

Subject 클래스는 차가운 Observable을 뜨거운 Observable로 바꿔준다. Subject 클래스의 특성은 Observable의 속성과 구독자의 속성이 모두 있다는 점이다. Observable처럼 데이터를 발행할 수도 있고 구독자처럼 발행된 데이터를 바로 처리할 수도 있다.

RxJava에서 제공하는 Subject 클래스에는 AsyncSubject, BehaviorSubject, PublishSubject, ReplaySubject 등이 있다.



#### AsyncSubject

---

AsyncSubject 클래스는 Observable에서 발행한 마지막 데이터를 얻어올 수 있는 Subject 클래스이다. 완료되기 전 마지막 데이터에만 관심이 있으며 이전 데이터는 무시한다.

<img width="785" alt="AsyncSubject" src="https://user-images.githubusercontent.com/48676762/114408552-7b650980-9be4-11eb-87ef-2ba81199d6c6.png">

AsyncSubject 클래스는 지금까지와 다르게 마블 다이어그램의 아래쪽에 있는 구독자의 시간 표시줄이 여러 개인 것이 다르다. 

처리흐름

1. 처음 구독자가 subscribe() 함수를 호출한다.
2. 이후에 빨간색 원과 연두색 원이 발행된 후 두 번째 구독자가 subscribe() 함수를 호출한다.
3. 마지막으로 파란색 원이 발행되고 데이터 발행을 완료한다.

이때 완료되기 전까지는 구독자에게 데이터를 전달하지 않다가 완료됨과 동시에 첫 번째와 두 번째 구독자에게 마지막 데이터를 발행하고 종료한다.



```java
AsyncSubject<String> subject = AsyneSubject.create();
subject.subscribe(data -> System.out.println("Subscriber #1 => " + data));
subject.onNext("1");
subject.onNext("3");
subject.subscribe(data -> System.out.println("Subscriber #2 => " + data));
subject.onNext("5");
subject.onComplete();
```



먼저 AsyncSubject 객체인 subject는 정적 팩토리 함수인 create()로 생성한다. Subscribe() 함수를 호출하여 구독을 시작한다. 이 함수의 원형은 Observable과 같아 data -> { } 형식의 람다 표현식을 활용한다.

onNext() 함수를 호출하면 데이터를 발행한다. Subject 변수에서 String 타입을 지정했으므로 String 타입을 onNext()의 인자로 넣어야 한다. 3 을 발행한 후에는 두 번째 구독자가 subscribe() 를 호출한다.

'5' 를 발행한 후 마지막으로 onComplete() 함수를 호출했다. 이때 마지막으로 입력된 데이터가 구독자에게 최종으로 전달된다.



실행결과

```
Subscriber #1 => 5
Subscriber #2 => 5
```



#### BehaviorSubject

----

BehaviorSubject는 구독자가 구독을 하면 가장 최근 값 혹은 기본값을 넘겨주는 클래스이다. 예를 들어 온도 센서에서 값을 받아온다면 가장 최근의 온도 값을 받아오는 동작을 구현할 수 있다. 또한 온도를 처음 얻을 때는 초기값을 반환하기도 한다.

<img width="756" alt="BehaviorSubject" src="https://user-images.githubusercontent.com/48676762/114408592-89b32580-9be4-11eb-8f50-f7840a8917e8.png">



옵저버가 BehaviorSubject를 구독하기 시작하면 옵저버는 소스 Observable이 가장 최근에 발행한 항목(또는 아직 아무 값도 발행되지 않았다면 맨 처음 값이나 기본 값)의 발행을 시작하며 그 이후 소스 Observable에 의해 발행된 항목들을 계속 발행한다.

```Java
BehaviorSubject<String> subject = BehaviorSubject.createDefault("6");
subject.subscribe(data -> System.out.println("Subscriber #1 => " + data));
subject.onNext("1");
subject.onNext("3");
subject.subscribe(data -> System.out.println("Subscriber #2 => " + data));
subject.onNext("5");
subject.onComplete();
```



BehaviorSubject 클래스는 AsyncSubject 클래스와는 다르게 createDefault() 함수로 생성한다. 구독자가 subscribe() 호출시 발행한 값이 없다면 기본값을 대신 발행해야 하기 때문이다.

첫번째 구독자가 subscribe() 함수를 호출하면 기본값인 '6'을 발행한다. 그 후 차례대로 '1', '3' 발행한 후 두번 째 구독자가 '3' 을 구독하고 두개 구독자 모두 '5' 를 발행한다.



실행결과

```
Subscriber #1 => 6
Subscriber #1 => 1
Subscriber #1 => 3
Subscriber #2 => 3
Subscriber #1 => 5
Subscriber #2 => 5
```



#### PublishSubject

---

구독자가 subscribe() 함수를 호출하면 값을 발행하기 시작한다. AsyncSubject 클래스처럼 마지막 값만 발행하거나 BehaviorSubject 클래스처럼 값이 없을 때 기본값을 밣애하지도 않는다. 오직 해당 시간에 발생한 데이터를 그대로 구독자에게 전달받는다.

<img width="743" alt="PublishSubject" src="https://user-images.githubusercontent.com/48676762/114408663-9b94c880-9be4-11eb-9174-e56ca8a451fe.png">



```java
PublishSubject<String> subject = PublishSubject.create();
subject.subscribe(data -> System.out.println("Subscriber #1 =>" + data));
subject.onNext("1");
subject.onNext("3");
subject.subscribe(data -> System.out.println("Subscriber #2 =>" + data));
subject.onNext("5");
subject.onComplete();
```

PublishSubject 클래스는 별도의 기본값을 제공하지 않으므로 create()를 호출하여 생성한다.

첫 번째 구독자가 subscribe() 함수를 호출하면 '1', '3' 이라는 데이터를 발행한다. 두 번째 구독자가 subscribe() 함수를 호출하면 '5'를 발행하고 onComplete() 함수를 호출해 데이터 발행을 완료한다.



실행결과

```
Subscriber #1 => 1
Subscriber #1 => 3
Subscriber #1 => 5
Subscriber #2 => 5
```



#### ReplaySubject

---

Subject 클래스의 목적은 뜨거운 Observable을 활용하는 것인데 차가운 Observable처럼 동작하기 때문이다.

ReplaySubject 클래스는 구독자가 새로 생기면 항상 데이터의 처음부터 끝까지 발행하는 것을 보장해준다. 마치 테이프로 전체 내용을 녹음해두었다가 새로운 사람이 들어오면 정해진 음악을 처음부터 들려주는 것과 같다.

그러므로 모든 데이터 내용을 저장해두는 과정 중 메모리 누수가 발생할 가능성을 염두에 두고 사용할 때 주의해야 한다.

<img width="743" alt="ReplaySubject" src="https://user-images.githubusercontent.com/48676762/114408725-abaca800-9be4-11eb-92ad-0f5d3c4f5dd5.png">



```java
ReplaySubject<String> subject = ReplaySubject.create();
subject.subscribe(data -> System.out.println("Subscriber #1 =>" + data));
subject.onNext("1");
subject.onNext("3");
subject.subscribe(data -> System.out.println("Subscriber #2 =>" + data));
subject.onNext("5");
subject.onComplete();
```

ReplaySubject 클래스의 객체는 create() 를 호출하여 생성한다. 첫 번째 구독자가 subscribe() 함수를 호출하면 '1', '3' 이라는 데이터를 발행하며 두 번째 구독자에겐 그 전에 발행한 '1', '3' 을 전달받는다.



실행결과

```
Subscriber #1 => 1
Subscriber #1 => 3
Subscriber #2 => 1
Subscriber #2 => 3
Subscriber #1 => 5
Subscriber #2 => 5
```



#### 출처

---

RxJava 프로그래밍 - 유동환, 박정준

[ReactiveX 공식 홈페이지](http://reactivex.io/documentation/ko/single.html)

