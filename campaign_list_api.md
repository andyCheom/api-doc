

## 📧 대량메일 캠페인 목록 및 발송 통계 조회 API

-----

## ⚙️ 요청 상세 정보 (Custom Operation Details)

### 1\. 인증 및 헤더

| 속성 | 위치 | 필수 여부 | 설명 |
| :--- | :--- | :--- | :--- |
| **`Content-Type`** | Header | **Yes** | `application/json` |

### 2\. 요청 본문 (Request Body)


| 이름 | 타입 | 필수 여부 | 설명 | 예시 |
| :--- | :--- | :--- | :--- | :--- |
| **`api_key`** | `string` | **Yes** | 계정관리 - API용 KEY를 그대로 입력하시면 됩니다. | dfd6fd9fsdfbjdnfkd38fndjfd023 |
| **`start_time`** | `string` | No | 발송 시작일시로 필터링 | 2025-11-01T00:00:00Z |
| **`end_time`** | `string` | No | 발송 종료일시로 필터링 | 2025-11-30T23:59:59Z |
| **`campaign_title`** | `string` | No | 캠페인명(제목)으로 필터링 | '보유 포인트 유효기간 안내' |
| **`campaign_writer`** | `string` | No | 캠페인 작성자로 필터링 | '관리자' |
| **`limit`** | `number` | No | 페이지당 반환되는 캠페인 수 (최대 1000) **기본값:** 10 | 50 |
| **`page`** | `number` | No | 조회할 페이지 번호. **기본값:** 1 | 2 |

**요청 본문 형식 (JSON 예시):**

```json
{
  "api_key": "dfd6fd9fsdfbjdnfkd38fndjfd023",
  "start_time": "2025-11-01T00:00:00Z",
  "end_time": "2025-11-30T23:59:59Z",
  "campaign_title": "보유 포인트 유효기간 안내",
  "campaign_writer": "관리자",
  "limit": 50,
  "page": 2
}
```

-----

## ✅ Responses (응답)

### 200 OK (성공)

조회 조건에 맞는 캠페인 목록과 통계 정보를 반환합니다.

| 속성 | 타입 | 설명 |
| :--- | :--- | :--- |
| **`campaigns`** | `array` | 캠페인 정보 객체의 배열 |
| **`no`** | `integer` | 리스트 내 순번 (Row Number) |
| **`campaign_id`** | `string` | 고유 캠페인 ID |
| **`title`** | `string` | 캠페인 제목 |
| **`writer`** | `string` | 캠페인 작성자 |
| **`writer_time`** | `string` | 캠페인 작성(생성) 일시 (ISO 8601) |
| **`send_time`** | `string` | 캠페인 발송 일시 (ISO 8601) |
| **`total_count`** | `integer` | 총 발송 시도 건수 |
| **`success_count`** | `integer` | 발송 성공 건수 |
| **`fail_count`** | `integer` | 발송 실패 건수 |
| **`current_page`** | `integer` | 현재 조회된 페이지 번호 |
| **`total_pages`** | `integer` | 총 페이지 수 |
| **`total_items`** | `integer` | 필터 조건에 맞는 전체 캠페인 수 |

**응답 본문 형식 (JSON 예시):**

```json
{
  "campaigns": [
    {
      "no": 1,
      "campaign_id": "abc12345",
      "title": "보유 포인트 유효기간 안내",
      "writer": "관리자",
      "writer_time": "2025-11-19T09:30:00Z",
      "send_time": "2025-11-19T10:30:00Z",
      "total_count": 5,
      "success_count": 3,
      "fail_count": 2
    },
    {
      "no": 2,
      "campaign_id": "xyz67890",
      "title": "신규 회원 가입 환영 인사",
      "writer": "매니저",
      "writer_time": "2025-11-20T11:00:00Z",
      "send_time": "2025-11-20T12:00:00Z",
      "total_count": 100,
      "success_count": 98,
      "fail_count": 2
    }
  ],
  "current_page": 2,
  "total_pages": 100, 
  "total_items": 5000
}
```

### 400 Bad Request (잘못된 요청)

```json
{
  "errors": [
    {
      "message": "Missing required field: api_key"
    }
  ]
}
```
