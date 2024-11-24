
---

### **requirements.md**

# Airbnb Clone Backend Requirement Specifications

## **1. User Authentication**

### **Objective**
Provide secure user authentication to allow guests and hosts to register, log in, and manage their accounts.

### **API Endpoints**
1. **POST** `/api/auth/register/`
   - **Description**: Registers a new user as a guest or host.
   - **Input**:
     ```json
     {
       "email": "string",
       "password": "string",
       "role": "string (guest/host)"
     }
     ```
   - **Output**:
     ```json
     {
       "message": "User registered successfully",
       "user_id": "integer"
     }
     ```
   - **Validation Rules**:
     - `email`: Must be a valid email address and unique.
     - `password`: Minimum 8 characters, includes letters and numbers.
     - `role`: Must be "guest" or "host".

2. **POST** `/api/auth/login/`
   - **Description**: Logs in a registered user.
   - **Input**:
     ```json
     {
       "email": "string",
       "password": "string"
     }
     ```
   - **Output**:
     ```json
     {
       "token": "string (JWT token)",
       "user": {
         "id": "integer",
         "email": "string",
         "role": "string"
       }
     }
     ```
   - **Validation Rules**:
     - `email`: Must exist in the database.
     - `password`: Must match the hashed password.

### **Performance Criteria**
- User registration response time should not exceed **500ms**.
- JWT tokens should be generated within **300ms**.

---

## **2. Property Management**

### **Objective**
Enable hosts to create, update, and delete property listings.

### **API Endpoints**
1. **POST** `/api/properties/`
   - **Description**: Adds a new property listing.
   - **Input**:
     ```json
     {
       "title": "string",
       "description": "string",
       "location": "string",
       "price": "float",
       "amenities": ["string"],
       "availability": {
         "start_date": "YYYY-MM-DD",
         "end_date": "YYYY-MM-DD"
       }
     }
     ```
   - **Output**:
     ```json
     {
       "message": "Property created successfully",
       "property_id": "integer"
     }
     ```
   - **Validation Rules**:
     - `title`: Required, max 150 characters.
     - `price`: Must be a positive number.
     - `availability.start_date`: Must be in the future.

2. **PUT** `/api/properties/{id}/`
   - **Description**: Updates an existing property listing.
   - **Validation**:
     - `id`: Must belong to the authenticated user.

3. **DELETE** `/api/properties/{id}/`
   - **Description**: Deletes a property listing.

### **Performance Criteria**
- Property creation and updates should take no longer than **700ms**.
- Bulk fetching of properties should support **pagination** with a max response time of **1s** for 100 items.

---

## **3. Booking System**

### **Objective**
Allow guests to book properties and hosts to manage bookings.

### **API Endpoints**
1. **POST** `/api/bookings/`
   - **Description**: Creates a new booking.
   - **Input**:
     ```json
     {
       "property_id": "integer",
       "guest_id": "integer",
       "start_date": "YYYY-MM-DD",
       "end_date": "YYYY-MM-DD"
     }
     ```
   - **Output**:
     ```json
     {
       "message": "Booking created successfully",
       "booking_id": "integer",
       "status": "string (pending/confirmed)"
     }
     ```
   - **Validation Rules**:
     - `property_id`: Must exist and be available for the given dates.
     - `start_date`: Must be before `end_date`.

2. **GET** `/api/bookings/`
   - **Description**: Fetches all bookings for a user (guest or host).
   - **Output**:
     ```json
     [
       {
         "booking_id": "integer",
         "property": "string",
         "status": "string",
         "dates": {
           "start_date": "YYYY-MM-DD",
           "end_date": "YYYY-MM-DD"
         }
       }
     ]
     ```

3. **PUT** `/api/bookings/{id}/cancel/`
   - **Description**: Cancels an existing booking.

### **Performance Criteria**
- Booking creation should validate double bookings in **500ms**.
- Booking fetches should be paginated with a max response time of **1s**.

---
