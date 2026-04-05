# Requirements Specification
**Project**: Chat Application (MERN + Socket.io)
**Version**: 1.0
**Status**: Baselined

> This document defines what the system must do. Every test scenario and test case in this project is linked back to a requirement listed here. This ensures nothing is tested without a reason, and nothing required is left untested.

---

## 1. User Authentication

These requirements cover how users sign up, log in, stay logged in, and log out securely.

| ID | Requirement Name | Description |
| :--- | :--- | :--- |
| **REQ-AUTH-01** | User Registration | A new user must be able to create an account using a unique email address, a full name, and a password that is at least 6 characters long. |
| **REQ-AUTH-02** | User Login | A registered user must be able to log in using their correct email and password. |
| **REQ-AUTH-03** | Session Persistence | After a successful login or signup, the system must keep the user logged in using a secure `jwt` cookie. |
| **REQ-AUTH-04** | Session Restoration | When a user refreshes the page or reopens the browser, the system must automatically check the cookie and restore the session without asking for credentials again. |
| **REQ-AUTH-05** | Secure Logout | When a user logs out, the system must clear the `jwt` cookie and end the session completely. |
| **REQ-AUTH-06** | Route Protection | Pages that require login (such as Dashboard and Profile) must redirect unauthenticated users to the Login page. |
| **REQ-AUTH-07** | Duplicate Email Prevention | The system must reject any registration attempt using an email address that is already registered, regardless of letter casing (e.g., `user@test.com` and `USER@test.com` must be treated as the same email). |
| **REQ-AUTH-08** | Error Feedback | When a login or signup request fails (wrong password, missing fields, etc.), the system must return a clear error message and an appropriate HTTP status code. |

---

## 2. Messaging and Communication

These requirements define how users find each other and exchange messages.

| ID | Requirement Name | Description |
| :--- | :--- | :--- |
| **REQ-MSG-01** | Contact List | After login, users must see a sidebar listing all other registered users in real time. |
| **REQ-MSG-02** | Chat History | When a user selects a contact, the previous messages from that private conversation must load in the correct order. |
| **REQ-MSG-03** | Send Text and Images | Users must be able to send both plain text messages and image attachments (encoded as base64). |
| **REQ-MSG-04** | Real-Time Delivery | Messages sent by one user must appear in the recipient's chat window immediately, without the recipient needing to refresh. There must be no duplicate messages. |
| **REQ-MSG-05** | Online/Offline Status | The system must show every user whether their contacts are currently online or offline, and this status must update in real time. |
| **REQ-MSG-06** | File Size Limit | If a message payload (such as a base64-encoded image) is too large, the system must reject it and return a `413 Payload Too Large` error. |
| **REQ-MSG-07** | Message Consistency | After a page refresh or switching browser tabs, all previously sent messages must still be visible and correctly ordered. |

---

## 3. Profile Management

These requirements cover how users view and update their personal information.

| ID | Requirement Name | Description |
| :--- | :--- | :--- |
| **REQ-PRF-01** | Profile Picture Upload | Users must be able to upload a new profile picture. The image must be stored securely on Cloudinary. |
| **REQ-PRF-02** | View Profile Details | Users must be able to view their account information: full name, email address, and account creation date. |
| **REQ-PRF-03** | Upload Failure Handling | If a profile picture upload fails (for example, due to a Cloudinary timeout), the system must display an appropriate error message and preserve the existing photo. |

---

## 4. Technical and Security Requirements

These requirements define the behind-the-scenes rules the system must follow to remain secure and reliable.

| ID | Requirement Name | Description |
| :--- | :--- | :--- |
| **REQ-TECH-01** | Password Hashing | User passwords must never be stored as plain text. They must be hashed and salted using Bcrypt before being saved to the database. |
| **REQ-TECH-02** | Socket Connection Management | Each logged-in user must have exactly one active WebSocket (Socket.io) connection. When the user logs out, that connection must be closed immediately. |
| **REQ-TECH-03** | Message Ordering | The system must store and display messages in the exact order they were sent, using `createdAt` timestamps to prevent ordering errors during rapid messaging. |
