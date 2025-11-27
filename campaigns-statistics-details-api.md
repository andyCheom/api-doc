# 캠페인 통계 상세 데이터 조회 API

## 기본 정보
- **URL**: `/api/v3/campaigns/{campaign_id}/statistics/details/{type}`
- **Method**: `POST`
- **설명**: 캠페인 통계의 상세 이메일 목록을 조회합니다. (발송 대상자, 성공, 실패, 오픈, 클릭 각각의 상세 목록)

## 요청 파라미터

### Path Parameters

| 파라미터명 | 타입 | 필수 | 설명 | 예시 |
|---------|------|------|------|------|
| campaign_id | Integer | Y | 캠페인 고유 ID | 88 |
| type | String | Y | 조회 타입 (total, success, fail, open, click) | success |

### Request Body

| 파라미터명 | 타입 | 필수 | 설명 | 예시 |
|---------|------|------|------|------|
| api_key | String | Y | API 인증 키 | your_api_key_here |
| page | Integer | N | 페이지 번호 (기본값: 1) | 1 |
| rows | Integer | N | 페이지당 행 수 (기본값: 10) | 20 |

## 응답

### 성공 응답 (200 OK)

```json
{
  "success": true,
  "data": {
    "campaign_results": [
      {
        "email": "user1@example.com",
        "sendmail": {
          "name": "홍길동",
          "mobile": "010-1234-5678",
          "first": "변수1값",
          "second": "변수2값"
        }
      },
      {
        "email": "user2@example.com",
        "sendmail": {
          "name": "김철수",
          "mobile": "010-9876-5432",
          "first": "변수1값",
          "second": "변수2값"
        }
      }
    ],
    "pagination": {
      "page": 1,
      "rows": 10,
      "total_count": 9500,
      "total_pages": 950,
      "start": 1,
      "end": 10,
      "prev": false,
      "next": true
    }
  }
}
```

### 필드 설명

**campaign_results 배열**

| 필드명 | 타입 | 설명 |
|-------|------|------|
| email | String | 수신자 이메일 주소 |
| sendmail | Object | 수신자 상세 정보 |

**sendmail 객체**

| 필드명 | 타입 | 설명 |
|-------|------|------|
| name | String | 수신자 이름 |
| mobile | String | 수신자 휴대폰 번호 |
| first | String | 사용자 정의 변수 1 |
| second | String | 사용자 정의 변수 2 |
| third | String | 사용자 정의 변수 3 |
| fourth | String | 사용자 정의 변수 4 |
| fifth | String | 사용자 정의 변수 5 |
| sixth | String | 사용자 정의 변수 6 |
| seventh | String | 사용자 정의 변수 7 |
| eighth | String | 사용자 정의 변수 8 |
| ninth | String | 사용자 정의 변수 9 |
| tenth | String | 사용자 정의 변수 10 |
| eleventh ~ seventeenth | String | 사용자 정의 변수 11~17 |

**참고**: 사용자 정의 변수는 주소록에 설정된 필드에 따라 표시됩니다.

## type별 데이터 차이

| type | 설명 | 주요 필터 조건 |
|------|------|--------------|
| total | 전체 발송 대상자 | 모든 데이터 |
| success | 발송 성공 | 발송 성공한 이메일만 |
| fail | 발송 실패 | 발송 실패한 이메일만 |
| open | 오픈한 수신자 | 오픈한 이메일만 |
| click | 클릭한 수신자 | 클릭한 이메일만 |

### 에러 응답

**400 Bad Request** - 잘못된 타입
```json
{
  "success": false,
  "error": {
    "code": "INVALID_TYPE",
    "message": "유효하지 않은 조회 타입입니다. (total, success, fail, open, click 중 하나를 선택하세요)"
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

**404 Not Found** - 캠페인을 찾을 수 없음
```json
{
  "success": false,
  "error": {
    "code": "CAMPAIGN_NOT_FOUND",
    "message": "캠페인을 찾을 수 없습니다."
  }
}
```

## 요청 예시

```bash
# 성공한 이메일 목록 조회 (1페이지)
POST /api/v3/campaigns/88/statistics/details/success
Content-Type: application/json

{
  "api_key": "your_api_key_here",
  "page": 1,
  "rows": 20
}

# 실패한 이메일 목록 조회
POST /api/v3/campaigns/88/statistics/details/fail
Content-Type: application/json

{
  "api_key": "your_api_key_here",
  "page": 1
}

# 오픈한 수신자 목록 조회
POST /api/v3/campaigns/88/statistics/details/open
Content-Type: application/json

{
  "api_key": "your_api_key_here",
  "page": 1
}

# 클릭한 수신자 목록 조회
POST /api/v3/campaigns/88/statistics/details/click
Content-Type: application/json

{
  "api_key": "your_api_key_here",
  "page": 1
}

# 전체 발송 대상자 목록 조회
POST /api/v3/campaigns/88/statistics/details/total
Content-Type: application/json

{
  "api_key": "your_api_key_here",
  "page": 1
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
