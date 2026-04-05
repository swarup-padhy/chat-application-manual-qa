# Defect Log
**Project**: Chat Application (MERN + Socket.io)
**Version**: 1.0
**Prepared By**: Alex (Swarup Padhy)

> This document is the single source of truth for all defects discovered during this testing cycle. Each entry includes reproduction steps, expected vs actual behavior, and evidence references.

---

## Defect Summary

| Bug ID | Title | Severity | Status | Test Case | Requirement |
| :--- | :--- | :--- | :--- | :--- | :--- |
| BUG-001 | Case-variant emails create duplicate accounts | 🔴 High | Open | TC-AUTH-04 | REQ-AUTH-07 |
| BUG-002 | JWT token remains valid after logout (server-side not invalidated) | 🔴 Critical | Open | TC-AUTH-09 | REQ-AUTH-05 |
| BUG-003 | Image messages fail to send in chat | 🔴 High | Open | TC-MSG-04 | REQ-MSG-03 |
| BUG-004 | WebSocket connection stays open after logout (Zombie Connection) | 🔴 High | Open | TC-TECH-02a | REQ-TECH-02 |

---

## BUG-001 — Case-Variant Emails Create Duplicate Accounts

| Field | Detail |
| :--- | :--- |
| **Bug ID** | BUG-001 |
| **Severity** | 🔴 High |
| **Status** | Open |
| **Test Case** | TC-AUTH-04 |
| **Requirement** | REQ-AUTH-07 |
| **Discovered** | Manual execution — Signup flow |
| **Evidence** | `evidence/BUG-001/` — MongoDB screenshot showing two separate records for `user@gmail.com` and `USER@gmail.com` |

**Description**

The system checks for duplicate emails in a case-sensitive way. This means `user@test.com` and `USER@test.com` are treated as two different email addresses. A user can register multiple accounts with the same real-world email simply by changing the letter casing.

**Preconditions**
- App is running.
- An account already exists for `user@test.com`.

**Steps to Reproduce**
1. Navigate to the Signup page.
2. Register an account with `user@test.com` and any valid password.
3. Log out.
4. Return to Signup and attempt to register with `USER@test.com`.
5. Open the database (e.g., MongoDB Compass) and check the `users` collection.

**Expected Result**
- Step 4 should return a **400 Bad Request** with the message *"Email already exists"*.
- Only one database record should exist for this email.

**Actual Result**
- Registration succeeds. Two separate records are created in the database — one for `user@test.com` and one for `USER@test.com`.

**Fix Recommendation**
In the `signup` controller, convert the email to lowercase before checking for duplicates and before saving the record:
```javascript
const email = req.body.email.toLowerCase();
const existingUser = await User.findOne({ email });
```

---

## BUG-002 — JWT Token Remains Valid After Logout

| Field | Detail |
| :--- | :--- |
| **Bug ID** | BUG-002 |
| **Severity** | 🔴 Critical |
| **Status** | Open |
| **Test Case** | TC-AUTH-09 |
| **Requirement** | REQ-AUTH-05 |
| **Discovered** | Exploratory testing — Logout and cookie inspection |
| **Evidence** | `evidence/BUG-002/` — Screen recording showing Dashboard access after logout using a copied JWT |

**Description**

When a user logs out, the application clears the `jwt` cookie from the browser. However, the token itself is never invalidated on the server. If the token value is copied before logout and manually placed back into the browser after logout, the server accepts it and restores the full session. This means anyone who obtains a valid JWT — through network interception, XSS, or physical access to the device — can access the account even after the legitimate user has logged out.

**Preconditions**
- User is logged in.
- The `jwt` cookie is visible in DevTools → Application → Cookies.

**Steps to Reproduce**
1. Log in to the application.
2. Open DevTools → Application → Cookies and copy the full `jwt` cookie value.
3. Click **Logout**. Confirm the cookie is gone and the Login page is shown.
4. In DevTools, manually create a new cookie named `jwt` and paste in the copied value.
5. Perform a hard page refresh.

**Expected Result**
- The server should reject the old token.
- The user should remain on the Login page.

**Actual Result**
- The user is fully re-authenticated and lands on the Dashboard with access to all private conversations and application state.

**Fix Recommendation**

The backend needs a way to track which tokens have been invalidated. Three common approaches:

