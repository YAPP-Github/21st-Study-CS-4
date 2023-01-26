# fiber

### React v15까지의 reconcillation

- 재귀적으로 일어나기 때문에 모든 리액트 엘리먼트를 순회할 때까지 자바스크립트 엔진을 독차지 하게 됨
- 프레임 드랍 발생→사용자 경험에 좋지 않음
- 애니메이션에 취약

### key points of scheduling

- 모든 업데이트가 UI에 즉각적으로 업데이트 될 필요 없다(오히려 발생하는 프레임 드랍이 사용자 경험을 저하시킴)
- 다른 종류의 업데이트는 다른 우선순위를 가져야 한다.
  - ex) 애니메이션 업데이트가 data 업데이트보다 더 빨라야 한다.
- 프레임워크(리액트)가 schedule work decision을 대신 해준다.

# Fiber

- 리액트의 핵심 알고리즘인 reconcillation을 재구성한 엔진
- 단일 fiber==가상의 stack frame
- 리액트는 변경 가능한 fiber 노드 트리(state, props, DOM요소) 생성
- single-linked-list로 만들어져있고, 부모 우선 순회를 진행

### 필요한 기능

- 작업을 중지하고 다시 실행
- 우선순위 지정
- 이전에 완료된 작업 재사용
- 필요 없는 작업 중단

## Fiber의 요소

**type**

- `<div>`, `<span>` 등 host 컴포넌트(문자열) 이거나 합쳐진 컴포넌트인 클래스나 함수

**key**

- reconcilation 과정에서 fiber가 재사용될 수 있는지를 결정

**child**

- 컴포넌트에서 `render()` 을 호출할 때 반환하는 값

```jsx
const Component = (props) => {
  return <div>blah blah blah </div>;
};
```

여기서 `<div>` 를 반환하기 때문에 `<div>` 가 child가 된다.

**sibling**

- `render()` 가 반환하는 엘리먼트 목록

```jsx
const Component=(props)=>{
	return ([<Child/>, <Child2>])
}
```

`Child` 와 `Child2`가 sibling이며 단순 연결 리스트로 연결되어 있음

**return**

- 프로그램이 현재의 fiber을 처리하고 리턴해야하는 fiber
- fiber에 여러 child fiber들이 있으면, 각 child fiber의 return fiber는 parent이다.

**pendingProps**와 **memoizedProps**

- pendingProps는 컴포넌트로 전달된 props이고, memoizedProps는 노드의 props를 저장하고 실행 스택의 끝에서 초기화
- pendingProps와 memoizedProps가 같은 값이면 fiber의 이전 값을 사용해 불필요한 계산 방지

**pendingWorkPriority**

- 작업의 우선순위를 숫자로 나타냄

**alternate**

- 컴포넌트는 최대 두 개의 fiber(flushed fiber과 work-in-progress fiber)를 가질 수 있음
  - 각 fiber은 서로의 alternate임
- alternate는 `cloneFiber` 을 통해 만들어지는데 만약 alternate가 존재한다면 재사용하며 allocation을 최소화

**output**

- React 어플리케이션의 leaf 노드
- JSX의 경우 lowercase tag name을 의미한다.
- 실제 output은 host component의 leaf 노드에서만 생성되며, 그 다음 output은 트리 위로 전달된다.
- 최종적으로 output은 rendering 환경으로 변경사항을 flush할 수 있도록 renderer에게 전달된다.
  ![Untitled](https://user-images.githubusercontent.com/55427367/214853176-4ee3485a-b7b7-4d82-b76a-6cf08e74efec.png)

[https://github.com/acdlite/react-fiber-architecture](https://github.com/acdlite/react-fiber-architecture)
