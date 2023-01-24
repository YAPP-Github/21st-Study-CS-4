## React 컴포넌트의 lifecycle

1. mount
2. update
3. unmount

## 클래스 컴포넌트의 lifecycle

클래스 컴포넌트는 여러 종류의 생명주기 메서드를 가지며 이 메서드를 오버라이딩하여 특정 시점에 코드가 실행되도록 설정할 수 있다.

![image](https://user-images.githubusercontent.com/62097867/214280011-8bb896dd-58f2-4f78-9324-d69d0320a549.png)

**1. Mount**

`constructor`
- 컴포넌트가 렌더링되기 전에 호출되는 생성자 메서드
- `super(props)`를 통해 생성자 내에서 전달받은 props를 사용할 수 있다.
- `this.state`에 객체를 할당하여 local state를 초기화한다.
- 인스턴스에 메서드를 바인딩한다.

`render`
- React 엘리먼트를 반환하는 메서드
- 컴포넌트의 state를 변경하지 않고, 호출될 때마다 동일한 결과를 반환하며 브라우저와 직접적으로 상호작용하지 않는 순수 함수여야 한다.

`componentDidMount`
- 컴포넌트가 DOM에 렌더링된 후 호출되는 메서드
- DOM 노드가 있어야 하는 초기화 작업
- 네트워크 요청 작업

**2. Update**

`componentDidUpdate`
- 상태 갱신이 일어난 후 호출되는 메서드
- 최초 렌더링에서는 호출되지 않는다.
- 컴포넌트가 갱신되었을 때 수행할 작업

**3. Unmount**

`componentWillUnmount`
- 컴포넌트가 마운트 해제되어 DOM에서 제거되기 직전에 호출되는 메서드
- 타이머 제거
- 네트워크 요청 취소
- componentDidMount() 내에서 생성된 구독 해제 작업


## hooks lifecycle

![image](https://user-images.githubusercontent.com/62097867/214349689-289f3e0a-597e-4767-8e5b-3472919857bf.png)

useEffect hook을 통해 lifecycle 메서드를 대체할 수 있다.

**1. Mount**

렌더링될 때마다 실행된다.
```jsx
// componentDidMount + componentDidUpdate
useEffect(() => {
 console.log(“This is mounted or updated.”);
});
```

초기 렌더링시에만 실행된다.
```jsx
// componentDidMount
 useEffect(() => {
   console.log(“This is mounted only not updated.”);
 }, []);
```

**2. Update**

초기 렌더링과 deps에 주어진 상태가 변경될 때마다 실행된다.
```jsx
// componentDidMount + componentDidUpdate(deps에 주어진 상태가 변경될 때만)
 useEffect(() => {
   console.log(“This is mounted or count state updated.”);
 }, [count]);
```

**3. Unmount**

컴포넌트가 unmount될 때 cleanup function이 실행된다.
```jsx
// Equivalent of componentWillUnmount
 useEffect(() => {
   return () => {
     console.log(“This is unmounted.”);
   };
 }, []);
 ```
 
✅deps가 있다면 effect가 실행되기 전에 매번 cleanup이 먼저 실행된다. 

