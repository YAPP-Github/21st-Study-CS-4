- TTFB (Time to First Byte) : 네트워크 요청 후에 첫 바이트가 도달하는 시점
- FP (First Paint) : 처음으로 사용자에게 픽셀로 보여지는 시점
- FCP (First Contentful Paint) : 요청된 컨텐츠가 보여지는 시점
- TTI (Time To Interactive) : 페이지가 상호작용이 가능해지는 (interactive) 시점

## Client Side Rendering (CSR)

자바스크립트를 사용하여 브라우저에서 페이지를 렌더링하는 방식.
모든 로직, data fetching, routing은 클라이언트에서 처리된다.

<img src="https://user-images.githubusercontent.com/62097867/217820107-54f6d716-a3a7-4877-8ca7-15f668c02dbe.png" width="600px" />

1. 브라우저는 서버로부터 비어있는 index.html 파일을 응답받는다.
2. js bundle을 로드하여 동적으로 DOM을 생성한다.
3. 페이지를 렌더링한다.

### 장점

- 초기 로딩 이후에는 모든 스크립트가 이미 로드된 상태이므로 동작 속도가 빠르다.
- 부드러운 화면 전환으로 사용자 경험이 향상된다.
- 서버 측 부하가 적다.

### 단점

- 초기 페이지 로딩 속도가 느리다.
  - 자바스크립트 번들이 커질수록 더욱 지연된다.
  - 사용자는 페이지가 표시되기 전까지 빈 페이지를 보게 되기 때문에 사용자 경험이 좋지 않다.
  - code splitting, tree-shaking을 통해 초기 로딩 개선 가능

- SEO에 불리하다. 검색 엔진 크롤러가 데이터를 제대로 수집하지 못할 수 있다.
  - 사전 렌더링을 통해 SEO 개선 가능 (라이브러리, webpack 플러그인 사용)

## Server Side Rendering (SSR)

서버에서 페이지에 대한 전체 HTML을 생성하여 응답하는 방식.
data fetching이 미리 처리되므로 추가적인 round trip을 피할 수 있다.

<img src="https://user-images.githubusercontent.com/62097867/217781175-6029d922-6d08-4d55-8222-391750e8517d.png" width="500px" />

1. 특정 페이지를 요청하면 서버는 html을 완성해서 응답한다.
2. 페이지를 렌더링한다.
3. js를 로드하고 hydration한다. 이로써 페이지는 상호작용이 가능하게(interactive) 된다.
4. 사용자가 페이지를 이동하거나 특정 액션으로 인한 요청이 발생할 때마다 이 과정을 반복한다.

### 장점

- 초기 페이지 로딩이 빠르다. 사용자가 컨텐츠를 빨리 볼 수 있다. (FP, FCP 향상)
- 검색엔진 최적화(SEO)에 유리하다.
- 서버에서 페이지 로직과 렌더링을 실행하면 클라이언트로 보내는 js 크기를 줄여 인터랙션을 빠르게 수행할 수 있다. (TTI 향상)

### 단점

- 페이지를 이동할 때마다 서버에 요청을 하기 때문에 서버 측 부하가 커질 수 있다.
- 페이지를 생성하는 시간이 걸리므로 종종 TTFB가 느려질 수 있다.

## Static Site Generation (SSG)

빌드시에 미리 각 url별로 별도의 html 파일을 생성해두고 이를 응답하는 방식

<img src="https://user-images.githubusercontent.com/62097867/217801623-eda1ad02-2d48-4a42-a347-e58637ad90aa.png" width="500px" />

### 장점

- SSR과 달리 미리 생성된 HTML을 불러오기 때문에 가장 빠른 방식이다. (FFTB 향상)
- 여러 CDN에 배포하여 edge caching을 활용할 수 있다.
- 검색엔진 최적화(SEO)에 유리하다.

### 단점

- 실시간 데이터를 반영하기 어렵다. 정적인 컨텐츠에 적합한 방식이다.
- 컨텐츠가 변경되는 경우 다시 빌드하고 배포해야 한다.

## CSR + SSR/SSG (Universal Rendering)

초기 로딩시에는 SSR/SSG 방식으로 동작하고 이후 브라우저에서 rehydration을 통해 CSR로 동작하는 방식

<img src="https://user-images.githubusercontent.com/62097867/217827141-643ec811-64a6-48fa-b596-3868ebcb3fa4.png" width="600pz" />

- hydration이 완료될 때까지 사용자 인터랙션 수행이 불가능하다. (TTV !== TTI)

https://web.dev/rendering-on-the-web/
