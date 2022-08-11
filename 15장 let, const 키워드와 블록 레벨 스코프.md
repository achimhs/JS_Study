# [22/08/10] 15장 let, const 키워드와 블록 레벨 스코프

## 1. var 키워드로 선언한 변수의 문제점

ES5까지 변수를 선언할 수 있는 방법은 var키워드가 유일했다. var키워드는 3가지 특징이 있는데 이는 다른 언어와 구별되는 독특한 특징으로 사용시 유의해야 한다.

### 1-1. 변수 중복 선언 허용

var 키워드로 선언한 변수는 중복 선언이 가능하다.

```javascript
var x = 1;
var y = 1;

// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 초기화문이 있는 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var x = 100;
// 초기화문이 없는 변수 선언문은 무시된다.
var y;

console.log(x); // 100
console.log(y); // 1
```

var키워드로 x와 y변수가 중복 선언되었다. 중복 선언하면 초기화문 유무에 따라 다르게 동작한다.

> 초기화 문이 있는 경우 var키워드가 없는것 처럼 동작하고
> 초기화 문이 없는 경우 변수 선언문은 무시된다.(에러발생X) ❓

### 1-2. 함수 레벨 스코프

`함수 내부`에서 var키워드로 선언한 변수는 오로지 `함수의 코드 블록만`을 `지역 스코프`로 인정한다.
`함수 외부`에서 var키워드로 선언하면 `코드 블록{ }`내에서 선언해도 모두 `전역 변수`가 된다.

if나 for문 안에서 var의 값을 바꾸면 바꿔지고,
함수 내부에서 var를 선언한걸 가져올 순 없다.

<if문>

```javascript
var x = 1;

if (true) {
  // x는 전역 변수다. 이미 선언된 전역 변수 x가 있으므로 x 변수는 중복 선언된다.
  // 이는 의도치 않게 변수값이 변경되는 부작용을 발생시킨다.
  var x = 10;
}

console.log(x); // 10
```

<for문>

```javascript
var i = 10;

// for문에서 선언한 i는 전역 변수이다. 이미 선언된 전역 변수 i가 있으므로 중복 선언된다.
for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

// 의도치 않게 i 변수의 값이 변경되었다.
console.log(i); // 5
```

### 1-3. 변수 호이스팅

변수 선언문이 스코프의 `선두`로 끌어 올려진 것처럼 동작. 그래서 변수 선언문 `이전`에 참조할 수 있다. 단, 선언문 이전에 참조하는 경우 `undefined`를 반환한다.

```javascript
// 이 시점에는 변수 호이스팅에 의해 이미 foo 변수가 선언되었다(1. 선언 단계)
// 변수 foo는 undefined로 초기화된다. (2. 초기화 단계)
console.log(foo); // undefined

// 변수에 값을 할당(3. 할당 단계)
foo = 123;

console.log(foo); // 123

// 변수 선언은 런타임 이전에 자바스크립트 엔진에 의해 암묵적으로 실행된다.
var foo;
```

위와 같은 예제는 프로그램의 흐름상 맞지도 않고 가독성도 떨어뜨리므로 좋지 않다.

## 2. let 키워드

- var의 단점을 보완하기 위해 등장

### 2-1. 변수 중복 선언 금지

let키워드는 `중복 선언을 할 수 없다`. 중복 선언하면 `문법에러SyntaxError`가 발생한다

```javascript
var foo = 123;
// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
// 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
var foo = 456;

let bar = 123;
// let이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다.
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

### 2-2. 블록 레벨 스코프

`var키워드`는 `함수 레벨 스코프`를 따른다.
하지만 `let키워드`는 모든 코드 블록(함수,if문, for문, while문, try/catch문 등)을 `지역 스코프`로 인정하는 `블록 레벨 스코프`를 따른다.

```javascript
let foo = 1; // 전역 변수

