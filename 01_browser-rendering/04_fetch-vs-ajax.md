# 📡 Fetch vs Ajax

## 개념 정리

### Ajax란?
- AJAX(Asynchronous JavaScript and XML)는 웹페이지를 새로 고침하지 않고 서버와 비동기적으로 통신할 수 있게 해주는 기술
- 초기에는 XML을 주고받았지만, 현재는 대부분 JSON을 사용
- Ajax를 구현하는 대표적인 방법은 XMLHttpRequest 객체(XHR)를 사용

⸻

### Fetch API란?
- Fetch API는 Promise 기반으로 비동기 HTTP 요청을 처리하는 최신 표준 API
- 코드를 더 간결하고 직관적으로 작성할 수 있고, **async/await**과도 잘 어울림
- XMLHttpRequest의 복잡함을 대체하는 방식으로 등장했으며, 대부분의 최신 브라우저에서 기본 제공됨

⸻

### 예제 코드 비교

XMLHttpRequest (XHR)
```
const xhr = new XMLHttpRequest();
xhr.open("GET", "https://api.example.com/data");
xhr.onload = function () {
  if (xhr.status === 200) {
    console.log(xhr.responseText);
  }
};
xhr.onerror = function () {
  console.error("Request failed");
};
xhr.send();
```


Fetch API
```
fetch("https://api.example.com/data")
  .then((res) => {
    if (!res.ok) throw new Error("Network response was not ok");
    return res.json();
  })
  .then((data) => console.log(data))
  .catch((err) => console.error("Fetch error:", err));
```


Fetch + async/await
```
async function fetchData() {
  try {
    const res = await fetch("https://api.example.com/data");
    if (!res.ok) throw new Error("Network response was not ok");
    const data = await res.json();
    console.log(data);
  } catch (err) {
    console.error("Fetch error:", err);
  }
}
```

⸻

### XMLHttpRequest와 Fetch API의 차이점

1. API 스타일
- XHR은 콜백 기반으로 동작하여 코드 흐름이 복잡해지기 쉬움
- Fetch는 Promise 기반으로 더 직관적인 비동기 처리를 지원<br>

2. 코드 가독성
- XHR은 함수 중첩과 상태 확인 등으로 코드가 지저분해지기 쉬움
- Fetch는 체이닝 또는 async/await로 작성이 간결하고 가독성이 높음<br>

3. 에러 처리 방식
- XHR은 onerror 이벤트와 status 값을 수동으로 확인해야함
- Fetch는 .catch() 또는 try/catch 블록으로 에러를 간단하게 처리할 수 있음<br>

4. JSON 처리 방식
- XHR로 응답을 받을 경우, 문자열을 JSON.parse()로 수동 변환해야함
- Fetch는 .json() 메서드를 제공하여 간편하게 JSON 데이터를 추출할 수 있음<br>

5. 브라우저 호환성
- XHR은 대부분의 브라우저, 심지어 구형 브라우저(IE 포함)도 지원함
- Fetch는 최신 브라우저에서 기본 지원하지만, Internet Explorer는 지원하지 않기 때문에 필요 시 폴리필을 사용해야함<br>

6. 요청 취소 방법
- XHR은 .abort() 메서드를 사용해 요청을 취소할 수 있음
- Fetch는 AbortController를 활용해 요청을 중단할 수 있음<br>


⸻

### 요약
- Ajax는 비동기 통신을 위한 기술 개념이며 XHR이 초기 구현 방식
- Fetch API는 최신 자바스크립트 표준으로 가독성이 높고 에러 처리가 직관적
- 실무에서는 Fetch + async/await가 가장 일반적으로 사용됨
- 구형 브라우저(특히 IE)를 지원해야 한다면 여전히 XHR 또는 폴리필이 필요할 수 있음
