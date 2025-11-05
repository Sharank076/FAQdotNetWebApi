# FAQ Assistant API - Quick Start Guide

## 🚀 Get Started in 5 Minutes

### Prerequisites
- .NET 8.0 SDK installed
- SQL Server or SQL Server LocalDB

### Step 1: Database Setup
```bash
cd FAQproblem
dotnet ef database update
```

This creates the database and seeds sample data.

### Step 2: Configure AI API (Optional)
Edit `appsettings.json`:
```json
"AISettings": {
  "ApiKey": "YOUR_GROQ_API_KEY_HERE"
}
```
Get a free API key at: https://console.groq.com/

### Step 3: Run the Application
```bash
dotnet run
```

### Step 4: Open Swagger UI
Navigate to: **https://localhost:5001**

## 🔐 Sample Login Credentials

```
Username: admin
Password: Admin@123
```

Or:
```
Username: john.doe
Password: Password@123
```

## 📝 Quick Test Flow

### 1. Get Sample Data (No Auth Required)
```http
GET https://localhost:5001/api/categories
GET https://localhost:5001/api/tags
GET https://localhost:5001/api/faqs/search?pageNumber=1&pageSize=10
```

### 2. Login to Get Token
```http
POST https://localhost:5001/api/auth/login
Content-Type: application/json

{
  "username": "admin",
  "password": "Admin@123"
}
```

Copy the `token` from the response.

### 3. Authorize in Swagger
1. Click the **"Authorize"** button in Swagger UI
2. Enter: `Bearer YOUR_TOKEN_HERE`
3. Click "Authorize"

### 4. Create a New FAQ
```http
POST https://localhost:5001/api/faqs
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{
  "question": "How do I get started?",
  "answer": "Follow the quick start guide to get up and running in minutes!",
  "categoryId": 1,
  "tagIds": [1],
  "isPublished": true
}
```

### 5. Try AI Answer Suggestion (Requires API Key)
```http
POST https://localhost:5001/api/ai/suggest-answer
Authorization: Bearer YOUR_TOKEN
Content-Type: application/json

{
  "question": "What payment methods do you accept?",
  "context": "We are an e-commerce platform"
}
```

## 📚 Key Endpoints

| Endpoint | Method | Auth Required | Description |
|----------|--------|---------------|-------------|
| `/api/auth/register` | POST | No | Register new user |
| `/api/auth/login` | POST | No | Login and get JWT token |
| `/api/categories` | GET | No | Get all categories |
| `/api/tags` | GET | No | Get all tags |
| `/api/faqs/search` | GET | No | Search FAQs with filters |
| `/api/faqs` | POST | Yes | Create new FAQ |
| `/api/faqs/{id}` | PUT | Yes | Update FAQ |
| `/api/faqs/{id}` | DELETE | Yes | Delete FAQ |
| `/api/ai/suggest-answer` | POST | Yes | AI answer generation |

## 🎯 Common Use Cases

### Search FAQs by Keyword
```
GET /api/faqs/search?searchTerm=password&pageSize=5
```

### Get FAQs by Category
```
GET /api/faqs/category/1
```

### Get FAQs by Tag
```
GET /api/faqs/tag/1
```

### Filter with Multiple Criteria
```
GET /api/faqs/search?searchTerm=billing&categoryId=2&pageNumber=1&pageSize=10&sortBy=ViewCount&sortOrder=desc
```

## ⚙️ Configuration Quick Reference

### Database Connection
```json
"ConnectionStrings": {
  "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=FAQAssistantDb;Trusted_Connection=True;MultipleActiveResultSets=true;TrustServerCertificate=True"
}
```

### JWT Settings
```json
"JwtSettings": {
  "SecretKey": "Your-Secret-Key-Here",
  "Issuer": "FAQAssistantAPI",
  "Audience": "FAQAssistantClient",
  "ExpirationInMinutes": 60
}
```

### Rate Limiting
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

## 🐛 Troubleshooting

### Database Connection Failed
```bash
# Verify LocalDB is running
sqllocaldb info

# Start LocalDB if needed
sqllocaldb start mssqllocaldb

# Drop and recreate database
dotnet ef database drop
dotnet ef database update
```

### Build Errors
```bash
# Clean and rebuild
dotnet clean
dotnet restore
dotnet build
```

### Port Already in Use
Edit `Properties/launchSettings.json` to change ports:
```json
"applicationUrl": "https://localhost:5001;http://localhost:5000"
```

## 📖 Additional Resources

- **Full Documentation**: See README.md
- **API Examples**: See API_EXAMPLES.md
- **Database Design**: See DATABASE_DESIGN.md
- **Project Summary**: See PROJECT_SUMMARY.md

## 💡 Pro Tips

1. **Use Swagger UI** for easy testing - it's available at the root URL
2. **JWT tokens expire after 60 minutes** - re-login if you get 401 errors
3. **AI endpoint is rate limited** - max 5 requests per minute
4. **Sample data is seeded automatically** on first run
5. **Check logs** in the console for detailed error messages

## 🎓 Next Steps

1. Explore the seeded data via Swagger
2. Try all CRUD operations
3. Test the search and filtering capabilities
4. Experiment with AI answer suggestions
5. Review the code structure and architecture

## ✅ Verify Installation

Run these commands to verify everything is working:

```bash
# Check .NET version
dotnet --version
# Should show 8.0.x or higher

# Build the project
cd FAQproblem
dotnet build
# Should complete with 0 errors

# Check database
dotnet ef database update
# Should show "Done" or "Already applied"

# Run the application
dotnet run
# Should start without errors
```

## 📞 Need Help?

- Check the comprehensive README.md
- Review API_EXAMPLES.md for request/response examples
- Consult DATABASE_DESIGN.md for schema details
- Use Swagger UI for interactive API documentation

---

**Happy Coding! 🎉**
