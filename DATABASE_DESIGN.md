# FAQ Assistant Database Design

## Database Schema Overview

The FAQ Assistant uses a normalized relational database design (3NF+) with proper foreign key constraints and indexes for optimal performance.

## Entity-Relationship Diagram (ERD)

```
┌─────────────────┐
│     User        │
├─────────────────┤
│ Id (PK)         │
│ Username (UQ)   │
│ Email (UQ)      │
│ FullName        │
│ PasswordHash    │
│ CreatedAt       │
│ UpdatedAt       │
│ IsActive        │
└─────────────────┘
        │
        │ 1
        │
        │ *
┌─────────────────┐         ┌─────────────────┐
│      FAQ        │    *    │    Category     │
├─────────────────┤─────────├─────────────────┤
│ Id (PK)         │    1    │ Id (PK)         │
│ Question        │         │ Name (UQ)       │
│ Answer          │         │ Description     │
│ CategoryId (FK) │◄────────│ IconName        │
│ CreatedByUserId │         │ CreatedAt       │
│ LastUpdatedById │         │ UpdatedAt       │
│ CreatedAt       │         │ IsActive        │
│ UpdatedAt       │         └─────────────────┘
│ ViewCount       │
│ IsActive        │
│ IsPublished     │
└─────────────────┘
        │
        │ *
        │
        │ *
┌─────────────────┐
│    FAQTag       │
├─────────────────┤
│ Id (PK)         │
│ FAQId (FK)      │
│ TagId (FK)      │
│ CreatedAt       │
└─────────────────┘
        │
        │ *
        │
        │ 1
┌─────────────────┐
│      Tag        │
├─────────────────┤
│ Id (PK)         │
│ Name (UQ)       │
│ Description     │
│ CreatedAt       │
└─────────────────┘
```

## Tables

### Users Table
Stores user account information for authentication and audit trails.

| Column Name      | Data Type       | Constraints                    | Description                      |
|-----------------|-----------------|--------------------------------|----------------------------------|
| Id              | INT             | PRIMARY KEY, IDENTITY          | Unique user identifier           |
| Username        | NVARCHAR(100)   | NOT NULL, UNIQUE               | User's login username            |
| Email           | NVARCHAR(255)   | NOT NULL, UNIQUE               | User's email address             |
| FullName        | NVARCHAR(100)   | NOT NULL                       | User's full name                 |
| PasswordHash    | NVARCHAR(MAX)   | NOT NULL                       | Hashed password (SHA-256)        |
| CreatedAt       | DATETIME2       | NOT NULL, DEFAULT GETUTCDATE() | Account creation timestamp       |
| UpdatedAt       | DATETIME2       | NULL                           | Last update timestamp            |
| IsActive        | BIT             | NOT NULL                       | Account active status            |

**Indexes:**
- Unique index on Username
- Unique index on Email

---

### Categories Table
Organizes FAQs into logical groups.

| Column Name      | Data Type       | Constraints                    | Description                      |
|-----------------|-----------------|--------------------------------|----------------------------------|
| Id              | INT             | PRIMARY KEY, IDENTITY          | Unique category identifier       |
| Name            | NVARCHAR(100)   | NOT NULL, UNIQUE               | Category name                    |
| Description     | NVARCHAR(500)   | NULL                           | Category description             |
| IconName        | NVARCHAR(50)    | NULL                           | Icon identifier for UI           |
| CreatedAt       | DATETIME2       | NOT NULL, DEFAULT GETUTCDATE() | Creation timestamp               |
| UpdatedAt       | DATETIME2       | NULL                           | Last update timestamp            |
| IsActive        | BIT             | NOT NULL                       | Category active status           |

**Indexes:**
- Unique index on Name

---

### Tags Table
Provides keyword-based classification for FAQs.

| Column Name      | Data Type       | Constraints                    | Description                      |
|-----------------|-----------------|--------------------------------|----------------------------------|
| Id              | INT             | PRIMARY KEY, IDENTITY          | Unique tag identifier            |
| Name            | NVARCHAR(50)    | NOT NULL, UNIQUE               | Tag name                         |
| Description     | NVARCHAR(255)   | NULL                           | Tag description                  |
| CreatedAt       | DATETIME2       | NOT NULL, DEFAULT GETUTCDATE() | Creation timestamp               |

**Indexes:**
- Unique index on Name

---

