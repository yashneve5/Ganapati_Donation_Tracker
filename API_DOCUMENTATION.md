# 📚 Ganpati Tracker API Documentation

## 🎯 Overview

The Ganpati Tracker API provides comprehensive endpoints for managing donations, expenses, users, analytics, and receipts for Ganpati festival celebrations. This RESTful API supports JWT-based authentication, role-based permissions, and comprehensive data validation.

## 🔗 Base URL

```
http://localhost:5000/api
```

## 🔐 Authentication

The API uses JWT (JSON Web Tokens) for authentication. Include the token in the Authorization header:

```
Authorization: Bearer <your-jwt-token>
```

### Authentication Flow

1. **Register/Login** → Receive JWT token
2. **Include token** in subsequent requests
3. **Token expires** → Use refresh token or re-login

## 👥 User Roles & Permissions

| Role | Permissions |
|------|-------------|
| **Admin** | All permissions (manage users, approve expenses, view all data) |
| **Moderator** | Approve expenses, manage donations/expenses, view analytics |
| **Volunteer** | Add donations/expenses, view basic analytics |
| **Viewer** | View donations, basic analytics (read-only) |

## 📋 API Endpoints

### 🔐 Authentication Endpoints

#### POST `/auth/register`
Register a new user

**Request Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123",
  "phone": "+91 98765 43210",
  "role": "viewer"
}
```

**Response:**
```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "user": {
      "id": "user_id",
      "name": "John Doe",
      "email": "john@example.com",
      "role": "viewer",
      "status": "active"
    },
    "tokens": {
      "authToken": "jwt_token",
      "refreshToken": "refresh_token"
    }
  }
}
```

#### POST `/auth/login`
Login user

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```

**Response:**
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "user": {
      "id": "user_id",
      "name": "John Doe",
      "email": "john@example.com",
      "role": "viewer",
      "permissions": ["view_donations", "view_analytics"]
    },
    "tokens": {
      "authToken": "jwt_token",
      "refreshToken": "refresh_token"
    }
  }
}
```

#### POST `/auth/refresh`
Refresh JWT token

**Request Body:**
```json
{
  "refreshToken": "refresh_token"
}
```

#### GET `/auth/me`
Get current user profile

**Headers:** `Authorization: Bearer <token>`

**Response:**
```json
{
  "success": true,
  "data": {
    "id": "user_id",
    "name": "John Doe",
    "email": "john@example.com",
    "role": "viewer",
    "permissions": ["view_donations", "view_analytics"],
    "lastLoginAt": "2024-01-01T10:00:00.000Z"
  }
}
```

#### POST `/auth/logout`
Logout user (invalidate tokens)

**Headers:** `Authorization: Bearer <token>`

### 💰 Donation Endpoints

#### GET `/donations`
Get all donations with filtering and pagination

**Query Parameters:**
- `page` (number): Page number (default: 1)
- `limit` (number): Items per page (default: 10)
- `wing` (string): Filter by wing (A, B, C, D, E)
- `paymentMode` (string): Filter by payment mode
- `startDate` (date): Filter from date
- `endDate` (date): Filter to date
- `search` (string): Search in name, ID, note
- `sortBy` (string): Sort field (default: createdAt)
- `sortOrder` (string): Sort order (asc/desc, default: desc)

**Example Request:**
```
GET /donations?page=1&limit=10&wing=A&paymentMode=UPI&startDate=2024-01-01&endDate=2024-01-31
```

**Response:**
```json
{
  "success": true,
  "data": {
    "donations": [
      {
        "id": "donation_id",
        "donationId": "GP20240101123ABC",
        "name": "John Doe",
        "email": "john@example.com",
        "phone": "+91 98765 43210",
        "wing": "A",
        "floor": 3,
        "flat": "302",
        "amount": 1001,
        "paymentMode": "UPI",
        "date": "2024-01-01T00:00:00.000Z",
        "note": "Ganpati Bappa Morya",
        "createdAt": "2024-01-01T10:00:00.000Z"
      }
    ],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalDonations": 50,
      "hasNextPage": true,
      "hasPrevPage": false,
      "limit": 10
    }
  }
}
```

#### POST `/donations`
Create new donation

**Request Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "phone": "+91 98765 43210",
  "wing": "A",
  "floor": 3,
  "flat": "302",
  "amount": 1001,
  "paymentMode": "UPI",
  "date": "2024-01-01",
  "note": "Ganpati Bappa Morya"
}
```

#### GET `/donations/:id`
Get donation by ID

#### PUT `/donations/:id`
Update donation (requires `manage_donations` permission)

#### DELETE `/donations/:id`
Delete donation (requires `manage_donations` permission)

#### GET `/donations/stats`
Get donation statistics

**Response:**
```json
{
  "success": true,
  "data": {
    "total": {
      "total": 50000,
      "count": 25,
      "average": 2000
    },
    "byWing": [
      { "wing": "A", "total": 15000, "count": 8 },
      { "wing": "B", "total": 12000, "count": 6 }
    ],
    "byPaymentMode": [
      { "mode": "UPI", "total": 25000, "count": 15 },
      { "mode": "Cash", "total": 20000, "count": 8 }
    ],
    "monthlyTrends": [
      { "month": "2024-01", "total": 30000, "count": 15 }
    ],
    "topDonors": [
      { "name": "John Doe", "total": 5000, "count": 3 }
    ]
  }
}
```

