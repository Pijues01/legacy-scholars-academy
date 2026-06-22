# LSA (Laravel Student Academy) - Project Tracker

## Overview
A school management system built with Laravel 10. Manages students, teachers, parents, branches, class routines, attendance, notices, feedback, holidays, leave applications, learning materials, and job applications.

---

## Tech Stack
- **Backend:** Laravel 10 (PHP 8.1+)
- **Frontend:** Blade templates, vanilla JS, Bootstrap
- **Auth:** Laravel Sanctum (API tokens) + custom session-based auth using `unique_id`
- **Packages:** barryvdh/laravel-dompdf (PDF), intervention/image (image mgmt), spatie/laravel-permission (installed but unused), guzzlehttp/guzzle
- **Database:** MySQL (via XAMPP)

---

## Auth System
- Authentication uses `unique_id` (not email) via `getAuthIdentifierName()`
- Roles: `admin`, `teacher`, `student`, `parent` (enum on `users` table)
- Login: POST `/login` | Logout: GET `/logout`
- `RedirectIfAuthenticated` middleware redirects logged-in users to their role dashboard
- `RoleMiddleware` checks `Auth::user()->role == $role`

---

## Database Models & Schemas

### Users (`users`)
| Column | Type | Notes |
|--------|------|-------|
| id | bigint PK | |
| name | string | |
| unique_id | string | used for login |
| email | string unique | |
| password | hashed | |
| role | enum(student,teacher,parent,admin) | |
| remember_token | string nullable | |
| timestamps | | |

Relations: `studentProfile()`, `teacherProfile()`, `parentProfile()` (hasOne via unique_id)

### Student (`student`)
| Column | Type |
|--------|------|
| id | bigint PK |
| stu_id | string (FK -> users.unique_id) |
| name | string |
| image | string nullable |
| class | string (deprecated?) |
| school_name | string nullable |
| ph_no | string nullable |
| email | string nullable |
| address | text nullable |
| medium | string nullable |
| branch_id | bigint FK nullable |
| status | string nullable |

Relations: `classLevel()`, `branch()`, `subjects()` (belongsToMany via student_subject pivot), `class_level()`, `user()`

### Teacher (`teacher`)
| Column | Type |
|--------|------|
| id | bigint PK |
| teacher_id | string (FK -> users.unique_id) |
| name | string |
| image | string nullable |
| subject | bigint FK nullable |
| ph_no | string nullable |
| email | string nullable |
| address | text nullable |

Relations: `subject()`, `user()`

### Parents (`parent`)
| Column | Type |
|--------|------|
| id | bigint PK |
| parent_id | string (FK -> users.unique_id) |
| name | string |
| image | string nullable |
| ph_no | string nullable |
| email | string nullable |
| address | text nullable |
| stu_id | string nullable |

Relations: `user()`

### Branch (`branches`)
| Column | Type |
|--------|------|
| id | bigint PK |
| branch_name | string |
| location | string |
| description | text nullable |
| working_hours | string nullable |
| contact | string nullable |
| email | string nullable |
| images | json nullable (cast as array) |
| timestamps | |

Relations: `classRoutines()`, `todayClasses()`

### ClassLevels (`class_levels`)
| Column | Type |
|--------|------|
| id | bigint PK |
| name | string |
| description | text nullable |

Relations: `classRoutines()`, `routines()`, `students()`

### Subject (`subject`)
| Column | Type |
|--------|------|
| id | bigint PK |
| sub_name | string |

### ClassRoutine (`class_routines`)
| Column | Type |
|--------|------|
| id | bigint PK |
| branch_id | bigint FK |
| class_level_id | bigint FK |
| day_of_week | string |
| start_time | time |
| end_time | time |
| subject_id | bigint FK |
| teacher_id | string FK |

Relations: `branch()`, `classLevel()`, `subject()`, `teacher()`, `class_levels()`

### Attendance (`student_attendance`)
| Column | Type |
|--------|------|
| id | bigint PK |
| student_id | string FK |
| class_routine_id | bigint FK |
| date | date |
| status | string(present/absent/late) |
| remarks | text nullable |

Relations: `classRoutine()`, `student()`

### TeacherAttendance (`teacher_attendances`)
| Column | Type |
|--------|------|
| id | bigint PK |
| teacher_id | bigint FK |
| class_routine_id | bigint FK |
| date | date |
| status | string |
| remarks | text nullable |

Relations: `teacher()`, `classRoutine()`

### Notices (`notices`)
| Column | Type |
|--------|------|
| id | bigint PK |
| title | string |
| shortdescription | text nullable |
| description | text nullable |
| audience | json (cast as array: teacher,student,parent) |
| attachment | string nullable |
| timestamps | |

