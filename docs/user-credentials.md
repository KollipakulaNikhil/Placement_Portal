# User Credentials — EduRev Platform

All accounts use the password: **`Demo@123`**

---

## Staff Accounts

| Role | Email | Name | Department |
|------|-------|------|------------|
| **Admin** | admin@lpu.in | Admin User | CSE |
| **DAA** | daa@lpu.in | Prof. Ashok Mittal | CSE |
| **School HOD** | schoolhod@lpu.in | Dr. Meena Sharma | CSE |
| **Concern HOD (CSE)** | hod.cse@lpu.in | Dr. Rajesh Kumar | CSE |
| **Concern HOD (ECE)** | hod.ece@lpu.in | Dr. Anita Verma | ECE |
| **Concern HOD (ME)** | hod.me@lpu.in | Dr. Suresh Patel | ME |
| **Concern HOD (CE)** | hod.ce@lpu.in | Dr. Kavita Singh | CE |
| **Concern HOD (IT)** | hod.it@lpu.in | Dr. Vikram Reddy | IT |
| **Concern HOD (AI-ML)** | hod.aiml@lpu.in | Dr. Priya Nair | AI-ML |

---

## Student Accounts

200 student accounts are seeded.

### 4th Year (Batch 2021-25)
- **Emails**: `student1@lpu.in` through `student100@lpu.in`
- **Reg Numbers**: `12407001` through `12407100`
- **Sections**: K21EA, K21EB, K21EC, K21ED, K21FA, K21FB (rotating)

### 3rd Year (Batch 2022-26)
- **Emails**: `student101@lpu.in` through `student200@lpu.in`
- **Reg Numbers**: `12307001` through `12307100`
- **Sections**: K22EA, K22EB, K22EC, K22ED, K22FA, K22FB (rotating)

### Department Assignment
Students are assigned to departments in a round-robin:
1. CSE → 2. ECE → 3. ME → 4. CE → 5. IT → 6. AI-ML

So:
- student1 → CSE, student2 → ECE, student3 → ME, student4 → CE, student5 → IT, student6 → AI-ML
- student7 → CSE (repeats)

### School Mapping
| Department | School |
|------------|--------|
| CSE | School of Computer Science |
| IT | School of Computer Science |
| AI-ML | School of Computer Science |
| ECE | School of Electronics |
| ME | School of Mechanical Engineering |
| CE | School of Civil Engineering |

---

## Seeded Data

- **50 achievements** across 12 categories with various statuses (submitted, under_review, approved, rejected, forwarded)
- **50 achievement reviews** linked to the approval chain
- **12 notifications** (broadcast + targeted)

---

## Achievement Categories
hackathon, research-paper, nptel, internship, sports, certification, community-service, patent, entrepreneurship, cultural, workshop, competition

## Approval Chain
1. Student submits → **Concern HOD** reviews
2. Concern HOD approves → **School HOD** reviews
3. School HOD approves → **DAA** gives final approval
