
# Backend Feature Requirement Specifications - Airbnb Clone

---

## 1. 🧑‍💻 User Authentication

### Functional Requirements
- Users should be able to register (as guest or host)
- Login using email/password
- Authentication via JWT
- OAuth (Google/Facebook) optional

### API Endpoints
#### `POST /api/v1/register`
- **Input:**  
  ```json
  {
    "first_name": "John",
    "last_name": "Doe",
    "email": "john@example.com",
    "password": "SecurePass123",
    "role": "guest"
  }
  ```
- **Validations:**
  - Email must be unique and valid format
  - Password must be at least 8 characters
  - Role must be one of: `guest`, `host`

- **Output:**  
  ```json
  {
    "message": "User registered successfully",
    "token": "JWT-TOKEN"
  }
  ```

#### `POST /api/v1/login`
- **Input:** `{ "email": "john@example.com", "password": "SecurePass123" }`
- **Output:** `{ "token": "JWT-TOKEN", "user": { "role": "guest", ... } }`

### Performance Criteria
- Response time: < 500ms
- Token expiration: 24h

---

## 2. 🏠 Property Management

### Functional Requirements
- Hosts can create, update, and delete property listings
- Guests can view and search properties

### API Endpoints
#### `POST /api/v1/properties`
- **Input:**
  ```json
  {
    "name": "Cozy Apartment",
    "description": "Close to downtown",
    "location": "Nairobi",
    "price_per_night": 45.00,
    "amenities": ["WiFi", "Parking"]
  }
  ```
- **Validations:**
  - `price_per_night` must be > 0
  - All required fields must be filled

- **Output:**
  ```json
  {
    "message": "Property created successfully",
    "property_id": "uuid"
  }
  ```

#### `GET /api/v1/properties`
- **Query Parameters:**
  - `location`
  - `min_price`, `max_price`
  - `guests`
- **Output:** Paginated list of properties

### Performance Criteria
- Support at least 100 concurrent searches
- Listings should load in < 1s with caching (e.g., Redis)

---

## 3. 📅 Booking System

### Functional Requirements
- Guests can book available properties
- Double-booking prevention
- Booking status management (pending, confirmed, canceled)

### API Endpoints
#### `POST /api/v1/bookings`
- **Input:**
  ```json
  {
    "property_id": "uuid",
    "start_date": "2025-07-01",
    "end_date": "2025-07-05"
  }
  ```
- **Validation:**
  - Dates must not overlap existing bookings
  - `start_date < end_date`

- **Output:**
  ```json
  {
    "booking_id": "uuid",
    "status": "pending",
    "total_price": 180.00
  }
  ```

#### `PATCH /api/v1/bookings/:booking_id/cancel`
- **Output:** `{ "message": "Booking canceled", "status": "canceled" }`

### Performance Criteria
- Handle real-time booking checks
- Transactions must ensure atomicity

---

##  Summary

| Feature              | Endpoints Covered | Auth Required | Notes                        |
|----------------------|------------------|---------------|------------------------------|
| User Authentication  | 2                | No (login)    | JWT for session mgmt         |
| Property Management  | 2                | Yes           | Host role only for edits     |
| Booking System       | 2                | Yes           | Double-booking protection    |
