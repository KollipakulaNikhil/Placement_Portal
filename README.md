<p align="center">
  <img src="src/assets/lpu-logo.png" alt="EduRev Logo" width="100" />
</p>

<h1 align="center">🎓 EduRev — Achievement Intelligence Platform</h1>

<p align="center">
  <b>A comprehensive, AI-powered student achievement management and approval workflow system</b>
  <br/>
  <sub>Designed for universities to streamline achievement tracking, multi-level approvals, and intelligent analytics.</sub>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/React-18.3-61DAFB?style=for-the-badge&logo=react&logoColor=white" />
  <img src="https://img.shields.io/badge/TypeScript-5.8-3178C6?style=for-the-badge&logo=typescript&logoColor=white" />
  <img src="https://img.shields.io/badge/Vite-5.4-646CFF?style=for-the-badge&logo=vite&logoColor=white" />
  <img src="https://img.shields.io/badge/Tailwind_CSS-3.4-06B6D4?style=for-the-badge&logo=tailwindcss&logoColor=white" />
  <img src="https://img.shields.io/badge/Supabase-BaaS-3FCF8E?style=for-the-badge&logo=supabase&logoColor=white" />
  <img src="https://img.shields.io/badge/Framer_Motion-Animations-FF0055?style=for-the-badge&logo=framer&logoColor=white" />
</p>

---

## 📌 Table of Contents

