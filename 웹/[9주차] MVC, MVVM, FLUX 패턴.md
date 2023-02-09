# MVC, MVVM, FLUX 아키텍쳐

# 아키텍쳐란?

→ 잘 정리된 옷장!

옷장에 옷을 그냥 넣으면 옷을 ‘보관’할 수 는 있지만, 내가 원할 때 필요한 옷을 쉽게 꺼낸다는 `목적성`은 일게 된다. 따라서 ‘**잘 꺼낼 수 있도록 잘 넣어두는 방법**’을 고려해야 한다. 이를 효과적으로 하기 위해 비슷한 옷끼리 같이 두는 등 규칙을 만들면, 이 과정에 패턴이 만들어지면서 ‘이 패턴에 맞는 목적성을 가진 **옷장**’이 등장하게 된다. 그렇다면 단순한 옷장에 비해 세부적인 규칙을 잘 모르더라도 옷을 정리하기가 훨씬 편해지게 된다.

> 아키텍처란 구조화 된 옷장과 비슷하다.

처음 개발할 때에는 규칙없이 그저 코드를 작성하다가, 덩치가 커지면 불편하고 정리가 안되는 시점이 생긴다. 따라서 **처음부터 몇가지 규칙을 지켜가며 개발을 하면 훨씬 편해지는데**, 이러한 것들이 반복 되어 **하나의 패턴이 만들어진다**. 이러한 패턴들을 **모두가 이해하고 따를 수 있도록 하는 구조를 ‘아키텍쳐’** 라고 할 수 있다.

> 옷장에서 상의, 하의, 양말처럼 서로 비슷한 것끼리,
> 그리고 섞이면 안 되는 것끼리 구분을 하는 것이 중요하다.

---

# MVC 패턴

### Model (데이터)

- 데이터 로직과 DB와의 소통을 하는 부분
- 화면에 반영되는 실제 데이터.

### View (화면)

- 데이터를 보여주는 부분
- 대게 HTML과 CSS로 만들어지는 결과물을 의미함.

### Controller (컨트롤러)

- 사용자의 입력을 받고 처리하거나 데이터의 변화에 맞게 화면을 변경하도록 하는 부분.
- 데이터를 화면에 그리고, 이벤트가 발생하면 데이터가 바뀌는데 데이터가 바뀌면 화면도 바뀌어야 한다. 이렇게 Model에서 데이터를 받아 화면이 그리고, 화면으로부터 사용자의 동작을 받아 Model을 변경하는, 즉 Model과 View 사이의 중간 역할을 하는 것이 Controller이다.

## MVC 패턴 작동 로직(패턴)

<img width="560" alt="d" src="https://user-images.githubusercontent.com/68051794/217755994-7c67d325-bf3d-4121-a97c-212c92845521.png">

1. 유저가 서버에게 리퀘스트를 전송
2. Controller가 리퀘스트를 Model에게 전달
3. Model이 리퀘스트에 상응하는 부분을 DB로부터 가져옴
4. Model이 DB로부터 받아온 데이터를 Controller에게 전달
5. Controller가 화면에 보여지기 위해 수정될 데이터들을 View에게 전달

   → 받아온 데이터가 human-readable한 형식이 아닐수도 있기 때문에 Controller가 바로 유저에게 반환할 수 없다.

6. View가 데이터를 보기좋게 정리, 정렬한 후 스타일을 더해 Controller에게 보내준다.
7. Controller가 유저에게 화면을 보여준다.

## MVC 패턴 예시

<img width="885" alt="asdf" src="https://user-images.githubusercontent.com/68051794/217756075-eaa8c597-d7d6-4858-a3a4-c292f34b8b89.png">

## MVC 패턴의 특징

- 보편적으로 널리 사용되어왔고 사용되는 패턴.
- 양방향 데이터 바인딩을 기본으로 함.
  - View가 변경되면 Controller에 반영되고, 이를 Model에 반영한다. Model이 변경되면 Controller에 반영되고, 이를 View에 반영한다.