Accessor: `getAudienceDisplayAttribute()` - ucfirst(implode(', ', audience))

### Feedback (`feedbacks`)
| Column | Type |
|--------|------|
| id | bigint PK |
| type | string(complaint/suggestion/appreciation) |
| role_type | string(teacher/student/parent/admin) |
| user_id | string FK (users.unique_id) |
| branch_id | bigint FK nullable |
| title | string |
| description | text |
| status | string(pending/resolved) default:pending |
| admin_notes | text nullable |

Relations: `user()`, `branch()`

### GalleryImage (`gallery_images`)
| Column | Type |
|--------|------|
| id | bigint PK |
| image_path | string |
| is_featured | boolean default:false |
| timestamps | |

### Holiday (`holidays`)
| Column | Type |
|--------|------|
| id | bigint PK |
| title | string |
| date | date (cast as date) |
| description | text nullable |
| is_recurring | boolean (cast) default:false |
| branch_id | bigint FK nullable |

Relations: `branch()`

### Material (`materials`)
| Column | Type |
|--------|------|
| id | bigint PK |
| teacher_id | bigint FK |
| class_id | bigint FK |
| title | string |
| description | text nullable |
| type | string(file/content) |
| file_path | string nullable |
| content | text nullable |

Relations: `teacher()`, `class()`

### LeaveApplication (`leave_applications`)
| Column | Type |
|--------|------|
| id | bigint PK |
| user_id | bigint FK |
| type | string(sick/casual/emergency) |
| start_date | date |
| end_date | date |
| reason | text |
| status | string(pending/approved/rejected) default:pending |
| admin_comment | text nullable |

Relations: `user()`

### Objection (`objections`)
| Column | Type |
|--------|------|
| id | bigint PK |
| student_id | string FK |
| class_level_id | bigint FK |
| branch_id | bigint FK |
| teacher_id | string FK |
| title | string |
| description | text |
| approved | boolean default:false |

Relations: `student()`, `teacher()`, `classLevel()`, `branch()`

### Application (`applications`) - Job Applications
| Column | Type |
|--------|------|
| id | bigint PK |
| job_id | bigint FK |
| name | string |
| email | string |
| phone | string |
| resume | string(file path) |
| message | text nullable |
| timestamps | |

Relations: `job()`

### Job (`jobs`)
| Column | Type |
|--------|------|
| id | bigint PK |
| title | string |
| location | string |
| type | string(full-time/part-time/contract) |
| degree | string nullable |
| description | text nullable |
| timestamps | |

### Contact (`contact`)
| Column | Type |
|--------|------|
| id | bigint PK |
| name | string |
| email | string |
| phone | string |
| subject | string nullable |
| comment | text |
| timestamps | |

### Consult (`consult`)
| Column | Type |
|--------|------|
| id | bigint PK |
| name | string |
| email | string |
| phone | string |
| subject | string nullable |
| comment | text |
| timestamps | |

---

## Routes by Role

### Public (Website)
| Route | Method | Controller@action |
|-------|--------|-------------------|
| `/` | GET | WebsiteController@home |
| `/gallery` | GET | WebsiteController@gallery |
| `/branches` | GET | WebsiteController@branches |
| `/contact-us` | GET | WebsiteController@contactus |
| `/about-us` | GET | WebsiteController@aboutus |
| `/notification/{id}` | GET | WebsiteController@shownotice |
| `/contact-submit` | POST | WebsiteController@contactSubmit |
| `/consult-submit` | POST | WebsiteController@consultSubmit |
| `/career` | GET | WebsiteController@career |
| `/job-apply` | POST | WebsiteController@jobApply |
| `/login` | GET/POST | AuthController |

