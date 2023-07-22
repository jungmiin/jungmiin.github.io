---
title: "클로저(Closure)"
category: 자바스크립트
tags: [자바스크립트, 클로저, 코어 자바스크립트]
layout: post
toc: true
---

# 클로저란?

- 함수형 프로그래밍 언어에서 등장하는 보편적인 특성
- 자바스크립트 고유의 개념이 아님
- 자신을 내포하는 함수의 컨텍스트에 접근할 수 있는 함수
- 어렵다…

일단, 아래의 예제를 **실행 컨텍스트** 관점에서 생각해보자.

```jsx
function outer() {
  let A;
  function inner() {
    console.log(A++);
  }
  inner();
}
outer();
```

`outer` 의 실행 컨텍스트에서 선언한 내부함수 `inner` 의 실행 컨텍스트가 활성화된 시점에는, `inner` 의 `outerEnvironmeentReference` 가 참조하는 대상인 `outer` 의 `LexicalEnvironment` 에도 접근이 가능하다.

즉, `outer` 에서는 `inner` 에서 선언한 변수에 접근할 수 없지만, `inner` 에서는 `outer` 에서 선언한 변수에 접근이 가능하다.

여기서 다시 한 번 MDN이 소개한 클로저의 문구를 확인해보자.

> A closure is the **combination** of a function and the lexical environment within which that function was declared - MDN
> {: .prompt-info }

직역하자면, “클로저는 함수와 그 함수가 선언될 당시의 lexical environment의 **상호관계**에 따른 현상” 이다.

여기서 주목해야 할 것은 **combination**!

내부함수 `inner` 가 언제나 `outer` 의 `LexicalEnvironment` 를 사용하는 것은 아니다. 위의 예제와 같이 내부 함수에서 외부 변수를 참조하는 경우에서만 combination, `선언될 당시의 LexicalEnvironment와의 상호관계` 가 나타나게 된다.

즉, 클로저란 **“어떤 함수에서 선언한 변수를 참조하는 내부 함수에서만 발생하는 현상”** 이라고 볼 수 있다.

## 예제 보기

조금 더 자세한 예제를 확인해보자.

```jsx
const outer = () => {
  let A = 0;
  const inner = () => {
    return ++A;
  };
  return inner();
};

const outer2 = outer();
console.log("outer2:", outer2); // outer2: 1
console.log("outer2:", outer2); // outer2: 1
```

- `outer` 는 `inner` 함수를 실행한 결과를 리턴하고 있다.
- `outer` 함수의 실행 컨텍스트가 종료된 시점에는 `A` 를 참조하는 대상이 없어진다.
- `A` 와 `inner` 는 언젠가 가비지 컬렉터에 의해 소멸된다.
- `outer` 컨텍스트가 종료되기 이전에 `inner` 함수의 실행 컨텍스트가 종료돼 있으며, 이후 별도로 `inner` 함수를 호출할 수 없다.

```jsx
const outer = () => {
  let A = 0;
  const inner = () => {
    return ++A;
  };
  return inner;
};

const outer2 = outer();
console.log("outer2:", outer2()); // outer2: 1
console.log("outer2:", outer2()); // outer2: 2
```

- `inner` 함수의 실행 결과가 아닌 `inner` 함수 자체를 반환했다.
- `outer` 함수의 실행 컨텍스트가 종료될 때 `outer2` 변수는 `outer` 의 실행 결과인 `inner` 함수를 참조한다.
- 이후 `outer2` 를 호출하면 앞서 반환된 `inner` 가 실행된다.
- `inner` 의 `outerEnvironmentReference` 에는 `inner` 함수가 선언된 위치의 `LexicalEnvironment` 가 참조복사된다. (= `outer` 함수의 `LexicalEnvironment` 가 담긴다.)
- 첫 번째 `outer2` 가 호출됐을 때, 스코프 체이닝에 따라 `outer` 에서 선언한 변수 `A`에 접근해서 1만큼 증가시킨 후 `1` 을 반환한 후 `inner` 함수의 실행 컨텍스트가 종료된다.
- 이후 다시 `outer2` 가 호출되면, 같은 방식으로 `A` 의 값을 1만큼 증가 시켜 `2` 를 반환한다.

여기서 의문점은, `inner` 함수의 실행 시점에는 **`outer` 함수는 이미 실행이 종료된 상태인데 어떻게 `outer` 함수의 `LexicalEnvironment` 에 접근할 수 있는 건가?** 이다.

이는 가비지 컬렉터의 동작 방식 때문이다. 가비지 컬렉터는 어떤 값을 참조하는 변수가 하나라도 있다면, 그 값은 수집 대상에 포함시키지 않는다.

즉, `outer` 함수는 실행 종료 시점에 `inner` 함수를 반환한다. 외부 함수인 `outer` 실행이 종료되더라도 내부 함수인 `inner` 함수는 언젠가 `outer2` 를 실행함으로써 호출될 가능성이 열린 것이기 때문에, 언젠가 `inner` 함수의 실행 컨텍스트가 활성화되면 `outerEnvironmentReference` 가 `outer` 함수의 `LexicalEnvironment` 를 필요로 할 것이므로 수집 대상에서 제외된다.

이처럼 함수의 실행 컨텍스트가 종료된 후에도 `LexicalEnvironment` 가 가비지 컬렉터의 수집 대상에서 제외되는 경우는, **지역 변수를 참조하는 내부 함수가 외부로 전달되는 경우**가 유일하다.

여기서 말하는 “**외부로 전달”**은, `return` 뿐만이 아니라, `eventListener`의 콜백 함수, 혹은 `setTimeout`, `setInterval`의 콜백함수로도 가능하다.

# 클로저와 메모리

클로저는 개발자가 의도적으로 참조 카운트를 0이 되지 않게 설계함으로써 데이터가 가비지 컬렉터의 수거 대상이 되지 않도록 설계한 것으로도 볼 수 있다.

그러한 관점에서 메모리가 예상보다 더 소모될 수 있으며, 이에 대한 관리법을 파악하는 것이 중요할 수 있다.

## 메모리 관리 방법

클로저는 어떤 필요에 의해 의도적으로 함수의 지역변수의 메모리를 소모하도록 함으로써 발생하기에, 필요성이 사라진 시점에는 더는 메모리를 소모하지 않게 해주면 된다. (= 참조 카운트를 0으로 만들면 된다.)

이는 식별자에 참조형이 아닌 기본형 데이터(null, undefined)를 할당함으로써 가능하다.

```jsx
const outer = () => {
  let A = 0;
  const inner = () => {
    return ++A;
  };
  return inner;
};

let outer2 = outer();
console.log("outer2:", outer2()); // outer2: 1
console.log("outer2:", outer2()); // outer2: 2
outer2 = null;
```

# 클로저 활용 사례

## 콜백 함수 내부에서 외부 데이터를 사용하고자 할 때

## 접근 권한 제어(정보 은닉)

## 부분 적용 함수

## 커링 함수
