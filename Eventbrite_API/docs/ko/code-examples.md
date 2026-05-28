# 코드 예제

아래 예제를 복사해서 Eventbrite API 요청에 바로 활용하세요.
각 섹션은 같은 작업을 다른 언어나 도구로 보여줍니다.
전체 파라미터와 응답 필드 정의는
[API 레퍼런스]()를 참고하세요.

---

## 목차

- [인증](#인증)
- [cURL](#curl)
- [JavaScript](#javascript)
- [Node.js](#nodejs)
- [응답 처리](#응답-처리)

<br>

## 인증

모든 예제는 `Authorization` 헤더에 프라이빗 토큰을 사용합니다.
아래 모든 예제에서 `YOUR_PRIVATE_TOKEN`을 내 토큰으로 바꾸세요.

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
```

토큰 생성 방법은 [인증 가이드]()를 참고하세요.

<br>

## cURL

### 이벤트 조회

```bash
curl --request GET \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  --header "Content-Type: application/json" \\
  "<https://www.eventbriteapi.com/v3/events/{event_id}/>"
```

### 이벤트 생성

```bash
curl --request POST \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  --header "Content-Type: application/json" \\
  --data '{
    "event": {
      "name": { "html": "<p>내 이벤트</p>" },
      "start": { "timezone": "UTC", "utc": "2026-06-01T09:00:00Z" },
      "end":   { "timezone": "UTC", "utc": "2026-06-01T17:00:00Z" },
      "currency": "USD"
    }
  }' \\
  "<https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/>"
```

<br>

## JavaScript

아래 예제는 모든 최신 브라우저에서 지원하는 Fetch API를 사용합니다.

### 이벤트 조회

```jsx
async function getEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      method: "GET",
      headers: {
        "Authorization": "Bearer YOUR_PRIVATE_TOKEN",
        "Content-Type": "application/json"
      }
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

### 이벤트 생성

```jsx
async function createEvent(organizationId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/organizations/${organizationId}/events/`,
    {
      method: "POST",
      headers: {
        "Authorization": "Bearer YOUR_PRIVATE_TOKEN",
        "Content-Type": "application/json"
      },
      body: JSON.stringify({
        event: {
          name: { html: "<p>내 이벤트</p>" },
          start: { timezone: "UTC", utc: "2026-06-01T09:00:00Z" },
          end:   { timezone: "UTC", utc: "2026-06-01T17:00:00Z" },
          currency: "USD"
        }
      })
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

<br>

## Node.js

아래 예제는 서버 사이드 요청에 `node-fetch`를 사용하고
환경 변수에서 토큰을 읽습니다.

```bash
npm install node-fetch
```

### 이벤트 조회

```jsx
import fetch from "node-fetch";

async function getEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`,
        "Content-Type": "application/json"
      }
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  const event = await response.json();

  return {
    title:       event.name?.text,
    description: event.description?.html,
    status:      event.status
  };
}
```

### 이벤트 생성

```jsx
import fetch from "node-fetch";

async function createEvent(organizationId, eventData) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/organizations/${organizationId}/events/`,
    {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({ event: eventData })
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

<br>

## 응답 처리

### 필요한 필드만 추출하기

애플리케이션에서 필요한 필드만 접근하세요.

```jsx
const event = await getEvent("12345");

const title       = event.name?.text        ?? "제목 없음";
const description = event.description?.text ?? "";
const startTime   = event.start?.utc        ?? null;
```

### HTML과 일반 텍스트 분리하기

웹 인터페이스에서 렌더링할 때는 `html` 필드를 사용하세요.
알림이나 로그 같은 일반 문자열 컨텍스트에서는 `text` 필드를 사용하세요.

```jsx
// UI에 렌더링
container.innerHTML = event.description.html;

// 알림 시스템에 전달
sendNotification({ title: event.name.text });
```

### API 오류 처리하기

오류 코드를 애플리케이션에서 표시할 수 있는 메시지로 변환하세요.

```jsx
async function safeGetEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`
      }
    }
  );

  if (!response.ok) {
    const error = await response.json();
    const messages = {
      NO_AUTH:        "유효하지 않거나 없는 토큰입니다.",
      NOT_AUTHORIZED: "이 이벤트에 접근할 권한이 없습니다.",
      NOT_FOUND:      "이벤트를 찾을 수 없습니다."
    };
    throw new Error(
      messages[error.error] ?? `예기치 않은 오류: ${response.status}`
    );
  }

  return response.json();
}
```

<br>

## 다음 단계

- [API 레퍼런스]()
- [단계별 튜토리얼]()
- [인증 가이드]()
- [응답 처리 가이드]()
- [SDK]()

<br>
