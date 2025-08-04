# 🚀 Ganpati Tracker Backend Setup & Integration Guide

## 📋 Table of Contents
1. [Overview](#overview)
2. [Prerequisites](#prerequisites)
3. [Installation Steps](#installation-steps)
4. [Database Setup](#database-setup)
5. [Environment Configuration](#environment-configuration)
6. [Frontend Integration](#frontend-integration)
7. [API Documentation](#api-documentation)
8. [Testing](#testing)
9. [Deployment](#deployment)
10. [Troubleshooting](#troubleshooting)

## 🎯 Overview

This backend provides a complete REST API for the Ganpati Tracker application with:

- **Authentication & Authorization** (JWT-based)
- **Donation Management** (CRUD operations with validation)
- **Expense Tracking** (Approval workflow)
- **Analytics & Reporting** (Real-time insights)
- **Receipt Generation** (PDF/Excel export)
- **User Management** (Role-based permissions)
- **Data Validation** (Comprehensive input validation)
- **Security Features** (Rate limiting, CORS, etc.)

## 🔧 Prerequisites

### Required Software
- **Node.js** (v16.0.0 or higher)
- **MongoDB** (v4.4 or higher)
- **npm** or **yarn** package manager

### Optional (Recommended)
- **Redis** (for caching and sessions)
- **PM2** (for production deployment)
- **MongoDB Compass** (database GUI)

## 📦 Installation Steps

### 1. Clone and Setup Backend

```bash
# Navigate to your project directory
cd /path/to/your/project

# The backend folder should already exist with all files
cd backend

# Install dependencies
npm install

# Create environment file
cp .env.example .env
```

### 2. Configure Environment Variables

Edit the `.env` file with your specific configuration:

```bash
# Essential Configuration
NODE_ENV=development
PORT=5000
MONGODB_URI=mongodb://localhost:27017/ganpati_tracker
JWT_SECRET=your_super_secret_jwt_key_here_make_it_long_and_complex
FRONTEND_URL=http://localhost:3000

# Email Configuration (for notifications)
EMAIL_SERVICE=gmail
EMAIL_HOST=smtp.gmail.com
EMAIL_PORT=587
EMAIL_USER=your_email@gmail.com
EMAIL_PASS=your_app_password
EMAIL_FROM=Ganpati Tracker <noreply@ganpatitracker.com>
```

### 3. Database Setup

```bash
# Start MongoDB service
# On macOS with Homebrew:
brew services start mongodb-community

# On Ubuntu:
sudo systemctl start mongod

# On Windows:
net start MongoDB

# Seed the database with sample data
npm run seed
```

### 4. Start the Server

```bash
# Development mode (with auto-restart)
npm run dev

# Production mode
npm start
```

The server will start on `http://localhost:5000`

## 🗄️ Database Setup

### MongoDB Collections

The application creates the following collections:

1. **users** - User accounts and authentication
2. **donations** - Donation records
3. **expenses** - Expense records

### Sample Data

The seeding script creates:
- 4 sample users (admin, moderator, volunteer, viewer)
- 5 sample donations
- 5 sample expenses

### Login Credentials (After Seeding)

```
Admin: admin@ganpatitracker.com / admin123
Moderator: rajesh@example.com / password123
Volunteer: priya@example.com / password123
Viewer: amit@example.com / password123
```

## 🔗 Frontend Integration

### 1. Update Frontend API Configuration

Create or update your frontend API configuration:

```javascript
// In your frontend project (e.g., js/config.js)
const API_CONFIG = {
  BASE_URL: 'http://localhost:5000/api',
  ENDPOINTS: {
    // Authentication
    LOGIN: '/auth/login',
    REGISTER: '/auth/register',
    REFRESH: '/auth/refresh',
    PROFILE: '/auth/me',
    
    // Donations
    DONATIONS: '/donations',
    DONATION_STATS: '/donations/stats',
    
    // Expenses
    EXPENSES: '/expenses',
    EXPENSE_STATS: '/expenses/stats',
    
    // Analytics
    ANALYTICS: '/analytics',
    
    // Receipts
    RECEIPTS: '/receipts'
  }
};
```

### 2. Update Frontend JavaScript

Replace your existing localStorage-based data management with API calls:

```javascript
// Example: Update donation form submission
async function handleDonationSubmit(formData) {
  try {
    const response = await fetch(`${API_CONFIG.BASE_URL}${API_CONFIG.ENDPOINTS.DONATIONS}`, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${localStorage.getItem('authToken')}`
      },
      body: JSON.stringify(formData)
    });

    const result = await response.json();
    
    if (result.success) {
      showMessage('Donation recorded successfully!', 'success');
      refreshDonationsTable();
      updateAnalytics();
    } else {
      showMessage(result.message, 'error');
    }
  } catch (error) {
    showMessage('Error submitting donation', 'error');
    console.error('Error:', error);
  }
}

// Example: Fetch donations
async function fetchDonations(page = 1, limit = 10) {
  try {
    const response = await fetch(
      `${API_CONFIG.BASE_URL}${API_CONFIG.ENDPOINTS.DONATIONS}?page=${page}&limit=${limit}`
    );
    
    const result = await response.json();
    
    if (result.success) {
      return result.data;
    } else {
      throw new Error(result.message);
    }
  } catch (error) {
    console.error('Error fetching donations:', error);
    return [];
  }
}
```

### 3. Add Authentication

```javascript
// Authentication helper functions
class AuthManager {
  static setTokens(authToken, refreshToken) {
    localStorage.setItem('authToken', authToken);
    localStorage.setItem('refreshToken', refreshToken);
  }

  static getAuthToken() {
    return localStorage.getItem('authToken');
  }

  static clearTokens() {
    localStorage.removeItem('authToken');
    localStorage.removeItem('refreshToken');
  }

  static async login(email, password) {
    try {
      const response = await fetch(`${API_CONFIG.BASE_URL}${API_CONFIG.ENDPOINTS.LOGIN}`, {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ email, password })
      });

      const result = await response.json();
      
      if (result.success) {
        this.setTokens(result.data.tokens.authToken, result.data.tokens.refreshToken);
        return result.data.user;
      } else {
        throw new Error(result.message);
      }
    } catch (error) {
      throw error;
    }
  }

  static async logout() {
    try {
      await fetch(`${API_CONFIG.BASE_URL}/auth/logout`, {
        method: 'POST',
        headers: {
          'Authorization': `Bearer ${this.getAuthToken()}`
        }
      });
    } catch (error) {
      console.error('Logout error:', error);
    } finally {
      this.clearTokens();
    }
  }
}
```

## 📚 API Documentation

### Authentication Endpoints

#### POST /api/auth/register
Register a new user
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "password123",
  "phone": "+91 98765 43210",
  "role": "viewer"
}
```

#### POST /api/auth/login
Login user
```json
{
  "email": "john@example.com",
  "password": "password123"
}
```

### Donation Endpoints

#### GET /api/donations
Get all donations with filtering
```
Query Parameters:
- page: Page number (default: 1)
- limit: Items per page (default: 10)
- wing: Filter by wing (A, B, C, D, E)
- paymentMode: Filter by payment mode
- startDate: Filter from date
- endDate: Filter to date
- search: Search in name, ID, note
```

#### POST /api/donations
Create new donation
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

#### GET /api/donations/stats
Get donation statistics
```json
{
  "success": true,
  "data": {
    "total": { "total": 50000, "count": 25 },
    "byWing": [...],
    "monthlyTrends": [...],
    "topDonors": [...]
  }
}
```

### Expense Endpoints

#### GET /api/expenses
Get all expenses (requires authentication)

#### POST /api/expenses
Create new expense (requires authentication)
```json
{
  "item": "Decoration Materials",
  "cost": 1500,
  "date": "2024-01-05",
  "reason": "Flowers and lights for mandap",
  "category": "Decoration",
  "vendorName": "Flower Market",
  "paymentMethod": "Cash"
}
```

#### PUT /api/expenses/:id/approve
Approve expense (requires approve_expense permission)

#### PUT /api/expenses/:id/reject
Reject expense (requires approve_expense permission)
```json
{
  "reason": "Insufficient documentation"
}
```

## 🧪 Testing

### Manual Testing

1. **Test Authentication**
```bash
# Register new user
curl -X POST http://localhost:5000/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Test User","email":"test@example.com","password":"password123"}'

# Login
curl -X POST http://localhost:5000/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"password123"}'
```

2. **Test Donations**
```bash
# Get donations
curl http://localhost:5000/api/donations

# Create donation
curl -X POST http://localhost:5000/api/donations \
  -H "Content-Type: application/json" \
  -d '{"name":"Test Donor","wing":"A","floor":1,"flat":"101","amount":500,"paymentMode":"Cash","date":"2024-01-01"}'
```

### Health Check

Visit `http://localhost:5000/health` to verify server status.

## 🚀 Deployment

### Production Environment

1. **Environment Setup**
```bash
# Set production environment
NODE_ENV=production

# Use production MongoDB URI
MONGODB_URI=mongodb://your-production-server:27017/ganpati_tracker

# Set secure JWT secrets
JWT_SECRET=your_very_secure_production_jwt_secret
```

2. **Using PM2**
```bash
# Install PM2 globally
npm install -g pm2

# Start application with PM2
pm2 start server.js --name "ganpati-tracker-api"

# Save PM2 configuration
pm2 save

# Setup PM2 startup
pm2 startup
```

3. **Nginx Configuration**
```nginx
server {
    listen 80;
    server_name your-domain.com;

    location /api {
        proxy_pass http://localhost:5000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }

    location / {
        root /path/to/your/frontend;
        try_files $uri $uri/ /index.html;
    }
}
```

### Docker Deployment

Create `Dockerfile`:
```dockerfile
FROM node:16-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 5000

CMD ["npm", "start"]
```

Create `docker-compose.yml`:
```yaml
version: '3.8'
services:
  api:
    build: .
    ports:
      - "5000:5000"
    environment:
      - NODE_ENV=production
      - MONGODB_URI=mongodb://mongo:27017/ganpati_tracker
    depends_on:
      - mongo

  mongo:
    image: mongo:4.4
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
```

## 🔧 Troubleshooting

### Common Issues

1. **MongoDB Connection Error**
```
Error: connect ECONNREFUSED 127.0.0.1:27017
```
**Solution**: Ensure MongoDB is running
```bash
# Check MongoDB status
brew services list | grep mongodb  # macOS
sudo systemctl status mongod       # Ubuntu
```

2. **JWT Secret Error**
```
Error: JWT secret is not defined
```
**Solution**: Set JWT_SECRET in .env file

3. **CORS Error**
```
Access to fetch blocked by CORS policy
```
**Solution**: Update FRONTEND_URL in .env file

4. **Port Already in Use**
```
Error: listen EADDRINUSE :::5000
```
**Solution**: Change PORT in .env or kill existing process
```bash
# Find process using port 5000
lsof -i :5000

# Kill process
kill -9 <PID>
```

### Debug Mode

Enable debug logging:
```bash
DEBUG_MODE=true npm run dev
```

### Log Files

Check log files for errors:
```bash
# View error logs
tail -f backend/logs/error.log

# View combined logs
tail -f backend/logs/combined.log
```

## 📈 Performance Optimization

### Database Indexing

The models include optimized indexes for:
- Donation queries by wing, floor, date
- Expense queries by category, status, date
- User queries by email, role

### Caching (Optional)

To enable Redis caching:
```bash
# Install Redis
brew install redis  # macOS
sudo apt install redis-server  # Ubuntu

# Start Redis
redis-server

# Update .env
REDIS_URL=redis://localhost:6379
```

### Rate Limiting

The API includes rate limiting:
- 100 requests per 15 minutes per IP
- Configurable in server.js

## 🔒 Security Features

- **JWT Authentication** with refresh tokens
- **Password Hashing** using bcrypt
- **Input Validation** using Joi schemas
- **Rate Limiting** to prevent abuse
- **CORS Protection** for cross-origin requests
- **Helmet.js** for security headers
- **Request Logging** for audit trails

## 📞 Support

For technical support or questions:

1. Check the logs in `backend/logs/`
2. Verify environment configuration
3. Test API endpoints using curl or Postman
4. Check MongoDB connection and data

## 🎉 Success!

If everything is set up correctly, you should see:

```
🚀 Ganpati Tracker API Server running on port 5000
📊 Environment: development
🔗 Health check: http://localhost:5000/health
```

Your backend is now ready to serve your Ganpati Tracker frontend!

**Ganpati Bappa Morya! 🙏**