---
title: onKeyDown에서 키가 두번 입력되는 문제
author: jungmiin
category: 프론트엔드
tags:
  - 프론트엔드
  - 자바스크립트
layout: post
toc: true
---
## 문제 상황
- `onKeyDown` 을 통해 `Enter` 키를 누르면 자동으로 넘어가는 기능을 구현하고자 하였다.
- 그런데 이 상황을 통해 넘어가면, 요청이 두번씩 날라가는지 페이지가 두번 이동하는 현상이 발생하였다.
![문제 상황에서의 페이지 스택](https://i.imgur.com/jUFEN3H.png)
_페이지 스택에 동일한 페이지가 이미 있는 것을 알 수 있다._

## onKeyDown
- 확실하게 문제를 파악하기 위해 어디서부터 요청이 두번 이루어지는 것을 파악하고자 하였다.
- 다음은 `keyDown` 이벤트에 따른 처리를 해주는 `handleKeyDown` 함수이다.
```typescript
// 엔터 처리
const handleKeyDown = (e: KeyboardEvent<HTMLInputElement>) => {
	console.log(e.key, e.nativeEvent.isComposing);
	onSubmit && e && e.key === "Enter" ? onSubmit(input) : null;
};	
```
- **e.nativeEvent.isComposing?**
	- isComposing이란 한글의 특성상 유의미해지는 속성이다.
	- 자음과 모음의 조합으로 글자가 만들어지는 조합문자이기 때문에 글자가 조합중인지, 조합이 끝난 상태인지를 알고 있어야 한다.
	- 그렇지만 이러한 속성을 `React`에서 지원해주지 않기 때문에 (`React`는 `SyntheticEvent` 여서 따로 구현된 합성 이벤트가 존재한다.)
	- 이 속성을 보기 위해서는 `nativeEvent` 를 통해 브라우저의 고유 이벤트에 접근해야 한다.
- 이 코드를 실행시키면 아래와 같은 결과가 나온다.
	
    |                      한글                       |                      영문                       |
    |:-----------------------------------------------:|:-----------------------------------------------:|
    | ![한글의 경우](https://i.imgur.com/RiZZxNH.png) | ![영문의 경우](https://i.imgur.com/yluK3RX.png) |
- 모종의 이유로 한글에서는 Enter 키가 두번 인식이된다.
>If I insert korean characters in `<input>` tag without moving the cursor, and press down & hold the Enter key,
>
>1. keydown event occurs (normal)
>2. keyup event occurs (unexpected)
>3. keydown event occurs (unexpected)
>4. keypress event occurs (normal)
>5. keydown & keypress event occurs continuously until enter key is released. (normal) And when I release the Enter key,
>6. keyup event occurs (normal)
>
>**Step 2 & 3 are unexpected behaviors.**
>
>[출처](https://github.com/vuejs/vue/issues/10277#issuecomment-731768059)
- 이러한 이유는 IME(Input Method Editor) 와 관련된 이슈인 것으로 추정되며, 이러한 조합 문자를 다루는 경우 `Enter` 키를 처리하는데에 있어서 불분명한 `keyUp` 이벤트와 `keyDown` 이벤트가 다시 한번 발생하게 되며, 그에 따라 `onKeyDown` 핸들러가 두번 실행되는 것이다.

## 해결 방법
- 위의 `isComposing` 속성을 이용해 해결할 수 있다.
```typescript
// 엔터 처리
const handleKeyDown = (e: KeyboardEvent<HTMLInputElement>) => {
	onSubmit && e && !e.nativeEvent.isComposing && e.key === "Enter"
	? onSubmit(input)
	: null;
};
```
- `isComposing` 은 영어, 숫자, 조합이 필요하지 않은 언어를 작성할 때에는 늘 `false` 이다.
- 그러나 한글과 같이 조합이 필요한 언어로 작성할 때는 키를 한번만 눌렀음에도 불구하고 두 번 이벤트가 발생할 때가 존재하고
	- 그때의 경우 `isComposing` 의 상태는 늘 `true -> false` 로 변경된다.
- 그렇기 때문에 `isComposing` 상태를 `false` 일 때만으로 제한을 두고, `e.key` 의 `Enter` 여부를 검사한다면 어떤 언어의 문자를 작성하든지 단 한번의 이벤트를 처리할 수 있게 보장이 가능하다.