### 💸 Expense Endpoints

#### GET `/expenses`
Get all expenses (requires authentication)

**Query Parameters:** Similar to donations with additional:
- `category` (string): Filter by category
- `status` (string): Filter by status (pending, approved, rejected)
- `createdBy` (string): Filter by creator

#### POST `/expenses`
Create new expense (requires authentication)

**Request Body:**
```json
{
  "item": "Decoration Materials",
  "cost": 1500,
  "date": "2024-01-05",
  "reason": "Flowers and lights for mandap decoration",
  "category": "Decoration",
  "vendorName": "Flower Market",
  "vendorContact": "+91 98765 43210",
  "paymentMethod": "Cash"
}
```

#### PUT `/expenses/:id/approve`
Approve expense (requires `approve_expense` permission)

**Request Body:**
```json
{
  "reason": "Approved for festival decoration"
}
```

#### PUT `/expenses/:id/reject`
Reject expense (requires `approve_expense` permission)

**Request Body:**
```json
{
  "reason": "Insufficient documentation provided"
}
```

#### GET `/expenses/stats`
Get expense statistics

### 📊 Analytics Endpoints

#### GET `/analytics/dashboard`
Get dashboard analytics (requires `view_analytics` permission)

**Query Parameters:**
- `startDate` (date): Filter from date
- `endDate` (date): Filter to date

**Response:**
```json
{
  "success": true,
  "data": {
    "summary": {
      "donations": {
        "totalAmount": 50000,
        "totalCount": 25,
        "averageAmount": 2000
      },
      "expenses": {
        "totalAmount": 30000,
        "totalCount": 15,
        "averageAmount": 2000
      },
      "balance": 20000,
      "pendingExpensesCount": 3
    },
    "distributions": {
      "wings": [...],
      "paymentModes": [...],
      "expenseCategories": [...]
    },
    "trends": {
      "daily": [...]
    },
    "topDonors": [...],
    "recentActivities": {
      "donations": [...],
      "expenses": [...]
    }
  }
}
```

#### GET `/analytics/donations`
Get detailed donation analytics

#### GET `/analytics/expenses`
Get detailed expense analytics

#### GET `/analytics/comparison`
Get comparative analytics between periods

**Query Parameters:**
- `currentStartDate` (date): Current period start
- `currentEndDate` (date): Current period end
- `previousStartDate` (date): Previous period start
- `previousEndDate` (date): Previous period end

### 🧾 Receipt Endpoints

#### GET `/receipts/donation/:id`
Generate donation receipt (requires `view_receipts` permission)

#### GET `/receipts/expense/:id`
Generate expense receipt (requires `view_receipts` permission)

#### POST `/receipts/bulk-donations`
Generate bulk donation receipts

**Request Body:**
```json
{
  "donationIds": ["id1", "id2", "id3"],
  "filters": {
    "startDate": "2024-01-01",
    "endDate": "2024-01-31",
    "wing": "A",
    "paymentMode": "UPI"
  }
}
```

#### POST `/receipts/bulk-expenses`
Generate bulk expense receipts

#### GET `/receipts/summary`
Generate financial summary receipt

#### GET `/receipts/export/csv`
Export data as CSV

**Query Parameters:**
- `type` (string): "donations" or "expenses"
- `startDate` (date): Filter from date
- `endDate` (date): Filter to date

### 👥 User Management Endpoints

#### GET `/users`
Get all users (requires `manage_users` permission)

#### GET `/users/:id`
Get user by ID (own profile or `manage_users` permission)

#### POST `/users`
Create new user (requires `manage_users` permission)

#### PUT `/users/:id`
Update user (own profile or `manage_users` permission)

#### DELETE `/users/:id`
Delete user (requires `manage_users` permission)

#### PUT `/users/:id/role`
Update user role (requires `manage_users` permission)

#### PUT `/users/:id/status`
Update user status (requires `manage_users` permission)

#### GET `/users/stats/overview`
Get user statistics (requires `manage_users` permission)

#### POST `/users/:id/reset-password`
Reset user password (requires `manage_users` permission)

## 🔍 Query Parameters

### Pagination
```
?page=1&limit=10&sortBy=createdAt&sortOrder=desc
```

### Date Filtering
```
?startDate=2024-01-01&endDate=2024-01-31
```

### Search and Filtering
```
?search=john&wing=A&paymentMode=UPI&category=Decoration&status=approved
```

## 📝 Request/Response Format

### Standard Success Response
```json
{
  "success": true,
  "message": "Operation completed successfully",
  "data": {
    // Response data
  }
}
```

### Standard Error Response
```json
{
  "success": false,
  "message": "Error description",
  "errors": [
    {
      "field": "email",
      "message": "Please provide a valid email address"
    }
  ]
}
```

