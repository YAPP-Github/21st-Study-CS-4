# React Fiber

# Element와 컴포넌트

```jsx
// <button onClick={}> Update counter </button>
{
     $$typeof: Symbol(react.element),
     type: 'button',
     key: "1",
     props: {
         children: 'Update counter',
         onClick: () => { ... }
 }
```

<aside>
💡 `Element`는 DOM 노드와 컴포넌트를 표현하는 변경 불가능한 객체이다
`컴포넌트`는 렌더링할 Element를 반환하는 작고 재사용 가능한 코드이다

</aside>

리액트가 함수/클래스 컴포넌트를 렌더링 한다는 것은 반환된 JSX 구문을 React Element 형식으로 바꾼다는 걸 뜻한다

- `React.createElement()`의 반환값이다

일반적으로 Virtual DOM이라고 알려져 있지만 공식문서에선 UI Tree라고 부른다

각 컴포넌트들의 Element 객체가 모인 전체 UI Tree를 `Render` 과정에서 생성한다

이후 재조정 과정에서 React Element 는 각각 해당되는 Fiber Node 로 병합된다

# React Fiber

<aside>
💡 **모든 컴포넌트 인스턴스를 `추적`하는 데이터 구조이며
재조정 과정에서 하나의 `작업 단위`이다**

1. 해당 컴포넌트 트리 지점에서 렌더링 되어야할 `타입`
2. 관련된 `props`, `state`
3. 상위, 형제, 하위 컴포넌트에 대한 `포인터`
4. 렌더링 프로세스를 추적하는 데 사용하는 기타 내부 데이터

</aside>

React Element가 처음으로 생성될 때, 해당 데이터를 사용하여 Fiber도 생성한다

생성된 Fiber는 이후 재조정 과정에서 재사용된다 

> **Element vs Fiber**
> 
> 
> 렌더링 사이클마다 생성되는 `Element` 와 달리 `Fiber` 는 매번 생성되지 않는다
> 
> **즉 컴포넌트의 상태와 DOM 노드를 유지하는 변경 가능한 데이터 구조이다**
> 

## 목적

<aside>
💡 React의 Fiber Architecture는 
렌더링 작업을 추적, 예약, 중지, 재개 가능하게 하는 기반을 제공한다

즉 리액트 렌더링 알고리즘의 **스케줄링 가능한 virtual stack이다**

</aside>

### Legacy Stack Reconciler

React 16 이전의 Stack Reconciler는 내부 Virutal DOM Tree를 재귀적으로 탐색하여 작업을 중간에 멈출 수 없었다

즉 Render 과정도 동기적으로 이루어지며, 시간이 오래 걸릴수록 그만큼 브라우저를 block 하게 되어 사용자에게 안좋은 경험도 주었다

