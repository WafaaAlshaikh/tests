# 📊 Invoice Tracker System

A comprehensive Spring Boot application for managing and tracking invoices with AI-powered features including invoice data extraction from images/PDFs and an intelligent chatbot assistant.

## 🌟 Features

### Core Functionality
- **Multi-format Invoice Upload**: Support for PDF, Images (JPG, PNG), and Web Form entry
- **AI-Powered Extraction**: Automatic invoice data extraction using Google Gemini Vision API
- **Intelligent Chatbot**: Ask questions about your invoices using natural language
- **Role-Based Access Control**: Three user roles (USER, SUPERUSER, AUDITOR) with different permissions
- **Comprehensive Audit Trail**: Track all changes and actions on invoices
- **Advanced Search**: Filter invoices by date, status, file type, and more
- **File Management**: Secure file storage and retrieval system

### Technical Highlights
- RESTful API architecture
- JWT-based authentication
- Pagination support (10 items per page)
- Soft delete functionality
- Exception handling with custom error responses
- OpenAPI/Swagger documentation
- Design patterns implementation (Factory, Strategy, Repository, Builder)

---

## 🏗️ Architecture

### Design Patterns Used

#### 1. **Factory Pattern**
```
InvoiceFactory
├── FileInvoiceCreator (PDF/Image invoices)
├── FormInvoiceCreator (Web form invoices)
└── HybridInvoiceCreator (File + Products)
```

#### 2. **Strategy Pattern**
```
FileProcessingStrategy
├── ImageProcessingStrategy
└── PdfProcessingStrategy
```

#### 3. **Repository Pattern**
```
JPA Repositories for data access abstraction
```

#### 4. **Builder Pattern**
```
DTOs and Entities using Lombok @Builder
```

### Layered Architecture
```
┌─────────────────────────────────────┐
│         Controllers                 │  ← REST endpoints
├─────────────────────────────────────┤
│         Services                    │  ← Business logic
├─────────────────────────────────────┤
│         Repositories                │  ← Data access
├─────────────────────────────────────┤
│         Database (PostgreSQL)       │  ← Persistence
└─────────────────────────────────────┘
```

---

## 🚀 Getting Started

### Prerequisites

