# 📱 Online QR Code Attendance Management System
 
A full-stack web application that automates student attendance using a **3-layer verification system** combining **Face Recognition**, **QR Code Scanning**, and **GPS Location Validation** — eliminating proxy attendance completely.
 
> 🎓 Academic Project | AI-Assisted Development  
> ⚡ Reduces attendance marking from ~5 minutes (manual roll call) to **under 60 seconds per class**
 
---
 
## 📌 Project Overview
 
Traditional attendance systems are slow and prone to proxy marking. This system solves both problems by requiring students to simultaneously verify their **identity (face)**, **session token (QR code)**, and **physical location (GPS)** — all in a single seamless flow. Teachers generate time-limited QR codes, and students can only mark attendance if all three layers pass validation.
 
---
 
## 🚀 Features
 
### 👤 Multi-Role Access
- **Admin**, **Teacher**, and **Student** roles with separate dashboards and permissions
- **SHA-256** secured password authentication
- **Email OTP-based** password recovery
 
### 🧑‍🏫 Teacher Side
- Generate **UUID-based, time-limited QR codes** per class session
- Optional **GPS location lock** — restricts QR scanning to within a defined classroom radius using the **Haversine formula**
- Real-time student attendance tracking during active sessions
 
### 🎓 Student Side
- **Face verification** via `face-api.js` (TensorFlow.js powered)
- **QR code scanning** via `jsQR`
- **GPS location capture** via Geolocation API
- All 3 steps completed in a single seamless session
- **Real-time notifications** when a teacher generates a QR — auto-polls every **15 seconds**
 
### 🛡️ Admin Panel
- Full **CRUD operations** for users (Admin / Teacher / Student) and classes
- **QR session management** — view, monitor, and invalidate sessions
- **Attendance reports** with **CSV export**
- **Google Maps links** for every GPS-verified attendance entry
- Dashboard with system-wide statistics
 
### 🗄️ Database
- **10 relational tables** with foreign key constraints
- **UUID-based session management** for security and uniqueness
- Designed for data integrity across multi-role, multi-session usage
 
---
 
## 🔐 3-Layer Verification Flow
 
```
Student Opens Attendance Page
            │
            ▼
  ┌─────────────────────┐
  │  Layer 1: Face      │  ── face-api.js compares live webcam
  │  Verification       │     feed against stored student photo
  └────────┬────────────┘
           │ ✅ Face Matched
           ▼
  ┌─────────────────────┐
  │  Layer 2: QR Code   │  ── jsQR scans teacher-generated
  │  Scanning           │     UUID time-limited QR code
  └────────┬────────────┘
           │ ✅ Valid & Not Expired
           ▼
  ┌─────────────────────┐
  │  Layer 3: GPS       │  ── Haversine formula checks student
  │  Location Check     │     is within classroom radius
  └────────┬────────────┘
           │ ✅ Within Range
           ▼
   Attendance Marked ✅
   (Stored with timestamp + GPS coords + session ID)
```
 
---
 
## 🛠️ Tech Stack
 
| Layer | Technology |
|-------|------------|
| Backend | Python, Flask REST API |
| Database | MySQL, PyMySQL |
| Frontend | HTML, CSS, JavaScript (AJAX, Fetch API) |
| Face Recognition | face-api.js, TensorFlow.js |
| QR Code | jsQR (scanning), qrcode + Pillow (generation) |
| Location | Geolocation API, Haversine formula |
| Security | SHA-256, UUID, Email OTP |
 
---
 
## 🗃️ Database Schema (10 Tables)
 
```
┌──────────────┐     ┌──────────────┐     ┌──────────────────┐
│    admins    │     │   teachers   │     │    students      │
│──────────────│     │──────────────│     │──────────────────│
│ id (PK)      │     │ id (PK)      │     │ id (PK)          │
│ name         │     │ name         │     │ name             │
│ email        │     │ email        │     │ email            │
│ password     │     │ password     │     │ password         │
│ ...          │     │ ...          │     │ face_image_path  │
└──────────────┘     └──────┬───────┘     └──────┬───────────┘
                            │                    │
                     ┌──────▼───────┐     ┌──────▼───────────┐
                     │   classes    │     │   enrollments    │
                     │──────────────│     │──────────────────│
                     │ id (PK)      │◄────│ class_id (FK)    │
                     │ teacher_id   │     │ student_id (FK)  │
                     │ class_name   │     └──────────────────┘
                     └──────┬───────┘
                            │
                     ┌──────▼───────┐     ┌──────────────────┐
                     │ qr_sessions  │     │   attendance     │
                     │──────────────│     │──────────────────│
                     │ id (PK)      │◄────│ session_id (FK)  │
                     │ uuid_token   │     │ student_id (FK)  │
                     │ class_id(FK) │     │ timestamp        │
                     │ expires_at   │     │ gps_lat          │
                     │ gps_lat/lng  │     │ gps_lng          │
                     │ is_active    │     │ face_verified    │
                     └──────────────┘     └──────────────────┘
```
 
---
 
## 📁 Project Structure
 
