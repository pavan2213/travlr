# Travlr Getaways - Software Design Document (SDD)

**Project:** Travlr Full Stack Web Application  
**Client:** Travlr Getaways  
**Date:** February 22, 2026  
**Module:** CS 465 - Full Stack Development (Complete Course)  
**Status:** Production Ready

---

## 1. Executive Summary

### 1.1 Project Overview

Travlr Getaways required a comprehensive travel booking web application enabling customers to:
- Search and browse travel packages
- Create accounts and manage bookings
- View itineraries and reservation details
- Access an administrative interface for package management

This Software Design Document outlines the architecture, design decisions, and implementation approach for Travlr Getaways' full-stack web application using the MEAN stack (MongoDB, Express, Angular, Node.js).

### 1.2 Architecture Approach: The MEAN Stack

The application implements a modern, scalable MEAN stack architecture:

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Database** | MongoDB | NoSQL database for flexible data storage |
| **Backend** | Node.js + Express | RESTful API server for business logic |
| **Frontend (Customer)** | Handlebars (HBS) Templates | Server-rendered customer website |
| **Frontend (Admin)** | Angular (SPA) | Rich, interactive admin dashboard |

### 1.3 Key Design Principles

1. **Separation of Concerns:** Distinct layers for data, business logic, and presentation
2. **RESTful Architecture:** Standard HTTP verbs (GET, POST, PUT, DELETE) for API operations
3. **Single Page Application:** Angular SPA eliminates page reloads for admin functionality
4. **Security First:** JWT authentication, password hashing, and protected endpoints
5. **Database-Driven:** MongoDB provides flexible, scalable data persistence
6. **Modular Development:** Reusable components, services, and controllers

### 1.4 Technology Stack Summary

```
┌─────────────────────────────────────────────────────────────────┐
│                    Travlr Getaways Architecture                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                   │
│  FRONTEND LAYER                                                  │
│  ├─ Customer Website: Handlebars Templates + Bootstrap          │
│  └─ Admin Dashboard: Angular 17 SPA + TypeScript                │
│                                                                   │
│  ↓ HTTP/REST API                                                │
│                                                                   │
│  BACKEND LAYER                                                   │
│  ├─ Express.js Web Framework                                    │
│  ├─ RESTful API Endpoints                                       │
│  ├─ JWT Authentication & Authorization                          │
│  └─ Business Logic Controllers                                  │
│                                                                   │
│  ↓ Mongoose ODM                                                 │
│                                                                   │
│  DATA LAYER                                                      │
│  └─ MongoDB (NoSQL Database)                                    │
│                                                                   │
└─────────────────────────────────────────────────────────────────┘
```

---

## 2. System Architecture View

### 2.1 Component Diagram

#### 2.1.1 High-Level System Components

```
┌──────────────────────────────────────────────────────────────────────────┐
│                         Client Information Systems                        │
├──────────────────────────────────────────────────────────────────────────┤
│                                                                            │
│  ┌────────────────────────────┐          ┌─────────────────────────────┐ │
│  │  Customer-Facing Website   │          │   Admin Single Page App     │ │
│  │  (Handlebars Templating)   │          │      (Angular SPA)          │ │
│  │                            │          │                             │ │
│  │  - Homepage (Public)       │          │  Protected Routes:          │ │
│  │  - Trip Listings           │          │  - Trip Management          │ │
│  │  - Contact/About Pages     │          │  - Add/Edit/Delete Trips    │ │
│  │  - News Section            │          │  - User Authentication      │ │
│  │  - Meals/Rooms Display     │          │  - Admin Dashboard          │ │
│  │                            │          │                             │ │
│  │  Port: 3000                │          │  Port: 4200                 │ │
│  └────────────────────────────┘          └─────────────────────────────┘ │
│              │                                     │                      │
│              └─────────────────┬───────────────────┘                      │
│                                │                                          │
│                    REST API Calls (HTTP/JSON)                            │
│                                │                                          │
│  ┌─────────────────────────────▼──────────────────────────────────────┐  │
│  │              Backend API Server (Express.js)                       │  │
│  │                                                                    │  │
│  │  Authentication Routes:                  CRUD Routes:             │  │
│  │  - POST /api/register (Public)          - GET /api/trips (Public)│  │
│  │  - POST /api/login (Public)             - POST /api/trips (Auth) │  │
│  │  - GET /api/validate (Protected)        - PUT /api/trips (Auth)  │  │
│  │                                          - DELETE /api/trips(Auth)│  │
│  │  Middleware:                             Controllers:             │  │
│  │  - JWT Verification                     - Authentication Logic   │  │
│  │  - Error Handling                       - CRUD Operations        │  │
│  │  - CORS                                 - Data Validation        │  │
│  │                                          - Response Formatting    │  │
│  │                                                                    │  │
│  └─────────────────────────────┬──────────────────────────────────────┘  │
│                                │                                          │
│                    Mongoose ODM (Data Mapping)                           │
│                                │                                          │
│  ┌─────────────────────────────▼──────────────────────────────────────┐  │
│  │              Database Layer (MongoDB)                              │  │
│  │                                                                    │  │
│  │  Collections:                                                      │  │
│  │  ├─ users (Authentication & User Management)                     │  │
│  │  │   └─ Fields: _id, email, password (hashed), role, createdAt  │  │
│  │  │                                                                │  │
│  │  └─ trips (Travel Package Management)                            │  │
│  │      └─ Fields: _id, code, name, resort, description, start,    │  │
│  │              length, perPerson, image, createdBy, updatedAt     │  │
│  │                                                                    │  │
│  └─────────────────────────────────────────────────────────────────────┘  │
│                                                                            │
└──────────────────────────────────────────────────────────────────────────┘
```

