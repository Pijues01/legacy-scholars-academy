# Database Rules

## Naming
- Tables: lowercase, snake_case, plural (`class_routines`, `gallery_images`, `student_attendance`)
- Exceptions: `student`, `teacher`, `parent`, `subject`, `contact`, `consult` (singular)
- Columns: lowercase, snake_case (`branch_name`, `class_level_id`, `is_featured`)
- Primary keys: `id` (bigint, auto-increment)
- Foreign keys: `{referenced_table_singular}_id` (e.g. `branch_id`, `class_level_id`, `teacher_id`)
- Pivot tables: `{table1}_{table2}` in alphabetical order (e.g. `student_subject`)

## Schema Design
- All tables have `id` as primary key (bigint, unsigned, auto-increment)
- All tables should have `timestamps()` (created_at, updated_at)
- Use `$casts` for JSON columns (`audience` in notices, `images` in branches)
- Use `$casts` for boolean columns (`is_featured`, `is_recurring`, `approved`)
- Use `$casts` for date columns (`date` in holidays, `start_date`/`end_date` in leave_applications)

## Eloquent Models
- Every model defines `$table` and `$primaryKey` properties
- Every model defines `$fillable` (mass assignment protection) - never use `$guarded`
- Model names are singular PascalCase, even for singular table names
- Define all relationships explicitly with proper foreign/owner keys
- Use `belongsTo`, `hasMany`, `hasOne` with explicit key params when non-standard

## Existing Schema Notes
- `users.unique_id` is the auth identifier, used as FK in student/teacher/parent tables
- `student.stu_id`, `teacher.teacher_id`, `parent.parent_id` reference `users.unique_id`
- `student.class` column appears to be a string (deprecated?) while `class_level_id` relationship also exists
- `teacher.subject` is a bigint FK to `subject.id` (not a string)
- `class_routines.teacher_id` is a string FK to `teacher.teacher_id` (not `teachers.id`)
- `attendances.student_id` is a string FK to `student.stu_id`

## Migration Conventions
- Timestamp format: `YYYY_MM_DD_HHMMSS`
- Each migration has both `up()` and `down()` methods
- Use `Schema::table()` for modifications, `Schema::create()` for new tables
- Test `down()` before committing migrations
