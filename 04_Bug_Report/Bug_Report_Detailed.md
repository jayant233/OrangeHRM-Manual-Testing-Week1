# Defect Report — OrangeHRM Open Source Demo

| | |
|---|---|
| **Application** | OrangeHRM Open Source HRM (demo instance) |
| **URL** | https://opensource-demo.orangehrmlive.com/ |
| **Environment** | Chrome 129 / Firefox 130, Windows 11, 1920×1080 |
| **Reported by** | Jayant Bansal |
| **Reported on** | 16 September 2026 |
| **Total defects** | 6 — 1 Critical, 1 High, 1 Medium, 3 Low |

---

## BUG_001 — Session is not invalidated after logout; protected pages remain viewable via the browser Back button

| Field | Value |
|---|---|
| **Module** | Login / Authentication |
| **Test Case** | TC_009 |
| **Severity** | 🔴 Critical |
| **Priority** | High |
| **Status** | Open |
| **Screenshot** | `BUG_001.png` |

**Steps to Reproduce**

1. Navigate to https://opensource-demo.orangehrmlive.com/
2. Log in as `Admin` / `admin123`
3. Navigate to **PIM → Employee List** and wait for the employee records to load
4. Click the profile icon in the top-right and select **Logout**
5. Confirm that the Login page is displayed
6. Press the browser **Back** button once

**Expected Result**
The user remains logged out. The application redirects back to the Login page and no authenticated content is rendered.

**Actual Result**
The previously visited Employee List page is restored from the browser cache with employee names and records still readable. The protected view is exposed to anyone with physical access to the machine after the user has logged out.

**Notes**
Reproduced 3/3 times in Chrome 129 and 2/2 times in Firefox 130. `Cache-Control: no-store` headers appear to be missing on authenticated pages. The server-side session may also not be destroyed on logout — worth confirming by replaying the session cookie.

---

## BUG_002 — No account lockout or throttling is applied after repeated failed login attempts

| Field | Value |
|---|---|
| **Module** | Login / Authentication |
| **Test Case** | TC_010 |
| **Severity** | 🟠 High |
| **Priority** | High |
| **Status** | Open |
| **Screenshot** | `BUG_002.png` |

**Steps to Reproduce**

1. Navigate to https://opensource-demo.orangehrmlive.com/
2. Enter Username `Admin` and Password `wrongpass`
3. Click **Login** and observe the error message
4. Repeat steps 2–3 ten consecutive times
5. Observe whether any lockout, delay or CAPTCHA is applied

**Expected Result**
After a defined threshold (commonly 3–5 attempts) the account is temporarily locked, a progressive delay is applied, or a CAPTCHA challenge is presented.

**Actual Result**
All ten consecutive failed attempts were processed immediately and returned only *"Invalid credentials"*. No lockout, rate limit, delay or CAPTCHA was applied at any point, leaving the login endpoint open to unlimited password guessing.

**Notes**
Verified against OWASP WSTG-ATHN-03 (Testing for Weak Lock Out Mechanism). Attempts were made manually at roughly 3-second intervals. The generic error message is correct behaviour and prevents username enumeration — the missing control is the lockout itself.

---

## BUG_003 — Username is not trimmed; valid credentials are rejected when surrounding whitespace is present

| Field | Value |
|---|---|
| **Module** | Login / Authentication |
| **Test Case** | TC_011 |
| **Severity** | 🟢 Low |
| **Priority** | Medium |
| **Status** | Open |
| **Screenshot** | `BUG_003.png` |

**Steps to Reproduce**

1. Navigate to https://opensource-demo.orangehrmlive.com/
2. In the Username field enter `␣␣Admin␣␣` — including the leading and trailing spaces
3. Enter Password `admin123`
4. Click **Login**

**Expected Result**
Leading and trailing whitespace is trimmed from the username before authentication and the user logs in successfully.

**Actual Result**
Login is rejected with *"Invalid credentials"*. The whitespace is submitted as part of the username, so a user who copy-pastes their username from an email or document is blocked without being told why.

**Notes**
Commonly hit when credentials are pasted from a welcome email. The password field is expected to remain untrimmed, since whitespace can be a legitimate part of a password — this report covers the username field only.

---

## BUG_004 — Oversized profile photo is rejected without stating the maximum permitted file size

| Field | Value |
|---|---|
| **Module** | PIM – Add Employee |
| **Test Case** | TC_020 |
| **Severity** | 🟡 Medium |
| **Priority** | Medium |
| **Status** | Open |
| **Screenshot** | `BUG_004.png` |

**Steps to Reproduce**

1. Log in as `Admin`
2. Navigate to **PIM → Add Employee**
3. Click the profile photo placeholder
4. Select a `.jpg` image of approximately 3 MB
5. Complete First Name and Last Name, then click **Save**
6. Read the error message displayed

**Expected Result**
The upload is rejected with a specific, actionable message naming the maximum permitted file size — for example *"File size exceeds the maximum limit of 1 MB"*.

**Actual Result**
The upload is rejected, but the message does not state the size limit. The user has no way to know how far over the limit the file is or what size would be accepted, so they must guess and retry.

**Notes**
Usability / error-handling defect rather than a functional break — the rejection itself works correctly, so no invalid data enters the system.

---

## BUG_005 — Employee Id field accepts special characters with no format validation

| Field | Value |
|---|---|
| **Module** | PIM – Add Employee |
| **Test Case** | TC_021 |
| **Severity** | 🟢 Low |
| **Priority** | Low |
| **Status** | Open |
| **Screenshot** | `BUG_005.png` |

**Steps to Reproduce**

1. Log in as `Admin`
2. Navigate to **PIM → Add Employee**
3. Enter First Name `Test` and Last Name `Record`
4. Clear the auto-generated Employee Id and enter `@@##$$%%`
5. Click **Save**
6. Navigate to **PIM → Employee List** and locate the record

**Expected Result**
The field validates its input and rejects special characters, or restricts the identifier to an alphanumeric format.

**Actual Result**
The record is saved successfully with the Employee Id `@@##$$%%` and the value is displayed verbatim in the Employee List. Invalid identifiers can therefore enter the employee master data and may break downstream reports or integrations.

**Notes**
Data-integrity defect. An empty Employee Id is also accepted, which may be intentional; that behaviour is not covered by this report.

---

## Severity & Priority Definitions

**Severity** — the technical impact of the defect on the application:

| Level | Definition |
|---|---|
| Critical | Data loss, security exposure, or a core function unusable with no workaround |
| High | A major function is broken or a significant control is missing; a workaround may exist |
| Medium | A function behaves incorrectly but the user can still complete the task |
| Low | Cosmetic, usability or minor validation issue with negligible functional impact |

**Priority** — how urgently a fix is required:

| Level | Definition |
|---|---|
| High | Fix before release |
| Medium | Fix in the current cycle |
| Low | Fix when capacity allows |
