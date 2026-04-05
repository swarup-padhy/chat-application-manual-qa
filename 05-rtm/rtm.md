# Requirements Traceability Matrix (RTM)
**Project**: Chat Application (MERN + Socket.io)
**Version**: 1.0
**Prepared By**: Alex (Swarup Padhy)

---

## Coverage Summary

| Category | Total Requirements | Scenarios Written | Test Cases Written | Defects Found |
| :--- | :--- | :--- | :--- | :--- |
| Authentication (AUTH) | 8 | 9 | 10 | 2 (BUG-001, BUG-002) |
| Messaging (MSG) | 7 | 6 | 7 | 1 (BUG-003) |
| Real-Time / Socket (REAL) | Covered under MSG | 4 | 4 | 1 (BUG-004) |
| Profile Management (PRF) | 3 | 3 | 3 | 0 |
| Technical / Security (TECH) | 3 | 4 | 5 | 0 (execution pending) |
| **Total** | **21** | **26** | **29+** | **4** |

---

## Full Traceability Map

### 1. Authentication Requirements

| Requirement ID | Requirement Name | Scenario ID | Test Case ID | Execution Status | Defect |
| :--- | :--- | :--- | :--- | :--- | :--- |
| REQ-AUTH-01 | User Registration | TS-AUTH-01 | TC-AUTH-01 | ✅ Pass | — |
| REQ-AUTH-01 | User Registration (boundary: 5 chars) | TS-AUTH-03 | TC-AUTH-02 | ✅ Pass | — |
| REQ-AUTH-01 | User Registration (boundary: 6 chars) | TS-AUTH-01 | TC-AUTH-03 | ✅ Pass | — |
| REQ-AUTH-02 | User Login | TS-AUTH-04 | TC-AUTH-05 | ✅ Pass | — |
| REQ-AUTH-03 | Session Persistence | TS-AUTH-06 | TC-AUTH-06 | ✅ Pass | — |
| REQ-AUTH-03 | Multi-tab session sync | — | TC-SESSION-10 | ✅ Pass | — |
| REQ-AUTH-04 | Session Restoration | TS-AUTH-09 | TC-AUTH-08 | ✅ Pass | — |
| REQ-AUTH-04 | Session after browser restart | — | TC-SESSION-11 | ✅ Pass | — |
| REQ-AUTH-05 | Secure Logout | TS-AUTH-07 | TC-AUTH-09 | ❌ Fail | BUG-002 |
| REQ-AUTH-06 | Route Protection | TS-AUTH-08 | TC-AUTH-10 | ✅ Pass | — |
| REQ-AUTH-07 | Duplicate Email Prevention | TS-AUTH-02 | TC-AUTH-04 | ❌ Fail | BUG-001 |
| REQ-AUTH-08 | Error Feedback (wrong password) | TS-AUTH-05 | TC-AUTH-05 | ✅ Pass | — |
| REQ-AUTH-08 | Error Feedback (malformed request) | TS-AUTH-08 | TC-API-11 | ✅ Pass | — |

---

### 2. Messaging Requirements

| Requirement ID | Requirement Name | Scenario ID | Test Case ID | Execution Status | Defect |
| :--- | :--- | :--- | :--- | :--- | :--- |
| REQ-MSG-01 | Contact List | TS-MSG-01 | TC-MSG-01 | ✅ Pass | — |
| REQ-MSG-01 | API auth guard on contact list | TS-MSG-03 | TC-API-10 | ✅ Pass | — |
| REQ-MSG-02 | Chat History | TS-MSG-02 | TC-MSG-02 | ✅ Pass | — |
| REQ-MSG-03 | Send Text Message | TS-MSG-03 | TC-MSG-03 | ✅ Pass | — |
| REQ-MSG-03 | Send Image Message | TS-MSG-04 | TC-MSG-04 | ❌ Fail | BUG-003 |
| REQ-MSG-04 | Real-Time Delivery | TS-REAL-01 | TC-REAL-01 | ✅ Pass | — |
| REQ-MSG-04 | Duplicate message prevention | TS-REAL-02 | TC-REAL-04 | ✅ Pass | — |
| REQ-MSG-04 | Sender identity verification | TS-MSG-04 | TC-API-12 | ✅ Pass | — |
| REQ-MSG-04 | Real-time mixed media ordering | TS-MSG-04 | TC-REAL-10 | ✅ Pass | — |
| REQ-MSG-05 | Online/Offline Status — Login | TS-REAL-03 | TC-REAL-02 | ✅ Pass | — |
| REQ-MSG-05 | Online/Offline Status — Logout | TS-REAL-04 | TC-REAL-03 | ✅ Pass | — |
| REQ-MSG-06 | File Size Limit (at limit) | TS-MSG-05 | TC-MSG-05 | ✅ Pass | — |
| REQ-MSG-06 | File Size Limit (above limit → 413) | TS-MSG-05 | TC-MSG-06 | ✅ Pass | — |
| REQ-MSG-07 | Message Consistency after refresh | TS-MSG-06 | TC-MSG-07 | ✅ Pass | — |

