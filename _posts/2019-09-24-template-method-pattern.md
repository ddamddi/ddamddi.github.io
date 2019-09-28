---
layout: post
title: "[Design Pattern] 템플릿 메소드 패턴(Template Method Pattern)이란 ??"
description: ""
modified: 2019-09-24
categories: study
tags: design_pattern
comments: true
---

### 템플릿 메소드 패턴이란? (알고리즘의 캡슐화 !!)
- 알고리즘을 캡슐화하고 서브 클래스에서 필요할 때 마다 가져다 쓸 수 있도록 하는 패턴
- 템플릿 메소드 패턴에서는 메소드로 알고리즘의 골격(틀)을 정의한다.
- 템플릿 메소드를 이용하면 알고리즘의 구조는 유지하면서 서브클래스에서 특정 단계를 재정의할 수 있다.

###  Examples

커피를 만드는 *Coffee* 클래스와 홍차를 만드는 *Tea* 클래스가 있다고 하자.  커피와 홍차를 만드는 과정은 각각 *prepareRecipe( )* 메소드에 정의되어있다. 

__*Coffee* 클래스__
```java
public class Coffee {
	void prepareRecipe(){
		boilWater(); // 물을 끓인다.
		brewCoffeeGrinds(); // 끓는 물에 커피를 우려낸다. 
		pourInCup(); // 커피를 컵에 따른다.
		addSugarAndMilk(); // 설탕과 우유를 넣는다.
	}
	public void boilWater() {
		Sysetm.out.println("물을 끓이는 중...");
	}
	public void brewCoffeeGrinds() {
		Sysetm.out.println("커피를 우려내는 중...");
	}
	public void pourInCup(){
		System.out.println("컵에 따르는 중...");
	}
	public void addSugarAndMilk(){
		System.out.println("설탕과 우유를 추가하는 중...");
	}
}
```	


__*Tea* 클래스__
```java
public class Tea {
	void prepareRecipe() {
		boilWater(); // 물을 끓인다.
		steepTeaBag(); // 끓는 물에 차를 우려낸다.
		pourInCup(); // 차를 컵에 따른다.
		addLemon(); // 레몬을 추가한다.
	}
	public void boilWater(){
		Sysetm.out.println("물을 끓이는 중...");
	}
	public void steepTeaBag(){
		Sysetm.out.println("끓는 물에 차를 우려내는 중...");
	}
	public void pourInCup(){
		System.out.println("컵에 따르는 중...");
	}
	public void addLemon(){
		System.out.println("레몬을 추가하는 중...");
	}
}
```
이때 *boilWater()* 와 *pourInCup()* 메소드는 커피 클래스와 홍차 클래스에서 완전히 중복된다. 따라서 중복되는 부분을 다음과 같이 추상화 시킬 수 있다.

<img src="https://github.com/ddamddi/ddamddi.github.io/blob/master/assets/img/tmp_uml.jpg?raw=true">

- *CaffeineBeverage*라는 수퍼클래스를 만들어 중복되는 *boilWater()* 와  *pourInCup()* 메소드를 정의했습니다.
- *prepareRecipe()* 메소드는 서브클래스마다 다르기 때문에 *Abstract Method*로 선언하여 서브클래스에서 오버라이드하여 구현할 수 있도록 합니다. 
- *Coffee*, *Tea* 와 같은 서브클래스에서는 각각 만드는 방법을 구현합니다.

### *prepareRecipe()* 추상화
 *Coffee* 클래스와 *Tea* 클래스를 만드는 순서를 다시 한번 살펴보면 만드는 방법의 알고리즘이 다음과 같이 똑같다는 것을 알 수 있습니다.
 ```
 (1) 물을 끓인다.
 (2) 뜨거운 물을 이용하여 커피 또는 홍차를 우려낸다.
 (3) 만들어진 음료를 컵에 따른다.
 (4) 각 음료에 맞는 첨가물을 추가한다.
 ```

<img src="https://github.com/ddamddi/ddamddi.github.io/blob/master/assets/img/tmp_cmp.jpg?raw=true">

