## **1. Agency (회사)**

한 회사가 여러 지사를 운영하는 구조.

| 컬럼 | 타입 | 설명 |
| --- | --- | --- |
| agency_id (PK) | BIGINT | 회사 고유 ID |
| name | VARCHAR | 회사명 |
| created_at | DATETIME | 생성일 |

---

## **2. Branch (지사) 등록 가능**

회사(Agency)에 속한 지사

| 컬럼 | 타입 | 설명 |
| --- | --- | --- |
| branch_id (PK) | BIGINT | 지사 ID |
| agency_id (FK) | BIGINT | 소속 회사 |
| name | VARCHAR | 지사명 |
| region | VARCHAR | 관할 지역명 |
| created_at | DATETIME | 생성일 |

관계

**Agency 1 : N Branch**

---

## **3. Rider (라이더)**

라이더 계정

| 컬럼 | 타입 | 설명 |
| --- | --- | --- |
| rider_id (PK) | BIGINT |  |
| name | VARCHAR |  |
| phone | VARCHAR |  |
| created_at | DATETIME |  |

---

## **4. RiderBranchMap (라이더-지사 연결)**

라이더는 여러 지사에서 일할 수 있으므로 N:N 관계를 풀기 위한 매핑 테이블

| 컬럼 | 타입 | 설명 |
| --- | --- | --- |
| map_id (PK) | BIGINT |  |
| rider_id (FK) | BIGINT |  |
| branch_id (FK) | BIGINT |  |
| status | ENUM(active, inactive) |  |
| created_at | DATETIME |  |

관계

**Rider N : N Branch**

---

## **5. BranchManager (지사 관리자 계정) 읽기 전용**

지사에서 올라온 데이터를 관리

| 컬럼 | 타입 |
| --- | --- |
| manager_id (PK) | BIGINT |
| branch_id (FK) | BIGINT |
| email | VARCHAR |
| password_hash | VARCHAR |
| name | VARCHAR |
| auth | STRING |
| created_at | DATETIME |

---

## **6. DailySettlement (일일 정산 데이터)**

지사가 업로드한 엑셀 파일을 파싱한 결과

(파일은 저장하지 않고 값만 보관)

| 컬럼 | 타입 | 설명 |
| --- | --- | --- |
| settlement_id (PK) | BIGINT |  |
| branch_id (FK) | BIGINT | 업로드한 지사 |
| rider_id (FK) | BIGINT | 해당 라이더 |
| work_date | DATE | 정산 날짜 |
| delivery_count | INT | 배달 건수 |
| delivery_fee | INT | 배달료(총액) |
| withholding_tax | INT | 원천세 |
| insurance | INT | 고용/산재 보험 |
| hour_insurance | INT | 시간제 보험 |
| deduction | INT | 차감 |
| pre_paid | INT | 선지급 |
| commission | INT | 정산 수수료 |
| subsidy | INT | 지원금 |
| promo1 | INT | 프로모션1 |
| promo2 | INT | 프로모션2 |
| promo3 | INT | 프로모션3 |
| promo4 | INT | 프로모션4 |
| total_pay | INT | 총 지급액 |
| created_at | DATETIME |  |

---

## **7. PayslipGenerated (명세서 생성 기록)**

주간 명세서 이미지 생성 여부 기록

| 컬럼 | 타입 |
| --- | --- |
| payslip_id (PK) | BIGINT |
| branch_id (FK) | BIGINT |
| rider_id (FK) | BIGINT |
| week_start | DATE |
| week_end | DATE |
| image_url | VARCHAR |
| created_at | DATETIME |

---

## **8. MessageLog (카카오 알림톡 발송 기록)**

| 컬럼 | 타입 |
| --- | --- |
| message_id (PK) | BIGINT |
| rider_id (FK) | BIGINT |
| agency_id (FK) | BIGINT |
| payslip_id (FK) | BIGINT |
| message_type | ENUM('payslip') |
| status | ENUM('success','fail') |
| kakao_response_code | VARCHAR |
| image_url | VARCHAR |
| sent_at | DATETIME |

---

## **9. KakaoProfile (옵션)**

지사별 발신 프로필 등록을 대비

| 컬럼 | 타입 |
| --- | --- |
| profile_id (PK) | BIGINT |
| branch_id (FK) | BIGINT |
| sender_key | VARCHAR |
| template_code | VARCHAR |
| created_at | DATETIME |

---

# 🌐 **ERD 관계 요약**

```
Agency 1 ─── N Branch
Branch 1 ─── N BranchManager
Branch N ─── N Rider (via RiderBranchMap)
Branch 1 ─── N DailySettlement
Rider 1 ─── N DailySettlement
DailySettlement → weekly grouped → PayslipGenerated
PayslipGenerated 1 ─── N MessageLog
Branch 1 ─── 1 KakaoProfile (optional)

```