{
  let foo = 2; // 지역 변수
  let bar = 3; // 지역 변수
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

### 2-3. 변수 호이스팅

변수 호이스팅이 발생하지 않는 것처럼 동작한다.
하지만 동일하게 `변수 호이스팅이 발생`한다.

```javascript
console.log(foo); // ReferenceError: foo is not defined
let foo;
```

이처럼 `참조에러ReferenceError`가 발생한다.
`var키워드`로 선언한 변수는 암묵적으로 `선언 단계와 초기화` 단계가 `한 번에` 진행된다.
반면, `let키워드`로 선언한 변수는 `선언 단계와 초기화 단계`가 `분리`되어 진행된다.

<var 키워드로 선언한 변수>

```javascript
// var 키워드로 선언한 변수는 런타임 이전에 선언 단계와 초기화 단계가 실행된다.
// 따라서 변수 선언문 이전에 변수를 참조할 수 있다.
console.log(foo); // undefined

var foo;
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

<let 키워드로 선언한 변수>

```javascript
// 런타임 이전에 선언 단계가 실행된다. 아직 변수가 초기화되지 않았다.
// 초기화 이전의 일시적 사각 지대에서는 변수를 참조할 수 없다.
console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

let키워드로 선언한 변수는 `일시적 사각지대(TDZ Temporal Dead Zone)`가 발생한다.
변수가 호이스팅된 `스코프의 시작점부터 let키워드로 선언된 선언문을 만나기 전 까지`의 구간을 일시적 사각지대 (TDZ)라고 부른다.
이 지점에서 변수를 참조하려고 하면 `참조에러ReferenceError`가 발생한다.

let키워드로 선언한 변수가 호이스팅 된다는 증거는 아래 예제를 통해 살펴 보자.

```javascript
let foo = 1; // 전역 변수

{
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  let foo = 2; // 지역 변수
}
```

만약 변수 호이스팅이 되지 않는다면 위 예제에서 전역 변수 foo값인 1을 출력해야 한다. 하지만 let키워드로 선언한 변수도 호이스팅이 발생하기 때문에 참조에러ReferenceError가 발생한다.

자바스크립트는 ES6에서 도입된 let, const를 포함해서 모든 선언(var, let, const, function, function\*, class 등)을 호이스팅한다.

### 2-4. 전역 객체와 let

`var키워드`로 전역 변수와 전역 함수, 그리고 선언하지 않은 변수(암묵적 전역변수)는 모두 `window객체의 프로퍼티`가 된다. (전역 객체의 프로퍼티를 참조할 때 window를 생략할 수 있다.) 하지만 `let키워드`로 선언된 변수는 `전역 객체의 프로퍼티가 아니다`.

<var 키워드>

```javascript
// 이 예제는 브라우저 환경에서 실행해야 한다.

// 전역 변수
var x = 1;
// 암묵적 전역
y = 2;
// 전역 함수
function foo() {}

// var 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티다.
console.log(window.x); // 1
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(x); // 1

// 암묵적 전역은 전역 객체 window의 프로퍼티다.
console.log(window.y); // 2
console.log(y); // 2

// 함수 선언문으로 정의한 전역 함수는 전역 객체 window의 프로퍼티다.
console.log(window.foo); // ƒ foo() {}
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(foo); // ƒ foo() {}
```

<let키워드

```javascript
// 이 예제는 브라우저 환경에서 실행해야 한다.
let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

## 3. const 키워드

- `상수` 선언(반드시는 아님)

### 3-1. 선언과 초기화

const키워드로 선언한 변수는 반드시 `선언과 동시에 초기화` 해야 한다.

```javascript
const foo = 1;
```

그렇지 않으면 `문법에러SyntaxError`가 발생한다

```javascript
const foo; // SyntaxError: Missing initializer in const declaration
```

const키워드로 선언한 변수도 let과 마찬가지로 `블록 레벨 스코프`를 가지며, 변수 호이스팅이 발생하지만 발생하지 않는것 처럼 동작한다.

```javascript
{
  // 변수 호이스팅이 발생하지 않는 것처럼 동작한다
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  const foo = 1;
  console.log(foo); // 1
}

// 블록 레벨 스코프를 갖는다.
console.log(foo); // ReferenceError: foo is not defined
```

### 3-2. 재할당 금지

const키워드로 선언한 변수는 재할당이 금지된다.

```javascript
const foo = 1;
foo = 2; // TypeError: Assignment to constant variable.
```

### 3-3. 상수

- 재할당이 금지된 변수
- 대문자
- 스네이크 케이스

```javascript
// 세율을 의미하는 0.1은 변경할 수 없는 상수로서 사용될 값이다.
// 변수 이름을 대문자로 선언해 상수임을 명확히 나타낸다.
const TAX_RATE = 0.1;

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + preTaxPrice * TAX_RATE;

console.log(afterTaxPrice); // 110
```

### 3-4. const 키워드와 객체

- 객체는 변경 가능한 값이므로 const 키워드로 선언된 변수에 객체를 할당한 경우 값을 변경할 수 있다.

- 원시값: 변경 불가=재할당 불가능/ 객체: 변경 가능=재할당 없이도 변경 가능
- const는 재할당을 금지할 뿐 '불변'을 의미하지 않는다.
- 객체가 변경되더라도 변수에 할당된 참조값은 변경되지 않고 프로퍼티의 값이 변경되는 것이다.

### 3-5. var vs let vs const

- 변수 선언시 `기본`적으로 `const`를 사용한다.
- `재할당`이 필요한 경우에 한정하여 `let`을 사용하는 것이 좋다.

> [var, let, const키워드 사용시 권장사항]
>
> - ES6를 사용한다면 var는 사용하지 않는다.
> - 재할당이 필요한 경우에 한정해 let키워드를 사용하고, 스코프는 최대한 좁게 만든다.
> - 변경이 없고 읽기 전용으로 사용하는 원시 값과 객체에는 const를 사용한다.(재할당이 안되므로 안전하다.)

※ 변수를 선언하는 시점에는 재할당이 필요할지 모르는 경우가 많다. 그래서 변수를 선언할 때 일다는 const키워드를 사용해 선언하자. 코드를 만들다가 재할당이 필요하면 그때 let키워드로 변경해도 결코 늦지 않다.
