# 모던 리액트 Deep Dive 스터디 week2

## 리액트 핵심 요소 깊게 살펴보기: JSX란?

JSX는 자바스크립트 표준 코드가 아닌 페이스북이 임의로 만든 새로운 문법이기 때문에 JSX는 반드시 트랜스파일러를 거쳐야 비로소 자바스크립트 런타임이 이해할 수 있는 의미 있는 자바스크립트 코드로 변환된다. <br/>
JSX는 HTML이나 XML을 자바스크립트 내부에 표현하는 것이 유일한 목적은 아니다. JSX의 설계 목적은 다양한 트랜스파일러에서 다양한 속성을 가진 트리 구조를 토큰화해 ECMASCript로 변환하는 데 초점을 두고 있다. JSX 내부에 트리 구조로 표현하고 싶은 다양한 것들을 작성해 두고, 이 JSX를 트랜스파일이라는 과정을 거쳐 자바스크립트(ECMAScript)가 이해할 수 있는 코드로 변경하는 것이 목표라고 볼 수 있다.

### JSX의 정의

#### JSXElement

JSX를 구성하는 가장 기본 요소로, HTML의 요소(element)와 비슷한 역할

- JSXOpeningElement
- JSXClosingElement
- JSXSelfClosingElement
- JSXFragment

JSXElement의 요소 이름으로는 자바스크립트 식별자 규칙과 동일하게 숫자로 시작하거나 $와 \_ 외의 다른 특수문자로는 시작할 수 없다. JSXIdentifier(JSX 식별자)끼리는 `:`와 `.`을 통해서 조합하고 묶을 수 있지만 `:`의 경우에는 다른 하나와만 묶일 수 있다.

#### JSXAttributes

JSXElement에 부여할 수 있는 속성

- JSXSpreadAttributes
- JSXAttribute: 키는 JSXAttributeName, 값은 JSXAttributeValue

#### JSXChildren

JSXElement의 자식 값

- JSXChild

#### JSXStrings

### JSX는 어떻게 자바스크립트에서 변환될까?

자바스크립트에서 JSX가 변환되는 방식을 알려면 리액트에서 JSX를 변환하는 `@babel/plugin-transform-react-jsx` 플러그인을 알아야 한다.

```js
// JSX 코드
const Component = (
  <div>
    <span>hello world</span>
  </div>
);
```

```js
// JSX 코드를 @babel/plugin-transform-react-jsx로 변환한 결과
"use strict";

var Component = React.createElement(
  "div",
  null,
  React.createElement("span", null, "hello world")
);
```

```js
// 리액트 17, 바벨 7.9.0 이후 버전에서 추가된 자동 런타임(automatic runtime)으로 트랜스파일한 결과

"use strict";

var _jsxRuntime = require("custom-jsx-library/jsx-runtime");

var Component = (0, _jsxRuntime.jsx)("div", {
  children: (0, _jsxRuntime.jsx)("span", {
    children: "hello world",
  }),
});
```

#### JSX가 변환되는 특성 활용

JSX 반환값이 결국 `React.createElement`로 귀결된다는 사실을 파악한다면 쉬운 리팩토링 가능

```ts
// JSX가 변환되는 특성을 활용하여 삼항 연산자를 쓰지 않고 간결하게 처리
import { createElement, PropsWithChildren } from "react";

function TextOrHeading({
  isHeading,
  children,
}: PropsWithChildren<{ isHeading: boolean }>) {
  return createElement(
    isHeading ? "h1" : "span",
    { className: "text" },
    children
  );
}
```

<hr />

JSX 문법에는 있지만 실제로 리액트에서 사용하지 않는 것

- JSXNamespacedName
- JSXMemberExpression
