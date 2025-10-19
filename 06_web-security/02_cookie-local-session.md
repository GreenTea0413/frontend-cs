# 💿 쿠키 vs 로컬스토리지 vs 세션스토리지

## 개념 요약

| 항목 | 쿠키 (Cookie) | 로컬스토리지 (localStorage) | 세션스토리지 (sessionStorage) |
| --- | --- | --- | --- |
| 저장 위치 | 브라우저 + 서버 | 브라우저 전용 | 브라우저 전용 |
| 저장 용량 | 약 4KB | 약 5~10MB | 약 5~10MB |
| 데이터 수명 | 설정 가능 (없으면 세션 종료 시) | 명시적 삭제 전까지 유지 | 탭 종료 시 삭제 |
| 자동 전송 | 요청마다 서버에 자동 전송 | 자동 전송 안 됨 | 자동 전송 안 됨 |
| 보안 설정 | `HttpOnly`, `Secure`, `SameSite` 등 가능 | 설정 불가 | 설정 불가 |
| 사용 예시 | 인증, 로그인 유지 | 토큰, 사용자 설정값 | 일시적 폼 값, UI 상태 |

⸻

## 쿠키
- 클라이언트 <-> 서버 간에 자동으로 함께 전송됨
- 민감 정보는 HttpOnly, Secure, SameSite 속성 꼭 설정
- 보통 JWT Refresh Token을 저장할 때 사용

## localStorage
- 브라우저에 영구적으로 저장 (삭제 전까지 유지)
- JWT Access Token이나 사용자 설정값 등에 사용되나 XSS 공격에 취약하므로 민감한 정보 저장 ❌

## sessionStorage
- 같은 탭에서만 유지, 탭 닫으면 삭제
- 로그인 상태, 임시 폼 저장 등에 유용
- 페이지 새로고침해도 유지되지만, 탭이 바뀌면 사라짐

⸻

## 추천 저장 위치

| 상황 | 추천 저장소 |
| --- | --- |
| 민감한 인증 정보 저장 | 쿠키 (`HttpOnly`) |
| 장기적인 사용자 데이터 | localStorage |
| 탭 단위 일시적인 데이터 | sessionStorage |
| accessToken 저장 | localStorage 또는 메모리 (단기 저장) |
| refreshToken 저장 | HttpOnly 쿠키 (XSS 방지 목적) |
