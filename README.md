# Student Management REST API

A beginner-friendly backend project for **SW426 Software Quality Engineering**. It provides a complete Student Management REST API using **Node.js, Express.js, MongoDB, Mongoose, Jest, Supertest, and Postman**.

The project supports creating, reading, updating, and deleting student records. It includes validation, centralized error handling, automated API tests, an importable Postman collection, and a project report with a presentation guide.

## Project Overview

The API stores student records in MongoDB and exposes JSON endpoints under `/api/students`.

Main capabilities:

- Create a student with a unique roll number.
- List all students.
- Retrieve one student by MongoDB ObjectId.
- Update one or more fields of an existing student.
- Delete a student and verify that it no longer exists.
- Validate required fields, data types, semester range, and email format.
- Return clear HTTP status codes and JSON error messages.
- Run automated integration tests against a separate test database.
- Run a 12-request Postman collection in a safe order.

There is no frontend because this assignment focuses on backend development, API quality, and Postman testing.

## Features

- Full CRUD REST API
- MongoDB persistence through Mongoose
- Centralized error-handling middleware
- Unknown-route handling
- Strict request validation before database writes
- Unique roll-number enforcement
- A separate database for automated tests
- 30 Jest/Supertest tests
- 12-request Postman collection with 25 assertions
- Environment-based database configuration
- Local MongoDB or MongoDB Atlas support
- No credentials hardcoded in source code
- Detailed README, project report, and presentation guide

## Technology Stack

| Technology | Purpose |
|---|---|
| Node.js | JavaScript runtime |
| Express.js | Web server and routing |
| MongoDB | NoSQL database |
| Mongoose | MongoDB object modeling and validation |
| JavaScript | Implementation language |
| dotenv | Environment-variable configuration |
| Jest | Automated test runner |
| Supertest | HTTP API testing |
| Postman / Newman | Manual and command-line API collection testing |
| Git and GitHub | Version control and source hosting |

Tested environment:

- Node.js v24.11.1
- npm 11.6.2
- MongoDB 8.3.4
- Express 5.3.0
- Mongoose 8.24.6
- Jest 30.5.2
- Supertest 7.3.1

> Mongoose 8 is intentionally used because MongoDB driver 7.x currently has a handshake compatibility issue when running under Jest. Project code uses standard Mongoose APIs and is beginner-friendly.

## Prerequisites

Install the following before running the project:

1. **Node.js 18 or newer** and npm
2. **MongoDB Community Server** or a **MongoDB Atlas** account
3. **Postman** for manually running the collection
4. **Git** for version control

Check the installed versions in PowerShell:

```powershell
node --version
npm --version
git --version
```

Check that MongoDB is installed and running locally:

```powershell
Get-Service MongoDB
mongosh --quiet --eval "db.adminCommand({ ping: 1 })" mongodb://127.0.0.1:27017/admin
```

The expected ping result is similar to:

```json
{ "ok": 1 }
```

## Project Structure

```text
SQE project/
├── src/
│   ├── config/
│   │   └── db.js
│   ├── controllers/
│   │   └── studentController.js
│   ├── middleware/
│   │   ├── errorHandler.js
│   │   └── notFound.js
│   ├── models/
│   │   └── Student.js
│   ├── routes/
│   │   └── studentRoutes.js
│   ├── utils/
│   │   └── validateStudentInput.js
│   ├── app.js
│   └── server.js
├── tests/
│   └── student.test.js
├── postman/
│   └── Student-Management-API.postman_collection.json
├── docs/
│   ├── Project-Report.md
│   ├── Presentation-Guide.md
│   └── screenshots/
│       └── README.md
├── .env
├── .env.example
├── .gitignore
├── package.json
├── package-lock.json
└── README.md
```

Important files:

- `src/app.js` builds the Express application and is imported by the tests.
- `src/server.js` loads environment variables, connects to MongoDB, and starts the HTTP server.
- `src/models/Student.js` defines the database schema and Mongoose validation.
- `src/utils/validateStudentInput.js` performs strict request validation before database operations.
- `src/controllers/studentController.js` contains the CRUD logic.
- `src/middleware/errorHandler.js` converts errors into safe JSON responses.

## Installation

Open **Windows PowerShell** and run:

