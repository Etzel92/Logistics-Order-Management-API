# Logistics Order Management API

A modular NestJS REST API for managing logistics orders, trucks, locations, and user-scoped workflows.

This project provides JWT-based authentication, order lifecycle management, user-owned resources, paginated order queries, status transition rules, Google Places integration for location resolution, and interactive API documentation with Swagger.

> **Current scope**: authentication, users, trucks, locations, orders, status transitions, paginated queries, optional relation expansion, order statistics, protected routes, and local environment-based configuration.

---

## Contents

- [Key Features](#key-features)
- [Architecture and Stack](#architecture-and-stack)
- [Project Structure](#project-structure)
- [Core Modules](#core-modules)
- [Business Rules](#business-rules)
- [Requirements](#requirements)
- [Environment Variables](#environment-variables)
- [Quick Start](#quick-start)
- [Running the API](#running-the-api)
- [API Documentation](#api-documentation)
- [Useful Endpoints](#useful-endpoints)
- [Testing and Tooling](#testing-and-tooling)
- [Notes](#notes)

---

## Key Features

- JWT-based authentication with signup and login flows
- Global route protection with public auth endpoints
- Modular domain structure for users, trucks, locations, and orders
- Order creation linked to truck, pickup location, and dropoff location
- User-scoped access control for order and location resources
- Dedicated order status transition endpoint
- Paginated order listing with filtering options
- Optional relation expansion for detailed order responses
- Aggregation endpoint for order count by status
- Google Places API integration for resolving locations from `placeId`
- Interactive Swagger documentation at `/docs`
- Configuration loading and environment validation with NestJS Config and Joi

---

## Architecture and Stack

- **Framework**: NestJS
- **Language**: TypeScript
- **Database**: MongoDB
- **ODM**: Mongoose
- **Authentication**: JWT + Passport
- **Validation**: class-validator, class-transformer, global `ValidationPipe`
- **Documentation**: Swagger
- **External integration**: Google Places API
- **Configuration**: `@nestjs/config` with Joi-based validation
- **Testing**: Jest and Supertest
- **Tooling**: ESLint and Prettier

---

## Project Structure

```text
/src
  /auth         Authentication, login/signup, JWT strategy and guard
  /common       Shared decorators and pipes
  /config       Environment loading and validation schema
  /database     MongoDB connection setup
  /health       Health check endpoint
  /locations    User-owned locations resolved from Google Places
  /orders       Order workflows, filters, stats, and status transitions
  /trucks       Truck resource management
  /users        User resource management and persistence
/test           End-to-end test setup
```

---

## Core Modules

### Auth
Handles signup, login, JWT generation, and route protection.

### Users
Provides user creation and management, unique email handling, and password hashing.

### Trucks
Provides CRUD operations for trucks, including validation and unique plate handling.

### Locations
Stores user-owned locations and resolves address and coordinates from Google Places using `placeId`.

### Orders
Handles order creation, listing, detail retrieval, updates, deletion, status changes, and status-based aggregation.

### Health
Exposes an authenticated health endpoint to verify API and database connection state.

---

## Business Rules

- Orders are created by authenticated users.
- Each order references:
  - one `truck`
  - one `pickup` location
  - one `dropoff` location
- The order owner is taken from the JWT token.
- Order status changes are handled only through a dedicated endpoint.
- Allowed status flow is:

```text
created -> in_transit -> completed
```

- Invalid status transitions are rejected.
- Non-admin users can only access and modify their own orders.
- Admin users can query orders across users and filter by `user`.
- Locations are user-scoped.
- Location creation resolves address and coordinates from Google Places using `placeId`.
- Order statistics return counts grouped by status.

---

## Requirements

- Node.js 18+
- npm
- MongoDB
- Google Places API key

---

## Environment Variables

Create a `.env` file based on `.env.example`.

```env
PORT=3000
NODE_ENV=development
MONGODB_URI=mongodb://localhost:27017/logistics_order_management
JWT_SECRET=__set_in_local_env__
JWT_EXPIRES=1d
GOOGLE_PLACES_API_KEY=YOUR_API_KEY
```

### Variable reference

- `PORT`: API port
- `NODE_ENV`: runtime environment
- `MONGODB_URI`: MongoDB connection string
- `JWT_SECRET`: secret used to sign access tokens
- `JWT_EXPIRES`: token expiration time
- `GOOGLE_PLACES_API_KEY`: key used to resolve locations from `placeId`

---

## Quick Start

```bash
git clone <your-repository-url>
cd Logistics-Order-Management-API
npm install
cp .env.example .env
```

Update `.env` with your local values, then start MongoDB and run the API.

---

## Running the API

### Development

```bash
npm run start:dev
```

### Production build

```bash
npm run build
npm run start:prod
```

By default, the API runs at:

```text
http://localhost:3000
```

---

## API Documentation

Swagger UI is available at:

```text
http://localhost:3000/docs
```

The API is configured with Bearer authentication support, so JWT tokens can be tested directly from Swagger.

---

## Useful Endpoints

### Auth
- `POST /auth/signup` — register a new user and return an access token
- `POST /auth/login` — authenticate a user and return an access token

### Users
- `POST /users` — create a user
- `GET /users` — list users with pagination and optional search
- `GET /users/:id` — get a user by id
- `PATCH /users/:id` — update a user
- `DELETE /users/:id` — delete a user

### Trucks
- `POST /trucks` — create a truck
- `GET /trucks` — list trucks
- `GET /trucks/:id` — get a truck by id
- `PATCH /trucks/:id` — update a truck
- `DELETE /trucks/:id` — delete a truck

### Locations
- `POST /locations` — create a location from `placeId`
- `GET /locations?page=1&limit=10` — list user locations
- `PATCH /locations/:id` — update a location
- `DELETE /locations/:id` — delete a location

### Orders
- `POST /orders` — create a new order
- `GET /orders` — list orders with pagination and filters
- `GET /orders/:id` — get order detail
- `PATCH /orders/:id` — update truck, pickup, or dropoff references
- `PATCH /orders/:id/status` — change order status
- `DELETE /orders/:id` — delete an order
- `GET /orders/stats/status` — get aggregated order counts by status

### Health
- `GET /health` — return API and database connection state

---

## Testing and Tooling

### Development scripts

```bash
npm run start
npm run start:dev
npm run start:debug
npm run start:prod
```

### Lint and formatting

```bash
npm run lint
npm run format
```

### Tests

```bash
npm run test
npm run test:watch
npm run test:cov
npm run test:e2e
```

---

## Notes

- The application uses a global JWT guard, so routes are protected by default.
- Public routes are the authentication endpoints used for signup and login.
- `GET /health` exists for runtime checks and reports MongoDB connection state.
- Google Places integration is required for location creation and place-based updates.
- Order listing supports pagination and filtering by `status`, `truck`, and `user` where applicable.
- Order detail and listing can expand related references when requested.
- MongoDB is required for local development and testing.
- The default package name can be renamed in `package.json` if you want the repository to use a cleaner public identifier.
