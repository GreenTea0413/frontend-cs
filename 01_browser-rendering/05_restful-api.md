# 🌐 RESTful API 개념


## REST란?

REST(Representational State Transfer)는
HTTP 프로토콜 기반에서 자원을 URI로 표현하고, 표현(Representation)을 통해 상태를 주고받는 아키텍처 스타일
- 2000년 로이 필딩(Roy Fielding)이 논문에서 제안
- 클라이언트-서버 구조에서 리소스를 일관되고 예측 가능한 방식으로 조작하기 위한 설계 기준

⸻

### REST의 구성 요소

- 자원(Resource) → URI
  - 예: /users, /posts/123
	
- 행위(Verb) → HTTP Method
  - GET : 조회
  - POST : 생성
  - PUT : 전체 수정
  - PATCH : 부분 수정
  - DELETE : 삭제

- 표현(Representation) → JSON, XML 등
  - 클라이언트와 서버는 데이터를 특정 포맷으로 주고받음 (요즘은 거의 JSON 사용)

⸻

### RESTful API란?

REST의 설계 원칙을 정확히 따르는 API를 RESTful API
- URI는 자원을 나타내야 하고, 동사는 HTTP Method로 표현해야 함
- 예: /users/1/delete ❌ (동사가 URI에 있음) /users/1 + DELETE 메서드 ✅

⸻

### RESTful API 예시

- 유저 목록 조회	/users	GET
- 특정 유저 조회	/users/1	GET
- 유저 생성	/users	POST
- 유저 정보 수정	/users/1	PUT or PATCH
- 유저 삭제	/users/1	DELETE


⸻

### RESTful 설계 원칙 요약
- 자원 중심: URI는 명사형으로 자원을 나타냄 (ex. /products, /orders/1)
- HTTP 메서드의 의미 지키기: GET = 조회, POST = 생성 등
- 상태 비저장(Stateless): 요청 간에 서버는 클라이언트의 상태를 저장하지 않음
- 계층 구조 지원: URI는 /users/1/posts처럼 계층적으로 설계 가능

⸻

### REST vs RESTful의 차이?

REST
- 애초에 하나의 아키텍처 스타일, 즉 설계 철학이나 원칙, 실제 API 구현이 아니라 이론적인 설계 방식에 가까움

RESTful은
- REST 원칙을 실제로 구현에 잘 반영한 API를 의미  
- 즉, REST의 규칙(자원 중심 URI, HTTP 메서드의 의미 준수, 상태 비저장 등)을 실무에서 지키는 API를 RESTful

예를 들어
- REST는 개념적으로 “자원을 URI로 표현하고, HTTP 메서드로 행위를 구분해야 한다”고 정의
- RESTful API는 실제로 /users/1이라는 URI에 DELETE 메서드를 보내 유저를 삭제하는 API처럼 구체적인 설계 방식을 따르는 것

⸻
