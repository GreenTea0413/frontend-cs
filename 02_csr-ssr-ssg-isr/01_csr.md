# ⚙️ CSR (Client-Side Rendering)

## CSR이란?

HTML, CSS, JavaScript를 모두 클라이언트(브라우저)에서 처리해 화면을 구성하는 방식
- 최초 요청 시 서버는 빈 HTML 껍데기와 JavaScript 파일만 전달
- 이후 JavaScript가 동작하면서 데이터를 불러오고, 화면을 브라우저에서 동적으로 구성
- React, Vue, Angular 등의 SPA(Single Page Application) 프레임워크가 사용하는 기본 렌더링 방식

⸻

### 작동 흐름
```
브라우저 → HTML 요청 → 서버는 기본 HTML + JS 응답
↓
브라우저는 JS 실행 → API로 데이터 요청 → 받은 데이터를 화면에 렌더링
```

예를 들어 React에서 다음과 같은 구조로 렌더링이 이루어짐:

```
// index.html
<body>
  <div id="root"></div>
  <script src="/main.js"></script>
</body>

// main.js (React 코드)
ReactDOM.render(<App />, document.getElementById("root"));
```

⸻

### 예제 코드
```
// App.jsx
import { useEffect, useState } from "react";

function App() {
  const [data, setData] = useState(null);

  useEffect(() => {
    fetch("/api/data")
      .then(res => res.json())
      .then(setData);
  }, []);

  return <div>{data ? data.message : "로딩 중..."}</div>;
}
```
- 서버는 /api/data만 제공하고, 렌더링은 브라우저가 직접 수행함

⸻

### ✅ 장점
- 빠른 사용자 경험(UX): 초기 로딩 후 페이지 간 이동이 빠름
- SPA 구현에 적합: 상태 유지, 부드러운 전환, 라우팅 처리 가능
- 클라이언트에서 동적으로 UI 조작 가능

⸻

### ❗단점
- 초기 로딩 속도 느림: 브라우저가 JS를 다운로드·해석·실행해야만 화면을 볼 수 있음
- SEO 불리: 초기 HTML에 콘텐츠가 없음 → 검색엔진이 페이지 내용을 인식하지 못할 수 있음
- 자바스크립트 의존도 높음: JS 비활성 환경에서는 페이지가 작동하지 않음

⸻

🧠 실제 사용 사례
- 대시보드, 웹앱 등 사용자 로그인 후 콘텐츠가 주로 변화하는 서비스
- 예: Gmail, Notion, Trello 등
