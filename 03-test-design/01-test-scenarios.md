# Test Scenarios
**Project**: Chat Application (MERN + Socket.io)
**Version**: 1.0

> A test scenario is a single, high-level behavior we want to verify. Each scenario is linked to a specific requirement and is later broken down into one or more detailed test cases.

---

## 1. User Authentication (AUTH)

| Scenario ID | Requirement | Description | Priority |
| :--- | :--- | :--- | :--- |
| **TS-AUTH-01** | REQ-AUTH-01 | Verify that a new user can successfully register with valid details. | High |
| **TS-AUTH-02** | REQ-AUTH-07 | Verify that registration is rejected when the email address is already taken (case-insensitive). | High |
| **TS-AUTH-03** | REQ-AUTH-01 | Verify that registration fails if the password is shorter than 6 characters. | Medium |
| **TS-AUTH-04** | REQ-AUTH-02 | Verify that a registered user can log in with the correct email and password. | Critical |
| **TS-AUTH-05** | REQ-AUTH-08 | Verify that login fails when the wrong password is used. | High |
| **TS-AUTH-06** | REQ-AUTH-03 | Verify that a secure `jwt` cookie is created with the correct security properties after login. | Critical |
| **TS-AUTH-07** | REQ-AUTH-05 | Verify that logging out clears the session and removes the `jwt` cookie. | High |
| **TS-AUTH-08** | REQ-AUTH-06 | Verify that visiting a protected page (e.g. `/profile`) without being logged in redirects to Login. | High |
| **TS-AUTH-09** | REQ-AUTH-04 | Verify that the session is automatically restored when the user refreshes the page. | High |

---

## 2. Messaging and Communication (MSG)

| Scenario ID | Requirement | Description | Priority |
| :--- | :--- | :--- | :--- |
| **TS-MSG-01** | REQ-MSG-01 | Verify that the sidebar shows an up-to-date list of all other registered users after login. | High |
| **TS-MSG-02** | REQ-MSG-02 | Verify that selecting a contact loads the correct chat history in the correct order. | High |
| **TS-MSG-03** | REQ-MSG-03 | Verify that a user can successfully send a plain text message. | Critical |
| **TS-MSG-04** | REQ-MSG-03 | Verify that a user can successfully send an image as a message. | High |
| **TS-MSG-05** | REQ-MSG-06 | Verify that the system rejects a message payload that exceeds the size limit with a 413 error. | Medium |
| **TS-MSG-06** | REQ-MSG-07 | Verify that chat history is still correct and complete after a page refresh. | High |

---

## 3. Real-Time and Socket Behavior (REAL)

| Scenario ID | Requirement | Description | Priority |
| :--- | :--- | :--- | :--- |
| **TS-REAL-01** | REQ-MSG-04 | Verify that a message sent by one user appears in the recipient's chat immediately, without a refresh. | Critical |
| **TS-REAL-02** | REQ-MSG-04 | Verify that sending messages rapidly does not produce duplicate entries. | Critical |
| **TS-REAL-03** | REQ-MSG-05 | Verify that a user's status changes to "Online" for all other users as soon as they log in. | High |
| **TS-REAL-04** | REQ-MSG-05 | Verify that a user's status changes to "Offline" for all other users as soon as they log out. | High |

---

## 4. Profile Management (PRF)

| Scenario ID | Requirement | Description | Priority |
| :--- | :--- | :--- | :--- |
| **TS-PRF-01** | REQ-PRF-01 | Verify that a user can upload and save a new profile picture successfully. | Medium |
| **TS-PRF-02** | REQ-PRF-02 | Verify that the profile page displays the correct account details (name, email, date). | Medium |
| **TS-PRF-03** | REQ-PRF-03 | Verify that the system shows an appropriate error message when a profile picture upload fails. | Low |

---

## 5. Technical and Security (TECH)

| Scenario ID | Requirement | Description | Priority |
| :--- | :--- | :--- | :--- |
| **TS-TECH-01** | REQ-TECH-01 | Verify that passwords are stored as Bcrypt hashes in the database — never as plain text. | Critical |
| **TS-TECH-02** | REQ-TECH-02 | Verify that exactly one WebSocket connection exists per user session and that it is closed on logout. | High |
| **TS-TECH-03** | REQ-TECH-03 | Verify that messages are displayed in the correct timestamp order, even under rapid-send conditions. | High |
| **TS-TECH-04** | N/A | Verify that the system does not execute injected scripts (XSS) entered in any input field. | Critical |
