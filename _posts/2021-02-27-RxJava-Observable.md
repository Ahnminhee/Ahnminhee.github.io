---
title: "RxJava-Observable"
date: 2021-02-27
categories: RxJava
---

### Observable

Observable은 옵서버 패턴을 구현한다. 

#### Observer pattern

- 객체의 상태 변화를 관찰하는 관찰자 목록을 객체에 등록한다.

- 상태 변화가 있을 때마다 메서드를 호출하여 객체가 직접 목록의 각 옵서버에게 변화를 알려준다.
- 라이프 사이클은 존재하지 않으며 보통 단일 함수를 통해 변화만 알린다.
- ex) 사용자가 버튼을 누르면 버튼에 등록해 둔 onClick() 메서드를 호출해 원하는 처리를 하는 것

> Observable의 뜻
>
> 관찰자가 관찰하는 대상이라고 할 수 있다. 화이트헤드의 책에서 **Observed라는 단어가 관찰을 통해서 얻은 결과를 의미한다면 Observable은 현재 관찰되지 않았지만 이론을 통해서 앞으로 관찰할 가능성을 의미한다.** 라고 표현했다.



#### Observer Create

비동기와 병렬로 메소드를 호출한다.

1. 비동기 메소드 호출로 결과를 리턴받고 필요한 동작을 처리하는 메서드를 정의한다.  -> 이 메서드는 Observer 일부가 된다.
2. Observable로 비동기 호출을 정의한다.
3. 구독을 통해 Observer를 Observable 객체에 연결 시킨다.(동시에 Observable의 동작 초기화)
4. 메서드 호출로 결과가 리턴될 때마다 Observer의 메서드는 리턴 값 또는 Observable이 배출하는 항목들을 사용해서 연산을 시작한다.



#### Observable 알림

Subscribe 메서드를 통해 Observer와 Observable을 연결한다.

**onNext**

- Observable이 데이터의 발행을 알리며 기존의 옵서버 패턴과 같다.
- Observable은 새로운 항목을 배출할 때마다 이 메서드를 호출한다.
- Observable이 배출하는 항목을 파라미터로 전달 받는다.

**onCompleted**

- 모든 데이터의 발행을 완료했음을 알린다.
- onComplete 이벤트는 단 한 번만 발생하며, 발생한 후에는 더 이상 onNext 이벤트가 발생해선 안된다.

**onError**

- Observable에서 어떤 이유로 에러가 발생했음을 알린다. 
- onError 이벤트가 발생하면 이후에 다른 이벤트가 발생하지 않는다. 즉 Observable의 실행을 종료한다.
- onError 메서드는 오류 정보를 저장하고 있는 객체를 파라미터로 전달 받는다.

onNext는 0번 이상 호출 될 수 있으며 그 후에는 onCompleted 또는 onError 둘 중 하나를 마지막으로 호출한다.(두개다 호출하지 않음)



**구독 해지**

Rx 구현체 중에는 Subscriber라는 옵저버 인터페이스에서 unsubscribe라는 메서드를 제공한다. 현재 구독중인 Observable 중, 옵저버가 더 이상 구독을 원하지 않는 경우 이 메서드를 호출하여 구독을 해지할 수 있다.

unsubscribe은 연산자 체인을 통해 옵저버가 구독하고 있던 Observable들이 더 이상 항목들을 배출하지 못하도록 연결된 링크들를 끊어 버린다.



#### Hot or Cold Observable

**Hot Observable**

- 구독자가 존재 여부와 관계없이 데이터를 발행한다. 
- 여러 구독자를 고려할 수 있지만 구독자로서 Observable에서 발행하는 데이터를 처음부터 모두 수신할 것으로 보장할 수 없다.
- 즉 구독한 시점부터 Observable에서 발행한 값을 받는다.
- ex) 마우스 이벤트, 키보드 이벤트, 센서 데이터와 주식 가격 등

**Cold Obserble**

- Observable을 선언하고 just(), fromIterable() 함수를 호출해도 옵서버가 subscribe() 함수를 호출하여 구독하지 않으면 데이터를 발행하지 않음(lazy 접근법)
- Observable을 구독하는 옵저버는 Observable이 배출하는 항목 전체를 구독할 수 있도록 보장 받는다.
- ex) 웹 요청, 데이터베이스 쿼리와 파일 읽기 등

> 구독자가 여러 명이라는 것에 의미
>
> 데이터의 원천은 한 곳이지만 내가 최종적으로 원하는 결과 데이터가 여러 종류일 때는 각각을 구독자로 생각하면 좋다. 예를 들어 날씨 정보, 지역 정보, 시간 정보를 반환하는 경우 RxJava에서는 위의 세 가지 정보를 구독자라고 생각하면 편리하다.



#### Observable 연산자를 활용한 구성

Observable과 Observer는 연속된 이벤트를 처리하는데 있어서는 싱글 콜백보다 효과적인 방법을 제공한다. 연산자들은 Observable이 배출하는 연속된 항목들을 변환시키고, 결합하고, 조작하는 기능을 제공한다. 또한 비동기 시스템이 가진 중첩된 콜백 핸들러의 단점을 제거했다.

여러 연산자들은 [ReactiveX 공식 홈페이지](http://reactivex.io/documentation/ko/observable.html) 에서 확인할 수 있다.



#### 출처

RxJava 프로그래밍 - 유동환, 박정준

http://reactivex.io/documentation/ko/observable.html