- **Java**: JDK 17 or higher
- **Maven**: 3.8+
- **PostgreSQL**: 14+
- **Google Gemini API Key**: [Get it here](https://makersuite.google.com/app/apikey)

### Installation

#### 1. Clone the Repository
```bash
git clone https://github.com/your-username/invoice-tracker-system.git
cd invoice-tracker-system
```

#### 2. Configure Database

Create a PostgreSQL database:
```sql
CREATE DATABASE invoicetracker;
```

#### 3. Set Environment Variables

Create `src/main/resources/application.properties`:

```properties
# Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/invoicetracker
spring.datasource.username=your_db_username
spring.datasource.password=your_db_password

# JPA/Hibernate
spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=false
spring.jpa.properties.hibernate.format_sql=true

# JWT Configuration
jwt.secret=your-secret-key-here-make-it-long-and-secure
jwt.expiration=86400000

# File Upload Configuration
spring.servlet.multipart.max-file-size=10MB
spring.servlet.multipart.max-request-size=10MB

# File Storage Path
file.storage.path=./uploads

# Google Gemini AI Configuration
gemini.api.key=your-gemini-api-key-here
gemini.api.endpoint=https://generativelanguage.googleapis.com/v1beta/models/gemini-1.5-flash:generateContent

# Server Configuration
server.port=8080
```

#### 4. Build the Project
```bash
mvn clean install
```

#### 5. Run the Application
```bash
mvn spring-boot:run
```

The application will be available at: `http://localhost:8080`

---

## 👥 User Roles & Permissions

| Role | Permissions |
|------|------------|
| **USER** | - Create/edit/delete own invoices<br>- View own invoices<br>- Upload files<br>- Use AI features |
| **SUPERUSER** | - Full control over all invoices<br>- Manage users, products, categories<br>- Change invoice status<br>- Access all features |
| **AUDITOR** | - View all invoices (read-only)<br>- Access audit logs<br>- Generate reports |

---

## 📚 API Documentation

### Authentication Endpoints

#### Register New User
```http
POST /auth/signup
Content-Type: application/json

{
  "username": "john_doe",
  "password": "SecurePass123!",
  "fullName": "John Doe",
  "email": "john@example.com"
}
```

#### Login
```http
POST /auth/login
Content-Type: application/json

{
  "username": "john_doe",
  "password": "SecurePass123!"
}

Response:
{
  "token": "eyJhbGciOiJIUzI1NiIs...",
  "username": "john_doe",
  "roles": ["USER"],
  "expiresIn": 86400000
}
```

### Invoice Endpoints

#### Create Invoice (Web Form)
```http
POST /invoices
Authorization: Bearer {token}
Content-Type: application/json

{
  "invoiceDate": "2024-11-11",
  "productQuantities": {
    "1": 5.0,
    "2": 3.0
  }
}
```

#### Upload Invoice (File)
```http
POST /invoices
Authorization: Bearer {token}
Content-Type: multipart/form-data

file: <invoice.pdf>
invoiceDate: 2024-11-11
productIds: [1, 2]
productQuantities: [5.0, 3.0]
```

#### Get All Invoices (Paginated)
```http
GET /invoices?page=0&size=10&sortBy=createdAt&direction=DESC
Authorization: Bearer {token}
```

#### Search Invoices
```http
POST /invoices/search
Authorization: Bearer {token}
Content-Type: application/json

{
  "search": "vendor name",
  "startDate": "2024-01-01",
  "endDate": "2024-12-31",
  "fileType": "PDF",
  "status": "PENDING",
  "page": 0,
  "size": 10
}
```

#### Get Invoice Details
```http
GET /invoices/{id}/details
Authorization: Bearer {token}
```

#### Update Invoice
```http
PUT /invoices/{id}
Authorization: Bearer {token}
Content-Type: multipart/form-data

file: <new_invoice.pdf>
invoiceDate: 2024-11-12
```

#### Delete Invoice (Soft Delete)
```http
DELETE /invoices/{id}
Authorization: Bearer {token}
```

### AI Features

#### Extract Invoice Data from File
```http
POST /ai/invoices/extract
Authorization: Bearer {token}
Content-Type: multipart/form-data

file: <invoice.pdf>

Response:
{
  "success": true,
  "invoiceDate": "2024-11-11",
  "totalAmount": 1500.75,
  "vendor": "ABC Company",
  "extractedItems": [
    {
      "name": "Product A",
      "quantity": 2.0,
      "unitPrice": 500.0,
      "subtotal": 1000.0
    }
  ]
}
```

#### Chat with AI Assistant
```http
POST /ai/chat
Authorization: Bearer {token}
Content-Type: application/json

{
  "query": "How many invoices did I create this month?"
}

Response:
{
  "response": "You have created 15 invoices this month (November 2024)..."
}
```

#### Debug Invoice Data (for AI)
```http
GET /ai/chat/debug
Authorization: Bearer {token}
```

### User Management (SUPERUSER only)

#### Get All Users
```http
GET /users?page=0&size=10
Authorization: Bearer {token}
```

#### Update User Role
```http
PUT /users/{userId}/role?newRole=SUPERUSER
Authorization: Bearer {token}
```

#### Deactivate User
```http
DELETE /users/{userId}
Authorization: Bearer {token}
```

---

## 🗄️ Database Schema

### Main Entities

#### Users
- `user_id` (PK)
- `username` (unique)
- `password` (encrypted)
- `full_name`
- `email`
- `is_active`
- Relationships: ManyToMany with Roles

#### Invoices
- `invoice_id` (PK)
- `uploaded_by_user_id` (FK)
- `invoice_date`
- `file_type` (PDF, IMAGE, WEB_FORM)
- `file_name`
- `stored_file_name`
- `original_file_name`
- `file_size`
- `total_amount`
- `status` (PENDING, APPROVED, REJECTED)
- `is_active`

#### Products
- `product_id` (PK)
- `product_name`
- `category_id` (FK)
- `unit_price`
- `is_active`

#### Categories
- `category_id` (PK)
- `category_name`
- `is_active`

#### Audit Logs
- `log_id` (PK)
- `invoice_id` (FK)
- `performed_by_user_id` (FK)
- `action_type` (CREATE, UPDATE, DELETE, STATUS_CHANGE)
- `timestamp`
- `old_values` (JSON)
- `new_values` (JSON)

---

## 🤖 AI Features Details

### Invoice Data Extraction

The system uses **Google Gemini Vision API** to automatically extract:
- Invoice date
- Total amount
- Vendor name
- Line items (products, quantities, prices)

**Supported formats**: PDF, JPG, PNG, GIF (max 4MB)

**How it works**:
1. User uploads invoice file
2. System converts to Base64
3. Sends to Gemini Vision API
4. Extracts structured JSON data
5. Calculates total from items if not directly available
6. Stores in database

### AI Chatbot

Ask questions about your invoices in natural language:
- "How many invoices did I create this month?"
- "What's the total amount of PDF invoices?"
- "Show me invoices from last month"
- "What percentage are image files?"

The chatbot:
- Understands relative dates (this month, last month)
- Provides statistical breakdowns
- Filters by user role permissions
- Formats responses with bullet points

---

## 🔒 Security

- **JWT Authentication**: Secure token-based auth
- **Password Encryption**: BCrypt hashing
- **Role-Based Access Control**: Method-level security with `@PreAuthorize`
- **Input Validation**: `@Valid` annotations on DTOs
- **SQL Injection Protection**: JPA/Hibernate prepared statements
- **File Upload Validation**: Size and type restrictions

---

## 🧪 Testing

### Run Unit Tests
```bash
mvn test
```

### Run Integration Tests
```bash
mvn verify
```

### Test Coverage
```bash
mvn jacoco:report
```

---

## 📝 Logging

The application uses SLF4J with Logback:

- **INFO**: General operations, API calls
- **DEBUG**: Detailed debugging information
- **WARN**: Warnings and recoverable errors
- **ERROR**: Critical errors and exceptions

Log files location: `./logs/application.log`

---

## 🐛 Common Issues & Solutions

### Issue: Database Connection Failed
**Solution**: Verify PostgreSQL is running and credentials in `application.properties` are correct.

### Issue: File Upload Fails
**Solution**: Check `file.storage.path` exists and application has write permissions.

### Issue: AI Extraction Not Working
**Solution**: Verify `gemini.api.key` is valid and has quota remaining.

### Issue: JWT Token Expired
**Solution**: Re-login to get a new token. Default expiration is 24 hours.

---

## 🛠️ Development

### Project Structure
```
src/
├── main/
│   ├── java/com/example/invoicetracker/
│   │   ├── config/           # Configuration classes
│   │   ├── controller/       # REST controllers
│   │   ├── dto/              # Data Transfer Objects
│   │   ├── exception/        # Custom exceptions
│   │   ├── factory/          # Factory pattern implementations
│   │   ├── model/
│   │   │   ├── entity/       # JPA entities
│   │   │   └── enums/        # Enums
│   │   ├── repository/       # JPA repositories
│   │   ├── security/         # Security configs & JWT
│   │   ├── service/          # Business logic
│   │   │   └── ai/           # AI services
│   │   └── strategy/         # Strategy pattern implementations
│   └── resources/
│       └── application.properties
└── test/                     # Unit & integration tests
```

### Adding a New Feature

1. Create entity in `model/entity/`
2. Create repository in `repository/`
3. Create DTOs in `dto/`
4. Implement service in `service/`
5. Create controller in `controller/`
6. Add security rules in `SecurityConfig`
7. Write tests

---

## 📊 API Documentation (Swagger)

Once the application is running, access interactive API docs:

```
http://localhost:8080/swagger-ui.html
```

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## 📄 License

This project is licensed under the MIT License.

---

## 👨‍💻 Author

**Wafaa Alshaikh**

---

## 🙏 Acknowledgments

- Spring Boot team for the excellent framework
- Google Gemini AI for vision capabilities
- PostgreSQL for reliable database
- All contributors and testers

---

## 📞 Support

For issues and questions:
- Create an issue on GitHub
- Contact: wafaa@example.com

---

**Made with ❤️ using Spring Boot**