### Admin (`/admin/*`, role:admin)
- Dashboard, Notices (CRUD), Members (CRUD - students/teachers/parents)
- Contacts/Consults view, Gallery management
- Branches CRUD, Jobs CRUD, Subjects CRUD
- Applications view, Routine management (CRUD + PDF download)
- Feedback management, Attendance (today's class, calendar, date-based)
- Holidays CRUD, Leave applications (approve/reject)
- Objections management (store, approve)

### Teacher (`/teacher/*`, role:teacher)
- Dashboard, Schedule view, Students by class
- Notices (view), Objections (submit), Feedback (submit + index)
- Holidays view, Leave applications (create)
- Materials CRUD (study materials)

### Student (`/student/*`, role:student)
- Dashboard, Attendance (calendar + date view)
- Notices (view), Feedback (submit + index)
- Class schedule, Holidays view
- Leave applications (create)
- Materials view

### Parent (`/parent/*`, role:parent)
- Dashboard, Children list
- Notices view, Feedback (submit + index)
- Holidays view, Objections view
- Class schedule (by child), Attendance (calendar + date by child)

---

## Controllers

| Controller | Key Methods |
|------------|-------------|
| **AuthController** | showLoginForm, login, logout, profileshow, profileupdate, profileupdateAvatar |
| **AdminController** | view, noticeAddForm/store/noticeShow/noticeedit/updateNotice/noticedestroy, showRegisterForm/memberregister/showMembers/memberUpdate/memberedit/deleteMember, getSubjects/searchStudents, contactView/consultView, admingallery/uploadImage/deleteImage/toggleFeatured, branches/createBranches/storeBranches/branchEdit/branchUpdate/branchDelete, jobs/createJob/storeJob/jobEdit/jobUpdate/jobDelete, addsubject/storeSubject/subjects/subjectUpdate/subjectEdit/subjectDelete, ViewApplication/deleteApplication, routineList/createRoutine/storeRoutine/viewRoutine/downloadRoutine/routineEdit/routineUpdate/routineDelete, getTeachersBySubject, feedbackIndex/updateFeedback, todayclass/classStudents/storeAttendance/calendarView/getDateClasses/getClassAttendance, holidayindex/create/store/show/edit/update/destroy, leaveindex/leaveupdate, objections/storeObjection/approveObjection, searchStudents1/getStudentDetails/getStudentsByBranchClass |
| **TeacherController** | dashboard, mySchedule, studentbyclass, allnotice/noticeview/allnoticegeneral, objectionstore, feedbackIndex/storeFeedback, holidays, leavecreate/leavestore, materialsindex/create/store/edit/update/show/destroy |
| **StudentController** | view, studentCalendarView/getStudentDateAttendance, studentnotice/noticeview/allnoticegeneral, feedbackIndex/storeFeedback, classschedule, holidays, leavecreate/leavestore |
| **ParentController** | view, parentChildrenList, parentnotice/noticeview/generalnotice, feedbackIndex/storeFeedback, holidays, parentObjections, classschedule, studentCalendarView/getStudentDateAttendance |
| **WebsiteController** | home, gallery, branches, contactus, aboutus, shownotice, contactSubmit, consultSubmit, career, jobApply |

---

## Middleware
- `Authenticate` - default Laravel auth
- `RedirectIfAuthenticated` - redirects to role-specific dashboard if logged in
- `RoleMiddleware` - checks `Auth::user()->role == $role`
- Standard: EncryptCookies, PreventRequestsDuringMaintenance, TrimStrings, TrustHosts, TrustProxies, ValidateSignature, VerifyCsrfToken

---

## Views Structure
```
resources/views/
├── auth/                    # login, register
├── dashboard/               # shared: dash, profile, feedback, holiday, leavecreate
│   ├── admin/               # all admin views + subdirs: holidays/, leave/
│   ├── layout/              # master, sidebar, topbar, footer
│   ├── parent/              # parent-specific views
│   ├── student/             # student-specific + materials/
│   └── teacher/             # teacher-specific + materials/
└── website/                 # public pages + layout/
```

---

## Key Features
1. **Role-based dashboards** (admin, teacher, student, parent)
2. **Notice management** with audience targeting (teacher/student/parent)
3. **Member management** (CRUD for students, teachers, parents)
4. **Class routine management** with PDF download (dompdf)
5. **Attendance tracking** (per class, calendar view, date-based queries)
6. **Feedback system** (complaint/suggestion/appreciation with admin response)
7. **Holiday management** (branch-specific, recurring option)
8. **Leave application** (sick/casual/emergency with admin approval)
9. **Objection management** (student objections to marks/grades)
10. **Study materials** (file uploads and content posts by teachers)
11. **Public website** (home, gallery, branches, about, contact, career/jobs)
12. **Gallery** with featured image toggle
13. **Job postings & applications** (with resume upload)
14. **Contact & consult forms** on public website

---

## Coding Conventions Found
- `HasFactory` used with commented-out instances (legacy cleanup needed)
- Duplicate model code blocks (commented-out old versions retained)
- Inconsistent table naming: `student` vs `student_attendance`, `parent` vs `parents` (model class)
- `public $timestamps = true` explicitly set even though default
- Migration 2025_03_20_144546 has a bug: uses `Schema::create` inside `Schema::table`
- `spatie/laravel-permission` installed but custom `RoleMiddleware` used instead
- Intervention Image 2.7 used for image uploads
- API routes minimal (only Sanctum user endpoint)
