# FAQ Assistant API - Request/Response Examples

This document contains example requests and responses for all major API endpoints.

## Authentication Endpoints

### Register a New User

**Request:**
```http
POST /api/auth/register
Content-Type: application/json

{
  "username": "testuser",
  "email": "testuser@example.com",
  "fullName": "Test User",
  "password": "TestPass@123"
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "message": "User registered successfully",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": 4,
      "username": "testuser",
      "email": "testuser@example.com",
      "fullName": "Test User",
      "createdAt": "2025-01-01T10:00:00Z",
      "updatedAt": null
    },
    "expiresAt": "2025-01-01T11:00:00Z"
  },
  "errors": [],
  "timestamp": "2025-01-01T10:00:00Z"
}
```

### Login

**Request:**
```http
POST /api/auth/login
Content-Type: application/json

{
  "username": "admin",
  "password": "Admin@123"
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Login successful",
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "user": {
      "id": 1,
      "username": "admin",
      "email": "admin@faqassistant.com",
      "fullName": "System Administrator",
      "createdAt": "2025-01-01T08:00:00Z",
      "updatedAt": null
    },
    "expiresAt": "2025-01-01T11:00:00Z"
  },
  "errors": [],
  "timestamp": "2025-01-01T10:00:00Z"
}
```

## Category Endpoints

### Get All Categories

**Request:**
```http
GET /api/categories
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Categories retrieved successfully",
  "data": [
    {
      "id": 1,
      "name": "Account Setup",
      "description": "Questions related to account creation and setup",
      "iconName": "account-circle",
      "faqCount": 3,
      "createdAt": "2025-01-01T08:00:00Z",
      "updatedAt": null
    },
    {
      "id": 2,
      "name": "Billing",
      "description": "Questions about billing, payments, and invoices",
      "iconName": "payment",
      "faqCount": 4,
      "createdAt": "2025-01-01T08:00:00Z",
      "updatedAt": null
    }
  ],
  "errors": [],
  "timestamp": "2025-01-01T10:00:00Z"
}
```

### Create a Category

**Request:**
```http
POST /api/categories
Authorization: Bearer <your-jwt-token>
Content-Type: application/json

{
  "name": "Product Updates",
  "description": "Information about new features and updates",
  "iconName": "new-releases"
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "message": "Category created successfully",
  "data": {
    "id": 6,
    "name": "Product Updates",
    "description": "Information about new features and updates",
    "iconName": "new-releases",
    "faqCount": 0,
    "createdAt": "2025-01-01T10:15:00Z",
    "updatedAt": null
  },
  "errors": [],
  "timestamp": "2025-01-01T10:15:00Z"
}
```

## Tag Endpoints

### Get All Tags

**Request:**
```http
GET /api/tags
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Tags retrieved successfully",
  "data": [
    {
      "id": 1,
      "name": "Getting Started",
      "description": "Initial setup and onboarding",
      "faqCount": 2,
      "createdAt": "2025-01-01T08:00:00Z"
    },
    {
      "id": 2,
      "name": "Payment",
      "description": "Payment methods and processing",
      "faqCount": 3,
      "createdAt": "2025-01-01T08:00:00Z"
    }
  ],
  "errors": [],
  "timestamp": "2025-01-01T10:00:00Z"
}
```

### Create a Tag

**Request:**
```http
POST /api/tags
Authorization: Bearer <your-jwt-token>
Content-Type: application/json

{
  "name": "Tutorial",
  "description": "Step-by-step guides and tutorials"
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "message": "Tag created successfully",
  "data": {
    "id": 11,
    "name": "Tutorial",
    "description": "Step-by-step guides and tutorials",
    "faqCount": 0,
    "createdAt": "2025-01-01T10:20:00Z"
  },
  "errors": [],
  "timestamp": "2025-01-01T10:20:00Z"
}
```

## FAQ Endpoints

### Search FAQs with Filters

