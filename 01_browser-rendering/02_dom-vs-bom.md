# DOM vs BOM

## 📚 DOM이란?

**DOM(Document Object Model)**
- 웹 페이지의 HTML 문서를 객체(트리) 형태로 표현한 구조
- 브라우저는 이 DOM을 통해 페이지 내용을 읽고, 변경하고, 추가하고, 삭제할 수 있음
- HTML은 원래 단순한 “문자열”이지만, 브라우저는 이걸 JS가 다룰 수 있도록 객체 트리 구조로 변환해줌
<br><br/>
- 트리 구조 : 요소들이 부모-자식 관계로 구성됨
- 실시간 조작 가능 : JS로 DOM을 수정하면 즉시 화면에 반영됨
- 이벤트 바인딩 : 특정 요소에 클릭 등 이벤트를 연결할 수 있음

⸻

### DOM 예시 코드

```
// 문서에서 <h1> 요소를 찾고 텍스트 변경
document.querySelector('h1').textContent = '👋 안녕, DOM!';

// 배경색 변경
document.body.style.backgroundColor = 'lightyellow';

// 새로운 요소 추가
const newP = document.createElement('p');
newP.textContent = '나는 새로 추가된 문단이야!';
document.body.appendChild(newP);
```

⸻

### DOM을 구성하는 주요 객체들

**1. document**

DOM의 진입점이자 **최상위 객체**<br>
HTML 문서 전체를 대표하며, document.getElementById()나 querySelector() 등을 통해 요소에 접근할 수 있음

```
console.log(document.title); // 현재 문서의 제목
```

⸻

**2. element**

HTML 태그 하나하나를 **의미하는 객체**
```<div>, <p>, <ul> 같은 태그는 모두 Element로 취급되며, 속성 조작, 스타일 변경, 이벤트 바인딩 등에 활용```

```
const btn = document.querySelector('button');
btn.style.backgroundColor = 'blue';
```

⸻

**3. node**

DOM 내 모든 구성 요소의 최상위 개념<br>
Element, Text, Comment 등은 모두 Node의 하위 타입<br>
모든 element는 node이지만 모든 node가 element인 것은 아닙니다 (예: 텍스트, 주석도 node임)

⸻

**4. text**

HTML 요소 내부의 **텍스트 자체를 의미하는 노드**
```예를 들어 <p>Hello</p> 라면, p는 element이고 Hello는 그 안의 text node라고 함```

```
const p = document.querySelector('p');
console.log(p.firstChild.nodeValue); // "Hello"
```

⸻

**5. attribute**

HTML 요소의 **속성을 나타내는 객체**<br>
class, id, src, href 등의 속성값을 조작할 수 있음

```
const img = document.querySelector('img');
console.log(img.getAttribute('src'));
img.setAttribute('alt', '대체 텍스트');
```

⸻
## 📚 BOM이란?

**BOM (Browser Object Model)**
- 웹 브라우저 자체를 JS로 제어할 수 있게 해주는 **객체 구조(model)**
- BOM은 **window**를 시작점으로 함
- window는 브라우저의 “전체 창”을 의미하고, 그 안에 navigator, location, history 같은 브라우저 기능 객체들이 들어있음


⸻

### BOM 예시 코드
```
// 창의 크기
console.log(window.innerWidth); // 1280
console.log(window.innerHeight); // 720

// 주소창 정보
console.log(location.href); // 현재 페이지 URL
location.href = 'https://google.com'; // 페이지 이동

// 브라우저 정보
console.log(navigator.userAgent);

// 방문 기록 제어
history.back(); // 뒤로 가기
history.forward(); // 앞으로 가기
```

⸻

### BOM 주요 구성 요소

**1. window**

브라우저 창 전체를 나타내는 **최상위 객체로, 모든 BOM 요소의 루트**<br>
JS 실행 환경에서는 전역 객체(Global Object)로도 작동하며, 우리가 자주 사용하는 alert, setTimeout, console 등의 함수도 사실 window 객체의 메서드

⸻

**2. navigator**

사용자의 **브라우저 종류, 버전, 운영체제 등의 정보를 담고 있는 객체**<br>
브라우저 환경에 따라 조건을 나누거나 사용자 통계를 수집할 때 사용됨
```
console.log(navigator.userAgent); // ex: Mozilla/5.0 ...
```

⸻

**3. location**

현재 페이지의 **URL 정보와 관련된 기능을 제공해줌**<br>
페이지를 이동하거나 새로고침, URL 변경 등을 할 수 있음
```
console.log(location.href); // 현재 URL
location.reload();          // 새로고침
```

⸻

**4. history**

브라우저의 **방문 기록을 제어할 수 있는 객체**<br>
뒤로가기, 앞으로가기, 특정 위치로 이동하는 기능을 제공
```
history.back();    // 뒤로 가기
history.forward(); // 앞으로 가기
```

⸻

**5. screen**

**사용자의 화면** 해상도, 색상 깊이, 너비/높이 등의 **정보**를 담고 있음<br>
반응형 디자인이나 디바이스 환경을 체크할 때 사용됨
```
console.log(screen.width, screen.height); // ex: 1920 1080
```

⸻

**6. alert, confirm, setTimeout, setInterval**

이러한 함수들은 전역에서 바로 호출할 수 있지만, 실제로는 **모두 window 객체의 속성**<br>
즉, window.alert(), window.setTimeout()으로도 동일하게 작동함
```
setTimeout(() => {
  alert('Hello BOM!');
}, 1000);
```

⸻

### 💡 한 문장 요약

- DOM은 “문서를 다루는 모델”
- BOM은 “브라우저를 다루는 모델”

⸻
