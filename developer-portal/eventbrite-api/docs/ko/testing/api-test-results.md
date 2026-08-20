# Eventbrite Event Order API 테스트 결과

Eventbrite Event Order API를 연동하기 전에 이 문서에서 공식 문서와 실제 API 동작의 차이를 확인하세요. 공식 문서에 명시됐지만 실제로 거부되는 파라미터 값과 엔드포인트별 응답 필드 불일치를 미리 파악하면 런타임 오류를 예방할 수 있습니다. 단일 주문 조회, 이벤트·사용자·조직별 주문 목록 조회, 필터 파라미터 5가지, 오류 응답을 포함한 총 15건의 테스트 케이스와 2건의 발견사항을 다룹니다.

> **이 문서는 Eventbrite API 공식 문서가 소개하는 Event Order 사용 사례를 직접 테스트하고 결과를 기록합니다. Event 문서와 동일한 테스트 방법론을 Order 엔드포인트에 확장해 적용했습니다.

**사전 요구사항:**

- 유효한 프라이빗 토큰 — [인증 가이드](../guides/authentication.md)를 참고하세요.
- 유효한 `order_id`, `event_id`, `organization_id` —  Eventbrite 계정에서 각 리소스의 ID를 확인하세요.

<br>
<br>

## 목차

- 테스트 환경
- 결과 요약
- 발견 사항
    - F-001 — `status=both` 문서 불일치
    - F-002 — `costs` 필드 엔드포인트별 불일치
- 테스트 케이스 및 결과
    - TC-001 — 단일 주문 조회
    - TC-002 — 이벤트별 주문 목록 조회
    - TC-003 — status 필터 검증
    - TC-004 — 내 주문 목록 조회
    - TC-005 — 이메일 필터 검증
    - TC-006 — refund_request_statuses 필터 검증
    - TC-007 — 조직별 주문 및 time_filter 검증
    - TC-008 — 존재하지 않는 주문 조회

<br>
<br>

## 테스트 환경

| 항목 | 값 |
| --- | --- |
| API 버전 | v3 |
| Base URL | `https://www.eventbriteapi.com/v3` |
| 인증 방식 | Bearer Token (프라이빗 토큰) |
| 테스트 도구 | cURL |

<br>
<br>

## 결과 요약

| TC ID | 테스트 대상 | 판정 |
| --- | --- | --- |
| TC-001 | 단일 주문 조회 | ✅ PASS |
| TC-002 | 이벤트별 주문 목록 조회 | ✅ PASS |
| TC-003 | `status=active` | ✅ PASS |
| TC-003 | `status=inactive` | ✅ PASS |
| TC-003 | `status=both` | ❌ 문서 불일치 |
| TC-003 | `status=all_not_deleted` | ✅ PASS |
| TC-003 | `status=invalid_value` | ✅ PASS |
| TC-004 | 내 주문 목록 조회 | ✅ PASS |
| TC-005 | `only_emails` 필터 | ✅ PASS |
| TC-005 | `exclude_emails` 필터 | ✅ PASS |
| TC-006 | `refund_request_statuses` 필터 (5가지) | ✅ PASS (조건부) |
| TC-007 | 조직별 주문 목록 조회 — 기본 | ✅ PASS |
| TC-007 | `time_filter=current_future` | ✅ PASS |
| TC-007 | `time_filter=past` | ✅ PASS |
| TC-008 | 존재하지 않는 주문 ID | ✅ PASS |

- 전체 테스트: 15건
- PASS: 13건
- PASS (조건부): 1건 — TC-006 (양성 케이스 미검증)
- 문서 불일치: 1건 — TC-003 `status=both`
- 미테스트: 1건 — `time_filter=all`

<br>
<br>

## 발견 사항

<br>

### F-001 — `stats=both` 문서 불일치

**심각도:** 높음

**대상 엔드포인트:** `GET /events/{event_id}/orders?status=both`

공식 문서는 `status` 파라미터의 유효한 값으로 `active`, `inactive`, `both`, `all_not_deleted` 4가지를 명시합니다. `both`로 요청하면 API는 유효하지 않은 값이라는 `400 ARGUMENTS_ERROR`를 반환하며 거부합니다. 원인은 현재 확인할 수 없습니다.

| 항목 | 내용 |
| --- | --- |
| 공식 문서 | `both`를 유효한 값으로 명시 |
| 실제 API 응답 | `400 ARGUMENTS_ERROR` — `"both" is not a valid choice` |
| 원인 | 확인 불가 |
| 영향 | `both` 값에 의존하는 구현은 런타임 오류 발생 |

