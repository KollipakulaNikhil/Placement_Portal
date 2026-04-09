

# Admin Dashboard & Analytics — Full Industry-Grade Rebuild

## The Problem

Both admin pages are empty shells. The dashboard has 4 basic stat cards and a "coming soon" placeholder. Analytics is completely blank. For a product that should beat 3,000 competing teams, the admin needs a **war room** — every metric, every department, every batch, every trend, every failure point visible and drillable.

## What We're Building

### File 1: `src/pages/admin/AdminDashboard.tsx` (~600 lines)

**Row 1 — 6 KPI Cards (equal height grid)**
- Total Students (all profiles)
- Placed Students (placement_status = 'placed')
- Placement Rate % (placed/total)
- Active Companies
- Open Positions
- Total Applications
- Each card: animated counter, trend arrow, mini sparkline

**Row 2 — Two large panels side by side**

Left (7 cols): **Monthly Placement Trend** — AreaChart with dual lines (Applications vs Placements) over last 12 months. Toggle between weekly/monthly/quarterly view.

Right (5 cols): **Company Tier Distribution** — Donut chart showing Tier 1/2/3 company split with package ranges.

**Row 3 — Department Performance Table (full width)**
- All 6 departments: CSE, IT, ECE, AI-ML, CE, ME
- Columns: Department, Total Students, Placed, Unplaced, Placement Rate (progress bar), Avg CGPA, Top Company, Failure Rate %
- Sortable by any column
- Color-coded: green >70%, amber 40-70%, red <40%

**Row 4 — Two panels**

Left (6 cols): **Batch-wise Analysis** — Grouped bar chart showing placement stats by graduation year/batch. Filter by department.

Right (6 cols): **Live Activity Feed** — Recent applications, offers, referrals with timestamps, auto-refresh.

**Row 5 — Quick Actions Grid**
- Manage Students, Manage Staff, Analytics Deep-Dive, Company Registration, Placement Training Cell, Settings
- Gradient icon cards linking to respective pages

**Row 6 — Application Status Pipeline**
- Horizontal funnel: Applied → Shortlisted → Interviewing → Offered → Hired → Rejected
- Real counts and % conversion between stages

### File 2: `src/pages/admin/AdminAnalytics.tsx` (~700 lines)

This is the deep-dive analytics page — the unique differentiator.

**Filter Bar (sticky top)**
- Department multi-select (all 6 departments)
- School multi-select (all 4 schools)
- Programme filter
- Batch/Graduation Year filter
- Section filter (K21EA, K21EB, etc.)
- Date range: 7d, 30d, Quarter, Year, Custom
- "Clear All" + "Apply" buttons

**Row 1 — 8 Metric Cards (scrollable row)**
- Total Students, Placed, Unplaced, Placement Rate %, Avg Package (LPA), Highest Package, Total Applications, Conversion Rate (hired/applied)
- All respond to filters — change department and every number updates

**Row 2 — Placement Trend Over Time**
- Line chart with multiple series (one per selected department)
- Toggle: Weekly / Monthly / Quarterly
- Hover tooltip with exact numbers
- Shows applications submitted AND placements confirmed

**Row 3 — Department Comparison (full width)**
- Stacked bar chart: each department bar split into Placed/Unplaced/In-Progress
- Below: detailed table with columns: Dept, School, Total, Placed, Rate%, Avg CGPA, Avg Aptitude, Avg Programming, Failure Rate%, Top Skills

**Row 4 — Failure Analysis (the unique feature no one has)**
- **Failure Rate by Department**: Which departments have lowest placement rates
- **Failure Rate by CGPA Range**: Bar chart — students with CGPA <6, 6-7, 7-8, 8-9, 9+ and their placement rates
- **Failure Rate by Skills Gap**: What skills do unplaced students lack vs placed students
- **At-Risk Prediction**: Table of students most likely to NOT get placed based on CGPA + aptitude + programming scores

**Row 5 — Section-Level Drill-Down**
- When user selects a department → shows sections (K21EA, K21EB, etc.)
- Each section: student count, placed count, avg CGPA, placement rate
- Click section → expands to show student list with individual stats

**Row 6 — Company Analytics**
- **Top Hiring Companies**: Ranked list with hire count, avg package, departments hired from
- **Package Distribution**: Histogram of package ranges (3-5 LPA, 5-8, 8-12, 12-20, 20+)
- **Company Visit Calendar**: Timeline of upcoming/past placement drives

**Row 7 — Skill Demand vs Supply**
- Dual bar chart: "Skills companies ask for" vs "Skills students have"
- Gap analysis — highlights biggest mismatches
- Data from `skills_required` in job_postings vs `skills` in profiles

**Row 8 — Leaderboard**
- Top 10 placed students by package
- Gold/Silver/Bronze badges for top 3
- Shows name, department, company, package, CGPA

### File 3: Sidebar Update (`DashboardSidebar.tsx`)

Add to admin nav:
- `BarChart3` → Analytics (`/admin/analytics`)
- `Zap` → Placement Training Cell (`/admin/users`)

### No New Tables Needed

All analytics pull from existing tables:
- `profiles` — student data, departments, schools, sections, CGPA, scores, placement_status, skills
- `companies` — company info, tiers, packages
- `job_postings` — positions, skills_required, eligible_branches
- `applications` — pipeline stages, status tracking
- `placement_drives` — drive scheduling
- `referrals` — referral tracking

## Technical Approach

- Recharts for all charts (AreaChart, BarChart, PieChart, LineChart, RadarChart) — already available
- Framer Motion for card animations — already installed
- React Query for data fetching with proper caching
- All filters use URL search params so analytics views are shareable/bookmarkable
- Responsive grid — works on tablets too
- No new npm packages needed

## Files Changed

| File | Action | Size |
|------|--------|------|
| `src/pages/admin/AdminDashboard.tsx` | Full rewrite | ~600 lines |
| `src/pages/admin/AdminAnalytics.tsx` | Full rewrite | ~700 lines |
| `src/components/dashboard/shared/DashboardSidebar.tsx` | Add Analytics + PTC to admin nav | ~5 lines changed |

