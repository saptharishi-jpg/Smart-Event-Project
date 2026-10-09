# SmartEvent — Event Discovery, Ticket Booking & Management Platform

A full-stack event platform built with **FastAPI, SQLAlchemy, SQLite, JWT authentication, React, and Vite**. The project is developed in two phases: Phase 1 delivers the customer event-booking journey, and Phase 2 adds role-based access, organizer tools, event lifecycle updates, and admin analytics.

## Contents

1. [Project Overview](#1-project-overview)
2. [Technology Stack](#2-technology-stack)
3. [Phase 1 — Event Discovery & Ticket Booking](#3-phase-1--event-discovery--ticket-booking)
4. [Phase 2 — RBAC, Event Management & Admin Analytics](#4-phase-2--rbac-event-management--admin-analytics)
5. [Roles and Permissions](#5-roles-and-permissions)
6. [Project Structure](#6-project-structure)
7. [Prerequisites](#7-prerequisites)
8. [Environment Configuration](#8-environment-configuration)
9. [Database](#9-database)
10. [Installation and Startup](#10-installation-and-startup)
11. [API Documentation and Main Endpoints](#11-api-documentation-and-main-endpoints)
12. [How to Test the Main Workflows](#12-how-to-test-the-main-workflows)
13. [Security Notes](#13-security-notes)
14. [Troubleshooting](#14-troubleshooting)
15. [Project Deliverables Checklist](#15-project-deliverables-checklist)

---

## 1. Project Overview

**SmartEvent** allows users to discover events, book tickets, view their bookings and tickets, and receive notifications. Phase 2 extends the same project with three roles—`USER`, `ORGANIZER`, and `ADMIN`—and role-specific APIs and dashboard functionality.

### Objectives

- Build a REST API using FastAPI.
- Build the user interface using React and Vite.
- Store users, events, bookings, tickets, and notifications in SQLite through SQLAlchemy.
- Authenticate users with JWT access tokens.
- Restrict protected actions by role and organizer ownership.
- Provide organizer booking insights and platform-level admin analytics.

## 2. Technology Stack

| Technology | Purpose |
|---|---|
| Python | Backend programming |
| FastAPI | REST API and API documentation |
| Uvicorn | Runs the backend server |
| SQLAlchemy | Database models and queries |
| SQLite | Local development database |
| Pydantic | Request and input validation |
| JWT (`python-jose`) | Token-based authentication |
| `python-dotenv` | Reads environment variables |
| React | Frontend user interface |
| Vite | Frontend development server and build |
| Axios | HTTP requests from React |
| `qrcode` and Pillow | Ticket QR-code support |

## 3. Phase 1 — Event Discovery & Ticket Booking

Phase 1 establishes the customer-facing booking flow.

### User functionality

- Register a user account.
- Log in and receive a JWT access token.
- View and browse available events.
- View event details and categories.
- Book tickets for an event.
- View booking history.
- View personal tickets and ticket QR codes.
- Verify a ticket using its ticket code.
- View notifications and mark notifications as read.

### Phase 1 APIs

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/auth/register` | Register an account |
| `POST` | `/auth/login` | Log in and obtain a token |
| `GET` | `/auth/profile` | Get the logged-in user's profile |
| `GET` | `/events` | Browse events |
| `GET` | `/events/categories/list` | List event categories |
| `GET` | `/events/{event_id}` | View event details |
| `POST` | `/bookings` | Book tickets |
| `GET` | `/bookings` | View the current user's bookings |
| `GET` | `/tickets` | View personal tickets |
| `GET` | `/tickets/{ticket_code}/qr` | Get a ticket QR code |
| `GET` | `/tickets/verify/{ticket_code}` | Verify a ticket code |
| `GET` | `/notifications` | View notifications |
| `PATCH` | `/notifications/{notification_id}/read` | Mark a notification as read |

## 4. Phase 2 — RBAC, Event Management & Admin Analytics

Phase 2 extends the Phase 1 platform. The Phase 2 code includes the following functionality.

### Module 7 — Role-Based Access Control (RBAC)

- Supports `USER`, `ORGANIZER`, and `ADMIN` roles.
- Includes role information in the JWT access token.
- Uses protected backend dependencies to validate authenticated users and roles.
- Restricts organizer and admin APIs.
- Checks event ownership before an organizer edits or cancels an event.
- Provides an admin API for listing users and updating a user's role.

### Module 8 — Organizer Event Management

- Create events.
- View events owned by the logged-in organizer.
- Update an organizer's own events.
- Cancel an organizer's own events.
- View bookings for the organizer's events.
- View bookings for a specific organizer event.

Relevant APIs:

| Method | Endpoint | Purpose |
|---|---|---|
| `POST` | `/events` | Create an event (organizer role) |
| `GET` | `/organizer/events` | List the organizer's events |
| `PUT` | `/events/{event_id}` | Update an owned event |
| `POST` | `/events/{event_id}/cancel` | Cancel an owned event |
| `GET` | `/organizer/bookings` | View bookings for the organizer's events |
| `GET` | `/organizer/events/{event_id}/bookings` | View bookings for one owned event |

### Module 9 — Organizer Booking Insights

Organizer analytics include metrics for ticket sales, remaining tickets, bookings, and revenue for the organizer's events.

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/organizer/analytics` | Organizer event and booking metrics |

The React Phase 2 interface includes a Phase 2 dashboard area for organizer and admin functions. Available metrics depend on the booking and event data in the local database.

### Module 10 — Event Status & Updates

- Tracks event lifecycle states, including `UPCOMING`, `ONGOING`, `COMPLETED`, and `CANCELLED`.
- Supports event cancellation and update notifications.
- Displays event status and cancellation information in the interface where supplied by the API.
- Updates date-related status when event data is processed by the backend; this is not a separately scheduled background service.

**Status terminology:** the Phase 2 requirements also mention `ACTIVE` for Module 8. The full lifecycle set in Module 10 is used for the overall project status vocabulary.

### Module 11 — Admin Dashboard & Platform Analytics

Admin-only APIs provide platform-wide visibility.

| Method | Endpoint | Purpose |
|---|---|---|
| `GET` | `/admin/users` | View users |
| `PATCH` | `/admin/users/{user_id}/role` | Update a user's role |
| `GET` | `/admin/events` | View all events |
| `GET` | `/admin/bookings` | View all bookings |
| `GET` | `/admin/analytics` | View platform analytics |

Analytics cover registered users, events, bookings, ticket sales, revenue, daily ticket sales, monthly booking trends, popular events, and top revenue events, subject to the available data and implementation.

### Ticket cancellation

- `POST /bookings/{booking_id}/cancel` cancels the logged-in user's booking when permitted by the backend.
- Cancelled tickets should no longer be treated as active tickets.
- The project does not integrate a real payment processor or automatically issue a real-world refund.

## 5. Roles and Permissions

| Role | Permissions |
|---|---|
| `USER` | Browse events, book tickets, view own booking history, view own tickets, and view personal notifications |
| `ORGANIZER` | User capabilities as allowed by the backend, plus create and manage owned events, view event bookings, and view organizer analytics |
| `ADMIN` | Access admin user, event, booking, role-management, and analytics APIs |

Protected endpoints require a valid JWT. Organizer actions must pass ownership checks, and admin APIs must be accessed by an administrator.

## 6. Project Structure

```text
SmartEvent/
/ README.md
/.gitignore
/ backend/
   ── main.py
   ── requirements.txt
   ── .env.example
   ── smartevent.db       
/ frontend/
     ──.env
     ── package.json
     ── package-lock.json
     ── index.html
     ── vite.config.mjs
    / ── src/
        ── api.js
        ── App.jsx
        ── index.css
        ── main.jsx
        ── components/
           ── EventCard.jsx
           ── Navbar.jsx
           ── NotificationDropdown.jsx
           ── ProtectedRoute.jsx
           ── TicketCard.jsx
        / ── pages/
            ── AuthPages.jsx
            ── BookingConfirmationPage.jsx
            ── BookingHistoryPage.jsx
            ── EventDetailsPage.jsx
            ── HomePage.jsx
            ── NotificationsPage.jsx
            ── Phase2Page.jsx
            ── TicketsPage.jsx
```

## 7. Prerequisites

Install the following before running the project:

- Python 3.10 or newer
- Node.js and npm
- Visual Studio Code (recommended)

No Python virtual environment is required for the simple local setup below, although using one is recommended for isolated development.

## 8. Environment Configuration

Use one local environment file for this project: `backend/.env`. The frontend API client defaults to `http://localhost:8000`, so a separate frontend `.env` file is not required for the default local setup.

### Backend `.env`

Copy `backend/.env.example` to `backend/.env`, then set a strong secret key:

```env
SECRET_KEY=replace-with-a-long-random-secret
DATABASE_URL=sqlite:///./smartevent.db
CORS_ORIGINS=http://localhost:5173
ADMIN_EMAILS=saptha@gmail.com
```

- `SECRET_KEY`: signs JWT tokens. Replace the example with a long, random secret.
- `DATABASE_URL`: database connection string.
- `CORS_ORIGINS`: frontend origin allowed to call the API.
- `ADMIN_EMAILS`: comma-separated email addresses recognized as administrators by the backend configuration.

The email in `ADMIN_EMAILS` must match the account email you intend to use as an admin. Confirm that the backend's administrator assignment logic is enabled for your setup.

The frontend uses the default API URL `http://localhost:8000` from its API client. You do not need a second `.env` file for the standard local setup.

 Keep `backend/.env` private. The ZIP contains `backend/.env.example` only; create `backend/.env` locally.

## 9. Database

The project uses SQLite by default and SQLAlchemy models. The database path is controlled by `DATABASE_URL`.

The main database entities are:

- **Users** — account details and role (`USER`, `ORGANIZER`, or `ADMIN`).
- **Events** — event information, organizer ownership, and event status.
- **Bookings** — a user's booking and quantity/status information.
- **Tickets** — ticket codes and ticket records linked to bookings.
- **Notifications** — messages associated with users, including event updates/cancellations.

The application initializes database tables when the backend starts. The database file is local development data and is not included in the merged ZIP; it is created when you run the backend. Back up any existing database before replacing project files.

## 10. Installation and Startup

Open the extracted `SmartEvent` folder in VS Code. Use two terminals: one for the backend and one for the frontend.

### A. Backend setup

In Terminal 1:

```powershell
cd backend
py -m pip install -r requirements.txt
Copy-Item .env.example .env
```

If `backend/.env` already exists, do not overwrite it; edit it directly instead. Update `SECRET_KEY` to a strong random value.

Start FastAPI:

```powershell
py -m uvicorn main:app --reload --env-file .env
```

Backend URL: `http://127.0.0.1:8000`

### B. Frontend setup

Open Terminal 2 in VS Code:

```powershell
cd frontend
npm.cmd install
npm.cmd run dev
```

If `frontend/.env` already exists, do not overwrite it; edit it directly instead.

Frontend URL: `http://localhost:5173`

### C. Useful URLs

| Service | URL |
|---|---|
| React frontend | `http://localhost:5173` |
| FastAPI backend | `http://127.0.0.1:8000` |
| Swagger UI | `http://127.0.0.1:8000/docs` |
| OpenAPI schema | `http://127.0.0.1:8000/openapi.json` |

Keep both development servers running while using the application.

## 11. API Documentation and Main Endpoints

Open Swagger UI at `http://127.0.0.1:8000/docs` to inspect request bodies, run API calls, and review responses.

### Authentication

- `POST /auth/register`
- `POST /auth/login`
- `GET /auth/profile`

Log in through `/auth/login`. Copy the `access_token` from the successful response and use the Swagger **Authorize** button for protected endpoints. Do not use the `.env` `SECRET_KEY` as the access token.

### Events and bookings

- `GET /events`
- `GET /events/categories/list`
- `GET /events/{event_id}`
- `POST /bookings`
- `GET /bookings`
- `POST /bookings/{booking_id}/cancel`
- `GET /tickets`
- `GET /tickets/{ticket_code}/qr`
- `GET /tickets/verify/{ticket_code}`

### Organizer and admin

See the Phase 2 endpoint tables above. Role-protected APIs return an authorization error when called by an account without the required role.

## 12. How to Test the Main Workflows

1. Start the backend and frontend.
2. Register a regular user and log in.
3. Browse events, book tickets, and check Booking History and My Tickets.
4. Use `/auth/login` in Swagger to obtain a token; authorize Swagger for protected endpoints.
5. Test user booking cancellation with `POST /bookings/{booking_id}/cancel`.
6. Sign in with an organizer account and test event creation, editing, cancellation, event bookings, and organizer analytics.
7. Sign in with an administrator account and test the admin users, events, bookings, and analytics endpoints.
8. Check notifications after an event update or cancellation.

The project does not perform real payment processing. Revenue shown by analytics is based on application booking data, not confirmed payment-provider transactions.

## 13. Security Notes

- Replace the example `SECRET_KEY` before using the application.
- Never commit or share `backend/.env`.
- Do not put secrets in `frontend/.env`.
- Do not publish JWT access tokens or passwords.
- JWT access tokens should be treated as credentials; log in again if a token is exposed.
- Keep organizer ownership checks and admin-only restrictions enabled.
- This project is for learning and local development; review and harden it before production deployment.

## 14. Troubleshooting

### `cd backend` says the folder does not exist

Open the extracted `SmartEvent` root folder in VS Code, then open a new terminal. Run `dir` and check that `backend` and `frontend` are listed before running `cd backend`.

### Backend cannot load `.env`

Make sure the terminal is inside `backend`, the file is named `.env` (not `.env.txt`), and start the server with:

```powershell
py -m uvicorn main:app --reload --env-file .env
```

### `401 Unauthorized`

Log in again and use the new `access_token`. Paste the token into Swagger's **Authorize** dialog; do not paste `SECRET_KEY`.

### `403 Forbidden`

The current account may not have the required role. Check the logged-in account's role and use an authorized organizer or admin account for the relevant endpoint.

### Frontend cannot connect to the backend

Check that FastAPI is running on port `8000` and that the API client is configured for `http://localhost:8000`.

### npm command is blocked in PowerShell

Try `npm.cmd install` and `npm.cmd run dev`.

## 15. Project Deliverables Checklist

### Phase 1

- [x] User registration and login
- [x] JWT authentication
- [x] Event discovery and event details
- [x] Ticket booking
- [x] Booking history
- [x] Personal tickets and QR-code support
- [x] Notifications

### Phase 2

- [x] USER / ORGANIZER / ADMIN roles
- [x] Role information in JWT and protected APIs
- [x] Organizer event creation, editing, and cancellation
- [x] Organizer booking lists and analytics
- [x] Event lifecycle statuses and event notifications
- [x] Admin users, events, bookings, and analytics endpoints
- [x] Ticket cancellation endpoint
- [x] Pydantic request validation and environment-based configuration

---

**Project:** SmartEvent — Event Discovery & Ticket Booking System  
**Backend:** FastAPI  
**Frontend:** React + Vite  
**Database:** SQLite  
**Phases:** Phase 1 — Event Discovery & Ticket Booking; Phase 2 — RBAC, Organizer Event Management & Admin Analytics

## Author

Saptharishi reddy D
