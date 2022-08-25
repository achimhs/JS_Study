# [22/08/25] 20장 strict mode

## 1. strict mode란?

```javascript
function foo() {
  x = 10;
}
foo();

console.log(x); // ?
```

💡암묵적 전역: 전역 스코프에 변수 x의 선언이 존재하지 않기 때문에 RefferenceError가 발생해야 하는데 자바스크립트 엔진은 `암묵적으로 전역 객체에 x프로퍼티를 동적으로 생성`한다. 전역 객체의 x프로퍼티는 `마치 전역 변수처럼` 사용할 수 있다. ➡️ 암묵적 전역은 개발자 의도와 상관없는 `오류` 발생 ➡️ `var, let, const키워드` 사용

ES5부터 strict mode(엄격 모드)가 추가되었다.
💡strict mode : 자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류 발생이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적 에러를 발생
💡ESLint(선호) : strict mode와 유사, 정적 분석 기능을 통해 소스코드를 실행하기 전에 소스코드를 스캔하여 문법적 오류만이 아니라 잠재적 오류까지 찾아내고 오류의 원인을 리포팅 해주는 도구다. (익스텐션에서 설치하면 됨)

## 2. strict mode의 적용

💡전역의 선두 'use strict';를 추가 ➡️ 스크립트 전체에 strict mode가 적용
💡함수의 선두 'use strict';를 추가 ➡️ 함수 내부만 strict mode가 적용

<전역 선두에 선언>

```javascript
"use strict";

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
```

<함수 몸체 선두에 선언>

```javascript
function foo() {
  "use strict";

  x = 10; // ReferenceError: x is not defined
}
foo();
```

<'use strict';를 선두에 위치하지 않으면 제대로 동작하지 않는다>

```javascript
function foo() {
  x = 10; // 에러를 발생시키지 않는다.
  ("use strict");
}
foo();
```

## 3. 전역에 strict mode를 적용하는 것은 피하자

전역에 적용한 strict mode는 스크립트 단위로 적용된다.

```html
<!DOCTYPE html>
<html>
  <body>
    <script>
      "use strict";
    </script>

    <script>
      x = 1; // 에러가 발생하지 않는다.
      console.log(x); // 1
    </script>

    <script>
      "use strict";

      y = 1; // ReferenceError: y is not defined
      console.log(y);
    </script>
  </body>
</html>
```

위와 같이 `스크립트 단위로 적용된 strict mode`는 다른 스크립트에 영향을 주지 않고 `해당 스크립트에 한정`되어 적용된다.

`strict mode 와 non-strict mode를 혼용`해서 사용하면 `오류`를 발생시킬 수 있다. 특히 외부 서드파티 라이브러리를 사용하는 경우 라이브러리가 non-strict mode인 경우도 있기 때문에 전역에 strict mode를 적용하는 것은 바람직하지 않다.
➡️`즉시 실행 함수`를 써서 스크립트 전체를 감싸 스코프를 구분하고 `선두에 strict mode`를 적용한다.

```javascript
// 즉시 실행 함수의 선두에 strict mode 적용
(function () {
  "use strict";

  // Do something...
})();
```

## 4. 함수 단위로 strict mode를 적용하는 것도 피하자

- 매번 함수마다 일일이 strict mode를 적용하는 건 번거로운 일
- strict mode가 적용된 함수가 참조할 함수 외부의 컨텍스트에 strict mode를 적용하지 않는다면 문제가 발생할 수 있다.

```javascript
(function () {
  // non-strict mode
  var lеt = 10; // 에러가 발생하지 않는다.

  function foo() {
    "use strict";

    let = 20; // SyntaxError: Unexpected strict mode reserved word
  }
  foo();
})();
```

## 5. strict mode가 발생시키는 에러

> 1. 암묵적 전역
> 2. 변수, 함수, 매개변수의 삭제(delete)
> 3. 매개변수 이름의 중복
> 4. with문의 사용

### 5-1. 암묵적 전역

```javascript
(function () {
  "use strict";

  x = 1;
  console.log(x); // ReferenceError: x is not defined
})();
```

### 5-2. 변수, 함수, 매개변수의 삭제

delete연산자로 변수, 함수, 매개변수를 삭제하면 SyntaxError가 발생한다.

```javascript
(function () {
  "use strict";

  var x = 1;
  delete x;
  // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a;
    // SyntaxError: Delete of an unqualified identifier in strict mode.
  }
  delete foo;
  // SyntaxError: Delete of an unqualified identifier in strict mode.
})();
```

### 5-3. 매개변수 이름의 중복

중복된 매개변수 이름을 사용하면 SyntaxError가 발생한다.

```javascript
(function () {
  "use strict";

  //SyntaxError: Duplicate parameter name not allowed in this context
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
})();
```

### 5-4. with문의 사용

with문은 전달된 객체를 스코프 체인에 추가한다. with문은 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있어서 코드가 간단해지는 효과가 있지만 성능과 가독성이 나빠져 사용하지 않는것이 좋다.

```javascript
(function () {
  "use strict";

  // SyntaxError: Strict mode code may not include a with statement
  with ({ x: 1 }) {
    console.log(x);
  }
})();
```

## 6. strict mode 적용에 의한 변화

### 6-1. 일반 함수의 this

strict mode에서 함수를 `일반함수로 호출`하면 `this`에 `undefined`가 바인딩된다.

생성자 함수가 아닌 일반 함수 내부에서는 this를 사용할 필요가 없기 때문이다.

```javascript
(function () {
  "use strict";

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
})();
```

만약 strict mode가 아닌 경우 window전역 객체가 바인딩된다.

### 6-2. arguments 객체

strict mode에서 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.

```javascript
(function (a) {
  "use strict";
  // 매개변수에 전달된 인수를 재할당하여 변경
  a = 2;

  // 변경된 인수가 arguments 객체에 반영되지 않는다.
  console.log(arguments); // { 0: 1, length: 1 }
})(1);
```