### Pagination Response
```json
{
  "success": true,
  "data": {
    "items": [...],
    "pagination": {
      "currentPage": 1,
      "totalPages": 5,
      "totalItems": 50,
      "hasNextPage": true,
      "hasPrevPage": false,
      "limit": 10
    }
  }
}
```

## 🚨 Error Codes

| Status Code | Description |
|-------------|-------------|
| 200 | Success |
| 201 | Created |
| 400 | Bad Request (validation errors) |
| 401 | Unauthorized (invalid/missing token) |
| 403 | Forbidden (insufficient permissions) |
| 404 | Not Found |
| 409 | Conflict (duplicate data) |
| 429 | Too Many Requests (rate limited) |
| 500 | Internal Server Error |

## 🔒 Security Features

### Rate Limiting
- **Global**: 100 requests per 15 minutes per IP
- **Auth endpoints**: Additional stricter limits

### Input Validation
- **Joi schemas** for all request validation
- **Sanitization** of user inputs
- **Type checking** and format validation

### Authentication Security
- **JWT tokens** with expiration
- **Refresh tokens** for session management
- **Password hashing** using bcrypt
- **Role-based permissions**

### CORS Protection
- Configured for specific frontend origins
- Credentials support for authenticated requests

## 📊 Data Models

### Donation Model
```javascript
{
  donationId: "GP20240101123ABC",
  name: "John Doe",
  email: "john@example.com",
  phone: "+91 98765 43210",
  wing: "A",
  floor: 3,
  flat: "302",
  amount: 1001,
  paymentMode: "UPI",
  date: Date,
  note: "Optional note",
  status: "confirmed",
  createdAt: Date,
  updatedAt: Date
}
```

### Expense Model
```javascript
{
  expenseId: "EX20240101123ABC",
  item: "Decoration Materials",
  cost: 1500,
  date: Date,
  reason: "Detailed reason",
  category: "Decoration",
  status: "approved",
  vendorName: "Vendor Name",
  vendorContact: "+91 98765 43210",
  paymentMethod: "Cash",
  createdBy: ObjectId,
  approvedBy: ObjectId,
  approvedAt: Date,
  createdAt: Date,
  updatedAt: Date
}
```

### User Model
```javascript
{
  name: "John Doe",
  email: "john@example.com",
  password: "hashed_password",
  phone: "+91 98765 43210",
  role: "viewer",
  status: "active",
  permissions: ["view_donations", "view_analytics"],
  lastLoginAt: Date,
  createdAt: Date,
  updatedAt: Date
}
```

## 🧪 Testing

### Health Check
```
GET /health
```

**Response:**
```json
{
  "status": "OK",
  "timestamp": "2024-01-01T10:00:00.000Z",
  "uptime": 3600,
  "environment": "development"
}
```

### Test Authentication
```bash
# Register
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Test User","email":"test@example.com","password":"password123"}'

# Login
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123"}'
```

### Test Donations
```bash
# Get donations
curl http://localhost:5000/api/donations

# Create donation
curl -X POST http://localhost:5000/api/donations \
  -H "Content-Type: application/json" \
  -d '{"name":"Test Donor","wing":"A","floor":1,"flat":"101","amount":500,"paymentMode":"Cash","date":"2024-01-01"}'
```

## 📱 Frontend Integration

### API Configuration
```javascript
const API_CONFIG = {
  BASE_URL: 'http://localhost:5000/api',
  ENDPOINTS: {
    LOGIN: '/auth/login',
    DONATIONS: '/donations',
    EXPENSES: '/expenses',
    ANALYTICS: '/analytics',
    RECEIPTS: '/receipts'
  }
};
```

### Authentication Helper
```javascript
class AuthManager {
  static setTokens(authToken, refreshToken) {
    localStorage.setItem('authToken', authToken);
    localStorage.setItem('refreshToken', refreshToken);
  }

  static getAuthToken() {
    return localStorage.getItem('authToken');
  }

  static async makeAuthenticatedRequest(url, options = {}) {
    const token = this.getAuthToken();
    return fetch(url, {
      ...options,
      headers: {
        ...options.headers,
        'Authorization': `Bearer ${token}`,
        'Content-Type': 'application/json'
      }
    });
  }
}
```

## 🚀 Deployment

### Environment Variables
```bash
NODE_ENV=production
PORT=5000
MONGODB_URI=mongodb://localhost:27017/ganpati_tracker
JWT_SECRET=your_super_secret_jwt_key
FRONTEND_URL=http://localhost:3000
EMAIL_SERVICE=gmail
EMAIL_USER=your_email@gmail.com
EMAIL_PASS=your_app_password
```

### Production Considerations
- Use HTTPS in production
- Set secure JWT secrets
- Configure proper CORS origins
- Enable MongoDB authentication
- Set up proper logging and monitoring
- Use environment-specific configurations

## 📞 Support

For API support or questions:
1. Check the logs in `backend/logs/`
2. Verify environment configuration
3. Test endpoints using curl or Postman
4. Check MongoDB connection and data

**Ganpati Bappa Morya! 🙏**