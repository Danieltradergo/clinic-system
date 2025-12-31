# Users CRUD Module Integration Guide

This guide provides complete integration instructions for the Users CRUD module (Create, Read, Update, Delete operations).

## Overview

The Users CRUD module provides full management capabilities for system users including admins and staff members.

**Complete source code:** [Users CRUD Gist](https://gist.github.com/Danieltradergo/d98004ae163f4c43b22da60d6c2e82fc)

## Files to Create

```
src/modules/users/
├── users.service.ts      # Business logic for user operations
├── users.controller.ts   # API endpoints (includes users.module.ts)
├── users.module.ts       # Module definition
└── dto/
    ├── create-user.dto.ts
    └── update-user.dto.ts
```

## Step 1: Create Users Service

**File:** `src/modules/users/users.service.ts`

### Features:
- **create()** - Create new user with hashed password
- **findAll()** - Retrieve all users (excludes password)
- **findOne(id)** - Find user by ID
- **update(id, data)** - Update user profile
- **remove(id)** - Delete user
- **findByEmail(email)** - Find user by email (used in auth)

### Key Implementation Details:
- Uses Prisma ORM for database operations
- Handles duplicate email errors (P2002)
- Handles user not found errors (P2025)
- Excludes password from returned user data

## Step 2: Create Users Controller

**File:** `src/modules/users/users.controller.ts`

### API Endpoints:

```
POST   /users              # Create new user
GET    /users              # List all users
GET    /users/:id          # Get user by ID
PUT    /users/:id          # Update user
DELETE /users/:id          # Delete user
```

### Security:
- All endpoints protected with `@UseGuards(JwtAuthGuard)`
- Requires valid JWT token in Authorization header
- Password is hashed using bcrypt (10 rounds) before storing

### Example Requests:

**Create User:**
```bash
curl -X POST http://localhost:3000/users \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "email": "doctor@clinic.com",
    "password": "SecurePass123",
    "name": "Dr. Silva",
    "role": "staff"
  }'
```

**Get All Users:**
```bash
curl -X GET http://localhost:3000/users \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

**Update User:**
```bash
curl -X PUT http://localhost:3000/users/<USER_ID> \
  -H "Authorization: Bearer <JWT_TOKEN>" \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Dr. Updated Name",
    "role": "admin"
  }'
```

**Delete User:**
```bash
curl -X DELETE http://localhost:3000/users/<USER_ID> \
  -H "Authorization: Bearer <JWT_TOKEN>"
```

## Step 3: Update App Module

**File:** `src/app.module.ts`

Add UsersModule to imports:

```typescript
import { UsersModule } from './modules/users/users.module';

@Module({
  imports: [
    ConfigModule.forRoot(),
    PrismaModule,
    AuthModule,
    UsersModule,  // Add this line
  ],
})
export class AppModule {}
```

## Step 4: Install Dependencies

If not already installed:

```bash
npm install bcrypt
npm install --save-dev @types/bcrypt
```

## Testing

1. Start the application:
   ```bash
   npm run start:dev
   ```

2. Login first to get JWT token (see AUTH-INTEGRATION.md)

3. Test user endpoints using the curl commands above

## Database Schema

The User model (from Prisma schema):

```prisma
model User {
  id        String   @id @default(cuid())
  email     String   @unique
  password  String
  name      String
  role      String   @default("staff") // "admin" or "staff"
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

## Error Handling

- **409 Conflict** - Email already exists
- **404 Not Found** - User not found
- **400 Bad Request** - Invalid input data
- **401 Unauthorized** - Missing or invalid JWT token

## Next Steps

1. ✅ Authentication Module (JWT)
2. ✅ Users CRUD Module
3. ⏭️  Patients CRUD Module
4. ⏭️  Professionals CRUD Module
5. ⏭️  Frontend Integration (Next.js)
6. ⏭️  Deploy to Railway + Vercel

## Notes

- All timestamps use UTC
- User IDs are generated using CUID (collision-resistant unique ID)
- Passwords are never returned in API responses
- Role-based access control can be added using custom decorators
