# 캠페인 통계 기본 정보 조회 API

## 기본 정보
- **URL**: `/api/v3/campaigns/{campaign_id}/statistics`
- **Method**: `POST`
- **설명**: 특정 캠페인의 기본 통계 정보(발송 대상자, 성공/실패/오픈/클릭 통수 및 비율)를 조회합니다.

## 요청 파라미터

### Path Parameters

| 파라미터명 | 타입 | 필수 | 설명 | 예시 |
|---------|------|------|------|------|
| campaign_id | Integer | Y | 캠페인 고유 ID | 88 |

### Request Body

| 파라미터명 | 타입 | 필수 | 설명 | 예시 |
|---------|------|------|------|------|
| api_key | String | Y | API 인증 키 | your_api_key_here |

## 응답

### 성공 응답 (200 OK)

```json
{
  "success": true,
  "data": {
    "id": 88,
    "campaign_title": "2024 신년 프로모션 캠페인",
    "state": 7,
    "writer": "홍길동",
    "campaign_type": "manual",
    "regist_date": "2024-01-15 10:30:00",
    "retry_count": 3,
    "engine_name": "Postfix",
    "domain_id": 1,
    "api_url": "http://127.0.0.1:3000",
    "campaign_mail": {
      "title": "2024 신년 프로모션 메일 제목",
      "sender_alias": "마케팅팀",
      "sender": "marketing@example.com"
    },
    "campaign_info": {
      "send_time": "2024-01-20 09:00:00",
      "retry": 5,
      "thread": 10
    },
    "campaign_summary": {
      "send_total": 10000,
      "success_total": 9500,
      "open_total": 4750,
      "click_total": 2375
    },
    "campaign_target_lists": [
      {
        "target": {
          "name": "VIP 고객",
          "rs_count": 5000
        }
      },
      {
        "target": {
          "name": "일반 고객",
          "rs_count": 5000
        }
      }
    ],
    "helo_domain": {
      "helo_domain_api": "smtp.example.com"
    }
  }
}
```

### 필드 설명

**기본 필드**

| 필드명 | 타입 | 설명 |
|-------|------|------|
| id | Integer | 캠페인 고유 ID |
| campaign_title | String | 캠페인 제목 |
| state | Integer | 캠페인 상태 코드 (1: 작성중, 2: 예약됨, 3: 발송중, 7: 완료 등) |
| writer | String | 작성자 이름 |
| campaign_type | String | 캠페인 타입 (manual: 수동발송, api: API발송, db: DB연동) |
| regist_date | String | 작성일시 (YYYY-MM-DD HH:mm:ss) |
| retry_count | Integer | 현재 재발송 횟수 |
| engine_name | String | 발송 엔진 이름 |
| domain_id | Integer | 발송 도메인 ID |
| api_url | String | API-URL |

**campaign_mail 객체**

| 필드명 | 타입 | 설명 |
|-------|------|------|
| title | String | 메일 제목 |
| sender_alias | String | 발신자 이름 |
| sender | String | 발신자 이메일 주소 |

**campaign_info 객체**

| 필드명 | 타입 | 설명 |
|-------|------|------|
| send_time | String | 발송 시작 시간 (YYYY-MM-DD HH:mm:ss) |
| retry | Integer | 최대 재발송 횟수 |
| thread | Integer | 스레드 수 |

**campaign_summary 객체**

| 필드명 | 타입 | 설명 |
|-------|------|------|
| send_total | Integer | 총 발송 대상자 수 |
| success_total | Integer | 성공 건수 |
| open_total | Integer | 오픈 건수 |
| click_total | Integer | 클릭 건수 |

**campaign_target_lists 배열**

| 필드명 | 타입 | 설명 |
|-------|------|------|
| target.name | String | 대상자 그룹명 |
| target.rs_count | Integer | 대상자 수 |

**helo_domain 객체**

| 필드명 | 타입 | 설명 |
|-------|------|------|
| helo_domain_api | String | HELO 도메인 |

### 에러 응답

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

**403 Forbidden** - 권한 없음
```json
{
  "success": false,
  "error": {
    "code": "FORBIDDEN",
    "message": "해당 캠페인에 대한 조회 권한이 없습니다."
  }
}
```

## 요청 예시

```bash
# 캠페인 ID 88번의 통계 정보 조회
POST /api/v3/campaigns/88/statistics
Content-Type: application/json

{
  "api_key": "your_api_key_here"
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

### HTTP 상태 코드

| 상태 코드 | 설명 |
|---------|------|
| 200 | 성공 |
| 400 | 잘못된 요청 |
| 401 | 인증 실패 |
| 403 | 권한 없음 |
| 404 | 리소스를 찾을 수 없음 |
| 500 | 서버 오류 |