#### 2.1.2 Component Details

**Frontend Components (Angular SPA):**
- **Login Component:** Authentication UI with email/password form
- **Trip List Component:** Displays all trips in a responsive grid
- **Trip Card Component:** Individual trip display with edit/delete actions
- **Trip Add Component:** Form for creating new trips
- **Trip Edit Component:** Form for modifying existing trips

**Backend Components (Express):**
- **Authentication Controller:** Handles register, login, validate operations
- **Trips Controller:** Manages CRUD operations for trip data
- **Auth Middleware:** Validates JWT tokens on protected endpoints
- **User Model:** Defines user schema and password hashing logic
- **Trip Model:** Defines trip schema and validation rules

**Data Layer (MongoDB):**
- **Users Collection:** Stores admin accounts with hashed passwords
- **Trips Collection:** Stores travel package information

---

### 2.2 Sequence Diagrams

#### 2.2.1 User Registration & Login Sequence

```
┌──────────┐         ┌──────────────────┐         ┌─────────────────┐
│  Browser │         │  Express Server  │         │  MongoDB        │
└────┬─────┘         └────┬─────────────┘         └────┬────────────┘
     │                    │                             │
     │ 1. Display         │                             │
     │    Login Form      │                             │
     ├─────────────────>  │                             │
     │                    │                             │
     │ 2. User Enters     │                             │
     │    email/password  │                             │
     │    & Clicks Login  │                             │
     │                    │                             │
     │ 3. POST /api/login │                             │
     │    (email, pwd)    │                             │
     ├───────────────────>│                             │
     │                    │ 4. Hash password          │
     │                    │    and verify             │
     │                    │                            │
     │                    │ 5. Query users            │
     │                    │    collection             │
     │                    ├───────────────────────────>│
     │                    │ 6. Return matching user   │
     │                    │<───────────────────────────┤
     │                    │                            │
     │                    │ 7. Compare password hash  │
     │                    │    Valid = generate JWT   │
     │                    │                            │
     │ 8. Return JWT      │                             │
     │    token           │                             │
     │<───────────────────┤                             │
     │                    │                             │
     │ 9. Store token     │                             │
     │    in localStorage │                             │
     │                    │                             │
     │ 10. Redirect to    │                             │
     │     /trips         │                             │
     │                    │                             │
```

#### 2.2.2 Trip Creation (Admin User) Sequence

