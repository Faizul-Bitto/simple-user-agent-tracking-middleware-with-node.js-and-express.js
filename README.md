# 🔐 User Agent Tracking Middleware with Node.js and Express.js

<div align="center">

![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=nodedotjs&logoColor=white)
![Express.js](https://img.shields.io/badge/Express.js-000000?style=for-the-badge&logo=express&logoColor=white)
![Joi](https://img.shields.io/badge/Joi-Validation-4A90E2?style=for-the-badge)

A robust REST API application demonstrating middleware implementation for user agent tracking and token-based authentication in Express.js.

[Features](#-features) • [Installation](#-installation) • [API Endpoints](#-api-endpoints) • [Usage](#-usage)

</div>

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Installation](#-installation)
- [Configuration](#-configuration)
- [API Endpoints](#-api-endpoints)
- [Usage Examples](#-usage-examples)
- [Middleware Details](#-middleware-details)
- [Development](#-development)
- [Author](#-author)

---

## 🎯 Overview

This project is a comprehensive Express.js application that showcases the implementation of custom middleware for:

- **Token-based Authentication** - Validates API requests using query parameters
- **User-Agent Tracking** - Monitors and validates client user agents
- **Request Validation** - Uses Joi schema validation for data integrity
- **RESTful API Design** - Clean and organized route structure

Perfect for learning middleware patterns, request validation, and building scalable Express.js applications.

---

## ✨ Features

- 🔑 **Token Authentication Middleware** - Secure your API endpoints with token validation
- 🌐 **User-Agent Tracking** - Monitor and log client user agents for analytics and security
- ✅ **Joi Schema Validation** - Robust input validation for user data
- 📝 **RESTful API** - Well-structured endpoints for user management
- 🚀 **Development Ready** - Configured with nodemon for hot-reloading
- 📦 **Modular Architecture** - Clean separation of concerns (routes, controllers, middleware, models)

---

## 🛠 Tech Stack

| Technology | Version | Purpose |
|------------|---------|---------|
| **Node.js** | Latest | Runtime environment |
| **Express.js** | ^5.1.0 | Web framework |
| **Joi** | ^18.0.1 | Schema validation |
| **Nodemon** | ^3.1.10 | Development tool |

---

## 📁 Project Structure

```
simple-user-agent-tracking-middleware-with-node.js-and-express.js/
│
├── 📂 controller/
│   └── UserController.js          # Business logic for user operations
│
├── 📂 data/
│   └── users.js                   # In-memory user data store
│
├── 📂 middleware/
│   ├── CheckUserAgent.js          # User-Agent header validation
│   └── IsValid.js                 # Token authentication middleware
│
├── 📂 model/
│   └── 📂 schema/
│       └── User.js                # Joi validation schema
│
├── 📂 routes/
│   └── user.js                    # User API routes
│
├── index.js                       # Application entry point
├── package.json                   # Project dependencies
└── README.md                      # Project documentation
```

---

## 🚀 Installation

### Prerequisites

- Node.js (v14 or higher)
- npm or yarn

### Steps

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd simple-user-agent-tracking-middleware-with-node.js-and-express.js
   ```

2. **Install dependencies**
   ```bash
   npm install
   ```

3. **Create environment file**
   ```bash
   # Create a .env file in the root directory
   PORT=3000
   ```

4. **Start the development server**
   ```bash
   npm start
   ```

   The server will start on the port specified in your `.env` file (default: 3000).

---

## ⚙️ Configuration

### Environment Variables

Create a `.env` file in the root directory:

```env
PORT=3000
```

### Middleware Configuration

#### Token Authentication
- **Token**: `123` (hardcoded for demo purposes)
- **Location**: Query parameter (`?token=123`)
- **Middleware**: `IsValid.js`

#### User-Agent Validation
- **Middleware**: `CheckUserAgent.js`
- **Behavior**: Logs user agent and validates presence
- **Note**: User-agent blocking is commented out (can be enabled for production)

---

## 📡 API Endpoints

### Base URL
```
http://localhost:3000/api
```

### Authentication
All endpoints require a token query parameter:
```
?token=123
```

### Endpoints

#### 1. Get All Users
```http
GET /api/users?token=123
```

**Response:**
```json
[
  {
    "id": 1,
    "name": "John",
    "email": "john@gmail.com"
  },
  {
    "id": 2,
    "name": "Jane",
    "email": "jane@gmail.com"
  },
  {
    "id": 3,
    "name": "Jim",
    "email": "jim@gmail.com"
  }
]
```

---

#### 2. Get User by ID
```http
GET /api/users/:id?token=123
```

**Parameters:**
- `id` (path parameter) - User ID

**Response:**
```json
[
  {
    "id": 1,
    "name": "John",
    "email": "john@gmail.com"
  }
]
```

---

#### 3. Create User
```http
POST /api/users?token=123
Content-Type: application/json
```

**Request Body:**
```json
{
  "id": 4,
  "name": "Alice",
  "email": "alice@example.com"
}
```

**Validation Rules:**
- `id`: Positive number (required)
- `name`: String, 3-100 characters (required)
- `email`: Valid email format (required)

**Success Response (201):**
```json
{
  "message": "User created successfully",
  "user": {
    "id": 4,
    "name": "Alice",
    "email": "alice@example.com"
  }
}
```

**Error Response (400):**
```json
{
  "message": "Validation error message"
}
```

---

## 💡 Usage Examples

### Using cURL

#### Get All Users
```bash
curl -X GET "http://localhost:3000/api/users?token=123" \
  -H "User-Agent: MyApp/1.0"
```

#### Get User by ID
```bash
curl -X GET "http://localhost:3000/api/users/1?token=123" \
  -H "User-Agent: MyApp/1.0"
```

#### Create User
```bash
curl -X POST "http://localhost:3000/api/users?token=123" \
  -H "Content-Type: application/json" \
  -H "User-Agent: MyApp/1.0" \
  -d '{
    "id": 4,
    "name": "Alice",
    "email": "alice@example.com"
  }'
```

### Using JavaScript (Fetch API)

```javascript
// Get all users
fetch('http://localhost:3000/api/users?token=123', {
  headers: {
    'User-Agent': 'MyApp/1.0'
  }
})
  .then(response => response.json())
  .then(data => console.log(data));

// Create a new user
fetch('http://localhost:3000/api/users?token=123', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'User-Agent': 'MyApp/1.0'
  },
  body: JSON.stringify({
    id: 4,
    name: 'Alice',
    email: 'alice@example.com'
  })
})
  .then(response => response.json())
  .then(data => console.log(data));
```

### Using Postman/Thunder Client

1. Set the request URL: `http://localhost:3000/api/users?token=123`
2. Add header: `User-Agent: YourApp/1.0`
3. For POST requests, set body type to `JSON` and include the user data

---

## 🔧 Middleware Details

### 1. IsValid Middleware
**File:** `middleware/IsValid.js`

Validates the authentication token from query parameters.

- **Checks**: `req.query.token === "123"`
- **Success**: Calls `next()` to proceed
- **Failure**: Returns `401 Unauthorized`

```javascript
// Example usage in route
app.use('/api', isValid, checkUserAgent, userRoutes);
```

---

### 2. CheckUserAgent Middleware
**File:** `middleware/CheckUserAgent.js`

Tracks and validates the User-Agent header.

- **Logs**: User agent to console
- **Validates**: Presence of User-Agent header
- **Failure**: Returns `400 Bad Request` if missing
- **Optional**: Can block specific user agents (commented out)

```javascript
// Console output example
// The agent is Mozilla/5.0 (Windows NT 10.0; Win64; x64)...
```

---

## 🧪 Development

### Running in Development Mode

The project uses `nodemon` for automatic server restarts:

```bash
npm start
```

### Project Scripts

```json
{
  "start": "nodemon --env-file .env index.js"
}
```

### Adding New Features

1. **New Route**: Add to `routes/user.js`
2. **New Controller**: Add method to `controller/UserController.js`
3. **New Middleware**: Create file in `middleware/` directory
4. **New Schema**: Add to `model/schema/User.js` or create new schema file

---

## 📝 Error Handling

### Common Error Responses

| Status Code | Message | Cause |
|------------|---------|-------|
| `400` | Bad Request: Missing User-Agent header | User-Agent header not provided |
| `400` | Validation error message | Invalid request body (Joi validation) |
| `401` | Unauthorized: Invalid Token | Missing or incorrect token |
| `404` | User not found | Invalid user ID parameter |

---

## 🔒 Security Considerations

⚠️ **Important Notes:**

- The current token (`123`) is hardcoded for demonstration purposes
- For production, implement:
  - Environment variable for tokens
  - JWT or OAuth2 authentication
  - Rate limiting
  - HTTPS encryption
  - Input sanitization
  - Database instead of in-memory storage

---

## 👤 Author

**Faizul Bitto**

---

## 📄 License

This project is licensed under the ISC License.

---

## 🤝 Contributing

Contributions, issues, and feature requests are welcome! Feel free to check the issues page.

---

<div align="center">

**⭐ Star this repo if you find it helpful! ⭐**

Made with ❤️ using Express.js

</div>

