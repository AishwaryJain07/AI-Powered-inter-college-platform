## 3. Entity Relationship Diagram (ERD)

```mermaid
erDiagram
    auth_user {
        bigint id PK
        varchar email UK
        varchar username
        varchar password
        bool is_staff
    }

    accounts_userprofile {
        bigint id PK
        bigint user_id FK
        bigint college_id FK
    }

    accounts_otpverification {
        bigint id PK
        bigint user_id FK
        varchar otp_hash
        bool is_used
        timestamptz created_at
    }

    documents_college {
        bigint id PK
        varchar name UK
        varchar slug UK
        jsonb parent_card_names
        jsonb subcard_names
        jsonb email_domains
    }

    documents_pdfdocument {
        bigint id PK
        varchar title
        varchar pdf_file
        bigint uploaded_by_id FK
        bigint college_id FK
        varchar parent_card_id
        varchar subcard_id
        timestamptz uploaded_at
    }

    documents_cardsettings {
        bigint id PK
        varchar max_parent_cards
        varchar max_subcards
    }

    auth_user ||--o| accounts_userprofile : "has one profile"
    documents_college ||--o{ accounts_userprofile : "has members"
    auth_user ||--o{ accounts_otpverification : "receives OTPs"
    auth_user ||--o{ documents_pdfdocument : "uploads"
    documents_college ||--o{ documents_pdfdocument : "owns"