```powershell
cd "C:\Users\Waseem Ahmed\Desktop\SQE project"
npm install
```

If you downloaded this project as a fresh copy, create your local environment file:

```powershell
Copy-Item .env.example .env
```

Then open `.env` in a text editor:

```powershell
notepad .env
```

A local `.env` file already exists in this working copy and is excluded from Git.

## Environment Variable Configuration

Example `.env` for local MongoDB:

```env
PORT=5000
NODE_ENV=development
MONGO_URI=mongodb://127.0.0.1:27017/student_management
MONGO_URI_TEST=mongodb://127.0.0.1:27017/student_management_test
```

| Variable | Meaning |
|---|---|
| `PORT` | HTTP port used by the Express server |
| `NODE_ENV` | Runtime environment name |
| `MONGO_URI` | Main development database connection string |
| `MONGO_URI_TEST` | Isolated database used by Jest |

The automated test database name **must end with `_test`**. The test cleanup code refuses to drop a database whose name does not end with `_test`. This prevents accidental deletion of the development database.

## MongoDB Setup

### Option A: Local MongoDB

1. Install MongoDB Community Server.
2. Make sure the MongoDB Windows service is running:

```powershell
Get-Service MongoDB
```

If it is stopped, start it from an Administrator PowerShell window:

```powershell
Start-Service MongoDB
```

3. Keep this value in `.env`:

```env
MONGO_URI=mongodb://127.0.0.1:27017/student_management
```

The `student_management` database and `students` collection are created automatically when the first student is inserted.

### Option B: MongoDB Atlas

1. Create a free cluster in MongoDB Atlas.
2. Create a database user.
3. Add your development IP address to the Atlas network-access list.
4. Copy the driver connection string.
5. Replace the local `MONGO_URI` in `.env` with your Atlas URI:

```env
MONGO_URI=mongodb+srv://YOUR_USER:YOUR_PASSWORD@YOUR_CLUSTER/student_management?retryWrites=true&w=majority
```

6. Keep the test URI local, or create a separate Atlas database ending in `_test`.

Never commit the real `.env` file or real database credentials. Only `.env.example` belongs in Git.

## Running the Server

Development mode with automatic restart:

```powershell
npm run dev
```

Normal mode:

```powershell
npm start
```

Expected output:

```text
MongoDB connected: 127.0.0.1/student_management
Server running at http://localhost:5000
```

Open the health route:

```powershell
Invoke-RestMethod -Uri "http://localhost:5000/"
```

If the server is running correctly, you will receive a JSON message that the Student Management API is running.

To stop the server, press `Ctrl + C` in the terminal.

## API Endpoints

Base URL: `http://localhost:5000`

| No. | Method | Endpoint | Purpose | Success Status |
|---|---|---|---|---|
| 1 | POST | `/api/students` | Create a student | 201 Created |
| 2 | GET | `/api/students` | Get all students | 200 OK |
| 3 | GET | `/api/students/:id` | Get one student | 200 OK |
| 4 | PUT | `/api/students/:id` | Update a student | 200 OK |
| 5 | DELETE | `/api/students/:id` | Delete a student | 200 OK |
| 6 | GET | `/` | Health check | 200 OK |

`PUT` accepts partial updates. For example, you can update only the semester.

### Student Fields

| Field | Type | Required | Rules |
|---|---|---|---|
| `name` | String | Yes | 2–100 characters, trimmed |
| `rollNo` | String | Yes | 2–30 characters, trimmed, unique |
| `department` | String | Yes | 2–100 characters, trimmed |
| `semester` | Integer | Yes | 1–12 only |
| `email` | String | No | Valid email format, maximum 254 characters |

## Example Requests and Responses

### 1. Create a Student

Request:

```http
POST /api/students
Content-Type: application/json
```

```json
{
  "name": "Ayesha Khan",
  "rollNo": "BSCS-2026-001",
  "department": "Computer Science",
  "semester": 3,
  "email": "ayesha.khan@example.com"
}
```

Example response: **201 Created**

```json
{
  "success": true,
  "message": "Student created successfully.",
  "data": {
    "_id": "507f1f77bcf86cd799439011",
    "name": "Ayesha Khan",
    "rollNo": "BSCS-2026-001",
    "department": "Computer Science",
    "semester": 3,
    "email": "ayesha.khan@example.com",
    "createdAt": "2026-10-22T10:00:00.000Z",
    "updatedAt": "2026-10-22T10:00:00.000Z"
  }
}
```

