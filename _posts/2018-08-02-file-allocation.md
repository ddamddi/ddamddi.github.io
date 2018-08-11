---
layout: post
title:  "OS 파일 할당 이해하기"
subtitle:   "OS 파일 할당 이해하기"
categories: development
tags: os
comments: true
---

파일 할당에 대해 작성한 글입니다

---

## 파일 할당
- 파일 시스템 중 일부인 파일 할당에 대해 배웁니다
		- Sector를 모아둔 것이 Block
		- <-> character device : 글자 단위	
	- 디스크 = pool of free blocks
- 리스트처럼 할당
	- 우리가 가장 쉽게 생각할 수 있는 할당 방법 
	- 디스크의 1/3을 날림
- 또 다른 문제점
- 연결 리스트처럼 할당
		- 연속 할당은 포인터가 끊어져도 그 다음 것을 알 수 있음 
- 개선: FAT 파일 시스템
	- 몇 bit를 할당하면 좋을까?
		- 32비트 -> FAT32, 64비트 -> FAT62
 
	- Unix/Linux 등에서 사용
	- Sequential accecss 가능 
	- 바이트가 작기 때문에 큰 파일을 만들 수 없음 => 여러 인덱스로 구성(Linked)
	- Linked : 여러 인덱스로 계층적 구성
	- Multilevel : 여러 인덱스로 넓게 구성
	- Combined : Linked + Multilevel index


## Reference
- [양희재 교수님 운영체제 강의](http://kocw.net/home/search/kemView.do?kemId=978503)
- [introduction to Operating System](https://www.slideshare.net/LukaXavi/introduction-to-operating-system-10938506)



