# ⚙️ 렌더링 조건 최적화


## React의 렌더링 방식

기본적으로 React는 state나 props가 바뀌면 해당 컴포넌트를 리렌더링함
하지만 이 리렌더링이 항상 사용자에게 필요한 건 아니기 때문에, 불필요한 렌더링을 방지하는 것이 성능 최적화의 핵심!

⸻

### 리렌더링 발생 조건
- props, state, context, key가 변경되면 렌더링됨
- 부모가 렌더링되면 자식도 기본적으로 렌더링됨

⸻

### 최적화 기법 요약

| 기법                    | 설명                                                            |
|-------------------------|-----------------------------------------------------------------|
| `React.memo()`          | props가 바뀌지 않으면 리렌더링 방지 (함수형 컴포넌트용)         |
| `shouldComponentUpdate` | 클래스형 컴포넌트의 렌더링 조건 제어                            |
| `useMemo` / `useCallback` | 연산 결과 / 함수 재생성을 방지 (메모이제이션)                  |
| `key` 최적화            | 리스트 렌더링에서 diffing 효율 증가                              |


⸻

## ✅ React.memo
**메모이제이션** 기법으로 동작하는 고차 컴포넌트<br>
컴포넌트에 전달된 props가 이전과 같다면, React는 Virtual DOM 비교 없이 컴포넌트 자체를 건너뜀<br>
React는 컴포넌트를 재렌더링 하지않고 마지막으로 렌더링 결과를 재사용


### 예시
```
const Child = React.memo(({ count }) => {
  console.log('👶 Child rendered');
  return <div>Count: {count}</div>;
});

function Parent() {
  const [value, setValue] = useState(0);
  return (
    <>
      <button onClick={() => setValue((v) => v + 1)}>Increment</button>
      <Child count={1} />
    </>
  );
}
```
- Parent는 버튼 클릭할 때마다 리렌더링됨
- 하지만 Child의 count는 바뀌지 않으므로 렌더링되지 않음

### 작동 원리
```
if (arePropsEqual(prevProps, nextProps)) {
  // skip render
} else {
  // render
}
```

기본 비교 방식은 shallow comparison (얕은 비교)
- number, string, boolean 등은 값으로 비교
- object, array, function 등은 참조값으로 비교


### 주의할 점

1. 참조형 props는 주의
```
<Child data={{ name: '보성' }} /> // ❌ 항상 새 객체 → 리렌더링됨
```

- 매 렌더링마다 data는 새로운 객체 → memo가 의미 없음
- 해결법: 상위에서 useMemo로 메모이제이션

2. 함수 props도 마찬가지
  
```
<Child onClick={() => {}} /> // ❌ 매번 새 함수
```
- 해결법: useCallback 사용

⸻

## ✅ useMemo: 값을 메모이제이션

useMemo(fn, deps)은 의존성(deps)이 바뀌지 않으면 fn의 결과값을 다시 계산하지 않고 재사용함
- 무거운 계산이 반복되지 않도록 최적화
- 계산된 값 자체를 기억함

### 예시
```
const heavyValue = useMemo(() => {
  return expensiveCalculation(input);
}, [input]);
```
- input이 바뀌지 않으면 expensiveCalculation은 실행되지 않음

⸻

## ✅ useCallback: 함수를 메모이제이션

useCallback(fn, deps)은 의존성(deps)이 바뀌지 않으면 같은 함수 객체를 반환해서 불필요한 함수 재생성 방지

- 특히 자식 컴포넌트에 함수를 props로 넘길 때 유용
- useMemo(() => fn, deps)와 사실상 동일하지만, 명확하게 함수라는 점에서 구분

### 예시
```
const handleClick = useCallback(() => {
  console.log('clicked');
}, []);
```
- handleClick은 렌더링이 반복돼도 같은 함수 객체

⸻

### 왜 필요한가?

