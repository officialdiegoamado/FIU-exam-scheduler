# 🚀 FIU Exam Scheduler - Live Demo Guide

## ⚡ Quick Start (3 Steps)

### Step 1: Start Database
```bash
cd "fiu exam scheduler"
docker-compose up db
```
**Result:** PostgreSQL running on localhost:5432

### Step 2: Start Backend
```bash
cd backend
./mvnw spring-boot:run
# OR if you have Maven installed:
# mvn spring-boot:run
```
**Result:** Spring Boot API running on http://localhost:8080

### Step 3: Start Frontend
```bash
cd frontend
npm install
npm run dev
```
**Result:** React app running on http://localhost:5173

---

## 🧪 Live Testing Examples

### 1. Test Authentication (Admin Login)
```bash
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "admin@fiu.edu",
    "password": "admin123"
  }'
```

**Expected Response:**
```json
{
  "token": "eyJhbGciOiJIUzI1NiJ9...",
  "role": "admin"
}
```

### 2. Register a New Student
```bash
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{
    "email": "john.doe@fiu.edu",
    "password": "password123",
    "fullName": "John Doe"
  }'
```

### 3. Search Courses (No Auth Required)
```bash
curl -X GET "http://localhost:8080/api/courses?query=programming"
```

**Expected Response:**
```json
[
  {
    "id": "660e8400-e29b-41d4-a716-446655440001",
    "code": "COP2210",
    "title": "Programming I",
    "department": "CS"
  },
  {
    "id": "660e8400-e29b-41d4-a716-446655440002", 
    "code": "COP3337",
    "title": "Programming II",
    "department": "CS"
  }
]
```

### 4. View Available Sections
```bash
curl -X GET "http://localhost:8080/api/sections"
```

**Expected Response:**
```json
[
  {
    "id": "770e8400-e29b-41d4-a716-446655440001",
    "courseCode": "COP2210",
    "title": "Programming I",
    "time": "09:00-10:15",
    "room": "ECS 134",
    "instructor": "Dr. John Smith",
    "capacity": 30,
    "available": 15
  }
]
```

### 5. View All Exams
```bash
curl -X GET "http://localhost:8080/api/exams"
```

**Expected Response:**
```json
[
  {
    "id": "990e8400-e29b-41d4-a716-446655440001",
    "courseCode": "COP2210", 
    "courseTitle": "Programming I",
    "examDate": "2024-03-15",
    "startTime": "09:00",
    "endTime": "10:30",
    "location": "ECS 134",
    "examType": "midterm",
    "instructor": "Dr. John Smith",
    "room": "ECS 134"
  }
]
```

---

## 🎯 Frontend Testing (Visual Interface)

### 1. Open Browser
Navigate to: **http://localhost:5173**

### 2. Login as Admin
- Email: `admin@fiu.edu`
- Password: `admin123`
- **Result:** Access to admin panel for managing exams

### 3. Register as Student
- Use the register form
- **Result:** Student dashboard with enrollment features

### 4. Test Course Search
- Search for "programming" or "math"
- **Result:** Filtered course results

### 5. Test Enrollment (Student)
- Browse available sections
- Click "Enroll" on any section
- **Result:** Added to your schedule, seat count decreases

---

## 📊 Database Verification

### Connect to Database
```bash
docker exec -it fiu-scheduler-db psql -U scheduler -d scheduler
```

### Sample Queries
```sql
-- View all users
SELECT email, role FROM users;

-- View all courses
SELECT code, title, department FROM courses;

-- View enrollments with student info
SELECT s.full_name, c.code, c.title 
FROM enrollments e
JOIN students s ON e.student_id = s.id
JOIN sections sec ON e.section_id = sec.id  
JOIN courses c ON sec.course_id = c.id;

-- View upcoming exams
SELECT c.code, c.title, e.exam_date, e.start_time, e.location
FROM exams e
JOIN sections s ON e.section_id = s.id
JOIN courses c ON s.course_id = c.id
ORDER BY e.exam_date;
```

---

## 🔧 Advanced API Testing

### Enroll Student in Section (Requires Auth)
```bash
# First login to get token
TOKEN=$(curl -s -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"admin@fiu.edu","password":"admin123"}' | \
  jq -r '.token')

# Then enroll (replace sectionId with actual ID)
curl -X POST http://localhost:8080/api/schedule/enroll \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"sectionId":"770e8400-e29b-41d4-a716-446655440001"}'
```

### Create New Exam (Admin Only)
```bash
curl -X POST http://localhost:8080/api/admin/exams \
  -H "Authorization: Bearer $TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "sectionId": "770e8400-e29b-41d4-a716-446655440001",
    "examDate": "2024-05-15",
    "startTime": "14:00", 
    "endTime": "15:30",
    "location": "ECS 142",
    "examType": "final"
  }'
```

---

## ✅ Expected Results Checklist

### Backend Health Check
- [ ] ✅ Database connected (no connection errors)
- [ ] ✅ Sample data loaded (5 courses, 6 sections, 6 exams)
- [ ] ✅ Admin login works (`admin@fiu.edu` / `admin123`)
- [ ] ✅ Student registration works
- [ ] ✅ JWT tokens generated and validated
- [ ] ✅ Course search returns results
- [ ] ✅ Exam endpoints return data

### Frontend Health Check  
- [ ] ✅ Login page loads
- [ ] ✅ Registration works
- [ ] ✅ Course search interface works
- [ ] ✅ Enrollment/drop functionality works
- [ ] ✅ Real-time updates when enrolling
- [ ] ✅ Admin panel accessible for admin users

### Integration Test
- [ ] ✅ Register new student via frontend
- [ ] ✅ Login with new credentials
- [ ] ✅ Search and enroll in course
- [ ] ✅ View personal schedule
- [ ] ✅ View personal exam schedule
- [ ] ✅ Admin can create/manage exams

---

## 🎉 You Now Have:

1. **Complete Authentication System** - JWT tokens, password hashing, role-based access
2. **Full Course Management** - Search, filtering, enrollment with capacity tracking  
3. **Exam Scheduling System** - Create, view, manage exams by student/admin
4. **Real-time Updates** - Live seat availability, schedule changes
5. **Production Database** - PostgreSQL with proper relationships and migrations
6. **Secure API** - 15+ endpoints with proper authentication and authorization

**Your FIU Exam Scheduler is now a professional-grade university system!** 🎓✨ 