```
┌──────────┐         ┌──────────────────┐         ┌─────────────────┐
│  Admin   │         │  Express Server  │         │  MongoDB        │
│  Browser │         │                  │         │                 │
└────┬─────┘         └────┬─────────────┘         └────┬────────────┘
     │                    │                             │
     │ 1. Display         │                             │
     │    Add Trip Form   │                             │
     ├─────────────────>  │                             │
     │                    │                             │
     │ 2. User Fills      │                             │
     │    Trip Details    │                             │
     │    & Clicks Submit │                             │
     │                    │                             │
     │ 3. POST /api/trips │                             │
     │    + JWT Token     │                             │
     │    (in header)     │                             │
     ├───────────────────>│                             │
     │                    │                             │
     │                    │ 4. JWT Middleware         │
     │                    │    Verify token           │
     │                    │    Extract user ID        │
     │                    │                            │
     │                    │ 5. Validate trip data     │
     │                    │    Check required fields  │
     │                    │                            │
     │                    │ 6. Create new trip doc    │
     │                    │    Add createdBy field    │
     │                    │                            │
     │                    │ 7. Insert into trips      │
     │                    │    collection             │
     │                    ├───────────────────────────>│
     │                    │ 8. Return inserted doc    │
     │                    │<───────────────────────────┤
     │                    │                            │
     │ 9. Return 201      │                             │
     │    Created + trip  │                             │
     │<───────────────────┤                             │
     │                    │                             │
     │ 10. Display        │                             │
     │     success msg    │                             │
     │     and refresh    │                             │
     │     trip list      │                             │
     │                    │                             │
```

#### 2.2.3 Protected Endpoint Access Sequence

```
┌──────────┐         ┌──────────────────┐         ┌─────────────────┐
│  Browser │         │  Express Server  │         │  MongoDB        │
└────┬─────┘         └────┬─────────────┘         └────┬────────────┘
     │                    │                             │
     │ 1. HTTP Request    │                             │
     │    DELETE /trips   │                             │
     │    + JWT in header │                             │
     ├───────────────────>│                             │
     │                    │                             │
     │                    │ 2. Auth Middleware        │
     │                    │    Extract token          │
     │                    │                            │
     │                    │ 3. Verify JWT signature   │
     │                    │    Check expiration       │
     │                    │                            │
     │ 4. Token Invalid?  │                             │
     │ (401 Unauthorized) │                             │
     │<───────────────────┤                             │
     │                    │                             │
     │ 5. Angular Catches │                             │
     │    401 Response    │                             │
     │                    │                             │
     │ 6. Redirect to     │                             │
     │    /login          │                             │
     │                    │                             │
     │ 7. Display login   │                             │
     │    form again      │                             │
     │                    │                             │
```

---

### 2.3 Data Flow Architecture

```
User Request → Routing Layer → Middleware → Controller → Model → Database
    ↑                                                                  │
    │                                                                  │
    └──────────── Response Serialization ←───────────────────────────┘
```

**Request Flow Steps:**

1. **Routing:** Express routes incoming HTTP request to appropriate controller
2. **Middleware:** JWT verification, CORS, body parsing, error handling
3. **Controller:** Business logic, data validation, service calls
4. **Model:** Mongoose schema validation, data type checking
5. **Database:** MongoDB stores/retrieves data
6. **Response:** JSON serialization and return to client

---

## 3. User Interface Design

### 3.1 Angular Project Structure vs. Express Project Structure

#### 3.1.1 Express Project Structure

```
travlr/ (Express Backend)
├── app.js                          # Main application entry
├── package.json                    # Dependencies
├── bin/
│   └── www                         # Server startup script
├── routes/
│   ├── index.js                    # Main routes
│   └── users.js                    # User routes
├── controllers/
│   └── travel.js                   # Business logic
├── views/                          # Handlebars templates
│   ├── layout.hbs                  # Base template
│   ├── index.hbs                   # Homepage
│   └── ...                         # Other pages
├── public/                         # Static files
│   ├── css/
│   ├── images/
│   └── index.html
├── data/                           # Static data (seed files)
└── app_api/                        # RESTful API
    ├── routes/
    │   └── index.js                # API endpoints
    ├── controllers/
    │   ├── authentication.js        # Auth logic
    │   └── trips.js                # Trip CRUD
    ├── models/
    │   ├── user.js                 # User schema
    │   └── trip.js                 # Trip schema
    ├── middleware/
    │   └── auth.js                 # JWT verification
    └── database/
        ├── db.js                   # MongoDB connection
        └── seed.js                 # Initial data
```

**Express Architecture Characteristics:**
- **Server-Rendered:** Handlebars templates rendered on server
- **Request-Response Cycle:** Each interaction triggers new request
- **Monolithic Structure:** Frontend and backend closely coupled
- **Session-Based:** Stateless HTTP with JWT tokens
- **RESTful API:** Separate API layer for SPA consumption
- **Template Engine:** Handlebars (HBS) for dynamic content