```
Online_qr_attendance_system/
│
├── qr_codes/                       # Generated QR code images
│
├── static/
│   ├── css/
│   │   └── style.css
│   ├── img/
│   └── js/
│
├── templates/
│   ├── admin/
│   │   ├── dashboard.html
│   │   └── login.html
│   │
│   ├── auth/
│   │   ├── forgot_password.html
│   │   └── reset_password.html
│   │
│   ├── student/
│   │   ├── dashboard.html
│   │   ├── login.html
│   │   └── register.html
│   │
│   ├── teacher/
│   │   ├── dashboard.html
│   │   ├── login.html
│   │   └── register.html
│   │
│   └── index.html                  # Landing page
│
├── app.py                          # Main Flask application & API routes
├── database_pro.sql                # Full database schema & seed data
├── requirements.txt                # Python dependencies
└── README.md
```
 
---
 
## ⚙️ Setup & Installation
 
### Prerequisites
- Python 3.8+
- MySQL Server / WAMP Server
- pip
 
### Steps
 
1. **Clone the repository**
   ```bash
   git clone https://github.com/your-username/qr-attendance.git
   cd qr-attendance
   ```
 
2. **Install dependencies**
   ```bash
   pip install Flask pymysql qrcode[pil] Pillow
   ```
 
3. **Set up the database**
   - Open **phpMyAdmin** at `http://localhost/phpmyadmin`
   - Create a new database named `attendance_db`
   - Go to **Import** → select `database_pro.sql` → click **Go**
 
4. **Run the application**
   ```bash
   python app.py
   ```
 
5. **Open in browser**
   ```
   http://127.0.0.1:5500/
   ```
 
---
 
## 🔑 Default Login Credentials
 
| Role    | Email                  | Password     |
|---------|------------------------|--------------|
| Admin   | admin@qrpro.com        | admin123     |
| Teacher | teacher@qrpro.com      | teacher123   |
| Student | student@qrpro.com      | student123   |
 
> ⚠️ Change these credentials immediately after first login in a production environment.
 
---
 
## 📸 Screenshots
 
### 🏠 Index Page
<img width="1000" height="800" alt="Index Page" src="https://github.com/user-attachments/assets/b97813ab-9b06-4c36-b804-d45cddbba1d8" />
 
---
 
### 🎓 Student Module
 
<img width="1000" height="800" alt="Student Dashboard" src="https://github.com/user-attachments/assets/e8f78a3c-cf06-4f41-a7fc-550dde5cebee" />
 
<img width="1000" height="800" alt="Student Attendance" src="https://github.com/user-attachments/assets/ed0e4a3b-9d0c-4363-9e47-523f80170f0f" />
 
<img width="1000" height="800" alt="Student QR Scan" src="https://github.com/user-attachments/assets/aa2bf6e4-eb22-4cf5-b61c-6efa5a7badfb" />
 
<img width="1000" height="800" alt="Student View" src="https://github.com/user-attachments/assets/500ec02c-c504-422d-b0e1-b535ef93d31a" />
 
---
 
### 🧑‍🏫 Teacher Module
 
<img width="1000" height="800" alt="Teacher Dashboard" src="https://github.com/user-attachments/assets/74abca41-a04f-47ba-92e8-2a9588c91116" />
 
<img width="1000" height="800" alt="QR Generation" src="https://github.com/user-attachments/assets/e84b8555-0155-4113-b84f-06141b899ba5" />
 
<img width="1000" height="800" alt="Teacher Attendance View" src="https://github.com/user-attachments/assets/0d3ed8fe-98e1-45d8-8a23-02e14a5eb929" />
 
<img width="1000" height="800" alt="Teacher Reports" src="https://github.com/user-attachments/assets/a652ebed-b386-4c32-aee3-774baf7eb8e4" />
 
---
 
### 🛡️ Admin Module
 
<img width="1000" height="800" alt="Admin Dashboard" src="https://github.com/user-attachments/assets/98c56e7a-b876-4070-bcf1-cd92ec1d999f" />
 
<img width="1000" height="800" alt="Admin User Management" src="https://github.com/user-attachments/assets/c69d904f-6e42-4cf2-b922-72ee9d9a4af2" />
 
---
 
## 📊 Key Metrics
 
| Metric | Before (Manual) | After (This System) |
|--------|----------------|---------------------|
| Time per class | ~5 minutes | Under 60 seconds |
| Proxy attendance | Possible | Zero — 3-layer block |
| Record accuracy | Human error prone | Automated & timestamped |
| Report generation | Manual | Instant CSV export |
| Location verification | None | GPS + Haversine radius check |
 
---
 
## 🖥️ Role-Based Access Summary
 
| Feature | Admin | Teacher | Student |
|---------|-------|---------|---------|
| Manage Users | ✅ | ❌ | ❌ |
| Manage Classes | ✅ | ❌ | ❌ |
| Generate QR Code | ❌ | ✅ | ❌ |
| View Attendance | ✅ | ✅ (own classes) | ✅ (own records) |
| Export CSV Report | ✅ | ✅ | ❌ |
| Mark Attendance | ❌ | ❌ | ✅ |
| QR Session Control | ✅ | ✅ | ❌ |
 
---
 
## 🔒 Security Highlights
 
- All passwords hashed with **SHA-256** before storage
- QR codes are **UUID-based and time-limited** — expired tokens are rejected server-side
- **GPS radius enforcement** via Haversine formula prevents scanning from outside the classroom
- **Face verification** via face-api.js prevents identity spoofing
- **Email OTP** ensures only the account owner can reset their password
 
---
 
## 📄 License
 
This project was developed as an academic project with AI-assisted development. All rights reserved.
 
