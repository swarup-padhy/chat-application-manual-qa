# API Inventory
**Project**: Chat Application (MERN + Socket.io)
**Version**: 1.0

> This document lists all backend API endpoints used during manual and Postman-based testing. Each endpoint is linked to the test cases that verify it. The base URL for all endpoints during local testing is `http://localhost:5001/api`.

---

## 1. Authentication Endpoints

These endpoints handle user registration, login, logout, profile updates, and session checks. They are the most security-critical part of the API.

| Endpoint | Method | Auth Required | Purpose | Expected Success Code | Tested By |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `/auth/signup` | POST | No | Creates a new user account with full name, email, and password. | 201 Created | TC-AUTH-01, TC-AUTH-02, TC-AUTH-03, TC-AUTH-04 |
| `/auth/login` | POST | No | Authenticates an existing user and sets the `jwt` cookie. | 200 OK | TC-AUTH-05, TC-AUTH-07 |
| `/auth/logout` | POST | No | Clears the `jwt` cookie and ends the session on the client side. | 200 OK | TC-AUTH-09 |
| `/auth/update-profile` | PUT | **Yes** | Uploads a new profile picture (base64) to Cloudinary and updates the user record. | 200 OK | TC-PRF-01, TC-PRF-02 |
| `/auth/check` | GET | **Yes** | Verifies whether the current `jwt` cookie belongs to an active, valid session. Called automatically on page load. | 200 OK | TC-AUTH-08, TC-SESSION-11 |

---

## 2. Messaging Endpoints

These endpoints handle fetching the user list, loading chat history, and sending messages. All three require a valid session.

| Endpoint | Method | Auth Required | Purpose | Expected Success Code | Tested By |
| :--- | :--- | :--- | :--- | :--- | :--- |
| `/messages/users` | GET | **Yes** | Returns a list of all registered users except the currently logged-in user. Populates the sidebar. | 200 OK | TC-MSG-01, TC-API-10 |
| `/messages/:id` | GET | **Yes** | Returns the full message history between the logged-in user and the user identified by `:id`. | 200 OK | TC-MSG-02 |
| `/messages/send/:id` | POST | **Yes** | Sends a text or image message to the user identified by `:id`. Saves to the database and triggers a Socket.io event to the recipient. | 201 Created | TC-MSG-03, TC-MSG-04, TC-MSG-05, TC-MSG-06, TC-API-12, TC-API-13 |

---

## 3. Negative and Security Test Cases per Endpoint

This table shows what the API should return when requests are intentionally invalid or unauthorized.

| Endpoint | Scenario | Expected Code | Tested By |
| :--- | :--- | :--- | :--- |
| `/auth/signup` | Password shorter than 6 characters | 400 Bad Request | TC-AUTH-02 |
| `/auth/signup` | Duplicate email (case-insensitive) | 400 Bad Request | TC-AUTH-04 |
| `/auth/login` | Wrong password | 400 Bad Request | TC-AUTH-05 |
| `/auth/login` | Email field is an integer (malformed) | 400 Bad Request | TC-API-11 |
| `/messages/users` | No `jwt` cookie present | 401 Unauthorized | TC-API-10 |
| `/messages/send/:id` | Image payload exceeds size limit | 413 Payload Too Large | TC-MSG-06 |
