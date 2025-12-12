# 대량메일 캠페인 검색 API 명세서

## API 개요

대량메일 캠페인을 검색하는 API입니다. 다양한 조건(이름, 상태, 작성자, 날짜 등)으로 캠페인을 검색하고 페이징된 결과를 반환합니다.

---

## 엔드포인트 정보

### 캠페인 검색

```
POST /api/massive/v3/campaigns
```

**설명:** 대량메일 캠페인 목록을 검색 조건에 따라 조회합니다.

---

## 요청 (Request)

### Headers

| 이름 | 타입 | 필수 | 설명 |
|------|------|------|------|
| X-API-KEY | String | O | API 인증 키 |
| Content-Type | String | O | `application/json` |

### Query Parameters

| 이름 | 타입 | 필수 | 기본값 | 제약조건 | 설명 |
|------|------|------|--------|----------|------|
| limit | Integer | X | 10 | 0-1000 | 한 페이지당 조회할 캠페인 수 |
| offset | Integer | X | 0 | 0 이상 | 건너뛸 캠페인 수 (페이징 시작 위치) |

### Request Body

**Content-Type:** `application/json`

**필수 여부:** 선택 (빈 JSON `{}` 또는 body 없이 요청 가능)

#### 필드 정의

| 필드명 | 타입 | 필수 | 설명                      | 예시 |
|--------|------|------|-------------------------|------|
| name | String | X | 캠페인 이름 (부분 일치 검색)       | "테스트 캠페인" |
| status | Array[String] | X | 캠페인 상태 목록               | ["RESERVED", "PAUSED"] |
| writer | String | X | 캠페인 작성자 이름              | "admin" |
| created_at_start | String | X | 캠페인 생성일 시작 (yyyy-MM-dd) | "2024-01-01" |
| created_at_end | String | X | 캠페인 생성일 종료 (yyyy-MM-dd) | "2024-12-31" |
| campaign_group_id | Integer | X | 캠페인 그룹 ID               | 1 |

#### 캠페인 상태 (status) 값

| 값 | 설명 |
|----|------|
| RESERVED | 예약됨 |
| PAUSED | 일시중지 |
| COMPLETED | 완료 |
| READY | 준비 |
| STANDBY | 대기 |
| RESEND | 재발송 |
| COLLECTING_RESULTS | 결과 수집 중 |
| COLLECTION_COMPLETED | 수집 완료 |
| UPDATING_STATISTICS | 통계 업데이트 중 |

#### Request Body 예시

**1. 빈 요청 (모든 캠페인 조회)**
```json
{}
```

**2. 이름으로 검색**
```json
{
  "name": "테스트 캠페인"
}
```

**3. 상태로 검색**
```json
{
  "status": ["RESERVED", "PAUSED"]
}
```

**4. 복합 검색**
```json
{
  "name": "캠페인",
  "status": ["RESERVED", "READY"],
  "writer": "admin",
  "created_at_start": "2024-01-01",
  "created_at_end": "2024-12-31",
  "campaign_group_id": 1
}
```

---

## 응답 (Response)

### 성공 응답

**HTTP Status:** `200 OK`

**Content-Type:** `application/json`

#### Response Body 구조

| 필드명 | 타입 | 설명 |
|--------|------|------|
| total | Integer | 검색 조건에 맞는 전체 캠페인 수 |
| offset | Integer | 현재 페이지 오프셋 |
| limit | Integer | 페이지당 항목 수 |
| campaigns | Array[CampaignInfo] | 캠페인 정보 배열 |

#### CampaignInfo 구조

| 필드명 | 타입 | 설명 |
|--------|------|------|
| id | Integer | 캠페인 ID |
| name | String | 캠페인 이름 |
| status | String | 캠페인 상태 |
| writer | String | 작성자 |
| created_at | String | 생성일시 (ISO 8601 형식) |
| target_name | String | 주소록 이름 |
| send_at | String | 발송일시 (ISO 8601 형식) |
| total_count | Integer | 전체 발송 수 |
| success_count | Integer | 성공 발송 수 |
| fail_count | Integer | 실패 발송 수 |

#### 성공 응답 예시

```json
{
  "total": 25,
  "offset": 0,
  "limit": 10,
  "campaigns": [
    {
      "id": 123,
      "name": "2024 신년 이벤트 캠페인",
      "status": "COMPLETED",
      "writer": "admin",
      "created_at": "2024-01-05T09:30:00.000Z",
      "target_name": "VIP 고객 리스트",
      "send_at": "2024-01-10T10:00:00.000Z",
      "total_count": 1000,
      "success_count": 980,
      "fail_count": 20
    },
    {
      "id": 124,
      "name": "주간 뉴스레터",
      "status": "RESERVED",
      "writer": "admin",
      "created_at": "2024-01-08T14:20:00.000Z",
      "target_name": "전체 구독자",
      "send_at": "2024-01-15T09:00:00.000Z",
      "total_count": 5000,
      "success_count": 0,
      "fail_count": 0
    }
  ]
}
```