**Request:**
```http
GET /api/faqs/search?searchTerm=password&categoryId=1&pageNumber=1&pageSize=10&sortBy=ViewCount&sortOrder=desc
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "FAQs retrieved successfully",
  "data": {
    "items": [
      {
        "id": 3,
        "question": "How can I reset my password?",
        "answer": "If you've forgotten your password, click on the 'Forgot Password' link...",
        "categoryId": 1,
        "categoryName": "Account Setup",
        "tags": [
          {
            "id": 5,
            "name": "Login",
            "description": "Login and authentication",
            "faqCount": 3,
            "createdAt": "2025-01-01T08:00:00Z"
          },
          {
            "id": 6,
            "name": "Password",
            "description": "Password reset and recovery",
            "faqCount": 1,
            "createdAt": "2025-01-01T08:00:00Z"
          }
        ],
        "createdBy": {
          "id": 2,
          "username": "john.doe",
          "fullName": "John Doe"
        },
        "lastUpdatedBy": null,
        "createdAt": "2025-01-01T08:00:00Z",
        "updatedAt": null,
        "viewCount": 156,
        "isPublished": true
      }
    ],
    "totalCount": 1,
    "pageNumber": 1,
    "pageSize": 10,
    "totalPages": 1,
    "hasPrevious": false,
    "hasNext": false
  },
  "errors": [],
  "timestamp": "2025-01-01T10:00:00Z"
}
```

### Get a Specific FAQ

**Request:**
```http
GET /api/faqs/1
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "FAQ retrieved successfully",
  "data": {
    "id": 1,
    "question": "How do I create a new account?",
    "answer": "To create a new account, click on the 'Sign Up' button...",
    "categoryId": 1,
    "categoryName": "Account Setup",
    "tags": [
      {
        "id": 1,
        "name": "Getting Started",
        "description": "Initial setup and onboarding",
        "faqCount": 2,
        "createdAt": "2025-01-01T08:00:00Z"
      },
      {
        "id": 5,
        "name": "Login",
        "description": "Login and authentication",
        "faqCount": 3,
        "createdAt": "2025-01-01T08:00:00Z"
      }
    ],
    "createdBy": {
      "id": 1,
      "username": "admin",
      "fullName": "System Administrator"
    },
    "lastUpdatedBy": null,
    "createdAt": "2025-01-01T08:00:00Z",
    "updatedAt": null,
    "viewCount": 126,
    "isPublished": true
  },
  "errors": [],
  "timestamp": "2025-01-01T10:00:00Z"
}
```

### Create a New FAQ

**Request:**
```http
POST /api/faqs
Authorization: Bearer <your-jwt-token>
Content-Type: application/json

{
  "question": "How do I cancel my subscription?",
  "answer": "To cancel your subscription, go to Account Settings > Billing > Manage Subscription. Click on 'Cancel Subscription' and follow the prompts. You'll continue to have access until the end of your billing period.",
  "categoryId": 2,
  "tagIds": [2, 3],
  "isPublished": true
}
```

**Response (201 Created):**
```json
{
  "success": true,
  "message": "FAQ created successfully",
  "data": {
    "id": 9,
    "question": "How do I cancel my subscription?",
    "answer": "To cancel your subscription, go to Account Settings...",
    "categoryId": 2,
    "categoryName": "Billing",
    "tags": [
      {
        "id": 2,
        "name": "Payment",
        "description": "Payment methods and processing",
        "faqCount": 4,
        "createdAt": "2025-01-01T08:00:00Z"
      },
      {
        "id": 3,
        "name": "Invoice",
        "description": "Invoice generation and management",
        "faqCount": 2,
        "createdAt": "2025-01-01T08:00:00Z"
      }
    ],
    "createdBy": {
      "id": 4,
      "username": "testuser",
      "fullName": "Test User"
    },
    "lastUpdatedBy": null,
    "createdAt": "2025-01-01T10:30:00Z",
    "updatedAt": null,
    "viewCount": 0,
    "isPublished": true
  },
  "errors": [],
  "timestamp": "2025-01-01T10:30:00Z"
}
```

### Update an FAQ

**Request:**
```http
PUT /api/faqs/9
Authorization: Bearer <your-jwt-token>
Content-Type: application/json

{
  "answer": "To cancel your subscription, go to Account Settings > Billing > Manage Subscription. Click 'Cancel Subscription' and confirm. Note: You'll receive a prorated refund for any unused time.",
  "tagIds": [2, 3, 4]
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "FAQ updated successfully",
  "data": {
    "id": 9,
    "question": "How do I cancel my subscription?",
    "answer": "To cancel your subscription, go to Account Settings... Note: You'll receive a prorated refund...",
    "categoryId": 2,
    "categoryName": "Billing",
    "tags": [
      {
        "id": 2,
        "name": "Payment",
        "description": "Payment methods and processing",
        "faqCount": 4,
        "createdAt": "2025-01-01T08:00:00Z"
      },
      {
        "id": 3,
        "name": "Invoice",
        "description": "Invoice generation and management",
        "faqCount": 2,
        "createdAt": "2025-01-01T08:00:00Z"
      },
      {
        "id": 4,
        "name": "Refund",
        "description": "Refund policies and processes",
        "faqCount": 2,
        "createdAt": "2025-01-01T08:00:00Z"
      }
    ],
    "createdBy": {
      "id": 4,
      "username": "testuser",
      "fullName": "Test User"
    },
    "lastUpdatedBy": {
      "id": 4,
      "username": "testuser",
      "fullName": "Test User"
    },
    "createdAt": "2025-01-01T10:30:00Z",
    "updatedAt": "2025-01-01T10:35:00Z",
    "viewCount": 0,
    "isPublished": true
  },
  "errors": [],
  "timestamp": "2025-01-01T10:35:00Z"
}
```

