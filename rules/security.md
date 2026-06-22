# Security Rules

## Authentication
- Login uses `unique_id`, not email (`getAuthIdentifierName()`)
- Passwords hashed via Laravel's `Hash::make()` / bcrypt
- Session-based auth for web, Sanctum tokens for API
- `RedirectIfAuthenticated` prevents logged-in users from seeing login page

## Authorization
- Use `RoleMiddleware` with `role:{role}` for route protection
- Admin routes prefixed with `/admin`, teacher `/teacher`, student `/student`, parent `/parent`
- Always check user role before accessing role-specific data
- `spatie/laravel-permission` is installed but not used; use custom `RoleMiddleware` instead

## CSRF
- All web routes (except GET) require CSRF token
- Verify `VerifyCsrfToken` middleware is in `web` middleware group
- Exclude external webhook URLs from CSRF if needed (via `$except` in `VerifyCsrfToken`)

## Input Validation
- Validate all input in controller methods using `$request->validate([...])`
- Sanitize file uploads: validate mime types, max file sizes
- Never trust `$request->all()` without validation
- Use Laravel's built-in validation rules (required, string, email, etc.)

## Database
- Use Eloquent ORM (prevents SQL injection)
- Never use raw SQL with concatenated variables
- Mass assignment protection via `$fillable` in all models
- All models define `$fillable` (none use `$guarded`)

## File Uploads
- Store in `public/uploads/` with unique filenames (timestamp + original)
- Validate file types before storage
- Use ` Intervention\Image` for image resizing/optimization
- Delete old files when updating/deleting records

## Session & Cookies
- Default Laravel cookie encryption via `EncryptCookies` middleware
- Session data should not store sensitive info beyond user ID/role
- Use `remember_token` for "remember me" functionality
