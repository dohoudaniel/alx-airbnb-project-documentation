## 🎯 Objectives
Document the technical and functional requirements for key backend features of the Airbnb Clone project. Each feature spec includes API endpoints, payload schemas, validation rules, and performance criteria.

---

## 1. User Authentication

### 1.1 Overview
Allows users to register, log in, refresh tokens, and log out securely. Supports both “guest” and “host” roles.

### 1.2 API Endpoints

| Method | Endpoint               | Description                   |
| ------ | ---------------------- | ----------------------------- |
| POST   | `/api/v1/auth/register`| Register new user            |
| POST   | `/api/v1/auth/login`   | User login                   |
| POST   | `/api/v1/auth/refresh` | Refresh access token         |
| POST   | `/api/v1/auth/logout`  | Invalidate refresh token     |

### 1.3 Request / Response

#### POST `/api/v1/auth/register`
- **Input JSON**  
  ```json
  {
    "first_name": "string",
    "last_name":  "string",
    "email":      "string (email format)",
    "password":   "string (min 8 chars)",
    "role":       "guest|host"
  }
````

* **Success (201 Created)**

  ```json
  {
    "user_id":   "uuid",
    "first_name":"string",
    "last_name": "string",
    "email":     "string",
    "role":      "guest|host",
    "created_at":"timestamp"
  }
  ```
* **Errors (400 Bad Request)**

  * `email` already in use
  * Password too weak
  * Missing required field

#### POST `/api/v1/auth/login`

* **Input JSON**

  ```json
  {
    "email":    "string",
    "password": "string"
  }
  ```
* **Success (200 OK)**

  ```json
  {
    "access_token":  "jwt",
    "refresh_token": "jwt",
    "expires_in":    3600
  }
  ```
* **Errors (401 Unauthorized)**

  * Invalid credentials

#### POST `/api/v1/auth/refresh`

* **Input JSON**

  ```json
  { "refresh_token": "jwt" }
  ```
* **Success (200 OK)**

  ```json
  {
    "access_token": "jwt",
    "expires_in":   3600
  }
  ```
* **Errors (401 Unauthorized)**

  * Refresh token expired or revoked

#### POST `/api/v1/auth/logout`

* **Input JSON**

  ```json
  { "refresh_token": "jwt" }
  ```
* **Success (204 No Content)**
* **Errors (400 Bad Request)**

  * Token missing or invalid

### 1.4 Validation Rules

* `email`: valid email format, max length 150
* `password`: min 8 characters, at least one uppercase, one digit
* `role`: must be `guest` or `host`

### 1.5 Performance Criteria

* **Registration / Login**: ≤ 200 ms on average under 100 RPS
* **Token Refresh**: ≤ 100 ms
* **Throughput**: sustain 200 concurrent auth requests

---

## 2. Property Management

### 2.1 Overview

Hosts can create, update, delete, and retrieve property listings. Guests can browse and filter listings.

### 2.2 API Endpoints

| Method | Endpoint                  | Description                      |
| ------ | ------------------------- | -------------------------------- |
| POST   | `/api/v1/properties`      | Create new property (host only)  |
| GET    | `/api/v1/properties`      | List/search properties           |
| GET    | `/api/v1/properties/{id}` | Retrieve single property details |
| PUT    | `/api/v1/properties/{id}` | Update property (host only)      |
| DELETE | `/api/v1/properties/{id}` | Delete property (host only)      |

### 2.3 Request / Response

#### POST `/api/v1/properties`

* **Input JSON**

  ```json
  {
    "name":         "string",
    "description":  "string",
    "location":     "string",
    "price_per_night":  "decimal",
    "amenities":    ["string", ...],
    "availability": [
      {"start_date":"YYYY-MM-DD","end_date":"YYYY-MM-DD"}, ...
    ]
  }
  ```
* **Success (201 Created)**

  ```json
  {
    "property_id":"uuid",
    "host_id":    "uuid",
    "created_at": "timestamp"
  }
  ```

#### GET `/api/v1/properties`

* **Query Parameters**
  `?location=&min_price=&max_price=&guests=&amenities=wifi,pool&page=1&limit=20`
* **Success (200 OK)**

  ```json
  {
    "total":  123,
    "page":   1,
    "limit":  20,
    "data": [ { property_obj }, ... ]
  }
  ```

#### GET `/api/v1/properties/{id}`

* **Success (200 OK)**

  ```json
  { property_obj }
  ```
* **Errors (404 Not Found)**

  * Property does not exist

### 2.4 Validation Rules

* `name`: 5–100 chars
* `description`: max 2000 chars
* `location`: non-empty
* `price_per_night`: positive decimal ≥ 10.00
* `amenities`: subset of allowed list
* `availability` dates must not overlap

### 2.5 Performance Criteria

* **List/Search**: ≤ 300 ms for 20 results under 100 RPS
* **Create/Update/Delete**: ≤ 250 ms under 50 RPS
* **Database indexing** on `location`, `price_per_night`

---

## 3. Booking System

### 3.1 Overview

Allows guests to reserve properties, view, modify, and cancel bookings.

### 3.2 API Endpoints

| Method | Endpoint                | Description                       |
| ------ | ----------------------- | --------------------------------- |
| POST   | `/api/v1/bookings`      | Create a new booking (guest only) |
| GET    | `/api/v1/bookings`      | List user’s bookings              |
| GET    | `/api/v1/bookings/{id}` | Retrieve booking details          |
| PUT    | `/api/v1/bookings/{id}` | Modify booking (dates/status)     |
| DELETE | `/api/v1/bookings/{id}` | Cancel booking                    |

### 3.3 Request / Response

#### POST `/api/v1/bookings`

* **Input JSON**

  ```json
  {
    "property_id": "uuid",
    "start_date":  "YYYY-MM-DD",
    "end_date":    "YYYY-MM-DD"
  }
  ```
* **Success (201 Created)**

  ```json
  {
    "booking_id": "uuid",
    "status":     "pending",
    "total_price": "decimal",
    "created_at": "timestamp"
  }
  ```
* **Errors (400 / 409)**

  * Date range invalid or overlaps existing booking
  * Property not available

#### PUT `/api/v1/bookings/{id}`

* **Input JSON (partial update)**

  ```json
  {
    "start_date":"YYYY-MM-DD",
    "end_date":  "YYYY-MM-DD"
  }
  ```
* **Success (200 OK)**

  ```json
  {
    "booking_id":"uuid",
    "status":    "confirmed",
    "updated_at":"timestamp"
  }
  ```

#### DELETE `/api/v1/bookings/{id}`

* **Success (204 No Content)**

### 3.4 Validation Rules

* `start_date` < `end_date`
* Booking window ≤ 30 days
* No overlapping bookings for same property
* Cancellation allowed only if `start_date` > now + 24h

### 3.5 Performance Criteria

* **Create Booking**: ≤ 200 ms under 50 RPS
* **List Bookings**: ≤ 150 ms for 20 records
* Use **row-level locks** or **optimistic concurrency** to prevent double-booking

---

## 📂 File Location

Save this file as `requirements.md` at the root of:

```
alx-airbnb-project-documentation/
└── requirements.md