전체 주문 조회가 필요하다면 `all_not_deleted`를 사용하세요. 동작 검증은 [TC-003](#tc-003--status-필터-검증)을 참고하세요.

<br>

### F-002 — `costs` 필드 엔드포인트별 불일치

**심각도:** 낮음

**대상 엔드포인트:** `GET /organizations/{organization_id}/orders`

단일 주문 조회, 이벤트별 주문 조회, 내 주문 조회의 `costs` 객체는 아래 필드를 포함합니다.

```
display_fee · display_price · has_gts_tax · fee_components · shipping_components · tax_components
```

조직별 주문 조회는 앞서 언급된 필드를 반환하지 않으며, `base_price`, `eventbrite_fee`, `gross`, `payment_fee`, `tax` 5개 필드만 포함합니다. 별도의 이유는 공식 문서에 명시되어 있지 않습니다. 

| 엔드포인트 | `display_fee` | `display_price` | `has_gts_tax` | `fee_components` | `shipping_components` | `tax_components` |
| --- | --- | --- | --- | --- | --- | --- |
| `GET /orders/{id}/` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `GET /events/{event_id}/orders` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `GET /users/me/orders` | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| `GET /organizations/{org_id}/orders` | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

조직별 주문 목록에서 비용 상세 정보가 필요하다면 각 주문에 대해 `GET /orders/{order_id}/`를 추가로 호출하세요.

<br>
<br>

## 테스트 케이스 및 결과

<br>

### TC-001 — 단일 주문 조회

**엔드포인트:** `GET /orders/{order_id}/`

**목적:** 유효한 `order_id`로 단일 주문의 전체 정보를 가져옵니다.

**요청:**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/orders/{order_id}/"
```
**예상 결과:** `200 OK`. 주문 객체와 전체 비용(`costs`) 필드 반환.

**실제 응답:**

```json
{
  "id": "155064580...",
  "name": "Summer Lee",
  "status": "placed",
  "event_id": "1998401665...",
  "costs": {
    "base_price":    { "display": "$0.00", "currency": "USD", "value": 0, "major_value": "0.00" },
    "display_fee":   { "display": "$0.00", "currency": "USD", "value": 0, "major_value": "0.00" },
    "display_price": { "display": "$0.00", "currency": "USD", "value": 0, "major_value": "0.00" },
    "gross":         { "display": "$0.00", "currency": "USD", "value": 0, "major_value": "0.00" },
    "has_gts_tax": false,
    "fee_components": [],
    "shipping_components": [],
    "tax_components": []
  },
  "time_remaining": 0
}
```

**판정:** ✅ PASS — `200 OK`. 주문 객체와 전체 비용 필드를 반환합니다.

<br>

### TC-002 — 이벤트별 주문 목록 조회

**엔드포인트:** `GET /events/{event_id}/orders`

**목적:** 유효한 `event_id`로 해당 이벤트의 주문 목록과 페이지네이션 객체를 가져옵니다.

**요청:**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/orders"
```

**예상 결과:** `200 OK`. `pagination` 객체와 `orders` 배열 반환.

**실제 응답:**

```json
{
  "pagination": {
    "object_count": 1,
    "page_number": 1,
    "page_size": 50,
    "page_count": 1,
    "has_more_items": false
  },
  "orders": [
    {
      "id": "15506458...",
      "name": "Summer Lee",
      "status": "placed",
      "event_id": "1998401665..."
    }
  ]
}
```

**판정:** ✅ PASS — `200 OK`. `pagination` 객체와 `orders` 배열을 반환합니다.

<br>

### TC-003 — status 필터 검증

**엔드포인트:** `GET /events/{event_id}/orders?status={value}`

**목적:** `status` 필터 값 4가지와 유효하지 않은 값 1가지의 동작을 검증합니다.

유효값: `active`, `inactive`, `both`, `all_not_deleted`

| 파라미터 값 | 예상 결과 | 실제 결과 | 판정 |
| --- | --- | --- | --- |
| `active` | 활성 주문 반환 | `object_count: 1`, 1건 반환 | ✅ PASS |
| `inactive` | 비활성 주문 반환 | `object_count: 0`, 0건 반환 | ✅ PASS |
| `both` | 전체 주문 반환 | `400 ARGUMENTS_ERROR` 반환 | ❌ 문서 불일치 |
| `all_not_deleted` | 삭제되지 않은 전체 주문 반환 | `object_count: 1`, 1건 반환 | ✅ PASS |
| `invalid_value` | `400` 오류 반환 | `400 ARGUMENTS_ERROR` 반환 | ✅ PASS |

**`both` 실제 오류 응답:**

```json
{
  "error": "ARGUMENTS_ERROR",
  "status_code": 400,
  "error_description": "There are errors with your arguments: status - \"both\" is not a valid choice."
}
```

> **`both` 값은 공식 문서에 유효한 값으로 명시되어 있으나 API가 거부합니다.** 자세한 내용은 [발견 사항 — F-001](#f-001--statusboth-문서-불일치)을 참고하세요.

<br>

### TC-004 — 내 주문 목록 조회

**엔드포인트:** `GET /users/me/orders`

**목적:** 인증된 사용자의 전체 주문 목록을 가져옵니다.

**요청:**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/users/me/orders"
```

**예상 결과:** `200 OK`. 현재 사용자의 주문 목록 반환.

**실제 응답:**

```json
{
  "orders": [
    {
      "id": "15506458...",
      "name": "Summer Lee",
      "status": "placed",
      "event_id": "1998401665..."
    }
  ],
  "pagination": {
    "object_count": 1,
    "page_number": 1,
    "page_size": 50,
    "page_count": 1,
    "has_more_items": false
  }
}
```

**판정:** ✅ PASS — `200 OK`. 현재 사용자의 주문 목록을 반환합니다.

<br>

### TC-005 — 이메일 필터 검증

**엔드포인트:** `GET /events/{event_id}/orders?only_emails={email}` / `?exclude_emails={email}`

**목적:** `only_emails`와 `exclude_emails` 파라미터가 이메일 주소를 기준으로 주문을 올바르게 필터링하는지 검증합니다.

| 파라미터 | 예상 결과 | 실제 결과 | 판정 |
| --- | --- | --- | --- |
| `only_emails={email}` | 해당 이메일 주문만 반환 | `object_count: 1`, 1건 반환 | ✅ PASS |
| `exclude_emails={email}` | 해당 이메일 주문 제외 | `object_count: 0`, 0건 반환 | ✅ PASS |

**판정:** ✅ PASS — 두 파라미터 모두 이메일 주소를 기준으로 올바르게 필터링합니다.

<br>

### TC-006 — refund_request_statuses 필터 검증

**엔드포인트:** `GET /events/{event_id}/orders?refund_request_statuses={value}`

**목적:** `refund_request_statuses` 필터 값 5가지의 동작을 검증합니다.

유효값: `completed`, `pending`, `outside_policy`, `disputed`, `denied`

| 파라미터 값 | 실제 결과 | 판정 |
| --- | --- | --- |
| `completed` | `object_count: 0`, 0건 반환 | ✅ PASS |
| `pending` | `object_count: 0`, 0건 반환 | ✅ PASS |
| `outside_policy` | `object_count: 0`, 0건 반환 | ✅ PASS |
| `disputed` | `object_count: 0`, 0건 반환 | ✅ PASS |
| `denied` | `object_count: 0`, 0건 반환 | ✅ PASS |

> **테스트 데이터 한계:** 테스트한 주문에는 환불 요청이 없습니다. 5가지 값 모두 오류 없이 빈 배열을 반환했으나, 각 필터 값이 해당 상태의 주문을 올바르게 반환하는지는 이 테스트에서 확인할 수 없습니다. 환불 요청이 있는 주문으로 추가 검증이 필요합니다.

**판정:** ✅ PASS (조건부) — 필터 파라미터 자체는 오류 없이 동작합니다. 단, 양성 케이스로 검증되지 않았습니다.

<br>

### TC-007 — 조직별 주문 및 time_filter 검증

**엔드포인트:** `GET /organizations/{organization_id}/orders`

**목적:** 조직 단위로 주문 목록을 가져오고, `time_filter` 파라미터의 동작을 검증합니다.

유효값: `all`, `past`, `current_future`

| 조건 | 예상 결과 | 실제 결과 | 판정 |
| --- | --- | --- | --- |
| 필터 없음 (기본) | 전체 주문 반환 | `object_count: 1`, 1건 반환 | ✅ PASS |
| `time_filter=current_future` | 현재·미래 이벤트 주문 반환 | `object_count: 1`, 1건 반환 | ✅ PASS |
| `time_filter=past` | 과거 이벤트 주문 반환 | `object_count: 0`, 0건 반환 | ✅ PASS |
| `time_filter=all` | 전체 주문 반환 | 미테스트 | — |

**판정:** ✅ PASS — 테스트한 값을 기준으로 이벤트 시간에 따라 올바르게 필터링합니다.

> **참고:** 조직별 주문 조회 응답의 `costs` 객체는 다른 엔드포인트보다 적은 필드를 반환합니다. 자세한 내용은 [발견 사항 — F-002](#f-002--costs-필드-엔드포인트별-불일치)를 참고하세요.

<br>

### TC-008 — 존재하지 않는 주문 조회

**엔드포인트:** `GET /orders/{order_id}/`

**목적:** 유효하지 않은 `order_id`로 요청했을 때 API가 `404` 오류를 반환하는지 검증합니다.

**요청:**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/orders/00000000000/"
```

**예상 결과:** `404 NOT_FOUND` 오류 반환.

**실제 응답:**

```json
{
  "status_code": 404,
  "error": "NOT_FOUND",
  "error_description": "The path you requested does not exist."
}
```

**판정:** ✅ PASS — `404 NOT_FOUND`. 표준 오류 형식으로 응답합니다.

<br>
<br>

## 다음 단계

- [오류 레퍼런스](../api/error-reference.md)
- [응답 처리 가이드](../guides/response-handling.md)
- [API 레퍼런스](../api/api-reference.md)

<br>
<br>
