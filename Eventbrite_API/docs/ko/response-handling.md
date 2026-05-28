# 응답 처리 가이드

Eventbrite API 응답은 중첩된 JSON 객체, 선택적 필드, HTML 형식의 콘텐츠를
포함합니다. 이 가이드에서는 응답 구조를 읽는 방법, 널(null)이거나 선택적인
필드를 안전하게 처리하는 방법, HTML 콘텐츠를 올바르게 처리하는 방법을
알아볼게요.

> **참고:** 이 가이드는 응답 처리를 중심으로 설명합니다. 엔드포인트 스키마와
파라미터 정의는 [API 레퍼런스]()를 참고하세요.
> 

<br>

## 목차

- [응답 구조](#응답-구조)
- [HTML 형식 콘텐츠 처리](#html-형식-콘텐츠-처리)
- [선택적·널 필드 처리](#선택적널-필드-처리)
- [오류 응답](#오류-응답)
- [Best Practices](#best-practices)

<br>

## 응답 구조

### 이벤트 객체

대부분의 이벤트 관련 엔드포인트는 이벤트 객체를 반환합니다.
이 객체는 네 가지 범주의 필드로 구성됩니다.

| 범주 | 필드 |
| --- | --- |
| 식별 | `id` |
| 텍스트 콘텐츠 | `name`, `description` — 각각 `text`와 `html` 변형을 포함 |
| 일정 | `start`, `end` — 각각 `utc`, `local`, `timezone`을 포함 |
| 메타데이터 | `status`, `currency`, `capacity`, `url` |

**응답 예시:**

```json
{
  "id": "12345",
  "name": {
    "text": "내 이벤트",
    "html": "<p>내 이벤트</p>"
  },
  "description": {
    "text": "이벤트 설명",
    "html": "<p>이벤트 설명</p>"
  },
  "start": {
    "timezone": "America/Los_Angeles",
    "utc": "2026-06-01T09:00:00Z",
    "local": "2026-06-01T02:00:00"
  },
  "end": {
    "timezone": "America/Los_Angeles",
    "utc": "2026-06-01T17:00:00Z",
    "local": "2026-06-01T10:00:00"
  },
  "status": "live",
  "currency": "USD",
  "capacity": 100,
  "url": "<https://www.eventbrite.com/e/12345>"
}
```

### 주요 필드

| 필드 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `id` | String | 예 | 이벤트의 고유 식별자 |
| `name` | Object | 예 | 이벤트 이름 — `text`(일반 텍스트)와 `html`(HTML 형식) 변형 포함 |
| `description` | Object | 아니오 | 이벤트 설명 — `text`와 `html` 변형 포함. 초안(draft) 이벤트에서는 `null`일 수 있음 |
| `status` | String | 예 | 이벤트 상태 — `draft`, `live`, `started`, `ended`, `completed`, `canceled` |
| `start` | Object | 예 | 시작 시간 — `utc`, `local`, `timezone` 포함 |
| `end` | Object | 예 | 종료 시간 — `utc`, `local`, `timezone` 포함 |
| `hide_start_date` | Boolean | 아니오 | `true`이면 공개 이벤트 페이지에서 시작 날짜를 숨깁니다 |
| `hide_end_date` | Boolean | 아니오 | `true`이면 공개 이벤트 페이지에서 종료 날짜를 숨깁니다 |

<br>

## HTML 형식 콘텐츠 처리

Eventbrite는 두 가지 패턴으로 HTML 콘텐츠를 반환합니다.

### 패턴 1 — 전체 HTML 응답

일부 엔드포인트는 응답 본문 전체를 원시 HTML 문자열로 반환합니다.

**엔드포인트:**

```
GET /events/{event_id}/description/
```

**응답:**

```json
{
  "description": "<div>예시 요약!</div>\\n<div><p>내 <em>이벤트</em> 설명이 <strong>여기</strong>에 들어갑니다.</p></div>"
}
```

이 콘텐츠를 렌더링할 때는 HTML 파서를 사용하세요.
원시 문자열을 사용자에게 직접 표시하지 마세요.

### 패턴 2 — text/html 이중 필드

일부 엔드포인트는 같은 콘텐츠에 대해 일반 텍스트 변형과
HTML 변형을 모두 포함하는 객체를 반환합니다.

**예시 — 티켓 구매자 설정:**

```
GET /events/{event_id}/ticket_buyer_settings/
```

**응답:**

```json
{
  "confirmation_message": {
    "text": "확인 메시지",
    "html": "<h1>확인 메시지</h1>"
  },
  "instructions": {
    "text": "안내사항",
    "html": "<h1>안내사항</h1>"
  }
}
```

알림이나 메타데이터처럼 일반 문자열이 필요할 때는 `text`를 사용하세요.
웹 인터페이스에서 콘텐츠를 렌더링할 때는 `html`을 사용하세요.

<br>

## 선택적·널 필드 처리

모든 응답에 모든 필드가 포함되지는 않습니다.
다음의 경우에는 필드가 없거나 `null`일 수 있습니다.

- 데이터가 불완전한 초안(draft) 이벤트
- 토큰에 접근 권한이 없는 필드
- 이벤트 주최자가 입력하지 않은 필드

중첩된 속성에 접근하기 전에 필드가 존재하는지 확인하세요.

**JavaScript 예시:**

```jsx
const name = event?.name?.text ?? '제목 없는 이벤트';
const description = event?.description?.text ?? '';
const capacity = event?.capacity ?? null;
```

**Python 예시:**

```python
name = event.get('name', {}).get('text', '제목 없는 이벤트')
description = event.get('description', {}).get('text', '')
capacity = event.get('capacity')
```

<br>

## 오류 응답

모든 이벤트 엔드포인트는 일관된 오류 형식을 반환합니다.

```json
{
  "error": "ERROR_CODE",
  "error_description": "사람이 읽을 수 있는 설명",
  "status_code": 400
}
```

### 주요 오류 코드

| 상태 코드 | 오류 코드 | 원인 | 조치 |
| --- | --- | --- | --- |
| `400` | `FIELD_INVALID` | 요청에 유효하지 않거나 충돌하는 파라미터가 있습니다 | [API 레퍼런스]()에서 요청 본문과 파라미터를 확인하세요 |
| `401` | `NO_AUTH` | 토큰이 없거나 유효하지 않습니다 | `Authorization` 헤더에 유효한 프라이빗 토큰을 포함하세요 |
| `403` | `NOT_AUTHORIZED` | 토큰이 리소스에 접근할 권한이 없습니다 | 토큰 범위를 확인하거나 올바른 권한을 가진 토큰을 사용하세요 |
| `404` | `NOT_FOUND` | 요청한 이벤트가 존재하지 않습니다 | `event_id`가 올바른지, 이벤트가 삭제되지 않았는지 확인하세요 |

<br>

## Best Practices

### 필요한 필드만 추출하기

전체 응답 객체를 저장하지 마세요. 애플리케이션에서 사용하는 필드만 접근하세요.

```jsx
// 필요한 것만 추출하기
const { id, status } = response;
const name = response.name?.text;
const startUtc = response.start?.utc;
```

### HTML과 일반 텍스트 분리하기

`html` 필드를 일반 텍스트가 필요한 곳에 사용하지 마세요.
`text` 필드를 HTML 렌더러에 전달하지 마세요.

```jsx
// UI에 표시할 때 — html 사용
container.innerHTML = event.name.html;

// 알림 시스템에 전달할 때 — text 사용
sendNotification({ title: event.name.text });
```

### 오류를 사용자 메시지로 변환하기

API 오류 코드를 사용자가 이해할 수 있는 메시지로 매핑하세요.

```jsx
const ERROR_MESSAGES = {
  NO_AUTH:        '인증에 실패했습니다. API 토큰을 확인하세요.',
  NOT_AUTHORIZED: '이 이벤트에 접근할 권한이 없습니다.',
  NOT_FOUND:      '이벤트를 찾을 수 없습니다.',
  FIELD_INVALID:  '요청에 유효하지 않은 값이 포함되어 있습니다.',
};

function handleApiError(error) {
  return ERROR_MESSAGES[error.error] ?? '예기치 않은 오류가 발생했습니다.';
}
```

<br>

## 다음 단계

- [API 레퍼런스]()
- [인증 가이드]()
- [코드 예제]()

<br>
