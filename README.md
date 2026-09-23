# OrangeHRM — Manual Testing Mini Project (Week 1)

End-to-end manual QA project on the **OrangeHRM Open Source demo** application: test plan, 37 test cases, full execution results, a defect report with 6 logged bugs, and a requirements traceability matrix.

| | |
|---|---|
| **Application** | OrangeHRM Open Source HRM (demo instance) |
| **URL** | https://opensource-demo.orangehrmlive.com/ |
| **Credentials** | `Admin` / `admin123` (publicly published demo credentials) |
| **Testing type** | Manual, black-box, functional |
| **Test period** | 17 august – 16 September 2026 |
| **Tester** | Jayant Bansal |

---

## Execution Results at a Glance

| Metric | Count | % |
|---|---|---|
| Test cases designed | 37 | 100% |
| Test cases executed | 37 | 100% |
| ✅ Passed | 29 | 78.4% |
| ❌ Failed | 6 | 16.2% |
| ⛔ Blocked | 2 | 5.4% |
| 🐞 Defects logged | 5 | — |

**Defect severity split:** 1 Critical · 1 High · 1 Medium · 2 Low

---

## Repository Structure

```
OrangeHRM-Manual-Testing-Week1/
│
├── README.md                          ← you are here
│
├── 01_Test_Plan/
│   └── Test_Plan_OrangeHRM.docx       Scope, approach, environment, entry/exit
│                                      criteria, schedule, risks, sign-off
├── 02_Test_Cases/
│   └── OrangeHRM_Test_Cases_Executed.xlsx
│                                      37 test cases with actual results,
│                                      Pass/Fail/Blocked status and bug IDs
├── 03_Test_Summary_Report/
│   └── Test_Summary_Report.docx       Module-wise results, defect summary,
│                                      observations, recommendations, conclusion
├── 04_Bug_Report/
│   ├── OrangeHRM_Bug_Report.xlsx      6 defects with full reproduction steps
│   └── Bug_Report_Detailed.md         Same defects in readable Markdown
│
├── 05_Traceability_Matrix/
│   └── Requirements_Traceability_Matrix.xlsx
│                                      27 requirements → 37 test cases (100% coverage)
├── 06_Screenshots/
│   └── BUG_001.png … BUG_005.png      Evidence for each logged defect
│
└── 07_Week1_Assignments/
    └── Week1_Manual_Testing_Deliverables.xlsx
                                       20 login test cases, e-commerce checkout
                                       scenarios, login RTM, bug-hunt worksheet
```

---

## Modules Covered

| Module | Cases | Pass | Fail | Blocked |
|---|---|---|---|---|
| Login / Authentication | 11 | 7 | 3 | 1 |
| Dashboard | 4 | 4 | 0 | 0 |
| PIM – Add Employee | 6 | 4 | 2 | 0 |
| PIM – Employee List & Search | 5 | 5 | 0 | 0 |
| Leave Management | 5 | 4 | 0 | 1 |
| Admin – User Management | 4 | 4 | 0 | 0 |
| My Info | 2 | 1 | 1 | 0 |
| **Total** | **37** | **29** | **6** | **2** |

---

## Defects Found

| ID | Title | Severity | Priority | Status |
|---|---|---|---|---|
| BUG_001 | Session not invalidated after logout — protected pages viewable via Back button | 🔴 Critical | High | Open |
| BUG_002 | No account lockout or throttling after repeated failed login attempts | 🟠 High | High | Open |
| BUG_003 | Username not trimmed — valid credentials rejected when whitespace is present | 🟢 Low | Medium | Open |
| BUG_004 | Oversized profile photo rejected without stating the maximum file size | 🟡 Medium | Medium | Open |
| BUG_005 | Employee Id field accepts special characters with no format validation | 🟢 Low | Low | Open |

Full reproduction steps for each are in [`04_Bug_Report/Bug_Report_Detailed.md`](04_Bug_Report/Bug_Report_Detailed.md).

---

## Test Design Techniques Used

- **Equivalence Partitioning** — valid vs. invalid credential classes, valid vs. oversized file uploads
- **Boundary Value Analysis** — file size limits on profile photo upload, leave date ranges
- **Error Guessing** — whitespace in credentials, special characters in the Employee Id field
- **Negative Testing** — invalid logins, blank mandatory fields, mismatched password confirmation
- **Security-oriented checks** — session invalidation on logout, brute-force lockout (OWASP WSTG-ATHN-03)

---

## Test Environment

| Item | Detail |
|---|---|
| Primary browser | Google Chrome 129 (64-bit) |
| Secondary browser | Mozilla Firefox 130 — used to confirm defect reproducibility |
| Operating system | Windows 11 Home, 16 GB RAM |
| Resolution | 1920 × 1080 |
| Tools | Microsoft Excel, Microsoft Word, Chrome DevTools, Snipping Tool |

---

## Key Findings

1. **BUG_001 is the most significant finding.** After logout, authenticated pages are restored from the browser cache via the Back button, exposing employee records on a shared machine. Reproduced in both Chrome and Firefox.
2. **BUG_002** shows the login endpoint applies no brute-force protection — ten consecutive failed attempts were processed with no lockout, delay, or CAPTCHA.
3. **Core CRUD and search operations are stable.** All 5 Employee List tests and all 4 Admin User Management tests passed.
4. **Role-based access control works correctly** — a newly created ESS user saw only the permitted modules.
5. The remaining defects are validation and usability issues that do not block task completion.

---

## Blocked Test Cases

| TC ID | Test Case | Reason |
|---|---|---|
| TC_007 | Password reset email delivery | No mailbox attached to the public demo account — environment limitation, not a product defect |
| TC_031 | Approval of a pending leave request | No supervisor-subordinate reporting hierarchy configured on the demo instance |

---

## How to Review This Project

1. Start with **`01_Test_Plan/`** to understand the scope and approach.
2. Open **`02_Test_Cases/`** to see all 37 cases with their execution results (filter the Status column).
3. Read **`03_Test_Summary_Report/`** for the consolidated outcome and recommendations.
4. Check **`04_Bug_Report/`** for defect details and **`06_Screenshots/`** for the supporting evidence.
5. Use **`05_Traceability_Matrix/`** to confirm every requirement maps to at least one test case.

---

## Note on the Test Environment

OrangeHRM publishes this demo instance publicly and resets its data periodically. Records created during testing use clearly identifiable test values (`Rahul Sharma`, Employee Id `0421`, user `rahul.sharma`). Because the instance is shared, record-dependent results may vary between runs — each defect in this report was re-verified before being logged.
