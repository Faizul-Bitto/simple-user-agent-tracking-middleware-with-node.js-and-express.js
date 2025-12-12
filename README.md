# Simple User Agent Tracking Middleware with Node.js and Express.js

A lightweight Express.js API server that implements user-agent tracking and validation middleware. This project demonstrates how to secure API endpoints by validating user-agent headers and tracking them for security analysis.

## 📋 Table of Contents

- [Features](#features)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Usage](#usage)
- [API Endpoints](#api-endpoints)
- [Middleware](#middleware)
- [Project Structure](#project-structure)
- [Technologies Used](#technologies-used)
- [Examples](#examples)
- [Security Features](#security-features)

## ✨ Features

- **User-Agent Validation**: Automatically blocks suspicious user-agents (curl, wget, bots, scanners, etc.)
- **User-Agent Tracking with Counters**: Tracks and counts all user-agent strings (both blocked and non-blocked) in separate JSON files
- **Interactive Dashboard**: Beautiful web dashboard with real-time statistics, charts, and data visualization
- **Data Visualization**: Pie charts and bar graphs showing blocked vs non-blocked agents
- **Token Authentication**: Simple token-based authentication via query parameter
- **Request Validation**: Uses Joi schema validation for user data
- **RESTful API**: Clean REST endpoints for user management
- **Express.js Middleware**: Modular middleware architecture

## 🔧 Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js** (v14 or higher)
- **npm** (v6 or higher) or **yarn**

## 📦 Installation

1. Clone the repository:
```bash
git clone <repository-url>
cd simple-user-agent-tracking-middleware-with-node.js-and-express.js
```

2. Install dependencies:
```bash
npm install
```

3. Create a `.env` file in the root directory:
```env
PORT=3000
```

## ⚙️ Configuration

### Environment Variables

Create a `.env` file in the root directory with the following variable:

- `PORT`: The port number on which the server will run (default: 3000)

### Token Configuration

The API uses a simple token-based authentication. Currently, the token is hardcoded as `"123"` in the `IsValid` middleware. To change it, modify `middleware/IsValid.js` or consider using environment variables for production.

## 🚀 Usage

Start the development server:

```bash
npm start
```

The server will start on the port specified in your `.env` file (default: 3000).

### Viewing the Dashboard

Once the server is running, open your browser and navigate to:

```
http://localhost:3000
```

You'll see an interactive dashboard displaying:
- Statistics cards (Total User Agents, Total Requests, Total Blocked)
- Pie chart showing blocked vs not blocked user agents
- Bar chart showing all blocked agents with their counts
- Detailed table with all user agents and their tracking data

## 📡 API Endpoints

### Get User Agent Tracking Data

```http
GET /api/user-agent-tracking
```

**No authentication required** - This endpoint is public for dashboard access.

**Response:**
```json
[
  {
    "userAgent": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36",
    "blocked": "No",
    "count": 5,
    "blockedCount": 0
  },
  {
    "userAgent": "curl/8.16.0",
    "blocked": "Yes",
    "count": 0,
    "blockedCount": 3
  }
]
```

### User Management Endpoints

All user endpoints are prefixed with `/api` and require:
1. A valid token query parameter (`?token=123`)
2. A valid user-agent header (not blocked)

### Get All Users

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
  }
]
```

### Get User by ID

```http
GET /api/users/:id?token=123
```

**Parameters:**
- `id` (path parameter): User ID

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

### Create User

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
- `id`: Must be a positive number (required)
- `name`: Must be a string between 3-100 characters (required)
- `email`: Must be a valid email address (required)

**Response:**
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

## 🛡️ Middleware

### 1. `isValid` Middleware

Located in `middleware/IsValid.js`

- **Purpose**: Validates the authentication token
- **Validation**: Checks for `token` query parameter (must equal `"123"`)
- **Response**: Returns 401 Unauthorized if token is invalid

### 2. `checkUserAgent` Middleware

Located in `middleware/CheckUserAgent.js`

- **Purpose**: Validates and tracks user-agent headers
- **Blocked Patterns**: Automatically blocks requests from:
  - `curl`
  - `wget`
  - `python-requests`
  - `Go-http-client`
  - `Java`
  - `sqlmap`
  - `nmap`
  - `Nikto`
  - `HeadlessChrome`
  - `PhantomJS`
- **Tracking**: 
  - Non-blocked agents: Logs to `userAgentTracking/userAgent.json` with counters
  - Blocked agents: Logs to `userAgentTracking/blockedUserAgent.json` with counters
  - Each user agent is tracked with a count of how many times it was seen
- **Response**: Returns 403 Forbidden if user-agent is missing or blocked

## 📁 Project Structure

```
.
├── controller/
│   ├── UserController.js                    # User business logic
│   └── UserAgentTrackingController.js      # User agent tracking data controller
├── middleware/
│   ├── CheckUserAgent.js                    # User-agent validation middleware
│   └── IsValid.js                           # Token validation middleware
├── model/
│   └── schema/
│       └── User.js                          # Joi validation schema
├── routes/
│   ├── user.js                              # User routes
│   └── userAgentTracking.js                 # User agent tracking routes
├── utils/
│   └── logger.js                            # User-agent logging utility with counters
├── public/
│   └── index.html                           # Interactive dashboard (HTML/CSS/JS)
├── userAgentTracking/
│   ├── userAgent.json                       # Non-blocked user agents with counts
│   └── blockedUserAgent.json               # Blocked user agents with counts
├── index.js                                 # Application entry point
├── users.js                                 # User data store
├── package.json                             # Project dependencies
└── README.md                                # This file
```

## 🛠️ Technologies Used

- **Express.js** (v5.1.0): Web framework for Node.js
- **Joi** (v18.0.1): Schema validation library
- **Nodemon** (v3.1.10): Development tool for auto-restarting server
- **Chart.js**: JavaScript charting library for data visualization (loaded via CDN)

## 💡 Examples

### Using cURL (Will be blocked)

```bash
curl http://localhost:3000/api/users?token=123
```

**Response:**
```json
{
  "message": "Forbidden: Suspicious User-Agent"
}
```

### Using Browser or Postman

```bash
GET http://localhost:3000/api/users?token=123
```

With a valid browser user-agent, this will return the users list.

### Creating a User

```bash
curl -X POST http://localhost:3000/api/users?token=123 \
  -H "Content-Type: application/json" \
  -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36" \
  -d '{
    "id": 4,
    "name": "Alice",
    "email": "alice@example.com"
  }'
```

### Invalid Token

```bash
GET http://localhost:3000/api/users?token=wrong
```

**Response:**
```json
{
  "message": "Unauthorized: Invalid Token"
}
```

## 🔒 Security Features

1. **User-Agent Filtering**: Blocks automated tools and bots
2. **Token Authentication**: Simple token-based access control
3. **Request Validation**: Validates all incoming user data using Joi schemas
4. **User-Agent Tracking with Counters**: Tracks and counts all user-agent strings (both blocked and non-blocked) for security analysis
5. **Separate Tracking Files**: Maintains separate files for blocked and non-blocked agents for better analysis

## 📊 Dashboard Features

The interactive dashboard provides:

- **Statistics Overview**: 
  - Total User Agents tracked
  - Total Requests (non-blocked)
  - Total Blocked requests

- **Pie Chart**: Visual representation of blocked vs not blocked user agents with percentages

- **Bar Chart**: All blocked user agents displayed with their blocked counts

- **Detailed Table**: Complete list of all user agents showing:
  - User Agent Name
  - Blocked Status (Yes/No)
  - Count (non-blocked requests)
  - Blocked Count

- **Manual Refresh**: Click the refresh button to update data (no auto-refresh)

## 📝 Notes

- The `userAgentTracking/` directory is automatically created with JSON files tracking user agents
- Files use object format: `{ "user-agent-string": count }` for efficient tracking
- User data is currently stored in memory (`users.js`). For production, consider using a database
- The token validation is simplified for demonstration. For production, use proper authentication (JWT, OAuth, etc.)
- The user-agent blocking patterns can be customized in `middleware/CheckUserAgent.js`
- The dashboard is accessible at the root URL (`http://localhost:PORT`) without authentication

## 👤 Author

**Faizul Bitto**