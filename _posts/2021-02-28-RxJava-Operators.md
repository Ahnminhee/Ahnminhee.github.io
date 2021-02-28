---
title: "RxJava-Operators"
date: 2021-02-27
categories: RxJava
---

### Operators

ReactiveX를 지원하는 언어 별 구현체들은 다양한 연산자들을 제공한다. 이 중에는 공통적으로 제공되는 연산자도 있지만 반대로 특정 구현체에서만 제공하는 연산자들도 존재한다. 언어별 구현체들은 이미 언어에서 제공하는 메서드의 이름과 유사한 형태로 연산자의 네이밍 컨벤션을 유지하고 있다. 또한 직접 연산자를 구현할 수도 있다.

---

#### Create 

![create](/Users/anminhee/Desktop/image/create.png)

**직접적인 코드 구현을 통해 옵저버 메서드를 호출하여 Observable을 생성**

- 가장 기본적인 Observable 생성연산자로 옵저버 메소드를 호출

- 아이템을 발행하기 위해서는 onNext(), onError(), onCompleted()를 적절히 호출

- onError()와 onCompleted() 는 동시에 호출할 수 없으며 이 연산자 중 하나가 호출된 이후엔 observable의 어떠한 연산자도 호출되지 않아야 한다.

사용 시 주의 사항

- Observable이 구독 해지 되었을 때 등록된 콜백을 모두 해제 -> 메모리 누수 방지
- 구독자가 구독하는 동안에만 onNext, onComplete 이벤트 호출
- 에러가 발생했을 때는 오직 onError 이벤트로만 에러 전달

Ex) 

```java
Observable<Integer> source = observable.create(
  (observableEmitter<Integer> emitter) -> {
    emitter.onNext(100);
    emitter.onNext(200);
    emitter.onComplete();
  });

source.subscribe(System.out::println);
```



***

#### Just

![Just](/Users/anminhee/Desktop/image/Just.png)

**객체 하나 또는 객채집합을 Observable로 변환한다. 변환된 Observable은 원본 객체들을 발행**

- 인자로 넣은 데이터를 차례대로 발행하기 위하여 Observable 생성 (실제 데이터의 발행은 subscribe() 함수 호출시 시작)

- 자동으로 알림 이벤트 발생

- 인자로 1~10개의 값을 넣을 수 있지만 타입이 모두 같아야 한다.

- 발생한 데이터로 just() 함수를 거치면 입력한 데이터가 그대로 발행

Ex)

```java
public class Ex {
  public void emit(){
    Observable.just(1,2,3,4,5)
      .subscribe(System.out::println);
  }
}
```



***

#### Map

![Map](/Users/anminhee/Desktop/image/Map.png)

**Observable이 배출한 항목에 함수를 적용**

입력값을 어떤 함수에 넣어서 원하는 값으로 변환하는 함수

> ex) String -> Integer

Ex)

``` java
Function<String, String> getDiamood = ball -> ball + "<>";

String[] balls = {"1", "2", "3"};
Observable<String> source = Observable.fromArray(balls)
  .map(getDiamood);
source.subseribe(Log::i)
```



***

#### FlatMap

![flatMap](/Users/anminhee/Desktop/image/flatMap.png)

**하나의 Observable이 발행하는 항목들을 여러개의 Observable로 변환하고, 항목들의 배출을 차례차례 줄 세워 하나의 Observable로 전달**

- Map 함수를 조금 더 발전시킨 함수로 원하는 입력 값을 어떤 함수에 넣으면 Observable 반환

- 1:n or 1:1Observable 함수이다.

Ex)

```java
Function<String, Observable<String>> getDoubleDiamonds =
  ball -> Observable.just(ball + "<>", ball + "<>");

String[] balls = {"1","3","5"};

Observable<String> source = Observable.fromArray(balls)
  .flatMap(getDoubleDiamonds);
source.subscribe(Log::i)
```



***

#### Filter

![Filter](/Users/anminhee/Desktop/image/Filter.png)

**테스트 조건을 만족하는 항목들만 배출한다**

- Observable에서 원하는 데이터만 걸러내는 역할이다. 즉, 필요 없는 데이터는 제거하고 조건에 맞는 데이터만 filter 함수를 통과하게 된다.

- 간단한 수식을 적용하는데 사용할 수 있다.

Ex)

```java
Integer[] data = {"100", "34", "1", "35"};
Observable<Integer> source = Observable.fromArray(data)
  .filter(number -> number % 2 == 0);
source.subscribe(System.out::println);
```



***

#### Reduce

![reduce](/Users/anminhee/Desktop/image/reduce.png)

**Observable이 배출한 항목에 함수를 순서대로 적용하고 함수를 연산한 후 최종 결과를 발행한다**

- 발행한 데이터를 모두 사용하여 어떤 최종 결과 데이터를 합성

- 수치와 관련된 계산 문제에 사용할 수 있다.

Ex)

``` java
String[] balls = {"1", "3", "5"};
Maybe<String> source = Observable.fromArray(balls)
  .reduce((ball1, ball2) -> ball2 + "(" + ball1 + ")");
source.subscribe(System.out::println);
```

-> 실행결과 : 5(3(1))



***

#### CombineLatest

![combineLatest](/Users/anminhee/Desktop/image/combineLatest.png)

**두 개의 Observable 중 하나가 항목을 배출할 때 배출된 마지막 항목과 다른 한 Observable이 배출한 항목을 결합한 후 함수를 적용하여 실행 후 실행된 결과를 배출한다**

- 2개 이상의 Observable을 기반으로 Observable 각각의 값이 변경되었을 때 갱신해주는 함수

- 마지막 인자인 combiner이 각 Observable을 결합하여 결과를 만들어주는 역할

- 하나의 Observable만 값을 발행하면 연산이 이루어지지 않는다.

Ex)

```java
@SchedulerSupport(SchedulerSupport.NONE)
public static <T1, T2, R> Observable<R> combineLatest(
  ObservableSource<? extends T1> source1,
  ObservableSource<? extends T2> source2,
  BiFunction<? super T1, ? super T2, ? extends R> combiner)
```



***

#### Merge

![merge](/Users/anminhee/Desktop/image/merge.png)

**복수 개의 Observable들이 배출하는 항목들을 머지시켜 하나의 Observable로 만든다**

- 입력 Observable의 순서와 모든 Observable이 데이터를 발행하는지 등에 관여하지 않고 어느 것이든 업스트림에서 먼저 입력되는 데이터를 그대로 발행

- Observable의 데이터 발행이 모두 개별의 스레드에서 이루어짐

Ex)

```java
String[] data1 = {"1", "3"};
String[] data2 = {"2", "4", "6"};

Observable<String> source1 = Observable.interval(0L, 100L, TimeUnit.MILLISECONDS)
  .map(Long::intValue)
  .map(idx -> data1[idx])
  .take(data1.lenght);
Observable<String> source2 = Observable.interval(50L, TimeUnit.MILLISECONDS)
  .map(Long::intValue)
  .map(idx -> data2[idx])
  .take(data2.lenght);

Observable<String> source = Observable.merge(source1, source2);

source.subscribe(Log::i);
CommonUtils.sleep(1000);
```



***

공식 문서에 들어가면 적절한 연산자를 사용할 수 있도록 도움을 주니 필요한 연산자가 있다면 들어가서 찾아보는 게 가장 좋은 방법인 것 같다.

![OperatorsTree](/Users/anminhee/Desktop/image/OperatorsTree.png)



#### 출처

RxJava 프로그래밍 - 유동환, 박정준

[ReactiveX 공식 홈페이지](http://reactivex.io/documentation/ko/operators.html)

