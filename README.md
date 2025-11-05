# FAQ Assistant API

A comprehensive REST API backend for a FAQ (Frequently Asked Questions) management system with AI-powered answer suggestions. Built with ASP.NET Core 8.0 and SQL Server.

## Table of Contents

- [Features](#features)
- [Technology Stack](#technology-stack)
- [Database Design](#database-design)
- [Getting Started](#getting-started)
- [Configuration](#configuration)
- [Running the Application](#running-the-application)
- [API Documentation](#api-documentation)
- [Testing](#testing)
- [Project Structure](#project-structure)
- [Contributing](#contributing)

## Features

### Core Features
- **FAQ Management**: Full CRUD operations for FAQs with category and tag associations
- **Category Management**: Organize FAQs into logical categories
- **Tag System**: Flexible tagging system for enhanced searchability
- **Advanced Search**: Search and filter FAQs by keywords, categories, and tags with pagination
- **User Management**: User authentication and authorization

### Bonus Features
- **AI-Powered Answer Suggestions**: Generate draft answers using LLM (via Groq API)
- **JWT Authentication**: Secure token-based authentication
- **Rate Limiting**: Protect AI endpoints with configurable rate limits
- **Global Error Handling**: Consistent error responses across all endpoints
- **Input Validation**: FluentValidation for robust request validation
- **API Documentation**: Interactive Swagger/OpenAPI documentation

## Technology Stack

- **Framework**: ASP.NET Core 8.0 Web API
- **Database**: SQL Server (LocalDB for development)
- **ORM**: Entity Framework Core 9.0
- **Authentication**: JWT Bearer tokens
- **Validation**: FluentValidation
- **Rate Limiting**: AspNetCoreRateLimit
- **AI Integration**: Groq API (LLama 3 70B model)
- **Documentation**: Swagger/OpenAPI

## Database Design

### Entity Relationship Diagram

The database follows 3NF normalization with the following entities:

#### Core Entities

1. **User**
   - Id (PK)
   - Username (Unique)
   - Email (Unique)
   - FullName
   - PasswordHash
   - CreatedAt, UpdatedAt
   - IsActive

2. **Category**
   - Id (PK)
   - Name (Unique)
   - Description
   - IconName
   - CreatedAt, UpdatedAt
   - IsActive

3. **Tag**
   - Id (PK)
   - Name (Unique)
   - Description
   - CreatedAt

4. **FAQ**
   - Id (PK)
   - Question
   - Answer
   - CategoryId (FK → Category)
   - CreatedByUserId (FK → User)
   - LastUpdatedByUserId (FK → User)
   - CreatedAt, UpdatedAt
   - ViewCount
   - IsActive
   - IsPublished

5. **FAQTag** (Junction Table)
   - Id (PK)
   - FAQId (FK → FAQ)
   - TagId (FK → Tag)
   - CreatedAt

### Relationships

- **Category → FAQ**: One-to-Many (One category can have multiple FAQs)
- **FAQ ↔ Tag**: Many-to-Many (via FAQTag junction table)
- **User → FAQ**: One-to-Many (One user can create/update multiple FAQs)

## Getting Started

### Prerequisites

- [.NET 8.0 SDK](https://dotnet.microsoft.com/download/dotnet/8.0)
- [SQL Server](https://www.microsoft.com/en-us/sql-server/sql-server-downloads) or SQL Server LocalDB
- [Groq API Key](https://console.groq.com/) (for AI features)
- [Git](https://git-scm.com/)

### Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd FAQproblem
   ```

2. **Restore NuGet packages**
   ```bash
   cd FAQproblem
   dotnet restore
   ```

3. **Update database connection string**

   Edit `appsettings.json` and update the connection string if needed:
   ```json
   "ConnectionStrings": {
     "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=FAQAssistantDb;Trusted_Connection=True;MultipleActiveResultSets=true;TrustServerCertificate=True"
   }
   ```

4. **Configure AI API Key**

   In `appsettings.json`, add your Groq API key:
   ```json
   "AISettings": {
     "ApiKey": "YOUR_GROQ_API_KEY_HERE"
   }
   ```

   Get your free API key from: https://console.groq.com/

5. **Apply database migrations**
   ```bash
   dotnet ef database update
   ```

6. **Build the project**
   ```bash
   dotnet build
   ```

## Configuration

### JWT Settings

Configure JWT authentication in `appsettings.json`:

```json
"JwtSettings": {
  "SecretKey": "YourSuperSecretKeyForJWTTokenGeneration",
  "Issuer": "FAQAssistantAPI",
  "Audience": "FAQAssistantClient",
  "ExpirationInMinutes": 60
}
```

### Rate Limiting

Configure rate limits for API endpoints:

```json
"IpRateLimiting": {
  "EnableEndpointRateLimiting": true,
  "GeneralRules": [
    {
      "Endpoint": "*:/api/ai/*",
      "Period": "1m",
      "Limit": 5
    }
  ]
}
```

## Running the Application

### Development Mode

```bash
cd FAQproblem
dotnet run
```

The API will be available at:
- HTTPS: `https://localhost:5001`
- HTTP: `http://localhost:5000`
- Swagger UI: `https://localhost:5001` (root path)

### Production Build

```bash
dotnet publish -c Release -o ./publish
```

## API Documentation

### Base URL
`https://localhost:5001/api`

### Authentication

Most write operations require authentication. To authenticate:

1. **Register a new user**
   ```http
   POST /api/auth/register
   Content-Type: application/json

   {
     "username": "johndoe",
     "email": "john@example.com",
     "fullName": "John Doe",
     "password": "Password@123"
   }
   ```

2. **Login**
   ```http
   POST /api/auth/login
   Content-Type: application/json

   {
     "username": "johndoe",
     "password": "Password@123"
   }
   ```

3. **Use the JWT token**
   Include the token in the Authorization header:
   ```
   Authorization: Bearer <your-jwt-token>
   ```

### Sample Credentials (Seeded Data)

```
Username: admin
Password: Admin@123

Username: john.doe
Password: Password@123
```

### Key Endpoints

#### FAQs
- `GET /api/faqs/search` - Search and filter FAQs (supports pagination, sorting, filtering)
- `GET /api/faqs/{id}` - Get a specific FAQ by ID
- `GET /api/faqs/category/{categoryId}` - Get FAQs by category
- `GET /api/faqs/tag/{tagId}` - Get FAQs by tag
- `POST /api/faqs` - Create a new FAQ (requires authentication)
- `PUT /api/faqs/{id}` - Update an existing FAQ (requires authentication)
- `DELETE /api/faqs/{id}` - Delete a FAQ (requires authentication)

#### Categories
- `GET /api/categories` - Get all categories
- `GET /api/categories/{id}` - Get a specific category
- `POST /api/categories` - Create a new category (requires authentication)
- `PUT /api/categories/{id}` - Update a category (requires authentication)
- `DELETE /api/categories/{id}` - Delete a category (requires authentication)

#### Tags
- `GET /api/tags` - Get all tags
- `GET /api/tags/{id}` - Get a specific tag
- `POST /api/tags` - Create a new tag (requires authentication)
- `PUT /api/tags/{id}` - Update a tag (requires authentication)
- `DELETE /api/tags/{id}` - Delete a tag (requires authentication)

#### AI Features
- `POST /api/ai/suggest-answer` - Generate AI-powered answer suggestions (requires authentication, rate limited)

### Example Requests

#### Search FAQs
```http
GET /api/faqs/search?searchTerm=password&pageNumber=1&pageSize=10&sortBy=CreatedAt&sortOrder=desc
```

#### Create a new FAQ
```http
POST /api/faqs
Authorization: Bearer <token>
Content-Type: application/json

{
  "question": "How do I export my data?",
  "answer": "You can export your data from the Settings page...",
  "categoryId": 1,
  "tagIds": [1, 3],
  "isPublished": true
}
```

#### AI Answer Suggestion
```http
POST /api/ai/suggest-answer
Authorization: Bearer <token>
Content-Type: application/json

{
  "question": "What are your business hours?",
  "context": "We are a global company with offices in multiple time zones"
}
```

### API Response Format

All API responses follow a consistent format:

**Success Response:**
```json
{
  "success": true,
  "message": "Success message",
  "data": { ... },
  "errors": [],
  "timestamp": "2025-01-01T00:00:00Z"
}
```

**Error Response:**
```json
{
  "success": false,
  "message": "Error message",
  "data": null,
  "errors": ["Error detail 1", "Error detail 2"],
  "timestamp": "2025-01-01T00:00:00Z"
}
```

## Testing

### Swagger UI

The easiest way to test the API is through the built-in Swagger UI:

1. Run the application
2. Navigate to `https://localhost:5001`
3. Use the interactive documentation to test endpoints

### Sample Test Flow

1. Register a new user via `/api/auth/register`
2. Login via `/api/auth/login` to get a JWT token
3. Click "Authorize" in Swagger UI and enter: `Bearer <your-token>`
4. Create a new category via `/api/categories`
5. Create a new tag via `/api/tags`
6. Create a new FAQ via `/api/faqs`
7. Search for FAQs via `/api/faqs/search`
8. Try AI answer suggestion via `/api/ai/suggest-answer`

## Project Structure

```
FAQproblem/
├── Controllers/          # API Controllers
│   ├── FAQsController.cs
│   ├── CategoriesController.cs
│   ├── TagsController.cs
│   ├── AuthController.cs
│   └── AIController.cs
├── Data/                 # Database Context and Seeder
│   ├── ApplicationDbContext.cs
│   └── DbSeeder.cs
├── DTOs/                 # Data Transfer Objects
│   ├── FAQDto.cs
│   ├── CategoryDto.cs
│   ├── TagDto.cs
│   ├── UserDto.cs
│   ├── AIDto.cs
│   └── ApiResponse.cs
├── Middleware/           # Custom Middleware
│   └── GlobalExceptionMiddleware.cs
├── Models/               # Entity Models
│   ├── FAQ.cs
│   ├── Category.cs
│   ├── Tag.cs
│   ├── User.cs
│   └── FAQTag.cs
├── Repositories/         # Repository Pattern
│   ├── IRepository.cs
│   ├── Repository.cs
│   ├── IFAQRepository.cs
│   ├── FAQRepository.cs
│   └── ...
├── Services/             # Business Logic Layer
│   ├── IFAQService.cs
│   ├── FAQService.cs
│   ├── IAIService.cs
│   ├── AIService.cs
│   └── ...
├── Validators/           # FluentValidation Validators
│   ├── FAQValidators.cs
│   ├── CategoryValidators.cs
│   └── ...
├── Migrations/           # EF Core Migrations
├── appsettings.json      # Configuration
└── Program.cs            # Application Entry Point

DatabaseScripts/
└── InitialSchema.sql     # SQL Script for database creation
```

## Database Scripts

SQL scripts for creating the database schema are available in the `DatabaseScripts` folder:

- `InitialSchema.sql` - Complete database schema with all tables, indexes, and foreign keys

To run the script manually:
```sql
sqlcmd -S (localdb)\mssqllocaldb -i DatabaseScripts/InitialSchema.sql
```

## Design Principles

### Architecture
- **Repository Pattern**: Separates data access logic
- **Service Layer**: Encapsulates business logic
- **Dependency Injection**: Loose coupling and testability
- **DTO Pattern**: Decouples API contracts from domain models

### Code Quality
- **SOLID Principles**: Maintainable and extensible code
- **Separation of Concerns**: Clear boundaries between layers
- **Input Validation**: Comprehensive validation with FluentValidation
- **Error Handling**: Global exception middleware for consistent error responses

### API Design
- **RESTful Conventions**: Standard HTTP methods and status codes
- **Pagination**: Efficient data retrieval for large datasets
- **Filtering & Sorting**: Flexible query capabilities
- **Versioning-Ready**: Structure supports future API versioning

## Security Features

- JWT-based authentication
- Password hashing (SHA-256)
- Input validation and sanitization
- SQL injection protection (via EF Core parameterized queries)
- Rate limiting on sensitive endpoints
- CORS configuration

## Troubleshooting

### Database Connection Issues

If you encounter database connection issues:

1. Verify SQL Server/LocalDB is running
2. Check the connection string in `appsettings.json`
3. Ensure the database has been created:
   ```bash
   dotnet ef database update
   ```

### AI Service Issues

If AI answer suggestions fail:

1. Verify your Groq API key in `appsettings.json`
2. Check your internet connection
3. Verify rate limits haven't been exceeded
4. Check the application logs for detailed error messages

### Migration Issues

To reset the database:
```bash
dotnet ef database drop
dotnet ef database update
```

## Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Submit a pull request

## License

This project is licensed under the MIT License.

## Support

For issues and questions:
- Create an issue in the repository
- Check the Swagger documentation
- Review the code documentation

## Acknowledgments

- Built with ASP.NET Core
- AI powered by Groq (LLama 3)
- Database design follows best practices from Microsoft EF Core documentation
