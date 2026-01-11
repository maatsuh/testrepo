# EduIA Platform Documentation

## Table of Contents
1. [Overview](#overview)
2. [Installation](#installation)
3. [SSL Configuration](#ssl-configuration)
4. [API Routes](#api-routes)
5. [Security](#security)
6. [Database](#database)
7. [Deployment](#deployment)
8. [Troubleshooting](#troubleshooting)

---

## Overview

EduIA is a comprehensive educational platform powered by artificial intelligence. It provides integrated tools for learning management, interactive content delivery, and personalized student assessments. The platform is built with scalability, security, and user experience as core principles.

**Key Features:**
- AI-powered learning personalization
- Real-time collaboration tools
- Comprehensive student analytics
- Multi-language support
- Secure user authentication and authorization
- RESTful API for third-party integration

---

## Installation

### Prerequisites

Before installing EduIA, ensure you have the following:

- **Node.js** (v16.0.0 or higher)
- **Python** (v3.8 or higher)
- **PostgreSQL** (v12 or higher)
- **Redis** (v6.0 or higher)
- **npm** or **yarn** package manager
- **Git** version control system
- **OpenSSL** for SSL certificate generation

### Step-by-Step Installation

#### 1. Clone the Repository

```bash
git clone https://github.com/maatsuh/testrepo.git
cd testrepo
```

#### 2. Install Node.js Dependencies

```bash
npm install
# or
yarn install
```

#### 3. Install Python Dependencies

Create a virtual environment and install Python packages:

```bash
python3 -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements.txt
```

#### 4. Set Up Environment Variables

Create a `.env` file in the root directory with the following variables:

```env
# Application
NODE_ENV=development
APP_NAME=EduIA
APP_PORT=3000
APP_HOST=localhost

# Database
DATABASE_URL=postgresql://user:password@localhost:5432/eduia
DATABASE_POOL_SIZE=20
DATABASE_TIMEOUT=5000

# Redis
REDIS_URL=redis://localhost:6379
REDIS_PASSWORD=your_redis_password

# JWT Authentication
JWT_SECRET=your_jwt_secret_key_here
JWT_EXPIRATION=24h
REFRESH_TOKEN_SECRET=your_refresh_token_secret

# SSL/TLS
SSL_ENABLED=true
SSL_CERT_PATH=./certs/cert.pem
SSL_KEY_PATH=./certs/key.pem

# AI Integration
AI_API_KEY=your_ai_api_key
AI_MODEL=gpt-4
AI_BASE_URL=https://api.openai.com/v1

# Email Service
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your_email@gmail.com
SMTP_PASSWORD=your_app_password
SMTP_FROM=noreply@eduia.com

# AWS S3 (Optional)
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_S3_BUCKET=eduia-storage
AWS_REGION=us-east-1

# Logging
LOG_LEVEL=info
LOG_FILE=./logs/app.log
```

#### 5. Initialize the Database

```bash
npm run db:init
npm run db:migrate
npm run db:seed
```

#### 6. Start the Application

```bash
# Development
npm run dev

# Production
npm run build
npm start
```

The application will be available at `https://localhost:3000` (or your configured port).

---

## SSL Configuration

### Generating Self-Signed Certificates (Development)

For development environments, generate self-signed SSL certificates:

```bash
mkdir -p certs
openssl req -x509 -newkey rsa:4096 -keyout certs/key.pem -out certs/cert.pem -days 365 -nodes
```

When prompted, enter your certificate details:
- **Country Name:** US
- **State:** Your State
- **Locality:** Your City
- **Organization:** Your Organization
- **Common Name:** localhost

### Using Let's Encrypt Certificates (Production)

For production, use Let's Encrypt for free SSL certificates:

#### 1. Install Certbot

```bash
sudo apt-get update
sudo apt-get install certbot python3-certbot-nginx
```

#### 2. Generate Certificate

```bash
sudo certbot certonly --standalone -d yourdomain.com -d www.yourdomain.com
```

#### 3. Configure Certificate Paths

Update your `.env` file:

```env
SSL_CERT_PATH=/etc/letsencrypt/live/yourdomain.com/fullchain.pem
SSL_KEY_PATH=/etc/letsencrypt/live/yourdomain.com/privkey.pem
```

#### 4. Auto-Renewal Setup

```bash
sudo certbot renew --dry-run
```

### SSL Configuration in Application

The application automatically loads SSL certificates if `SSL_ENABLED=true` and paths are configured:

```javascript
// Server setup will use:
// - cert: process.env.SSL_CERT_PATH
// - key: process.env.SSL_KEY_PATH
```

### Verify SSL Configuration

```bash
curl -k https://localhost:3000/health
openssl s_client -connect localhost:3000
```

---

## API Routes

### Authentication Endpoints

#### Register User
- **Route:** `POST /api/v1/auth/register`
- **Description:** Create a new user account
- **Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePassword123!",
  "firstName": "John",
  "lastName": "Doe",
  "role": "student"
}
```
- **Response:** 201 Created
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "firstName": "John",
    "lastName": "Doe",
    "role": "student",
    "createdAt": "2026-01-11T18:04:10Z"
  },
  "accessToken": "jwt_token",
  "refreshToken": "refresh_token"
}
```

#### Login
- **Route:** `POST /api/v1/auth/login`
- **Description:** Authenticate user and receive tokens
- **Request Body:**
```json
{
  "email": "user@example.com",
  "password": "SecurePassword123!"
}
```
- **Response:** 200 OK
```json
{
  "accessToken": "jwt_token",
  "refreshToken": "refresh_token",
  "user": { /* user object */ }
}
```

#### Refresh Token
- **Route:** `POST /api/v1/auth/refresh`
- **Description:** Get new access token using refresh token
- **Headers:** `Authorization: Bearer <refresh_token>`
- **Response:** 200 OK
```json
{
  "accessToken": "new_jwt_token"
}
```

#### Logout
- **Route:** `POST /api/v1/auth/logout`
- **Description:** Invalidate user session
- **Headers:** `Authorization: Bearer <access_token>`
- **Response:** 200 OK

---

### Course Endpoints

#### List Courses
- **Route:** `GET /api/v1/courses`
- **Description:** Retrieve paginated list of courses
- **Query Parameters:**
  - `page` (optional): Page number (default: 1)
  - `limit` (optional): Items per page (default: 10)
  - `search` (optional): Search term
  - `category` (optional): Filter by category
- **Response:** 200 OK
```json
{
  "data": [
    {
      "id": "uuid",
      "title": "Introduction to Python",
      "description": "Learn Python basics",
      "instructor": "John Doe",
      "level": "beginner",
      "duration": "4 weeks",
      "students": 150,
      "rating": 4.8,
      "createdAt": "2026-01-11T18:04:10Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 10,
    "total": 45,
    "totalPages": 5
  }
}
```

#### Get Course Details
- **Route:** `GET /api/v1/courses/:courseId`
- **Description:** Retrieve detailed information about a specific course
- **Response:** 200 OK
```json
{
  "id": "uuid",
  "title": "Introduction to Python",
  "description": "Comprehensive Python course",
  "instructor": { /* instructor details */ },
  "modules": [ /* module list */ ],
  "lessons": [ /* lesson list */ ],
  "prerequisites": [],
  "targetAudience": "Beginners",
  "rating": 4.8,
  "reviews": 245
}
```

#### Create Course (Instructor only)
- **Route:** `POST /api/v1/courses`
- **Description:** Create a new course
- **Headers:** `Authorization: Bearer <access_token>`
- **Request Body:**
```json
{
  "title": "Advanced Python",
  "description": "Master Python programming",
  "category": "Programming",
  "level": "advanced",
  "prerequisites": ["Intro to Python"],
  "targetAudience": "Intermediate Programmers"
}
```
- **Response:** 201 Created

#### Update Course
- **Route:** `PUT /api/v1/courses/:courseId`
- **Description:** Update course details
- **Headers:** `Authorization: Bearer <access_token>`
- **Response:** 200 OK

#### Delete Course
- **Route:** `DELETE /api/v1/courses/:courseId`
- **Description:** Delete a course (Admin/Instructor only)
- **Headers:** `Authorization: Bearer <access_token>`
- **Response:** 204 No Content

---

### Student Enrollment Endpoints

#### Enroll in Course
- **Route:** `POST /api/v1/courses/:courseId/enroll`
- **Description:** Enroll current user in a course
- **Headers:** `Authorization: Bearer <access_token>`
- **Response:** 201 Created
```json
{
  "enrollmentId": "uuid",
  "userId": "uuid",
  "courseId": "uuid",
  "enrolledAt": "2026-01-11T18:04:10Z",
  "progress": 0,
  "status": "active"
}
```

#### Get User Enrollments
- **Route:** `GET /api/v1/users/me/enrollments`
- **Description:** Get all courses enrolled by current user
- **Headers:** `Authorization: Bearer <access_token>`
- **Response:** 200 OK
```json
{
  "enrollments": [
    {
      "courseId": "uuid",
      "courseName": "Python Basics",
      "progress": 35,
      "status": "active",
      "completedLessons": 12,
      "totalLessons": 35
    }
  ]
}
```

#### Get Course Progress
- **Route:** `GET /api/v1/courses/:courseId/progress`
- **Description:** Get detailed progress for a specific course
- **Headers:** `Authorization: Bearer <access_token>`
- **Response:** 200 OK
```json
{
  "courseId": "uuid",
  "progress": 45,
  "completedModules": 3,
  "totalModules": 7,
  "lastAccessed": "2026-01-11T18:04:10Z",
  "estimatedTimeRemaining": "12 hours"
}
```

---

### Assignment & Quiz Endpoints

#### Submit Assignment
- **Route:** `POST /api/v1/assignments/:assignmentId/submit`
- **Description:** Submit assignment for a course
- **Headers:** `Authorization: Bearer <access_token>`
- **Request Body:**
```json
{
  "content": "Assignment solution",
  "attachments": ["file_ids"]
}
```
- **Response:** 201 Created
```json
{
  "submissionId": "uuid",
  "assignmentId": "uuid",
  "submittedAt": "2026-01-11T18:04:10Z",
  "status": "submitted"
}
```

#### Get Quiz Questions
- **Route:** `GET /api/v1/quizzes/:quizId/questions`
- **Description:** Retrieve quiz questions
- **Headers:** `Authorization: Bearer <access_token>`
- **Response:** 200 OK
```json
{
  "quizId": "uuid",
  "title": "Python Fundamentals Quiz",
  "timeLimit": 30,
  "questions": [
    {
      "id": "uuid",
      "type": "multiple-choice",
      "question": "What is Python?",
      "options": ["A", "B", "C", "D"],
      "points": 1
    }
  ]
}
```

#### Submit Quiz
- **Route:** `POST /api/v1/quizzes/:quizId/submit`
- **Description:** Submit quiz answers
- **Headers:** `Authorization: Bearer <access_token>`
- **Request Body:**
```json
{
  "answers": [
    {
      "questionId": "uuid",
      "answer": "A"
    }
  ]
}
```
- **Response:** 201 Created
```json
{
  "score": 85,
  "totalPoints": 100,
  "percentage": 85,
  "feedback": "Great job!",
  "submittedAt": "2026-01-11T18:04:10Z"
}
```

---

### Analytics Endpoints

#### Get User Analytics
- **Route:** `GET /api/v1/analytics/user`
- **Description:** Get analytics for current user
- **Headers:** `Authorization: Bearer <access_token>`
- **Response:** 200 OK
```json
{
  "totalCoursesEnrolled": 5,
  "totalCoursesCompleted": 2,
  "averageScore": 87.5,
  "totalLearningHours": 120,
  "recentActivity": [
    {
      "timestamp": "2026-01-11T18:04:10Z",
      "type": "quiz_completed",
      "courseName": "Python Basics",
      "details": "Scored 95%"
    }
  ]
}
```

#### Get Course Analytics (Instructor)
- **Route:** `GET /api/v1/courses/:courseId/analytics`
- **Description:** Get enrollment and performance analytics for a course
- **Headers:** `Authorization: Bearer <access_token>`
- **Response:** 200 OK
```json
{
  "courseId": "uuid",
  "totalEnrollments": 250,
  "activeStudents": 180,
  "completionRate": 65,
  "averageScore": 78.4,
  "studentPerformance": [
    {
      "rank": 1,
      "studentName": "John Smith",
      "score": 98,
      "progress": 100,
      "completionDate": "2026-01-10T10:30:00Z"
    }
  ]
}
```

---

### User Management Endpoints

#### Get Current User
- **Route:** `GET /api/v1/users/me`
- **Description:** Get current authenticated user's information
- **Headers:** `Authorization: Bearer <access_token>`
- **Response:** 200 OK
```json
{
  "id": "uuid",
  "email": "user@example.com",
  "firstName": "John",
  "lastName": "Doe",
  "role": "student",
  "profilePicture": "url",
  "bio": "Passionate learner",
  "preferences": {
    "emailNotifications": true,
    "language": "en",
    "theme": "dark"
  },
  "createdAt": "2026-01-01T00:00:00Z"
}
```

#### Update User Profile
- **Route:** `PUT /api/v1/users/me`
- **Description:** Update current user's profile
- **Headers:** `Authorization: Bearer <access_token>`
- **Request Body:**
```json
{
  "firstName": "John",
  "lastName": "Doe",
  "bio": "Updated bio",
  "preferences": {
    "emailNotifications": false,
    "language": "es",
    "theme": "light"
  }
}
```
- **Response:** 200 OK

#### Change Password
- **Route:** `POST /api/v1/users/me/change-password`
- **Description:** Change user password
- **Headers:** `Authorization: Bearer <access_token>`
- **Request Body:**
```json
{
  "currentPassword": "OldPassword123!",
  "newPassword": "NewPassword456!",
  "confirmPassword": "NewPassword456!"
}
```
- **Response:** 200 OK

#### List Users (Admin only)
- **Route:** `GET /api/v1/admin/users`
- **Description:** Get all users with pagination
- **Headers:** `Authorization: Bearer <access_token>`
- **Query Parameters:**
  - `page`: Page number
  - `limit`: Items per page
  - `role`: Filter by role
  - `status`: Filter by status
- **Response:** 200 OK

---

### Health & Status Endpoints

#### Health Check
- **Route:** `GET /health`
- **Description:** Check API health status
- **Response:** 200 OK
```json
{
  "status": "healthy",
  "timestamp": "2026-01-11T18:04:10Z",
  "version": "1.0.0",
  "uptime": 3600
}
```

#### Detailed Health Check
- **Route:** `GET /api/v1/health/detailed`
- **Description:** Get detailed system health information
- **Response:** 200 OK
```json
{
  "status": "healthy",
  "components": {
    "database": "connected",
    "redis": "connected",
    "email": "operational",
    "ai_service": "operational"
  },
  "timestamp": "2026-01-11T18:04:10Z"
}
```

---

## Security

### Authentication & Authorization

#### JWT Token Structure
The platform uses JSON Web Tokens (JWT) for stateless authentication:

```
Header: {
  "alg": "HS256",
  "typ": "JWT"
}

Payload: {
  "sub": "user_id",
  "email": "user@example.com",
  "role": "student",
  "iat": 1234567890,
  "exp": 1234571490
}

Signature: HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload), secret)
```

#### Token Management
- **Access Token:** Expires in 24 hours (configurable)
- **Refresh Token:** Expires in 7 days
- Tokens are blacklisted on logout
- Automatic token rotation on refresh

#### User Roles & Permissions
```
Admin:
  - Manage all users
  - View system analytics
  - Configure system settings
  - Manage courses (all)
  - View all submissions

Instructor:
  - Create and manage own courses
  - Grade assignments
  - View course analytics
  - Manage course materials

Student:
  - Enroll in courses
  - Submit assignments
  - Take quizzes
  - View own analytics
  - Access course materials
```

### Password Security

#### Requirements
- Minimum 12 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character (!@#$%^&*)
- Not contain username or email

#### Hashing
- Algorithm: bcrypt
- Salt rounds: 12
- Passwords are hashed before storage
- Password history tracked (last 5 passwords)

#### Password Reset Flow
```
1. User requests password reset with email
2. System generates secure reset token (expires in 1 hour)
3. Reset link sent to email with token
4. User clicks link and creates new password
5. Token is invalidated after use
6. User notified of password change
```

### Data Protection

#### Encryption
- **In Transit:** TLS 1.3 for all HTTPS connections
- **At Rest:** AES-256 encryption for sensitive data fields:
  - User passwords (bcrypt)
  - SSN/ID numbers
  - Payment information
  - API keys

#### Data Validation
- Input sanitization on all endpoints
- CSRF protection tokens
- Rate limiting: 100 requests per minute per IP
- Request size limits: 10MB max

### Compliance & Audit

#### Audit Logging
All sensitive operations are logged:
```json
{
  "timestamp": "2026-01-11T18:04:10Z",
  "userId": "uuid",
  "action": "login",
  "resource": "user",
  "resourceId": "uuid",
  "ipAddress": "192.168.1.1",
  "userAgent": "Mozilla/5.0...",
  "status": "success",
  "details": {}
}
```

#### GDPR Compliance
- User data export functionality
- Right to be forgotten implementation
- Consent management
- Data retention policies
- Privacy policy enforcement

#### Security Headers
```
Strict-Transport-Security: max-age=31536000; includeSubDomains
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Content-Security-Policy: default-src 'self'
Referrer-Policy: strict-origin-when-cross-origin
```

### API Security

#### Rate Limiting
```
Global: 1000 requests per minute
Authentication: 5 attempts per 15 minutes
File Upload: 10 files per minute
```

#### CORS Configuration
```javascript
{
  origin: process.env.ALLOWED_ORIGINS.split(','),
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  maxAge: 3600
}
```

#### API Key Management
- Generate unique API keys for integrations
- Keys rotated quarterly
- Permissions scoped per key
- Keys logged on each use

---

## Database

### Architecture

#### Schema Overview
The database uses PostgreSQL with the following main tables:

```
users
├── id (UUID, PK)
├── email (VARCHAR, UNIQUE)
├── password (VARCHAR)
├── firstName (VARCHAR)
├── lastName (VARCHAR)
├── role (ENUM: admin, instructor, student)
├── profilePicture (VARCHAR)
├── bio (TEXT)
├── isActive (BOOLEAN)
├── emailVerified (BOOLEAN)
├── createdAt (TIMESTAMP)
├── updatedAt (TIMESTAMP)

courses
├── id (UUID, PK)
├── title (VARCHAR)
├── description (TEXT)
├── instructorId (UUID, FK)
├── category (VARCHAR)
├── level (ENUM: beginner, intermediate, advanced)
├── duration (VARCHAR)
├── prerequisites (JSONB)
├── targetAudience (TEXT)
├── isPublished (BOOLEAN)
├── createdAt (TIMESTAMP)
├── updatedAt (TIMESTAMP)

enrollments
├── id (UUID, PK)
├── userId (UUID, FK)
├── courseId (UUID, FK)
├── enrolledAt (TIMESTAMP)
├── completedAt (TIMESTAMP, NULLABLE)
├── progress (DECIMAL)
├── status (ENUM: active, completed, dropped)
├── lastAccessed (TIMESTAMP)

modules
├── id (UUID, PK)
├── courseId (UUID, FK)
├── title (VARCHAR)
├── order (INTEGER)
├── createdAt (TIMESTAMP)

lessons
├── id (UUID, PK)
├── moduleId (UUID, FK)
├── title (VARCHAR)
├── content (TEXT)
├── videoUrl (VARCHAR, NULLABLE)
├── duration (INTEGER)
├── order (INTEGER)
├── createdAt (TIMESTAMP)

assignments
├── id (UUID, PK)
├── courseId (UUID, FK)
├── title (VARCHAR)
├── description (TEXT)
├── dueDate (TIMESTAMP)
├── maxScore (INTEGER)
├── createdAt (TIMESTAMP)

submissions
├── id (UUID, PK)
├── assignmentId (UUID, FK)
├── userId (UUID, FK)
├── content (TEXT)
├── submittedAt (TIMESTAMP)
├── grade (INTEGER, NULLABLE)
├── feedback (TEXT, NULLABLE)
├── gradedAt (TIMESTAMP, NULLABLE)

quizzes
├── id (UUID, PK)
├── courseId (UUID, FK)
├── title (VARCHAR)
├── description (TEXT)
├── timeLimit (INTEGER)
├── passingScore (INTEGER)
├── createdAt (TIMESTAMP)

quiz_questions
├── id (UUID, PK)
├── quizId (UUID, FK)
├── question (TEXT)
├── type (ENUM: multiple-choice, essay, true-false)
├── options (JSONB)
├── correctAnswer (VARCHAR)
├── points (INTEGER)
├── order (INTEGER)

quiz_answers
├── id (UUID, PK)
├── userId (UUID, FK)
├── quizId (UUID, FK)
├── questionId (UUID, FK)
├── answer (TEXT)
├── isCorrect (BOOLEAN)
├── submittedAt (TIMESTAMP)

audit_logs
├── id (UUID, PK)
├── userId (UUID, FK)
├── action (VARCHAR)
├── resource (VARCHAR)
├── resourceId (UUID)
├── ipAddress (VARCHAR)
├── userAgent (TEXT)
├── status (VARCHAR)
├── createdAt (TIMESTAMP)
```

### Indexes

For optimal performance, the following indexes are created:

```sql
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role ON users(role);
CREATE INDEX idx_enrollments_user_course ON enrollments(userId, courseId);
CREATE INDEX idx_enrollments_course_status ON enrollments(courseId, status);
CREATE INDEX idx_lessons_module_order ON lessons(moduleId, order);
CREATE INDEX idx_submissions_assignment_user ON submissions(assignmentId, userId);
CREATE INDEX idx_submissions_graded ON submissions(gradedAt) WHERE gradedAt IS NOT NULL;
CREATE INDEX idx_quiz_answers_user_quiz ON quiz_answers(userId, quizId);
CREATE INDEX idx_audit_logs_user_date ON audit_logs(userId, createdAt);
```

### Migrations

Database migrations are managed using a migration system. Run migrations with:

```bash
# Create a new migration
npm run db:migrate:create -- --name migration_description

# Run pending migrations
npm run db:migrate:up

# Rollback last migration
npm run db:migrate:down

# Show migration status
npm run db:migrate:status
```

### Backup & Recovery

#### Automated Backups
```bash
# Daily backup at 2 AM UTC
0 2 * * * /usr/local/bin/backup-db.sh

# Backup script location: scripts/backup-db.sh
# Retention: 30 days for daily, 12 months for monthly
```

#### Manual Backup
```bash
pg_dump -U postgres -h localhost eduia > backup-$(date +%Y%m%d).sql
```

#### Restore from Backup
```bash
psql -U postgres -h localhost eduia < backup-20260111.sql
```

#### Point-in-Time Recovery
```bash
# Enable WAL archiving in postgresql.conf
wal_level = replica
archive_mode = on
archive_command = 'cp %p /path/to/wal_archive/%f'

# Restore to specific time
pg_ctl start -o "-c recovery_target_time='2026-01-11 18:04:10'"
```

### Connection Pooling

```
Pool Configuration:
├── Max Connections: 20
├── Min Connections: 5
├── Idle Timeout: 30 seconds
├── Connection Timeout: 5 seconds
└── Max Lifetime: 30 minutes
```

### Query Optimization

#### Common Slow Queries and Fixes

```sql
-- Example: Enrollment with course details
SELECT e.*, c.title, c.description, i.firstName, i.lastName
FROM enrollments e
JOIN courses c ON e.courseId = c.id
JOIN users i ON c.instructorId = i.id
WHERE e.userId = $1
ORDER BY e.enrolledAt DESC;

-- Use EXPLAIN ANALYZE to check execution plan
EXPLAIN ANALYZE SELECT ...
```

---

## Deployment

### Prerequisites

- **Server OS:** Ubuntu 20.04 LTS or higher
- **Memory:** Minimum 4GB RAM
- **Storage:** Minimum 20GB SSD
- **Network:** Stable internet connection
- **Domain:** Valid domain name
- **SSL Certificate:** From Let's Encrypt or CA

### Production Environment Setup

#### 1. Server Preparation

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install required tools
sudo apt install -y curl wget git nodejs npm postgresql postgresql-contrib redis-server nginx certbot python3-certbot-nginx

# Create application user
sudo useradd -m -s /bin/bash eduia
sudo usermod -aG sudo eduia
```

#### 2. PostgreSQL Setup

```bash
# Start PostgreSQL service
sudo systemctl start postgresql
sudo systemctl enable postgresql

# Create database and user
sudo -u postgres psql << EOF
CREATE DATABASE eduia;
CREATE USER eduia_user WITH PASSWORD 'strong_password_here';
ALTER ROLE eduia_user SET client_encoding TO 'utf8';
ALTER ROLE eduia_user SET default_transaction_isolation TO 'read committed';
ALTER ROLE eduia_user SET default_transaction_deferrable TO on;
ALTER ROLE eduia_user SET default_transaction_level TO 'read committed';
ALTER ROLE eduia_user SET timezone TO 'UTC';
GRANT ALL PRIVILEGES ON DATABASE eduia TO eduia_user;
\q
EOF
```

#### 3. Redis Setup

```bash
# Configure Redis
sudo nano /etc/redis/redis.conf
# Set: maxmemory 2gb
# Set: maxmemory-policy allkeys-lru
# Enable: requirepass your_redis_password

# Restart Redis
sudo systemctl restart redis-server
sudo systemctl enable redis-server
```

#### 4. Application Deployment

```bash
# Switch to application user
su - eduia

# Clone repository
git clone https://github.com/maatsuh/testrepo.git
cd testrepo

# Install dependencies
npm ci --production
python3 -m pip install -r requirements.txt

# Set up environment
cp .env.production .env
# Edit .env with production values

# Build application
npm run build

# Initialize database
npm run db:migrate
```

#### 5. Nginx Configuration

Create `/etc/nginx/sites-available/eduia`:

```nginx
upstream eduia_backend {
    least_conn;
    server 127.0.0.1:3000 max_fails=3 fail_timeout=30s;
    keepalive 32;
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    location / {
        return 301 https://$server_name$request_uri;
    }
    location /.well-known/acme-challenge/ {
        root /var/www/certbot;
    }
}

# HTTPS Server
server {
    listen 443 ssl http2;
    server_name yourdomain.com www.yourdomain.com;

    # SSL Configuration
    ssl_certificate /etc/letsencrypt/live/yourdomain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/yourdomain.com/privkey.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    # Security Headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-Frame-Options "DENY" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "strict-origin-when-cross-origin" always;

    # Logging
    access_log /var/log/nginx/eduia_access.log combined;
    error_log /var/log/nginx/eduia_error.log warn;

    # Client upload size
    client_max_body_size 100M;

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1000;
    gzip_proxied any;
    gzip_types text/plain text/css text/xml text/javascript application/x-javascript application/xml+rss;

    # Proxy settings
    location / {
        proxy_pass http://eduia_backend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
        
        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
    }

    # Static files caching
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg|woff|woff2|ttf|eot)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

Enable the site:

```bash
sudo ln -s /etc/nginx/sites-available/eduia /etc/nginx/sites-enabled/
sudo systemctl restart nginx
```

#### 6. Process Management with PM2

Install and configure PM2:

```bash
npm install -g pm2

# Create ecosystem configuration file
cat > ecosystem.config.js << 'EOF'
module.exports = {
  apps: [{
    name: 'eduia-api',
    script: './dist/index.js',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production'
    },
    error_file: './logs/err.log',
    out_file: './logs/out.log',
    log_date_format: 'YYYY-MM-DD HH:mm:ss Z',
    max_restarts: 10,
    min_uptime: '10s',
    max_memory_restart: '1G'
  }]
};
EOF

# Start application
pm2 start ecosystem.config.js

# Save PM2 configuration
pm2 save

# Enable startup
pm2 startup
```

#### 7. SSL Certificate Setup

```bash
# Generate certificate using Certbot
sudo certbot certonly --nginx -d yourdomain.com -d www.yourdomain.com

# Auto-renewal setup
sudo systemctl enable certbot.timer
sudo systemctl start certbot.timer

# Test renewal
sudo certbot renew --dry-run
```

#### 8. Monitoring & Logging

Install and configure monitoring:

```bash
# Install Prometheus Node Exporter
sudo apt install -y prometheus-node-exporter

# Configure application logging
# Create logs directory
mkdir -p ~/testrepo/logs

# Configure log rotation
cat > /etc/logrotate.d/eduia << 'EOF'
/home/eduia/testrepo/logs/*.log {
    daily
    rotate 14
    compress
    delaycompress
    notifempty
    create 0640 eduia eduia
    sharedscripts
    postrotate
        pm2 reloadLogs
    endscript
}
EOF
```

#### 9. Firewall Configuration

```bash
sudo ufw enable
sudo ufw allow 22/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 3000/tcp  # Internal only if needed
```

#### 10. Health Check & Monitoring

```bash
# Add health check to systemd
sudo nano /etc/systemd/system/eduia-health-check.service

# Service content:
[Unit]
Description=EduIA Health Check
After=network.target

[Service]
Type=simple
User=eduia
ExecStart=/usr/bin/curl -f https://yourdomain.com/health || systemctl restart eduia
StandardOutput=journal

[Install]
WantedBy=multi-user.target
```

### Scaling Considerations

#### Horizontal Scaling
```yaml
Load Balancer: Nginx (upstream directive)
Instances: Multiple application servers
Database: Read replicas for reporting
Cache: Redis cluster for session management
Storage: S3 or NFS for file sharing
```

#### Vertical Scaling
```yaml
Memory: Increase to 8GB+ for high traffic
CPU: Use multi-core instances
Storage: SSD for better I/O performance
Network: Bandwidth provisioning
```

### Continuous Integration/Deployment

#### GitHub Actions CI/CD Pipeline

Create `.github/workflows/deploy.yml`:

```yaml
name: Deploy to Production

on:
  push:
    branches: [main]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v2
      
      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '16'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Run tests
        run: npm test
      
      - name: Build application
        run: npm run build
      
      - name: Deploy to server
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ${{ secrets.SERVER_USER }}
          key: ${{ secrets.SERVER_SSH_KEY }}
          script: |
            cd ~/testrepo
            git pull origin main
            npm ci --production
            npm run build
            npm run db:migrate
            pm2 restart ecosytem.config.js
```

---

## Troubleshooting

### Common Issues and Solutions

#### Issue: Application Won't Start

**Symptoms:**
- `Cannot find module` errors
- Port already in use
- Environment variables not found

**Solutions:**
```bash
# Check if dependencies are installed
npm list

# Reinstall dependencies
rm -rf node_modules package-lock.json
npm install

# Check if port is in use
lsof -i :3000
# Kill process if needed
kill -9 <PID>

# Verify environment variables
cat .env
source .env

# Check logs
npm run dev 2>&1 | tee debug.log
```

#### Issue: Database Connection Errors

**Symptoms:**
- `ECONNREFUSED` when connecting to database
- `Error: connect ETIMEDOUT`
- Authentication failures

**Solutions:**
```bash
# Check PostgreSQL service
sudo systemctl status postgresql

# Verify database exists
sudo -u postgres psql -l

# Test connection
psql -U eduia_user -h localhost -d eduia

# Check connection string format
# Should be: postgresql://user:password@host:port/database

# Reset database connection pool
npm run db:pool:reset

# Increase connection timeout
# In .env: DATABASE_TIMEOUT=10000
```

**Connection String Examples:**
```
# Local development
postgresql://postgres:password@localhost:5432/eduia

# Remote with password
postgresql://user:password@host.example.com:5432/eduia

# With SSL
postgresql://user:password@host.example.com:5432/eduia?sslmode=require

# Connection pool
postgresql://user:password@host:5432/eduia?pool_size=20&max_overflow=0
```

#### Issue: Redis Connection Issues

**Symptoms:**
- `ECONNREFUSED` Redis connection
- Session data not persisting
- Cache not working

**Solutions:**
```bash
# Check Redis service
sudo systemctl status redis-server

# Test Redis connection
redis-cli ping
# Expected output: PONG

# Check Redis configuration
redis-cli CONFIG GET "*"

# Clear Redis cache (if needed)
redis-cli FLUSHALL

# Check memory usage
redis-cli INFO memory

# Monitor Redis commands
redis-cli MONITOR
```

#### Issue: SSL/TLS Certificate Errors

**Symptoms:**
- `ERR_SSL_PROTOCOL_ERROR` in browser
- `unable to get local issuer certificate`
- Certificate validation failures

**Solutions:**
```bash
# Check certificate validity
openssl x509 -in /path/to/cert.pem -text -noout

# Verify certificate matches key
openssl x509 -noout -modulus -in cert.pem | openssl md5
openssl rsa -noout -modulus -in key.pem | openssl md5

# Test SSL configuration
openssl s_client -connect localhost:3000

# For Let's Encrypt renewal
sudo certbot renew --force-renewal

# Validate certificate installation
curl -vI https://localhost:3000
```

#### Issue: High Memory Usage

**Symptoms:**
- Application crashes with out-of-memory error
- Slow response times
- System becomes unresponsive

**Solutions:**
```bash
# Check current memory usage
ps aux | grep node

# Monitor in real-time
top -p <PID>

# Check for memory leaks
npm install -g clinic
clinic doctor -- node dist/index.js

# Increase Node.js heap size
NODE_OPTIONS="--max-old-space-size=4096" npm start

# Analyze heap dump
node --inspect dist/index.js
# Open chrome://inspect in Chrome DevTools

# Clear cache
npm run cache:clear
```

#### Issue: Slow API Responses

**Symptoms:**
- API endpoints taking > 500ms
- Timeout errors
- High response times under load

**Solutions:**
```bash
# Enable query logging
# In .env: DATABASE_LOG_QUERIES=true

# Analyze slow queries
sudo -u postgres psql -d eduia -c "SELECT query, calls, mean_time FROM pg_stat_statements ORDER BY mean_time DESC LIMIT 10;"

# Check database connection pool status
npm run db:pool:status

# Monitor response times
npm install -g clinic
clinic bubbleprof -- npm start

# Check for N+1 queries
npm run analyze:queries

# Optimize indexes
npm run db:indexes:analyze
```

**Performance Optimization Checklist:**
```
- Enable database query caching
- Implement API response caching (Redis)
- Optimize database indexes
- Use connection pooling
- Enable gzip compression
- Implement pagination for large datasets
- Use lazy loading for resources
- Monitor and profile regularly
```

#### Issue: Authentication Token Problems

**Symptoms:**
- `Invalid token` or `Token expired`
- `Unauthorized` errors on valid tokens
- Token refresh not working

**Solutions:**
```bash
# Decode JWT token (for debugging)
npm install -g jwt-cli
jwt-cli decode <token>

# Check token expiration
npm run token:validate -- <token>

# Clear blacklisted tokens
npm run token:clear:blacklist

# Regenerate JWT secrets
npm run secrets:regenerate
# WARNING: This will invalidate all existing tokens

# Check token in database
psql -c "SELECT * FROM token_blacklist WHERE token = '<token>';"
```

#### Issue: File Upload Failures

**Symptoms:**
- Upload size exceeded errors
- File type rejected
- Upload hangs or times out

**Solutions:**
```bash
# Check file size limit
# In .env: MAX_FILE_SIZE=10485760  (10MB)

# Verify upload directory permissions
ls -la /path/to/uploads

# Check available disk space
df -h

# Monitor upload in progress
tail -f /var/log/nginx/eduia_access.log | grep upload

# Test upload directly
curl -F "file=@test.pdf" https://yourdomain.com/api/v1/upload

# For large files, use chunked upload
npm install --save resumable.js
```

#### Issue: Email Not Sending

**Symptoms:**
- Password reset emails not received
- Verification emails not sent
- SMTP connection errors

**Solutions:**
```bash
# Check email service configuration
# Verify in .env:
# SMTP_HOST=smtp.gmail.com
# SMTP_PORT=587
# SMTP_USER=your_email@gmail.com

# Test SMTP connection
npm run test:email

# Check email logs
tail -f logs/email.log

# Verify Gmail app password (if using Gmail)
# Create at: https://myaccount.google.com/apppasswords

# Check spam folder for test emails

# Enable detailed email logging
# In .env: EMAIL_DEBUG=true
```

#### Issue: Deployment Failed

**Symptoms:**
- `npm install` fails during deployment
- Build step times out
- Application crashes after deploy

**Solutions:**
```bash
# Check deployment logs
pm2 logs eduia-api

# Verify all environment variables are set
env | grep -i eduia

# Check disk space
df -h

# Verify Git access
git status

# Test database migration
npm run db:migrate:status

# Rollback to previous version
git revert HEAD
npm run build
pm2 restart ecosystem.config.js

# Check application health
curl https://yourdomain.com/health
```

#### Issue: CORS Errors in Frontend

**Symptoms:**
- `Access-Control-Allow-Origin` errors
- Cross-origin requests blocked
- Preflight requests failing

**Solutions:**
```bash
# Verify CORS configuration in .env
ALLOWED_ORIGINS=https://yourdomain.com,https://www.yourdomain.com

# Check CORS headers in response
curl -i -H "Origin: https://example.com" https://yourdomain.com/api/v1/users/me

# Enable CORS debugging
// In application config
app.use(cors({
  origin: function(origin, callback) {
    console.log('CORS request from:', origin);
    // ... rest of config
  }
}));

# Verify preflight OPTIONS request
curl -i -X OPTIONS https://yourdomain.com/api/v1/users/me \
  -H "Origin: https://frontend.com" \
  -H "Access-Control-Request-Method: GET"
```

### Debug Mode

Enable comprehensive debugging:

```bash
# Set debug level
DEBUG=eduia:* npm run dev

# Or in .env
DEBUG=*
LOG_LEVEL=debug

# Common debug namespaces
DEBUG=eduia:db,eduia:auth,eduia:api npm run dev
```

### Getting Help

**Resources:**
- GitHub Issues: https://github.com/maatsuh/testrepo/issues
- Documentation: https://eduia.docs.example.com
- Email Support: support@eduia.com
- Community Forum: https://forum.eduia.com

**When reporting issues, include:**
1. Node.js and npm versions: `node -v && npm -v`
2. Database version: `psql --version`
3. Full error message and stack trace
4. Steps to reproduce
5. Environment details (OS, Node.js version)
6. Relevant logs from `logs/` directory

---

## Version History

**Latest Version:** 1.0.0
**Release Date:** 2026-01-11

### v1.0.0 Features
- Core learning management system
- AI-powered course recommendations
- Student assessment and grading
- Real-time collaboration tools
- Comprehensive analytics dashboard
- Multi-language support
- Secure authentication system

---

## License

EduIA Platform is licensed under the MIT License. See LICENSE file for details.

---

## Contributing

Contributions are welcome! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

---

## Support & Contact

- **Email:** support@eduia.com
- **Website:** https://www.eduia.com
- **Documentation:** https://docs.eduia.com
- **Status Page:** https://status.eduia.com

---

**Last Updated:** January 11, 2026
**Maintained By:** EduIA Development Team
