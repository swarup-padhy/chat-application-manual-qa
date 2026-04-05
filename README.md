# 🧪 Chat Application — Manual Testing Project

> A structured manual testing engagement covering core functional, security, API, and real-time requirements of a fullstack MERN chat application. **4 real defects discovered — including a Critical security vulnerability.**

---

![Test Cases](https://img.shields.io/badge/Test%20Cases-29-brightgreen?style=flat-square)
![Passed](https://img.shields.io/badge/Passed-25-success?style=flat-square)
![Failed](https://img.shields.io/badge/Failed-4-critical?style=flat-square)
![Defects](https://img.shields.io/badge/Defects%20Found-4%20Open-red?style=flat-square)
![Coverage](https://img.shields.io/badge/Requirement%20Coverage-100%25-blue?style=flat-square)
![Status](https://img.shields.io/badge/Cycle%20Status-Complete-blueviolet?style=flat-square)

---

## 🐛 Defects Discovered

> These were found through manual exploratory and structured testing — not guesswork.

| Bug ID | Title | Severity | How It Was Found |
| :--- | :--- | :--- | :--- |
| [BUG-001](04-defects/defect-log.md) | Case-variant emails bypass duplicate account check | 🔴 High | Structured test — attempted registration with `USER@test.com` after `user@test.com` existed |
| [BUG-002](04-defects/defect-log.md) | JWT token remains valid after logout — session not invalidated server-side | 🔴 **Critical** | Exploratory — copied JWT before logout, re-inserted it after, regained full Dashboard access |
| [BUG-003](04-defects/defect-log.md) | Image messages fail to send in chat | 🔴 High | Structured test — image attachment sent via messaging flow, server returned error |
| [BUG-004](04-defects/defect-log.md) | WebSocket connection stays open after logout (Zombie Connection) | 🔴 High | Exploratory — monitored Network tab in DevTools before and after logout |

---

## 📊 Execution Summary

| Status | Count | Visual |
| :--- | :---: | :--- |
| ✅ Passed | 25 | `████████████████████░░░` 86% |
| ❌ Failed | 4 | `██░░░░░░░░░░░░░░░░░░░░░` 14% |
| **Total Executed** | **29** | **100%** |

---

## 🧰 Testing Techniques Applied

| Technique | Where It Was Used |
| :--- | :--- |
| **Boundary Value Analysis** | Password length — verified 5 chars fails, 6 chars passes |
| **Exploratory Testing** | Discovered BUG-002 (JWT reuse) and BUG-004 (zombie socket) without predefined steps |
| **Negative Testing** | Wrong passwords, malformed API requests, unauthorized endpoint access |
| **Cookie Inspection** | DevTools used to verify `HttpOnly`, `Secure`, `SameSite=Strict` JWT flags |
| **WebSocket Monitoring** | Network tab observed before and after logout to detect unclosed socket |
| **Database Verification** | MongoDB Compass used to confirm Bcrypt hashing and duplicate email records |
| **XSS Injection** | Script payloads tested in chat input and signup name fields |
| **Multi-Session Testing** | Chrome + Edge used simultaneously for real-time presence verification |
| **API Testing** | All 8 endpoints tested via Postman — status codes, auth guards, payload validation |

---

## 🔍 Scope — What Was Tested

| Area | Coverage |
| :--- | :--- |
| 🔐 **Authentication** | Signup (boundary values), Login, Logout, JWT lifecycle, session restore, route protection |
| 💬 **Messaging** | Text and image sending, chat history loading, payload size limits, message consistency |
| ⚡ **Real-Time (Socket.io)** | Live message delivery, online/offline presence broadcast, socket closure on logout |
| 🛡️ **Security** | XSS injection, JWT reuse after logout, duplicate email bypass, sender identity verification |
| 🔌 **API Validation** | Auth middleware enforcement, 401/400/413 error handling, malformed request rejection |
| 🪟 **Session Handling** | Multi-tab session sync, persistence after full browser restart |

---

## 🔗 Traceability

Every test case connects back to a documented requirement. Every defect connects back to a test case.

```
REQ  ──►  TS  ──►  TC  ──►  BUG
(What)  (Behavior) (Steps) (Finding)
```

**Real example from this project:**
```
REQ-AUTH-07        ──►  TS-AUTH-02       ──►  TC-AUTH-04          ──►  BUG-001
Duplicate email         Registration          Case-variant               Two accounts
must be rejected        must fail             email test                 created in DB
```

→ Full traceability map: [`05-rtm/rtm.md`](05-rtm/rtm.md)

---

## 📁 Repository Structure

```
chat-app-manual-testing/
│
├── 📄 README.md
├── 📁 01-requirements/
│   └── requirements.md       ← 21 functional and technical requirements
├── 📁 02-test-plan/
│   └── test-plan.md          ← Strategy, scope, risks, environment, entry/exit criteria
├── 📁 03-test-design/
│   ├── test-scenarios.md     ← 26 high-level test scenarios
│   ├── test-cases.md         ← 29 detailed test cases with execution status
│   └── api-inventory.md      ← 8 API endpoints mapped to test cases
├── 📁 04-defects/
│   └── defect-log.md         ← 4 defects with reproduction steps, evidence, fix notes
├── 📁 05-rtm/
│   └── rtm.md                ← REQ → TS → TC → BUG traceability matrix
└── 📁 evidence/
    ├── BUG-001/              ← MongoDB screenshot: duplicate email records
    ├── BUG-002/              ← Recording: JWT reuse after logout
    ├── BUG-003/              ← Screenshot: image send failure
    └── BUG-004/              ← Before/after screenshots: zombie WebSocket
```

---

## 📌 Project Overview

| Field | Detail |
| :--- | :--- |
| **Application** | Fullstack Chat App — MERN + Socket.io |
| **Source** | [github.com/burakorkmez/fullstack-chat-app](https://github.com/burakorkmez/fullstack-chat-app) |
| **Tester** | Swarup Padhy |
| **Environment** | Windows 11 · Chrome · Node.js (Local) · MongoDB |
| **Tools** | Postman · Chrome DevTools · MongoDB Compass · Firefox |