1. **Token Blacklist** — Store invalidated tokens in a fast-access store (like Redis) until they expire naturally. The `checkAuth` middleware checks this list before granting access.
2. **Session Table** — Store active sessions in MongoDB. Logout deletes the session record. The middleware checks for an active session.
3. **Logout Timestamp** — Add a `lastLogoutAt` timestamp field to the `User` model. On logout, update it. In the middleware, reject any JWT whose `iat` (issued-at time) is older than `lastLogoutAt`.

---

## BUG-003 — Image Messages Fail to Send in Chat

| Field | Detail |
| :--- | :--- |
| **Bug ID** | BUG-003 |
| **Severity** | 🔴 High |
| **Status** | Open — Investigation Required |
| **Test Case** | TC-MSG-04 |
| **Requirement** | REQ-MSG-03 |
| **Discovered** | Manual execution — Messaging flow |
| **Evidence** | `evidence/BUG-003/` — Screenshot of send failure in chat UI |

**Description**

Users cannot send image attachments inside the chat message area. Profile picture uploads to Cloudinary work correctly, but the same type of image fails when sent as a message. The exact root cause has not yet been confirmed and requires backend log investigation.

**Preconditions**
- User is logged in.
- A contact is selected in the sidebar.

**Steps to Reproduce**
1. Open a chat with any contact.
2. Click the image attachment icon in the message input area.
3. Select a valid PNG or JPG image file (under 500KB).
4. Click **Send**.

**Expected Result**
- `POST /api/messages/send/:id` returns **201 Created**.
- The image appears in the chat for both the sender and the recipient.
- A valid Cloudinary URL is returned in the `image` field of the response.

**Actual Result**
- The image fails to send. An error is returned. The message does not appear in the chat.

**Investigation Notes**
- Profile picture uploads (via `PUT /api/auth/update-profile`) work correctly, which suggests Cloudinary itself is available.
- The issue is likely in how the `sendMessage` function in `message.controller.js` handles or passes the base64 image string to Cloudinary — compare it against the working `updateProfile` function in `auth.controller.js`.
- Backend server logs are needed to confirm the exact error.

---

## BUG-004 — WebSocket Stays Open After Logout (Zombie Connection)

| Field | Detail |
| :--- | :--- |
| **Bug ID** | BUG-004 |
| **Severity** | 🔴 High |
| **Status** | Open |
| **Test Case** | TC-TECH-02a |
| **Requirement** | REQ-TECH-02 |
| **Discovered** | Manual execution — DevTools Network tab monitoring during logout |
| **Evidence** | `evidence/BUG-004/before-logout.png`, `evidence/BUG-004/after-logout.png` |

**Description**

When a user logs out, the client-side app returns to the Login page and clears the `jwt` cookie. However, the underlying WebSocket (Socket.io) connection is never explicitly closed. The connection continues to run on the server, consuming resources and keeping the user's socket entry alive in the server's `userSocketMap`. This can cause presence status to remain as "Online" after logout and may allow socket events to continue reaching a session that should no longer exist.

**Preconditions**
- User is logged in.
- DevTools is open → Network tab → filtered by `socket.io`.

**Steps to Reproduce**
1. Log in to the application.
2. In the Network tab, confirm the WebSocket connection is active — look for `transport=websocket` with Status `101` and a `Pending` time value.
3. Click **Logout**.
4. Continue watching the Network tab.

**Expected Result**
- The WebSocket row should turn red and stop updating — indicating the connection was closed by the client.
- In a second browser (logged in as another user), the logged-out user's status should switch from Online to Offline immediately.

**Actual Result**
- The WebSocket connection remained active with Status `101` for more than 2.5 minutes after logout.
- The app had already navigated to the Login page, confirming the UI portion of logout worked — but the socket was never closed.

**Evidence Summary**

| File | What It Shows |
| :--- | :--- |
| `before-logout.png` | App on Dashboard. WebSocket row: Status `101`, Time: `Pending`. |
| `after-logout.png` | App on Login page (user logged out). Same WebSocket row still shows Status `101`, Time: `2.5 min`. |

**Fix Recommendation**

In the frontend, the `logout` function must explicitly disconnect the Socket.io client before or at the same time as clearing the auth state. In the authentication store (likely `useAuthStore`):

```javascript
// Inside the logout function
socket.disconnect();
```

This ensures the client actively drops the connection, which triggers the server's `disconnect` event and removes the user from `userSocketMap`.
