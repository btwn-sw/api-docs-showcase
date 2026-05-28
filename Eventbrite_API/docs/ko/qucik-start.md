# 빠른 시작 가이드

3분 안에 Eventbrite API 첫 요청을 성공시키세요. 이 가이드는 토큰으로 인증하고, 엔드포인트를 호출하고, 실제 응답을 확인하는 최소한의 단계만 다룹니다. Eventbrite API 사전 경험은 필요하지 않습니다.

**사전 요구사항:**

- 활성화된 Eventbrite 계정
- cURL, Postman, 또는 웹 브라우저
- HTTP 요청에 대한 기본 이해

<br>

## 목차

- [1단계: 프라이빗 토큰 받기](#1단계-프라이빗-토큰-받기)
- [2단계: 첫 요청 보내기](#2단계-첫-요청-보내기)
- [3단계: 응답 확인하기](#3단계-응답-확인하기)

<br>

## 1단계: 프라이빗 토큰 받기

모든 Eventbrite API 요청은 `Authorization` 헤더에 프라이빗 토큰(Private Token)이 필요합니다.

1. [Eventbrite](https://www.eventbrite.com/signin/)에 로그인하세요.
2. **계정 설정(Account Settings)** → **Developer Links** → **API Keys**로 이동하세요.
3. *API 키 만들기(Create API Key)**를 클릭하고 필수 항목을 입력한 후
**키 만들기(Create Key)**를 클릭하세요.
4. **프라이빗 토큰(Private Token)**을 복사하세요.

전체 토큰 설정 방법은 [인증 가이드](../ko/authentication.md)를 참고하세요.

<br>

## 2단계: 첫 요청 보내기

이 요청은 Eventbrite 이벤트의 상세 정보를 가져옵니다.
`{event_id}`를 내 Eventbrite 계정의 유효한 이벤트 ID로 바꾸세요.

**방법 A — cURL**

```bash
curl --request GET \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  <https://www.eventbriteapi.com/v3/events/{event_id}/>
```

**방법 B — Eventbrite API 콘솔**

1. [Eventbrite API 레퍼런스](https://www.eventbrite.com/platform/api)를 여세요.
2. **Reference** → **Event** → **Retrieve an Event**로 이동하세요.
3. **Try**를 클릭하세요.
4. URI 파라미터에 유효한 `event_id`를 입력하세요.
5. 아래 Authorization 헤더를 추가하세요.

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
```

6. **Call Resource**를 클릭하세요.

<br>

## 3단계: 응답 확인하기

요청이 성공하면 `200 OK`와 함께 이벤트 상세 정보가 담긴 JSON 객체를 반환합니다.

```json
{
  "name": { "text": "My Event", "html": "<p>My Event</p>" },
  "status": "live",
  "start": { "utc": "2026-06-01T09:00:00Z" },
  "end":   { "utc": "2026-06-01T17:00:00Z" }
}
```

`401 Unauthorized` 오류가 발생하면 토큰이 올바른지, URL 파라미터가 아닌 `Authorization` 헤더에 포함되어 있는지 확인하세요.

<br>

## 다음 단계

첫 API 요청을 성공했습니다. 목표에 맞는 다음 단계를 선택하세요.

| 목표 | 가이드 |
| --- | --- |
| 전체 API 흐름을 단계별로 학습하고 싶다 | [단계별 튜토리얼]() |
| 사용 가능한 모든 엔드포인트를 보고 싶다 | [API 레퍼런스]() |
| 코드에서 응답을 안전하게 처리하고 싶다 | [응답 처리 가이드]() |
| 문제를 해결하고 싶다 | [트러블슈팅]() |

<br>
