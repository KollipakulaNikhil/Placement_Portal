# EduRev Platform — Database Schema Reference

## Overview

The EduRev backend uses **Supabase** (PostgreSQL) with 5 core tables, 5 custom enums, RLS policies, and a storage bucket.

---

## Enums

| Enum | Values |
|------|--------|
| `app_role` | `student`, `concern-hod`, `school-hod`, `daa`, `admin` |
| `achievement_status` | `draft`, `submitted`, `under_review`, `approved`, `rejected`, `forwarded` |
| `review_action` | `approved`, `rejected`, `forwarded`, `returned` |
| `notification_type` | `approved`, `rejected`, `pending`, `info`, `warning`, `announcement` |
| `notification_priority` | `normal`, `urgent`, `critical` |

---

## Tables

### 1. `profiles`
Extends `auth.users` with application-specific metadata.

| Column | Type | Default | Notes |
|--------|------|---------|-------|
| `id` | uuid (PK, FK → auth.users) | — | Cascade delete |
| `name` | text | — | NOT NULL |
| `registration_number` | text | — | Students only |
| `department` | text | — | e.g. CSE, ECE, MBA |
| `school` | text | — | e.g. School of CSE |
| `section` | text | — | e.g. K22EA |
| `phone` | text | — | |
| `avatar_url` | text | — | |
| `created_at` | timestamptz | `now()` | |
| `updated_at` | timestamptz | `now()` | Auto-updated via trigger |

### 2. `user_roles`
Separate role table to prevent privilege escalation. Uses `has_role()` security definer function.

| Column | Type | Default | Notes |
|--------|------|---------|-------|
| `id` | uuid (PK) | `gen_random_uuid()` | |
| `user_id` | uuid (FK → auth.users) | — | NOT NULL, cascade delete |
| `role` | `app_role` | — | NOT NULL |

**Constraint:** `UNIQUE(user_id, role)`

### 3. `achievements`
Core submissions table — each row is one student achievement.

| Column | Type | Default | Notes |
|--------|------|---------|-------|
| `id` | uuid (PK) | `gen_random_uuid()` | |
| `student_id` | uuid (FK → auth.users) | — | NOT NULL |
| `category` | text | — | NOT NULL (one of 12 categories) |
| `title` | text | — | NOT NULL |
| `description` | text | — | |
| `form_data` | jsonb | `{}` | Category-specific fields |
| `file_urls` | text[] | `{}` | Storage paths |
| `team_members` | jsonb | `[]` | Array of `{name, regNo, role}` |
| `status` | `achievement_status` | `draft` | |
| `current_reviewer_role` | text | — | Which role level it's at |
| `submitted_at` | timestamptz | — | Set on submission |
| `created_at` | timestamptz | `now()` | |
| `updated_at` | timestamptz | `now()` | Auto-updated via trigger |

### 4. `achievement_reviews`
Audit log for the 3-level approval chain.

| Column | Type | Default | Notes |
|--------|------|---------|-------|
| `id` | uuid (PK) | `gen_random_uuid()` | |
| `achievement_id` | uuid (FK → achievements) | — | NOT NULL, cascade delete |
| `reviewer_id` | uuid (FK → auth.users) | — | NOT NULL |
| `reviewer_role` | `app_role` | — | NOT NULL |
| `action` | `review_action` | — | NOT NULL |
| `comments` | text | — | |
| `reviewed_at` | timestamptz | `now()` | |

### 5. `notifications`
Persistent notifications with broadcast support.

| Column | Type | Default | Notes |
|--------|------|---------|-------|
| `id` | uuid (PK) | `gen_random_uuid()` | |
| `sender_id` | uuid (FK → auth.users) | — | NULL for system |
| `recipient_id` | uuid (FK → auth.users) | — | NULL for broadcast |
| `title` | text | — | NOT NULL |
| `message` | text | — | NOT NULL |
| `type` | `notification_type` | `info` | |
| `priority` | `notification_priority` | `normal` | |
| `target_audience` | text | — | all, students, specific-department, etc. |
| `target_details` | text | — | Department name, section, etc. |
| `link` | text | — | Attached URL |
| `read` | boolean | `false` | |
| `created_at` | timestamptz | `now()` | |

---

## Security

### `has_role()` Function
```sql
has_role(_user_id UUID, _role app_role) → BOOLEAN
```
SECURITY DEFINER function that checks `user_roles` without triggering RLS recursion. Used in all RLS policies.

### `get_user_department()` Function
```sql
get_user_department(_user_id UUID) → TEXT
```
Returns the user's department from `profiles`. Used for department-scoped access.

### RLS Policy Summary

| Table | Policy | Access |
|-------|--------|--------|
| `profiles` | Own profile | SELECT own row |
| `profiles` | Admin/DAA | SELECT all |
| `profiles` | HODs | SELECT same department |
| `profiles` | Update own | UPDATE own row |
| `user_roles` | Own roles | SELECT own |
| `user_roles` | Admin manage | ALL for admins |
| `achievements` | Student CRUD | ALL on own rows |
| `achievements` | HOD read | SELECT same department |
| `achievements` | DAA/Admin read | SELECT all |
| `achievements` | Reviewer update | UPDATE for HODs/DAA/Admin |
| `achievement_reviews` | Insert own | INSERT where reviewer_id = self |
| `achievement_reviews` | Read own | SELECT own reviews or own achievements |
| `achievement_reviews` | Admin read | SELECT all |
| `notifications` | Read own | SELECT where recipient = self or broadcast |
| `notifications` | Send | INSERT where sender = self |
| `notifications` | Mark read | UPDATE own |
| `notifications` | Admin manage | ALL for admins |

---

## Approval Workflow

```
Student submits achievement (status: submitted)
        ↓
  Concern HOD reviews
    ├── approved → forwarded to School HOD (status: forwarded)
    └── rejected → returned to student (status: rejected)
        ↓
  School HOD reviews
    ├── approved → forwarded to DAA (status: forwarded)
    └── rejected → returned (status: rejected)
        ↓
  DAA final review
    ├── approved → (status: approved) ✅
    └── rejected → (status: rejected) ❌
```

Each review action creates a row in `achievement_reviews` for full audit trail.

---

## Storage

**Bucket:** `achievement-files` (private)

**File path convention:** `{user_id}/{achievement_id}/{filename}`

| Policy | Access |
|--------|--------|
| Students upload | Own folder only |
| Students read | Own files only |
| Reviewers read | All files (HODs, DAA, Admin) |

---

## Auto-triggers

| Trigger | Table | Action |
|---------|-------|--------|
| `on_auth_user_created` | `auth.users` | Creates profile + assigns `student` role |
| `set_profiles_updated_at` | `profiles` | Updates `updated_at` on change |
| `set_achievements_updated_at` | `achievements` | Updates `updated_at` on change |

---

## Achievement Categories (12)

1. Hackathon
2. Research Paper
3. Patent
4. Workshop/Seminar
5. Certification
6. Sports Achievement
7. Cultural Event
8. Community Service
9. Internship
10. Startup/Entrepreneurship
11. Competition/Contest
12. Other
