# Professionals CRUD Module Integration Guide

This guide provides complete integration instructions for the Professionals CRUD module with availability management and specialization filtering.

## Overview

The Professionals module manages all healthcare professionals including doctors, psychologists, and psychiatrists. It includes scheduling capabilities and availability tracking.

**Complete source code:** [Professionals CRUD Gist](https://gist.github.com/Danieltradergo/9c8424ef62ec6ef9630be0726cc90834)

## Features

- Full CRUD operations for professional management
- Specialization filtering
- Work schedule management
- Availability checking for specific dates
- Professional licensing information
- Attendance history tracking
- Pagination support

## Files to Create

```
src/modules/professionals/
├── professionals.service.ts      # Business logic
├── professionals.controller.ts   # API endpoints
├── professionals.module.ts       # Module definition
└── dto/
    ├── create-professional.dto.ts
    └── update-professional.dto.ts
```

## Step 1: Create Professionals Service

**File:** `src/modules/professionals/professionals.service.ts`

### Methods:
- **create()** - Register new professional
- **findAll()** - List professionals with pagination and appointment count
- **findOne()** - Get professional details with attendance history
- **update()** - Update professional information
- **remove()** - Delete professional
- **findBySpecialization()** - Filter by specialty
- **getAvailability()** - Check availability for specific date

### Key Features:
- Work days management (MON, TUE, WED, etc.)
- Work hours tracking (start and end time)
- CRP/licensing information storage
- Attendance count statistics

## Step 2: Create Professionals Controller

**File:** `src/modules/professionals/professionals.controller.ts`

### API Endpoints:

```
POST   /professionals                              # Create professional
GET    /professionals                              # List all professionals
GET    /professionals/by-specialization/:spec      # Filter by specialization
GET    /professionals/availability/:id?date=YYYY-MM-DD  # Check availability
GET    /professionals/:id                          # Get professional details
PUT    /professionals/:id                          # Update professional
DELETE /professionals/:id                          # Delete professional
```

## Step 3: Example Requests

**Create Professional:**
```bash
curl -X POST http://localhost:3000/professionals \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Dr. Maria Silva",
    "email": "maria@clinic.com",
    "phone": "(11) 98765-4321",
    "specialization": "Psicólogo",
    "crp": "06/12345",
    "bio": "10 years experience in clinical psychology",
    "workDays": ["MON", "TUE", "WED", "THU", "FRI"],
    "workStartTime": "09:00",
    "workEndTime": "18:00"
  }'
```

**List Professionals:**
```bash
curl -X GET "http://localhost:3000/professionals?skip=0&take=10" \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

**Filter by Specialization:**
```bash
curl -X GET "http://localhost:3000/professionals/by-specialization/Psicólogo" \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

**Check Availability:**
```bash
curl -X GET "http://localhost:3000/professionals/availability/prof-123?date=2024-01-15" \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

Response includes:
- Work days for that professional
- Work schedule (start/end times)
- Already scheduled appointments for that date

**Get Professional Details:**
```bash
curl -X GET http://localhost:3000/professionals/<PROFESSIONAL_ID> \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

Returns full history of attendances with patient information.

**Update Professional:**
```bash
curl -X PUT http://localhost:3000/professionals/<PROFESSIONAL_ID> \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "workStartTime": "08:00",
    "workEndTime": "17:00",
    "bio": "Updated bio"
  }'
```

**Delete Professional:**
```bash
curl -X DELETE http://localhost:3000/professionals/<PROFESSIONAL_ID> \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

## Step 4: Update App Module

**File:** `src/app.module.ts`

```typescript
import { ProfessionalsModule } from './modules/professionals/professionals.module';

@Module({
  imports: [
    ConfigModule.forRoot(),
    PrismaModule,
    AuthModule,
    UsersModule,
    PatientsModule,
    ProfessionalsModule,  // Add this line
  ],
})
export class AppModule {}
```

## Database Schema

```prisma
model Professional {
  id              String   @id @default(cuid())
  name            String
  email           String   @unique
  phone           String
  specialization  String   // e.g., "Psicólogo", "Psiquiatra"
  crp             String?  @unique  // Council registration number
  license         String?
  bio             String?
  workDays        String[]  // ["MON", "TUE", ...]
  workStartTime   String?   // "09:00"
  workEndTime     String?   // "18:00"
  attendances     Attendance[] @relation("ProfessionalAttendances")
  createdAt       DateTime @default(now())
  updatedAt       DateTime @updatedAt
}
```

## Work Days Format

The system accepts work days as an array of abbreviations:

```
"MON" = Monday
"TUE" = Tuesday
"WED" = Wednesday
"THU" = Thursday
"FRI" = Friday
"SAT" = Saturday
"SUN" = Sunday
```

Example: `"workDays": ["MON", "TUE", "WED", "THU", "FRI"]`

## Work Time Format

Time should be in 24-hour format (HH:MM):

```
"workStartTime": "09:00"  // 9 AM
"workEndTime": "18:00"    // 6 PM
```

## Availability Check Response

```json
{
  "workDays": ["MON", "TUE", "WED", "THU", "FRI"],
  "workStartTime": "09:00",
  "workEndTime": "18:00",
  "scheduledAppointments": [
    "2024-01-15T10:00:00Z",
    "2024-01-15T14:30:00Z"
  ]
}
```

## Error Handling

- **400 Bad Request** - Invalid data or duplicate email/CRP
- **404 Not Found** - Professional not found
- **401 Unauthorized** - Missing or invalid JWT token

## Specializations Examples

```
- Psicólogo (Psychologist)
- Psiquiatra (Psychiatrist)
- Neuropsicólogo (Neuropsychologist)
- Terapeuta (Therapist)
- Conselheiro (Counselor)
```

## Next Steps

1. ✅ Authentication Module (JWT)
2. ✅ Users CRUD Module
3. ✅ Patients CRUD Module
4. ✅ Professionals CRUD Module
5. ⏭️  Frontend Integration (Next.js)
6. ⏭️  Deploy to Railway + Vercel

## Notes

- CRP is a unique identifier in Brazil (Council of Psychology)
- Work schedule is flexible - can be changed per professional
- Availability endpoint helps prevent double-booking
- All timestamps use UTC
- Professional deletion is permanent (no soft delete)