### Delete an FAQ

**Request:**
```http
DELETE /api/faqs/9
Authorization: Bearer <your-jwt-token>
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "FAQ deleted successfully",
  "data": {},
  "errors": [],
  "timestamp": "2025-01-01T10:40:00Z"
}
```

## AI Endpoints

### Generate Answer Suggestion

**Request:**
```http
POST /api/ai/suggest-answer
Authorization: Bearer <your-jwt-token>
Content-Type: application/json

{
  "question": "What are your business hours?",
  "context": "We are a global company with support teams in multiple time zones including US, Europe, and Asia."
}
```

**Response (200 OK):**
```json
{
  "success": true,
  "message": "Answer suggestion generated successfully",
  "data": {
    "question": "What are your business hours?",
    "suggestedAnswer": "Our support is available 24/7 as we have teams distributed across multiple time zones including the United States, Europe, and Asia. This ensures that you can reach us at any time, day or night. For specific office hours by region:\n\n- US (PST): 6:00 AM - 6:00 PM\n- Europe (CET): 9:00 AM - 9:00 PM\n- Asia (IST): 9:00 AM - 9:00 PM\n\nOur chat support and email support are available around the clock. For phone support, please check our website for region-specific availability.",
    "provider": "Groq",
    "model": "llama3-70b-8192",
    "generatedAt": "2025-01-01T10:45:00Z",
    "confidence": null
  },
  "errors": [],
  "timestamp": "2025-01-01T10:45:00Z"
}
```

## Error Responses

### Validation Error (400 Bad Request)

**Request:**
```http
POST /api/faqs
Authorization: Bearer <your-jwt-token>
Content-Type: application/json

{
  "question": "",
  "answer": "Short",
  "categoryId": 0
}
```

**Response (400 Bad Request):**
```json
{
  "success": false,
  "message": "Validation failed",
  "data": null,
  "errors": [
    "Question is required",
    "Answer must be at least 10 characters",
    "Valid Category ID is required"
  ],
  "timestamp": "2025-01-01T10:50:00Z"
}
```

### Unauthorized (401)

**Request:**
```http
POST /api/faqs
Content-Type: application/json

{
  "question": "Test question",
  "answer": "Test answer",
  "categoryId": 1
}
```

**Response (401 Unauthorized):**
```json
{
  "success": false,
  "message": "Unauthorized access",
  "data": null,
  "errors": [],
  "timestamp": "2025-01-01T10:55:00Z"
}
```

### Not Found (404)

**Request:**
```http
GET /api/faqs/9999
```

**Response (404 Not Found):**
```json
{
  "success": false,
  "message": "FAQ not found",
  "data": null,
  "errors": [],
  "timestamp": "2025-01-01T11:00:00Z"
}
```

### Rate Limit Exceeded (429)

**Request:**
```http
POST /api/ai/suggest-answer
Authorization: Bearer <your-jwt-token>
Content-Type: application/json

{
  "question": "Test question"
}
```

**Response (429 Too Many Requests):**
```json
{
  "message": "API calls quota exceeded! Maximum allowed: 5 per 1m."
}
```

## Testing Workflow

1. **Register and Login**
   ```
   POST /api/auth/register
   POST /api/auth/login
   ```

2. **Explore existing data**
   ```
   GET /api/categories
   GET /api/tags
   GET /api/faqs/search
   ```

3. **Create new content**
   ```
   POST /api/categories
   POST /api/tags
   POST /api/faqs
   ```

4. **Try AI features**
   ```
   POST /api/ai/suggest-answer
   ```

5. **Update and manage**
   ```
   PUT /api/faqs/{id}
   DELETE /api/faqs/{id}
   ```

## Notes

- All timestamps are in UTC
- JWT tokens expire after 60 minutes (configurable)
- AI endpoint is rate-limited to 5 requests per minute
- Pagination defaults: pageNumber=1, pageSize=10
- Soft deletes are used for FAQs and Categories (IsActive flag)
