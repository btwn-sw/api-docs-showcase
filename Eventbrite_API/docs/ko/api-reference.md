# Eventbrite API 레퍼런스

Eventbrite API의 엔드포인트, 파라미터, 응답 필드를 조회하세요.
이 레퍼런스는 이벤트 관련 엔드포인트를 중심으로 안내합니다.
인증 설정은 [인증 가이드]()를 참고하세요.

<br>

## 목차

- [Base URL 및 버전](#base-url-및-버전)
- [인증](#인증)
- [요청 제한](#요청-제한)
- [오류 처리](#오류-처리)
- [이벤트 엔드포인트](#이벤트-엔드포인트)
    - [이벤트 생성](#이벤트-생성)
    - [이벤트 조회](#이벤트-조회)
    - [이벤트 수정](#이벤트-수정)
    - [이벤트 삭제](#이벤트-삭제)

<br>

## Base URL 및 버전

모든 엔드포인트는 아래 Base URL을 사용합니다.

```
https://www.eventbriteapi.com/v3
```

- 현재 API 버전: `v3`
- 모든 엔드포인트는 `/v3/`로 시작합니다
- 요청 형식: `application/json`
- 응답 형식: `application/json`

<br>

## 인증

모든 요청은 `Authorization` 헤더에 프라이빗 토큰이 필요합니다.

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
```

`YOUR_PRIVATE_TOKEN`을
[Eventbrite API Key](https://www.eventbrite.com/account-settings/apps)에서 제공한 
토큰으로 바꾸세요.
전체 설정 방법은 [인증 가이드]()를 참고하세요.

<br>

## 요청 제한

Eventbrite는 플랫폼 안정성을 위해 모든 API 통합에 요청 제한을 적용합니다.

### 기본 제한

| 제한 유형 | 제한 |
| --- | --- |
| 전체 요청 | 시간당 2,000건 |
| 전체 요청 | 일일 48,000건 |
| 이벤트 생성 | 시간당 200건 |
| 이벤트 게시 | 시간당 200건 |

### 요청 제한 헤더

요청 제한이 적용되면 Eventbrite는 응답에 아래 헤더를 포함합니다.

| 헤더 | 설명 |
| --- | --- |
| `X-Apiary-RateLimit-Limit` | 허용된 최대 요청 수 |
| `X-Apiary-RateLimit-Remaining` | 현재 윈도우에서 남은 요청 수 |

<br>

## 오류 처리

모든 이벤트 엔드포인트에서 아래 오류 응답이 반환될 수 있습니다.

| 상태 코드 | 오류 코드 | 설명 |
| --- | --- | --- |
| `400` | `BAD_CONTINUATION_TOKEN` | 요청이 유효하지 않거나 충돌하는 파라미터가 있습니다 |
| `401` | `NO_AUTH` | 인증 토큰이 없거나 유효하지 않습니다 |
| `403` | `NOT_AUTHORIZED` | 토큰에 이 작업을 수행할 권한이 없습니다 |
| `404` | `NOT_FOUND` | 요청한 이벤트가 존재하지 않습니다 |

**오류 응답 형식:**

```json
{
  "error": "VENUE_AND_ONLINE",
  "error_description": "You cannot both specify a venue and set online_event",
  "status_code": 400
}
```

다양한 오류 결과에 대한 해결책은 [응답 처리 가이드]()를 참고하세요.

<br>

## 이벤트 엔드포인트



### 이벤트 생성

조직 아래에 새 이벤트를 만듭니다.

**엔드포인트**

```
POST /organizations/{organization_id}/events/
```

**경로 파라미터**

| 이름 | 타입 | 필수 | 설명 | 예시 |
| --- | --- | --- | --- | --- |
| `organization_id` | String | 예 | 이벤트를 소유할 조직의 ID | `12345` |

**요청 본문 파라미터**

| 필드 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `event.name.html` | String | 예 | HTML 형식의 이벤트 이름 |
| `event.description.html` | String | 아니오 | HTML 형식의 이벤트 설명 |
| `event.start.utc` | String | 예 | UTC 시작 시간 — 형식: `YYYY-MM-DDTHH:MM:SSZ` |
| `event.start.timezone` | String | 예 | 시작 시간 시간대 — 예: `America/Los_Angeles` |
| `event.end.utc` | String | 예 | UTC 종료 시간 — 형식: `YYYY-MM-DDTHH:MM:SSZ` |
| `event.end.timezone` | String | 예 | 종료 시간 시간대 |
| `event.currency` | String | 예 | ISO 4217 통화 코드 — 예: `USD` |
| `event.online_event` | Boolean | 아니오 | 온라인 전용 이벤트는 `true`. 장소와 함께 설정할 수 없습니다. |
| `event.capacity` | Integer | 아니오 | 최대 참석자 수 |
| `event.listed` | Boolean | 아니오 | 이벤트를 공개적으로 표시하려면 `true` |
| `event.invite_only` | Boolean | 아니오 | 초대받은 참석자만 접근하도록 제한하려면 `true` |

**요청 예시**

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

**응답: `200 OK`**

```json
{
  "id": "12345",
  "name": {
    "text": "내 이벤트",
    "html": "<p>내 이벤트</p>"
  },
  "start": {
    "timezone": "UTC",
    "utc": "2026-06-01T09:00:00Z",
    "local": "2026-06-01T09:00:00"
  },
  "end": {
    "timezone": "UTC",
    "utc": "2026-06-01T17:00:00Z",
    "local": "2026-06-01T17:00:00"
  },
  "status": "draft",
  "url": "<https://www.eventbrite.com/e/12345>",
  "created": "2026-05-01T10:00:00Z"
}
```

**응답 필드**

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `id` | String | 생성된 이벤트의 고유 식별자 |
| `name.text` | String | 일반 텍스트 형식의 이벤트 이름 |
| `name.html` | String | HTML 형식의 이벤트 이름 |
| `start.utc` | String | UTC 시작 시간 |
| `start.local` | String | 이벤트 현지 시간대의 시작 시간 |
| `end.utc` | String | UTC 종료 시간 |
| `end.local` | String | 이벤트 현지 시간대의 종료 시간 |
| `status` | String | 이벤트 상태 — `draft`, `live`, `canceled` |
| `url` | String | Eventbrite의 이벤트 공개 URL |
| `created` | String | 이벤트 생성 시각 — UTC |



### 이벤트 조회

단일 이벤트의 상세 정보를 반환합니다.

**엔드포인트**

```
GET /events/{event_id}/
```

**경로 파라미터**

| 이름 | 타입 | 필수 | 설명 | 예시 |
| --- | --- | --- | --- | --- |
| `event_id` | String | 예 | 조회할 이벤트의 고유 ID | `12345` |

**요청 예시**

```bash
curl --request GET \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  "<https://www.eventbriteapi.com/v3/events/{event_id}/>"
```

**응답: `200 OK`**

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
  "url": "<https://www.eventbrite.com/e/12345>",
  "capacity": 100,
  "currency": "USD"
}
```

**응답 필드**

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `id` | String | 이벤트의 고유 식별자 |
| `name.text` | String | 일반 텍스트 형식의 이벤트 이름 |
| `name.html` | String | HTML 형식의 이벤트 이름 |
| `description.text` | String | 일반 텍스트 형식의 이벤트 설명 |
| `description.html` | String | HTML 형식의 이벤트 설명 |
| `start.utc` | String | UTC 시작 시간 |
| `start.local` | String | 이벤트 현지 시간대의 시작 시간 |
| `end.utc` | String | UTC 종료 시간 |
| `end.local` | String | 이벤트 현지 시간대의 종료 시간 |
| `status` | String | 이벤트 상태 — `draft`, `live`, `canceled` |
| `url` | String | Eventbrite의 이벤트 공개 URL |
| `capacity` | Integer | 최대 참석자 수 |
| `currency` | String | 이벤트의 ISO 4217 통화 코드 |



### 이벤트 수정

기존 이벤트의 필드를 수정합니다. 변경할 필드만 포함하세요.

**엔드포인트**

```
POST /events/{event_id}/
```

**경로 파라미터**

| 이름 | 타입 | 필수 | 설명 | 예시 |
| --- | --- | --- | --- | --- |
| `event_id` | String | 예 | 수정할 이벤트의 고유 ID | `12345` |

**요청 본문 파라미터**

변경할 필드만 포함하세요.
요청 본문 구조는 [이벤트 생성](#이벤트-생성) 엔드포인트와 동일합니다.

**요청 예시**

```bash
curl --request POST \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  --header "Content-Type: application/json" \\
  --data '{
    "event": {
      "name": { "html": "<p>수정된 이벤트 이름</p>" },
      "capacity": 200
    }
  }' \\
  "<https://www.eventbriteapi.com/v3/events/{event_id}/>"
```

**응답: `200 OK`**

수정된 이벤트 객체를 반환합니다.
응답 필드는 [이벤트 조회](#이벤트-조회) 엔드포인트의 응답 필드와 동일합니다.



### 이벤트 삭제

이벤트를 영구적으로 삭제합니다. 이 작업은 되돌릴 수 없습니다.

**엔드포인트**

```
DELETE /events/{event_id}/
```

**경로 파라미터**

| 이름 | 타입 | 필수 | 설명 | 예시 |
| --- | --- | --- | --- | --- |
| `event_id` | String | 예 | 삭제할 이벤트의 고유 ID | `12345` |

**요청 예시**

```bash
curl --request DELETE \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  --header "Content-Type: application/json" \\
  "<https://www.eventbriteapi.com/v3/events/{event_id}/>"
```

**응답: `200 OK`**

```json
{
  "deleted": true
}
```

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `deleted` | Boolean | 이벤트가 성공적으로 삭제되면 `true`를 반환합니다 |

<br>

## 다음 단계

- [인증 가이드]()
- [응답 처리 가이드]()
- [코드 예제]()
- [SDK]()

<br>
