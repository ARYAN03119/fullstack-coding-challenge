# Store Rating Platform

A full-stack web application built for the **FullStack Intern Coding Challenge**. Users can browse stores and submit 1-5 star ratings; store owners can see how their store is rated; administrators manage the whole platform from a dashboard.

## Tech Stack
- **Backend:** Express.js, PostgreSQL, JWT auth, bcrypt password hashing
- **Frontend:** React 19 (Vite), React Router
- **Database:** PostgreSQL

## Project Structure
```
store-rating-app/
├── backend/      # Express API, PostgreSQL schema, JWT auth
└── frontend/     # React (Vite) single-page app
```

Each folder has its own `README.md` with detailed setup steps. The short version:

## Quick Start

**1. Backend**
```bash
cd backend
npm install
cp .env.example .env       # then fill in your PostgreSQL credentials
# create an empty database matching DB_NAME in .env, then:
npm run seed                # creates tables + a default admin account
npm run dev                  # starts API on http://localhost:5000
```

**2. Frontend** (in a second terminal)
```bash
cd frontend
npm install
cp .env.example .env        # default already points to localhost:5000
npm run dev                   # starts app on http://localhost:5173
```

**3. Log in**
Use the default admin credentials printed by `npm run seed` (also visible in `backend/.env.example` as `DEFAULT_ADMIN_EMAIL` / `DEFAULT_ADMIN_PASSWORD`) to log in as System Administrator. From there you can create Normal Users and Store Owners.

## User Roles & Functionality

**System Administrator**
- Dashboard: total users, total stores, total ratings submitted
- Add new users (any role) and new stores
- View/filter/sort all users and stores by Name, Email, Address, Role
- View full details of any user (Store Owners additionally show their store's rating)

**Normal User**
- Sign up via a public registration page
- Browse all stores, search by Name and Address
- Submit a 1-5 star rating per store, and modify it later
- Update their own password

**Store Owner**
- Dashboard showing everyone who rated their store, plus the average rating
- Update their own password

## Design Decisions Worth Knowing
- **One `users` table with a `role` column**, not three separate tables — matches the spec's "single login system" requirement directly, and role-specific behavior is enforced in middleware (`authorize('ADMIN')`, etc.) rather than at the schema level.
- **Ratings use `INSERT ... ON CONFLICT (user_id, store_id) DO UPDATE`** — so "submit" and "modify" a rating are literally the same database operation, which mirrors how the spec describes them as one continuous action rather than two separate features.
- **All sortable/filterable list endpoints whitelist their sort columns server-side** to avoid building dynamic SQL from unsanitized query parameters.
- **Client-side validation mirrors backend validation rules exactly** (name length, password complexity) so users get instant feedback, but the backend independently re-validates everything since client checks can be bypassed.

## Form Validation Rules (enforced both client- and server-side)
- Name: 20-60 characters
- Address: up to 400 characters
- Password: 8-16 characters, at least one uppercase letter and one special character
- Email: standard email format
