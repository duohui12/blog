---
layout: post
title: Tell Don't Ask (TDA) 원칙
tags:
  - [OOP]
---

<br>

요즘 회사 내부에서 진행중인 스프링 스터디에 참여중이다. 이번주에는 OOP에 대해서 정리하는 시간을 가졌는데 그 중 나왔던 TDA(Tell Don't Ask) 개념이 부족해서 [마틴파울러가 설명하는 Tell Don't Ask (TDA)](https://martinfowler.com/bliki/TellDontAsk.html) 문서를 보면서 개념에 대해 정리해보았다. 

<br>

## Tell Dont Ask

> Tell-Don't-Ask is a principle that helps people remember that object-orientation is about bundling data with the functions that operate on that data. It reminds us that rather than asking an object for data and acting on that data, we should instead tell an object what to do. This encourages to move behavior into an object to go with the data.

Tell-Don't-Ask는 객체지향이 데이터와 그 데이터를 조작하는 함수들을 묶어줌을 기억하는데 도움을 주는 원칙이다. 이 원칙은 객체에게 데이터를 요청하고 그 데이터를 가지고 행동하기 보다, 객체에게 무엇을 할지 말해주도록 한다. 이 원칙은 행동이 데이터와 같이 있도록 객체안으로 이동시킨다. 

<br>

<div>
  <img src="https://martinfowler.com/bliki/images/tellDontAsk/sketch.png" alt="sample-image" style="zoom:50%;"/>
  <p>이미지출처 : martinfowler.com</p>
</div>

<br>

예제로 살펴보자. 우리는 특정 값을 모니터링하고, 그 값이 임계치를 넘어가면 알람을 울려야 한다고 가정하자. 먼저 <b>Ask</b> 스타일은 아래와 같을 것이다.

```java
class AskMonitor{
	...
  private int value;
  private int limit;
  private boolean isTooHigh;
  private String name;
  private Alarm alarm;
  
  public AskMonitor (String name, int limit, Alarm alarm) {
    this.name = name;
    this.limit = limit;
    this.alarm = alarm;
  }
  
  public int getValue() {return value;}
  public void setValue(int arg) {value = arg;}
  public int getLimit() {return limit;}
  public String getName()  {return name;}
  public Alarm getAlarm() {return alarm;}
  
 ...
}  
```

```java
AskMonitor am = new AskMonitor("Time Vortex Hocus", 2, alarm);
am.setValue(3);
if (am.getValue() > am.getLimit())   //객체에게 데이터를 요청하고
  am.getAlarm().warn(am.getName() + " too high"); //그 데이터를 가지고 행동한다.
```

<br>

<b>Tell Don't Ask</b> 는 다르다. 이 방식은 위에서 작성한 행동을 모니터 객체 안에 넣도록 한다. 모니터 객체에게 특정 값을 셋팅하고, 그 값이 임계치를 넘어가면 알람을 울려줘 라고 시킨다. 

```java
class AskMonitor{
  ...
    
  public void setValue(int arg) {
      value = arg;
      if (value > limit) alarm.warn(name + " too high");
  }
}
```

```java
TellMonitor tm = new TellMonitor("Time Vortex Hocus", 2, alarm);
tm.setValue(3);  //객체에서 데이터를 가져오고 로직을 실행하는게 아니라, 객체가 어떤 작업을 할지 말해준다!
```

<br>

> Many people find tell-don't-ask to be a useful principle. One of the fundamental principles of object-oriented design is to combine data and behavior, so that the basic elements of our system (objects) combine both together. This is often a good thing because this data and the behavior that manipulates them are tightly coupled: changes in one cause changes in the other, understanding one helps you understand the other. Things that are tightly coupled should be in the same component. Thinking of tell-don't-ask is a way to help programmers to see how they can increase this co-location.

많은 사람들이 tell-don't-ask는 유용한 원칙이라고 한다. 객체지향 디자인의 기본적인 원칙 중 하나는 데이터와 행위를 결합시켜서, 객체의 기본 요소들을 함께 결합시키는 것이다. 이것은 데이터와 데이터를 조작하는 동작이 타이트하게 결합되어 있기 때문에 종종 유용하다: 하나의 변화는 다른 하나의 변화를 야기하고, 하나를 이해하면 다른 하나를 이해하는데 도움이 된다. 강하게 결합하는 것들은 같은 컴포넌트 안에 있어야 한다. tell-don't-ask 원칙은 개발자가 co-location(data와 behavior를 같이 두는것)을 늘리는 방법을 찾는데 도움이 될것이다. 

<br>

> But personally, I don't use tell-dont-ask. I do look to co-locate data and behavior, which often leads to similar results. One thing I find troubling about tell-dont-ask is that I've seen it encourage people to become [GetterEradicators](https://martinfowler.com/bliki/GetterEradicator.html), seeking to get rid of all query methods. But there are times when objects collaborate effectively by providing information. A good example are objects that take input information and transform it to simplify their clients, such as using [EmbeddedDocument](https://martinfowler.com/bliki/EmbeddedDocument.html). I've seen code get into convolutions of only telling where suitably responsible query methods would simplify matters 1. For me, tell-don't-ask is a stepping stone towards co-locating behavior and data, but I don't find it a point worth highlighting.

하지만, 나는 개인적으로 tell-don't-ask를 사용하지 않는다. 나는 데이터와 행동을 같은 위치에 두려고 하는데, 이는 종종 같은 결과로 이어진다. tell-don't-ask원칙을 사용할 때 내가 발견한 문제중 하나는 그것이 사람들로 하여금 모든 쿼리 메서드를 제거해버리는, GetterEradicators(?) 가 되도록 장려한다는 것이다. 하지만 객체가 정보를 제공하여 효과적으로 협업하는 경우도 있다. 좋은 예는 EmbeddedDocument를 사용하는 것처럼, 입력 정보를 가져와서 이를 단순화하기 위해 변환하는 객체이다. 나는 적절한 쿼리 메소드가 문제들을 단순화하는 부분에 telling을 사용해서 코드가 복잡해지는 걸 본적이 있다. 나에게는, tell don't ask 원칙이 데이터와 행동을 함께 위치시키는 것으로 나아가는 디딤돌이지만, 강조할 가지가 있는 부분은 아니다. 

<br>

## 느낀점

TDA는 객체지향의 핵심 컨셉 중 하나인 <b>캡슐화</b>를 도와주는 원칙인것 같다. 재밌는 점은 이 글의 저자는 Tell-Don't-Ask 원칙에 대해 친절하게 설명하고, 마지막 문단에서 ''근데 난 이거 안써' 라고 말한다. 반전 주의 ^0^ 사실 마지막 문단이 가장 이해하기 힘들었는데, tell don't ask 원칙이 만능 해결책은 아니라는 것을 말하는 것 같다. 때로는 ask 방법처럼 데이터를 요청하고 적절한 쿼리문을 작성함으로 문제를 단순화 시킬수도 있는것 같다. 이 글을 읽으면서 또 느낀점은 모든 상황에 100% 적용되는 원칙은 없다는 것이다. 개발자는 tda 원칙같은 배경지식을 끊임없이 학습하고 상황에 맞게 <b>trade-off</b> 하는 능력이 필요하다고 생각한다. 



