# Test Plan
**Project**: Chat Application (MERN + Socket.io)
**Version**: 1.0
**Prepared By**: Swarup Padhy
**Status**: Active

---

## 1. Purpose

This document describes the overall testing approach for the Chat Application. It explains what will be tested, how it will be tested, what tools will be used, and what the team needs before and after testing begins.

The goal is to make sure the application works correctly for real users — that they can register, log in, send messages, and log out — and that it is secure enough to protect their data.

---

## 2. Testing Objectives

| # | Objective |
| :--- | :--- |
| 1 | Confirm that all authentication flows (signup, login, logout, session restore) work as expected. |
| 2 | Verify that users can send text messages and image attachments and that recipients receive them in real time. |
| 3 | Validate that the WebSocket (Socket.io) layer correctly manages connections and broadcasts online/offline status. |
| 4 | Confirm that backend APIs return the correct status codes and reject invalid or unauthorized requests. |
| 5 | Verify that the system is protected against basic security risks such as XSS injection and session hijacking. |
| 6 | Ensure every requirement in the Requirements Specification is covered by at least one test case. |

---

## 3. Scope

### What Is Being Tested

| Area | Description |
| :--- | :--- |
| **Authentication** | User registration (including boundary values), login, logout, and JWT cookie behavior. |
| **Messaging** | Sending and receiving text and image messages; loading historical chat data. |
| **Real-Time Behavior** | Socket.io message delivery, online/offline presence broadcasts, socket lifecycle on logout. |
| **API Validation** | HTTP status codes, request/response payload structure, and auth middleware enforcement. |
| **Security** | XSS input sanitization, duplicate email handling, JWT token reuse after logout. |
| **Session Handling** | Multi-tab sync, cookie persistence after browser restart. |

### What Is NOT Being Tested

| Area | Reason |
| :--- | :--- |
| **Load / Stress Testing** | Out of scope for this manual testing cycle. A limit of 50 burst messages is the boundary. |
| **Mobile Native Apps** | Only mobile-responsive web (browser) is in scope. |
| **Cloudinary / MongoDB Infrastructure** | Internal availability of third-party services is outside our control and scope. |

---

## 4. Testing Types and When They Are Used

| Phase | Testing Type | What It Checks | How It Is Done |
| :--- | :--- | :--- | :--- |
| Phase 1 | Functional Testing | Do core user flows work end to end? | Manual execution in the browser. |
| Phase 2 | Boundary and Negative Testing | Does the system handle edge cases and invalid inputs? | Entering boundary values and bad data manually. |
| Phase 3 | API Testing | Do backend endpoints return correct responses? | Postman requests directly to the API. |
| Phase 4 | Security Testing | Is the app protected against basic attacks? | XSS payloads and auth bypass attempts. |
| Phase 5 | Regression Testing | Did bug fixes break anything else? | Re-running affected test cases after fixes. |

---

## 5. Risk Assessment

These are the areas most likely to cause problems, based on the system analysis.

| Risk | Impact | How We Address It |
| :--- | :--- | :--- |
| JWT token not invalidated on logout | Critical | Manually re-insert the token after logout and check if session is restored. |
| WebSocket not closed on logout | High | Monitor the Network tab in DevTools before and after logout. |
| Duplicate accounts via case-variant emails | High | Attempt registration with same email in different cases and check the database. |
| Race conditions during rapid messaging | High | Send burst messages and verify ordering in chat history. |
| XSS injection via input fields | Critical | Paste script payloads in message, name, and profile fields and observe behavior. |
| Large image payloads crashing the server | Medium | Send oversized images and verify the 413 error is returned. |

---

## 6. Test Environment

| Item | Detail |
| :--- | :--- |
| Operating System | Windows 11 |
| Primary Browser | Google Chrome (latest) |
| Secondary Browser | Mozilla Firefox (for multi-tab/cross-browser tests) |
| Backend Runtime | Node.js v18+, running locally |
| Database | MongoDB (local instance) |
| Network Throttling | Chrome DevTools — No Throttling / Fast 3G / Slow 3G |
| Tools | Postman v10+, Chrome DevTools, MongoDB Compass |

---

## 7. Entry and Exit Criteria

### Entry Criteria (Testing Can Begin When)
- The backend server starts without errors and the database connection is confirmed.
- The React frontend loads in the browser without console errors.
- All requirements in `requirements.md` are reviewed and confirmed.
- Postman is configured with the base URL and at least one valid test user exists in the database.

### Exit Criteria (Testing Is Complete When)
- All test cases have been executed and their status recorded (Pass / Fail / Blocked).
- All discovered defects are documented in the Defect Log with reproduction steps and evidence.
- Every requirement maps to at least one executed test case in the RTM.

---

## 8. Deliverables

| Artifact | File |
| :--- | :--- |
| Requirements Specification | `01-requirements/requirements.md` |
| Test Plan (this document) | `02-test-plan/test-plan.md` |
| Test Scenarios | `03-test-design/test-scenarios.md` |
| Test Cases | `03-test-design/test-cases.md` |
| Defect Log | `04-defects/defect-log.md` |
| Requirements Traceability Matrix | `05-rtm/rtm.md` |
| Test Evidence | `evidence/BUG-00X/` folders |
