# 이메일 캠페인 목록 조회 API

## 기본 정보
- **URL**: `/api/v3/campaigns`
- **Method**: `POST`
- **설명**: 이메일 캠페인 목록을 페이지네이션과 다양한 검색 조건으로 조회합니다.

## 요청 파라미터

### Request Body

| 파라미터명 | 타입 | 필수 | 설명                               | 예시                |
|---------|------|------|----------------------------------|-------------------|
| api_key | String | Y | API 인증 키                         | your_api_key_here |
| page | Integer | N | 페이지 번호 (기본값: 1)                  | 1                 |
| rows | Integer | N | 페이지당 행 수 (기본값: 10)               | 20                |
| start_date | String | N | 검색 시작 날짜 (YYYY-MM-DD)            | 2024-01-01        |
| end_date | String | N | 검색 종료 날짜 (YYYY-MM-DD)            | 2024-01-31        |
| campaign_group_id | String | N | 캠페인 그룹 ID                        | 123               |
| like_type | String | N | 검색 타입 (campaign_title, writer)   | campaign_title    |
| like_word | String | N | 검색어                              | 캠페인 제목입니다         |
| type | String | N | 캠페인 연동 방식 필터 (all, web, api, db) | all               |
| state | String | N | 캠페인 상태 필터                        | 7                 |

## 응답

### 성공 응답 (200 OK)

```json
{
  "success": true,
  "data": {
    "campaigns": [
      {
        "id": 88,
        "campaign_title": "2024 신년 프로모션 캠페인",
        "writer": "홍길동",
        "regist_date": "2024-01-15 10:30:00",
        "send_time": "2024-01-20 09:00:00",
        "send_total": 10000,
        "success_total": 9500,
        "state": 7,
        "type": "manual",
        "save_step": "3"
      }
    ],
    "summary": [
      {
        "state": 1,
        "count": 5
      },
      {
        "state": 7,
        "count": 42
      }
    ],
    "pagination": {
      "page": 1,
      "rows": 10,
      "total_count": 150,
      "total_pages": 15,
      "start": 1,
      "end": 10,
      "prev": false,
      "next": true
    }
  }
}
```

### 필드 설명

**campaigns 배열**

| 필드명 | 타입 | 설명                                          |
|-------|------|---------------------------------------------|
| id | Integer | 캠페인 고유 ID                                   |
| campaign_title | String | 캠페인 제목                                      |
| writer | String | 작성자 이름                                      |
| regist_date | String | 작성일시 (YYYY-MM-DD HH:mm:ss)                  |
| send_time | String | 발송일시 (YYYY-MM-DD HH:mm:ss)                  |
| send_total | Integer | 총 발송 건수                                     |
| success_total | Integer | 성공 건수                                       |
| state | Integer | 캠페인 상태 코드 (1: 작성중, 2: 예약됨, 3: 발송중, 7: 완료 등) |
| type | String | 캠페인 연동 방식 (web: 수동발송, api: API발송, db: DB연동) |
| save_step | String | 저장 단계                                       |

**summary 배열**

| 필드명 | 타입 | 설명 |
|-------|------|------|
| state | Integer | 캠페인 상태 코드 |
| count | Integer | 해당 상태의 캠페인 개수 |

**pagination 객체**

| 필드명 | 타입 | 설명 |
|-------|------|------|
| page | Integer | 현재 페이지 번호 |
| rows | Integer | 페이지당 행 수 |
| total_count | Integer | 전체 데이터 개수 |
| total_pages | Integer | 전체 페이지 수 |
| start | Integer | 페이지네이션 시작 번호 |
| end | Integer | 페이지네이션 종료 번호 |
| prev | Boolean | 이전 페이지 존재 여부 |
| next | Boolean | 다음 페이지 존재 여부 |

### 에러 응답

**400 Bad Request** - 잘못된 파라미터
```json
{
  "success": false,
  "error": {
    "code": "INVALID_PARAMETER",
    "message": "유효하지 않은 날짜 형식입니다."
  }
}
```

**401 Unauthorized** - 인증 실패
```json
{
  "success": false,
  "error": {
    "code": "INVALID_API_KEY",
    "message": "유효하지 않은 API 키입니다."
  }
}
```

**403 Forbidden** - 권한 없음
```json
{
  "success": false,
  "error": {
    "code": "FORBIDDEN",
    "message": "접근 권한이 없습니다."
  }
}
```

## 요청 예시

```bash
# 기본 조회
POST /api/v3/campaigns
Content-Type: application/json

{
  "api_key": "your_api_key_here",
  "page": 1,
  "rows": 20
}

# 날짜 범위로 조회
POST /api/v3/campaigns
Content-Type: application/json

{
  "api_key": "your_api_key_here",
  "start_date": "2024-01-01",
  "end_date": "2024-01-31"
}

# 제목으로 검색
POST /api/v3/campaigns
Content-Type: application/json

{
  "api_key": "your_api_key_here",
  "like_type": "campaign_title",
  "like_word": "프로모션"
}

# 상태 필터링 (완료된 캠페인만)
POST /api/v3/campaigns
Content-Type: application/json

{
  "api_key": "your_api_key_here",
  "state": "7"
}
```

---

## 공통 사항

### 인증 방식
모든 API는 Request Body에 api_key를 포함하여 인증합니다.

### Content-Type
```http
Content-Type: application/json; charset=UTF-8
```

### 날짜/시간 형식
- 날짜: `YYYY-MM-DD` (예: 2024-01-20)
- 날짜 시간: `YYYY-MM-DD HH:mm:ss` (예: 2024-01-20 09:00:00)

### 페이지네이션 기본값
- page: 1
- rows: 10

### HTTP 상태 코드

| 상태 코드 | 설명 |
|---------|------|
| 200 | 성공 |
| 400 | 잘못된 요청 |
| 401 | 인증 실패 |
| 403 | 권한 없음 |
| 404 | 리소스를 찾을 수 없음 |
| 500 | 서버 오류 |
