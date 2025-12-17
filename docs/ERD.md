# RiderFlow ERD (v1.1)

```mermaid
erDiagram
    AGENCY ||--o{ BRANCH : "owns"
    AGENCY ||--o{ PROFILE : "has_admins"
    BRANCH ||--o{ PROFILE : "has_admins"
    BRANCH ||--o{ RIDER_BRANCH_MAP : "manages"
    RIDER ||--o{ RIDER_BRANCH_MAP : "belongs_to"
    BRANCH ||--o{ DAILY_SETTLEMENT : "records"
    RIDER ||--o{ DAILY_SETTLEMENT : "earns"
    BRANCH ||--o{ MESSAGE_LOG : "triggers"
    RIDER ||--o{ MESSAGE_LOG : "receives"

    AGENCY {
        uuid id PK
        string name
        string business_number
        timestamp created_at
    }

    BRANCH {
        uuid id PK
        uuid agency_id FK
        string name
        bigint target_revenue
        timestamp created_at
    }

    PROFILE {
        uuid id PK "auth.users FK"
        string email
        enum role "root | agency_admin | branch_admin"
        uuid agency_id FK
        uuid branch_id FK
    }

    RIDER {
        uuid id PK
        string name
        string phone
        string external_id "Internal Excel ID"
        timestamp created_at
    }

    RIDER_BRANCH_MAP {
        uuid id PK
        uuid rider_id FK
        uuid branch_id FK
        enum status "working | off | removed"
        timestamp joined_at
    }

    DAILY_SETTLEMENT {
        uuid id PK
        uuid branch_id FK
        uuid rider_id FK
        date work_date
        integer delivery_count
        bigint unit_price
        bigint promotion_fee
        timestamp created_at
    }

    MESSAGE_LOG {
        uuid id PK
        uuid rider_id FK
        uuid branch_id FK
        date settlement_date
        enum status "sent | failed"
        string error_message
        timestamp sent_at
    }
```

## 핵심 관계 설명

1. 조직 계층: `AGENCY`는 여러 `BRANCH`를 가질 수 있으며, 사용자는 `PROFILE`을 통해 특정 본사나 지사에 귀속됩니다.
2. 라이더 관리: `RIDER` 정보는 고유하지만, `RIDER_BRANCH_MAP`을 통해 특정 지사에서의 상태(근무/휴무)를 관리합니다.
3. 정산 구조: `DAILY_SETTLEMENT`는 엑셀 업로드 시 생성되며, 지사와 라이더를 연결하는 핵심 트랜잭션 데이터입니다.
4. 매칭 로직: 엑셀 파싱 시 `RIDER` 테이블의 `name`, `phone`, `external_id` 필드를 사용하여 정확한 기사를 식별합니다.