### **Concurrent React**
![image](https://user-images.githubusercontent.com/74011724/214883946-ed7dac5d-3f7f-4dd0-8998-6b95e1106047.png)


따라서 React는 기존 Stack reconciler의 문제점을 해결하기 위해 내부적으로 아키텍처를 재작성 했는데 이를 Fiber Architecture 라고 부른다

브라우저를 block 시키지 않도록 idle 타임에 렌더링 알고리즘을 스케줄링한다

- 또한 Fiber는 하나의 작업 단위이다

> **장점**
> 
> 
> 1. 작업을 멈추고, 나중에 다시 시작
> 
> 2. 다양한 종류의 작업에 우선순위 부여
> 
> 3. 완성된 작업의 결과 재사용
> 
> 4. 작업 중간에 버리기
> 

이 아키텍처는 추후 리액트의 동시성 패러다임을 구현하기 위한 필수적인 기반이 되었다

- Suspense, Transition
- Concurrent UI

## 구조
![image](https://user-images.githubusercontent.com/74011724/214884029-f7ba2a28-6a15-47f0-9f22-cb0af3e69f87.png)


```jsx
{
    stateNode: new ClickCounter,
    type: ClickCounter,
    alternate: null,
    key: null,
    memoizedState: {count: 0},
    pendingProps: {},
    memoizedProps: {},
    nextEffect: null
}
```

모든 Fiber들은 `child`, `sibling`, `return` 속성을 사용하여 연결 리스트로 연결된다

이외의 속성들

- stateNode : 해당되는 **DOM** 노드 또는 **클래스** 컴포넌트의 인스턴스
- type : 클래스 컴포넌트의 생성자 / HTML 태그 / 함수 컴포넌트

![image](https://user-images.githubusercontent.com/74011724/214884205-7b962b0b-25d7-471e-a117-9c27f48667e3.png)


**React는 어떻게 변경 사항을 알 수 있을까?**

변경을 추적하기 위해 비교 대상이 필요하며 이를 위해 `alternate` 속성을 도입했다

- 한 Fiber 노드의 사본을 갖는 것과 같으며
- 현재 브라우저에 표시된 값과 (current)
- 반영되어야 하는 새로운 변경 사항으로 구분된다 (work in progress)

### Work in Progress

<aside>
💡 리액트가 재조정 과정 완료 후 미래에 반영할 UI Tree 

**재조정 과정의 모든 작업들은 WIP 트리에서 수행된다**
WIP 트리가 화면에 렌더링 되면, Current 트리가 되며 서로의 포인터를 바꾼다

</aside>

Current 트리를 거치면서 기존 Fiber에 대한 Alternate Fiber를 필요한 경우 생성한다

- 두 트리의 Fiber 노드는 alternate 필드에서 서로를 가리키고 있다

# 재조정

컴포넌트의 props 또는 상태가 **변경**되면 

이전에 렌더링 된 요소와 비교해 **필요한 요소만 다시 그리는 과정**이다

## Render

<aside>
💡 컴포넌트를 렌더링하고 변경 사항을 계산하는 과정이다
Fiber 트리를 순회하면서 변경 사항들을 effect list로 모은다

</aside>

**특징**

- **비동기적**이며 **순수**해야 한다
- 1개 이상의 Fiber 노드를 처리한 다음, 일부 이벤트에 의해 **작업이 중지**될 수 있다
- 작업이 다시 재개 될 때 **이전에 중단된 위치에서 계속**된다
- 부모 컴포넌트가 렌더링 되면 일반적으로 자식 컴포넌트도 다시 렌더링 된다

**변경 사항에는 어떤 것들이 있을까?**

1. `type` 변경
    1. 호스트 컴포넌트 (DOM) : 태그 변경
    2. 함수/클래스 컴포넌트 : 레퍼런스 값 변경
    3. 해당 하위 Fiber 트리를 모두 **삭제**하고, 처음부터 다시 만든다
2. `key` 변경 
    1. 해당 하위 Fiber 트리를 모두 **삭제**하고, 처음부터 다시 만든다
3. `attibutes`, `state`, `props` 변경
    1. WIP Fiber를 **업데이트** 한다

> 컴포넌트의 **Rendering은 DOM 업데이트와 같지 않다**

 *1. 컴포넌트가 지난번과 동일한 렌더 출력을 반환할 수 있다
 2. Concurrent 모드인 경우 **한 컴포넌트를 여러번 렌더링** 할 수 있지만 
     다른 업데이트로 인해 **무효화 되는 경우 렌더 출력을 버릴 수 있다**
 3. strict mode 시 2번 실행된다*
> 

### Traverse

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/05b6ee5e-b04a-409e-ad39-76c755357714/Untitled.png)

재조정 알고리즘은 항상 최상위 Root Fiber 노드에서 시작한다

그러나 완료되지 않은 작업(setState)이 있는 노드를 찾을 때까지 중간 Fiber들을 빠르게 건너뛴다

- 건너뛴 Fiber와 하위 자식 노드들은 WIP Fiber 노드가 생성되지 않고
- 기존 Fiber를 **재사용**한다

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/99d0bb29-6c40-41b2-a1f6-c135fbd86d9d/tmp2.gif?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20230126%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20230126T155718Z&X-Amz-Expires=86400&X-Amz-Signature=73f64c8ac477f373d70794328909dfe7392cae4d0b428bd005fc26bf81c6b5c6&X-Amz-SignedHeaders=host&x-id=GetObject)


```jsx
function performUnitOfWork(unitOfWork) {
  var current = unitOfWork.alternate;
  setCurrentFiber(unitOfWork);

  var next = beginWork$1(current, unitOfWork, subtreeRenderLanes);

  if (next === null) {
    // If this doesn't spawn new work, complete the current work.
    completeUnitOfWork(unitOfWork);
  } else {
    workInProgress = next;
  }
}
```

- `performUnitOfWork` : 해당 Fiber 노드 작업 시작
- `beginWork` : 다음에 작업할 Fiber의 포인터 반환 (우선순위 : 자식 > 형제)
- `completeUnitOfWork` : 해당 Fiber 작업 완료 시 부모로 반환, **effect list 생성**

**흐름**

1. setState 발생 시 해당 Fiber부터 순회
2. 자식 노드에 대한 WIP Fiber 생성
    1. 생성되지 않으면 해당 노드의 작업은 완료되며 effect list는 부모 노드에 합쳐지고 **형제** 노드로 순회
        1. 형제 노드도 없다면 부모 노드로 되돌아감
    2. 생성되면 **자식** 노드로 순회
3. 변경 사항 발생 시 effect list에 저장
4. idle time 확인 후 시간이 남아있으면 다음 노드로 순회
5. 더이상 노드가 없다면 `commit`을 기다리는 상태로 바뀜
    1. effect list 모두 수집 완료

### Effect List

<aside>
💡 컴포넌트 렌더링/업데이트 이후 일어나는 `부작용`들도 Fiber로 관리한다
각 Fiber 노드는 연관된 Effect가 있을 수 있으며, flags 필드로 관리된다

</aside>

재조정 과정 중 또는 이후 **컴포넌트마다 추가적으로 수행해야 하는 작업**을 정의한다 

- 호스트 컴포넌트 : DOM 노드 추가, 업데이트, 제거
- 클래스/함수 컴포넌트 : 수명주기 메서드 호출

<img width="660" alt="image" src="https://user-images.githubusercontent.com/74011724/214884702-bbcf593b-8860-450a-a7fc-186ef4368408.png">

effect를 빠르게 처리하기 위해 해당되는 Fiber들의 Linked List를 작성한다

- Fiber updateQueue의 `firstEffect`, `nextEffect` 필드로 구현된다

> DOM 조작 또는 수명 주기 효과가 있는 Fiber들을 묶는다
**Render 단계의 결과이며, 추후 Commit 단계에서 처리된다**
> 

## Commit

<aside>
💡 Render 단계에서 일어난 변경 사항(effect list)들을 적용한다

</aside>

**특징**

- **동기적**이며 사이드 이펙트가 일어나도 된다 (DOM 조작 등)
- **DOM 업데이트**가 일어나며 이는 사용자가 볼 수 있기 때문에
- **중지되지 않고 항상 단일 패스로 수행된다**

두 가지 과정으로 수행되는데 

1. 첫 번째 과정에서 **모든 DOM 조작**이 일어난다
1. 그 후 두번째 과정에서 **수명주기** 메서드와 **ref**를 설정한다

# 정리

- 컴포넌트의 렌더링은 Element라고 부르는 일반 객체로 표현되고, 이 데이터를 활용하여 Virtual DOM 구현체인 Fiber가 관리된다
- Fiber는 컴포넌트의 내부 데이터를 추적하고 재조정 과정 시 하나의 작업 단위가 된다
- 컴포넌트의 state와 props 등이 바뀌면 해당 변경 사항을 Real DOM에 반영해야 하는데, 변경 사항들을 추적하고 반영하는 과정을 재조정이라고 한다
- 재조정 과정은 크게 Render와 Commit으로 구분된다
    - Render 과정은 비동기적이며 중지 가능하고 추후 반영할 effect list를 모은다
    - Commit 과정은 동기적이며 중지가 불가능하고 실제 DOM 변경이 일어나며 모은 effect list를 실행한다
- 변경 사항은 다음과 같이 분류된다
    - 컴포넌트의 type이 바뀔 때
    - 컴포넌트의 key가 바뀔 때
    - 컴포넌트의 state, props가 바뀔 때

---

**Reference**

- [https://indepth.dev/posts/1008/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react](https://indepth.dev/posts/1008/inside-fiber-in-depth-overview-of-the-new-reconciliation-algorithm-in-react)
- [https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html)
- [https://indepth.dev/posts/1501/exploring-how-virtual-dom-is-implemented-in-react](https://indepth.dev/posts/1501/exploring-how-virtual-dom-is-implemented-in-react)
