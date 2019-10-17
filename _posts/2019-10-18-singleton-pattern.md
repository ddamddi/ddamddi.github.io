---
layout: post
title: "[Design Pattern] 싱글턴 패턴(Singleton Pattern)"
description: ""
modified: 2019-10-18
categories: study
tags: design_pattern
comments: true
---

### 싱글턴 패턴(Singleton Pattern)
싱글턴 패턴이란 인스턴스를 단 하나만 만들어 사용하기 위한 패턴이다. 윈도우 매니져, 쓰레드 풀 매니져 등의 경우, 인스턴스를 여러 개 만들게 되면 자원을 낭비하게 되거나 오류를 발생할 수 있기 때문에 오직 하나의 인스턴스만 만들어 사용하도록 하는 것이 이 패턴의 ㄴ목적이다. 

### 싱글턴 패턴의 기본 구조
싱글턴 패턴은 하나의 인스턴스를 유지하기 위해 몇 가지 제약을 걸어둬야한다.
```java
public class Singleton {
	private static Sigleton uniqueInstance;
	private Singleton() {}
	public static Singleton getInstance() {
		if (uniqueInstance == null) {
			uniqueInstance = new Singleton();
		}
		return uniqueInstance;
	} 
}
```
- *new*를 실행할 수 없도록 생성자(*Constructor*)의 접근제어자는 *private*이어야 한다.
- 유일한 인스턴스를 리턴할 수 있는 *getInstance()* 메소드를 제공해야 한다.
	- ``public static Singleton getInstance();``
- 객체는 단 한 번만 생성되어야 하고 생성된 객체를 참조할 변수가 필요하다.
	- 처음 *uniqueInstance*가 null 일때만 *new* 연산자로 인스턴스 생성
	- 인스턴스는 *uniqueInstance* 변수를 통해 참조 가능


### 멀티 쓰레드에서의 싱글턴 패턴
멀티 쓰레드를 사용하는 경우 위의 기본 구조의 싱글턴 패턴은 여러 개의 객체가 생성되버리는 문제가 발생한다. 이를 해결하기 위한 방법이 3가지가 있다. 모두 Correctness를 보장하지만 성능면에서 차이가 있다.

Solving Option 1
```java
public class Singleton {
	private static Sigleton uniqueInstance;
	private Singleton() {}
	public static synchronized Singleton getInstance() {
		if (uniqueInstance == null) {
			uniqueInstance = new Singleton();
		}
		return uniqueInstance;
	} 
}
``` 
*getInstance( )* 에 *synchronized* 키워드를 추가하여  메소드 단위의 binary semaphore (mutex) 역할을 주면 해결된다. Correctness를 보장하지만 *getInstance( )* 가 처음 호출된 후에는 여러 쓰레드가 동시에 들어와도 문제가 발생하지 않으나 계속해서 메소드 단위의 locking이 걸리게 된다.

Solving Option 2
```java
public class Singleton {
	private static Sigleton uniqueInstance = new Singleton();
	private Singleton() {}
	public static Singleton getInstance() {
		return uniqueInstance;
	} 
}
``` 
Option 1에서 문제가 되었던 too much locking 문제를 해결하기 위해 전역변수로 인스턴스를 생성하였다. 역시 Correctness를 보장하지만 전역 변수 사용으로인한 overhead가 있다.  (프로그램에서 해당 인스턴스를 전혀 사용하지 않더라도 전역으로 생성되어 존재)

Solving Option 3
```java
public class Singleton {
	private volatile static Sigleton uniqueInstance = null;
	private Singleton() {}
	public static synchronized Singleton getInstance() {
		if (uniqueInstance == null) {
			synchronized(Singleton.class) {
				if (uniqueInstance == null) {	// 한번 더 Check (Double Checking!)
					uniqueInstance = new Singleton();
				}
			}	
		}
		return uniqueInstance;
	} 
}
``` 
Double Checking을 통해서 too much locking problem과 memory overhead problem을  해결한 방법이다. 역시 Correctness를 보장한다. 첫 번째 조건문은 Correctness issue가 아닌 첫 번째 쓰레드 이후의 성능개선을 위한 조건문이다. 

*volatile* 은 컴파일러의 옵티마이져가 캐싱을 하지 못하게 하는 키워드이다. 캐싱을 하지 않고 쓰기/읽기 작업이 메모리에서 이루어지도록 한다. 캐시 사용으로 인한 객체 초기화시 발생될 수 있는 동기화 문제를 막아준다. 




