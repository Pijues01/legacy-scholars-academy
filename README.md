# Legacy Scholars Academy (LSA) - School Management System

A comprehensive school management system built with **Laravel 10** designed to streamline academic and administrative operations for educational institutions. LSA provides role-based dashboards for **Admins, Teachers, Students, and Parents** with features spanning attendance tracking, notice management, class scheduling, feedback systems, and more.

---

## Features

### Role-Based Dashboards
| Role | Access |
|------|--------|
| **Admin** | Full system control — members, notices, routines, attendance, feedback, holidays, leaves, objections, gallery, branches, jobs |
| **Teacher** | Schedule view, student lookups, notices, materials management, feedback, leave requests |
| **Student** | Attendance history, notices, class schedule, feedback, leave requests, study materials |
| **Parent** | Children oversight, attendance view, notices, feedback, objections, class schedule |

### Core Modules
- **Member Management** — CRUD for students, teachers, and parents with role-based registration
- **Notice System** — Create notices with audience targeting (teachers/students/parents), PDF download
- **Class Routines** — Weekly schedule management with subject, teacher, branch assignments; PDF export
- **Attendance Tracking** — Per-class attendance with calendar view and date-based queries
- **Feedback System** — Submit complaints, suggestions, or appreciations; admin review and response
- **Holiday Management** — Branch-specific holidays with recurring option
- **Leave Applications** — Sick/casual/emergency leave with admin approval workflow
- **Objection Management** — Students raise objections; teachers and admins review and approve
- **Study Materials** — Teachers upload documents, images, videos; students view online
- **Gallery** — Image management with featured image toggle
- **Job Portal** — Public job listings with online application and resume upload
- **Public Website** — Home, gallery, branches, about, contact, career pages with inquiry forms

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| **Framework** | Laravel 10 |
| **Language** | PHP 8.1+ |
| **Frontend** | Blade templates, Bootstrap 4/5, vanilla JavaScript |
| **Authentication** | Custom session-based auth (unique_id) + Laravel Sanctum |
| **Database** | MySQL (via XAMPP) |
| **Packages** | barryvdh/laravel-dompdf (PDF), intervention/image (image processing), spatie/laravel-permission |
| **Build Tool** | Vite |

---

## Installation

### Prerequisites
- PHP 8.1+
- Composer
- MySQL
- Node.js & npm (for Vite)

### Setup

```bash
# Clone the repository
git clone <repository-url>
cd lsa

# Install PHP dependencies
composer install

# Install & build frontend assets
npm install
npm run build

# Environment configuration
cp .env.example .env
php artisan key:generate

# Configure database in .env, then run:
php artisan migrate

# Start the development server
php artisan serve
```

---

## Authentication

Login uses the **unique_id** field instead of email. Authentication is handled via:
- `AuthController` — Login, logout, profile management
- `RoleMiddleware` — Route protection by role (`admin`, `teacher`, `student`, `parent`)
- `RedirectIfAuthenticated` — Redirects logged-in users to their role-specific dashboard

---

## System Architecture

```
app/
├── Http/
│   ├── Controllers/     # Auth, Admin, Teacher, Student, Parent, Website, User
│   └── Middleware/       # RoleMiddleware, RedirectIfAuthenticated, Auth
├── Models/               # 21 Eloquent models
└── Providers/
config/                   # Application configuration
database/
├── migrations/           # Users, password resets, personal access tokens
└── seeders/
resources/views/
├── dashboard/            # Admin, Teacher, Student, Parent views + layout
├── website/              # Public-facing pages + layout
└── auth/                 # Login, register
routes/
├── web.php               # All web routes (271 lines)
└── api.php               # Sanctum API routes
public/                   # Assets, uploads
storage/                  # Logs, cache, compiled views
```

---

## Database Overview (21 Models)

| Model | Table | Purpose |
|-------|-------|---------|
| User | `users` | Authentication & role assignment |
| Student | `student` | Student profiles linked to users |
| Teacher | `teacher` | Teacher profiles linked to users |
| Parents | `parent` | Parent profiles linked to users |
| Branch | `branches` | Campus/branch locations |
| ClassLevels | `class_levels` | Academic class levels |
| Subject | `subject` | Subjects offered |
| ClassRoutine | `class_routines` | Weekly class schedules |
| Attendance | `student_attendance` | Student attendance records |
| TeacherAttendance | `teacher_attendances` | Teacher attendance records |
| Notices | `notices` | Notifications with audience targeting |
| Feedback | `feedbacks` | Complaints, suggestions, appreciations |
| GalleryImage | `gallery_images` | Public gallery images |
| Holiday | `holidays` | Branch-specific holidays |
| Material | `materials` | Study materials (files/content) |
| LeaveApplication | `leave_applications` | Leave requests and approvals |
| Objection | `objections` | Student grade/mark objections |
| Job | `jobs` | Job openings |
| Application | `applications` | Job applications with resumes |
| Contact | `contact` | Public contact form submissions |
| Consult | `consult` | Admission consultation inquiries |

---

## Development

```bash
# Run Laravel Pint for code style
./vendor/bin/pint

# Run tests
php artisan test

# Compile assets
npm run dev    # Development
npm run build  # Production
```

---

## Project Rules

This project includes a `rules/` directory with convention documentation:

- **design.md** — Layout, naming, forms, responses, assets
- **security.md** — Auth, authorization, CSRF, validation, file uploads
- **code-style.md** — PSR-12, Laravel conventions, naming, legacy cleanup
- **database.md** — Naming, schema design, Eloquent conventions, migrations

---

## License

This project is open-sourced under the MIT license.
