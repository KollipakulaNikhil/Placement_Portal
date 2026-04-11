# 🤖 CareerAI — Agentic AI Architecture

> **Built by ByteForces** | Lovely Professional University  
> This document details every AI agent deployed in the CareerAI platform, their purpose, data flow, and how they power an intelligent, end-to-end placement ecosystem.

---

## 📊 AI Agent Overview

CareerAI leverages **6 specialized AI agents** deployed as serverless edge functions, each powered by **Google Gemini 3 Flash Preview** via the Lovable AI Gateway. Every agent is purpose-built with domain-specific system prompts, live database context injection, and real-time streaming responses (SSE).

| # | Agent Name | Edge Function | Portal | Purpose | Response Type |
|---|-----------|---------------|--------|---------|---------------|
| 1 | **Career Coach** | `career-coach` | Student | Personalized career guidance, resume roast, skill gap analysis, interview prep | Streaming SSE |
| 2 | **Resume Analyzer** | `resume-analyzer` | Student | ATS-style resume scoring, JD matching, role recommendations | Streaming SSE |
| 3 | **Mock Interview Engine** | `mock-interview` | Student | Question generation, answer evaluation, coding assessment, report card | JSON |
| 4 | **AI Benefits Advisor** | `ai-recommend` | Student | LPU attendance & academic benefit eligibility analysis | Streaming SSE |
| 5 | **Analytics Assistant** | `analytics-assistant` | Admin / Director | Live placement data analysis, department rankings, trend insights | Streaming SSE |
| 6 | **AI Talent Scout** | `ai-talent-scout` | Recruiter | Natural language candidate search and ranking | JSON |

---

## 🏗️ System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                        FRONTEND (React SPA)                     │
│                                                                 │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────────────┐   │
│  │ Student  │ │ Recruiter│ │  Admin   │ │ Placement Dir.   │   │
│  │ Portal   │ │ Portal   │ │ Portal   │ │ Portal           │   │
│  └────┬─────┘ └────┬─────┘ └────┬─────┘ └────────┬─────────┘   │
│       │             │            │                 │             │
│       ▼             ▼            ▼                 ▼             │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │              SSE Stream / JSON Fetch Layer               │   │
│  └──────────────────────────┬───────────────────────────────┘   │
└─────────────────────────────┼───────────────────────────────────┘
                              │ HTTPS
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                   SUPABASE EDGE FUNCTIONS                       │
│                                                                 │
│  ┌────────────┐ ┌────────────┐ ┌────────────┐ ┌────────────┐  │
│  │  career-   │ │  resume-   │ │   mock-    │ │    ai-     │  │
│  │  coach     │ │  analyzer  │ │ interview  │ │  recommend │  │
│  └─────┬──────┘ └─────┬──────┘ └─────┬──────┘ └─────┬──────┘  │
│        │              │              │               │          │
│  ┌─────┴──────┐ ┌─────┴──────┐                                 │
│  │ analytics- │ │ ai-talent- │                                  │
│  │ assistant  │ │   scout    │                                  │
│  └─────┬──────┘ └─────┬──────┘                                  │
│        │              │                                         │
│        ▼              ▼                                         │
│  ┌──────────────────────────────────────────────────────────┐   │
│  │         Live Database Context Injection                  │   │
│  │    (Profiles, Jobs, Applications, Companies, Skills)     │   │
│  └──────────────────────────┬───────────────────────────────┘   │
└─────────────────────────────┼───────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    LOVABLE AI GATEWAY                           │
│              Google Gemini 3 Flash Preview                      │
│         (Streaming Chat Completions API — OpenAI-compatible)    │
└─────────────────────────────────────────────────────────────────┘
```

---

## 🔍 Agent Deep Dives

### Agent 1: Career Coach 🎯

| Attribute | Details |
|-----------|---------|
| **Function** | `career-coach` |
| **Used In** | Student Portal → AI Career Coach page |
| **AI Model** | `google/gemini-3-flash-preview` |
| **Persona** | "PlaceMe AI" — a senior career mentor at LPU |
| **Response** | Real-time streaming (SSE) |

**Capabilities:**
- 💬 **General Career Chat** — answers any placement, career, or skill question
- 🔥 **Resume Roast Mode** — brutally honest resume critique with actionable fixes
- 🎯 **Skill Gap Analysis** — identifies missing skills based on target roles
- 🎤 **Interview Prep Mode** — simulates HR/technical interview Q&A with model answers

**Data Context Injected:**
- Student's profile (name, department, CGPA, skills, placement status)
- Dynamic mode switching via action parameter

**Flow:**
```
Student types question
       │
       ▼
Frontend sends { messages[], action, studentProfile }
       │
       ▼
Edge Function builds system prompt + injects student context
       │
       ▼