PowerShell example:

```powershell
$body = @{
  name = "Ayesha Khan"
  rollNo = "BSCS-2026-001"
  department = "Computer Science"
  semester = 3
  email = "ayesha.khan@example.com"
} | ConvertTo-Json

$created = Invoke-RestMethod `
  -Method Post `
  -Uri "http://localhost:5000/api/students" `
  -ContentType "application/json" `
  -Body $body

$studentId = $created.data._id
$studentId
```

### 2. Get All Students

Request:

```http
GET /api/students
```

Example response: **200 OK**

```json
{
  "success": true,
  "count": 1,
  "data": [
    {
      "_id": "507f1f77bcf86cd799439011",
      "name": "Ayesha Khan",
      "rollNo": "BSCS-2026-001",
      "department": "Computer Science",
      "semester": 3,
      "email": "ayesha.khan@example.com",
      "createdAt": "2026-10-22T10:00:00.000Z",
      "updatedAt": "2026-10-22T10:00:00.000Z"
    }
  ]
}
```

PowerShell example:

```powershell
Invoke-RestMethod -Uri "http://localhost:5000/api/students"
```

### 3. Get One Student

Request:

```http
GET /api/students/507f1f77bcf86cd799439011
```

Example response: **200 OK**

```json
{
  "success": true,
  "data": {
    "_id": "507f1f77bcf86cd799439011",
    "name": "Ayesha Khan",
    "rollNo": "BSCS-2026-001",
    "department": "Computer Science",
    "semester": 3,
    "email": "ayesha.khan@example.com",
    "createdAt": "2026-10-22T10:00:00.000Z",
    "updatedAt": "2026-10-22T10:00:00.000Z"
  }
}
```

PowerShell example:

```powershell
Invoke-RestMethod -Uri "http://localhost:5000/api/students/$studentId"
```

### 4. Update a Student

Request:

```http
PUT /api/students/507f1f77bcf86cd799439011
Content-Type: application/json
```

```json
{
  "name": "Ayesha Noor",
  "department": "Software Engineering",
  "semester": 4
}
```

Example response: **200 OK**

```json
{
  "success": true,
  "message": "Student updated successfully.",
  "data": {
    "_id": "507f1f77bcf86cd799439011",
    "name": "Ayesha Noor",
    "rollNo": "BSCS-2026-001",
    "department": "Software Engineering",
    "semester": 4,
    "email": "ayesha.khan@example.com",
    "createdAt": "2026-10-22T10:00:00.000Z",
    "updatedAt": "2026-10-22T10:05:00.000Z"
  }
}
```

PowerShell example:

```powershell
$updateBody = @{
  name = "Ayesha Noor"
  department = "Software Engineering"
  semester = 4
} | ConvertTo-Json

Invoke-RestMethod `
  -Method Put `
  -Uri "http://localhost:5000/api/students/$studentId" `
  -ContentType "application/json" `
  -Body $updateBody
```

### 5. Delete a Student

Request:

```http
DELETE /api/students/507f1f77bcf86cd799439011
```

Example response: **200 OK**

```json
{
  "success": true,
  "message": "Student deleted successfully.",
  "data": {
    "_id": "507f1f77bcf86cd799439011",
    "name": "Ayesha Noor",
    "rollNo": "BSCS-2026-001",
    "department": "Software Engineering",
    "semester": 4
  }
}
```

PowerShell example:

```powershell
Invoke-RestMethod `
  -Method Delete `
  -Uri "http://localhost:5000/api/students/$studentId"
```

### 6. Verify the Deleted Student

Send the GET request again. The server should return **404 Not Found**:

```json
{
  "success": false,
  "message": "Student not found."
}
```

### Validation Error Example

Request:

```json
{
  "name": "Incomplete Student"
}
```

Response: **400 Bad Request**

```json
{
  "success": false,
  "message": "Validation failed.",
  "errors": {
    "rollNo": "Roll number is required.",
    "department": "Department is required.",
    "semester": "Semester is required."
  }
}
```

### Duplicate Roll Number Example

Response: **409 Conflict**