- [✨ Overview](#-overview)
- [🏗️ System Architecture](#️-system-architecture)
- [👥 User Roles & Workflows](#-user-roles--workflows)
- [🚀 Key Features](#-key-features)
- [🛠️ Tech Stack](#️-tech-stack)
- [📁 Project Structure](#-project-structure)
- [⚡ Getting Started](#-getting-started)
- [🔐 Demo Credentials](#-demo-credentials)
- [📊 Module Breakdown](#-module-breakdown)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)

---

## ✨ Overview

**EduRev** is a full-featured university achievement management platform that digitizes the entire lifecycle of student achievements — from submission to multi-level approval to analytics. It replaces manual, paper-based processes with a sleek, role-based web application featuring:

- 🎯 **Multi-role dashboards** for Students, HODs, DAA, and Admins
- 🤖 **AI-powered recommendations** and insights
- 📈 **Advanced analytics** with dynamic filtering and visualizations
- ✅ **Multi-level approval workflows** (Student → Concern HOD → School HOD → DAA)
- 🌗 **Dark/Light mode** with a polished, modern UI

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────────┐
│                        🌐 CLIENT (Browser)                         │
│                                                                     │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐           │
│  │ Student  │  │ Concern  │  │  School  │  │   DAA    │           │
│  │Dashboard │  │   HOD    │  │   HOD    │  │Dashboard │           │
│  └────┬─────┘  └────┬─────┘  └────┬─────┘  └────┬─────┘           │
│       │              │              │              │                 │
│  ┌────┴──────────────┴──────────────┴──────────────┴─────┐         │
│  │              🔀 React Router (SPA Navigation)          │         │
│  └────────────────────────┬──────────────────────────────┘         │
│                           │                                         │
│  ┌────────────────────────┴──────────────────────────────┐         │
│  │           🧠 Auth Context (Role-Based Access)          │         │
│  └────────────────────────┬──────────────────────────────┘         │
│                           │                                         │
│  ┌────────────────────────┴──────────────────────────────┐         │
│  │         📦 State Management (React Query + Context)    │         │
│  └───────────────────────────────────────────────────────┘         │
│                                                                     │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐                │
│  │  Recharts   │  │   Framer    │  │  shadcn/ui  │                │
│  │  (Charts)   │  │  Motion     │  │ (Components)│                │
│  └─────────────┘  └─────────────┘  └─────────────┘                │
└─────────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────────┐
│                      ☁️  BACKEND (Supabase)                        │
│                                                                     │
│  ┌──────────┐  ┌──────────────┐  ┌──────────┐  ┌──────────┐      │
│  │   Auth   │  │   Database   │  │ Storage  │  │  Edge    │      │
│  │ (Users)  │  │ (PostgreSQL) │  │ (Files)  │  │Functions │      │
│  └──────────┘  └──────────────┘  └──────────┘  └──────────┘      │
└─────────────────────────────────────────────────────────────────────┘
```

---

## 👥 User Roles & Workflows

### 🔄 Achievement Approval Flow

```
  📝 Student                 🔍 Concern HOD             🏫 School HOD              🎓 DAA
  ─────────                 ──────────────             ──────────────             ─────────
       │                          │                          │                        │
       │  Submit Achievement      │                          │                        │
       ├─────────────────────────▶│                          │                        │
       │                          │  Review & Approve/Reject │                        │
       │                          ├─────────────────────────▶│                        │
       │                          │                          │  Final Review &        │
       │                          │                          │  Approve/Reject        │
       │                          │                          ├───────────────────────▶│
       │                          │                          │                        │
       │◀─────────────── Status Updates & Notifications ────────────────────────────┤
       │                          │                          │                        │
```

| Role | Access Level | Key Responsibilities |
|------|-------------|---------------------|
| 🎒 **Student** | Personal dashboard | Upload achievements, track applications, view AI recommendations |
| 🔬 **Concern HOD** | Department-level | Review submissions, AI-assisted evaluation, approve/reject/forward |
| 🏫 **School HOD** | School-level | Oversee department approvals, batch reviews, analytics |
| 🎓 **DAA** | University-level | Final approval authority, institution-wide analytics |
| ⚙️ **Admin** | System-wide | User management, platform analytics, system configuration |

---

## 🚀 Key Features

### 🎒 Student Portal
- **📤 Upload Achievements** — Submit academic, sports, cultural, and technical achievements with supporting documents
- **📋 My Applications** — Track all submitted achievements and their approval status
- **🔍 Application Tracking** — Real-time status tracking across all approval levels
- **🤖 AI Recommendations** — Personalized suggestions based on achievement patterns and academic profile
- **📊 Dashboard** — Visual overview with stats cards, recent applications, and AI insights

### 🔬 HOD & Reviewer Portal
- **📥 Pending Reviews** — Queue of submissions awaiting approval with priority sorting
- **🤖 AI Assistant** — Smart evaluation assistant for reviewing submissions
- **📝 Review History** — Complete audit trail of all reviewed applications
- **📈 Department Analytics** — Performance metrics and trend analysis

### ⚙️ Admin Portal
- **👥 User Management** — Create, edit, and manage user accounts across all roles
- **📊 Advanced Analytics** — Dynamic, filterable reports with:
  - Multi-select filters (Year, Batch, Department, Category)
  - Interactive charts (Area, Pie, Bar, Line)
  - Department-wise comparisons
  - Reviewer performance metrics
  - Exportable reports (PDF/Excel)

### 🎨 UI/UX Features
- **🌗 Dark/Light Mode** — Seamless theme switching
- **📱 Responsive Design** — Optimized for desktop, tablet, and mobile
- **✨ Smooth Animations** — Framer Motion powered transitions
- **🧩 Component Library** — Built on shadcn/ui for consistent, accessible UI

---

## 🛠️ Tech Stack

| Category | Technology | Purpose |
|----------|-----------|---------|
| **Framework** | React 18 | Component-based UI library |
| **Language** | TypeScript 5.8 | Type-safe development |
| **Build Tool** | Vite 5.4 | Lightning-fast HMR and builds |
| **Styling** | Tailwind CSS 3.4 | Utility-first CSS framework |
| **UI Components** | shadcn/ui + Radix UI | Accessible, customizable components |
| **Animations** | Framer Motion | Declarative animations |
| **Charts** | Recharts | Composable charting library |
| **Routing** | React Router 6 | Client-side navigation |
| **State** | TanStack React Query | Server state management |
| **Forms** | React Hook Form + Zod | Form handling with validation |
| **Backend** | Supabase | Auth, Database, Storage, Edge Functions |
| **Notifications** | Sonner | Toast notifications |
| **Icons** | Lucide React | Beautiful open-source icons |

---

## 📁 Project Structure

```
edurev/
├── 📂 public/
│   ├── favicon.ico              # App favicon
│   ├── favicon.png              # LPU logo favicon
│   └── robots.txt               # SEO robots config
│
├── 📂 src/
│   ├── 📂 assets/               # Static assets (logos, avatars)
│   │   ├── lpu-logo.png
│   │   └── avatar-*.png
│   │
│   ├── 📂 components/
│   │   ├── 📂 dashboard/        # Dashboard-specific components
│   │   │   ├── AIInsightsPanel.tsx
│   │   │   ├── DashboardLayout.tsx
│   │   │   ├── DashboardNavbar.tsx
│   │   │   ├── DashboardSidebar.tsx
│   │   │   ├── RecentApplications.tsx
│   │   │   ├── StatsCards.tsx
│   │   │   ├── StudentInsights.tsx
│   │   │   ├── SubjectSelector.tsx
│   │   │   └── ThemeToggle.tsx
│   │   │
│   │   ├── 📂 ui/               # Reusable UI components (shadcn/ui)
│   │   │   ├── accordion.tsx
│   │   │   ├── button.tsx
│   │   │   ├── card.tsx
│   │   │   ├── dialog.tsx
│   │   │   ├── select.tsx
│   │   │   ├── table.tsx
│   │   │   ├── tabs.tsx
│   │   │   └── ... (40+ components)
│   │   │
│   │   └── NavLink.tsx          # Navigation link component
│   │
│   ├── 📂 contexts/
│   │   └── AuthContext.tsx       # Authentication & role management
│   │
│   ├── 📂 hooks/
│   │   ├── use-mobile.tsx        # Mobile detection hook
│   │   └── use-toast.ts          # Toast notification hook
│   │
│   ├── 📂 integrations/
│   │   └── supabase/             # Supabase client & types
│   │
│   ├── 📂 lib/
│   │   └── utils.ts              # Utility functions
│   │
│   ├── 📂 pages/
│   │   ├── Index.tsx             # Student dashboard
│   │   ├── Login.tsx             # Authentication page
│   │   ├── UploadAchievement.tsx # Achievement submission
│   │   ├── MyApplications.tsx    # Student applications list
│   │   ├── ApplicationTracking.tsx
│   │   ├── AIRecommendation.tsx  # AI-powered suggestions
│   │   ├── ConcernHOD.tsx        # Concern HOD dashboard
│   │   ├── SchoolHOD.tsx         # School HOD dashboard
│   │   ├── DAA.tsx               # DAA dashboard
│   │   ├── HODAIAssistant.tsx    # AI review assistant
│   │   ├── HODReviews.tsx        # Review history
│   │   ├── AdminDashboard.tsx    # Admin overview
│   │   ├── AdminUserManagement.tsx
│   │   ├── AdminAnalytics.tsx    # Advanced analytics
│   │   └── NotFound.tsx          # 404 page
│   │
│   ├── App.tsx                   # Root component with routing
│   ├── main.tsx                  # Application entry point
│   └── index.css                 # Global styles & design tokens
│
├── 📂 supabase/
│   └── config.toml               # Supabase configuration
│
├── index.html                    # HTML entry point
├── vite.config.ts                # Vite configuration
├── tailwind.config.ts            # Tailwind CSS configuration
├── tsconfig.json                 # TypeScript configuration
└── package.json                  # Dependencies & scripts
```

---

## ⚡ Getting Started

### Prerequisites

Ensure you have the following installed:

| Tool | Version | Download |
|------|---------|----------|
| **Node.js** | 18+ (LTS) | [nodejs.org](https://nodejs.org/) |
| **npm** or **bun** | Latest | Comes with Node.js / [bun.sh](https://bun.sh/) |
| **Git** | Latest | [git-scm.com](https://git-scm.com/) |

### 🚀 Installation

```bash
# 1️⃣  Clone the repository
git clone https://github.com/your-username/edurev.git

# 2️⃣  Navigate to the project directory
cd edurev

# 3️⃣  Install dependencies
npm install
# or
bun install

# 4️⃣  Set up environment variables
cp .env.example .env
# Edit .env with your Supabase credentials:
#   VITE_SUPABASE_URL=your_supabase_url
#   VITE_SUPABASE_ANON_KEY=your_supabase_anon_key

# 5️⃣  Start the development server
npm run dev
# or
bun dev
```

### 🌐 Open in Browser

Once the server starts, open your browser and navigate to:

```
http://localhost:5173
```

### 📦 Available Scripts

| Command | Description |
|---------|-------------|
| `npm run dev` | Start development server with hot reload |
| `npm run build` | Build optimized production bundle |
| `npm run preview` | Preview production build locally |
| `npm run lint` | Run ESLint for code quality checks |
| `npm run test` | Run test suite with Vitest |
| `npm run test:watch` | Run tests in watch mode |

---

## 🔐 Demo Credentials

Use these credentials to explore different roles:

| Role | Email | Password |
|------|-------|----------|
| 🎒 Student | `student@lpu.in` | `demo123` |
| 🔬 Concern HOD | `hod@lpu.in` | `demo123` |
| 🏫 School HOD | `schoolhod@lpu.in` | `demo123` |
| 🎓 DAA | `daa@lpu.in` | `demo123` |
| ⚙️ Admin | `admin@lpu.in` | `demo123` |

---

## 📊 Module Breakdown

### 🔑 Authentication Module
- Role-based login with context-aware routing
- Protected routes with automatic redirects
- Session management via React Context

### 📤 Achievement Submission Module
- Multi-category achievement upload (Academic, Sports, Cultural, Technical)
- Document/certificate attachment support
- Form validation with Zod schemas

### ✅ Approval Workflow Module
- Three-tier approval pipeline
- AI-assisted review suggestions
- Bulk review capabilities for HODs
- Complete audit trail

### 📈 Analytics Module
- **Multi-select filters**: Year, Batch, Department, Category
- **Chart types**: Area (trends), Pie (distribution), Bar (comparison), Line (yearly)
- **Tables**: Department analytics, Reviewer performance
- **Export**: PDF and Excel report generation

### 🤖 AI Intelligence Module
- Personalized achievement recommendations for students
- Smart review assistance for HODs
- Pattern recognition and trend insights
- Department-level AI analytics

---

## 🤝 Contributing

We welcome contributions! Here's how to get started:

```bash
# 1️⃣  Fork the repository

# 2️⃣  Create a feature branch
git checkout -b feature/amazing-feature

# 3️⃣  Make your changes and commit
git commit -m "feat: add amazing feature"

# 4️⃣  Push to your branch
git push origin feature/amazing-feature

# 5️⃣  Open a Pull Request
```

### 📝 Commit Convention

We follow [Conventional Commits](https://www.conventionalcommits.org/):

| Prefix | Usage |
|--------|-------|
| `feat:` | New feature |
| `fix:` | Bug fix |
| `docs:` | Documentation changes |
| `style:` | Formatting, no code change |
| `refactor:` | Code restructuring |
| `test:` | Adding tests |
| `chore:` | Maintenance tasks |

---

## 📄 License

This project is licensed under the **MIT License**.

```
MIT License — Copyright (c) 2024 EduRev Team
```

---

<p align="center">
  <b>Built with ❤️ for Lovely Professional University</b>
  <br/>
  <sub>Empowering education through intelligent technology.</sub>
</p>
