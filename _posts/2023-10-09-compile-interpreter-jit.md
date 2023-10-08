---
title: 컴파일 언어의 컴파일 과정, 인터프리터 언어, JIT
author: jungmiin
category: 면접 스터디
tags:
  - 운영체제
  - 면접
  - 스터디
  - "#컴파일"
  - 인터프리터
  - JIT
layout: post
toc: true
---
## Compiled Languages
-   C, C++, Object-C
- 소스코드는 컴파일러를 거쳐 기계어로 이루어진 실행 가능한 파일을 반환함
- 사용자는 코드를 볼 필요 없이, 실행 파일만 가져와서 실행하면 됨
- `Windows` 의 경우, 대부분의 실행 파일은 `.exe` 라는 확장자로 존재하는데, 이것이 실행 가능한 파일을 의미

## 장점

- 프로그램이 즉시 실행 가능함
- 일반적으로 다른 언어에 비해 빠르다
- 소스 코드가 공개되어 있지 않기 때문에 안전하다

## 단점

- 크로스 플랫폼에서 사용이 불가능하다
    - `MacOS` 에서 `exe` 가 실행 불가능하고, `Window` 에서 `dmg` 가 실행 불가능하다
- 다른 CPU/Memory 에서의 실행을 대비해야 하기 때문에 유연하지 못함