#### 3.1.2 Angular Project Structure

```
app_admin/ (Angular Frontend)
├── package.json                    # Dependencies
├── angular.json                    # Angular configuration
├── src/
│   ├── index.html                  # SPA entry point (single HTML)
│   ├── main.ts                     # Bootstrap file
│   ├── styles.css                  # Global styles
│   └── app/
│       ├── app.module.ts           # Root module (imports, providers)
│       ├── app-routing.module.ts   # Route configuration
│       ├── app.component.*         # Root component
│       ├── models/
│       │   ├── trip.ts             # Trip interface
│       │   ├── user.ts             # User interface
│       │   └── auth-response.ts    # Auth response interface
│       ├── services/
│       │   ├── authentication.service.ts  # Auth logic
│       │   ├── trip-data.service.ts       # API calls
│       │   ├── auth.guard.ts              # Route protection
│       │   └── jwt.interceptor.ts         # Token injection
│       ├── login/
│       │   ├── login.component.ts
│       │   ├── login.component.html
│       │   └── login.component.css
│       ├── trip-list/
│       │   └── ...
│       ├── trip-card/
│       │   └── ...
│       ├── trip-add/
│       │   └── ...
│       └── trip-edit/
│           └── ...
└── tsconfig.json                   # TypeScript configuration
```

**Angular Architecture Characteristics:**
- **Client-Side Rendering:** Templates rendered in browser
- **Single Page Application:** No full page reloads
- **Component-Based:** Reusable UI components
- **Service-Oriented:** Separates business logic into services
- **Dependency Injection:** Angular's DI system for loose coupling
- **TypeScript:** Type safety and modern language features
- **Reactive:** Angular bindings for dynamic UIs
- **Route Guards:** Protection of sensitive routes

#### 3.1.3 Key Architectural Differences

| Aspect | Express | Angular |
|--------|---------|---------|
| **Rendering** | Server-side | Client-side |
| **Page Reloads** | Full reload per request | No reloads (SPA) |
| **Architecture** | MVC (templates) | Component-based |
| **Language** | JavaScript (Node.js) | TypeScript |
| **State Management** | Server session | Client localStorage |
| **API Integration** | Both view & API | API-only client |
| **Performance** | Server processing | Client-side processing |
| **Scalability** | Easier horizontal scaling | Reduced server load |

### 3.2 Rich Functionality: SPA vs. Traditional Web Application

#### 3.2.1 Single Page Application (Angular) Advantages

```
Traditional Web App          vs.  Single Page Application (Angular)
────────────────────────────       ─────────────────────────────────

User clicks link             User clicks link
       ↓                             ↓
Server receives request      Client-side router handles
       ↓                             ↓
Template rendered            Component loads
       ↓                             ↓
Full page refreshes          Only affected component updates
       ↓                             ↓
User sees loading lag        Seamless, instant experience
```

**Angular SPA Benefits:**

1. **Responsive User Experience**
   - No full page reloads
   - Instantaneous route navigation
   - Desktop-like application feel

2. **Reduced Bandwidth Usage**
   - Initial load downloads entire app
   - Subsequent interactions download only data (JSON)
   - Smaller payloads vs. HTML templates

3. **Better UI State Management**
   - Component state persists between navigation
   - Form data retained during navigation
   - Undo/redo functionality possible

4. **Offline Capability**
   - Data cached locally
   - Works offline with cached data
   - Syncs when connection restored

5. **Rich Interactive Features**
   - Real-time form validation
   - Drag-and-drop operations
   - Animated transitions
   - Modal dialogs
   - Auto-save capabilities

6. **Separation of Concerns**
   - Frontend completely decoupled from backend
   - Frontend developers can work independently
   - Backend provides data, frontend presents data

7. **Scalability**
   - Client-side processing reduces server load
   - Multiple frontend frameworks can consume same API
   - Easy to add mobile apps using same backend

#### 3.2.2 Angular Components in Travlr Application

**Component Hierarchy:**
```
AppComponent (Root)
├── LoginComponent (Route: /login)
├── TripListComponent (Route: /trips)
│   └── TripCardComponent (Child - Reusable)
├── TripAddComponent (Route: /add-trip)
└── TripEditComponent (Route: /edit-trip/:id)
```

