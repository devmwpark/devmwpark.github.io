---
layout: post
title:  "심플한 컬렉션 데이터 처리 - 자바8"
date:   2017-01-02
desc: "JDK8 - StreamAPI"
keywords: "java,stream,jdk8"
categories: [Java]
tags: [java, JDK8, StreamAPI]
---

----  

그간 업무를 해오면서 자주 접했던 일은 List 에서 내가 원하는 값을 찾고 원하는 값으로 이루어진 새로운 List를 만들어 내는 일을 제법 많이 했다.  
주어진 List 를 외부반복문 코드를 통해 순회 하면서 원하는 값을 찾고 새로운 List에 값을 저장하고 반환하는 반복적인 일이 많았다.  
이 로직을 처리하기 위해 `JDK8` 에서는 내가 원하는 로직을 처리하기 위해 어떻게 코드가 바뀌었는지를 이야기 해보려 한다.  

## **순회하는코드를 숨기자**

----

가령 나에게 10개의 숫자가 든 리스트가 주어졌다는 가정 하에 100 이상의 숫자만을 필요로 할 경우 기존에는 아래와 같이 처리 했을 것이다.  

```java
public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(33,42,101,20,234,500,55,89,110,21);
        List<Integer> resultList = new ArrayList<>();
        for (Integer number:numbers){
            if( number > 100 ){
                resultList.add(number);
            }
        }
        System.out.println(resultList);
    } 
```  
> numbers List에서 원하는 값으로 List를 재구성  

물론 눈으로 봐도 numbers List 에 100 이상의 숫자를 찾을 수 있지만 어디까지나 예제일뿐... 나에게 주어진 List에 어떤 숫자가 존재하는지 모른다는 가정 하에 내가 원하는 값으로 List를 재구성 하기 위해서는 위와같이 했을 것이다. 나는 그저 100이상의 숫자를 얻고 싶었을 뿐이고 그것을 얻기 위해 List를 직접 순회 하면서 조건에 해당하는 값을 직접 새로운 List에 옮겨담고 이를 얻었다. 그냥 List에게 100이상의 숫자를 달라고 할 수는 없을까?  

`JDK8` 에 `SteamAPI` 가 그 해답을 알려 주었다. `Stream` 은 아래와 같은 특징을 가지고 있다.  

* 주어진 컬렉션 데이터를 내부적으로 순회 하며 연속된 요소의 스트림을 얻는다.
* 스트림은 단 한번만 반복하며 탐색된 요소는 소비 한다.

새롭게 추가된 Stream 은 내부 반복을 지원 한다. stream 메서드를 호출 하는 것 만으로도 주어진 컬렉션 데이터로 부터 연속된 요소를 스트림으로 얻게 된다. 직접 외부반복문을 실행 할 필요가 없다는 이야기 이다.  

## **원하는 데이터 처리를 선언**

----

Stream이 제공 하는 모든 기능을 이번 포스팅에 담기엔 너무 많다. 더 많은 메소드 들은 [java.util.stream.Stream](http://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)에 정의 되어 있으니 참고 하면 좋을 것 같다. 이번 포스팅에서는 선언형 코드를 통해 기존에 데이터를 처리하는 방식이 얼마나 단순화 되는지 만을 살펴 볼것이다.

stream 메소드의 내부반복을 통해 모든 요소의 스트림을 얻게 되었고, filter 메소드를 이용해 내가 원하는 데이터 처리를 선언형 코드로 전달 할 수 있다.

```java
Stream<T> filter(Predicate<? super T> predicate);
```
> Stream 에서 제공하는 filter 메소드

전달인자로 Predicate 를 받는다. Predicate 는 test 메소드를 가진 함수형 인터페이스 이고 함수 디스크립터는 `(T) -> boolean` 이다. 이전 람다 포스팅 내용 중 함수 디스크립터를 알고 있다면 람다표현식으로 이를 간소화 하여 동작 자체를 넘겨주는 코딩이 가능 하다.

## **Stream 의 강력함!**

----

그리고 마지막으로 collect 를 통해 스트림에 누적된 요소를 특정 결과로 변환 시키면 내가 원하는 과정은 모두 이룰 수 있다. 아래는 최종적으로 `JDK8-StreamAPI`를 이용해 내가 원하는 결과값을 얻는 예제 이다.

```java
public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(33,42,101,20,234,500,55,89,110,21);
        List<Integer> resultList = numbers.stream()
                                          .filter(number -> number > 100)
                                          .collect(Collectors.toList());
        System.out.println(resultList);
    }
```
> Stream 을 이용해 원하는 결과 List를 취득하는 과정

아래와 같이 정리 할 수 있을 것 같다.

* stream 메소드의 호출만으로 이미 List의 모든 요소를 내부적으로 순회 할 수 있게 된다.
* filter 메소드를 통한 검사를 Lambda 표현식으로 선언하므로써 이후 변경 사항에 대한 유연한 대처도 되며, 데이터 처리에 대한 가독성이 명확해 진다.
* collect 메소드를 통해 누적된 요소를 내가 원하는 형태의 결과물로 받을 수 있다.

Stream 덕분에 더이상 내가 직접 외부반복을 통해 원하는 데이터 처리를 하고 결과물을 받는 일은 더이상 하지 않아도 될 것 같다. 그저 나는 어떤 컬렉션 데이터를 탐색 할 것이고, 어떤 데이터 처리를 할 것이고, 어떻게 결과물을 받을 것 이다 라는 것만 명확하게 선언해 준다면 조금더 간소화 된 코드로 복잡한 데이터 처리를 할 수 있게 될 것이다.