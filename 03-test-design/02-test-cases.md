# Test Cases
**Project**: Chat Application (MERN + Socket.io)
**Version**: 1.0

> Each test case below maps to a scenario in `test-scenarios.md`, which in turn maps to a requirement in `requirements.md`. The **Execution Status** column reflects the result of the current testing cycle.

**Status Key**

| Status | Meaning |
| :--- | :--- |
| ✅ Pass | Test executed. System behaved as expected. |
| ❌ Fail | Test executed. System did NOT behave as expected. Defect logged. |
| ⚠️ Blocked | Test could not be executed due to a dependency or environment issue. |
| ⬜ Not Executed | Test has not been run yet in this cycle. |

---

## Section 1 — Authentication (AUTH)

---

**TC-AUTH-01** · Successful registration with valid data

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-01 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | App is open on the Signup page. No prior account exists for the test email. |
| **Test Data** | Name: `John Doe` · Email: `john@test.com` · Password: `password123` |

**Steps**
1. Enter the full name.
2. Enter a unique email address.
3. Enter a password with 6 or more characters.
4. Click **Create Account**.

**Expected Result**
- User is redirected to the Dashboard.
- `POST /api/auth/signup` returns **201 Created**.
- The response body contains the user's `fullName` and `email`.

---

**TC-AUTH-02** · Registration fails — password is exactly 5 characters (boundary)

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-03 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | App is open on the Signup page. |
| **Test Data** | Name: `Test User` · Email: `boundary@test.com` · Password: `12345` |

**Steps**
1. Enter a valid name and unique email.
2. Enter a password that is exactly **5 characters** long.
3. Click **Create Account**.

**Expected Result**
- `POST /api/auth/signup` returns **400 Bad Request**.
- Error message displayed: *"Password must be at least 6 characters"*.

---

**TC-AUTH-03** · Registration succeeds — password is exactly 6 characters (boundary)

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-01 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | App is open on the Signup page. |
| **Test Data** | Name: `Test User` · Email: `boundary2@test.com` · Password: `123456` |

**Steps**
1. Enter a valid name and unique email.
2. Enter a password that is exactly **6 characters** long.
3. Click **Create Account**.

**Expected Result**
- `POST /api/auth/signup` returns **201 Created**.
- User is authenticated and redirected to the Dashboard.

---

**TC-AUTH-04** · Registration fails — duplicate email (case-insensitive check)

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-02 |
| **Priority** | High |
| **Execution Status** | ❌ Fail → [BUG-001] |
| **Precondition** | An account with `user@test.com` already exists in the database. |
| **Test Data** | Email: `USER@test.com` |

**Steps**
1. Navigate to the Signup page.
2. Attempt to register using `USER@test.com` (uppercase variant of an existing email).
3. Click **Create Account**.

**Expected Result**
- `POST /api/auth/signup` returns **400 Bad Request**.
- Error message displayed: *"Email already exists"*.

**Actual Result**
- Registration succeeded. A second, separate account was created in the database for `USER@test.com`, confirming the system is treating emails as case-sensitive.

---

**TC-AUTH-05** · Login fails — incorrect password

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-05 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | App is open on the Login page. A registered account exists. |
| **Test Data** | Email: `john@test.com` · Password: `wrongpassword` |

**Steps**
1. Enter the registered email.
2. Enter an incorrect password.
3. Click **Login**.

**Expected Result**
- `POST /api/auth/login` returns **400 Bad Request**.
- Error message displayed: *"Invalid credentials"*.

---

**TC-AUTH-06** · JWT cookie has correct security properties after login

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-06 |
| **Priority** | Critical |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is successfully logged in. |
| **Test Data** | N/A |

**Steps**
1. Open DevTools → **Application** tab → **Cookies** → select `http://localhost`.
2. Locate the cookie named `jwt`.
3. Check the `HttpOnly`, `Secure`, and `SameSite` columns.
4. Open the **Console** tab and run `document.cookie`.

**Expected Result**
- `jwt` cookie is present.
- `HttpOnly` column is **checked** (✅).
- `SameSite` value is **Strict**.
- Running `document.cookie` in the Console does **not** show the `jwt` value — confirming JavaScript cannot access it.

---

**TC-AUTH-07** · Login fails — unregistered email

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-05 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | App is open on the Login page. |
| **Test Data** | Email: `notexist@test.com` · Password: `anypassword` |

**Steps**
1. Enter an email address that has never been registered.
2. Enter any password.
3. Click **Login**.

**Expected Result**
- `POST /api/auth/login` returns **400 Bad Request**.
- Error message displayed: *"Invalid credentials"*.

---

**TC-AUTH-08** · Session is restored automatically after page refresh

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-09 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in and on the Dashboard. |
| **Test Data** | N/A |

