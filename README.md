# DevGrade Documentation

## 1. Introduction
### What is DevGrade?
DevGrade is an advanced, fully automated platform designed to evaluate developers in real-world project environments. It offers an intelligent grading system that evaluates candidate performance based on the code they submit for an assessment. This is done by leveraging project templates, automatic test execution, and continuous feedback.

### Key Features:
- Automated grading based on predefined templates and test cases
- Real-time repository management using AWS CodeCommit
- Seamless CI/CD pipeline for continuous integration and deployment
- Detailed analytics on candidate performance

---

## 2. System Architecture
The architecture of DevGrade is based on a modern cloud-based infrastructure that ensures scalability, reliability, and security.

### Frontend:
The frontend is built using **Angular** and serves as the user interface for the platform. It allows both admins and candidates to interact with the system. Admins can manage assessments, templates, and view results, while candidates can receive assessments, clone repositories, complete tasks, and submit their results.

### Backend:
The backend is designed using **Node.js** in a **serverless microservices architecture** utilizing **AWS Lambda**. This ensures that the platform can scale with usage without needing to manage physical servers. The backend services handle tasks like user authentication, assessment scheduling, repository creation, and grading.

### Database:
DevGrade uses **MongoDB** as the primary database. It stores user data, assessment templates, candidate submissions, and scoring information.

---

## 3. Key Features
### Assessment Template Management:
- **AWS CodeCommit** is used to store project templates. These templates are partially completed projects that candidates will work on during their assessment. Templates can be versioned, ensuring that any updates don't affect ongoing assessments.
- Admins and developers can manage and modify these templates as needed. New templates can be added to support different types of assessments.
- When creating a template, developers receive an empty repository and credentials. They then build the project within this repository.
- The recommended workflow for creating templates is:
  1. Develop the complete solution first
  2. Remove specific parts of the code according to assessment tasks
  3. Create test cases for each task
  4. Push the modified code and tests to the repository


### Assessment Scheduling and Execution:
- Admin or HR schedule assessments for candidates, creating new repositories from templates stored in CodeCommit and S3.

#### Assessment Modes:
1. **Manual Mode**
   - Candidate clones the repository
   - Works on tasks locally
   - Pushes code back to the repository

2. **VM Mode**
   - Candidate receives VM credentials
   - Works on tasks within the provided VM environment
   - Submits work through the VM

#### Assessment Start Types:
1. **Manual Start**
   - Candidate can begin anytime after the scheduled time
   - Must complete within the given duration
   - Flexible start time within the allowed window

2. **Automatic Start**
   - Assessment begins automatically at the scheduled time
   - Ends automatically after the given duration
   - No code pushes allowed after the duration expires
   - Important: Set sufficient duration for completion

3. **Deadline Start**
   - Candidate can begin anytime after the scheduled time
   - Must complete within the given duration
   - System automatically ends the assessment after the duration expires
   - Flexible start time with strict end time

#### Assessment Process:
1. When scheduling an assessment:
   - System copies the template from S3
   - Creates a new repository in CodeCommit with a unique session ID
   - Candidate receives the repository link

2. During the assessment:
   - Candidate receives IAM credentials for repository access
   - Works on the project using the provided environment
   - Submits code by pushing to the repository

3. After submission:
   - Backend automatically triggers AWS CodeBuild for evaluation
   - Results are processed and stored
   - Admin receives notification of completion

#### Assessment Types
DevGrade supports three types of assessments to evaluate candidates comprehensively:

1. **Coding Assessment**
   - Candidates work on real-world programming tasks
   - Includes automated test case evaluation
   - Supports multiple programming languages
   - Features:
     - Code compilation and execution
     - Automated test running
     - Code quality checks
     - Performance evaluation
     - Real-time feedback

2. **Multiple Choice Questions (MCQ)**
   - Structured questions with predefined answers
   - Features:
     - Single and multiple correct answers
     - Not considered for evaluation

3. **Video Questions**
   - Candidates record video responses to questions
   - Features:
     - Time-limited video recording
     - Multiple attempts allowed
     - Video quality checks
     - Manual evaluation by reviewers
     - Not considered on evalation final marks

Each assessment type can be:
- Assesment must have coding assessment
- Combined with other types in a single assessment
- Scheduled independently
- Configured with different time limits
- Set up with custom evaluation criteria
- Integrated with automated or manual grading

### Assessment Evaluation:
The evaluation process is automated and follows a structured approach:

1. **Test Case Based Evaluation**
   - Each assessment contains predefined test cases
   - System automatically runs these tests against candidate submissions
   - Results are calculated based on test case outcomes

2. **Scoring Configuration**
   - Each assessment includes a configuration file
   - Defines how scores should be calculated
   - Specifies weightage for different components
   - Sets up grading criteria and thresholds

