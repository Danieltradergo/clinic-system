# Patients CRUD Module Integration Guide

This guide provides complete integration instructions for the Patients CRUD module with search, pagination, and data validation.

## Overview

The Patients module manages all patient records including contact information, medical history, insurance details, and relationships with healthcare professionals.

**Complete source code:** [Patients CRUD Gist](https://gist.github.com/Danieltradergo/b49cb81bfd4b24f2f169894389d0b994)

## Features

- Full CRUD operations for patient management
- CPF validation (Brazilian ID)
- Patient search by name, email, or CPF
- Pagination support (skip/take)
- Automatic timestamp tracking
- Medical history tracking
- Insurance integration
- Emergency contact management

## Files to Create

```
src/modules/patients/
├── patients.service.ts      # Business logic for patient operations
├── patients.controller.ts   # API endpoints
├── patients.module.ts       # Module definition
└── dto/
    ├── create-patient.dto.ts
    └── update-patient.dto.ts
```

## Step 1: Create Patients Service

**File:** `src/modules/patients/patients.service.ts`

### Methods:
- **create()** - Register new patient with validation
- **findAll()** - List patients with pagination
- **findOne()** - Get patient details with history
- **update()** - Update patient information
- **remove()** - Delete patient record
- **search()** - Full-text search by name, email, or CPF

### Key Features:
- CPF format validation (11 digits)
- Automatic CPF formatting (removes special characters)
- Relationship loading (insurance, attendance history)
- Error handling for duplicates

## Step 2: Create Patients Controller

**File:** `src/modules/patients/patients.controller.ts`

### API Endpoints:

```
POST   /patients                # Create new patient
GET    /patients                # List all patients (paginated)
GET    /patients/search?q=term  # Search patients
GET    /patients/:id            # Get patient details
PUT    /patients/:id            # Update patient
DELETE /patients/:id            # Delete patient
```

### Query Parameters:
- `skip` - Number of records to skip (default: 0)
- `take` - Number of records to return (default: 10)
- `q` - Search query for patient search

## Step 3: Example Requests

**Create Patient:**
```bash
curl -X POST http://localhost:3000/patients \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "João Silva",
    "email": "joao@example.com",
    "phone": "(11) 98765-4321",
    "dateOfBirth": "1985-05-15",
    "cpf": "123.456.789-00",
    "address": "Rua Principal 123",
    "city": "São Paulo",
    "state": "SP",
    "zipCode": "01310-100",
    "medicalHistory": "Asthma patient",
    "emergencyContact": "Maria Silva",
    "emergencyPhone": "(11) 99999-8888"
  }'
```

**List Patients (with pagination):**
```bash
curl -X GET "http://localhost:3000/patients?skip=0&take=20" \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

**Search Patients:**
```bash
curl -X GET "http://localhost:3000/patients/search?q=João" \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

**Get Patient Details:**
```bash
curl -X GET http://localhost:3000/patients/<PATIENT_ID> \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

**Update Patient:**
```bash
curl -X PUT http://localhost:3000/patients/<PATIENT_ID> \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "phone": "(11) 98765-0000",
    "medicalHistory": "Updated history"
  }'
```

**Delete Patient:**
```bash
curl -X DELETE http://localhost:3000/patients/<PATIENT_ID> \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

## Step 4: Update App Module

**File:** `src/app.module.ts`

```typescript
import { PatientsModule } from './modules/patients/patients.module';

@Module({
  imports: [
    ConfigModule.forRoot(),
    PrismaModule,
    AuthModule,
    UsersModule,
    PatientsModule,  // Add this line
  ],
})
export class AppModule {}
```

## Database Schema

Patient model relationships:

```prisma
model Patient {
  id                String   @id @default(cuid())
  name              String
  email             String   @unique
  phone             String
  dateOfBirth       DateTime
  cpf               String   @unique // 11 digits
  address           String
  city              String
  state             String
  zipCode           String
  insuranceId       String?
  insurance         Insurance?     @relation(fields: [insuranceId], references: [id])
  medicalHistory    String?
  emergencyContact  String?
  emergencyPhone    String?
  attendances       Attendance[]   @relation("PatientAttendances")
  createdAt         DateTime @default(now())
  updatedAt         DateTime @updatedAt
}
```

## CPF Validation

The service includes CPF format validation:
- Accepts format: XXX.XXX.XXX-XX or XXXXXXXXXXX
- Automatically removes formatting before storing
- Validates minimum 11 digits
- Prevents duplicate CPF registration

## Error Handling

- **400 Bad Request** - Invalid CPF format or duplicate email/CPF
- **404 Not Found** - Patient not found
- **401 Unauthorized** - Missing or invalid JWT token

## Pagination Best Practices

```bash
# Get first 10 patients
GET /patients?skip=0&take=10

# Get next 10 patients
GET /patients?skip=10&take=10

# Get 50 patients
GET /patients?skip=0&take=50
```

## Search Examples

```bash
# Search by name
GET /patients/search?q=João

# Search by email
GET /patients/search?q=joao@example.com

# Search by CPF (without formatting)
GET /patients/search?q=12345678900
```

## Next Steps

1. ✅ Authentication Module (JWT)
2. ✅ Users CRUD Module
3. ✅ Patients CRUD Module
4. ⏭️  Professionals CRUD Module
5. ⏭️  Frontend Integration (Next.js)
6. ⏭️  Deploy to Railway + Vercel

## Notes

- All dates use ISO 8601 format
- CPF is stored without formatting (11 digits)
- Patient records can be associated with multiple attendance records
- Insurance relationships are optional (can be null)
- Soft delete is not implemented - deletion is permanent
