# React lifecycle

- 각 컴포넌트에 라이프 사이클(수명주기)가 존재한다.
  - 클래스형 컴포넌트는 lifecycle method를 사용하고, 함수 컴포넌트는 hook을 사용
- 컴포넌트의 수명은 렌더링 전 준비 단계에서 시작하여 페이지에서 사라질 때 끝난다.

## 컴포넌트가 업데이트 되는 경우

- props가 변할 때
- state가 변할 때
- 부모 컴포넌트가 리렌더링 될 때

# 클래스형 컴포넌트

![Untitled](https://user-images.githubusercontent.com/55427367/214848766-533a8c3e-f551-42ae-b1d3-aa0e6044004d.png)

## Mount

1. state, context, defaultProps 저장
2. componentWillMount
   - mount중이기 때문에 props, state 변화 x
   - 렌더되지 않았기 때문에 DOM에 접근도 x
3. render
4. componentDidMount
   - mount가 완료되고 실행
   - 주로 setTimeout, AJAX 요청 등이 일어남

## Update

### Props Update

1. componentWillReceiveProps
   - 업데이트가 감지되면 실행
2. shouldComponentUpdate
   - 쓸데없는 업데이트가 일어나는지 확인
   - 주로 성능 최적화를 이 단계에서 함
3. componentWillUpdate
   - state를 바꾸게 되면 또 2번이 실행되기 때문에 state를 바꿀 수 없음
4. render
5. componentDidUpdate
   - 이 단계부터 DOM에 접근 가능

### State Update

- props 업데이트와 같지만 componentWillReceiveProps가 실행되지 않음

## Unmount

1. componentWillUnmount
   - 연결된 이벤트 리스너 정리 등이 일어남

# 함수 컴포넌트

![Untitled](https://user-images.githubusercontent.com/55427367/214848808-b6dfbde4-73d6-4d5f-8182-92f21178311f.png)

## Mount

1. 컴포넌트가 호출되었을 때 가장 먼저 컴포넌트 내부가 호출된다. (클래스형에서 constructor과 같은)
2. return()
   - 컴포넌트가 호출 됐을 때 엘리먼트 요소들을 화면에 그린다.
   - `setState()` 를 사용할 수 없다.

## Mount/Update/Unmount (useEffect())

- componentDidMount, componentDidUpdate, componentWillUnmount를 합쳐놓은 hook
- 컴포넌트 내에서 렌더링이 수행된 이후 실행된다.
  - `deps` 값이 없는 경우: 렌더링 될 때마다 수행
  - `deps` 가 빈배열인 경우: 렌더링 된 최초 한 번 수행
  - `deps` 에 값이 존재하는 경우: 렌더링 된 이후 수행되고 값이 변경될 때 마다 수행
- clean-up
  - useEffect 내부에 return문을 작성하면 DOM에서 제거될 때 메소드가 호출된다.
  - 메모리 누수를 방지할 수 있음

```jsx
const Component=()=>{
	const [number, setNumber] = useState(0);
	console.log("1. 컴포넌트 호출이 되었을 때 가장 먼저 호출되는 부분");

	useEffect(()=> {
		console.log("3. 화면이 렌더링 된 직후 일어나는 작업");

		return()=>{
			console.log("4. 컴포넌트 clean up");
		}
	}, []);

	return(
		<div>2. 컴포넌트가 호출되었을 때 요소를 화면에 그림</div>
	)
```
