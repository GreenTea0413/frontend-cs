# CSRF vs XSS 기본 보안 위협

## CSRF (Cross-Site Request Forgery)란?

사용자가 인증된 상태를 악용해서 의도치 않은 요청을 보내도록 유도하는 공격
- 예: 사용자가 로그인된 상태에서 악성 사이트가 은행 이체 요청을 자동으로 보냄

공격 조건
- 사용자가 이미 로그인되어 있어야 함 (세션/쿠키 존재)
- 요청을 유도할 수 있는 취약한 페이지가 존재해야 함

대응 방법
- CSRF 토큰 사용
- SameSite 쿠키 정책 설정 (Lax or Strict)
- 중요 요청은 GET이 아닌 POST, PUT으로 제한

⸻

## XSS (Cross-Site Scripting)란?

공격자가 웹사이트에 악성 스크립트를 삽입, 사용자의 브라우저에서 실행되도록 유도하는 공격
- 예: 댓글에 <script>alert("해킹")</script> 삽입 → 방문자 브라우저에서 실행

공격 유형
- Stored XSS: 서버에 저장된 콘텐츠에 스크립트 삽입 (댓글 등)
- Reflected XSS: URL 등 사용자 입력을 그대로 출력
- DOM-based XSS: JS가 클라이언트에서 DOM을 조작할 때 발생

대응 방법
- 출력 시 escape 처리 (ex: React는 기본적으로 XSS 방지됨)
- Content Security Policy (CSP) 설정
- 사용자 입력 검증 (특히 <, >, script 태그 등 차단)

⸻

### 비교 

| 항목 | CSRF | XSS |
| --- | --- | --- |
| 공격 대상 | 서버 | 클라이언트 (브라우저) |
| 목표 | 사용자의 권한 도용 | 스크립트 실행, 정보 탈취 |
| 전제 조건 | 로그인된 상태 필요 | 필요 없음 |
| 피해 범위 | 사용자의 권한으로 요청 전송 | 세션 탈취, 피싱, UI 조작 등 |
| 방어 방법 | CSRF 토큰, SameSite 쿠키 | 입력 검증, CSP, escape 처리 |