## Compilation Process
- 컴파일이란 소스 코드를 기계어로 변환하는 과정
- 고급 언어(C, C++, Java, Python, JavaScript...) 등으로 작성되어진 프로그램은 컴퓨터가 바로 인식해서 실행할 수 없다.
- 따라서 고급 언어로 작성된 프로그램은 컴퓨터가 인식할 수 있는 형식으로 변화하는 과정을 거쳐야 한다.
## 우리가 C 프로그램을 컴파일하고 실행하는 방법
![](https://i.imgur.com/rAOQrUk.png)
![](https://i.imgur.com/4cxFybz.png)
## 컴파일 프로세스에는 어떤 과정이 있을까?
![](https://i.imgur.com/6o24mtL.png)
1. Pre-processing(전처리)
2. Compile(컴파일)
3. Assembly(어셈블리)
4. Linking(링킹)
```shell
gcc -Wall -save-temps HelloWorld.c -o HelloWorld
```
- 위의 명령어를 실행하면, 실행 파일과 함께 컴파일 프로세스를 진행하며 생성되는 중간 파일을 모두 저장한다. 
![](https://i.imgur.com/Oa6v0BY.png)

### Pre-Processor(HelloWorld.c -> HelloWorld.i)
![](https://i.imgur.com/gYDGJS9.png)
#### 알 수 있는 것들
- 주석이 제거되었음
- `include`된 헤더 파일들이 사라지고 소스 코드에 포함되었음
#### 그래서 전처리기가 하는 일은?
- `#` 으로 시작하는 구문을 처리함 (`#include`, `#define` ...)
- 코드의 main이 실행되기 전 매크로나 지시자 같은 것들을 포함함으로써 사전 준비함
### Compiler(HelloWorld.i -> HelloWorld.s)
![](https://i.imgur.com/ODpcNN6.png)
#### 알 수 있는 것들
- 일반적으로 C언어로 보였던 텍스트들이 저수준의 언어로 변경되었음
#### 그래서 컴파일러가 하는 일은?
- 일반적인 고수준의 언어를 저수준의 언어로 번역함 (high-level -> low-level)
- 저수준의 언어의 대표적인 예는 어셈블리어
- 왜 기계어로 바로 번역하지 않고 이러한 과정을 거칠까?
	- 어셈블리어는 기계어와 1대1 매칭이 되는 부호화된 언어
	- 때문에 내부가 어떻게 작동하는지 알 수 있음
### Assembler(HelloWorld.s -> HelloWorld.o)
![](https://i.imgur.com/04cg3gl.png)
#### 알 수 있는 것들
- 쉽게 해독할 수 없는 기계어로 변경되었음
#### 그래서 어셈블러가 하는 일은?
- 저수준의 언어(어셈블리어)를 기계어로 번역함
### Linker(HelloWorld.o -> HelloWorld)
![](https://i.imgur.com/W6AXhvo.png)
- 실제 `HelloWorld.o` 와 `HelloWorld(실행파일)` 은 크기 차이가 존재함
- 기계어로 번역되어 있는 각 파일들을 하나로 연결해줌
- `Object File` 들과 필요한 라이브러리들을 연결시켜주고 최종적으로 하나의 `Executable File` 을 만들어줌
## Interpreted Languages
- PHP, Ruby
- 소스 코드가 사용자의 기기에 직접 복사된다
- 프로그램을 실행하고자 할 때, 사용자의 컴퓨터가 직접 소스 코드를 해석함
- 추가적인 다른 파일들을 생성하지 않으며, 한 줄씩 즉석에서 실행한다
- 일반적으로 인터프리터 방식도 내부적으로 소스코드를 기계가 이해할 수 있는 형태로 변환되어야 한다
- 그렇지만 **전체 소스코드를 한번에 변환하는게 아니라 코드를 한 줄씩 읽어들여** 중간 코드나 기계어로 변환해서 임시파일에 저장하고, 변환한 것을 바로 실행한다
## 장점
- 크로스 플랫폼에서 사용 가능하며, 휴대성이 좋다
- 컴파일 단계가 없기 때문에 테스트 및 디버깅에 유리하다
## 단점
- 사용자가 직접 코드를 실행해야 하기 때문에 인터프리터가 반드시 필요하다
- 직접 실행 중에 각 줄이 해석되어야 하기 때문에 속도가 느리다
- 소스 코드가 공개되어져 있다
## Compiled vs Interpreted
![](https://i.imgur.com/cpPQoEl.png)
- 인터프리터의 경우 코드가 번역된 후 컴퓨터로 전달되어 바로 실행된다
- 반면 컴파일러는 코드를 실행하지 않으며, **디스크에 번역이 완료된 코드를 저장**한다
- 이 저장된 코드는 언제든 실행될 수 있다 
- 따라서 인터프리터는 이식성에 장점이 있으며, 컴파일러는 성능에 장점이 있다
## Hybrid Languages(JIT)

- Java, C#, Python, JavaScript
- JIT(Just In Time)는 동적 컴파일이라고도 불리는데, 프로그램의 런타임 성능을 개선하기 위해서 사용된다
- 인터프리터와 정적 컴파일을 혼합한 방식이다
![](https://i.imgur.com/TdRJdYY.png)
- **작동 방식**
	- 컴파일러가 소스 코드를 바이트 코드로 번역한다
	- 이후 코드가 실행될 때 바이트 코드를 기계어로 번역하며, 기계어를 캐싱한다
	- 이를 통해 같은 함수가 여러번 불릴 때마다 매번 기계어가 생성되는 것을 방지한다
- 일반적으로 인터프리터를 통해 코드를 실행하는 시간보다, 기계어를 실행하는 성능이 빠르다
	- GCC와 같은 Static Compiler들은 기계어로 변환하는 도중 많은 최적화 알고리즘(캐싱)을 적용하기 때문
	- 그렇지만 JIT는 바이트 코드를 기계어로 변환하는 과정이 **실행 시점에서 이루어지기 때문에, 많은 최적화가 이루어 지지 않음**
	- 그렇기 때문에 JIT에서의 최적화는, Static Compile 만큼의 최적화가 이루어 지지는 않는다
- 그럼에도 불구하고 인터프리터를 통한 수행 성능 보다 기계어의 수행 성능이 월등히 낫기 때문에 Java VM은 JIT를 사용한다
## Adaptive Compilation
https://meetup.nhncloud.com/posts/77
- 일반적으로 Hybrid Languages(JIT) 가 Interpreted Languages 보다 수행 성능이 더 낫다고 여겨지지만, JavaScript에서는 그렇지 않다
## 동적 타입 문제
- JavaScript는 변수의 타입이 실행 도중에 달라질 수 있는 매우 동적인 언어이기 때문
- 만약 JavaScript의 JIT 컴파일러가 바이트 코드를 기계어로 변경하고자 한다면, 동적인 케이스를 모두 고려하여 코드를 생성해야한다
- 다음은 변수 두개를 더하는 덧셈 코드에 대한 native code들이다
	```assembly
	// add r2, r2, r0 : virtual register R0, R2를 더하여 R2 register에 저장
	ldr    r2, [r4] // R0의 값을 r2에 저장
	ldr    r7, [r4, #4] // R0의 타입 정보를 r7에 저장
	ldr    r0, [r4, #16] // R2의 값을 r0에 저장
	ldr    r1, [r4, #20] // R2의 타입 정보를 r1에 저장
	cmn    r1, #1 // R2의 타입이 int인지 체크
	bne    0x4121e2c0 // R2의 타입이 int가 아닐 경우 Slow Case
	cmn    r7, #1 // R1의 타입이 int인지 체크
	bne    0x4121e310 // R1의 타입이 int가 아닐 경우 Slow Case
	adds   r0, r0, r2 // R0와 R2의 값을 더함
	bvs    0x4121e498 // Overflow 발생 시 Slow Case
	str    r0, [r4, #16] // 덧셈 결과를 R2에 저장
	b      0x4121e33c // 점프
	```
- 이와 같이, 타입이 다르거나 overflow가 발생하는 등 예외 케이스가 나오게 되면 Slow Case로 건너 뛰게 된다
- Slow Case는 기계어로 표현하면 양이 많아지는 동작들을 변환하는 대신, 미리 내부에 C로 구현된 helper function을 호출하여 동작을 수행하는 경우를 말한다
- 이러한 helper function은 인터프리터와 똑같은 코드를 사용하기 때문에 JIT를 사용하든 인터프리터를 사용하든 별다른 차이가 없게 된다
## hotspot 문제
- JavaScript로 구현된 프로그램들은 연산이 많은 Java와 달리 주로 웹 페이지의 레이아웃을 건드리거나 사용자 입력에 반응하는 식의 코드가 많다
- 두 프로그램의 가장 큰 차이점은 자주 반복돼서 수행되는 구간, 즉 hotspot이 얼마나 많은가인데, JavaScript는 Java에 비해 hotspot이 매우 적다 
- 즉, loop를 도는 구간이 적고, 한두번 수행되고 마는 코드가 많다는 것
- 그렇다면 굳이 native code를 수행하기 위해 만드는 시간이 더 오래 걸리게 되고, 이는 곧 오버헤드가 커지게 되는 것을 의미한다
- 결과적으로 complication overhead + native code execute 가 인터프리터의 수행시간보다 짧을 것이라는 가정이 어려워진다 
- 그렇지만 최근의 JavaScript는, 단순이 웹 페이지에서 이벤트 처리 용도로만 사용되는 것이 아니라, 비즈니스 로직에도 관여를 하고 있다
## 이를 해결하기 위한 Adaptive Complication
- 모든 코드를 일괄적으로 같은 수준의 최적화를 적용하는 것이 아니라, 반복 수행되는 정도에 따라 유동적으로 서로 다른 최적화 수준을 적용하는 방식
	![](https://i.imgur.com/Nli4E7R.png)
- **작동 방식**
	- 기본적으로 모든 코드는 처음에 인터프리터로 수행
	- runtime profiler를 통해 함수의 수행 빈도, 사용되는 변수들의 타입이나 값을 기록
	- 자주 반복되는 부분(hotspot)이 발견되면, 그 부분에 대해서면 JITC를 적용하여 native code로 수행
## 결론
- Hotspot이 별로 없는 고전적인 JavaScript 프로그램들에는 interpreter가 JIT보다 효율이 좋다
- 최근 많이 사용되는 computer-intensive한 JavaScript 프로그램들에는 JITC가 좋다
- 두 가지 성향의 코드에 대한 성능을 모두 만족하기 위해 adaptive JIT를 사용한다
- 이는 type profiling을 수행하므로 변수의 타입이 변하지 않는다면 높은 성능을 얻을 수 있다