```json
{
  "success": false,
  "message": "A student with this roll number already exists.",
  "errors": {
    "rollNo": "A student with this roll number already exists."
  }
}
```

### Malformed MongoDB ID Example

Request:

```http
GET /api/students/not-a-valid-object-id
```

Response: **400 Bad Request**

```json
{
  "success": false,
  "message": "Invalid student ID.",
  "errors": {
    "id": "The student ID must be a valid MongoDB ObjectId."
  }
}
```

## HTTP Status Codes

| Status | Meaning in This Project |
|---|---|
| 200 OK | A read, update, or delete succeeded |
| 201 Created | A new student was created |
| 400 Bad Request | Validation failed, JSON was invalid, or the ID was malformed |
| 404 Not Found | A student or route does not exist |
| 409 Conflict | The roll number already exists |
| 500 Internal Server Error | An unexpected server or database error occurred |

Server-side stack traces and database credentials are never returned to API clients.

## How to Import and Run the Postman Collection

The collection is located at:

```text
postman/Student-Management-API.postman_collection.json
```

Steps:

1. Start the API server:

```powershell
npm run dev
```

2. Open Postman.
3. Click **Import**.
4. Select `Student-Management-API.postman_collection.json`.
5. Open the imported collection named **Student Management API**.
6. Confirm these collection variables exist:
   - `baseUrl` = `http://localhost:5000`
   - `studentId` = empty; filled automatically by the first test
   - `rollNo` = empty; generated automatically by the first test
7. Use **Run collection** and keep the request order.
8. Do not run delete or get-deleted requests before the create request.

The collection creates a unique roll number with `Date.now()` and saves the new student's `_id`. This makes repeated runs possible without manual clean-up.

### Postman Test Scenarios

| No. | Request | Expected Result |
|---|---|---|
| 01 | Create valid student | 201 Created |
| 02 | Get all students | 200 OK |
| 03 | Get one existing student | 200 OK |
| 04 | Update existing student | 200 OK |
| 05 | Create duplicate roll number | 409 Conflict |
| 06 | Delete existing student | 200 OK |
| 07 | Get deleted student | 404 Not Found |
| 08 | Missing required fields | 400 Bad Request |
| 09 | Invalid semester | 400 Bad Request |
| 10 | Nonexistent valid ObjectId | 404 Not Found |
| 11 | Malformed ObjectId | 400 Bad Request |
| 12 | Incorrect route | 404 Not Found |

### Run the Collection from PowerShell with Newman

Newman is the command-line runner for Postman collections. No permanent installation is required:

```powershell
npx --yes newman run ".\postman\Student-Management-API.postman_collection.json" --env-var "baseUrl=http://localhost:5000"
```

For a safe isolated verification run, use a separate database:

```powershell
$env:MONGO_URI = "mongodb://127.0.0.1:27017/student_management_postman_test"
$env:PORT = "5010"
npm start
```

Then, in a second PowerShell window:

```powershell
npx --yes newman run ".\postman\Student-Management-API.postman_collection.json" --env-var "baseUrl=http://127.0.0.1:5010"
```

After testing, stop the server and delete only the isolated Postman database:

```powershell
mongosh --quiet --eval "db.dropDatabase()" mongodb://127.0.0.1:27017/student_management_postman_test
```

## Running Automated Tests

Run all tests:

```powershell
npm test
```

The Jest suite uses `MONGO_URI_TEST` and performs these safety steps:

1. Verifies that the connected database name ends with `_test`.
2. Drops and recreates the isolated test data.
3. Runs the API integration tests with Supertest.
4. Removes the test data and disconnects.

The tests do not write to the development database.

Expected summary:

```text
Test Suites: 1 passed, 1 total
Tests:       30 passed, 30 total
```

### Automated Test Coverage

- Create a valid student
- Create a student without optional email
- Missing required fields
- Empty strings
- Incorrect data types
- Invalid semester values
- Invalid email
- Duplicate roll number
- Non-object request body
- Malformed JSON
- Get all students
- Get an empty list
- Get one student
- Nonexistent ObjectId
- Malformed ObjectId
- Full and partial update
- Invalid update data
- Update with no recognized fields
- Duplicate roll number during update
- Update nonexistent student
- Malformed ID during update
- Delete student
- Delete nonexistent student
- Malformed ID during delete
- Retrieve a deleted student
- Unknown route
- Health route

