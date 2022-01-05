# JavaScript / TypeScript

## 목차

1. [세미콜론(Semicolons)](<#세미콜론(Semicolons)>)
2. [블록(Blocks)](<#블록(Blocks)>)
3. [화살표 함수(Arrow Function)](<#화살표 함수(Arrow Function)>)
4. [상수(constant)](<#상수(constant)>)
5. [문자열(String)](<#문자열(String)>)
6. [모듈(Modules)](<#모듈(Modules)>)
7. [조건문(Conditional statements)](<#조건문(Conditional statements)>)
8. [형변환과 강제(Type Casting & Coercion)](<#형변환과 강제(Type Casting & Coercion)>)
9. [명명규칙(Naming Conventions)](<#명명규칙(Naming Conventions)>)

## 세미콜론(Semicolons)

- 세미콜론(;)은 사용한다

```JavaScript
// bad
(function() {
  const name = 'Indenter'
  return name
})()

// good
(() => {
  const name = 'Indenter';
  return name;
})();

// good (즉시함수가 연결된 2개의 파일일때 인수가 되는 부분을 보호)
;(() => {
  const name = 'Indenter';
  return name;
})();
```

- **가장 좋은 방법은 항상 끝에 세미콜론을 작성하는 것입니다.
  IIFE의 앞에 세미콜론을 넣는 것은, 혹시 발생할 실수를 막는 안전장치일 뿐입니다.**

```JavaScript
// foo.js (No tailing ;)
const foo = function() { ... }

// bar.js
(() => { ... })();

/*웹에서 js를 제공하기 위해 minimize는 항상 필요합니다. 따라서 두 파일을 하나로 합치는 경우도 잦습니다.
패키징 등을 통해 foo.js와 bar.js 파일이 합쳐진다면, 코드는 아래와 같이 됩니다.*/

// 이는 foo를 정의하며 동시에 arrow function을 인자로 받아 실행시킵니다.
const foo = function() { ... }(() => { ... })();
```

**[⬆ back to top](#목차)**

## 블록(Blocks)

- 복수행의 블록에는 `{}`를 생략하지 않기

```JavaScript
// bad
if (test)
  return false;

// good
if (test) return false;

// best
if (test) {
  return false;
}

// bad
function() { return false; }

// good
function() {
  return false;
}
```

**[⬆ back to top](#목차)**

## 화살표 함수(Arrow Function)

- (무명함수를 전달하는 듯한)함수식을 이용하는 경우 Arrow Function 사용

```JavaScript
// bad
[1, 2, 3].map(function (x) {
  const y = x + 1;
  return x * y;
});

// good
[1, 2, 3].map((x) => {
  const y = x + 1;
  return x * y;
});
```

**[⬆ back to top](#목차)**

## 상수(constant)

- 상수는 밑줄로 구분 된 UPPER_SNAKE_CASE (대문자 + 언더스코어로만 구성) 로만 선언

```JavaScript
// bad
const number = 5;

// good
const NUMBER = 5;
```

**[⬆ back to top](#목차)**

## 문자열(String)

- 문자열은 single quotes (`''`) 사용

```JavaScript
// bad
const name = "Indenter";

// good
const name = 'Indenter';
```

- template strings 사용

```JavaScript
// bad
function sayHi(name) {
  return 'How are you, ' + name + '?';
}

// bad
function sayHi(name) {
  return ['How are you, ', name, '?'].join();
}

// good
function sayHi(name) {
  return `How are you, ${name}?`;
}
```

**[⬆ back to top](#목차)**

## 모듈(Modules)

- 비표준 모듈시스템이 아니면 항상 (`import`/`export`) 를 이용
- 파일 경로는 상대 경로가 아닌 절대 경로로 참조

```JavaScript
// bad
const IndentStyleGuide = require('./IndentStyleGuide');
module.exports = IndentStyleGuide.es6;

// ok
import IndentStyleGuide from '@/src/IndentStyleGuide';
export default IndentStyleGuide.es6;

// best
import { es6 } from '@/src/IndentStyleGuide';
export default es6;
```

- wildcard import는 이용 금지

```JavaScript
// bad
import * as IndentStyleGuide from './IndentStyleGuide';

// good
import IndentStyleGuide from '@/src/IndentStyleGuide';
```

**[⬆ back to top](#목차)**

## 조건문(Conditional statements)

- 조건문은 단축식을 사용

```JavaScript
// bad
if (name !== '') {
  // ...
}

// good
if (name) {
  // ...
}

// bad
if (reviews.length > 0) {
  // ...
}

// good
if (reviews.length) {
  // ...
}
```

- 조건문은 의미 있는 내용을 사용

```JavaScript
const score = 30;

// bad
if (score >= 0 && score <= 70) {
  // ...
}

// good
const isWin = score >= 0 && score <= 70;
if (isWin) {
  // ...
}
```

**[⬆ back to top](#목차)**

## 형변환과 강제(Type Casting & Coercion)

- 문의 선두에서 형의 강제를 행함
- String

```JavaScript
let reviewScore = 9;

// bad
const totalScore = reviewScore + '';

// good
const totalScore = String(reviewScore);
```

- Number
- `Number` 로 형변환하는 경우는 `parseInt` 를 이용하고, 항상 형변환을 위한 기수를 인수로 전달

```JavaScript
const inputValue = '4';

// bad
const numberValue = new Number(inputValue);

// bad
const numberValue = +inputValue;

// bad
const numberValue = inputValue >> 0;

// bad
const numberValue = parseInt(inputValue);

// good
const numberValue = Number(inputValue);

// good
const numberValue = parseInt(inputValue, 10);
```

- Boolean

```JavaScript
const score = 0;

// bad
const hasScore = new Boolean(score);

// good
const hasScore = Boolean(score);

// good
const hasScore = !!score;
```

**[⬆ back to top](#목차)**

## 명명규칙(Naming Conventions)

- 변수명, 함수명은 의미를 알 수 있게 camelCase를 사용하여 선언
- 함수명은 항상 '동사'로 시작해야 하며 do, execute등 의미를 명확히 알 수 없는 이름 금지
- 축약형은 사용 금지

```JavaScript
// bad
const rr = [];
const review_results = [];
const rd = {};
const review_data = {};
const fn = () => {};

// good
const reviewResults = [];
const reviewData = {};
const handleReviewStatus = () => {};
```

- 컴포넌트명과 파일명(or 폴더명)은 PascalCase를 사용

```JavaScript
// CheckBox.tsx
const CheckBox = () => {
  // ...
}
export default CheckBox;

// in some other file
// bad
import CheckBox from './checkBox';

// bad
import CheckBox from './check_box';

// good
import CheckBox from '@/components/CheckBox';
```
