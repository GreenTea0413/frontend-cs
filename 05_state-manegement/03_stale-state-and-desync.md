# 🔄 상태 동기화 이슈 (Stale State & 비동기 처리)

## 개념

React에서 상태(state)는 UI의 현재 상태를 나타내는 중요한 값<br>
그런데 컴포넌트가 렌더링되는 과정에서, 상태가 최신 값이 아닐 수 있는 현상이 발생

이걸 흔히 stale state (오래된 상태)라고 함<br>
특히 비동기 처리나 useEffect 안에서 상태를 사용하는 경우 이런 문제가 자주 발생함

⸻

## ❗ stale state란?

이전 렌더링 시점의 상태를 참조_하는 현상
즉, 최신 상태를 받아오지 못하고, 이미 렌더된 상태 기준으로 동작하는 문제

### 예시
```
const [count, setCount] = useState(0);

const handleClick = () => {
  setTimeout(() => {
    count++;
    setCount(count);
  }, 1000);
};

return <button onClick={handleClick}>Click</button>;
```

코드가 작동은 하지만 여러번 클릭하면 이상한 동작 발생
- 3번 연속 빠르게 클릭한 경우 타이머는 각 count =0 을 기억하고 있어서 1만 3번 나올 수 있음
- 이게 바로 stale state + 비동기 타이밍 문제

⸻

### 발생하는 대표적인 상황들

| 상황 | 설명 |
| --- | --- |
| `setTimeout`, `setInterval` 안에서 state 사용 | 비동기 클로저가 이전 상태를 참조함 |
| `useEffect` 클로저에서 state 참조 | 의존성 배열이 누락되면 이전 값을 참조함 |
| 이벤트 핸들러 안에서 오래된 상태 참조 | 상태가 바뀌어도 이전 값만 남아있을 수 있음 |


⸻

### 해결 방법 (비동기 처리)

1. 최신 상태 보장: 업데이트 함수 방식 사용
```
setState(prev => prev + 1) 형식으로 작성하면, React가 최신 상태를 보장함.

const increment = () => {
  setCount(prev => prev + 1);
};
```
2. useRef로 최신 값 저장
```
const countRef = useRef(count);

useEffect(() => {
  countRef.current = count;
}, [count]);

const delayedAlert = () => {
  setTimeout(() => {
    alert(countRef.current); // 항상 최신 값 출력
  }, 1000);
};
```
3. 의존성 배열 정확하게 설정 (useEffect)
```
useEffect(() => {
  // count에 의존하므로 반드시 추가
  console.log(count);
}, [count]);
```

4. Promise, async/await
```
// ❗ stale state 예시 - Promise
const handleClick = () => {
  Promise.resolve().then(() => {
    console.log(count); // 오래된 count를 출력할 수 있음
  });
};

// ❗ stale state 예시 - async/await
const fetchData = async () => {
  const res = await fetch('/api/data');
  console.log(count); // 최신 값이 아닐 수 있음
};
```
⸻

🧠 정리

| 항목 | 설명 |
| --- | --- |
| stale state | 오래된 렌더링 시점의 상태가 비동기 함수에서 사용됨 |
| 원인 | 클로저에 의해 이전 상태가 캡처됨 |
| 대표 사례 | setTimeout, useEffect, 이벤트 핸들러 |
| 해결법 | setState 함수형 업데이트, useRef로 최신 값 저장, 의존성 배열 주의 |
