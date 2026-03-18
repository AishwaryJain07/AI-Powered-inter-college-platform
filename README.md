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