Calls Lovable AI Gateway (streaming)
       │
       ▼
SSE chunks streamed back → rendered in real-time chat UI
```

---

### Agent 2: Resume Analyzer 📄

| Attribute | Details |
|-----------|---------|
| **Function** | `resume-analyzer` |
| **Used In** | Student Portal → Resume Matcher page |
| **AI Model** | `google/gemini-3-flash-preview` |
| **Persona** | "ResumeIQ" — senior HR reviewer with 10,000+ resume experience |
| **Response** | Real-time streaming (SSE) |

**Two Operating Modes:**

| Mode | Input | Output |
|------|-------|--------|
| **Resume Analysis** | Resume text | ATS score, section-by-section breakdown, strengths, weaknesses, top matched roles with company suggestions |
| **JD Matching** | Resume text + Job Description | Match percentage, keyword analysis, missing skills, interview tips, improvement suggestions |

**Structured Output Format:**
```json
{
  "atsScore": 72,
  "sections": { "contact": {...}, "skills": {...}, "experience": {...} },
  "topRoles": [
    { "role": "Backend Developer", "match": 85, "companies": ["Microsoft", "Google"] }
  ],
  "jdMatch": { "score": 68, "matchedKeywords": [...], "missingSkills": [...] }
}
```

---

### Agent 3: Mock Interview Engine 🎤

| Attribute | Details |
|-----------|---------|
| **Function** | `mock-interview` |
| **Used In** | Student Portal → Mock Interview & Video Interview pages |
| **AI Model** | `google/gemini-3-flash-preview` |
| **Persona** | Expert technical interviewer |
| **Response** | JSON (non-streaming) |

**Four Sub-Agents in One:**

| Sub-Agent | Action | Purpose |
|-----------|--------|---------|
| **Question Generator** | `generate` | Creates customized interview questions based on domain, difficulty, job type |
| **Answer Evaluator** | `evaluate` | Scores student answers (0-10) with detailed feedback and model answers |
| **Code Evaluator** | `evaluate-code` | Evaluates coding/pseudocode solutions with partial credit scoring |
| **Report Card Generator** | `report` | Produces comprehensive interview performance report with metrics |

**Configurable Parameters:**
- Domain: DSA, System Design, DBMS, OS, Web Dev, ML, etc.
- Difficulty: Easy, Medium, Hard
- Question Count: 3–10
- Job Type: Software Engineer, Data Analyst, DevOps, etc.

**Flow:**
```
Student configures interview settings
       │
       ▼
Generate questions (action: "generate")
       │
       ▼
Student answers each question (text or code)
       │
       ▼
Evaluate each answer (action: "evaluate" / "evaluate-code")
       │
       ▼
Generate final report card (action: "report")
       │
       ▼
Save results to mock_interviews table in database
```

---

### Agent 4: AI Benefits Advisor 🎓

| Attribute | Details |
|-----------|---------|
| **Function** | `ai-recommend` |
| **Used In** | Student Portal → Benefits Analysis widget |
| **AI Model** | `google/gemini-3-flash-preview` |
| **Persona** | "EduRev AI Benefits Advisor" — LPU policy expert |
| **Response** | Real-time streaming (SSE) |

**Unique Feature — Embedded Knowledge Base:**
The agent has the complete LPU attendance benefit criteria hard-coded into its system prompt, including:
- Attendance percentage thresholds and corresponding benefits
- Academic excellence criteria
- Fee concession eligibility rules
- Special category benefits

**Flow:**
```
Student asks about benefits eligibility
       │
       ▼
Edge function loads CRITERIA_KNOWLEDGE (embedded policy docs)
       │
       ▼
Builds system prompt with student profile + criteria
       │
       ▼
AI analyzes eligibility and responds with specific recommendations
```

---

### Agent 5: Analytics Assistant 📊

| Attribute | Details |
|-----------|---------|
| **Function** | `analytics-assistant` |
| **Used In** | Admin Portal → AI Insights page, Placement Director Portal → AI Insights |
| **AI Model** | `google/gemini-3-flash-preview` |
| **Persona** | "CareerAI Analytics Assistant" — placement analytics expert |
| **Response** | Real-time streaming (SSE) |

**Live Database Context — Injected Every Request:**

| Data Source | Fields Queried |
|-------------|---------------|
| `profiles` | name, department, branch, school, section, CGPA, placement_status, skills, aptitude_score, programming_score |
| `companies` | name, industry, package range, active status |
| `job_postings` | title, status, skills_required, eligible_branches, package |
| `applications` | status, student_id, job_posting_id, applied_at |

**Pre-built Quick Actions (8 one-click analytics):**

| Quick Action | What It Does |
|-------------|-------------|
| Department Rankings | Ranks all departments by placement rate with CGPA averages |
| Top Performers | Lists top 10 students by CGPA with full profile |
| Unplaced High-Potential | Identifies unplaced students with CGPA ≥ 8.0 |
| Monthly Trends | Analyzes placement activity trends over time |
| Section Analysis | Compares section-wise placement performance |
| Skill Gaps | Identifies demand-supply gaps in student skills |
| Action Plan | Generates department-wise improvement recommendations |
| Best Student | Identifies the single best candidate across all metrics |

**Flow:**
```
Admin/Director clicks quick action or types question
       │
       ▼
