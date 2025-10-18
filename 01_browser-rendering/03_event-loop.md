# 🔁 이벤트 루프 (Event Loop)

## 먼저 알아야 할 개념
- 자바스크립트는 싱글 스레드 언어
- 즉, 한 번에 한 가지 작업만 수행할 수 있음
- 그런데도 동시에 여러 작업이 진행되는 것처럼 보이는 이유?
→ 이벤트 루프(Event Loop) 덕분!

⸻

### 자바스크립트 런타임 구성 요소

1. 메모리 힙 (Memory Heap)
- 객체(참조 타입)가 저장되는 공간
- 예: 배열, 객체, 함수 등은 이곳에 저장됨
- 구조적으로는 비정형적이고 동적으로 할당되는 큰 공간
- 자바스크립트 엔진이 필요에 따라 동적으로 할당하고 관리함
```
const obj = { name: '보성' }; // obj는 Heap에 저장됨
```
⸻

2. 콜 스택 (Call Stack)
- 자바스크립트 코드가 실행될 때, 함수 호출이 쌓이고 처리되는 공간
- 함수 실행 → 스택에 push → 실행 완료 → pop
- LIFO (Last-In, First-Out) 구조
- 스택이 가득 차면 “Stack Overflow” 발생
```
function a() {
  b();
}
function b() {
  c();
}
function c() {
  console.log('Hello');
}

a(); // 실행 순서: a → b → c
```

⸻

### 이벤트 루프 작동 원리

3. Web APIs
- 브라우저가 제공하는 비동기 API들 (자바스크립트 엔진 외부에서 실행)
- 예: setTimeout, fetch, addEventListener, DOM API, XHR, WebSocket, 등등
- 비동기 작업이 완료되면 콜백을 큐로 보냄

⸻

4. 콜백 큐 (Callback Queue 또는 Task Queue)
- Web API에서 완료된 비동기 작업의 콜백 함수가 들어가는 곳
- 대기열 형태이며, FIFO (First-In, First-Out) 구조
- setTimeout, setInterval, UI 이벤트 등 일반적인 비동기 작업의 콜백이 여기에 들어감

  - Promise의 .then()이나 async/await는 이 큐에 들어가지 않음
    -> 그건 Microtask Queue라는 별도 큐에 들어감

⸻

5. 이벤트 루프 (Event Loop)
- Call Stack이 비었는지 계속 감시
- 비어 있으면, Callback Queue에 쌓인 콜백을 하나씩 꺼내 Call Stack에 넣어 실행
- 이를 통해 비동기 작업도 마치 동시에 처리되는 것처럼 보이게 함

⸻

🎬 작동 순서 예시
예시 코드
```
console.log("1");

setTimeout(() => {
  console.log("2");
}, 0);

console.log("3");
```

결과
```
1
3
2
```

설명
1.	console.log("1") → 바로 실행 → Stack에서 빠짐<br>
2.	setTimeout → Web API로 보내짐 → 0ms 후 Callback Queue에 콜백 저장<br>
3.	console.log("3") → 바로 실행<br>
4.	Call Stack이 비는 순간 → 이벤트 루프가 Callback Queue에서 () => console.log("2") 꺼내 Stack으로 보냄<br>
5.	"2"가 실행됨<br>

⸻

### 보충 개념

마이크로태스크 큐(Microtask Queue)
- Promise.then(), async/await, queueMicrotask() 등은 Microtask Queue에 들어감
- Callback Queue보다 우선순위가 높음
- 하나의 Task 실행 → 그 안에서 생성된 모든 Microtask 먼저 처리 → 다음 Task로 이동
