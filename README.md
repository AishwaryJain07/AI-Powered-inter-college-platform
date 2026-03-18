# 📚 ResourceHub – Scalable Document Management System

🔗 **Live:** http://meetmycampus.onrender.com/landing/

---

## 🚀 Overview

ResourceHub is a **scalable document management platform** designed for colleges to upload, organize, and access academic resources efficiently.

It supports:

* Multi-college architecture
* Structured document categorization (parent/sub cards)
* Secure authentication with OTP + OAuth
* Cloud-based file storage
* Clean modular backend design

> Designed with production-ready architecture, focusing on scalability, security, and maintainability.

---

## ✨ Key Features

* 🔐 **Authentication System**

  * Email OTP verification
  * Google OAuth login

* 📂 **Document Management**

  * Upload, categorize, and fetch PDFs
  * Parent/Sub-card structured organization

* 🏫 **Multi-College Support**

  * Data isolation per college

* ☁️ **Cloud Storage Integration**

  * Cloudinary for PDF storage

* ⚡ **Scalable Backend**

  * Modular Django apps (accounts, documents, dashboard)

* 🛡 **Security**

  * Middleware-based protection
  * Secure OTP handling

---

## 🏗 System Architecture

```mermaid
flowchart TB

    User["🌐 User / Browser"]
    HTTPS["HTTPS"]

    User --> HTTPS

    subgraph Django["Django Application (Gunicorn)"]
        direction TB

        Middleware["🛡 Middleware Stack\nSecurity"]
        Router["🔀 Root URL Router\nlogin_system"]

        HTTPS --> Middleware --> Router

        subgraph Dashboard["📊 dashboard app"]
            DashboardView["Views\nLandingPage / UserDash"]
        end

        subgraph Documents["📁 documents app"]
            DocView["Views\nAddFileView / ViewPDF"]
            DocModels["Models\nCollege / PDFDocument"]
            DocForms["Forms\nPDFUploadForm"]
        end

        subgraph Accounts["👤 accounts app"]
            AuthView["Views\nLogin / Signup"]
            AuthModels["Models\nUserProfile / OTP"]
        end

        Router --> DashboardView
        Router --> DocView
        Router --> AuthView

        DashboardView --> DocModels
        DocView --> DocForms
        DocView --> DocModels
        AuthView --> AuthModels
    end

    DB[(🗄 NeonDB PostgreSQL)]
    Storage["☁️ Cloudinary\nPDF Storage"]

    Google["🔐 Google OAuth"]
    Gmail["📧 Gmail SMTP"]

    DocModels -->|read/write metadata| DB
    AuthModels -->|read/write| DB

    DocView -->|upload / fetch PDF| Storage

    AuthView -->|OAuth login| Google
    AuthView -->|send OTP| Gmail
```

---

## 🧠 Database Design (ERD)

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

    auth_user ||--o| accounts_userprofile : has_profile
    documents_college ||--o{ accounts_userprofile : has_members
    auth_user ||--o{ accounts_otpverification : receives_otps
    auth_user ||--o{ documents_pdfdocument : uploads
    documents_college ||--o{ documents_pdfdocument : owns
```

---

## ⚙️ Tech Stack

**Backend**

* Django (Modular Architecture)
* REST APIs

**Database**

* PostgreSQL (NeonDB)

**Cloud & Storage**

* Cloudinary

**Authentication**

* OTP-based Email Verification
* Google OAuth (django-allauth)

**Deployment**

* Gunicorn + Cloud Hosting

---

## 🧠 System Design Highlights

* Modular Django architecture (separation of concerns)
* Multi-tenant system (college-based isolation)
* Cloud storage instead of local filesystem
* Secure OTP authentication with hashing
* Extensible document categorization system

---

## 📈 Scalability & Future Improvements

* Add Redis caching
* CDN for faster delivery
* Async processing (Celery)
* Role-based access control (RBAC)
* Full-text search (Elasticsearch)

---

## ⚠️ Note

Though source code is not publicly available.
This repository showcases system design and live implementation.

---

Me: Aishwaryjain.in
