# 모던 리액트 Deep Dive 스터디 week4

## 서버 사이드 렌더링: 서버 사이드 렌더링을 위한 리액트 API 살펴보기

기본적으로 리액트는 SPA 환경에서 렌더링할 수 있는 방법을 제공하지만 이와 동시에 서버에서 렌더링할 수 있는 API도 제공한다. 이 API는 당연히 브라우저의 window 환경이 아닌 Node.js와 같은 서버 환경에서만 실행할 수 있다. 이러한 API를 확인해 보려면 리액트 저장소의 `react-dom/server.js`를 확인하면 된다.
(리액트 18이 릴리즈되면서 `react-dom/server`에 renderToPipeableStream이 추가됐고, 나머지는 대부분 지원 중단되는 등 큰 변화를 거쳤다.)

### 1. renderToString

인수로 넘겨받은 리액트 컴포넌트를 렌더링해 HTML 문자열로 반환하는 함수이다. 서버 사이드 렌더링을 구현하는 데 가장 기초적인 API로, 최초의 페이지를 HTML로 먼저 렌더링하는 역할을 하는 함수이다. renderToString은 인수로 주어진 리액트 컴포넌트를 기준으로 빠르게 브라우저가 렌더링할 수 있는 HTML을 제공하는 데 목적이 있는 함수일 뿐이므로 클라이언트에서 실행되는 자바스크르비트 코드를 포함시키거나 렌더링하는 역할까지 해주지는 않는다.

### 2. renderToStaticMarkup

`renderToString`과의 유의미한 차이점은 루트 요소에 추가한 `data-reactroot`와 같은 리액트에서만 사용하는 추가적인 DOM 속성을 만들지 않는다는 점이다. 해당 속석을 제거하면 결과물인 HTML의 크기를 아주 약간이라도 줄일 수 있다는 장점이 있다. 하지만, 이 함수를 실행한 결과로 렌더링을 수행하면 클라이언트에서는 리액트에서 제공하는 `useEffect`와 같은 브라우저 API를 절대로 실행할 수 없다. 즉, renderToStaticMarkup은 리액트의 이벤트 리스너가 필요 없는 완전히 순수한 HTML을 만들 때만 사용된다.

### 3. renderToNodeStream

`renderToString`과 결과물이 완전히 동일하지만 두 가지 차이점이 있다.

먼저 첫 번째 차이점으로 앞에서 살펴본 두 API인 `renderToString`과 `renderToStaticMarkup`은 브라우저에서도 실행할 수는 있지만 `renderToNodeStream`은 브라우저에서 사용하는 것이 완전히 불가능하다는 점이다. 두 번째 차이점은 결과물의 타입이다. `renderToString`은 이름에서도 알 수 있듯 결과물이 string인 문자열이지만, `renderToNodeStream`의 결과물은 Node.js의 `ReadableStream`이다. 스트림을 활용한다면 브라우저에 제공해야 할 큰 HTML을 청크로 쪼개 연속적으로 작성함으로써 리액트 애플리케이션을 렌더링하는 Node.js 서버의 부담을 덜 수 있다.

### 4. renderToStaticNodeStream

renderToStaticMarkup과 마찬가지로 리액트 자바스크립트에 필요한 리액트 속성이 제공되지 않는다.

### 5. hydrate

`renderToString`과 `renderToNodeStream`으로 생성된 HTML 콘텐츠에 자바스크립트 핸들러나 이벤트를 붙이는 역할을 한다. hydrate는 이처럼 정적으로 생성된 HTML에 이벤트와 핸들러를 붙여 완전한 웹페이지 결과물을 만든다. render와의 차이점으로 hydrate는 기본적으로 이미 렌더링된 HTML이 있다는 가정하에 작업이 수행되고, 이 렌더링된 HTML을 기준으로 이벤트를 붙이는 작업만 실행한다. 아무것도 없는 빈 HTML에 렌더링하는 render와의 차이점이 이것이다.

<!-- ### 6. SSR 예제

#### 1) index.tsx -->
