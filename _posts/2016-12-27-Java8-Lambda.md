---
layout: post
title:  "자바 8의 변화 - 람다"
date:   2016-12-28
desc: "JDK8 - Lambda"
keywords: "java,lambda,funtional"
categories: [Java]
tags: [devmwpark,lambda]
---

자바 8 에서는 새로운 개념이 도입되었다. 바로 메소드의 전달인자로 메소드 레퍼런스, 익명 함수를 포함한 모든 함수 를 전달 할 수 있게 되었다.
이중에 익명 함수 바로 람다에 대해서 이야기 해보려고 한다. syntax 에 대한 내용은 이미 다른 좋은 글들이 많으므로 다루지 않을것이고, 어떠한 경우에 람다를 사용하고 어떻게 응용해서 사용하면 좋을지에 대한 생각을 이야기 할 것 이다.


  
## **What is Lambda ?**  
---------------------  
자바 8에서 람다가 메소드가 아닌 함수로 불리우는 이유는 어느 한 클래스에 종속 되지 않으면서 전달인자 리스트, 바디, 결과 값 을 가지기 때문이다.
물론 이름도 가지고 있지 않기 때문에 익명 이다. 별도의 정의가 필요 없이 동작 자체를 다른 메소드의 전달인자로 전달 할 수 있다.

자바 8 이전에 익명 클래스와 비슷 한 개념이다. 다만 익명 클래스의 사용보다 형식적인 코드 들이 사라지고 람다만의 문법을 사용 하도록 하였다. 형식적인 코드가 줄어들고 개발자가 입력해야될 코드 양이 줄어 든다는 건 그만큼 생산적이고, 개발자의 실수로 버그를 만들어 낼 확률도 낮출 수 있다는 것이다. 

아래는 기존 익명클래스의 사용과 람다 표현식의 사용을 간단히 비교한 것이다.  

```java
public static void main(String[] args) {  
	new Thread(new Runnable() {
        @Override
        public void run() {
            System.out.println("Hello World");
        }
    }).start();
}  
```
> 자바 8 이전의 익명클래스를 이용한 Runnable 구현 및 실행

```java
public static void main(String[] args){
	new Thread(()->System.out.println("Hello World")).start();
}
```
> 자바 8 Lambda 표현식을 사용한 Runnable 동작 전달 실행

얼마나 많은 형식 코드들이 사라지고 깔끔한 코드가 되는지 눈에 보일 정도 이다. 물론 위의 예시는 간단한 동작을 람다 표현식으로 표현한 것일 뿐이며 실제로는 더 복잡한 형태로 람다 표현식을 사용 할 수 있다. 하지만 동작해야 하는 코드 가 복잡성을 떠나서 단 한번만 동작하는 코드 라면 별도의 메소드 정의없이 동작 자체를 다른메서드의 전달인자로써 사용 할 수 있다는 것 이다.  

그렇다면 람다 표현식은 언제 어디서나 아무런 제약 없이 사용 할 수 있는가? 그렇지 않다. 자바에서 람다 표현식을 사용하기 위해 사전에 필요한 것이 있다. 바로 람다 표현식을 사용 할 수 있는 시그니쳐를 가진 함수형 인터페이스 이다. 위 예제에서 봐서 알겠지만 Runnable Interface 역시 run 이라는 하나의 메소드를 가진 함수형 인터페이스 이다.

```java
@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```
> 함수형 인터페이스 Runnable 의 모습

`@FunctionalInterface` 가 바로 자바 8에서 함수형 인터페이스 임을 나타내는 새롭게 추가된 Annotation 이다. `@FunctionalInterface` 라는 Annotation이 있고 추상 메소드가 1개만 존재 한다면 바로 함수형 인터페이스 이다. 추상 메소드의 시그니처는 람다 표현식의 시그니처가 되는 것이고 이를 함수 디스크립터 라고 한다.  

가령 Runnable 의 추상 메서드 run 의 디스크립터는 `() -> void [전달인자가 없고, void를 반환]` 이 되는 것이고 이전 예제에서 사용한 람다 표현식 `() -> System.out.println("Hello World")` 는 run 메서드의 디스크립터와 일치 하기 때문에 정상적인 람다 표현식이 되는 것이다. 

이미 자바 8 라이브러리 설계자들은 [java.util.function](https://docs.oracle.com/javase/8/docs/api/java/util/function/package-summary.html) 패키지로 여러가지 함수형 인터페이스를 제공한다. 
함수 디스크립터만 알고 있다면 자바 8에서는 언제 어디서든 람다표현식으로 이를 간소화 하여 사용 할 수 있다.
