---
layout: post
title: Strategy Pattern
subtitle: Head First Design Patterns 01
date: 2020-06-08 00:00:00 +0300
description: # Add post description (optional)
tags: [DesignPattern, OOP]
categories: [DesignPattern, OOP]
---

## 학습 목표

- `interface` 개념을 이해한다.
- `delegate` 개념을 이해한다.
- `Strategy Pattern` 개념을 이해한다.

## 인터페이스

### 사전적 의미

- 키보드나 디스플레이 따위처럼 사람과 컴퓨터를 연결하는 장치

### Java에서의 의미

- 기능에 대한 `선언과 구현 분리`
- 기능을 사용할 수 있는 `통로`

```java
// 기능에 대한 선언
public interface AInterface {
    public void funcA();
}
```

```java
// 기능에 대한 구현
public class AinterfaceImpl implements Ainterface

    @Override
    public void funcA() {
        System.out.println("AAA");
    }
```

```java
public class Main {
    public static void main(String[] args) {
        Ainterface ainterface = new AinterfaceImpl();
        // 통로
        ainterface.funcA();
    }
}
```

## Delegate

### 사전적 의미

- `위임`하다
- 어떤 기능을 구현할 때, 그 책임을 다른 객체로 넘기는 것. 다른 객체의 기능을 빌려 사용하는 것을 delegate, 위임한다고 한다.

```java
public class AObject {
    Ainterface ainterface;

    public AObj(){
        ainterface = new AinterfaceImpl();
    }

    public void funcAA() {
        //위임한다.
        ainterface.funcA(); // == System.out.println("AAA");
        ainterface.funcA(); // == System.out.println("AAA");
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        AObject aObject = new AObject();
        aObject.funcAA();
    }
}
```

### Strategy Pattern

> 스트래티지 패턴(Strategy Pattern)에서는 알고리즘군을 정의하고 각각을 캡슐화하여 교환해서 사용할 수 있도록 만든다.  
> 스트래티지를 활용하면 알고리즘을 사용하는 클라이언트와는 독립적으로 알고리즘을 변경할 수 있다.

---

#### 첫번째 원칙

> 애플리케이션에서 달라지는 부분을 찾아내고, 달라지지 않는 부분으로부터 분리시킨다. 바뀌는 부분만 캡슐화하고 시스템의 유연성, 확장성을 높인다.  

예를 들면 모든 오리는 수영할 수 있다. 하지만 모든 오리가 날 수 없다. 이 때는 나는 행동을 분리하여 캡슐화 한다.

#### 두번째 원칙

>구현이 아닌 인터페이스(상위 형식)에 맞춰서 프로그래밍한다.  

- 구현에 맞춘 프로그래밍

```java
Dog d = new Dog();
d.bark();
```

- 인터페이스/상위 형식에 맞춘 프로그래밍

```java
Animal animal = new Dog();
animal.makeSound();
```

- 더 바람직한 방식

```java
a = getAnimal();
a.makeSound();
```

> 이 코드는 상위형식의 인스턴스를 만드는 과정을(new Dog()같은 방식) 직접 만들지않고, 구체적으로 구현된 객체를 실행시에 대입하는 것이다.  
> Animal의 하위 형식중 어떤 것이지 모르지만, makeSound()에 대해 올바른 행동만 할 수 있으면 된다.

#### 세번째 원칙

> 상속보다는 구성을 활용한다.  

여기서 `구성`이란, 예를 들어 오리라는 클래스는 `FlyBehavior`와 `QuackBehavior`의 `Interface`로부터 나는 행동과 꽥꽥거리는 행동을 `위임받는 행위`를 말한다.  
클래스는 행동을 상속받는 대신, 올바른 행동 객체로 구성되어 행동되어야 한다.  
이는 캡슐화 뿐만 아니라 동적으로 행동을 바꿀 수도 있게하고, 독립적으로 알고리즘을 변경할 수 있다.
