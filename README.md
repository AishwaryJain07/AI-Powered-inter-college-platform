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


## 4. System Architecture Diagram

```mermaid
flowchart TB

    %% ================= USER =================
    User["🌐 User / Browser"]
    HTTPS["HTTPS"]

    User --> HTTPS

    %% ================= DJANGO CONTAINER =================
    subgraph Django["Django Application (Gunicorn)"]
        direction TB

        Middleware["🛡 Middleware Stack\nSecurity"]
        Router["🔀 Root URL Router\nlogin_system"]

        HTTPS --> Middleware --> Router

        %% -------- Dashboard App --------
        subgraph Dashboard["📊 dashboard app"]
            direction TB
            DashboardView["Views\nLandingPage / UserDash"]
        end

        %% -------- Documents App --------
        subgraph Documents["📁 documents app"]
            direction TB
            DocView["Views\nAddFileView / ViewPDF"]
            DocModels["Models\nCollege / PDFDocument"]
            DocForms["Forms\nPDFUploadForm"]
        end

        %% -------- Accounts App --------
        subgraph Accounts["👤 accounts app"]
            direction TB
            AuthView["Views\nLogin / Signup"]
            AuthModels["Models\nUserProfile / OTP"]
        end

        %% Routing
        Router --> DashboardView
        Router --> DocView
        Router --> AuthView

        %% Internal Flow
        DashboardView --> DocModels
        DocView --> DocForms
        DocView --> DocModels
        AuthView --> AuthModels
    end

    %% ================= DATA + SERVICES =================
    DB[(🗄 NeonDB PostgreSQL)]
    Storage["☁️ Cloudinary\nPDF Storage"]

    Google["🔐 Google OAuth\n(django-allauth)"]
    Gmail["📧 Gmail SMTP\nEmail Delivery"]

    %% Connections
    DocModels -->|read/write metadata| DB
    AuthModels -->|read/write| DB

    DocView -->|upload / fetch PDF| Storage

    AuthView -->|OAuth login| Google
    AuthView -->|send OTP / verify email| Gmail

    %% ================= STYLING =================
    classDef user fill:#e3f2fd,stroke:#1e88e5,color:#000,stroke-width:2px;
    classDef core fill:#fff3e0,stroke:#fb8c00,color:#000,stroke-width:1.5px;
    classDef app fill:#ede7f6,stroke:#5e35b1,color:#000,stroke-width:1.5px;
    classDef db fill:#e8f5e9,stroke:#43a047,color:#000,stroke-width:2px;
    classDef service fill:#fce4ec,stroke:#d81b60,color:#000,stroke-width:1.5px;

    class User,HTTPS user;
    class Middleware,Router core;
    class DashboardView,DocView,DocModels,DocForms,AuthView,AuthModels app;
    class DB db;
    class Storage,Google,Gmail service;
