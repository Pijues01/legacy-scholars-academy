# Design Rules

## Layout & Views
- All views extend `dashboard.layout.master` or `website.layout.master`
- Sidebar, topbar, and footer are partials in `dashboard/layout/`
- Use Bootstrap 4/5 classes for styling
- Profile form uses `POST /profile/avatar` for image uploads
- Keep Blade views free of business logic (use controllers)

## Naming
- Routes: `{role}.{feature}.{action}` (e.g. `admin.noticeaddform`, `student.attendance.calendar`)
- Views: lowercase, kebab-case for multi-word files (`add-member.blade.php`, `attendance-calendar.blade.php`)
- Controllers: PascalCase (`AdminController`, `TeacherController`)
- Models: Singular PascalCase (`Student`, `Notices`, `ClassLevels`)

## Forms
- Use `@csrf` on all POST/PUT/DELETE forms
- Use `@method('PUT')` / `@method('DELETE')` for spoofing
- File upload forms must have `enctype="multipart/form-data"`
- Validate on server side; client-side validation is secondary

## Responses
- Redirect with `->with('success', 'message')` or `->with('error', 'message')` on success/failure
- Use `back()` for form validation failures
- Return JSON for AJAX endpoints only

## Assets
- Public assets in `public/` directory
- User uploads stored in `public/uploads/` (images, resumes, materials)
- Use `asset()` helper for URLs
- Use `Storage::disk('public')` for file operations
