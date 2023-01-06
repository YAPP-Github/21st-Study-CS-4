## SOP (Same-Origin Policy)와 CORS (Cross-Origin Resource Sharing)

`SOP` - **같은 출처(Same-Origin)에서만 리소스를 공유**할 수 있도록 하는 보안 정책

`CORS` - HTTP 헤더를 사용하여 **다른 출처(Cross-Origin)의 리소스를 공유**할 수 있도록 하는 정책

보안을 위해 SOP 정책을 사용하였으나 다른 출처의 리소스를 가져와서 사용하는 경우가 많아지면서 CORS 정책을 지킨 리소스 요청을 허용하게 되었다.

CORS 등장 이전에는 `JSONP` 라는 방식을 사용하여 SOP를 우회하기도 했는데 현재는 보안상의 이슈로 사용하지 않는다.

### 📌 출처(Origin)를 판단하는 기준

url 구성 요소 중 protocol, host, port 3가지만 동일하다면 동일 출처로 판단한다.

예시: https://www.example.com:3000

| protocol | host            | port |
| -------- | --------------- | ---- |
| https    | www.example.com | 3000 |

## CORS의 동작 과정

### 단순 요청 (Simple Request)

예비 요청 없이 바로 본 요청을 보내고 CORS 정책 위반 여부를 확인하는 방식이다.

다음과 같은 조건을 만족하는 경우에는 예비 요청을 생략한다. (하지만 이를 모두 충족하는 상황은 드물기 때문에 대부분은 예비 요청으로 이루어짐)

- 메서드는 GET, POST, HEAD 중 하나
- 헤더는 Accept, Accept-Language, Content-Language만 허용
- Content-Type은 다음의 값들만 허용
  - application/x-www-form-urlencoded
  - multipart/form-data
  - text/plain

![image](https://user-images.githubusercontent.com/62097867/210764409-e94913a4-7689-49ac-b744-26c7f00365a6.png)

1. 클라이언트는 HTTP 요청 헤더의 `Origin`에 **요청을 보내는 출처**를 담아 전달한다.
2. 서버는 응답 헤더의 `Access-Control-Allow-Origin`에 **리소스를 접근하는 것이 허용된 출처**를 담아 전달한다.
3. 응답을 받은 브라우저는 자신이 보냈던 요청의 `Origin`과 서버가 보내준 응답의 `Access-Control-Allow-Origin`을 비교한다.
4. 응답이 유효하지 않다면 사용하지 않고 버린다.

> ⚠️ 출처를 비교하는 로직은 **브라우저**에 구현되어 있는 스펙으로 응답의 파기 여부는 브라우저가 결정한다. **서버는 CORS 정책 위반 여부를 판단하지 않기 때문에** CORS 정책을 위반하는 요청에 서버가 정상적으로 응답을 할 수도 있다.
>
> 브라우저가 CORS 정책 위반 여부를 판단하는 것은 응답이 도착한 이후이기 때문에 서버 쪽 로그에는 정상 응답을 했다는 로그만 남기 때문에 헷갈리면 안된다!

### 예비 요청 (Preflight Request)

요청을 한번에 보내지 않고 예비 요청(preflight)과 본 요청으로 나누어서 서버로 전송하는 방식이다.

**`OPTIONS`** HTTP 메소드를 사용하여 요청을 보내는 것이 안전한지 먼저 확인하고, 안전하다고 판단되면 본 요청을 보낸다.

![image](https://user-images.githubusercontent.com/62097867/210764632-6f669472-3297-45a7-8924-296a3fa74265.png)

1. 요청 헤더에 다음과 같은 정보들을 담아 `OPTIONS` 메서드로 preflight 요청을 전송한다.

- 요청 헤더의 `Origin`에 요청을 보내는 출처를 담는다.
- Access-Control-Request-Method - 실제 요청에서 사용할 메서드
- Access-Control-Request-Headers - 실제 요청에서 사용할 헤더들

2. 서버는 허용하는 메서드와 헤더에 대한 정보를 담아서 응답한다.

- Access-Control-Allow-Origin
- Access-Control-Allow-Methods
- Access-Control-Allow-Headers
- Access-Control-Max-Age - preflight가 브라우저에 캐시되는 시간

3. 응답을 받은 브라우저는 예비 요청과 응답을 비교한다.
4. 예비 요청이 성공적이라면 본 요청을 보낸다.

### 인증된 요청 (Credentialed Request)

인증 관련 정보를 포함할 때 사용되는 방식이다.

기본적으로 브라우저가 제공하는 XMLHttpRequest 객체나 fetch API는 별도의 옵션없이 브라우저의 쿠키 정보나 인증과 관련된 헤더를 요청에 담지 않는다.

인증과 관련된 정보를 담을 수 있게 해주는 옵션이 바로 `credentials` 옵션이다. 이 옵션에는 다음과 같은 값을 사용할 수 있다.

| 옵션 값              | 의미                                      |
| -------------------- | ----------------------------------------- |
| same-origin (기본값) | 같은 출처 간 요청에만 인증 정보를 담는다. |
| include              | 모든 요청에 인증 정보를 담는다.           |
| omit                 | 모든 요청에 인증 정보를 담지 않는다       |

`credentials` 값을 `include`로 사용하면 동일 출처 여부와 상관없이 요청에 인증이 포함된다. 이처럼 요청에 인증 정보가 담겨있는 상태에서 다른 출처의 리소스를 요청하게 되면 브라우저는 CORS 정책 위반 여부를 검사하는 룰에 두 가지를 추가하게 된다.

- Access-Control-Allow-Origin 에는 \*를 사용할 수 없으며, 정확한 출처를 명시해야 한다.
- 응답 헤더에 Access-Control-Allow-Credentials가 true로 설정되어야 한다.

## CORS 문제 해결 방법

### 개발 환경에서 프록시 설정하기

웹팩이 요청을 프록싱하여 CORS 정책을 우회할 수 있다.

1. CRA 프로젝트일 경우 package.json에 proxy 추가
2. webpack dev server의 proxy 기능

### 서버에서 Access-Control-Allow-Origin 세팅하기

- 서버에서 해당 값을 알맞게 세팅해주는 것이다.
- 가장 정석적이고 근본적인 해결책
- 와일드카드(\*)를 사용하게 되면 모든 출처에서 오는 요청을 받으므로 보안 이슈가 발생할 수 있다.

### 웹서버에서 처리