**Steps**
1. While logged in, press **F5** (hard refresh).
2. Observe what happens after the page reloads.
3. Check the Network tab for a call to `/api/auth/check`.

**Expected Result**
- The app calls `GET /api/auth/check` automatically on page load.
- API returns **200 OK** with the user's data.
- User remains on the Dashboard — no login prompt appears.

---

**TC-AUTH-09** · Session is fully invalidated after logout

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-07 |
| **Priority** | High |
| **Execution Status** | ❌ Fail → [BUG-002] |
| **Precondition** | User is logged in. The `jwt` cookie is visible in DevTools. |
| **Test Data** | N/A |

**Steps**
1. Copy the full value of the `jwt` cookie from DevTools → Application → Cookies.
2. Click the **Logout** button.
3. Confirm the user is redirected to the Login page and the `jwt` cookie is gone.
4. Manually re-add the cookie in DevTools with the copied value.
5. Perform a hard page refresh.

**Expected Result**
- `POST /api/auth/logout` returns **200 OK**.
- The `jwt` cookie is cleared from the browser.
- After manually re-inserting the old token and refreshing, the backend should **reject** it and keep the user on the Login page.

**Actual Result**
- Cookie was cleared and user was redirected to Login (first 3 steps passed).
- After re-inserting the old token, the user was **fully re-authenticated** and landed on the Dashboard without entering credentials — confirming the token is never invalidated on the server side.

---

**TC-AUTH-10** · Protected route redirects unauthenticated users

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-08 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged out. |
| **Test Data** | URL: `/profile` |

**Steps**
1. While not logged in, type `/profile` directly in the browser address bar.
2. Press Enter.

**Expected Result**
- The system detects there is no valid session.
- User is immediately redirected to the Login page.

---

## Section 2 — Messaging (MSG)

---

**TC-MSG-01** · Sidebar contact list loads all other users

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-MSG-01 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in. At least one other registered user exists. |
| **Test Data** | N/A |

**Steps**
1. Log in and observe the sidebar.

**Expected Result**
- `GET /api/messages/users` returns **200 OK**.
- All other registered users appear in the sidebar list.

---

**TC-MSG-02** · Chat history loads when a contact is selected

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-MSG-02 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in. At least one previous message exists with the selected contact. |
| **Test Data** | N/A |

**Steps**
1. Click on a contact in the sidebar.

**Expected Result**
- `GET /api/messages/:userID` returns **200 OK**.
- Previous messages load in the chat area in the correct chronological order.

---

**TC-MSG-03** · Text message is sent successfully

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-MSG-03 |
| **Priority** | Critical |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in with a contact selected. |
| **Test Data** | Message: `Hello world` |

**Steps**
1. Type `Hello world` in the message input field.
2. Press **Enter** or click **Send**.

**Expected Result**
- `POST /api/messages/send/:id` returns **201 Created**.
- Response body contains the correct `text` value and a unique `_id`.
- The message bubble appears in the chat window.

---

**TC-MSG-04** · Image attachment is sent successfully

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-MSG-04 |
| **Priority** | High |
| **Execution Status** | ❌ Fail → [BUG-003] |
| **Precondition** | User is logged in with a contact selected. |
| **Test Data** | Image: `sample.png` (valid PNG, under 500KB) |

**Steps**
1. Click the image attachment icon in the message input area.
2. Select a valid image file.
3. Click **Send**.

**Expected Result**
- `POST /api/messages/send/:id` returns **201 Created**.
- Response body contains a valid Cloudinary URL in the `image` field.
- The image renders correctly in the chat for both sender and recipient.

**Actual Result**
- Image failed to send. Server returned an error. Root cause requires further backend log investigation.

---

**TC-MSG-05** · Payload at exactly the size limit is accepted

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-MSG-05 |
| **Priority** | Medium |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in with a contact selected. |
| **Test Data** | Image: exactly 500KB |

**Steps**
1. Upload an image file that is exactly at the defined payload limit.
2. Click **Send**.

**Expected Result**
- `POST /api/messages/send/:id` returns **201 Created**.
- Image renders correctly for the recipient.

---

**TC-MSG-06** · Payload above the size limit is rejected with 413

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-MSG-05 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in with a contact selected. |
| **Test Data** | Image: 10MB file |

**Steps**
1. Upload an image file that is clearly above the payload limit (e.g., 10MB).
2. Click **Send**.

**Expected Result**
- `POST /api/messages/send/:id` returns **413 Request Entity Too Large**.
- An appropriate error is shown in the UI.

---

**TC-MSG-07** · Chat history remains correct after page refresh

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-MSG-06 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | An active conversation exists with at least one sent message. |
| **Test Data** | N/A |