**Component Interactions:**
- **Shared Services:** TripsDataService manages API calls
- **AuthService:** Handles authentication state
- **Guards:** AuthGuard prevents unauthorized access
- **Interceptors:** JwtInterceptor adds authorization headers

**Rich Features Enabled:**
- Reactive forms with validation
- Loading states and spinners
- Error message display
- Success notifications
- Confirmation dialogs
- Form directives and validators

---

## 3.3 Testing Process: SPA Integration with API

### 3.3.1 GET Operation Testing (Retrieving Trips)

**Test Scenario: Fetch all trips from database**

```
Step 1: Initialize Test
├─ Start Angular dev server (port 4200)
├─ Start Express API server (port 3000)
├─ Ensure MongoDB is running
└─ Database contains seed data (3 test trips)

Step 2: User Navigation
├─ User navigates to http://localhost:4200/trips
└─ TripListComponent initializes

Step 3: Component Lifecycle
├─ ngOnInit() executes
├─ Calls TripDataService.getTrips()
└─ HTTP GET /api/trips request sent to backend

Step 4: Backend Processing
├─ Express receives GET /api/trips
├─ No authentication required (public endpoint)
├─ TripsController.getTrips() executes
├─ Queries MongoDB trips collection
├─ Returns array of trip documents
└─ Sends JSON response (200 OK)

Step 5: Frontend Reception
├─ Angular HTTP client receives response
├─ TripsDataService processes JSON
├─ Pushes data to component Observable
├─ Component's subscription receives data
├─ Template loop iterates over trips array

Step 6: UI Rendering
├─ TripCardComponent renders for each trip
├─ Dynamic data displayed in cards
├─ User sees trip list with details
└─ No page reload occurred (SPA behavior)

Expected Result:
─────────────────────────────────────────
✅ 3 trips displayed on screen
✅ All trip properties showing correctly
✅ Card styling intact and responsive
✅ No console errors
✅ Network request successful (200)
✅ No page reload occurred
```

**JSON Response Example:**
```json
[
  {
    "_id": "507f1f77bcf86cd799439011",
    "code": "GALR210214",
    "name": "Gale Reef",
    "resort": "Emerald Bay Resort",
    "description": "A wonderful island vacation",
    "start": "2021-02-14T00:00:00.000Z",
    "length": 5,
    "perPerson": 799,
    "image": "/images/gale-reef.jpg"
  },
  ...
]
```

### 3.3.2 PUT Operation Testing (Updating Trips)

**Test Scenario: Admin edits a trip and saves changes**

```
Step 1: User Authentication
├─ User logs in with credentials
├─ POST /api/login sends email/password
├─ Backend validates and returns JWT token
├─ Angular stores token in localStorage
└─ Auth header ready for protected requests

Step 2: User Initiates Edit
├─ User clicks "Edit" button on trip card
├─ Router navigates to /edit-trip/GALR210214
├─ TripEditComponent loads trip data

Step 3: Load Existing Data
├─ Component calls TripDataService.getTrip(code)
├─ HTTP GET /api/trips/GALR210214 with JWT
├─ Backend verifies JWT validity
├─ TripsController retrieves trip from MongoDB
├─ Current trip data sent back in JSON response

Step 4: Form Population
├─ Angular receives trip object
├─ Form fields populated with current values
├─ Reactive form created with FormBuilder

Step 5: User Modification
├─ User updates trip details (e.g., price, description)
├─ Form validation runs on each keystroke
├─ Submit button enabled (validation passed)

Step 6: Submit Changes
├─ User clicks "Save Changes"
├─ Form emits PUT request with updated data
├─ HTTP PUT /api/trips + JWT token + new data
├─ JwtInterceptor automatically adds Authorization header

Step 7: Backend Processing
├─ Express receives PUT /api/trips/GALR210214
├─ Auth middleware verifies JWT
├─ Validates user has permissions (or is creator)
├─ TripsController.updateTrip() executes
├─ MongoDB updates trip document with new values
├─ Updated document returned in response

Step 8: Frontend Response Handling
├─ Angular receives updated trip (200 OK)
├─ Component updates local data
├─ Form marked as pristine
├─ Success message displays

Step 9: UI Update
├─ Navigation back to trip list (/trips)
├─ TripListComponent refreshes data
├─ Updated trip displayed in grid
├─ Toast notification confirms success

Expected Result:
─────────────────────────────────────────
✅ Trip successfully updated in database
✅ Updated values visible in trip list
✅ Success notification displayed
✅ Form returned to pristine state
✅ No page reload (SPA navigation)
✅ JWT protection verified
✅ Unauthorized user cannot update
```

