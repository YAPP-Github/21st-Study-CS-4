## Fiber Architecture

### 도입 배경

기존 react는 한 번의 tick동안 재귀적으로 트리를 탐색하고 업데이트된 트리 전체의 render 함수를 호출하는 방식을 사용했다.

하지만 이때 너무 많은 작업이 동시에 수행되면 frame drop이 발생하고 뚝뚝 끊기는 느낌을 주어 사용자 경험을 저하시킬 수 있다.

이러한 문제를 해결하기 위해 react 16부터 **Fiber**라는 새로운 재조정(reconciliation) 엔진이 등장하였다.
Fiber는 몇몇 업데이트를 지연시키거나 우선순위에 따라 작업을 **스케줄링**할 수 있도록 한다.

모던 브라우저들도 동일한 문제를 해결하는 것을 돕기위한 API를 가지고 있다.

> - `requestIdleCallback`: 우선 순위가 낮은 함수들이 유휴 기간동안 호출될 수 있도록 스케줄링
> - `requestAnimationFrame`: 우선 순위가 높은 함수들이 다음 animation frame에 호출되도록 스케줄링

하지만 이런 API들을 사용하기 위해서는 렌더링 작업을 incremental unit으로 나눌 방법이 필요하다는 문제가 있다.
오직 콜스택에 의존한다면 스택이 비워질 때까지 작업을 계속할 것이다.

따라서 react에서는 Fiber를 도입하여 react 컴포넌트에 특화된 **스택을 재구현**하였고 이는 **메모리에 스택 프레임을 저장해두고 원할 때 실행**할 수 있도록 한다.
단일 fiber는 virtual stack frame이라 볼 수 있다.

이로 인해 스케줄링 작업뿐만 아니라 동시성, error boundary와 같은 기능들을이 가능하게 되었다.

### 🔑스케줄링 키 포인트

- 모든 업데이트가 즉각적으로 UI에 반영될 필요는 없다.

- 다른 종류의 업데이트는 다른 우선순위를 갖는다.
  - 유저 상호작용에서 발생하는 작업(ex. 버튼 클릭시 애니메이션)에 덜 중요한 백그라운드 작업(ex. 네트워크로부터 방금 막 도착하여 새롭게 렌더링되는 컨텐츠)보다 높은 우선순위를 줄 수 있다.

- pull 기반 접근 방식을 사용한다. react가 작업들의 우선순위를 전제하고 스케줄링을 대신한다는 의미

### 주요 목표

- 렌더링 작업을 청크로 나누고 여러 프레임에 분산한다. (virtual DOM의 incremental rendering)

- 새 업데이트가 들어올 때 기존의 작업을 pause, abort, reuse할 수 있다.

- 업데이트 종류에 따라 우선순위를 부여한다.

## fiber 노드 구조

fiber는 컴포넌트 인스턴스에 대응되며 컴포넌트의 입력과 출력에 대한 정보를 갖고 있다. 이는 작업 단위가 된다.

```jsx
export type Fiber = {|
  tag: WorkTag,

  // Unique identifier of this child.
  key: null | string,
  
  // The resolved function/class/ associated with this fiber.
  type: any,

  // The Fiber to return to after finishing processing this one.
  // This is effectively the parent, but there can be multiple parents (two)
  // so this is only the parent of the thing we're currently processing.
  // It is conceptually the same as the return address of a stack frame.
  return: Fiber | null,

  // Singly Linked List Tree Structure.
  child: Fiber | null,
  sibling: Fiber | null,
  index: number,

  // Input is the data coming into process this fiber. Arguments. Props.
  pendingProps: any, // This type will be more specific once we overload the tag.
  memoizedProps: any, // The props used to create the output.

  // ....
|};

```
| property | 설명 |
| --- | --- |
| `type`과 `key` | - type은 실행이 스택 프레임에 의해 추적되는 함수를 가리킨다. <br /> - key는 type과 함께 fiber가 재사용될 수 있는지를 결정하기 위해 재조정중에 사용된다. |
| `child`와 `sibling` | - 재귀적인 트리 구조를 갖는 다른 fiber를 가리킨다. |
| `return` | - return fiber는 현재 항목을 처리한 후 반환해야하는 fiber다. <br /> - stack frame의 return address와 동일하며 parent fiber로 생각할 수 있다. |
| `pendingProps`와 `memoizedProps` | - pendingProps는 실행 시작시 설정되고 memoizedProps는 마지막에 설정된다. <br /> - 새로 들어오는 pendingProps가 memoizedProps와 같다면 fiber의 이전 결과는 재사용될 수 있으며 불필요한 작업을 방지할 수 있다. |
| `pendingWorkPriority` | - fiber를 대표하는 작업의 우선순위를 나타내는 숫자 |
| `alternate` | - 컴포넌트 인스턴스는 최대 두가지 fiber를 갖는다. (현재 flushed fiber, work-in-progress fiber)<br /> - 서로가 서로의 alternate이다. <br /> - fiber의 alternate는 `cloneFiber`라는 함수를 통해 지연 생성된다. `cloneFiber`는 새로운 객체를 생성하는 대신에 기존의 fiber의 alternate를 재사용하여 allocation을 최소화한다. |
| `output` | - fiber의 출력값은 함수의 반환 값이다. <br /> - 출력값은 리프 노드에 있는 host component에 의해서만 생성된다. 이는 트리 위로 전달되며 결국 모든 fiber가 출력값을 갖게된다. <br /> - 출력값은 렌더링 환경에 변경 사항들을 flush할 수 있도록 renderer에게 전달된다. 출력값이 어떻게 생성되고 업데이트될지 정의하는 것은 renderer의 역할이다. |

> **flush**: fiber를 flush하는 것은 스크린에 결과를 렌더링하는 것을 말한다.
> 
> **work-in-progress**: 완료되지 않은 fiber, 즉 아직 return되지 않은 stack frame을 말한다.

> **host component**란 react 어플리케이션에서 리프 노드를 말하며 브라우저 환경을 예로 들면 div, span 등등이 있다. (렌더링 환경마다 다름)
> 이들은 JSX에서 lowercase tag name으로 표시되는 것을 의미한다.

https://github.com/acdlite/react-fiber-architecture