### FAQs Table
Stores frequently asked questions and their answers.

| Column Name         | Data Type       | Constraints                    | Description                      |
|--------------------|-----------------|--------------------------------|----------------------------------|
| Id                 | INT             | PRIMARY KEY, IDENTITY          | Unique FAQ identifier            |
| Question           | NVARCHAR(500)   | NOT NULL                       | The question text                |
| Answer             | NVARCHAR(MAX)   | NOT NULL                       | The answer text                  |
| CategoryId         | INT             | NOT NULL, FK → Categories.Id   | Associated category              |
| CreatedByUserId    | INT             | NOT NULL, FK → Users.Id        | User who created the FAQ         |
| LastUpdatedByUserId| INT             | NULL, FK → Users.Id            | User who last updated            |
| CreatedAt          | DATETIME2       | NOT NULL, DEFAULT GETUTCDATE() | Creation timestamp               |
| UpdatedAt          | DATETIME2       | NULL                           | Last update timestamp            |
| ViewCount          | INT             | NOT NULL, DEFAULT 0            | Number of times viewed           |
| IsActive           | BIT             | NOT NULL                       | FAQ active status                |
| IsPublished        | BIT             | NOT NULL                       | Publication status               |

**Foreign Keys:**
- FK_FAQs_Categories (CategoryId) → Categories.Id (RESTRICT)
- FK_FAQs_Users_CreatedBy (CreatedByUserId) → Users.Id (RESTRICT)
- FK_FAQs_Users_UpdatedBy (LastUpdatedByUserId) → Users.Id (RESTRICT)

**Indexes:**
- Index on CategoryId
- Index on CreatedByUserId
- Index on LastUpdatedByUserId

---

### FAQTags Table (Junction Table)
Implements many-to-many relationship between FAQs and Tags.

| Column Name      | Data Type       | Constraints                    | Description                      |
|-----------------|-----------------|--------------------------------|----------------------------------|
| Id              | INT             | PRIMARY KEY, IDENTITY          | Unique relationship identifier   |
| FAQId           | INT             | NOT NULL, FK → FAQs.Id         | Associated FAQ                   |
| TagId           | INT             | NOT NULL, FK → Tags.Id         | Associated tag                   |
| CreatedAt       | DATETIME2       | NOT NULL, DEFAULT GETUTCDATE() | Association timestamp            |

**Foreign Keys:**
- FK_FAQTags_FAQs (FAQId) → FAQs.Id (CASCADE)
- FK_FAQTags_Tags (TagId) → Tags.Id (CASCADE)

**Indexes:**
- Unique composite index on (FAQId, TagId)
- Index on TagId

## Relationships

### One-to-Many Relationships

1. **Category → FAQ**
   - One category can have multiple FAQs
   - Each FAQ must belong to exactly one category
   - Delete behavior: RESTRICT (cannot delete category with existing FAQs)

2. **User → FAQ (Author)**
   - One user can create multiple FAQs
   - Each FAQ must have one creator
   - Delete behavior: RESTRICT (preserves audit trail)

3. **User → FAQ (Last Updater)**
   - One user can update multiple FAQs
   - Each FAQ may have zero or one last updater
   - Delete behavior: RESTRICT (preserves audit trail)

### Many-to-Many Relationships

1. **FAQ ↔ Tag**
   - One FAQ can have multiple tags
   - One tag can be associated with multiple FAQs
   - Implemented via FAQTag junction table
   - Delete behavior: CASCADE (removing FAQ/Tag removes associations)

## Normalization

### First Normal Form (1NF)
✓ All attributes contain atomic values
✓ Each column has a single value per row
✓ Each row is unique (enforced by primary keys)

### Second Normal Form (2NF)
✓ Meets 1NF requirements
✓ All non-key attributes are fully dependent on the primary key
✓ No partial dependencies exist

### Third Normal Form (3NF)
✓ Meets 2NF requirements
✓ No transitive dependencies
✓ All non-key attributes depend only on the primary key

### Additional Normalization
- Junction table (FAQTag) eliminates many-to-many redundancy
- Separate audit fields (CreatedByUserId, LastUpdatedByUserId) maintain data integrity
- Soft deletes (IsActive flag) preserve data history

## Constraints and Integrity

### Primary Keys
- All tables have surrogate primary keys (auto-incrementing integers)
- Provides efficient joins and indexing