새로운 *prepareRecipe()* 메소드를 *CaffeineBeverage* 수퍼클래스에 정의해보면 다음과 같습니다.
```java
public abstract class CaffeineBeverage{
	final void prepareRecipe() {
		boilWater(); // 물을 끓인다.
		brew(); // 뜨거운 물을 이용하여 커피 또는 홍차를 우려낸다.
		pourInCup(); // 차를 컵에 따른다.
		addCondiments(); // 각 음료에 맞는 첨가물을 추가한다.
	}
	abstract void brew();
	abstract void addCondiments();
	public void boilWater() {
		System.out.println("물 끓이는 중...");
	}
	public void pourInCup() {
		System.out.println("컵에 따르는 중...");
	}
}
```
- *prepareRecipe()* 메소드를 *final*로 선언하여 서브클래스에서 오버라이드하여 음료 만드는 순서를 변경할 수 없도록 합니다.
- *brew()*와 *addCondiments()*는 추상메소드로 선언하여 서브클래스에서 알아서 정의할 수 있게합니다.

다음으로 *Coffee* 클래스와  *Tea* 클래스를 다시 정의해보면 다음과 같이 정의할 수 있습니다.
```java
public class Coffee extends CaffeineBeverage {
	public void brew() {
		Sysetm.out.println("필터로 커피를 우려내는 중...");
	}
	public void addCondiments(){
		System.out.println("설탕과 우유를 추가하는 중...");
	}
}

public class Tea extends CaffeineBeverage{
	public void brew() {
		Sysetm.out.println("차를 우려내는 중...");
	}
	public void addCondiments() {
		System.out.println("레몬을 추가하는 중...");
	}
}
```

<img src="https://github.com/ddamddi/ddamddi.github.io/blob/master/assets/img/tmp_uml2.jpg?raw=true">

수퍼클래스 *CaffeineBeverage*의 *prepareRecipe()*를 *템플릿 메소드 (Template Method)* 라고 한다.
- 어떤 알고리즘에 대한 템플릿(틀) 역할을 한다. 위와 같은 경우에는 카페인이 든 음료를 만들기 위한 템플릿이다.
- 템플릿 내에서 알고리즘의 각 *step*은 *method*로 표현됩니다.
- 중복되는 *step*의 *method*는 수퍼클래스 내부에서 처리되고, 서브클래스에서 구현해야 할 메소드는 *abstract method*로 선언되어 있습니다.

*템플릿 메소드 (Template Method)* 에서는 알고리즘의 각 단계(step)들을 정의하며, 그 중 한 개 이상의 단계가 서브클래스에 의해 구현되어 제공된다.

<img src="https://github.com/ddamddi/ddamddi.github.io/blob/master/assets/img/tmp_uml3.jpg?raw=true">


### *Hook*
- *Hook*는 추상 클래스에서 선언되는 메소드이지만, 비어있거나 기본적인 부분만 구현된 메소드이다.
- 이렇게 하면 서브 클래스가 원하는 경우 다양한 시점에서 알고리즘에 끼어 들 수 있다. 또한 무시하고 넘어갈 수 도 있다.

다음과 같이 후크를 활용할 수 있습니다.
```java
public abstract class CaffeineBeverage {
	final void prepareRecipe() {
		boilWater();
		brew();
		pourInCup();
		if (customerWantsCondiments()) {
			addCondiments();
		}
	}
	abstract void brew();
	abstract void addCondiments();
	public void boilWater() {
		Sysetm.out.println("물 꿇이는 중...");
	}
	public void pourInCup() {
		System.out.println("컵에 따르는 중...");
	}
	boolean customerWantsCondiments() {	// Hook
		return true;
	}
}
```
- *customerWantsCondiments()* 는 서브클래스의 필요에 따라 오버라이드 할 수 있는 *hook*입니다. 
- *prepareRecipe()* 에서 *hook*인 *customerWantsCondiments()* 리턴값에 따라 _addCondiments()_ 가 호출되거나 호출되지 않을 수 있습니다. 
```java
public class Coffee extends CaffeineBeverage{
	public void brew() {
		Sysetm.out.println("필터로 커피 우려내는 중...");
	}
	public void addCondiments() {
		System.out.println("우유와 설탕을 추가하는 중...");
	}
	public boolean customerWantsCondiments() {
		String answer = getUserInput();
		if (answer.toLowerCase().startsWith("y"_)) {
			return true;
		} else {
			return false;
		}
	}
}
```
- *hook*를 오버라이드하여 원하는 기능을 구현했습니다.
- 손님의 입력에 따라 _true_ 또는 *false*를 리턴합니다.



### 헐리우드 원칙 (Hollywood Principle)
- Don't call us, we'll call you ! (먼저 연락하지 마세요. 저희가 연락드리겠습니다.)
	- 의존성 부패(*Dependancy rot*)를 방지
	- 저수준 구성요소에서도 고수준의 구성요소를 활용할 수는 있으나,
	- 어떤 식으로 사용할 지는 고수준 구성요소에서 결정하게 된다.


### 관련 패턴