- View와 Model 사이의 의존성이 높음.
- 대표적인 MVC 패턴의 웹 프레임워크
  - Django, Laravel, Express JS, Flask, Ruby on Rails …
- MVC라는 것에 대한 개념이 이제는 확실해 졌지만 웹 서비스 초창기 시절의 MVC는 다음과 같았음.
  - Model : 데이터베이스
  - View : HTML, CSS, javascript까지 포함한 클라이언트 영역
  - Controller : 가운데서 라우터를 통해 데이터를 처리하고 새로운 HTML을 만들어 보여주는 백엔드 영역!
- **리액트 자체가 MVC 패턴인 것이 아님!**
  - 공식 문서에서도 언급, MVC 패턴을 리액트로 구현할 수 있는 것은 가능.

### 문제점

MVC 패턴으로 작업하다보니 데이터를 찾아서 데이터를 바꾸고 데이터를 수정하고 이벤트를 연결하고 이벤트를 수정하는 부분들에서 소모적인 반복적 패턴이 나타남.

---

# MVVM (Model View ViewModel)

### ViewModel

- View를 표현하기 위해 만든 ‘Model’
- View를 나타내기 위해 데이터를 처리하는 부분.

## MVVM 구조의 동작 과정

![IMG_D16F4E7D4D91-1](https://user-images.githubusercontent.com/68051794/217756125-4fde2617-59b3-41a0-a74f-f56cbe37d442.jpeg)

### MVC 패턴으로 부터 변한 것

- Model이 변하면 View를 수정하고, View에서 이벤트를 받아서 Model을 변경한다는 Controller의 역할은 그대로이지만, 이를 구현하는 방식이 (jQuery와 같이) DOM 조작에서, `템플릿과 바인딩을 통한 선언적인 방법`으로 변화.
- Dom을 조작하는 코드는 프레임워크가 맡게 됨. 개발자는 화면에 그려져야할 데이터만 만들어서 프레임워크에 전달해주면, 프레임워크가 알아서 그려주게 됨.

→ 이를 View를 그리는 Model만 다루게 되었다 하여(Controller는 뷰를 그리는 부분 뿐만이 아닌 라우팅, 그 이후엔 돔 조작 등 다양한 역할 수행했던것과 반해) ViewModel 이라고 부르며 이 방식을 MVVM이라고 칭하게 됨.

## MVC vs MVVM

- 컨트롤러의 반복적인 기능이 선언적인 방식으로 개선
- Model과 View의 관점을 분리하지 않고 `하나의 템플릿으로 관리하려는 방식`으로 발전했다. (기존에는 class, id 등을 사용해 간접적으로 HTML에 접근하려고 한것과 반해 이제는 직접적으로 HTML에 접근하는 방법으로 확장됨.)

→ React, Vue, Angular2, Svelte 등 디테일만 다를 뿐, MVVM이라는 아키텍쳐는 그대로 유지.

---

# Component와 Container-Presenter 패턴

- MVVM을 얻은 프론트엔드 개발은, 웹의 DOM API를 잘 다루지 못하더라도 비지니스 로직에만 집중하면 금방 서비스를 만들 수 있게 됨.
- 이후 하나의 Page단위가 아닌, Page안에 여러가지 모듈이 있고, Modal등 여러 화면들이 하나의 화면에서 구성이 될 수 있도록 발전함.

## Component 패턴

컴포넌트는 재사용이 가능해야 한다는 원칙에 따라 가급적 비즈니스 로직을 포함시키지 않으며 개발을 하게 되었으며, 이는 곧 컴포넌트 구조가 복잡해짐에 따라 Props Drilling Problem이 발생하게 됨.

![Untitled](https://user-images.githubusercontent.com/68051794/217756171-1557cdfd-37f5-47df-b403-38ee0d1eb5e5.png)

이를 해결하게 위해 새로 개발된 아키텍쳐가 **FLUX 패턴**.

---

# FLUX 패턴과 Redux

![Untitled 1](https://user-images.githubusercontent.com/68051794/217756219-29d284c4-9d85-486f-8bd5-ccd0dd1d2328.png)

![Untitled 2](https://user-images.githubusercontent.com/68051794/217756286-466051ae-06c9-4784-b2fb-f7e18210cf17.png)

- 프로젝트가 커질수록 모델과 뷰가 복잡하게 얽히며 Props Drilling Problem현상이 나타나는 등 단점이 분명한 MVC패턴의 대안으로 나온 패턴.

## FLUX 패턴의 구성요소

### Action

- 액션은 컴포넌트에서 데이터를 store로 전달한다.

### Dispatcher

- Action을 발생시키는 것.

### Store

- 앱의 전체 상태 트리를 가지고 있는 저장소. 현재 Redux 어플리케이션의 state를 가지고 있는 객체.

### Reducer

- 변화를 일으키는 함수로, 현재 state와 Action 객체를 받아 필요한 경우 상태를 업데이트 하는 방법을 결정하고, 새로운 상태를 반환 함.

## FLUX 패턴의 특징

![Untitled 3](https://user-images.githubusercontent.com/68051794/217756335-69f21f68-d2c0-4e99-ba51-e2d39cf0dfa4.png)

- `단일 흐름` !
- 기존의 컴포넌트를 지향하는 MVC가 아니라, View를 하나의 범주로 두고 View에서 Action을 호출하면 Dispatcher를 통해 Store라는 공간에 Data가 보관이 되고 다시 View로 전달되는 흐름.

![Untitled 4](https://user-images.githubusercontent.com/68051794/217756393-57a23f30-2647-44b3-be14-c07498587e22.png)

## Redux

![https://velog.velcdn.com/images%2Fteo%2Fpost%2F68ce57e2-d55c-4bba-9d4a-8c6bb3cfc795%2F999E564F5C0F63972E.gif](https://velog.velcdn.com/images%2Fteo%2Fpost%2F68ce57e2-d55c-4bba-9d4a-8c6bb3cfc795%2F999E564F5C0F63972E.gif)

![https://velog.velcdn.com/images%2Fteo%2Fpost%2F3e4ab548-241d-4382-b226-e920d97a44a3%2F99CC6B4F5C0F639E2C.gif](https://velog.velcdn.com/images%2Fteo%2Fpost%2F3e4ab548-241d-4382-b226-e920d97a44a3%2F99CC6B4F5C0F639E2C.gif)

- 기존의 Props Drilling Problem의 문제를 정확하게 짚어줌.
- Flux 패턴의 Store, Dispatch, Reducer에 대한 개념을 정확하게 정리함.

### FLUX 패턴 정리

Flux 패턴은 View를 각각의 MVC 컴포넌트 관점으로 보는 것이 아니라 하나의 큰 View로 이해하고, View에서는 Dispatch를 통해서 Action을 전달하면 Action은 Reducer를 통해서 Data가 Store에 보관되고, Store에 들어있는 데이터는 다시 View로 연결 되는 방식을 지향함.

## MVC vs FLUX

- FLUX는 (공통적으로 사용되는 비지니스 로직의 Layer)와 (View의 Layer)를 완전히 분리되어 상태관리라는 방식으로 관리한다.
- 각각의 독립된 컴포넌트가 아니라 `하나의 거대한 View 영역`으로 간주한다.
- 둘 사이의 관계는 Action과 Reduce라는 인터페이스로 분리하며, Controller는 이제 양방향이 아니라, 단방향으로 Cycle을 이루도록 설계되었다.

FLUX 패턴은 각 프레임워크 진영에게 많은 영감을 주었으며, 프레임워크와 더불어 본격적인 상태관리 라이브러리들이 만들어지기 시작하게 되었다. Redux, Vuex등이 대표적인 상태관리 라이브러리이다.

---
