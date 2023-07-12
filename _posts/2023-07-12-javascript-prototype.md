---
title: "프로토타입(Prototype)"
author: jungmiin
category: 자바스크립트
tags: [자바스크립트, 프로토타입, 코어 자바스크립트]
layout: post
toc: true
---

## 들어가기 전에…

- 자바스크립트는 프로토타입 기반 언어
- 클래스 기반 언어에서는 상속을 사용하지만, 프로토타입 기반 언어에서는 어떤 객체를 원형으로 삼고 이를 복제함으로써 상속과 비슷한 효과를 얻는다.

## [[Prototype]]과 ⎽⎽proto⎽⎽

이전 블로그 게시글을 보면 크롬 개발자 도구를 보며 프로토타입을 설명할 때, `__proto__` 라고 되어있는 경우가 많다. 그렇지만 실제로 찍어봤을 때는 `__proto__` 가 아닌, `[[Prototype]]` 이 나온다.

![Prototype1](https://github.com/jungmiin/jungmiin.github.io/assets/58061756/b4d25fb2-4a1b-4bbc-80e9-16d4a31097e1)

위 두 프로퍼티는 동일하고, 이름만 변경된 것이라 보는게 맞다. (헷갈릴 필요 없단 소리이다.)

이후에 더더욱 자세히 설명하겠지만, 자바스크립트의 객체는 내부에 `[[Prototype]]` 라는 숨김 프로퍼티를 갖는다. 그리고 그 프로퍼티의 값을 설정할 수 있는 방법이 `__proto__` 이다. **즉 `__proto__` 는 `[[Prototype]]` 의 getter이자 setter인 것!**

기존의 크롬 개발자 도구는 해당 내부 슬롯을 `__proto__` 라고 표현했지만, 실제로 `[[Prototype]]` 라고 표현하는 것이 더 적절하고, 혼동을 줄 여지가 있기 때문에 나타내는 이름이 변경된 것이다.

> 사실 `__proto__` 의 사용도 deprecated 되어있다. 기존의 ECMAScript 사양에 포함되지 않았음에도 브라우저는 이를 구현했고, 최신의 ECMAScript 에서 웹 브라우저와의 호환성을 위해 표준화 된 것. 그렇지만 프로토타입을 그때 그때 바꾸는 연산은 객체 프로퍼티 접근 관련 최적화를 망치기 때문에 성능에 나쁜 영향을 미칠수 있으니 지양하는게 좋다. 또한 `__proto__` 대신 `Object.getPrototypeOf` `Object.setPrototypeOf` 등의 사용을 권장한다.
{: .prompt-danger }

### 모던 메서드

- **`Object.create(proto, [descriptors])`**

  `[[Prototype]]`이 `proto`를 참조하는 빈 객체를 만든다. 이때 프로퍼티 설명자를 추가로 넘길 수 있다.

- **`Object.getPrototypeOf(obj)`**

  `obj`의 `[[Prototype]]`을 반환한다.

- **`Object.setPrototypeOf(obj, proto)`**

  `obj`의 `[[Prototype]]`이 `proto`가 되도록 설정한다.

```javascript
let animal = {
  eats: true
};

// 프로토타입이 animal인 새로운 객체를 생성합니다.
let rabbit = Object.create(animal, {
  jumps: {
    value: true
  }
});

alert(rabbit.jumps); // true

alert(rabbit.eats); // true

alert(Object.getPrototypeOf(rabbit) === animal); // true

Object.setPrototypeOf(rabbit, {}); // rabbit의 프로토타입을 {}으로 바꾼다.
```

## 프로토타입 이해하기

### constructor, prototype, instance

```javascript
var instance = new Constructor();
```

- 어떤 생성자 함수 `Constructor` 를 `new` 연산자와 함께 호출하면
- `Constructor` 에서 정의된 내용을 바탕으로 새로운 `instance` 가 생성된다
- 이때 `instance` 에는 `[[Prototype]]` 라는 프로퍼티가 자동으로 부여되는데
- 이 프로퍼티는 `Constructor` 의 `prototype` 이라는 프로퍼티를 참조한다
- `prototype`은 객체이고, 이를 참고하는 `__proto__` 역시 객체이다
- `prototype`의 객체 내부에는 `instance`가 사용할 메서드를 저장
- `instance`에서도 `__proto__`를 통해 메서드에 접근할 수 있게 된다

자바스크립트는 함수에 자동으로 객체인 `prototype` 프로퍼티를 생성해 놓는데, 해당 함수를 생성자 함수로서 사용할 경우, 즉 `new` 연산자와 함께 함수를 호출할 경우 그로부터 생성된 `instance`에는 숨겨진 프로퍼티인 `[[Prototype]]`가 자동으로 생성되며, 이 프로퍼티는 생성자 함수의 `prototype` 프로퍼티를 참조한다. 그리고 이는 `__proto__` 를 통해 접근이 가능하다.

`__proto__` 프로퍼티는 생략 가능하도록 구현돼 있기 때문에 생성자 함수의 `prototype`에 어떤 메서드나 프로퍼티가 있다면 `instance`에서도 마치 자신의 것처럼 해당 메서드나 프로퍼티에 접근할 수 있게 된다.

```javascript
var Constructor = function (name) {
	this.name = name;
}

Constructor.prototype.method1 = function() {};

Constructor.prototype.property1 = 'Constructor Prototype Property';

var instance = new Constructor('Instance');

console.dir(Constructor);
console.dir(instance);

// console.dir(Constructor);
ƒ Constructor(name)
	arguments: null
	caller: null
	length: 1
	name: "Constructor" //함수의 기본적인 프로퍼티. this.name과 관련 없음
	prototype: {property1: 'Constructor Prototype Property', method1: ƒ, constructor: ƒ}
	[[FunctionLocation]]: VM172:1
	[[Prototype]]: ƒ ()
	[[Scopes]]: Scopes[1]

// console.dir(instance);
Constructor
	name: "Instance"
	[[Prototype]]: Object
		method1: ƒ ()
		property1: "Constructor Prototype Property"
		constructor: ƒ (name)
		[[Prototype]]: Object
```

```javascript
var arr = [1, 2];

console.dir(arr);
console.dir(Array);

// console.dir(arr);
Array(2) // Array라는 생성자 함수를 원형으로 삼아 생성, length가 2
	0: 1
	1: 2
	length: 2
	[[Prototype]]: Array(0) // push, pop, shift, unshift, ... 등 배열에 사용하는 메서드들

// console.dir(Array);
ƒ Array() // 함수라는 의미의 ƒ가 표시
	from: ƒ from()  // Array 함수의 정적 메서드
	isArray: ƒ isArray() // Array 함수의 정적 메서드
	length: 1 // 함수의 기본적인 프로퍼티
	name: "Array" // 함수의 기본적인 프로퍼티
	of: ƒ of() // Array 함수의 정적 메서드
	prototype: [constructor: ƒ, at: ƒ, concat: ƒ, copyWithin: ƒ, fill: ƒ, …] // push, pop, shift, unshift, ... 등 배열에 사용하는 메서드들
	Symbol(Symbol.species): ƒ Array()
	Symbol(Symbol.species): ƒ Array()
	arguments: (...) // 함수의 기본적인 프로퍼티
	caller: (...) // 함수의 기본적인 프로퍼티
	[[Prototype]]: ƒ ()
	[[Scopes]]: Scopes[0]
```

해당 예제를 중점으로 더 자세하게 학습해보자.

`Array`를 **1. `new` 연산자와 함께 호출해서 인스턴스를 생성**하든 / **2. 그냥 배열 리터럴을 생성**하든 인스턴스인 `[ 1, 2 ]` 가 만들어진다.

이 인스턴스의 `[[Prototype]]` 은 `Array.prototype` 을 참조하는데, **`__proto__` 가 생략 가능하도록 설계**돼 있기 때문에, **인스턴스가 `push` , `pop` , `forEach` 등의 메서드를 마치 자신의 것처럼 호출**할 수 있다.

그렇지만 `Array`의 `prototype` 프로퍼티 내부에 있지 않은 `from` , `isArray` 등의 **정적 메서드들은 인스턴스가 직접 호출할 수 없다.** 이들은 `Array` 생성자 함수에서 직접 접근해야 실행 가능하다.

#### 정적 메서드?

`prototype` 이 아닌, 클래스 함수 자체에 메서드를 설정하는 것, 이는 메서드를 프로퍼티 형태로 직접 할당하는 것과도 동일하다.

```javascript
class User {
  static staticMethod() {
    alert(this === User);
  }
}

User.staticMethod(); // true

class User {}

User.staticMethod = function () {
  alert(this === User);
};

User.staticMethod(); // true
```

정적 메서드는 어떤 특정한 객체가 아닌 클래스에 속한 함수를 구현하고자 할 때 주로 사용된다.

### constructor

`prototype` 객체 내부에는 `constructor` 라는 프로퍼티가 있다. 이 프로퍼티는 원래의 생성자 함수를 참조한다.

```javascript
var arr = [1, 2];
Array.prototype.constructor === Array; // true
arr.__proto__.constructor === Array; // true
arr.constructor === Array; // true

var arr2 = new arr.constructor(3, 4);
console.log(arr2) // [3, 4]

// 모두 같은 대상을 가리킴
[Constructor]
[instance].__proto__.constructor
[instance].constructor
Object.getPrototypeOf([instance]).constructor
[Constructor].prototype.constructor

// 모두 동일한 객체에 접근함
[Constructor].prototype
[instance].__proto__
[instance]
Object.getPrototypeOf([instance])
```

## 프로토타입 체인

### 메서드 오버라이드

`prototype` 객체를 참조하는 `__proto__` 를 생략하면 인스턴스는 `prototype` 에 정의된 프로퍼티나 메서드를 마치 자신의 것처럼 사용할 수 있다.

그런데 만약 인스턴스가 동일한 이름의 프로퍼티 또는 메서드를 가지고 있는 상황이라면 어떻게 될까?

```javascript
var Person = function (name) {
  this.name = name;
};
Person.prototype.getName = function () {
  return this.name;
};

var developer = new Person("철수");
developer.getName = function () {
  return "개발자 " + this.name;
};

console.log(developer.getName()); // (1) 개발자 철수
console.log(developer.__proto__.getName()); // (2) undefined
console.log(developer.__proto__.getName.call(developer)); // (3) 철수
```

`developer.getName()` 을 호출하면, `developer.__proto__.getName` 이 아닌, `developer` 객체에 있는 `getName` 메서드가 호출된다. 이러한 현상이 **메서드 오버라이드**인데, 메서드 위에 메서드를 덮어씌웠다는 뜻이다.

즉, 원본을 제거하고 다른 대상으로 교체하는 것이 아니라, **원본이 그대로 있는 상태에서 다른 대상을 그 위에 얹는 것**이다.

자바스크립트 엔진이 `getName` 이라는 메서드를 찾는 방식은, 가장 가까운 대상인 자신의 프로퍼티를 검색하고, 없으면 그 다음으로 가까운 대상인 `__proto__` 를 검색하는 순서로 진행된다. 그렇기 때문에 `__proto__` 에 있는 메서드는 자신에게 있는 메서드보다 검색 순서에서 밀려 호출되지 않는다.

그렇기 때문에, prototype에 직접 접근하려면 (2)와 같이 prototype을 직접 가리키도록 해야한다. `undefined` 가 나오는 이유는 `this`는 prototype 객체, 즉 `developer.__proto__` 를 가리키는데 해당 객체에는 name이라는 프로퍼티가 없기 때문이다. 그렇기 때문에 `developer.__proto__`의 `this`를 `developer`에 바인딩 시킴으로써 정상적으로 `name`을 가리키게 만들 수 있다.

### 프로토타입 체인

```javascript
console.dir({ a: 1 }

Object
	a: 1
	[[Prototype]]: Object
		constructor: ƒ Object()
		hasOwnProperty: ƒ hasOwnProperty()
		isPrototypeOf: ƒ isPrototypeOf()
		propertyIsEnumerable: ƒ propertyIsEnumerable()
		toLocaleString: ƒ toLocaleString()
		toString: ƒ toString()
		valueOf: ƒ valueOf()
		__defineGetter__: ƒ __defineGetter__()
		__defineSetter__: ƒ __defineSetter__()
		__lookupGetter__: ƒ __lookupGetter__()
		__lookupSetter__: ƒ __lookupSetter__()
		__proto__: Object
			constructor: ƒ Object()
			...
			__proto__: null

```

- **`{ a: 1 }` 같은 경우**

`[[Prototype]]` 의 내부에는 `hasOwnProperty` `isPrototypeOf` 등의 메서드가 있다.

```javascript
console.dir([ 1, 2 ])

Array(2)
	0: 1
	1: 2
	length: 2
	[[Prototype]]: Array(0)
		at: ƒ at()
		concat: ƒ concat()
		constructor: ƒ Array()
		copyWithin: ƒ copyWithin()
		entries: ƒ entries()
		every: ƒ every()
		fill: ƒ fill()
		...
		[[Prototype]]: Object
			constructor: ƒ Object()
			hasOwnProperty: ƒ hasOwnProperty()
			...
			__proto__: Array(0)
				at: ƒ at()
				concat: ƒ concat()
				constructor: ƒ Array()
				...
				[[Prototype]]: Object
					constructor: ƒ Object()
					hasOwnProperty: ƒ hasOwnProperty()
					...
					__proto__: Object
						constructor: ƒ Object()
						hasOwnProperty: ƒ hasOwnProperty()
						...
						__proto__: null
```

- **`[ 1, 2 ]` 같은 경우**

`[[Prototype]]` 에는 `pop`, `push` 등의 배열 메서드가 있고, 이 `[[Prototype]]` 내부에는 또 다시 `[[Prototype]]` 가 등장하고, 열어보면 `Object`의 `[[Prototype]]` 와 같다.

![Prototype2](https://github.com/jungmiin/jungmiin.github.io/assets/58061756/861f485e-94bb-48eb-8431-ae1b090f5ab8){: .w-75}

이러한 이유는 `prototype`의 객체가 `Object`이기 때문이다. 기본적으로 모든 객체의 `[[Prototype]]`에는 `Object.prototype`이 연결된다. `prototype`또한 객체이기 때문에 `Object.prototype`이 연결된다.

```javascript
var arr = [1, 2];
arr.push(3); // arr.__proto__.push(3)와 같음
arr.hasOwnProperty(2); // arr.__proto__.__proto__.hasOwnProperty(2)와 같음
```

이와 같이 어떤 데이터의 `__proto__` 프로퍼티 내부에 다시 `__proto__` 프로퍼티가 연쇄적으로 이어진 것을 **프로토타입 체인**이라 하고, 이 체인을 따라가며 검색하는 것을 **프로토타입 체이닝**이라고 한다.

이는 메서드 오버라이드와 동일한 맥락으로, 어떤 메서드를 호출하면 자바스크립트 엔진은 데이터 자신의 프로퍼티를 검색해서 원하는 메서드가 있으면 그 메서드를 실행하고, 없으면 `__proto__`를 검색해서 있으면 그 메서드를 실행하고, 없으면 다시 `__proto__` 를 검색해서 실행하는 식으로 진행한다.

```javascript
var arr = [1, 2];
Array.prototype.toString.call(arr); // 1,2
Object.prototype.toString.call(arr); // [object Array]
arr.toString(); // 1,2

arr.toString = function () {
  return this.join("_");
};
arr.toString(); // 1_2 메소드 오버라이드
```

`arr`는 배열이므로, `arr.__proto__` 는 `Array.prototype`을 참조하고, `Array.prototype`은 객체이므로 `Array.prototype.__proto__`는 `Object.prototype`을 참조한다.

### 객체 전용 메서드

어떤 생성자 함수이든 `prototype`은 반드시 객체이기 때문에, `Object.prototype`이 언제나 프로토타입 체인의 최상단에 존재하게 된다.

따라서 객체에서만 사용할 메서드는 다른 데이터 타입처럼 프로토타입 객체 안에 정의할 수 없다. 객체에서만 사용할 메서드를 `Object.prototype` 내부에 정의한다면, **다른 데이터 타입도 해당 메서드를 사용할 수 있기 때문**이다.

```javascript
Object.prototype.getEntries = function () {
  var res = [];
  for (var prop in this) {
    if (this.hasOwnProperty(prop)) {
      res.push([prop, this[prop]]);
    }
  }
  return res;
};
var data = [
  ["object", { a: 1, b: 2, c: 3 }], // [["a",1], ["b", 2], ["c",3]]
  ["number", 345], // []
  ["string", "abc"], // [["0","a"], ["1","b"], ["2","c"]]
  ["boolean", false], // []
  ["func", function () {}], // []
  ["array", [1, 2, 3]] // [["0", 1], ["1", 2], ["2", 3]]
];
data.forEach(function (datum) {
  console.log(datum[1].getEntries());
});
```

`getEntries`는 객체에서만 사용할 의도로 메서드를 만들어졌지만, data라는 배열에서도 getEntries에 접근할 수 있게 된다. **어느 데이터 타입이건 무조건 프로토타입 체이닝을 통해 getEntries 메서드에 접근할 수 있으니 모두 사용할 수 있게 된것**이다.

이때문에 객체만을 대상으로 동작하는 객체 전용 메서드는 `Object.prototype`이 아닌 `Object`의 **정적 메서드**로 부여할 수 밖에 없어졌다.

생성자 함수인 `Object`와 인스턴스인 객체 리터럴 사이에는 `this`를 통한 연결이 불가능하기 때문에, 여느 전용 메서드 처럼 **‘메서드명 앞의 대상이 곧 this’**가 되는 방식 대신 대상 인스턴스를 인자로 직접 주입해야 하는 방식으로 구현되었다.

정리하자면, 객체 한정 메서드들을 `Object.prototype`이 아닌 `Object` 에 직접 부여할 수 밖에 없던 이유는, `Object.prototype`이 여타 참조형 데이터 뿐 아니라 기본형 데이터 조차 `**__proto__`에 반복 접근함으로써 도달할 수 있는 최상위 존재\*\*이기 때문!

그렇기 때문에 `Object.prototype` 에는 **어떤 데이터에서도 활용할 수 있는 범용적인 메서드**들만 있다. `toString`, `hasOwnProperty`, `valueOf`, `isPrototypeOf` …

### 다중 프로토타입 체인

```javascript
var Grade = function () {
  var args = Array.prototype.slice.call(arguments);
  for (var i = 0; i < args.length; i++) {
    this[i] = args[i];
  }
  this.length = args.length;
};
var g = new Grade(100, 80);
```

## 프로토타입의 장단점

- **장점**

  메모리를 아낄 수 있다. 클래스는 새로운 인스턴스를 만들 때 복사를 하지만, 프로토타입은 객체와 객체를 연결, 즉 참조하고 있을 뿐이므로 메모리에서 이점이 있다.

- **단점**

  객체를 생성할 때 상위 객체의 메서드를 연결하므로, 메서드를 검색하기 위해 상위 프로토타입 체인을 따라 검색해야 하고, 이는 속도가 저하될 수 있다.
  또한 프로토타입을 과도하게 변경하면 객체 프로퍼티 접근 관련 최적화를 망치기 때문에 성능에 나쁜 영향을 미칠 수 있다.
