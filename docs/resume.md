# Resume Intelligence — How It Works

This document explains the complete flow of Resume Analysis and JD Matching, including how skills are extracted, how scores are calculated, and how matched/missing skills are determined.

---

## Table of Contents

1. [Overview](#overview)
2. [Input Methods](#input-methods)
3. [Resume Analysis Mode](#resume-analysis-mode)
4. [JD Match Mode](#jd-match-mode)
5. [How Skills Are Identified](#how-skills-are-identified)
6. [How Scores Are Calculated](#how-scores-are-calculated)
7. [How Matched & Missing Skills Work](#how-matched--missing-skills-work)
8. [Top Matched Roles (Dashboard)](#top-matched-roles-dashboard)
9. [Architecture & Data Flow](#architecture--data-flow)

---

## Overview

The Resume Intelligence feature provides two modes:

| Mode | Purpose |
|------|---------|
| **Resume Analysis** | Comprehensive AI review of a resume — scores, strengths, weaknesses, ATS compatibility, role fitness, and improvement suggestions |
| **JD Match** | Compares a resume against a specific Job Description — match score, matched/missing/partial skills, deal-breakers, and apply recommendation |

Both modes use the **`resume-analyzer`** edge function, which sends the resume text (and optionally a JD) to **Google Gemini 3 Flash** via the Lovable AI Gateway.

---

## Input Methods

The system accepts resume content in **three ways** (in priority order):

1. **Pasted text** — User pastes their resume text directly into the textarea
2. **Uploaded file** — User uploads a PDF, DOCX, or TXT file. The file is parsed **client-side**:
   - **PDF**: Parsed using `pdfjs-dist` — extracts text from each page
   - **TXT**: Read directly as text
   - **DOCX**: Read as text with non-printable characters stripped (basic fallback)
3. **Profile fallback** — If no file or text is provided, the system constructs a basic resume from the user's profile data (name, branch, CGPA, skills, preferred roles, etc.)

> **Important**: The resume content is sent as **plain text** to the AI. No structured parsing happens before the AI call — the AI model receives the raw text and interprets it.

---

## Resume Analysis Mode

### What happens when you click "Analyze with AI"

1. The resume text is gathered (from paste, file, or profile)
2. A request is sent to the `resume-analyzer` edge function with `mode: "analyze"`
3. The edge function sends the text to Gemini with a detailed system prompt that instructs it to act as **"ResumeIQ"** — a senior HR reviewer
4. The AI returns a structured JSON response

### Response Structure

```json
{
  "overall_score": 72,
  "summary": "2-3 sentence executive summary",
  "strengths": ["strength 1", "strength 2", ...],
  "weaknesses": ["weakness 1", "weakness 2", ...],
  "extracted_skills": {
    "technical": ["Python", "React", "AWS"],
    "soft": ["Leadership", "Communication"],
    "tools": ["Git", "Docker", "Figma"]
  },
  "role_fitness": [
    {"role": "SDE-1 (Backend)", "fit_pct": 85, "reason": "Strong DSA + backend stack"}
  ],
  "hr_red_flags": ["No quantified achievements", ...],
  "improvements": [
    {"section": "Projects", "issue": "No metrics", "fix": "Add numbers like 'Reduced load time by 40%'"}
  ],
  "ats_score": 65,
  "ats_issues": ["Missing keywords for target roles", ...],
  "target_companies": [
    {"company": "Microsoft", "readiness": "medium", "gaps": ["Need system design projects"]}
  ]
}
```

### How the Overall Score is Determined

The **overall_score (0-100)** is generated entirely by the AI model based on these factors:

| Factor | Weight (approximate) | What the AI evaluates |
|--------|---------------------|----------------------|
| Skills relevance | ~25% | Are skills current, in-demand, and well-organized? |
| Experience quality | ~20% | Impact-focused vs duty-focused? Quantified achievements? |
| Projects | ~15% | Complexity, tech stack, metrics, real-world impact |
| Education | ~10% | Degree relevance, CGPA, certifications |
| Resume formatting | ~10% | ATS-friendly structure, readability, length |
| Missing elements | ~10% | Gaps in timeline, missing sections, generic content |
| Market readiness | ~10% | How competitive is this resume in the current market? |

> **Scoring guideline given to AI**: "Score realistically — most fresh grads are 40-65, not 80+."

### How the ATS Score is Determined

The **ats_score** evaluates how well the resume would perform with Applicant Tracking Systems:

- Keyword density and relevance
- Formatting issues (tables, columns, graphics that ATS can't parse)
- Section headers and structure
- Use of standard section names (Education, Experience, Skills)
- File format compatibility

---

## JD Match Mode

### What happens when you click "Match Resume vs JD"

1. Both resume text and job description text are gathered
2. A request is sent to the `resume-analyzer` edge function with `mode: "jd-match"`
3. The edge function sends both texts to Gemini with a system prompt for **"ResumeIQ JD Matcher"**
4. The AI compares the two documents and returns a structured JSON response

### Response Structure

```json
{
  "match_score": 72,
  "verdict": "PARTIAL MATCH",
  "summary": "How well the candidate fits",
  "matched_skills": ["Python", "React", "AWS"],
  "missing_skills": ["Kubernetes", "System Design", "GraphQL"],
  "partial_skills": [
    {"skill": "Cloud", "has": "AWS basics", "needs": "Production-level cloud architecture"}
  ],
  "experience_gap": "JD asks for 2+ years, candidate has ~1 year",
  "education_match": true,
  "culture_fit_signals": ["Open source contributor"],
  "deal_breakers": ["No experience with microservices"],
  "recommendations": ["Take a system design course"],
  "interview_prep": ["Expect questions on distributed systems"],
  "apply_recommendation": "APPLY WITH PREP",
  "apply_reason": "You have 60% of required skills..."
}
```

---

## How Skills Are Identified

### This is NOT string matching — it's AI semantic analysis

The skills in the response (both in Resume Analysis and JD Match) are **not** extracted via regex, keyword lists, or string comparison. Here's exactly what happens:

1. **The full resume text** is sent to the AI model as a single block of text
2. **The AI reads and understands** the content semantically — it identifies skills from:
   - Explicit skill sections ("Skills: Python, Java, React")
   - Project descriptions ("Built a REST API using Node.js and MongoDB")
   - Work experience ("Managed AWS infrastructure including EC2, S3, Lambda")
   - Certifications ("AWS Solutions Architect Certified")
   - Education ("B.Tech in Computer Science")
3. **For JD Match**, the AI also reads the job description and identifies:
   - Explicitly required skills ("Must have: Python, Kubernetes")
   - Implied skills ("Experience with CI/CD pipelines" → implies Jenkins/GitHub Actions/Docker)
   - Nice-to-have skills vs mandatory skills

### Matched vs Missing Skills in JD Match

| Category | How it's determined |
|----------|-------------------|
| **Matched Skills** | Skills that appear in BOTH the resume AND the JD. The AI uses semantic understanding, not exact string matching. E.g., "React.js" in resume matches "React" in JD. "AWS" in resume matches "Cloud experience" in JD. |
| **Missing Skills** | Skills required by the JD that are NOT found anywhere in the resume. The AI checks for both explicit mentions and implied experience. |
| **Partial Skills** | Skills where the candidate has SOME related experience but not at the level the JD requires. E.g., has "AWS basics" but JD needs "Production-level cloud architecture". |

### Example

**Resume says**: "Built a web app using Python, Flask, and deployed on AWS EC2"
**JD requires**: "Python, Django, AWS, Kubernetes, Docker"

Result:
- ✅ **Matched**: Python, AWS (semantic match — EC2 implies AWS knowledge)
- ❌ **Missing**: Kubernetes, Docker (not mentioned anywhere in resume)
- ⚠️ **Partial**: Django (has Flask which is similar, but not Django specifically)

---

## How Scores Are Calculated

### Resume Analysis — `overall_score` (0-100)

- **Generated entirely by AI** based on the system prompt guidelines
- The AI evaluates content quality, formatting, skill relevance, quantified achievements, ATS compatibility, and market competitiveness
- Scoring is intentionally conservative: fresh graduates typically score 40-65

### JD Match — `match_score` (0-100)

- **Generated entirely by AI** based on comparing resume content against JD requirements
- Factors considered:
  - % of required skills matched
  - Experience level alignment
  - Education requirements met
  - Deal-breakers present
  - Nice-to-have skills
  - Cultural fit signals

### Verdict Categories (JD Match)

| Verdict | Score Range | Meaning |
|---------|-----------|---------|
| STRONG MATCH | ~75-100% | Meets most/all requirements |
| PARTIAL MATCH | ~40-74% | Meets some requirements, gaps exist |
| WEAK MATCH | ~0-39% | Significant gaps, not recommended |

### Apply Recommendation (JD Match)

| Recommendation | Meaning |
|---------------|---------|
| DEFINITELY APPLY | Strong match, go ahead |
| APPLY WITH PREP | Good potential, prep on gaps first |
| APPLY AS STRETCH | Long shot but worth trying |
| SKIP | Too many deal-breakers |

---

## Top Matched Roles (Dashboard)

The **Top Matched Roles** section on the Resume Matcher page uses a **different, local algorithm** (not AI) to match the student's profile against open job postings in the database.

### How the local match score works

```
Score = Skills Match (50%) + CGPA Match (25%) + Branch Match (25%)
```

#### Skills Match (up to 50 points)
- Compares the student's skills (from profile OR from AI-extracted skills if a resume was analyzed) against each job's `skills_required` field
- Uses **case-insensitive substring matching**: if a student skill contains or is contained by a required skill, it counts as a match
- Score = (matched skills / total required skills) × 50

#### CGPA Match (up to 25 points)
- If the student's CGPA meets or exceeds the job's `min_cgpa`: full 25 points
- If below: proportional score = (student CGPA / min CGPA) × 20

#### Branch Match (up to 25 points)
- If the student's branch is in the job's `eligible_branches` array: full 25 points
- Otherwise: 0 points

### Match Labels

| Score | Label | Color |
|-------|-------|-------|
| ≥ 80% | HIGH MATCH | Green |
| 60-79% | MEDIUM | Amber |
| < 60% | LOW | Red |

> **Note**: This local matching is a lightweight approximation. The AI-based JD Match provides much deeper analysis.

---

## Architecture & Data Flow

```
┌──────────────────────────────────────────────────────────┐
│                    CLIENT (Browser)                       │
│                                                          │
│  1. User uploads PDF / pastes text / uses profile data   │
│  2. PDF → pdfjs-dist → plain text extraction             │
│  3. Text sent to edge function via supabase.functions    │
└──────────────────┬───────────────────────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────────────────────┐
│           EDGE FUNCTION: resume-analyzer                 │
│                                                          │
│  1. Receives { resumeText, jobDescription?, mode }       │
│  2. Constructs system prompt based on mode               │
│  3. Calls Lovable AI Gateway (Gemini 3 Flash)            │
│  4. Parses JSON from AI response                         │
│  5. Returns structured data to client                    │
└──────────────────┬───────────────────────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────────────────────┐
│            LOVABLE AI GATEWAY                            │
│                                                          │
│  Model: google/gemini-3-flash-preview                    │
│  Auth: LOVABLE_API_KEY (server-side secret)              │
│  Format: OpenAI-compatible chat completions API          │
└──────────────────────────────────────────────────────────┘
```

### Key Technical Details

| Aspect | Detail |
|--------|--------|
| **AI Model** | Google Gemini 3 Flash (Preview) |
| **API Format** | OpenAI-compatible chat completions |
| **Response Format** | Raw JSON (AI instructed to return only valid JSON, no markdown) |
| **Fallback Parsing** | Strips markdown code fences if AI wraps response in ```json blocks |
| **Error Handling** | 429 → rate limit message, 402 → credits exhausted, 500 → generic error |
| **File Size Limit** | 5MB max upload |
| **Supported Formats** | PDF, DOCX, TXT |

### What is NOT stored

- Resume text is **not saved** to the database after analysis
- Analysis results are **not persisted** — they exist only in the browser session
- Job descriptions are **not stored** after JD matching
- Each analysis is a one-time request/response cycle