### Foreign Keys
- All relationships enforced with foreign key constraints
- RESTRICT behavior for audit trail preservation
- CASCADE behavior for junction table cleanup

### Unique Constraints
- Username and Email in Users table
- Category Name
- Tag Name
- Composite unique constraint on (FAQId, TagId) in FAQTag

### Check Constraints (Application Level)
- Email format validation via FluentValidation
- Password strength requirements via FluentValidation
- Question and Answer minimum/maximum lengths

### Default Values
- CreatedAt fields default to GETUTCDATE()
- ViewCount defaults to 0
- IsActive/IsPublished default to true

## Indexes

### Clustered Indexes
- Primary key on each table (default)

### Non-Clustered Indexes
1. **Users.Username** - Unique, for login lookups
2. **Users.Email** - Unique, for email lookups
3. **Categories.Name** - Unique, for category lookups
4. **Tags.Name** - Unique, for tag lookups
5. **FAQs.CategoryId** - For category-based queries
6. **FAQs.CreatedByUserId** - For user-created FAQ queries
7. **FAQs.LastUpdatedByUserId** - For update tracking
8. **FAQTags.FAQId + TagId** - Unique composite, for relationship queries
9. **FAQTags.TagId** - For tag-based FAQ queries

## Performance Optimizations

1. **Efficient Queries**
   - Indexes on foreign keys for fast joins
   - Composite index on FAQTag for quick many-to-many lookups

2. **Data Integrity**
   - Foreign key constraints prevent orphaned records
   - Unique constraints prevent duplicate entries

3. **Audit Trail**
   - Soft deletes preserve historical data
   - Created/Updated timestamps for tracking

4. **Scalability**
   - Normalized structure reduces data redundancy
   - Efficient indexing supports large datasets
   - Junction table allows unlimited tag associations

## Sample Queries

### Get all FAQs with Category and Tags
```sql
SELECT
    f.Id, f.Question, f.Answer,
    c.Name AS CategoryName,
    STRING_AGG(t.Name, ', ') AS Tags
FROM FAQs f
INNER JOIN Categories c ON f.CategoryId = c.Id
LEFT JOIN FAQTags ft ON f.Id = ft.FAQId
LEFT JOIN Tags t ON ft.TagId = t.Id
WHERE f.IsActive = 1
GROUP BY f.Id, f.Question, f.Answer, c.Name
ORDER BY f.CreatedAt DESC;
```

### Get FAQs by Tag
```sql
SELECT f.*
FROM FAQs f
INNER JOIN FAQTags ft ON f.Id = ft.FAQId
WHERE ft.TagId = @TagId AND f.IsActive = 1;
```

### Get Category with FAQ Count
```sql
SELECT
    c.Id, c.Name, c.Description,
    COUNT(f.Id) AS FAQCount
FROM Categories c
LEFT JOIN FAQs f ON c.Id = f.CategoryId AND f.IsActive = 1
WHERE c.IsActive = 1
GROUP BY c.Id, c.Name, c.Description;
```

### Search FAQs by Text
```sql
SELECT f.*
FROM FAQs f
WHERE f.IsActive = 1
  AND (f.Question LIKE '%' + @SearchTerm + '%'
   OR f.Answer LIKE '%' + @SearchTerm + '%')
ORDER BY f.ViewCount DESC;
```

## Migration Strategy

1. **Initial Migration**
   - Create all tables with proper schema
   - Add indexes and constraints
   - Seed initial data

2. **Future Migrations**
   - Use EF Core migrations for schema changes
   - Always include rollback strategy
   - Test migrations in development first

## Data Retention

- **Active Records**: Normal operations
- **Soft Deleted Records**: Retained indefinitely (IsActive = false)
- **Hard Deletes**: Only for Tags (no audit requirement)
- **Audit Trail**: CreatedAt/UpdatedAt timestamps preserved

## Security Considerations

1. **Password Storage**
   - Passwords hashed using SHA-256
   - Original passwords never stored

2. **SQL Injection Prevention**
   - EF Core parameterized queries
   - No dynamic SQL

3. **Data Access**
   - Foreign key constraints enforce referential integrity
   - Application-level authorization controls access

## Backup Recommendations

1. **Full Backups**: Daily
2. **Differential Backups**: Every 6 hours
3. **Transaction Log Backups**: Every hour
4. **Retention Period**: 30 days minimum