---

### 에러 응답

**Content-Type:** `application/json`

#### 에러 응답 구조

| 필드명 | 타입 | 설명 |
|--------|------|------|
| code | String | 에러 코드 |
| httpStatusCode | Integer | HTTP 상태 코드 |
| message | String | 에러 메시지 |

#### 에러 케이스

**1. API Key 누락**

**HTTP Status:** `400 Bad Request`

```json
{
  "code": "A004",
  "httpStatusCode": 400,
  "message": "API 키가 입력되지 않았습니다."
}
```

**2. 유효하지 않은 API Key**

**HTTP Status:** `401 Unauthorized`

```json
{
  "code": "A003",
  "httpStatusCode": 401,
  "message": "유효하지 않은 API 키입니다."
}
```

**3. 파라미터 유효성 검증 실패 (limit 범위 초과)**

**HTTP Status:** `400 Bad Request`

```json
{
  "code": "C001",
  "httpStatusCode": 400,
  "message": "searchCampaigns.limit: 1000 이하여야 합니다"
}
```

**4. 서버 내부 오류**

**HTTP Status:** `500 Internal Server Error`

```json
{
  "code": "C003",
  "httpStatusCode": 500,
  "message": "서버 내부 오류가 발생했습니다."
}
```

---

## 사용 예시

### cURL

```bash
# 기본 조회 (모든 캠페인)
curl -X POST "http://localhost:8080/api/massive/v3/campaigns" \
  -H "X-API-KEY: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{}'

# 상태로 필터링
curl -X POST "http://localhost:8080/api/massive/v3/campaigns?limit=20&offset=0" \
  -H "X-API-KEY: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "status": ["RESERVED", "PAUSED"]
  }'

# 복합 검색
curl -X POST "http://localhost:8080/api/massive/v3/campaigns?limit=50&offset=0" \
  -H "X-API-KEY: your-api-key-here" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "캠페인",
    "status": ["COMPLETED"],
    "created_at_start": "2024-01-01",
    "created_at_end": "2024-12-31"
  }'
```

### JavaScript (Fetch API)

```javascript
const searchCampaigns = async () => {
  const response = await fetch('http://localhost:8080/api/massive/v3/campaigns?limit=10&offset=0', {
    method: 'POST',
    headers: {
      'X-API-KEY': 'your-api-key-here',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      status: ['RESERVED', 'PAUSED'],
      created_at_start: '2024-01-01',
      created_at_end: '2024-12-31'
    })
  });

  if (!response.ok) {
    const error = await response.json();
    console.error('Error:', error);
    return;
  }

  const data = await response.json();
  console.log('Total campaigns:', data.total);
  console.log('Campaigns:', data.campaigns);
};

searchCampaigns();
```

### Python (requests)

```python
import requests

url = "http://localhost:8080/api/massive/v3/campaigns"
headers = {
    "X-API-KEY": "your-api-key-here",
    "Content-Type": "application/json"
}
params = {
    "limit": 10,
    "offset": 0
}
payload = {
    "status": ["RESERVED", "PAUSED"],
    "created_at_start": "2024-01-01",
    "created_at_end": "2024-12-31"
}

response = requests.post(url, headers=headers, params=params, json=payload)

if response.status_code == 200:
    data = response.json()
    print(f"Total campaigns: {data['total']}")
    print(f"Campaigns: {data['campaigns']}")
else:
    error = response.json()
    print(f"Error: {error['message']}")
```

---

## 참고사항

### 검색 기간 기본값

검색 조건에서 `created_at_start`를 지정하지 않으면 **최근 1개월** 데이터를 기본으로 조회합니다.

### 페이징

- `offset`과 `limit`을 사용하여 페이징을 구현할 수 있습니다
- 예시:
  - 1페이지: `offset=0, limit=10`
  - 2페이지: `offset=10, limit=10`
  - 3페이지: `offset=20, limit=10`

### 성능 고려사항

- `limit`의 최대값은 1000입니다
- 대량의 데이터를 조회할 때는 적절한 `limit` 값을 사용하여 페이징 처리를 권장합니다
- 검색 조건을 구체적으로 지정하면 더 빠른 응답을 받을 수 있습니다

---

## 버전 정보

- **API Version:** v3
- **Last Updated:** 2024-12-12
- **Base URL:** `/api/massive/v3/campaigns`