**Steps**
1. Send a text message.
2. Perform a hard refresh (F5).
3. Reopen the same conversation.

**Expected Result**
- The message re-appears correctly in the chat history.
- No messages are missing or duplicated.

---

## Section 3 — Real-Time Behavior (REAL)

---

**TC-REAL-01** · Message is received in real time without refresh

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-REAL-01 |
| **Priority** | Critical |
| **Execution Status** | ✅ Pass |
| **Precondition** | Two users are logged in simultaneously (e.g., Chrome = User A, Edge = User B). Both have each other selected in chat. |
| **Test Data** | N/A |

**Steps**
1. From User A's browser, send a text message.
2. Observe User B's browser immediately — do not refresh.

**Expected Result**
- The message appears in User B's chat window instantly.
- The `newMessage` socket event is visible in User B's DevTools → Network → WS → Messages tab.

---

**TC-REAL-02** · "Online" status updates immediately when a peer logs in

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-REAL-03 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | User A is logged in. User B is logged out. User A can see User B in the sidebar. |
| **Test Data** | N/A |

**Steps**
1. While User A is watching the sidebar, User B logs in from another browser.

**Expected Result**
- User B's status indicator in User A's sidebar changes from grey (Offline) to green (Online) immediately.

---

**TC-REAL-03** · "Offline" status updates immediately when a peer logs out

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-REAL-04 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | Both User A and User B are logged in. User B shows as Online in User A's sidebar. |
| **Test Data** | N/A |

**Steps**
1. User B clicks **Logout**.
2. Observe User A's sidebar immediately.

**Expected Result**
- User B's status indicator in User A's sidebar switches from green (Online) to grey (Offline) immediately.

---

**TC-REAL-04** · Rapid message sending does not produce duplicates

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-REAL-02 |
| **Priority** | Critical |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in with a contact selected. |
| **Test Data** | Message: `Don't double me` |

**Steps**
1. Type the message in the input field.
2. Click **Send** rapidly 5 times in quick succession.
3. After sending, hard refresh and reopen the conversation.
4. Check `GET /api/messages/:id` response for the number of records.

**Expected Result**
- Only **one** copy of the message is stored in the database.
- Only one message bubble appears in the chat.

---

## Section 4 — Profile Management (PRF)

---

**TC-PRF-01** · Profile picture is updated successfully

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-PRF-01 |
| **Priority** | Medium |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in and on the Profile page. |
| **Test Data** | Image: `avatar.png` (valid file) |

**Steps**
1. Click the profile picture upload area.
2. Select a valid image file.
3. Save / confirm the upload.
4. Perform a hard refresh.

**Expected Result**
- `PUT /api/auth/update-profile` returns **200 OK**.
- The new profile picture is still visible after the page refreshes (confirming Cloudinary storage).

---

**TC-PRF-02** · System handles profile picture upload failure gracefully

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-PRF-03 |
| **Priority** | Low |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is on the Profile page. |
| **Test Data** | Invalid payload (e.g., a corrupted or non-image file) |

**Steps**
1. Attempt to upload an invalid file as a profile picture.

**Expected Result**
- `PUT /api/auth/update-profile` returns **400 Bad Request**.
- The UI displays an appropriate error message.
- The user's previous profile picture is preserved.

---

**TC-PRF-03** · Profile page displays correct account metadata

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-PRF-02 |
| **Priority** | Low |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in. |
| **Test Data** | N/A |

**Steps**
1. Navigate to the Profile page.

**Expected Result**
- The correct `fullName` and `email` are displayed.
- The current `profilePic` renders without error.
- Fields are read-only (no edit inputs visible by default).

---

## Section 5 — Technical and Security (TECH)

---

**TC-TECH-01** · Passwords are hashed in the database

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-TECH-01 |
| **Priority** | Critical |
| **Execution Status** | ✅ Pass |
| **Precondition** | A user has successfully registered. Database access is available (e.g., via MongoDB Compass). |
| **Test Data** | N/A |

**Steps**
1. Register a new user.
2. Open the database and inspect the `password` field in the `users` collection.
3. Also verify no API response returns the `password` field in plain text.

**Expected Result**
- The stored password begins with `$2a$10$` — the Bcrypt hash signature.
- The plain text password is **never** returned in any API response.

---

**TC-TECH-02a** · WebSocket connection is closed when the user logs out

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-TECH-02 |
| **Priority** | High |
| **Execution Status** | ❌ Fail → [BUG-004] |
| **Precondition** | User is logged in. DevTools is open on the Network tab, filtered by `socket.io`. |
| **Test Data** | N/A |

**Steps**
1. After login, confirm the WebSocket connection is active (Status: `101`, Type: `websocket`).
2. Click **Logout**.
3. Observe the WebSocket row in the Network tab.