Edge function queries ALL 4 tables in parallel (Promise.all)
       │
       ▼
Builds comprehensive data summary:
  • Total/placed counts, placement rate
  • Department-wise breakdown with avg CGPA
  • Unplaced high-CGPA students list
  • Top companies by package
       │
       ▼
Injects data summary into system prompt
       │
       ▼
AI analyzes and responds with formatted tables + insights
       │
       ▼
Markdown rendered with custom table styling in chat UI
```

---

### Agent 6: AI Talent Scout 🔎

| Attribute | Details |
|-----------|---------|
| **Function** | `ai-talent-scout` |
| **Used In** | Recruiter Portal → Candidate Search |
| **AI Model** | `google/gemini-3-flash-preview` |
| **Persona** | AI-powered recruiter assistant |
| **Response** | JSON (structured candidate ranking) |

**How It Works:**
- Recruiter types a natural language query (e.g., "Find Python developers with CGPA > 8 who know machine learning")
- Agent fetches all student profiles from the database
- AI ranks and filters candidates based on the query
- Returns structured JSON array of top matching students

**Flow:**
```
Recruiter types natural language search query
       │
       ▼
Edge function fetches all student profiles
       │
       ▼
Sends query + student database to AI
       │
       ▼
AI returns ranked JSON array of matching candidates
       │
       ▼
Results displayed as interactive candidate cards
```

---

## 📈 AI Usage Across Portals

| Portal | AI Agents Used | Features Powered |
|--------|---------------|-----------------|
| **Student** | Career Coach, Resume Analyzer, Mock Interview, Benefits Advisor | Career guidance, resume scoring, JD matching, interview practice, benefit eligibility |
| **Admin** | Analytics Assistant | Department rankings, placement trends, skill gap analysis, action plans |
| **Placement Director** | Analytics Assistant | Same analytics capabilities as admin for strategic decision-making |
| **Recruiter** | AI Talent Scout | Natural language candidate search and intelligent ranking |
| **Staff (HOD)** | — | At-risk student identification (rule-based, not AI-powered) |

---

## 🔧 Technical Specifications

| Specification | Details |
|--------------|---------|
| **AI Gateway** | Lovable AI Gateway (`ai.gateway.lovable.dev`) |
| **Model** | `google/gemini-3-flash-preview` (all 6 agents) |
| **API Format** | OpenAI-compatible Chat Completions API |
| **Auth** | `LOVABLE_API_KEY` (server-side only, never exposed to client) |
| **Streaming** | Server-Sent Events (SSE) for 4 agents, JSON for 2 agents |
| **Database Access** | Supabase Service Role Key (server-side only) |
| **Deployment** | Deno-based serverless edge functions, auto-deployed |
| **CORS** | Fully configured for cross-origin requests |
| **Error Handling** | Rate limiting (429), credit exhaustion (402), graceful fallbacks |

---

## 🛡️ Security & Data Privacy

| Measure | Implementation |
|---------|---------------|
| API keys never reach client | All AI calls go through edge functions; `LOVABLE_API_KEY` is server-side only |
| Database access is server-side | Service role key used only in edge functions, never in frontend |
| No student data stored by AI | AI processes data in-memory; no training on user data |
| Role-based access | Each portal only accesses its permitted AI agents |
| Rate limiting | Built-in 429 handling prevents abuse |

---

## 📊 Impact Metrics

| Metric | Value |
|--------|-------|
| **Total AI Agents** | 6 |
| **Total AI-Powered Features** | 14+ |
| **Portals with AI Integration** | 4 out of 5 |
| **AI Response Types** | Streaming SSE (4) + Structured JSON (2) |
| **Database Tables Queried by AI** | 4 (profiles, companies, job_postings, applications) |
| **Quick Action Presets** | 8 (Analytics Assistant) |
| **Interview Question Domains** | 8+ (DSA, System Design, DBMS, OS, Web Dev, ML, etc.) |

---

> *CareerAI demonstrates production-grade agentic AI integration — not just a wrapper around an LLM, but purpose-built agents with live data injection, domain-specific personas, multi-modal outputs (streaming text, structured JSON, evaluation rubrics), and role-based access control.*

---

**© 2025 ByteForces | Lovely Professional University**
