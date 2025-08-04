# User Management System Guide

## Overview

The Ganpati Tracker User Management System provides comprehensive role-based access control, user activity tracking, and profile management capabilities. This system ensures secure access to different features based on user roles and maintains detailed audit trails of user activities.

## Features

### 🔐 Authentication & Authorization
- **JWT-based Authentication**: Secure token-based authentication system
- **Role-based Access Control**: Four distinct user roles with specific permissions
- **Session Management**: Automatic token validation and refresh
- **Password Security**: Encrypted password storage with bcrypt

### 👥 User Roles & Permissions

#### Administrator
- **Full System Access**: Complete control over all system features
- **User Management**: Create, edit, delete, and manage all users
- **Role Assignment**: Change user roles and permissions
- **System Settings**: Access to system configuration and settings
- **Analytics Access**: Full access to all analytics and reports
- **Expense Approval**: Approve or reject expense requests

#### Moderator
- **Data Management**: Manage donations and expenses
- **Expense Approval**: Approve or reject expense requests
- **Analytics Access**: View detailed analytics and reports
- **Receipt Generation**: Generate and download receipts
- **Limited User View**: View user information (no management)

#### Volunteer
- **Data Entry**: Add new donations and expenses
- **Basic Analytics**: View basic financial summaries
- **Profile Management**: Manage own profile and settings
- **Receipt Download**: Download receipts for donations

#### Viewer
- **Read-only Access**: View donations and basic analytics
- **Profile Management**: Manage own profile and settings
- **Limited Features**: Cannot add, edit, or delete data

### 📊 User Activity Tracking
- **Login/Logout Tracking**: Monitor user session activities
- **Action Logging**: Track all user actions (donations, expenses, approvals)
- **Activity Timeline**: Visual timeline of recent user activities
- **Performance Metrics**: User engagement and activity statistics

### 👤 Profile Management
- **Personal Information**: Update name, email, phone number
- **Password Management**: Secure password change functionality
- **Activity Summary**: Personal activity statistics and history
- **Account Settings**: Manage account preferences

## User Interface Components

### 🏠 Navigation Bar
- **User Info Display**: Shows current user name and role
- **Role-based Menu**: Dynamic navigation based on user permissions
- **Logout Functionality**: Secure session termination

### 📋 User Management Dashboard
- **User Statistics**: Overview of total users, active users, recent registrations
- **User Table**: Comprehensive list with filtering and search capabilities
- **Action Buttons**: Quick access to user management functions

### 🔍 Advanced Filtering
- **Role Filter**: Filter users by role (Admin, Moderator, Volunteer, Viewer)
- **Status Filter**: Filter by user status (Active, Inactive, Suspended)
- **Search Function**: Search by name, email, or phone number
- **Pagination**: Efficient handling of large user lists

### 📈 Activity Monitoring
- **Real-time Activity**: Live feed of user activities
- **Activity Statistics**: Quantified metrics of user engagement
- **Time-based Filtering**: Filter activities by time periods
- **User-specific Tracking**: View activities for specific users

## API Endpoints

### Authentication Endpoints
```
POST /api/auth/login          - User login
POST /api/auth/logout         - User logout
GET  /api/auth/me            - Get current user info
POST /api/auth/refresh       - Refresh authentication token
```

### User Management Endpoints
```
GET    /api/users            - Get all users (with filtering)
POST   /api/users            - Create new user
GET    /api/users/:id        - Get user by ID
PUT    /api/users/:id        - Update user
DELETE /api/users/:id        - Delete user (soft delete)
```

### User Statistics Endpoints
```
GET /api/users/stats         - Get user statistics
GET /api/users/activity      - Get user activity timeline
GET /api/users/my-stats      - Get current user's statistics
```

### User Actions Endpoints
```
PATCH /api/users/:id/toggle-status    - Toggle user status
POST  /api/users/:id/reset-password   - Reset user password
PUT   /api/users/profile              - Update own profile
POST  /api/users/change-password      - Change own password
```

## Security Features

### 🔒 Password Security
- **Encryption**: All passwords encrypted using bcrypt
- **Strength Requirements**: Minimum 6 characters
- **Reset Functionality**: Admin-controlled password reset
- **Change Validation**: Current password verification required

### 🛡️ Access Control
- **Permission Middleware**: Route-level permission checking
- **Role Validation**: Automatic role-based access control
- **Token Verification**: JWT token validation on all protected routes
- **Session Management**: Automatic session timeout and refresh

### 📝 Audit Trail
- **Action Logging**: All user actions logged with timestamps
- **User Tracking**: Complete audit trail of user activities
- **Admin Oversight**: Administrators can view all user activities
- **Data Integrity**: Immutable activity logs for compliance

## Installation & Setup

