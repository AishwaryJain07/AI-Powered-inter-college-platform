# 📚 ResourceHub – Scalable Document Management System

🔗 **Live Demo:** https://your-live-link.com  
📄 **Type:** Full-Stack Backend System (Django + Cloud + APIs)

---

## 🚀 Overview

ResourceHub is a **scalable document management platform** designed for colleges to upload, organize, and access academic resources efficiently.

It supports:
- Multi-college architecture
- Structured document categorization (parent/sub cards)
- Secure authentication with OTP + OAuth
- Cloud-based file storage
- Clean modular backend design

> Designed with production-ready architecture, focusing on scalability, security, and maintainability.

---

## ✨ Key Features

- 🔐 **Authentication System**
  - Email OTP verification
  - Google OAuth login
- 📂 **Document Management**
  - Upload, categorize, and fetch PDFs
  - Parent/Sub-card structured organization
- 🏫 **Multi-College Support**
  - Data isolation per college
- ☁️ **Cloud Storage Integration**
  - Cloudinary for PDF storage
- ⚡ **Scalable Backend**
  - Modular Django apps (accounts, documents, dashboard)
- 🛡 **Security**
  - Middleware-based protection
  - Secure OTP handling

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