**Expected Result**
- The WebSocket connection row turns red and the status changes, indicating the connection was closed.
- In a second browser (logged in as a different user), the logged-out user's status switches to Offline immediately.

**Actual Result**
- The WebSocket remained active (Status `101`) for over 2.5 minutes after logout.
- The app had already returned to the Login page, confirming the UI logout happened — but the socket connection was never closed.

---

**TC-TECH-03** · Messages are ordered correctly under rapid-send conditions

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-TECH-03 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in with a contact selected. |
| **Test Data** | 10 messages: `Message 1` through `Message 10` |

**Steps**
1. Using the browser Console, inject 10 messages in rapid succession (100ms apart).
2. After all messages are sent, hard refresh the page.
3. Reopen the conversation and observe the message order.

**Expected Result**
- All 10 messages appear in the correct numerical order (`Message 1` → `Message 10`).
- No message appears out of sequence or is missing.

---

**TC-TECH-04** · XSS payload in the chat message field is rendered as plain text

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-TECH-04 |
| **Priority** | Critical |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in with a contact selected. |
| **Test Data** | `<img src=x onerror=alert(1)>` |

**Steps**
1. Paste the XSS payload into the chat message input field.
2. Click **Send**.
3. Observe both the sender's and recipient's browser for any script execution.

**Expected Result**
- The payload is displayed as literal text in the chat bubble.
- No alert box or script is executed in either browser.

---

**TC-TECH-05** · XSS payload in the Signup name field is rendered as plain text

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-TECH-04 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | App is open on the Signup page. |
| **Test Data** | Full Name: `<script>alert('xss')</script>` |

**Steps**
1. Enter the XSS payload as the full name during registration.
2. Complete the signup with a valid email and password.
3. Navigate to the Profile page and any page that displays the user's name.

**Expected Result**
- The name is stored and displayed as a plain text string.
- No script is executed on any page that renders the name.

---

## Section 6 — API and Session Tests

---

**TC-API-10** · Unauthenticated request to protected API returns 401

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-MSG-03 |
| **Priority** | Critical |
| **Execution Status** | ✅ Pass |
| **Precondition** | Postman is open. No active session or auth token is set. |
| **Test Data** | N/A |

**Steps**
1. In Postman, send `GET /api/messages/users` with no `jwt` cookie or auth header.

**Expected Result**
- API returns **401 Unauthorized**.
- No user data is returned.

---

**TC-API-11** · Malformed login request returns 400

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-AUTH-08 |
| **Priority** | Medium |
| **Execution Status** | ✅ Pass |
| **Precondition** | Postman is open. |
| **Test Data** | Request body: `{ "email": 1212, "password": "test" }` |

**Steps**
1. In Postman, send `POST /api/auth/login` with the email field set to an integer instead of a string.

**Expected Result**
- API returns **400 Bad Request**.
- No account data is created or accessed.

---

**TC-API-12** · Message sender ID is always set from the authenticated session

| Field | Detail |
| :--- | :--- |
| **Scenario** | TS-MSG-04 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | Logged in as User A. User B exists. |
| **Test Data** | Request body includes User B's ID as `senderId`. |

**Steps**
1. In Postman, send `POST /api/messages/send/:id` with User B's ID passed in the body as the sender.
2. Check the saved message record in the database.

**Expected Result**
- API returns **201 Created**.
- The `senderId` in the saved message matches **User A's ID** — not the one provided in the body.
- This confirms the server ignores client-supplied identity and uses the authenticated session.

---

**TC-SESSION-10** · Logout in one tab invalidates the session in all other open tabs

| Field | Detail |
| :--- | :--- |
| **Scenario** | REQ-AUTH-03 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | The same user is logged in on two browser tabs (Tab A and Tab B). |
| **Test Data** | N/A |

**Steps**
1. In Tab A, click **Logout**.
2. Switch to Tab B and attempt any action (e.g., sending a message or navigating).

**Expected Result**
- Tab B detects the session is no longer valid.
- Tab B redirects to the Login page.

---

**TC-SESSION-11** · Session is restored after a full browser restart

| Field | Detail |
| :--- | :--- |
| **Scenario** | REQ-AUTH-04 |
| **Priority** | High |
| **Execution Status** | ✅ Pass |
| **Precondition** | User is logged in. The browser is fully closed (not just the tab). |
| **Test Data** | N/A |

**Steps**
1. Force-close the entire browser.
2. Reopen the browser and navigate to the app URL.

**Expected Result**
- The app calls `GET /api/auth/check` automatically.
- The `jwt` cookie persists and is recognized.
- User is taken directly to the Dashboard without needing to log in again.