### Prerequisites
- Node.js (v14 or higher)
- MongoDB (v4.4 or higher)
- Modern web browser with JavaScript enabled

### Backend Setup
1. **Install Dependencies**
   ```bash
   cd backend
   npm install
   ```

2. **Environment Configuration**
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```

3. **Database Setup**
   ```bash
   # Ensure MongoDB is running
   npm run seed  # Optional: Seed with demo data
   ```

4. **Start Server**
   ```bash
   npm start
   ```

### Frontend Setup
1. **Open in Browser**
   - Navigate to the project directory
   - Open `index.html` in a web browser
   - Or use a local server (recommended)

2. **Demo Accounts**
   - **Admin**: admin@ganpati.com / admin123
   - **Moderator**: moderator@ganpati.com / mod123
   - **Volunteer**: volunteer@ganpati.com / vol123
   - **Viewer**: viewer@ganpati.com / view123

## Usage Instructions

### 🚀 Getting Started
1. **Login**: Use the login page with demo credentials or create new account
2. **Dashboard**: Navigate to the main dashboard to see overview
3. **User Management**: Access Users page (Admin/Moderator only)
4. **Profile**: Manage your profile from the Users tab

### 👨‍💼 Admin Tasks
1. **Create User**: Click "Add User" button and fill in details
2. **Manage Roles**: Edit user roles from the user table
3. **Monitor Activity**: View user activities in the Activity tab
4. **Generate Reports**: Export user data and activity reports

### 👤 User Tasks
1. **Update Profile**: Go to "My Profile" tab to update information
2. **Change Password**: Use "Change Password" option for security
3. **View Activity**: Check your personal activity summary
4. **Manage Settings**: Update account preferences

## Troubleshooting

### Common Issues

#### Login Problems
- **Invalid Credentials**: Check email and password
- **Account Suspended**: Contact administrator
- **Token Expired**: Refresh page and login again

#### Permission Errors
- **Access Denied**: Check user role and permissions
- **Feature Unavailable**: Role may not have required permissions
- **Page Not Loading**: Ensure proper authentication

#### Data Issues
- **Users Not Loading**: Check backend server status
- **Statistics Incorrect**: Refresh page or check data integrity
- **Export Failing**: Verify user permissions and data access

### Error Messages
- **"Authentication required"**: Login session expired
- **"Access denied"**: Insufficient permissions for action
- **"User not found"**: User may have been deleted or deactivated
- **"Invalid input"**: Check form data and validation requirements

## Best Practices

### 🔐 Security
- **Regular Password Updates**: Encourage users to change passwords regularly
- **Role Review**: Periodically review user roles and permissions
- **Activity Monitoring**: Monitor user activities for suspicious behavior
- **Access Audits**: Regular audits of user access and permissions

### 📊 Data Management
- **Regular Backups**: Backup user data and activity logs
- **Data Cleanup**: Remove inactive users and old activity logs
- **Performance Monitoring**: Monitor system performance with user load
- **Update Management**: Keep system updated with security patches

### 👥 User Experience
- **Clear Instructions**: Provide clear guidance for new users
- **Training Materials**: Create training materials for different roles
- **Support Documentation**: Maintain updated help documentation
- **Feedback Collection**: Collect user feedback for improvements

## Advanced Configuration

### Role Customization
```javascript
// Custom role permissions can be configured in:
// backend/middleware/auth.js

const rolePermissions = {
  admin: ['manage_users', 'approve_expenses', 'view_analytics', 'system_settings'],
  moderator: ['approve_expenses', 'view_analytics', 'manage_data'],
  volunteer: ['add_data', 'view_basic_analytics'],
  viewer: ['view_data']
};
```

### Activity Tracking
```javascript
// Custom activity tracking can be added in:
// backend/utils/activityLogger.js

function logActivity(userId, action, details) {
  // Custom activity logging logic
}
```

## Support & Maintenance

### Regular Maintenance
- **Database Optimization**: Regular database maintenance and optimization
- **Log Rotation**: Implement log rotation for activity logs
- **Performance Monitoring**: Monitor system performance metrics
- **Security Updates**: Apply security updates promptly

### Support Contacts
- **Technical Support**: Contact system administrator
- **User Training**: Request training sessions for new features
- **Bug Reports**: Report issues through proper channels
- **Feature Requests**: Submit enhancement requests

## Conclusion

The User Management System provides a robust, secure, and user-friendly solution for managing users in the Ganpati Tracker application. With comprehensive role-based access control, detailed activity tracking, and intuitive user interfaces, it ensures both security and usability for all stakeholders.

For additional support or questions, please refer to the API documentation or contact the system administrator.

---

**Last Updated**: January 2024  
**Version**: 1.0.0  
**Author**: Ganpati Tracker Development Team