**Request/Response Example:**

```javascript
// Frontend PUT Request
PUT /api/trips/GALR210214
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
Content-Type: application/json

{
  "name": "Gale Reef - Updated",
  "perPerson": 899,
  "description": "Updated description"
}

// Backend JSON Response
{
  "statusCode": 200,
  "message": "Trip updated successfully",
  "trip": {
    "_id": "507f1f77bcf86cd799439011",
    "code": "GALR210214",
    "name": "Gale Reef - Updated",
    "perPerson": 899,
    "description": "Updated description",
    "updatedAt": "2026-02-22T10:30:00Z"
  }
}
```

### 3.3.3 Error Handling & Edge Cases

**Test Case: Unauthorized Access (No JWT Token)**

```
Scenario: User without valid token attempts to modify trip

Step 1: User attempts action without login
├─ User directly calls API (e.g., via curl)
└─ No Authorization header included

Step 2: Backend receives request
├─ Express middleware checks for Authorization header
├─ JWT not found or invalid
└─ Returns 401 Unauthorized response

Step 3: Frontend Error Handling
├─ Angular HTTP interceptor catches 401
├─ User automatically redirected to /login
├─ Error message displayed: "Session expired. Please login."
├─ localStorage token cleared
└─ User forced re-authentication

Expected Result:
─────────────────────────────────────────
✅ 401 status code returned
✅ User redirected to login page
✅ Unauthorized action prevented
✅ No database modification occurred
```

**Test Case: Invalid JWT Token**

```
Scenario: Tampered or expired token

Step 1: Token verification
├─ JWT signature verification fails
├─ OR token expiration date passed
└─ Auth middleware rejects request

Step 2: Response
├─ 401 Unauthorized returned
├─ Error message: "Invalid or expired token"

Step 3: Frontend handling
├─ Interceptor catches 401
├─ localStorage token cleared
├─ User redirected to login
└─ Must re-authenticate

Expected Result:
─────────────────────────────────────────
✅ Tampered tokens rejected
✅ Expired tokens handled gracefully
✅ Security maintained
✅ User experience preserved
```

### 3.3.4 Integration Testing Checklist

**GET Endpoints (Public)**
- ✅ GET /api/trips - Retrieve all trips
- ✅ GET /api/trips/:code - Retrieve single trip
- ✅ No authentication required
- ✅ Response contains correct data
- ✅ Angular components render data

**POST Endpoints (Protected)**
- ✅ POST /api/register - Create user account
- ✅ POST /api/login - Authenticate user
- ✅ POST /api/trips - Create new trip (requires auth)
- ✅ JWT generation and return
- ✅ Token stored in localStorage
- ✅ Error responses for validation failures

**PUT Endpoints (Protected)**
- ✅ PUT /api/trips/:code - Update trip (requires auth)
- ✅ JwtInterceptor adds Authorization header
- ✅ Database updated successfully
- ✅ Response contains updated document
- ✅ UI reflects changes immediately

**DELETE Endpoints (Protected)**
- ✅ DELETE /api/trips/:code - Delete trip (requires auth)
- ✅ 401 returned without valid JWT
- ✅ Trip removed from database
- ✅ Success response returned
- ✅ Frontend list updates automatically

**Error Handling**
- ✅ 400 Bad Request - Invalid data
- ✅ 401 Unauthorized - Missing/invalid JWT
- ✅ 409 Conflict - Duplicate email on registration
- ✅ 500 Server Error - Database/server issues
- ✅ All errors handled gracefully
- ✅ User-friendly messages displayed

### 3.3.5 Postman Testing Integration

**Execution Flow:**