3. **Results Processing**
   - System processes test results using the assessment configuration
   - Calculates final scores based on defined criteria
   - Generates detailed evaluation report

4. **Results Delivery**
   - Scores are sent to the frontend via the `generatepdf` endpoint
   - Admin can review and approve results
   - Candidate receives their evaluation report once approved

### Automated Email Notifications:
- Once an assessment is scheduled, candidates are notified via email with instructions and a link to clone the repository.
- Admins are also notified when assessments are completed and graded, with results available for review.



---

## 4. User Roles and Permissions
### Admin Role:
Admins have complete control over the DevGrade platform. They can:
- Create, modify, and delete assessment templates
- Schedule and assign assessments to candidates
- View and evaluate results of candidate submissions
- Manage user roles and permissions

### Candidate Role:
Candidates are responsible for completing assessments. They can:
- Clone repositories assigned to them
- Modify the code to meet the requirements of the assessment
- Submit the completed code for evaluation


---

## 6. Terminology
### Assessments:
An **assessment** is a structured task or half build project that a candidate must complete. It typically involves working on a predefined project template, which is then evaluated using automated test cases. Assessments are created and scheduled by admins, and candidates are assigned assessments to complete.

### Assessment Groups:
**Assessment Groups** refer to a collection of related assessments grouped together for a particular purpose or cohort. This allows admins to organize and manage assessments based on themes, difficulty levels, or specific skill sets.

### Templates:
**Templates** are pre-existing, partially completed project repositories that candidates will use as the foundation for their assessments. Templates contain the basic structure and initial code, which candidates must complete or modify to fulfill the assessment requirements.

### Users:
**Users** in the DevGrade platform are classified into two main roles: admins and candidates. **Admins** are responsible for managing the platform, creating assessments, and overseeing the grading process. **Candidates** are the individuals who complete the assessments and submit their solutions for evaluation.

---

## 7. API Documentation
DevGrade provides a comprehensive REST API for interacting with the platform. The API is organized into several key endpoints:

### Authentication
- **POST /api/auth/login** - Authenticate user and get access token
- **POST /api/auth/register** - Register new user
- **POST /api/auth/refresh-token** - Refresh access token
- **POST /api/auth/logout** - Logout user

### Assessment Management
- **POST /api/assessments** - Create new assessment
- **GET /api/assessments** - List all assessments
- **GET /api/assessments/{id}** - Get assessment details
- **PUT /api/assessments/{id}** - Update assessment
- **DELETE /api/assessments/{id}** - Delete assessment
- **POST /api/assessments/{id}/start** - Start assessment
- **POST /api/assessments/{id}/submit** - Submit assessment
- **GET /api/assessments/{id}/results** - Get assessment results

### Template Management
- **POST /api/templates** - Create new template
- **GET /api/templates** - List all templates
- **GET /api/templates/{id}** - Get template details
- **PUT /api/templates/{id}** - Update template
- **DELETE /api/templates/{id}** - Delete template

### Repository Management
- **POST /api/repositories** - Create new repository
- **GET /api/repositories** - List all repositories
- **GET /api/repositories/{id}** - Get repository details
- **POST /api/repositories/{id}/clone** - Clone repository
- **POST /api/repositories/{id}/commit** - Commit changes to repository

### User Management
- **GET /api/users** - List all users
- **GET /api/users/{id}** - Get user details
- **PUT /api/users/{id}** - Update user
- **DELETE /api/users/{id}** - Delete user
- **PUT /api/users/{id}/role** - Update user role

### Test Execution
- **POST /api/tests/execute** - Execute test cases
- **GET /api/tests/results/{id}** - Get test execution results
- **POST /api/tests/feedback** - Submit test feedback

### Response Format
All API responses follow a standard format:
```json
{
  "success": boolean,
  "data": object | array | null,
  "error": {
    "code": string,
    "message": string
  } | null
}
```

### Authentication
All API requests require a valid JWT token in the Authorization header:
```
Authorization: Bearer <access_token>
```

### Rate Limiting
- API requests are limited to 100 requests per minute per user
- Rate limit headers are included in responses:
  - `X-RateLimit-Limit`: Maximum requests per minute
  - `X-RateLimit-Remaining`: Remaining requests in current window
  - `X-RateLimit-Reset`: Time when rate limit resets

For detailed API documentation, visit: [DevGrade API Documentation](https://devgrade-dev.github.io/DevGrade-API-Docs/)

---

## 8. Security and Authentication
DevGrade implements secure authentication and authorization through **AWS Amplify**. This service:
- Manages user sign-up and login securely
- Handles password reset and account recovery
- Assigns and validates user roles (admin or candidate)
- Enforces role-based access control to protect sensitive resources
- Provides JWT tokens for API authentication
