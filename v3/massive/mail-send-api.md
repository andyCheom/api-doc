# MassiveV3 API 명세서

## 개요
MassiveV3 API는 주소록 생성과 대량 메일 발송을 하나의 요청으로 처리할 수 있는 통합 API입니다.

## 기본 정보

- **Base URL**: 안내받으신 자동메일 서버의 도메인
- **Endpoint**: `/api/massive/v3/json-send`
- **Method**: `POST`
- **Content-Type**: `application/json`
- **인증 방식**: api_key

---

## 요청 (Request)

### Headers
```
Content-Type: application/json
```

### Request Body Parameters

#### 필수 파라미터

| 파라미터 | 타입 | 설명 | 예시 |
|---------|------|------|------|
| `api_key` | String | API 인증 키 | `"e059a55b2fd0e4da7fa0fc632cd6263ab26f06784dec15bb14476e05b9b0f255"` |

#### 주소록 생성 파라미터 (선택)

`address` 객체가 있으면 새 주소록을 생성한 후 그 주소록으로 메일을 발송합니다.
`address` 객체가 없으면 기존 주소록을 사용하여 메일을 발송합니다.

| 파라미터                  | 타입 | 설명                            | 제약 사항 | 예시                                               |
|-----------------------|------|-------------------------------|----------|--------------------------------------------------|
| `address`             | Object | 주소록 정보                        | - | `{"title": "테스트 주소록", ...}`                      |
| `address.target_id`   | String | 수신 주소록 id                     | - | `3`                                              |
| `address.title`       | String | 주소록 그룹 이름                     | - | `"2025년 1월 뉴스레터 수신자"`                            |
| `address.fields`      | String | 주소록 필드 정의 (콤마 구분)             | - | `"email,name,etc1,etc2.etc3"`                    |
| `address.description` | String |주소록 그룹 설명            | - | `"회원 뉴스레터 발송 대상자입니다."`                           |
| `address.target_list` | Array | 등록할 주소록 정보 목록(이메일, 이름 기타필드 등) | 최대 1000개 | `[{"email": "user@example.com", "name": "홍길동"}]` |
1. 등록할 주소록 그룹이 이미 생성이 되어 있는 경우(주소록 수정) : API 호출 시 등록할 주소록 그룹의 타겟 ID를 기재합니다. 이때, 주소록 필드(fields)와 주소록 그룹 제목(title)은 기입하지 않아도 됩니다.

2. 타겟ID가 별도로 없고, 주소록 그룹을 API 호출로 새로 생성하는 경우 : API 호출 시 새로 등록할 주소록 필드와(fields) 주소록 그룹 제목(title)을 기입하시면 됩니다.(필수)

#### 메일 발송 파라미터

| 파라미터 | 타입 | 필수 여부 | 설명                                                   | 예시                                     |
|---------|------|--------|------------------------------------------------------|----------------------------------------|
| `api_key` | String | 필수 | 인증 api key                                           | `"dfd6fd9fsdfbjdnfkd38fndjfd023"`                           |
| `template_id` | Integer | 필수 | 연동 발송할 메일의 템플릿 key                                   | `1`                                    |
| `subject` | String | 선택 | 메일 제목                                                | `"2025년 1월 뉴스레터"`                      |
| `sender` | String | 선택 | 발신자 이메일 주소                                           | `"newsletter@company.com"`             |
| `sender_name` | String | 선택 | 발신자 이름                                               | `"회사명"`                                |
| `user_info` | Object | 선택 | 사용자 정의 치환 변수(메일 본문 내용 또는 제목에서 [[$변수명]] 형태로 사용할 수 있음) | `{"user_name": "홍길동", "points": "1000"}` |
| `target_id` | String| 선택 | 메일 수신 주소록의 ID  (콤마 구분/ 입력시 템플릿에 등록된 주소록은 무시됨)        | `"123,12"`                      |
| `target_title` | String | 선택 | 주소록 제목 (target_id 대신 사용 가능)                          | `"VIP 고객 리스트"`                         |

- 모든 파라미터는 사전 등록된 템플릿 내용보다 입력된 파라미터의 값을 우선으로 합니다


---

## 요청 예시 (Request Examples)


### 1. 기존 템플릿 주소록으로 발송

```json
{
  "api_key": "your-api-key",
  "template_id": 1,
  "subject": "정기 업데이트 안내",
  "sender": "info@company.com",
  "sender_name": "회사명"
}
```

### 2. 주소록 생성 + 메일 발송

```json
{
  "api_key": "your-api-key",
  "address": {
    "title": "2025년 1월 뉴스레터 수신자",
    "fields": "email,name",
    "target_list": [
      {
        "email": "user1@example.com",
        "name": "홍길동"
      },
      {
        "email": "user2@example.com",
        "name": "김철수"
      }
    ]
  },
  "template_id": 1,
  "subject": "안녕하세요 [[$user_name]]님",
  "sender": "newsletter@company.com",
  "sender_name": "[[$company]]",
  "user_info": {
    "user_name": "홍길동",
    "company": "ABC 회사"
  }
}
```


---

## 응답 (Response)

### Response Body

| 필드 | 타입 | 설명 | 예시 |
|------|------|------|------|
| `result` | Integer | 결과 코드 (1: 성공, -1: 실패, -3: 인증 실패) | `1` |
| `msg` | String | 결과 메시지 | `"성공적으로 발송 하였습니다."` |
| `type` | String | API 타입 | `"massiveV3Json"` |
| `typeAlias` | String | API 타입 별칭 | `"[V3 대량메일 발송 API]"` |

### 성공 응답 예시

```json
{
  "result": 1,
  "msg": "성공적으로 발송 하였습니다.",
  "type": "massiveV3Json",
  "typeAlias": "[V3 대량메일 발송 API]"
}
```

### 메일 발송 실패 응답 예시

```json
{
  "result": -1,
  "msg": "[메일발송 오류] 템플릿 정보가 없습니다.",
  "type": "massiveV3Json",
  "typeAlias": "[V3 대량메일 발송 API]"
}
```


---

## 에러 코드

| 코드 | 설명                         | 예시 메시지 |
|------|----------------------------|------------|
| `1` | 성공(API 호출이 성공이 된 것이지, 발송이 성공 된 것은 아닙니다.)                     | `"성공적으로 발송 하였습니다."` |
| `-1` | 일반 오류 (유효성 검사 실패, 처리 오류 등) | `"올바른 json형식이 아닙니다."` |
| `-3` | API 키 인증 실패                | `"계정 정보가 맞지 않습니다."` |
| `90x` | 기타 오류                      | `"고객센터로 문의해주세요. "` |

---

## 제약 사항

1. **Request Body 크기**: 최대 2MB (2,097,152 bytes)
2. **target_list 개수**: 최대 1,000개
3. **Content-Type**: 반드시 `application/json`이어야 함
4. **주소록 자동 삭제**:
   - `address` 객체로 주소록 생성 후 메일 발송이 실패하면 생성된 주소록은 자동으로 삭제됩니다.


## 참고 사항

- 템플릿은 사전에 등록되어 있어야 합니다.
- `target_title`로 주소록을 조회할 때 동일한 제목의 주소록이 여러 개 있으면 오류가 반환됩니다.
- 실제 발송 결과는 웹의 보고서-발송 목록 메뉴에서 별도의 통계로 확인해야 합니다.
- 해당 api key는 메일 연동 시 꼭 필요한 필수값이며 고객별 고유값으로, 보안에 유의하시기 바랍니다.

