---
title: "RxJava-Single"
date: 2021-03-01
categories: RxJava
---

### Single

Single 클래스는 Observable의 특수한 형태이다. Observable 클래스는 데이터를 무한하게 발행할 수 있지만 Single 클래스는 오직 1개의 데이터만 발행하도록 한정한다. 보통 결과가 유일한 서버 API를 호출할 때 유용하게 사용할 수 있다. 

----

#### 마블 다이어그램

![스크린샷 2021-03-01 오후 11.05.07](/Users/anminhee/Desktop/스크린샷 2021-03-01 오후 11.05.07.png)

마블 다이어그램에서 중요한 것은 데이터 하나가 발행과 동시에 종료된다는 점이다. 라이프 사이클 관점에서 보면 onNext() 와 onComplete() 함수가 onSuccess() 함수로 통합된 것이다. 따라서 Single 클래스의 라이프 사이클 함수는 onSuccess(T value) 함수와 onError() 함수로 구성된다.

---

#### Single 연산자를 통한 구성

Observable과 마찬가지로, Single도 다양한 연산자를 제공한다. 이 중 어떤 연산자들은 Observable과 Single을 섞어서 사용할 수 있도 Observable의 영역과 Single 영역을 연결하는 인터페이스 역할을 한다. 공식홈페이지에 가면 확인할 수 있다.

---

#### 출처

RxJava 프로그래밍 - 유동환, 박정준

[ReactiveX 공식 홈페이지](http://reactivex.io/documentation/ko/single.html)