```
1. Register new test user
   POST /api/register
   ├─ Username: testadmin
   ├─ Email: test@travlr.com
   └─ Password: Test12345!

2. Login (save token to environment)
   POST /api/login
   ├─ Email: test@travlr.com
   ├─ Password: Test12345!
   └─ Response extracts token → environment variable

3. Test protected endpoints (token auto-added)
   GET /api/trips
   ├─ Header: Authorization: Bearer {{auth_token}}
   └─ Verify 200 response with trip data

   POST /api/trips
   ├─ Create new trip with token
   └─ Verify trip saved to database

   PUT /api/trips/GALR210214
   ├─ Update existing trip
   └─ Verify changes in response

   DELETE /api/trips/GALR210214
   ├─ Delete trip with auth
   └─ Verify 204 No Content response

4. Validate token endpoint
   GET /api/validate
   ├─ Verify token still valid
   └─ Confirm user information returned
```

---

## 4. Security Implementation

### 4.1 Authentication Flow

**User Registration:**
```
Email + Password → Hash (bcryptjs) → Store in MongoDB
                                      ↓
                              User created, login ready
```

**User Login:**
```
Email + Password → MongoDB lookup → Compare with hash → Generate JWT
                                                         ↓
                                                    Return token to client
```

**Protected Requests:**
```
HTTP Request + JWT → Auth Middleware → Verify signature + expiry
                                       ↓
                                   Valid: Allow request
                                   Invalid: Return 401
```

### 4.2 Protected Endpoints

| Endpoint | Method | Authentication | Purpose |
|----------|--------|----------------|---------|
| /api/register | POST | None | Create admin account |
| /api/login | POST | None | Authenticate user |
| /api/validate | GET | Required | Verify token validity |
| /api/trips | GET | None | Public view of trips |
| /api/trips/:code | GET | None | Public single trip view |
| /api/trips | POST | Required | Create new trip |
| /api/trips/:code | PUT | Required | Update trip |
| /api/trips/:code | DELETE | Required | Delete trip |

---

## 5. Database Schema

### 5.1 User Collection

```javascript
{
  _id: ObjectId,
  email: { type: String, unique: true, required: true },
  password: { type: String, required: true }, // bcryptjs hashed
  role: { type: String, default: 'admin' },
  createdAt: { type: Date, default: Date.now }
}
```

### 5.2 Trip Collection

```javascript
{
  _id: ObjectId,
  code: { type: String, unique: true, required: true },
  name: { type: String, required: true },
  resort: { type: String, required: true },
  description: { type: String },
  start: { type: Date, required: true },
  length: { type: Number, required: true },
  perPerson: { type: Number, required: true },
  image: { type: String },
  createdBy: ObjectId, // Reference to user who created
  createdAt: { type: Date, default: Date.now },
  updatedAt: { type: Date, default: Date.now }
}
```

---

## 6. Deployment Architecture

### 6.1 Development Environment (Current)

- **Frontend:** Angular dev server (port 4200)
- **Backend:** Node.js/Express (port 3000)
- **Database:** Local MongoDB (port 27017)
- **Tools:** npm, Angular CLI, Postman

### 6.2 Production Deployment Options

**Option 1: Cloud Platform (Heroku, AWS)**
```
                  Internet
                     ↓
            Load Balancer
                     ↓
      ┌──────────────┴──────────────┐
Express Server 1              Express Server 2
      ↓                             ↓
   API Traffic                  API Traffic
      ↓                             ↓
         MongoDB Atlas (Cloud)
                     ↓
            Data Persistence
```

**Option 2: Containerized (Docker)**
```
Docker Container 1: Frontend (Nginx serving Angular build)
Docker Container 2: Backend (Node.js/Express)
Docker Container 3: MongoDB

All orchestrated with Docker Compose or Kubernetes
```

---

## 7. Conclusion

The Travlr Getaways full-stack application demonstrates mastery of modern web development architecture by:

1. **Separating concerns** into distinct frontend, backend, and data layers
2. **Implementing security** with JWT authentication and password hashing
3. **Leveraging frameworks** (Express, Angular, Mongoose) for rapid development
4. **Providing rich UX** through a Single Page Application
5. **Ensuring scalability** with RESTful API and NoSQL database
6. **Validating functionality** through comprehensive testing

The MEAN stack architecture provides flexibility, scalability, and modern development practices suitable for enterprise applications.

---

**Document Version:** 1.0  
**Last Updated:** February 22, 2026  
**Next Review:** Post-deployment feedback