참조값 비교는 항상 다름
```
<Child onClick={() => console.log("click")} />
```
- 위 코드에서 () => {}는 렌더링할 때마다 새롭게 생성되는 함수
- 이처럼 매번 새로 생성되는 함수는 이전 props와 다르다고 판단되기 때문에 자식 컴포넌트가 React.memo로 감싸져 있어도 항상 리렌더링이 발생함

### 해결
```
const handleClick = useCallback(() => {
  console.log("click");
}, []);
<Child onClick={handleClick} />
```
- 이제 onClick은 항상 같은 함수 → 불필요한 리렌더 방지

⸻


## ✅ React의 diffing 알고리즘 
- Virtual DOM에서 변경된 부분만 실제 DOM에 반영하기 위해 이전 Virtual DOM과 새로운 Virtual DOM을 비교(diff) 하는 최적화된 방법
- React는 변경된 부분만 찾아내서 실제 DOM에 적용함 (→ 효율적)
- key를 잘못 설정하면 diffing 비용이 커지고, 리렌더링이 더 많이 발생

```
<ul>
  {items.map((item, i) => (
    <li key={i}>{item}</li> // ❌ 인덱스를 key로 사용 → 추천하지 않음
  ))}
</ul>
```
### diffing이 왜 필요한가?

- HTML을 직접 조작하는 건 느리고, 오류가 나기 쉬움
- React는 상태가 바뀌면 전체 UI를 다시 만든다고 가정함
- 하지만 실제로 브라우저에 전체를 다시 그리면 성능이 나빠짐
- 그래서 Virtual DOM을 두고, 차이만 찾아서 실제 DOM에 반영하는 방식 사용


### 작동원리

React의 diffing 알고리즘은 성능을 위해 몇 가지 가정을 전제로 최적화되어 있음

1. 트리 구조를 한 단계씩 비교
- 두 컴포넌트 트리를 위에서 아래로 내려가며 비교
- 같은 레벨에서만 비교함 (형제 간 비교 X)
- 즉, 자식 트리가 완전히 바뀌면 통째로 다시 그림

2. 컴포넌트 타입이 다르면 완전히 교체
```
<div> → <span>  ✅ 완전히 교체
<MyComponent> → <OtherComponent> ✅ 완전히 교체
```
3. key가 동일하면 이전 요소를 재사용
- 리스트 렌더링 시, key가 같으면 이전 요소를 재사용하고 다르면 새로 생성하거나 제거함
```
  <ul>
  {items.map((item) => (
    <li key={item.id}>{item.text}</li> // ✅ id를 key로 사용
  ))}
</ul>
```

만약에 key를 잘못 썼다면?
- 요소가 추가/삭제될 때, React는 기존 위치 기준으로 비교
- 결과적으로 불필요한 리렌더링 또는 상태 꼬임 발생 가능
- 그래서 사용할 떄 index 보다는 고유id 사용 -> 그래야 요소 재사용 가능

```
items.map(item => <li key={item.id}>{item.name}</li>) // 이게 고유 id
```

```
items.map((item, idx) => <li key={idx}>{item.name}</li>) // index를 바꾸면 상태 꼬임 가능성 O
```
⸻

### 상황별 전략

| 상황                              | 전략 / 해결책                            |
|-----------------------------------|-------------------------------------------|
| 같은 props로 자식이 계속 리렌더  | `React.memo()`                            |
| 고비용 연산이 매번 실행됨         | `useMemo`                                 |
| 자식에게 넘기는 함수가 매번 새로 생성됨 | `useCallback`                        |
| 리스트 리렌더링이 많음            | `key` 제대로 지정 (고유한 ID 등 사용)    |


⸻

### 요약
- React는 매우 빠르지만, 불필요한 렌더링이 많아지면 성능 저하가 생김
- 렌더링을 제어하는 도구 (memo, useMemo, useCallback)들을 상황에 맞게 활용해야 함
- 핵심은 “진짜 바뀐 것만 렌더링하자”는 전략