## Actual Verification Results

Verification was performed on **22 October 2026** in this workspace.

### Jest and Supertest

```text
Test Suites: 1 passed, 1 total
Tests:       30 passed, 30 total
Time:        2.93 s
```

### Postman Collection with Newman

```text
Iterations:          1 executed, 0 failed
Requests:           12 executed, 0 failed
Test scripts:       12 executed, 0 failed
Pre-request scripts: 1 executed, 0 failed
Assertions:         25 executed, 0 failed
```

The final Newman run returned exit code `0`.

During the first Newman run, one collection assertion incorrectly expected a `name` validation error even though the request contained a name. The assertion was corrected, the collection was rerun, and all 25 assertions passed.

Postman GUI screenshots are not generated automatically. See the screenshot instructions in `docs/Project-Report.md` and `docs/screenshots/README.md`.

## Common Errors and Solutions

### `ECONNREFUSED 127.0.0.1:27017`

MongoDB is not running.

```powershell
Get-Service MongoDB
Start-Service MongoDB
```

If you use Atlas, check `MONGO_URI` and your network access list.

### `MongooseServerSelectionError`

Possible causes:

- Incorrect MongoDB host or port
- MongoDB service stopped
- Atlas IP address not allowlisted
- Incorrect Atlas username or password
- Internet or DNS problem

Test the local connection:

```powershell
mongosh --quiet --eval "db.adminCommand({ ping: 1 })" mongodb://127.0.0.1:27017/admin
```

### `EADDRINUSE: address already in use :::5000`

Another process is using port 5000. Change the port in `.env`:

```env
PORT=5001
```

Then use `http://localhost:5001` in Postman.

### `MONGO_URI is missing`

Create `.env` from `.env.example`:

```powershell
Copy-Item .env.example .env
```

### Tests refuse to drop the database

The test database name must end with `_test`. This is a deliberate safety check. Use a value such as:

```env
MONGO_URI_TEST=mongodb://127.0.0.1:27017/student_management_test
```

### Postman requests fail because `studentId` is empty

Run the collection from the beginning. Request 01 creates the student and saves `studentId`. Do not run request 03 or later by itself before request 01.

### Duplicate roll number during repeated manual testing

The database enforces unique roll numbers. Delete the existing test student or use a different `rollNo`.

### Jest reports a MongoDB client-metadata handshake error

This project uses Mongoose 8 to avoid a known MongoDB Node driver 7.x compatibility issue with Jest. Do not upgrade to Mongoose 9 unless the dependency issue has been fixed and the test suite has been rerun.

### `npm audit` reports development-only warnings

Run:

```powershell
npm audit --omit=dev
```

The production dependency tree currently reports zero vulnerabilities. Development tooling may report transitive advisories from Jest and file-watching packages.

## Git and GitHub Upload Instructions

A separate Git repository has been initialized inside this project folder. The parent folder's repository is not used.

These commands are for **your** use when you are ready to publish the project:

```powershell
cd "C:\Users\Waseem Ahmed\Desktop\SQE project"
git status
git add .
git commit -m "Complete Student Management REST API project"
```

Create an empty repository on GitHub, then run:

```powershell
git remote add origin https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
git branch -M main
git push -u origin main
```

Replace the GitHub URL with your real repository URL. Do not commit `.env`, passwords, database connection strings containing credentials, or `node_modules`.

## Assignment Documentation

- Full report: `docs/Project-Report.md`
- Presentation script: `docs/Presentation-Guide.md`
- Screenshot instructions: `docs/screenshots/README.md`
- Postman collection: `postman/Student-Management-API.postman_collection.json`

## Limitations

- No user authentication or authorization
- No frontend or admin dashboard
- No pagination, filtering, or sorting
- No production deployment configuration
- Postman screenshots and student details must be added manually
- Local and Atlas database testing depend on your environment

## Future Improvements

- Add JWT authentication and role-based access
- Add pagination, search, and department filters
- Add Swagger/OpenAPI documentation
- Add Docker Compose for the API and MongoDB
- Add CI/CD with GitHub Actions
- Add coverage reporting and stricter linting
- Add a frontend dashboard
