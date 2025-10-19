# 🔐 JWT (JSON Web Token)

## JWT란?

JWT는 서버와 클라이언트 간의 인증 정보를 JSON 형식으로 안전하게 전달하기 위한 토큰 기반 인증 방식<br>
로그인 성공 시 서버는 JWT를 발급하고, 클라이언트는 이 토큰을 저장해서 이후 요청마다 서버에 함께 전송함으로써 인증을 유지할 수 있음

⸻

### JWT 구조

JWT는 총 3개의 파트로 구성된 문자열이야. 각 부분은 .으로 구분됨:

`xxxxx.yyyyy.zzzzz`

| 구성 요소 | 설명 | 인코딩 방식 |
| --- | --- | --- |
| Header | 토큰 타입 (typ: "JWT")과 알고리즘 (alg) 정보 | Base64URL |
| Payload | 사용자 정보, 권한, 만료 시간 등의 클레임 | Base64URL |
| Signature | Header + Payload를 비밀 키로 서명한 값 | HMAC SHA256 등 |


⸻

예시 구조
```
Header:
{
  "alg": "HS256",
  "typ": "JWT"
}

Payload:
{
  "userId": "123",
  "role": "admin",
  "exp": 1697900400
}

Signature:
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```
⸻

### JWT 작동 흐름
1.	사용자가 로그인 요청
2.	서버가 로그인 정보 검증 후 JWT 발급
3.	클라이언트는 JWT를 로컬스토리지 또는 쿠키에 저장
4.	이후 요청 시 Authorization 헤더에 토큰 포함
5.	서버는 토큰의 서명을 검증하고 유효하면 요청 처리

`Authorization: Bearer <JWT토큰>`
- 요청 보낼 때 해당 부분을 추가해서 보내면 됨

⸻

### JWT 장점 vs 단점

| 항목 | 장점 | 단점 |
| --- | --- | --- |
| 인증 방식 | Stateless (서버 저장 불필요) | 강제 로그아웃 어려움 |
| 속도 | DB 조회 없이 빠름 | Payload 탈취 시 정보 노출 |
| 적용 범위 | 다양한 플랫폼에서 사용 가능 | 보안 설정 미흡 시 XSS, CSRF 위험 |


⸻

### 주의사항
- JWT 자체는 암호화되지 않음, 단지 서명만 되어 있음
- 민감한 정보(비밀번호 등)는 절대 Payload에 넣지 말 것!
- 토큰 탈취 방지를 위해 HTTPS 필수
- 너무 긴 유효 시간은 ❌, 짧은 만료시간 + Refresh Token 전략을 병행하는 것이 일반적

⸻

### Access Token vs Refresh Token

| 항목 | Access Token | Refresh Token |
| --- | --- | --- |
| 목적 | 인증된 사용자임을 증명 | Access Token 재발급 용도 |
| 저장 위치 | 메모리, 로컬스토리지, 세션스토리지 | HttpOnly 쿠키 또는 서버 DB |
| 유효 기간 | 짧게 유지 (예: 15분) | 길게 유지 (예: 7일, 30일 등) |
| 발급 시점 | 로그인 성공 시 | 로그인 성공 시 |
| 전송 방식 | 요청 헤더에 포함 (`Authorization: Bearer`) | 쿠키 또는 서버 내부 |
| 노출 시 위험 | 사용자 권한 탈취 가능 | 무제한 토큰 발급 위험 |
| 보호 방법 | HTTPS, 만료시간 설정 | HTTPS + HttpOnly + Secure 쿠키 |


### 흐름
<img width="800" height="400" alt="image" src="https://github.com/user-attachments/assets/65a60eb0-9b25-4a64-9ff5-3b773091b870" />

프로젝트하면서 해당 그림과 동일하게 구현

<br>
1. 사용자 로그인<br>
2. 서버에서 Access + Refresh Token 발급<br>
3. Access는 클라이언트 저장, Refresh는 HttpOnly 쿠키에 저장<br>
4. API 요청 시 Access Token 사용<br>
5. Access Token이 만료되면 → Refresh Token으로 재발급 요청<br>
6. 서버가 Refresh Token 검증 후 새 Access Token 반환<br>
7. Refresh Token까지 만료 시 → 로그인 페이지로 이동

⸻

### 언제 쓰면 좋을까?

| 조건 | JWT 사용 적합 여부 |
| --- | --- |
| 모바일/SPA에서 Stateless 인증 | ✅ 적합 |
| 민감 정보 포함된 인증 | ❌ 부적합 |
| 마이크로서비스 간 인증 | ✅ 적합 |

