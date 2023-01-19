## 등장 배경

DOM은 1) HTML 문서 내에 모든 노드의 구조적인 표현이기도 하고 2) 스크립트가 내용, 스타일, 문서 구조를 업데이트할 수 있도록 하는 인터페이스이기도 하다.

DOM 조작을 통해 동적으로 웹 페이지의 내용을 변경할 수 있고, 그 과정은 다음과 같다.

1. 브라우저가 조작할 대상 element를 찾는다.
2. 해당 element와 자식 element들을 DOM에서 제거하고 업데이트한 element로 교체한다.
3. 스타일과 레이아웃(또는 하나만)을 다시 계산한다.
4. 변경된 내용을 브라우저에 다시 그린다.

### DOM 조작의 문제점

DOM을 업데이트하는 데 렌더트리를 갱신하고 UI를 리렌더링하는 과정에서 성능 이슈가 발생할 수 있다.

## Virtual DOM

Virtual DOM은 실제 DOM의 UI를 가상으로 표현한 개념이다.
React는 Virtual DOM 개념을 사용하여 변경 사항을 실제 DOM에 바로 적용하지 않고 Virtual DOM에 먼저 적용한 다음 이를 실제 DOM과 동기화하는 방식으로 동작한다.

💫 DOM 조작에 의한 업데이트에 비해 효율적인 방식이 장점 =>
모든 DOM 수정 작업은 real DOM이 아닌 virtual DOM에서 수행되어 변경된 element들을 실제 DOM에 한번에 적용할 수 있기 때문에 reflow, repaint가 한번만 발생한다.

## 재조정 (Reconciliation)

상태 변경이 발생하면 old Virtual DOM과 updated Virtual DOM을 diffing 알고리즘을 통해 비교한 후 업데이트가 필요한 요소만 새로 그리는 과정을 말한다.
React는 ReactDOM과 같은 라이브러리를 통해 재조정 과정을 거친다.

1. React는 상태 변경이 발생하면 메인 스레드가 idle 상태가 될 때까지 대기한 다음 fiber를 사용하여 WIP 트리를 생성하기 시작한다.
2. **WIP 트리를 구축**하면서 **WIP 트리와 current 트리와 비교하여 변경 사항이 있는지 확인**한다. (`Render Phase`)

- 변경 사항(effect)이 있는 fiber를 표시한다.
- 변경이 필요하지 않은 fiber는 current tree에서 복제한다. (fiber는 다른 트리의 fiber를 가리키는 대체 속성이 있다.)
- asynchronous하게 수행된다.
- 메인 스레드가 완료해야 할 다른 작업이 있는 경우 일시 중지될 수 있고 메인 스레드가 다시 idle 상태가 되면 중단했던 부분부터 WIP 트리 구축을 재개한다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/62097867/212477622-c6cb5e26-23af-4ad9-853a-f38b732ef618.png" width="600px" />
</p>

3. WIP 트리가 완성되고 **변경된 부분을 실제 DOM에 반영**한다. (`Commit Phase`)

- current tree와 WIP tree의 포인터를 스왑하고 변경 사항이 있는 fiber를 DOM으로 flush하여 DOM을 변경하는 것.
- synchronous하게 수행되고 중단될 수 없다.

<p align="center">
  <img src="https://user-images.githubusercontent.com/62097867/212477627-764b8917-9a37-40df-af63-0a33b8d917a7.png" width="600px" />
</p>

4. 새로운 WIP 트리(이전 current)는 새로운 상태 변경이 발생할 때 재사용된다.

## Diffing Algorithm

Render Phase에서 diffing algorithm이 사용되고 이것은 React를 더 효율적으로 만들어준다.

하나의 트리를 가지고 다른 트리로 변환하기 위한 최소한의 연산 수를 구하는 알고리즘 해결책도 n개의 요소가 있는 트리에 대해 `O(n^3) (n: 트리 요소수)`의 시간 복잡도를 가진다.
React에 적용하기에는 너무 비싼 연산이다. (ex. 1000개의 요소를 그리기 위해 10억 번의 비교 연산을 수행해야 함)
따라서 React는 두 가지 가정을 기반하여 `O(n)` 복잡도의 휴리스틱 알고리즘을 구현한다.

**1. 두 element의 타입이 다르면 다른 트리를 생성한다.**

트리를 비교하려 하지 않고 단순히 old tree를 교체한다.

다음과 같은 상황에서 이전 Counter는 사라지고 새로 다시 마운트가 될 것이다.

```jsx
<div>
  <Counter />
</div>

<span>
  <Counter />
</span>
```

**💡 DOM 요소의 타입이 같다면**

변경된 속성들만 갱신하고, 처리가 끝나면 이어서 해당 노드의 자식들을 재귀적으로 처리한다.

```jsx
<div className="before" title="stuff" />

<div className="after" title="stuff" />

// className만 수정
```

```jsx
<div style={{color: 'red', fontWeight: 'bold'}} />

<div style={{color: 'green', fontWeight: 'bold'}} />

// style이 갱신될 때는 color 속성만 수정
```

**💡 같은 타입의 컴포넌트 요소**

같은 타입의 컴포넌트가 갱신되면 인스턴스는 동일하게 유지되어 렌더링간 state가 유지된다.

새로운 요소의 내용을 반영하기 위해 현재 컴포넌트 인스턴스의 props를 갱신한다.

다음으로 render() 메서드가 호출되고 비교 알고리즘이 이전 결과와 새로운 결과를 재귀적으로 처리한다.

**2. 개발자는 key prop을 사용하여 변경이 필요하지 않은 자식 element를 표시할 수 있다.**