---

### 3. Profile Management Requirements

| Requirement ID | Requirement Name | Scenario ID | Test Case ID | Execution Status | Defect |
| :--- | :--- | :--- | :--- | :--- | :--- |
| REQ-PRF-01 | Profile Picture Upload | TS-PRF-01 | TC-PRF-01 | ✅ Pass | — |
| REQ-PRF-02 | View Profile Details | TS-PRF-02 | TC-PRF-03 | ✅ Pass | — |
| REQ-PRF-03 | Upload Failure Handling | TS-PRF-03 | TC-PRF-02 | ✅ Pass | — |

---

### 4. Technical and Security Requirements

| Requirement ID | Requirement Name | Scenario ID | Test Case ID | Execution Status | Defect |
| :--- | :--- | :--- | :--- | :--- | :--- |
| REQ-TECH-01 | Password Hashing | TS-TECH-01 | TC-TECH-01 | ✅ Pass | — |
| REQ-TECH-02 | Socket Lifecycle — closure on logout | TS-TECH-02 | TC-TECH-02a | ❌ Fail | BUG-004 |
| REQ-TECH-03 | Message Ordering under rapid send | TS-TECH-03 | TC-TECH-03 | ✅ Pass | — |
| N/A | XSS — Chat message input | TS-TECH-04 | TC-TECH-04 | ✅ Pass | — |
| N/A | XSS — Signup name field | TS-TECH-04 | TC-TECH-05 | ✅ Pass | — |

---

## Defect Traceability

| Defect ID | Linked Requirement | Linked Test Case | Severity | Status |
| :--- | :--- | :--- | :--- | :--- |
| BUG-001 | REQ-AUTH-07 | TC-AUTH-04 | 🔴 High | Open |
| BUG-002 | REQ-AUTH-05 | TC-AUTH-09 | 🔴 Critical | Open |
| BUG-003 | REQ-MSG-03 | TC-MSG-04 | 🔴 High | Open |
| BUG-004 | REQ-TECH-02 | TC-TECH-02a | 🔴 High | Open |

---

## API Endpoint Coverage

This confirms each API endpoint is covered by at least one test case.

| Endpoint | Method | Covered By |
| :--- | :--- | :--- |
| `/auth/signup` | POST | TC-AUTH-01, TC-AUTH-02, TC-AUTH-03, TC-AUTH-04 |
| `/auth/login` | POST | TC-AUTH-05, TC-AUTH-07 |
| `/auth/logout` | POST | TC-AUTH-09 |
| `/auth/update-profile` | PUT | TC-PRF-01, TC-PRF-02 |
| `/auth/check` | GET | TC-AUTH-08, TC-SESSION-11 |
| `/messages/users` | GET | TC-MSG-01, TC-API-10 |
| `/messages/:id` | GET | TC-MSG-02 |
| `/messages/send/:id` | POST | TC-MSG-03, TC-MSG-04, TC-MSG-05, TC-MSG-06, TC-API-12 |

---

## Coverage Status

| Status | Count | Percentage |
| :--- | :--- | :--- |
| ✅ Pass | 25 | 86% |
| ❌ Fail | 4 | 14% |
| **Total** | **29** | **100%** |

> **Note**: All 29 test cases have been executed. 25 passed. 4 failed and are logged as open defects.
