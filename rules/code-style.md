# Code Style Rules

## General
- PSR-4 autoloading (App\ namespace maps to `app/`)
- PSR-12 coding style (Laravel defaults)
- Run `php artisan pint` before commits
- No `dd()` or `var_dump()` in committed code
- Use `try-catch` for external API calls and file operations

## PHP / Laravel
- Controllers are single-word: `AdminController`, `UserController`
- Models use `HasFactory` trait and `$fillable` property
- Route names match controller method names when possible
- Use `Route::middleware()` groups for role-based routing
- Use named routes (`->name('route.name')`) for all routes
- Model relationships: use full class paths in return types `: BelongsTo`

## Blade Templates
- No PHP tags (`<?php`) in Blade files (use `@php` directive if needed)
- Use `{{ }}` for escaped output, `{!! !!}` only for trusted HTML
- Use `@if`, `@foreach`, `@section`, `@extends`, `@include` directives
- Keep logic in controllers, not in views

## Database
- Table names: lowercase, snake_case, plural (`class_routines`, `student_attendance`)
- Model names: singular PascalCase matching table convention
- Foreign keys: `{table}_id` (e.g. `branch_id`, `class_level_id`)
- Timestamps on all tables (`created_at`, `updated_at`)
- Use migrations for schema changes, not raw SQL

## Naming Conventions
- `$fillable` arrays: snake_case column names
- Methods: camelCase (`getAuthIdentifierName`, `profileupdateAvatar`)
- Properties: camelCase (`$fillable`, `$table`, `$primaryKey`)
- Config: snake_case keys in `config/` files
- Environment variables: UPPER_SNAKE_CASE in `.env`

## Legacy Code Notes
- Some models have duplicated code blocks (commented-out old versions). Clean up when modifying.
- Migration `2025_03_20_144546` has a `Schema::create` inside `Schema::table` bug. Fix if migration fails.
- `spatie/laravel-permission` package exists but `RoleMiddleware` is used instead. Standardize on one approach.
