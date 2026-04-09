<p align="center">
  <img src="../src/assets/lpu-logo.png" alt="LPU Logo" width="120" />
</p>

<h1 align="center">🎓 CareerAI — System Workflow & Architecture</h1>

<p align="center">
  <b>Comprehensive Placement Intelligence Platform</b><br/>
  <sub>Lovely Professional University • Developed by <strong>ByteForces</strong></sub>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Version-2.0-orange?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Status-Production-brightgreen?style=for-the-badge" />
  <img src="https://img.shields.io/badge/Team-ByteForces-blue?style=for-the-badge" />
</p>

---

## 📌 Table of Contents

- [System Overview](#-system-overview)
- [Role Hierarchy & Access Matrix](#-role-hierarchy--access-matrix)
- [Authentication Flow](#-authentication-flow)
- [Student Workflow](#-student-workflow)
- [Staff (TPC/HOD) Workflow](#-staff-tpchod-workflow)
- [Recruiter Workflow](#-recruiter-workflow)
- [Placement Director (DAA) Workflow](#-placement-director-daa-workflow)
- [Admin Workflow](#-admin-workflow)
- [Notification System](#-notification-system)
- [AI Integration Architecture](#-ai-integration-architecture)
- [Database Schema (ER Diagram)](#-database-schema-er-diagram)
- [Deployment Architecture](#-deployment-architecture)

---

## 🔷 System Overview

CareerAI is an end-to-end AI-powered placement management system designed for Lovely Professional University. It digitizes the entire placement lifecycle — from student profile building and job discovery to recruiter management and institutional analytics.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                          CareerAI — System Overview                         │
│                                                                             │
│   ┌───────────┐   ┌───────────┐   ┌───────────┐   ┌───────────┐           │
│   │  Student   │   │   Staff   │   │ Recruiter │   │   Admin   │           │
│   │  Portal    │   │  (HOD)    │   │  Portal   │   │  Portal   │           │
│   └─────┬─────┘   └─────┬─────┘   └─────┬─────┘   └─────┬─────┘           │
│         │               │               │               │                   │
│   ┌─────┴───────────────┴───────────────┴───────────────┴─────┐             │
│   │              React SPA (Vite + TypeScript)                 │             │
│   │         Role-Based Routing & Protected Routes              │             │
│   └───────────────────────────┬─────────────────────────────┘             │
│                               │                                             │
│   ┌───────────────────────────┴─────────────────────────────┐             │
│   │    Placement   │                                         │             │
│   │    Director    │  ← Full Analytics + AI + Management     │             │
│   │    (DAA)       │                                         │             │
│   └────────────────┴─────────────────────────────────────────┘             │
│                               │                                             │
│   ┌───────────────────────────┴─────────────────────────────┐             │
│   │                  Backend Services                        │             │
│   │  ┌──────┐  ┌──────────┐  ┌─────────┐  ┌─────────────┐  │             │
│   │  │ Auth │  │ Database │  │ Storage │  │Edge Functions│  │             │
│   │  └──────┘  └──────────┘  └─────────┘  └─────────────┘  │             │
│   └─────────────────────────────────────────────────────────┘             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## 👥 Role Hierarchy & Access Matrix

### Role Hierarchy (Top-Down)

```
                    ┌─────────────┐
                    │    Admin     │
                    │ (Superuser) │
                    └──────┬──────┘
                           │
              ┌────────────┼────────────┐
              │                         │
     ┌────────┴────────┐      ┌────────┴────────┐
     │   Placement     │      │   Recruiter     │
     │   Director      │      │   (School HOD)  │
     │   (DAA)         │      │                 │
     └────────┬────────┘      └─────────────────┘
              │
     ┌────────┴────────┐
     │   Staff / TPC   │
     │  (Concern HOD)  │
     └────────┬────────┘
              │
     ┌────────┴────────┐
     │    Student      │
     └─────────────────┘
```

### 📋 Detailed Access Matrix

| Feature / Module | Student | Staff (HOD) | Recruiter | Placement Director | Admin |
|---|:---:|:---:|:---:|:---:|:---:|
| **Dashboard** | ✅ Personal | ✅ Department | ✅ Hiring | ✅ Full Analytics | ✅ Full Analytics |
| **Job Board** | ✅ Browse & Apply | ❌ | ✅ Post & Manage | ✅ View All | ✅ View All |
| **Resume Matcher** | ✅ AI Analysis | ❌ | ❌ | ❌ | ❌ |
| **My Applications** | ✅ Track | ❌ | ✅ Review | ✅ View All | ✅ View All |
| **AI Career Coach** | ✅ Personalized | ❌ | ❌ | ❌ | ❌ |
| **Video Interview** | ✅ Practice | ❌ | ❌ | ❌ | ❌ |
| **At-Risk Students** | ❌ | ✅ Monitor & Alert | ❌ | ✅ View | ✅ View |
| **Referrals** | ❌ | ✅ Manage | ❌ | ✅ View All | ✅ View All |
| **AI Assistant** | ❌ | ✅ Analytics | ❌ | ✅ Full Insights | ✅ Full Insights |
| **Post Jobs** | ❌ | ❌ | ✅ Create & Edit | ❌ | ❌ |
| **Candidate Search** | ❌ | ❌ | ✅ Browse & Filter | ❌ | ❌ |
| **Student Management** | ❌ | ❌ | ❌ | ✅ Full CRUD | ✅ Full CRUD |
| **Staff Management** | ❌ | ❌ | ❌ | ✅ View | ✅ Full CRUD |
| **Advanced Analytics** | ❌ | ❌ | ❌ | ✅ Full Access | ✅ Full Access |
| **Companies** | ❌ | ❌ | ❌ | ✅ Manage | ✅ Manage |
| **PTC Management** | ❌ | ❌ | ❌ | ✅ Full Access | ✅ Full Access |
| **Notifications** | ✅ Receive | ✅ Send & Receive | ✅ Receive | ✅ Broadcast | ✅ Broadcast |
| **Settings** | ❌ | ❌ | ❌ | ❌ | ✅ System Config |
| **Profile** | ✅ Edit Own | ✅ Edit Own | ✅ Edit Own | ✅ Edit Own | ✅ Edit Own |

---

## 🔐 Authentication Flow

```
┌──────────────┐     ┌───────────────┐     ┌──────────────────┐     ┌───────────────┐
│  Login Page  │────▶│  Auth Service │────▶│  Role Detection  │────▶│  Route Guard  │
│  (Email/Pwd) │     │  (JWT Token)  │     │  (user_roles DB) │     │  (Protected)  │
└──────────────┘     └───────────────┘     └──────────────────┘     └───────┬───────┘
                                                                           │
                    ┌──────────────────────────────────────────────────────┘
                    │
        ┌───────────┴───────────┬────────────────┬──────────────────┬────────────────┐
        ▼                       ▼                ▼                  ▼                ▼
  ┌──────────┐          ┌──────────┐      ┌──────────┐      ┌──────────┐     ┌──────────┐
  │ Student  │          │  Staff   │      │Recruiter │      │Placement │     │  Admin   │
  │Dashboard │          │Dashboard │      │Dashboard │      │ Director │     │Dashboard │
  └──────────┘          └──────────┘      └──────────┘      └──────────┘     └──────────┘
```

### Authentication Details

1. **Login**: User submits email + password via the Login page
2. **Token Generation**: Backend validates credentials and issues a JWT token
3. **Role Lookup**: System queries `user_roles` table to determine the user's role (`student`, `concern-hod`, `school-hod`, `daa`, `admin`)
4. **Route Protection**: `ProtectedRoute` component ensures only authenticated users access internal pages
5. **Role Routing**: `RoleDashboard` component automatically redirects users to their role-specific dashboard
6. **Session Persistence**: JWT stored in browser; auto-refresh on expiry

### Quick Login Credentials

| Role | Email | Password |
|------|-------|----------|
| 🎓 Student | `student@lpu.in` | `demo123` |
| 📋 Staff (TPC) | `hod.cse@lpu.in` | `demo123` |
| 🏢 Recruiter | `recruiter@company.com` | `demo123` |
| 🎯 Placement Director | `daa@lpu.in` | `demo123` |
| ⚙️ Admin | `admin@lpu.in` | `demo123` |

---

## 🎓 Student Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                        STUDENT JOURNEY                              │
│                                                                     │
│  ┌──────────┐    ┌──────────────┐    ┌───────────────┐             │
│  │  Login   │───▶│  Dashboard   │───▶│  Build Profile│             │
│  │          │    │  (Overview)  │    │  (Skills/CGPA)│             │
│  └──────────┘    └──────┬───────┘    └───────────────┘             │
│                         │                                           │
│         ┌───────────────┼───────────────────────┐                   │
│         ▼               ▼                       ▼                   │
│  ┌──────────────┐ ┌──────────────┐  ┌──────────────────┐          │
│  │  Job Board   │ │ AI Career   │  │  Resume Matcher  │          │
│  │  Browse &    │ │ Coach       │  │  (JD Analysis)   │          │
│  │  Apply       │ │ (Guidance)  │  │                  │          │
│  └──────┬───────┘ └──────────────┘  └──────────────────┘          │
│         │                                                           │
│         ▼                                                           │
│  ┌──────────────┐    ┌──────────────────┐                          │
│  │    My        │───▶│  Application     │                          │
│  │ Applications │    │  Tracking        │                          │
│  └──────────────┘    └──────────────────┘                          │
│                                                                     │
│  ┌──────────────────────────────────────┐                          │
│  │  Video Interview Practice (AI Mock) │                          │
│  └──────────────────────────────────────┘                          │
└─────────────────────────────────────────────────────────────────────┘
```

### Student Features Breakdown

| Module | Description | AI-Powered? |
|--------|-------------|:-----------:|
| **Dashboard** | Stats overview — placement readiness, upcoming drives, recent apps | ✅ |
| **Job Board** | Browse active job postings, filter by branch/CGPA/skills, apply directly | ❌ |
| **Resume Matcher** | Upload resume, match against JD, get improvement suggestions | ✅ |
| **My Applications** | Track all submitted applications with real-time status | ❌ |
| **AI Career Coach** | Personalized career guidance, skill gap analysis, goal setting | ✅ |
| **Video Interview** | AI-powered mock interview with real-time feedback | ✅ |
| **Profile** | Complete academic profile — CGPA, skills, certifications, links | ❌ |

---

## 📋 Staff (TPC/HOD) Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                    STAFF (TPC / HOD) WORKFLOW                       │
│                                                                     │
│  ┌──────────┐    ┌──────────────────┐                              │
│  │  Login   │───▶│  TPC Dashboard   │                              │
│  │          │    │  (Dept Overview)  │                              │
│  └──────────┘    └──────┬───────────┘                              │
│                         │                                           │
│         ┌───────────────┼───────────────────┐                       │
│         ▼               ▼                   ▼                       │
│  ┌──────────────┐ ┌──────────────┐  ┌──────────────┐              │
│  │  At-Risk     │ │  Referrals   │  │ Notifications│              │
│  │  Students    │ │  Management  │  │ (Broadcast)  │              │
│  │  (<50% Prof) │ │              │  │              │              │
│  └──────┬───────┘ └──────────────┘  └──────────────┘              │
│         │                                                           │
│         ▼                                                           │
│  ┌────────────────────────────────┐                                │
│  │  Send Reminder / Notification │                                │
│  │  to incomplete-profile students│                                │
│  └────────────────────────────────┘                                │
│                                                                     │
│  ┌──────────────────────────────────────┐                          │
│  │  AI Assistant (Dept Analytics Chat) │                          │
│  └──────────────────────────────────────┘                          │
└─────────────────────────────────────────────────────────────────────┘
```

### Staff Capabilities

| Module | Description |
|--------|-------------|
| **TPC Dashboard** | Department-level placement stats, charts, trends |
| **At-Risk Students** | Students with profile completion <50%, with action buttons to send reminders |
| **Referrals** | Manage faculty referrals for students to specific job postings |
| **Notifications** | Send targeted notifications to students in their department |
| **AI Assistant** | Chat-based analytics — ask questions about department data |

---

## 🏢 Recruiter Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                      RECRUITER WORKFLOW                              │
│                                                                     │
│  ┌──────────┐    ┌──────────────────┐                              │
│  │  Login   │───▶│  Recruiter       │                              │
│  │          │    │  Dashboard       │                              │
│  └──────────┘    └──────┬───────────┘                              │
│                         │                                           │
│         ┌───────────────┼───────────────────┐                       │
│         ▼               ▼                   ▼                       │
│  ┌──────────────┐ ┌──────────────┐  ┌──────────────┐              │
│  │  Post Job    │ │  Review      │  │  Search      │              │
│  │  Openings    │ │  Applications│  │  Candidates  │              │
│  └──────┬───────┘ └──────┬───────┘  └──────────────┘              │
│         │               │                                           │
│         ▼               ▼                                           │
│  ┌──────────────────────────────────┐                              │
│  │  Manage Hiring Pipeline         │                              │
│  │  (Shortlist → Interview → Offer)│                              │
│  └──────────────────────────────────┘                              │
└─────────────────────────────────────────────────────────────────────┘
```

### Recruiter Capabilities

| Module | Description |
|--------|-------------|
| **Dashboard** | Overview of posted jobs, application counts, hiring status |
| **Post Job** | Create job postings with eligibility criteria (CGPA, branch, skills) |
| **Applications** | Review student applications, shortlist, reject |
| **Candidates** | Browse and filter the student database by skills, CGPA, branch |
| **Referrals** | View faculty-referred candidates |
| **Notifications** | Receive system notifications about new applications |

---

## 🎯 Placement Director (DAA) Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                   PLACEMENT DIRECTOR (DAA) WORKFLOW                  │
│                                                                     │
│  ┌──────────┐    ┌──────────────────┐                              │
│  │  Login   │───▶│  Command Center  │                              │
│  │          │    │  (Full Analytics)│                              │
│  └──────────┘    └──────┬───────────┘                              │
│                         │                                           │
│    ┌────────┬───────────┼────────────┬───────────┬──────────┐      │
│    ▼        ▼           ▼            ▼           ▼          ▼      │
│ ┌──────┐┌──────┐ ┌──────────┐ ┌──────────┐┌──────────┐┌────────┐ │
│ │Studs ││Staff │ │Analytics │ │Companies ││Referrals ││  PTC   │ │
│ │Mgmt  ││View  │ │Dashboard │ │  Mgmt    ││  View    ││  Mgmt  │ │
│ └──────┘└──────┘ └──────────┘ └──────────┘└──────────┘└────────┘ │
│                                                                     │
│  ┌──────────────────────────────────────┐                          │
│  │  AI Insights (Full Analytics Chat)  │                          │
│  └──────────────────────────────────────┘                          │
│                                                                     │
│  ┌──────────────────────────────────────┐                          │
│  │  Broadcast Notifications            │                          │
│  └──────────────────────────────────────┘                          │
└─────────────────────────────────────────────────────────────────────┘
```

### DAA vs Admin — Key Difference

| Capability | DAA | Admin |
|-----------|:---:|:-----:|
| Dashboard & Analytics | ✅ | ✅ |
| Student Management | ✅ | ✅ |
| Staff View | ✅ | ✅ |
| Companies | ✅ | ✅ |
| PTC | ✅ | ✅ |
| AI Insights | ✅ | ✅ |
| Referrals | ✅ | ✅ |
| Notifications | ✅ | ✅ |
| **System Settings** | ❌ | ✅ |

---

## ⚙️ Admin Workflow

```
┌─────────────────────────────────────────────────────────────────────┐
│                        ADMIN WORKFLOW                                │
│                                                                     │
│  ┌──────────┐    ┌──────────────────┐                              │
│  │  Login   │───▶│  Admin Command   │                              │
│  │          │    │  Center          │                              │
│  └──────────┘    └──────┬───────────┘                              │
│                         │                                           │
│  ┌──────────┬───────────┼────────────┬──────────┬──────────┐       │
│  ▼          ▼           ▼            ▼          ▼          ▼       │
│┌──────┐ ┌──────┐ ┌──────────┐ ┌──────────┐ ┌──────┐ ┌────────┐   │
││Studs │ │Staff │ │Analytics │ │Companies │ │ PTC  │ │Settings│   │
││CRUD  │ │CRUD  │ │Full Dash │ │  Manage  │ │ Mgmt │ │ Config │   │
│└──────┘ └──────┘ └──────────┘ └──────────┘ └──────┘ └────────┘   │
│                                                                     │
│  ┌──────────────────────────────────────┐                          │
│  │  AI Insights (Institution-wide)     │                          │
│  └──────────────────────────────────────┘                          │
│                                                                     │
│  ┌──────────────────────────────────────┐                          │
│  │  Referrals + Broadcast Notifications│                          │
│  └──────────────────────────────────────┘                          │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 🔔 Notification System

```
                    ┌──────────────────────┐
                    │   Notification       │
                    │   Composer           │
                    │   (Admin/HOD/DAA)    │
                    └──────────┬───────────┘
                               │
                    ┌──────────┴───────────┐
                    │   Target Audience    │
                    │   Selection          │
                    └──────────┬───────────┘
                               │
              ┌────────────────┼────────────────┐
              ▼                ▼                ▼
     ┌────────────────┐┌──────────────┐┌──────────────┐
     │  All Students  ││  Department  ││  Individual  │
     │  Broadcast     ││  Targeted    ││  Direct      │
     └────────┬───────┘└──────┬───────┘└──────┬───────┘
              │               │               │
              └───────────────┼───────────────┘
                              ▼
                    ┌──────────────────────┐
                    │  notifications       │
                    │  table (DB)          │
                    │                      │
                    │  priority: normal /  │
                    │  urgent / critical   │
                    │                      │
                    │  type: info /        │
                    │  warning /           │
                    │  announcement        │
                    └──────────┬───────────┘
                               │
                    ┌──────────┴───────────┐
                    │  Real-time Delivery  │
                    │  (Bell Icon Badge)   │
                    └──────────────────────┘
```

### Notification Types & Priority

| Type | Priority | Use Case |
|------|----------|----------|
| `info` | Normal | General updates, new features |
| `announcement` | Normal/Urgent | Drive announcements, deadlines |
| `warning` | Urgent | Profile incomplete, deadline approaching |
| `approved` | Normal | Application approved |
| `rejected` | Normal | Application rejected |
| `pending` | Normal | New application submitted |

---

## 🤖 AI Integration Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                     AI SERVICE ARCHITECTURE                         │
│                                                                     │
│  ┌─────────────────────────────────────────────────────────┐       │
│  │                    Frontend (React)                      │       │
│  │                                                         │       │
│  │  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐  │       │
│  │  │AI Career │ │ Resume   │ │  Mock    │ │Analytics │  │       │
│  │  │Coach     │ │ Matcher  │ │Interview │ │Assistant │  │       │
│  │  └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘  │       │
│  │       │             │            │             │        │       │
│  │  ┌────┴─────────────┴────────────┴─────────────┴────┐  │       │
│  │  │         SSE Streaming (Server-Sent Events)        │  │       │
│  │  └──────────────────────┬────────────────────────────┘  │       │
│  └─────────────────────────┼───────────────────────────────┘       │
│                             │                                       │
│  ┌─────────────────────────┼───────────────────────────────┐       │
│  │              Edge Functions (Serverless)                 │       │
│  │                         │                                │       │
│  │  ┌─────────────────────┴────────────────────────┐      │       │
│  │  │         Lovable AI Gateway                    │      │       │
│  │  │         (No API Key Required)                 │      │       │
│  │  └─────────────────────┬────────────────────────┘      │       │
│  │                         │                                │       │
│  │  ┌──────────┐ ┌────────┴────────┐ ┌──────────────┐    │       │
│  │  │career-   │ │resume-         │ │mock-          │    │       │
│  │  │coach     │ │analyzer        │ │interview      │    │       │
│  │  └──────────┘ └─────────────────┘ └──────────────┘    │       │
│  │  ┌──────────────────┐ ┌──────────────────┐            │       │
│  │  │analytics-        │ │ai-recommend      │            │       │
│  │  │assistant         │ │                  │            │       │
│  │  └──────────────────┘ └──────────────────┘            │       │
│  └──────────────────────────────────────────────────────────┘       │
└─────────────────────────────────────────────────────────────────────┘
```

### AI Edge Functions

| Function | Purpose | Used By |
|----------|---------|---------|
| `career-coach` | Personalized career guidance, skill analysis | Students |
| `resume-analyzer` | Resume parsing, JD matching, improvement tips | Students |
| `mock-interview` | Generate interview questions, evaluate answers | Students |
| `analytics-assistant` | Chat-based data analytics, department insights | Staff, DAA, Admin |
| `ai-recommend` | AI-powered job/role recommendations | Students |
| `ai-talent-scout` | Identify top candidates for specific roles | Recruiters |

---

## 🗃️ Database Schema (ER Diagram)

```
┌──────────────┐       ┌──────────────────┐       ┌──────────────┐
│   profiles   │       │   user_roles     │       │  auth.users  │
├──────────────┤       ├──────────────────┤       ├──────────────┤
│ id (PK/FK)   │◄─────│ user_id (FK)     │──────▶│ id (PK)      │
│ name         │       │ role (enum)      │       │ email        │
│ department   │       │                  │       │ password     │
│ branch       │       └──────────────────┘       └──────────────┘
│ cgpa         │
│ skills[]     │       ┌──────────────────┐
│ resume_url   │       │   companies      │
│ placement_   │       ├──────────────────┤
│   status     │       │ id (PK)          │
│ ...          │       │ name             │◄──────────────────┐
└──────┬───────┘       │ industry         │                   │
       │               │ package_min/max  │                   │
       │               └────────┬─────────┘                   │
       │                        │                              │
       │               ┌───────┴──────────┐                   │
       │               │  job_postings    │                   │
       │               ├──────────────────┤                   │
       │               │ id (PK)          │                   │
       │               │ company_id (FK)  │───────────────────┘
       │               │ title            │
       │               │ min_cgpa         │
       │               │ eligible_branches│
       │               │ skills_required[]│
       │               │ deadline         │
       │               └────────┬─────────┘
       │                        │
       │               ┌───────┴──────────┐
       │               │  applications    │
       │               ├──────────────────┤
       └──────────────▶│ student_id (FK)  │
                       │ job_posting_id   │
                       │ status           │
                       │ ai_match_score   │
                       │ resume_url       │
                       └──────────────────┘

┌──────────────────┐       ┌──────────────────┐
│  notifications   │       │  mock_interviews │
├──────────────────┤       ├──────────────────┤
│ id (PK)          │       │ id (PK)          │
│ recipient_id     │       │ student_id       │
│ sender_id        │       │ domain           │
│ title            │       │ difficulty       │
│ message          │       │ overall_score    │
│ type (enum)      │       │ ai_feedback      │
│ priority (enum)  │       │ questions        │
│ read             │       │ responses        │
│ target_audience  │       └──────────────────┘
└──────────────────┘

┌──────────────────┐       ┌──────────────────┐       ┌──────────────────┐
│   referrals      │       │   risk_scores    │       │  career_plans    │
├──────────────────┤       ├──────────────────┤       ├──────────────────┤
│ id (PK)          │       │ id (PK)          │       │ id (PK)          │
│ student_id       │       │ student_id       │       │ student_id       │
│ faculty_id       │       │ overall_score    │       │ target_company   │
│ job_posting_id   │       │ category         │       │ plan_data        │
│ status           │       │ factors          │       │ progress_pct     │
│ faculty_remarks  │       └──────────────────┘       └──────────────────┘
│ forwarded_to     │
└──────────────────┘

┌──────────────────┐       ┌──────────────────┐       ┌──────────────────┐
│    workshops     │       │training_attendance│      │ student_skills   │
├──────────────────┤       ├──────────────────┤       ├──────────────────┤
│ id (PK)          │       │ id (PK)          │       │ id (PK)          │
│ title            │       │ student_id       │       │ student_id       │
│ workshop_type    │       │ workshop_id (FK) │       │ skill_name       │
│ department       │       │ attendance_status│       │ proficiency      │
│ instructor       │       │ score            │       │ verified         │
│ scheduled_date   │       └──────────────────┘       └──────────────────┘
└──────────────────┘

┌──────────────────┐       ┌──────────────────┐
│placement_drives  │       │featured_placements│
├──────────────────┤       ├──────────────────┤
│ id (PK)          │       │ id (PK)          │
│ company_id (FK)  │       │ student_id       │
│ drive_date       │       │ company_name     │
│ status           │       │ package_lpa      │
│ offers_count     │       │ photo_url        │
│ rounds           │       └──────────────────┘
└──────────────────┘
```

---

## 🔒 Security Architecture

### Row-Level Security (RLS)

```
┌──────────────────────────────────────────────────────────────┐
│                    RLS POLICY STRUCTURE                       │
│                                                              │
│  ┌────────────┐    ┌──────────────────┐    ┌──────────────┐ │
│  │   Request  │───▶│  has_role()      │───▶│  Allow /     │ │
│  │  (JWT)     │    │  SECURITY DEFINER│    │  Deny        │ │
│  └────────────┘    └──────────────────┘    └──────────────┘ │
│                                                              │
│  Key Policies:                                               │
│  • Students: Can only read/write their own data              │
│  • Staff: Can read department-level data                     │
│  • Admin/DAA: Can read all data                              │
│  • Notifications: Filtered by recipient_id                   │
│  • Applications: Student can CRUD own; Admin can read all    │
└──────────────────────────────────────────────────────────────┘
```

### Security Functions

| Function | Purpose |
|----------|---------|
| `has_role(user_id, role)` | Check if user has a specific role (SECURITY DEFINER) |
| `get_user_department(user_id)` | Get user's department for scoped access |

---

## 🚀 Deployment Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                    DEPLOYMENT STACK                                │
│                                                                    │
│  ┌─────────────────────────────────────────────────────┐         │
│  │                   CDN / Edge Network                 │         │
│  │            (Static Asset Delivery)                   │         │
│  └───────────────────────┬─────────────────────────────┘         │
│                           │                                       │
│  ┌───────────────────────┴─────────────────────────────┐         │
│  │               Frontend Application                   │         │
│  │          React + Vite (Production Build)              │         │
│  │                                                      │         │
│  │  Tech: React 18, TypeScript 5, Tailwind CSS 3        │         │
│  │  UI:   shadcn/ui, Framer Motion, Recharts            │         │
│  │  State: TanStack React Query, React Context           │         │
│  └───────────────────────┬─────────────────────────────┘         │
│                           │                                       │
│  ┌───────────────────────┴─────────────────────────────┐         │
│  │                Backend Services                       │         │
│  │                                                      │         │
│  │  ┌──────────────┐  ┌─────────────┐  ┌────────────┐ │         │
│  │  │ PostgreSQL   │  │  Auth       │  │  Storage   │ │         │
│  │  │ (Database)   │  │  (JWT/RLS)  │  │  (Files)   │ │         │
│  │  └──────────────┘  └─────────────┘  └────────────┘ │         │
│  │                                                      │         │
│  │  ┌──────────────────────────────────────────────┐   │         │
│  │  │        Edge Functions (Serverless)            │   │         │
│  │  │  career-coach | resume-analyzer | mock-int   │   │         │
│  │  │  analytics-assistant | ai-recommend          │   │         │
│  │  │  ai-talent-scout | seed-data | db-proxy      │   │         │
│  │  └──────────────────────────────────────────────┘   │         │
│  └──────────────────────────────────────────────────────┘         │
└──────────────────────────────────────────────────────────────────┘
```

---

## 📊 Complete Navigation Map

### Student Portal
```
/dashboard ─── Home (Stats, Readiness, Upcoming Drives)
/jobs ─── Job Board (Browse, Filter, Apply)
/resume-matcher ─── Resume Matcher (AI JD Analysis)
/my-applications ─── My Applications (Track Status)
/ai-coach ─── AI Career Coach (Personalized Guidance)
/video-interview ─── Video Interview (AI Mock Practice)
/profile ─── Profile (Edit Academic Details)
```

### Staff Portal (TPC / HOD)
```
/concern-hod ─── TPC Dashboard (Dept Overview)
/at-risk ─── At-Risk Students (<50% Profile)
/referrals ─── Referrals Management
/notifications ─── Notifications (Send/Receive)
/hod-ai ─── AI Assistant (Dept Analytics Chat)
```

### Recruiter Portal
```
/school-hod ─── Recruiter Dashboard
/recruiter/candidates ─── Candidate Search
/recruiter/post-job ─── Post Job Openings
/recruiter/applications ─── Review Applications
/referrals ─── View Referrals
/notifications ─── Notifications
```

### Placement Director (DAA) Portal
```
/daa ─── Command Center Dashboard
/admin/students ─── Student Management
/admin/staff ─── Staff View
/admin/analytics ─── Advanced Analytics
/companies ─── Company Management
/referrals ─── Referral Oversight
/admin/ptc ─── PTC Management
/admin/ai-insights ─── AI Insights (Full Analytics Chat)
/notifications ─── Broadcast Notifications
```

### Admin Portal
```
/admin ─── Admin Command Center
/admin/students ─── Student CRUD
/admin/staff ─── Staff CRUD
/admin/analytics ─── Full Analytics Dashboard
/companies ─── Company Management
/referrals ─── Referral Management
/admin/ptc ─── PTC Management
/admin/ai-insights ─── AI Insights
/notifications ─── Broadcast Notifications
/admin/settings ─── System Configuration
```

---

## 📈 Tech Stack Summary

| Layer | Technology | Purpose |
|-------|-----------|---------|
| **Frontend** | React 18 + TypeScript 5 | Component-based UI with type safety |
| **Build** | Vite 5 | Lightning-fast dev server & builds |
| **Styling** | Tailwind CSS 3 + shadcn/ui | Utility-first CSS with accessible components |
| **Animations** | Framer Motion | Smooth page transitions & micro-interactions |
| **Charts** | Recharts | Data visualization for analytics dashboards |
| **Routing** | React Router 6 | Client-side SPA navigation |
| **State** | TanStack React Query | Server state caching & sync |
| **Forms** | React Hook Form + Zod | Type-safe form validation |
| **Auth** | JWT + RLS | Secure authentication with row-level security |
| **Database** | PostgreSQL | Relational data with advanced queries |
| **Storage** | Cloud Storage | File uploads (resumes, certificates) |
| **AI** | Edge Functions + LLM | Serverless AI with streaming responses |
| **Real-time** | WebSocket subscriptions | Live notification delivery |

---

<p align="center">
  <b>Developed with ❤️ by ByteForces</b><br/>
  <sub>For Lovely Professional University — Empowering Placements Through Intelligent Technology</sub>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/©_2025-ByteForces-orange?style=flat-square" />
  <img src="https://img.shields.io/badge/LPU-Placement_Cell-blue?style=flat-square" />
</p>
