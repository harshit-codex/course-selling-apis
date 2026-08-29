# Course Selling APIs

A RESTful backend API for a course selling platform built with Node.js, Express, and MongoDB. Supports separate flows for users (browse & purchase courses) and admins (create & manage courses), with cookie-based JWT authentication and Zod input validation.

## Tech Stack

- **Runtime:** Node.js (ESM)
- **Framework:** Express.js
- **Database:** MongoDB via Mongoose
- **Auth:** JWT stored in HTTP-only cookies
- **Validation:** Zod
- **Password hashing:** bcrypt

## Project Structure

```
├── index.js                  # Entry point
├── config/
│   ├── database.js           # MongoDB connection
│   └── constant.js           # Shared constants
├── routes/
│   ├── user.routes.js        # /api/v1/user
│   ├── admin.routes.js       # /api/v1/admin
│   └── courses.routes.js     # /api/v1/course
├── controllers/
│   ├── user.controllers.js
│   ├── admin.controllers.js
│   └── course.controllers.js
├── middlewares/
│   ├── user.middlewares.js   # JWT auth for users
│   └── admin.middlewares.js  # JWT auth for admins
├── models/
│   ├── user.model.js
│   ├── admin.model.js
│   ├── course.model.js
│   └── purchase.model.js
├── schemas/
│   ├── user.schemas.js       # Zod schemas for user input
│   ├── admin.schemas.js
│   └── course.schemas.js
└── .env.sample
```

## Getting Started

### Prerequisites

- Node.js 18+
- MongoDB instance (local or Atlas)

### Installation

```bash
git clone <repo-url>
cd course-selling-apis
npm install
```

### Environment Variables

Copy `.env.sample` and fill in the values:

```bash
cp .env.sample .env
```

| Variable             | Description                         |
|----------------------|-------------------------------------|
| `PORT`               | Server port (default: 5000)         |
| `MONGODB_URI`        | MongoDB connection string           |
| `JWT_USER_PASSWORD`  | Secret key for user JWT tokens      |
| `JWT_ADMIN_PASSWORD` | Secret key for admin JWT tokens     |

### Running the Server

```bash
# Production
npm start

# Development (with nodemon)
npm run dev
```

## API Reference

Base URL: `http://localhost:<PORT>/api/v1`

### User Routes `/api/v1/user`

| Method | Endpoint     | Auth | Body                                       | Description                  |
|--------|--------------|------|--------------------------------------------|------------------------------|
| POST   | `/signup`    | No   | `{ email, password, firstName, lastName }` | Register a new user          |
| POST   | `/signin`    | No   | `{ email, password }`                      | Sign in and set auth cookie  |
| GET    | `/purchases` | Yes  | —                                          | Get user's purchased courses |
| GET    | `/signout`   | Yes  | —                                          | Clear auth cookie            |

### Admin Routes `/api/v1/admin`

| Method | Endpoint       | Auth | Body                                               | Description                |
|--------|----------------|------|----------------------------------------------------|----------------------------|
| POST   | `/signup`      | No   | `{ email, password, firstName, lastName }`         | Register a new admin       |
| POST   | `/signin`      | No   | `{ email, password }`                              | Sign in and set auth cookie|
| GET    | `/signout`     | Yes  | —                                                  | Clear auth cookie          |
| POST   | `/course`      | Yes  | `{ title, description, imageUrl, price }`          | Create a new course        |
| PUT    | `/course`      | Yes  | `{ courseId, title, description, imageUrl, price }`| Update an existing course  |
| GET    | `/course/bulk` | Yes  | —                                                  | List all admin's courses   |

### Course Routes `/api/v1/course`

| Method | Endpoint    | Auth | Body           | Description                   |
|--------|-------------|------|----------------|-------------------------------|
| POST   | `/purchase` | Yes  | `{ courseId }` | Purchase a course (user auth) |
| GET    | `/preview`  | No   | —              | Browse all available courses  |

## Data Models

**User / Admin** — `email` (unique), `password` (hashed), `firstName`, `lastName`

**Course** — `title`, `description`, `price`, `imageUrl`, `creatorId` (ref: admin)

**Purchase** — links a `userId` to a `courseId`

## Authentication

Auth is handled via HTTP-only cookies. On successful signin, a signed JWT is set as a cookie. Protected routes validate this cookie via the respective middleware before granting access. Users and admins have separate JWT secrets.
