# 🚀 Express.js - Beginner to Advanced Complete Tutorial

## 📋 Table of Contents

---

# PHASE 1: FOUNDATION (Beginner)

---

## Chapter 1: Introduction & Setup

### 📖 Theory
Express.js is a **minimal and flexible Node.js web application framework** that provides a robust set of features for web and mobile applications. It's the "E" in the MEAN/MERN stack. Express simplifies Node.js's built-in HTTP module, making it easier to create servers, handle routes, manage middleware, and much more.

**Why Express.js?**
- Minimal and unopinionated
- Fast and lightweight
- Huge middleware ecosystem
- Most popular Node.js framework
- Great community support

### 💻 Setup

```bash
# Step 1: Initialize a Node.js project
mkdir express-mastery
cd express-mastery
npm init -y

# Step 2: Install Express
npm install express

# Step 3: Install development dependencies
npm install nodemon --save-dev
```

**Update package.json:**
```json
{
  "name": "express-mastery",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  },
  "devDependencies": {
    "nodemon": "^3.0.0"
  }
}
```

### 🏗️ Your First Server

```javascript
// index.js

const express = require('express');
const app = express();
const PORT = 3000;

// Your first route
app.get('/', (req, res) => {
  res.send('Hello World! Welcome to Express.js Mastery! 🚀');
});

// Start the server
app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
```

```bash
# Run the server
npm run dev
```

---

## Chapter 2: Understanding Request & Response

### 📖 Theory
Every time a client (browser, Postman, mobile app) communicates with an Express server, two objects are created:
- **Request (req):** Contains information about the incoming HTTP request (URL, headers, body, parameters, etc.)
- **Response (res):** Used to send back data to the client

### 💻 Example: Exploring req and res

```javascript
const express = require('express');
const app = express();

// ============================================
// RESPONSE METHODS
// ============================================

// res.send() - Send string/HTML/Buffer/Object
app.get('/send', (req, res) => {
  res.send('<h1>Hello with res.send()</h1>');
});

// res.json() - Send JSON response (most common in APIs)
app.get('/json', (req, res) => {
  res.json({
    success: true,
    message: 'This is a JSON response',
    data: { name: 'Express', version: '4.x' }
  });
});

// res.status() - Set HTTP status code
app.get('/not-found', (req, res) => {
  res.status(404).json({
    success: false,
    message: 'Resource not found'
  });
});

// res.redirect() - Redirect to another URL
app.get('/old-page', (req, res) => {
  res.redirect('/send');
});

// res.download() - Prompt file download
app.get('/download', (req, res) => {
  res.download('./package.json');
});

// ============================================
// REQUEST PROPERTIES
// ============================================

app.get('/request-info', (req, res) => {
  res.json({
    method: req.method,           // GET
    url: req.url,                 // /request-info
    path: req.path,               // /request-info
    protocol: req.protocol,       // http
    hostname: req.hostname,       // localhost
    ip: req.ip,                   // ::1 or 127.0.0.1
    headers: req.headers,         // All request headers
    query: req.query,             // Query string parameters
  });
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

---

## Chapter 3: Routing Basics

### 📖 Theory
**Routing** refers to how an application responds to a client request at a particular endpoint (URI) and HTTP method (GET, POST, PUT, DELETE, PATCH). Each route can have one or more handler functions.

**HTTP Methods:**
| Method | Purpose | Example |
|--------|---------|---------|
| GET | Retrieve data | Get all users |
| POST | Create new data | Create a user |
| PUT | Update entire resource | Update full user profile |
| PATCH | Update partial resource | Update just email |
| DELETE | Delete data | Delete a user |

### 💻 Example: Complete Routing

```javascript
const express = require('express');
const app = express();

// Middleware to parse JSON body
app.use(express.json());

// In-memory database
let books = [
  { id: 1, title: 'The Great Gatsby', author: 'F. Scott Fitzgerald', year: 1925 },
  { id: 2, title: '1984', author: 'George Orwell', year: 1949 },
  { id: 3, title: 'To Kill a Mockingbird', author: 'Harper Lee', year: 1960 }
];
let nextId = 4;

// ============================================
// GET - Retrieve all books
// ============================================
app.get('/api/books', (req, res) => {
  res.json({
    success: true,
    count: books.length,
    data: books
  });
});

// ============================================
// GET - Retrieve a single book by ID
// ============================================
app.get('/api/books/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const book = books.find(b => b.id === id);

  if (!book) {
    return res.status(404).json({
      success: false,
      message: `Book with id ${id} not found`
    });
  }

  res.json({ success: true, data: book });
});

// ============================================
// POST - Create a new book
// ============================================
app.post('/api/books', (req, res) => {
  const { title, author, year } = req.body;

  // Validation
  if (!title || !author) {
    return res.status(400).json({
      success: false,
      message: 'Please provide title and author'
    });
  }

  const newBook = {
    id: nextId++,
    title,
    author,
    year: year || 'Unknown'
  };

  books.push(newBook);

  res.status(201).json({
    success: true,
    message: 'Book created successfully',
    data: newBook
  });
});

// ============================================
// PUT - Update entire book
// ============================================
app.put('/api/books/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const index = books.findIndex(b => b.id === id);

  if (index === -1) {
    return res.status(404).json({
      success: false,
      message: `Book with id ${id} not found`
    });
  }

  const { title, author, year } = req.body;

  books[index] = { id, title, author, year };

  res.json({
    success: true,
    message: 'Book updated successfully',
    data: books[index]
  });
});

// ============================================
// PATCH - Partial update
// ============================================
app.patch('/api/books/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const index = books.findIndex(b => b.id === id);

  if (index === -1) {
    return res.status(404).json({
      success: false,
      message: `Book with id ${id} not found`
    });
  }

  // Only update provided fields
  books[index] = { ...books[index], ...req.body, id }; // id should not change

  res.json({
    success: true,
    message: 'Book partially updated',
    data: books[index]
  });
});

// ============================================
// DELETE - Remove a book
// ============================================
app.delete('/api/books/:id', (req, res) => {
  const id = parseInt(req.params.id);
  const index = books.findIndex(b => b.id === id);

  if (index === -1) {
    return res.status(404).json({
      success: false,
      message: `Book with id ${id} not found`
    });
  }

  const deletedBook = books.splice(index, 1);

  res.json({
    success: true,
    message: 'Book deleted successfully',
    data: deletedBook[0]
  });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## Chapter 4: Route Parameters, Query Strings & Request Body

### 📖 Theory

| Type | Location | Example URL | Access |
|------|----------|-------------|--------|
| **Route Params** | In the URL path | `/users/42` | `req.params.id` |
| **Query Strings** | After `?` in URL | `/users?age=25&city=NYC` | `req.query.age` |
| **Request Body** | In HTTP body | POST/PUT data | `req.body` |

### 💻 Example

```javascript
const express = require('express');
const app = express();

app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// ============================================
// ROUTE PARAMETERS
// ============================================

// Single parameter
app.get('/users/:id', (req, res) => {
  res.json({ userId: req.params.id });
});

// Multiple parameters
app.get('/users/:userId/posts/:postId', (req, res) => {
  res.json({
    userId: req.params.userId,
    postId: req.params.postId
  });
});

// Optional-like behavior using multiple routes
// Pattern with regex-like constraints
app.get('/products/:category/:id(\\d+)', (req, res) => {
  res.json({
    category: req.params.category,
    productId: req.params.id,
    note: 'id must be a number due to \\d+ pattern'
  });
});

// ============================================
// QUERY STRINGS
// ============================================

// Example: GET /search?keyword=express&page=1&limit=10&sort=name&order=asc
app.get('/search', (req, res) => {
  const {
    keyword = '',         // Default empty string
    page = 1,             // Default page 1
    limit = 10,           // Default 10 items
    sort = 'createdAt',   // Default sort field
    order = 'desc'        // Default order
  } = req.query;

  res.json({
    searchParams: {
      keyword,
      page: parseInt(page),
      limit: parseInt(limit),
      sort,
      order
    },
    message: `Searching for "${keyword}" - Page ${page}`
  });
});

// ============================================
// REQUEST BODY (POST/PUT/PATCH)
// ============================================

// JSON body
app.post('/api/register', (req, res) => {
  const { username, email, password } = req.body;

  // Validate
  const errors = [];
  if (!username) errors.push('Username is required');
  if (!email) errors.push('Email is required');
  if (!password) errors.push('Password is required');
  if (password && password.length < 6) errors.push('Password must be at least 6 characters');

  if (errors.length > 0) {
    return res.status(400).json({ success: false, errors });
  }

  res.status(201).json({
    success: true,
    message: 'User registered successfully',
    user: { username, email }  // Never send password back!
  });
});

// URL-encoded body (from HTML forms)
app.post('/api/contact', (req, res) => {
  const { name, email, message } = req.body;
  
  res.json({
    success: true,
    message: 'Contact form submitted',
    data: { name, email, message }
  });
});

// ============================================
// COMBINING ALL THREE
// ============================================

// GET /api/shops/:shopId/products?category=electronics&inStock=true
app.get('/api/shops/:shopId/products', (req, res) => {
  const { shopId } = req.params;               // Route param
  const { category, inStock, minPrice, maxPrice } = req.query;  // Query strings

  res.json({
    shopId,
    filters: {
      category: category || 'all',
      inStock: inStock === 'true',
      priceRange: {
        min: minPrice ? parseFloat(minPrice) : 0,
        max: maxPrice ? parseFloat(maxPrice) : Infinity
      }
    }
  });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## Chapter 5: Middleware — The Heart of Express

### 📖 Theory
**Middleware** functions are functions that have access to the **request object (req)**, the **response object (res)**, and the **next middleware function** in the application's request-response cycle (commonly denoted by `next`).

Middleware can:
- Execute any code
- Modify request and response objects
- End the request-response cycle
- Call the next middleware in the stack

**Types of Middleware:**
1. **Application-level** — `app.use()`, `app.get()`, etc.
2. **Router-level** — `router.use()`, `router.get()`, etc.
3. **Error-handling** — Has 4 arguments `(err, req, res, next)`
4. **Built-in** — `express.json()`, `express.static()`, `express.urlencoded()`
5. **Third-party** — `cors`, `morgan`, `helmet`, etc.

**Middleware Flow:**
```
Request → Middleware 1 → Middleware 2 → Middleware 3 → Route Handler → Response
```

### 💻 Example: All Types of Middleware

```javascript
const express = require('express');
const app = express();

// ============================================
// 1. BUILT-IN MIDDLEWARE
// ============================================

// Parse JSON request bodies
app.use(express.json());

// Parse URL-encoded bodies (form submissions)
app.use(express.urlencoded({ extended: true }));

// Serve static files from 'public' folder
app.use(express.static('public'));

// ============================================
// 2. APPLICATION-LEVEL MIDDLEWARE (Custom)
// ============================================

// Logger Middleware - Runs on EVERY request
const logger = (req, res, next) => {
  const timestamp = new Date().toISOString();
  console.log(`[${timestamp}] ${req.method} ${req.url} - IP: ${req.ip}`);
  
  // Add custom property to request
  req.requestTime = timestamp;
  
  // MUST call next() to pass control to next middleware
  next();
};

app.use(logger);

// Request Counter Middleware
let requestCount = 0;
app.use((req, res, next) => {
  requestCount++;
  req.requestNumber = requestCount;
  console.log(`Request #${requestCount}`);
  next();
});

// ============================================
// 3. ROUTE-SPECIFIC MIDDLEWARE
// ============================================

// Auth check middleware (not applied globally)
const authenticate = (req, res, next) => {
  const token = req.headers['authorization'];

  if (!token) {
    return res.status(401).json({
      success: false,
      message: 'Access denied. No token provided.'
    });
  }

  if (token !== 'Bearer mysecrettoken123') {
    return res.status(403).json({
      success: false,
      message: 'Invalid token.'
    });
  }

  // Attach user info to request
  req.user = { id: 1, name: 'John Doe', role: 'admin' };
  next();
};

// Role-based authorization middleware
const authorize = (...roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({
        success: false,
        message: `Role '${req.user.role}' is not authorized to access this route`
      });
    }
    next();
  };
};

// Public route - No auth needed
app.get('/', (req, res) => {
  res.json({
    message: 'Public route - No auth needed',
    requestTime: req.requestTime,
    requestNumber: req.requestNumber
  });
});

// Protected route - Single middleware
app.get('/profile', authenticate, (req, res) => {
  res.json({
    message: 'Protected route',
    user: req.user
  });
});

// Admin only route - Chained middleware
app.get('/admin', authenticate, authorize('admin'), (req, res) => {
  res.json({
    message: 'Admin dashboard',
    user: req.user
  });
});

// Multiple middleware in array
const validateBookInput = (req, res, next) => {
  const { title, author } = req.body;
  const errors = [];

  if (!title || title.trim() === '') errors.push('Title is required');
  if (!author || author.trim() === '') errors.push('Author is required');
  if (title && title.length < 2) errors.push('Title must be at least 2 characters');

  if (errors.length > 0) {
    return res.status(400).json({ success: false, errors });
  }

  // Sanitize input
  req.body.title = title.trim();
  req.body.author = author.trim();

  next();
};

app.post('/api/books', [authenticate, validateBookInput], (req, res) => {
  res.status(201).json({
    success: true,
    message: 'Book created',
    data: req.body,
    createdBy: req.user.name
  });
});

// ============================================
// 4. TIMING MIDDLEWARE (Measure response time)
// ============================================

const responseTime = (req, res, next) => {
  const start = Date.now();

  // Override res.json to capture timing
  const originalJson = res.json.bind(res);
  res.json = (body) => {
    const duration = Date.now() - start;
    body.responseTime = `${duration}ms`;
    return originalJson(body);
  };

  next();
};

app.use('/api/slow', responseTime);

app.get('/api/slow', (req, res) => {
  // Simulate slow operation
  setTimeout(() => {
    res.json({ message: 'Slow response', data: [1, 2, 3] });
  }, 1500);
});

// ============================================
// 5. ERROR-HANDLING MIDDLEWARE (Must have 4 params)
// ============================================

// Route that throws an error
app.get('/error', (req, res, next) => {
  try {
    throw new Error('Something went wrong!');
  } catch (error) {
    next(error);  // Pass error to error handler
  }
});

// Async error simulation
app.get('/async-error', (req, res, next) => {
  // Simulating an async operation that fails
  Promise.reject(new Error('Async operation failed'))
    .catch(next);  // Pass to error handler
});

// 404 Handler (must be after all other routes)
app.use((req, res, next) => {
  res.status(404).json({
    success: false,
    message: `Route ${req.originalUrl} not found`
  });
});

// Global Error Handler (must be last, must have 4 params)
app.use((err, req, res, next) => {
  console.error('ERROR:', err.message);

  res.status(err.statusCode || 500).json({
    success: false,
    message: err.message || 'Internal Server Error',
    stack: process.env.NODE_ENV === 'development' ? err.stack : undefined
  });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## Chapter 6: Express Router — Organizing Routes

### 📖 Theory
As your application grows, putting all routes in a single file becomes unmanageable. **Express Router** lets you create modular, mountable route handlers. A Router instance is a complete middleware and routing system — often referred to as a "mini-app."

### 📁 Project Structure
```
express-mastery/
├── index.js                 (Main entry point)
├── routes/
│   ├── userRoutes.js
│   ├── productRoutes.js
│   └── authRoutes.js
├── controllers/
│   ├── userController.js
│   ├── productController.js
│   └── authController.js
├── middleware/
│   ├── auth.js
│   ├── logger.js
│   └── errorHandler.js
└── package.json
```

### 💻 Example

**middleware/logger.js**
```javascript
const logger = (req, res, next) => {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.originalUrl}`);
  next();
};

module.exports = logger;
```

**middleware/auth.js**
```javascript
const authenticate = (req, res, next) => {
  const token = req.headers.authorization;

  if (!token || token !== 'Bearer secret123') {
    return res.status(401).json({ success: false, message: 'Unauthorized' });
  }

  req.user = { id: 1, name: 'John', role: 'admin' };
  next();
};

const authorize = (...roles) => (req, res, next) => {
  if (!roles.includes(req.user.role)) {
    return res.status(403).json({ success: false, message: 'Forbidden' });
  }
  next();
};

module.exports = { authenticate, authorize };
```

**middleware/errorHandler.js**
```javascript
const notFound = (req, res, next) => {
  const error = new Error(`Not Found - ${req.originalUrl}`);
  res.status(404);
  next(error);
};

const errorHandler = (err, req, res, next) => {
  const statusCode = res.statusCode === 200 ? 500 : res.statusCode;
  res.status(statusCode).json({
    success: false,
    message: err.message,
    stack: process.env.NODE_ENV === 'production' ? null : err.stack
  });
};

module.exports = { notFound, errorHandler };
```

**controllers/userController.js**
```javascript
let users = [
  { id: 1, name: 'Alice', email: 'alice@email.com', age: 28 },
  { id: 2, name: 'Bob', email: 'bob@email.com', age: 32 },
  { id: 3, name: 'Charlie', email: 'charlie@email.com', age: 25 }
];
let nextId = 4;

// @desc    Get all users
// @route   GET /api/users
// @access  Public
const getUsers = (req, res) => {
  // Support filtering and pagination via query params
  let result = [...users];

  // Filter by name
  if (req.query.name) {
    result = result.filter(u =>
      u.name.toLowerCase().includes(req.query.name.toLowerCase())
    );
  }

  // Filter by age
  if (req.query.minAge) {
    result = result.filter(u => u.age >= parseInt(req.query.minAge));
  }

  // Pagination
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 10;
  const startIndex = (page - 1) * limit;
  const endIndex = page * limit;

  const paginatedResult = result.slice(startIndex, endIndex);

  res.json({
    success: true,
    count: paginatedResult.length,
    total: result.length,
    page,
    pages: Math.ceil(result.length / limit),
    data: paginatedResult
  });
};

// @desc    Get single user
// @route   GET /api/users/:id
// @access  Public
const getUserById = (req, res) => {
  const user = users.find(u => u.id === parseInt(req.params.id));

  if (!user) {
    return res.status(404).json({
      success: false,
      message: 'User not found'
    });
  }

  res.json({ success: true, data: user });
};

// @desc    Create new user
// @route   POST /api/users
// @access  Private
const createUser = (req, res) => {
  const { name, email, age } = req.body;

  if (!name || !email) {
    return res.status(400).json({
      success: false,
      message: 'Name and email are required'
    });
  }

  // Check for duplicate email
  if (users.find(u => u.email === email)) {
    return res.status(400).json({
      success: false,
      message: 'Email already exists'
    });
  }

  const newUser = { id: nextId++, name, email, age: age || null };
  users.push(newUser);

  res.status(201).json({
    success: true,
    message: 'User created',
    data: newUser
  });
};

// @desc    Update user
// @route   PUT /api/users/:id
// @access  Private
const updateUser = (req, res) => {
  const index = users.findIndex(u => u.id === parseInt(req.params.id));

  if (index === -1) {
    return res.status(404).json({ success: false, message: 'User not found' });
  }

  users[index] = { ...users[index], ...req.body, id: users[index].id };

  res.json({ success: true, message: 'User updated', data: users[index] });
};

// @desc    Delete user
// @route   DELETE /api/users/:id
// @access  Private/Admin
const deleteUser = (req, res) => {
  const index = users.findIndex(u => u.id === parseInt(req.params.id));

  if (index === -1) {
    return res.status(404).json({ success: false, message: 'User not found' });
  }

  const deleted = users.splice(index, 1);
  res.json({ success: true, message: 'User deleted', data: deleted[0] });
};

module.exports = { getUsers, getUserById, createUser, updateUser, deleteUser };
```

**routes/userRoutes.js**
```javascript
const express = require('express');
const router = express.Router();
const { authenticate, authorize } = require('../middleware/auth');
const {
  getUsers,
  getUserById,
  createUser,
  updateUser,
  deleteUser
} = require('../controllers/userController');

// Chain routes for same path
router.route('/')
  .get(getUsers)                                      // Public
  .post(authenticate, createUser);                    // Private

router.route('/:id')
  .get(getUserById)                                   // Public
  .put(authenticate, updateUser)                      // Private
  .delete(authenticate, authorize('admin'), deleteUser); // Admin only

module.exports = router;
```

**routes/authRoutes.js**
```javascript
const express = require('express');
const router = express.Router();

// @desc    Login
// @route   POST /api/auth/login
router.post('/login', (req, res) => {
  const { email, password } = req.body;

  if (email === 'admin@email.com' && password === 'password123') {
    res.json({
      success: true,
      token: 'Bearer secret123',
      user: { id: 1, name: 'John', role: 'admin' }
    });
  } else {
    res.status(401).json({ success: false, message: 'Invalid credentials' });
  }
});

// @desc    Signup
// @route   POST /api/auth/signup
router.post('/signup', (req, res) => {
  const { name, email, password } = req.body;

  res.status(201).json({
    success: true,
    message: 'Account created successfully',
    token: 'Bearer newtoken456'
  });
});

module.exports = router;
```

**index.js (Main Entry Point)**
```javascript
const express = require('express');
const logger = require('./middleware/logger');
const { notFound, errorHandler } = require('./middleware/errorHandler');
const userRoutes = require('./routes/userRoutes');
const authRoutes = require('./routes/authRoutes');

const app = express();

// Body parsing middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Logger middleware
app.use(logger);

// Mount routes
app.get('/', (req, res) => {
  res.json({ message: 'API is running...', version: '1.0.0' });
});

app.use('/api/users', userRoutes);
app.use('/api/auth', authRoutes);

// Error handling
app.use(notFound);
app.use(errorHandler);

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

---

## Chapter 7: Serving Static Files & Template Engines

### 📖 Theory
- **Static Files:** CSS, JavaScript, images, fonts, etc. Express has built-in middleware `express.static()` to serve these files.
- **Template Engines:** Allow you to render dynamic HTML pages on the server. Popular options: EJS, Pug, Handlebars.

### 📁 Project Structure
```
project/
├── public/
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── main.js
│   └── images/
│       └── logo.png
├── views/
│   ├── partials/
│   │   ├── header.ejs
│   │   └── footer.ejs
│   ├── index.ejs
│   ├── about.ejs
│   └── users.ejs
└── index.js
```

```bash
npm install ejs
```

### 💻 Example

**public/css/style.css**
```css
* { margin: 0; padding: 0; box-sizing: border-box; }
body { font-family: 'Segoe UI', sans-serif; background: #f4f4f4; }
.container { max-width: 800px; margin: 0 auto; padding: 20px; }
nav { background: #333; color: #fff; padding: 15px; }
nav a { color: #fff; text-decoration: none; margin-right: 15px; }
.card { background: #fff; padding: 15px; margin: 10px 0; border-radius: 8px; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
h1 { color: #333; margin-bottom: 15px; }
```

**views/partials/header.ejs**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title><%= title %> | My Express App</title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <nav>
    <a href="/">Home</a>
    <a href="/about">About</a>
    <a href="/users">Users</a>
  </nav>
  <div class="container">
```

**views/partials/footer.ejs**
```html
  </div>
  <script src="/js/main.js"></script>
</body>
</html>
```

**views/index.ejs**
```html
<%- include('partials/header', { title: 'Home' }) %>

<h1>Welcome to Express.js!</h1>
<p>Current Time: <%= new Date().toLocaleString() %></p>

<div class="card">
  <h2>Server Info</h2>
  <p>Environment: <%= env %></p>
  <p>Visits: <%= visitCount %></p>
</div>

<%- include('partials/footer') %>
```

**views/users.ejs**
```html
<%- include('partials/header', { title: 'Users' }) %>

<h1>Users (<%= users.length %>)</h1>

<% if (users.length > 0) { %>
  <% users.forEach(user => { %>
    <div class="card">
      <h3><%= user.name %></h3>
      <p>Email: <%= user.email %></p>
      <p>Age: <%= user.age || 'N/A' %></p>
    </div>
  <% }) %>
<% } else { %>
  <p>No users found.</p>
<% } %>

<%- include('partials/footer') %>
```

**index.js**
```javascript
const express = require('express');
const path = require('path');
const app = express();

// Set template engine
app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));

// Serve static files
app.use(express.static(path.join(__dirname, 'public')));

let visitCount = 0;

// Routes rendering views
app.get('/', (req, res) => {
  visitCount++;
  res.render('index', {
    env: process.env.NODE_ENV || 'development',
    visitCount
  });
});

app.get('/about', (req, res) => {
  res.render('about', { title: 'About' });
});

app.get('/users', (req, res) => {
  const users = [
    { name: 'Alice', email: 'alice@email.com', age: 28 },
    { name: 'Bob', email: 'bob@email.com', age: 32 },
    { name: 'Charlie', email: 'charlie@email.com', age: 25 }
  ];

  res.render('users', { users });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

# PHASE 2: INTERMEDIATE

---

## Chapter 8: Third-Party Middleware

### 📖 Theory
The Express ecosystem has a rich collection of third-party middleware packages that add crucial functionality. These are battle-tested, production-ready solutions for common needs.

```bash
npm install cors morgan helmet express-rate-limit compression cookie-parser
```

### 💻 Example

```javascript
const express = require('express');
const cors = require('cors');           // Cross-Origin Resource Sharing
const morgan = require('morgan');       // HTTP request logger
const helmet = require('helmet');       // Security headers
const rateLimit = require('express-rate-limit');  // Rate limiting
const compression = require('compression');        // Gzip compression
const cookieParser = require('cookie-parser');     // Parse cookies

const app = express();

// ============================================
// HELMET - Secure HTTP headers
// ============================================
// Sets various HTTP headers to help protect your app
app.use(helmet());

// ============================================
// CORS - Cross-Origin Resource Sharing
// ============================================

// Simple: Allow all origins
// app.use(cors());

// Advanced: Configure CORS
app.use(cors({
  origin: ['http://localhost:3001', 'https://myapp.com'], // Allowed origins
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  credentials: true,            // Allow cookies
  maxAge: 86400                 // Cache preflight for 24 hours
}));

// ============================================
// MORGAN - HTTP request logger
// ============================================

// 'dev' format: colored status, method, url, response time
app.use(morgan('dev'));

// Custom format
// app.use(morgan(':method :url :status :res[content-length] - :response-time ms'));

// Log to file (for production)
// const fs = require('fs');
// const accessLogStream = fs.createWriteStream('./access.log', { flags: 'a' });
// app.use(morgan('combined', { stream: accessLogStream }));

// ============================================
// RATE LIMITING - Prevent abuse
// ============================================

// Global rate limit
const globalLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,    // 15 minutes
  max: 100,                     // Max 100 requests per window
  message: {
    success: false,
    message: 'Too many requests, please try again after 15 minutes'
  },
  standardHeaders: true,        // Return rate limit info in headers
  legacyHeaders: false
});

app.use(globalLimiter);

// Stricter limit for auth routes
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,                       // Only 5 login attempts per 15 min
  message: {
    success: false,
    message: 'Too many login attempts. Please try again later.'
  }
});

// ============================================
// COMPRESSION - Gzip responses
// ============================================
app.use(compression());

// ============================================
// COOKIE PARSER
// ============================================
app.use(cookieParser('mySecretKey'));  // Secret for signed cookies

// ============================================
// Body Parsers
// ============================================
app.use(express.json({ limit: '10mb' }));           // Limit body size
app.use(express.urlencoded({ extended: true }));

// ============================================
// ROUTES
// ============================================

app.get('/', (req, res) => {
  res.json({ message: 'API with all middleware active!' });
});

// Auth routes with strict rate limiting
app.post('/api/auth/login', authLimiter, (req, res) => {
  res.json({ message: 'Login endpoint' });
});

// Cookie examples
app.get('/set-cookie', (req, res) => {
  // Regular cookie
  res.cookie('username', 'john_doe', {
    maxAge: 24 * 60 * 60 * 1000,  // 1 day
    httpOnly: true,                 // Not accessible via JavaScript
    secure: false,                  // Set true in production (HTTPS only)
    sameSite: 'strict'
  });

  // Signed cookie (tamper-proof)
  res.cookie('sessionId', 'abc123', { signed: true });

  res.json({ message: 'Cookies set!' });
});

app.get('/get-cookie', (req, res) => {
  res.json({
    cookies: req.cookies,
    signedCookies: req.signedCookies
  });
});

app.get('/clear-cookie', (req, res) => {
  res.clearCookie('username');
  res.json({ message: 'Cookie cleared' });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## Chapter 9: Environment Variables & Configuration

### 📖 Theory
**Environment variables** let you configure your application without changing code. They store sensitive information (database URIs, API keys, secrets) and environment-specific settings (port, log level).

```bash
npm install dotenv
```

### 💻 Example

**.env** (NEVER commit this to git!)
```env
NODE_ENV=development
PORT=3000

# Database
DB_HOST=localhost
DB_PORT=27017
DB_NAME=express_mastery
DB_URI=mongodb://localhost:27017/express_mastery

# JWT
JWT_SECRET=my_super_secret_jwt_key_12345
JWT_EXPIRE=30d
JWT_COOKIE_EXPIRE=30

# API Keys
SMTP_HOST=smtp.mailtrap.io
SMTP_PORT=587
SMTP_USER=your_smtp_user
SMTP_PASS=your_smtp_pass

# Rate Limiting
RATE_LIMIT_WINDOW=15
RATE_LIMIT_MAX=100
```

**.gitignore**
```
node_modules/
.env
*.log
```

**config/config.js**
```javascript
const dotenv = require('dotenv');
const path = require('path');

// Load env vars
dotenv.config({ path: path.join(__dirname, '..', '.env') });

const config = {
  env: process.env.NODE_ENV || 'development',
  port: parseInt(process.env.PORT, 10) || 3000,

  db: {
    uri: process.env.DB_URI || 'mongodb://localhost:27017/myapp',
    host: process.env.DB_HOST || 'localhost',
    port: parseInt(process.env.DB_PORT, 10) || 27017,
    name: process.env.DB_NAME || 'myapp'
  },

  jwt: {
    secret: process.env.JWT_SECRET || 'fallback-secret',
    expire: process.env.JWT_EXPIRE || '7d',
    cookieExpire: parseInt(process.env.JWT_COOKIE_EXPIRE, 10) || 7
  },

  smtp: {
    host: process.env.SMTP_HOST,
    port: parseInt(process.env.SMTP_PORT, 10) || 587,
    user: process.env.SMTP_USER,
    pass: process.env.SMTP_PASS
  },

  rateLimit: {
    windowMs: parseInt(process.env.RATE_LIMIT_WINDOW, 10) * 60 * 1000 || 900000,
    max: parseInt(process.env.RATE_LIMIT_MAX, 10) || 100
  }
};

// Validation
const requiredEnvVars = ['JWT_SECRET'];
requiredEnvVars.forEach(envVar => {
  if (!process.env[envVar]) {
    console.error(`ERROR: Environment variable ${envVar} is not set`);
    process.exit(1);
  }
});

module.exports = config;
```

**index.js**
```javascript
const express = require('express');
const config = require('./config/config');

const app = express();

app.get('/', (req, res) => {
  res.json({
    message: 'Config loaded!',
    environment: config.env,
    port: config.port
  });
});

app.listen(config.port, () => {
  console.log(`Server running in ${config.env} mode on port ${config.port}`);
});
```

---

## Chapter 10: MongoDB with Mongoose (Database Integration)

### 📖 Theory
**MongoDB** is a NoSQL database that stores data in flexible, JSON-like documents. **Mongoose** is an ODM (Object Data Modeling) library that provides schema validation, type casting, query building, and business logic hooks.

**Key Concepts:**
- **Schema:** Defines the structure of documents
- **Model:** A class compiled from a Schema; used to create/read/update/delete documents
- **Document:** An instance of a Model

```bash
npm install mongoose
```

### 📁 Project Structure
```
project/
├── config/
│   └── db.js
├── models/
│   └── User.js
├── controllers/
│   └── userController.js
├── routes/
│   └── userRoutes.js
├── middleware/
│   └── asyncHandler.js
├── .env
└── server.js
```

### 💻 Example

**config/db.js**
```javascript
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    const conn = await mongoose.connect(process.env.DB_URI || 'mongodb://localhost:27017/express_mastery');
    console.log(`MongoDB Connected: ${conn.connection.host}`);
  } catch (error) {
    console.error(`Error: ${error.message}`);
    process.exit(1);
  }
};

module.exports = connectDB;
```

**models/User.js**
```javascript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema(
  {
    name: {
      type: String,
      required: [true, 'Please add a name'],
      trim: true,
      maxlength: [50, 'Name cannot be more than 50 characters']
    },
    email: {
      type: String,
      required: [true, 'Please add an email'],
      unique: true,
      lowercase: true,
      trim: true,
      match: [
        /^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/,
        'Please add a valid email'
      ]
    },
    age: {
      type: Number,
      min: [1, 'Age must be at least 1'],
      max: [150, 'Age cannot exceed 150']
    },
    role: {
      type: String,
      enum: ['user', 'admin', 'moderator'],
      default: 'user'
    },
    isActive: {
      type: Boolean,
      default: true
    },
    hobbies: [String],  // Array of strings
    address: {
      street: String,
      city: String,
      state: String,
      zipCode: String,
      country: { type: String, default: 'India' }
    },
    profilePicture: {
      type: String,
      default: 'default-avatar.png'
    }
  },
  {
    timestamps: true,  // Adds createdAt and updatedAt
    toJSON: { virtuals: true },
    toObject: { virtuals: true }
  }
);

// ============================================
// VIRTUAL FIELDS (not stored in DB)
// ============================================
userSchema.virtual('profile').get(function () {
  return `${this.name} (${this.email})`;
});

// ============================================
// INDEXES (for query performance)
// ============================================
userSchema.index({ email: 1 });
userSchema.index({ name: 'text' });  // Text search index

// ============================================
// PRE-SAVE MIDDLEWARE (runs before saving)
// ============================================
userSchema.pre('save', function (next) {
  console.log(`About to save user: ${this.name}`);
  // You can modify the document here (e.g., hash password)
  next();
});

// ============================================
// POST-SAVE MIDDLEWARE (runs after saving)
// ============================================
userSchema.post('save', function (doc) {
  console.log(`User saved: ${doc.name}`);
});

// ============================================
// INSTANCE METHODS (available on documents)
// ============================================
userSchema.methods.getPublicProfile = function () {
  return {
    id: this._id,
    name: this.name,
    email: this.email,
    role: this.role
  };
};

// ============================================
// STATIC METHODS (available on the Model)
// ============================================
userSchema.statics.findByEmail = function (email) {
  return this.findOne({ email });
};

userSchema.statics.findActiveUsers = function () {
  return this.find({ isActive: true });
};

const User = mongoose.model('User', userSchema);

module.exports = User;
```

**middleware/asyncHandler.js**
```javascript
// Wraps async functions so we don't need try-catch in every controller
const asyncHandler = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};

module.exports = asyncHandler;
```

**controllers/userController.js**
```javascript
const User = require('../models/User');
const asyncHandler = require('../middleware/asyncHandler');

// @desc    Get all users with filtering, sorting, pagination
// @route   GET /api/users
// @access  Public
exports.getUsers = asyncHandler(async (req, res) => {
  // Copy query
  const reqQuery = { ...req.query };

  // Fields to exclude from filtering
  const removeFields = ['select', 'sort', 'page', 'limit', 'search'];
  removeFields.forEach(param => delete reqQuery[param]);

  // Create operators ($gt, $gte, $lt, $lte, $in)
  let queryStr = JSON.stringify(reqQuery);
  queryStr = queryStr.replace(/\b(gt|gte|lt|lte|in)\b/g, match => `$${match}`);

  let query = User.find(JSON.parse(queryStr));

  // Text search
  if (req.query.search) {
    query = User.find({ $text: { $search: req.query.search } });
  }

  // Select specific fields
  if (req.query.select) {
    const fields = req.query.select.split(',').join(' ');
    query = query.select(fields);
  }

  // Sort
  if (req.query.sort) {
    const sortBy = req.query.sort.split(',').join(' ');
    query = query.sort(sortBy);
  } else {
    query = query.sort('-createdAt');
  }

  // Pagination
  const page = parseInt(req.query.page, 10) || 1;
  const limit = parseInt(req.query.limit, 10) || 10;
  const startIndex = (page - 1) * limit;
  const total = await User.countDocuments(JSON.parse(queryStr));

  query = query.skip(startIndex).limit(limit);

  // Execute query
  const users = await query;

  // Pagination result
  const pagination = {
    currentPage: page,
    totalPages: Math.ceil(total / limit),
    totalDocuments: total,
    limit
  };

  if (startIndex + limit < total) {
    pagination.nextPage = page + 1;
  }
  if (startIndex > 0) {
    pagination.prevPage = page - 1;
  }

  res.status(200).json({
    success: true,
    count: users.length,
    pagination,
    data: users
  });
});

// @desc    Get single user
// @route   GET /api/users/:id
exports.getUser = asyncHandler(async (req, res) => {
  const user = await User.findById(req.params.id);

  if (!user) {
    return res.status(404).json({
      success: false,
      message: `User not found with id ${req.params.id}`
    });
  }

  res.status(200).json({ success: true, data: user });
});

// @desc    Create user
// @route   POST /api/users
exports.createUser = asyncHandler(async (req, res) => {
  const user = await User.create(req.body);

  res.status(201).json({
    success: true,
    message: 'User created successfully',
    data: user
  });
});

// @desc    Update user
// @route   PUT /api/users/:id
exports.updateUser = asyncHandler(async (req, res) => {
  const user = await User.findByIdAndUpdate(req.params.id, req.body, {
    new: true,            // Return updated document
    runValidators: true   // Run schema validators on update
  });

  if (!user) {
    return res.status(404).json({
      success: false,
      message: `User not found with id ${req.params.id}`
    });
  }

  res.status(200).json({
    success: true,
    message: 'User updated successfully',
    data: user
  });
});

// @desc    Delete user
// @route   DELETE /api/users/:id
exports.deleteUser = asyncHandler(async (req, res) => {
  const user = await User.findByIdAndDelete(req.params.id);

  if (!user) {
    return res.status(404).json({
      success: false,
      message: `User not found with id ${req.params.id}`
    });
  }

  res.status(200).json({
    success: true,
    message: 'User deleted successfully',
    data: {}
  });
});
```

**routes/userRoutes.js**
```javascript
const express = require('express');
const router = express.Router();
const {
  getUsers, getUser, createUser, updateUser, deleteUser
} = require('../controllers/userController');

router.route('/')
  .get(getUsers)
  .post(createUser);

router.route('/:id')
  .get(getUser)
  .put(updateUser)
  .delete(deleteUser);

module.exports = router;
```

**server.js**
```javascript
require('dotenv').config();
const express = require('express');
const connectDB = require('./config/db');
const userRoutes = require('./routes/userRoutes');

const app = express();

// Connect to Database
connectDB();

// Body parser
app.use(express.json());

// Routes
app.use('/api/users', userRoutes);

// Error handler
app.use((err, req, res, next) => {
  console.error(err.stack);

  // Mongoose validation error
  if (err.name === 'ValidationError') {
    const messages = Object.values(err.errors).map(val => val.message);
    return res.status(400).json({ success: false, errors: messages });
  }

  // Mongoose duplicate key
  if (err.code === 11000) {
    return res.status(400).json({
      success: false,
      message: 'Duplicate field value entered'
    });
  }

  // Mongoose bad ObjectId
  if (err.name === 'CastError') {
    return res.status(400).json({
      success: false,
      message: `Invalid ID format`
    });
  }

  res.status(500).json({
    success: false,
    message: err.message || 'Server Error'
  });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

**Test with these URLs:**
```
GET    /api/users                          → Get all users
GET    /api/users?age[gte]=25&sort=-name   → Filter age >= 25, sort by name desc
GET    /api/users?select=name,email&limit=5 → Select specific fields, limit 5
GET    /api/users?page=2&limit=3            → Pagination
POST   /api/users                          → Create user
GET    /api/users/:id                      → Get single user
PUT    /api/users/:id                      → Update user
DELETE /api/users/:id                      → Delete user
```

---

## Chapter 11: Error Handling (Professional Way)

### 📖 Theory
Professional applications need centralized, consistent error handling. We create:
1. **Custom Error Class** — Extends native Error with status codes
2. **Async Handler** — Catches async errors automatically
3. **Global Error Handler** — Processes all errors in one place

### 💻 Example

**utils/AppError.js**
```javascript
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
    this.isOperational = true;  // Operational errors (expected)

    Error.captureStackTrace(this, this.constructor);
  }
}

module.exports = AppError;
```

**middleware/errorMiddleware.js**
```javascript
const AppError = require('../utils/AppError');

// Handle specific Mongoose errors
const handleCastErrorDB = (err) => {
  const message = `Invalid ${err.path}: ${err.value}`;
  return new AppError(message, 400);
};

const handleDuplicateFieldsDB = (err) => {
  const field = Object.keys(err.keyValue)[0];
  const value = err.keyValue[field];
  const message = `Duplicate field value: "${value}" for field "${field}". Please use another value.`;
  return new AppError(message, 400);
};

const handleValidationErrorDB = (err) => {
  const errors = Object.values(err.errors).map(el => el.message);
  const message = `Invalid input data: ${errors.join('. ')}`;
  return new AppError(message, 400);
};

const handleJWTError = () =>
  new AppError('Invalid token. Please log in again.', 401);

const handleJWTExpiredError = () =>
  new AppError('Token expired. Please log in again.', 401);

// ============================================
// DEVELOPMENT ERROR RESPONSE
// ============================================
const sendErrorDev = (err, res) => {
  res.status(err.statusCode).json({
    success: false,
    status: err.status,
    message: err.message,
    error: err,
    stack: err.stack
  });
};

// ============================================
// PRODUCTION ERROR RESPONSE
// ============================================
const sendErrorProd = (err, res) => {
  // Operational, trusted error: send message to client
  if (err.isOperational) {
    res.status(err.statusCode).json({
      success: false,
      status: err.status,
      message: err.message
    });
  }
  // Programming or unknown error: don't leak details
  else {
    console.error('ERROR 💥:', err);
    res.status(500).json({
      success: false,
      status: 'error',
      message: 'Something went very wrong!'
    });
  }
};

// ============================================
// GLOBAL ERROR HANDLER
// ============================================
const globalErrorHandler = (err, req, res, next) => {
  err.statusCode = err.statusCode || 500;
  err.status = err.status || 'error';

  if (process.env.NODE_ENV === 'development') {
    sendErrorDev(err, res);
  } else {
    let error = { ...err };
    error.message = err.message;

    if (err.name === 'CastError') error = handleCastErrorDB(err);
    if (err.code === 11000) error = handleDuplicateFieldsDB(err);
    if (err.name === 'ValidationError') error = handleValidationErrorDB(err);
    if (err.name === 'JsonWebTokenError') error = handleJWTError();
    if (err.name === 'TokenExpiredError') error = handleJWTExpiredError();

    sendErrorProd(error, res);
  }
};

module.exports = globalErrorHandler;
```

**Usage in controllers:**
```javascript
const AppError = require('../utils/AppError');
const asyncHandler = require('../middleware/asyncHandler');

exports.getUser = asyncHandler(async (req, res, next) => {
  const user = await User.findById(req.params.id);

  if (!user) {
    return next(new AppError('No user found with that ID', 404));
  }

  res.status(200).json({ success: true, data: user });
});
```

---

## Chapter 12: Authentication & Authorization (JWT)

### 📖 Theory
**Authentication** = Who are you? (Login/Signup)
**Authorization** = What can you do? (Role-based access)

**JWT (JSON Web Token)** is a self-contained token that contains encoded user information. It consists of three parts: Header.Payload.Signature

**Flow:**
1. User sends credentials → Server validates → Server creates JWT → Sends token to client
2. Client stores token → Sends token in headers with each request → Server verifies token

```bash
npm install jsonwebtoken bcryptjs
```

### 💻 Example

**models/User.js (Updated with auth)**
```javascript
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');
const jwt = require('jsonwebtoken');

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Please add a name'],
    trim: true
  },
  email: {
    type: String,
    required: [true, 'Please add an email'],
    unique: true,
    lowercase: true,
    match: [/^\w+([\.-]?\w+)*@\w+([\.-]?\w+)*(\.\w{2,3})+$/, 'Please add a valid email']
  },
  password: {
    type: String,
    required: [true, 'Please add a password'],
    minlength: [6, 'Password must be at least 6 characters'],
    select: false  // Don't return password in queries by default
  },
  role: {
    type: String,
    enum: ['user', 'admin', 'moderator'],
    default: 'user'
  },
  isActive: {
    type: Boolean,
    default: true
  },
  passwordChangedAt: Date,
  passwordResetToken: String,
  passwordResetExpires: Date
}, { timestamps: true });

// ============================================
// HASH PASSWORD BEFORE SAVING
// ============================================
userSchema.pre('save', async function (next) {
  // Only hash if password was modified
  if (!this.isModified('password')) return next();

  const salt = await bcrypt.genSalt(12);
  this.password = await bcrypt.hash(this.password, salt);
  next();
});

// ============================================
// COMPARE PASSWORD METHOD
// ============================================
userSchema.methods.comparePassword = async function (candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
};

// ============================================
// GENERATE JWT TOKEN
// ============================================
userSchema.methods.generateAuthToken = function () {
  return jwt.sign(
    { id: this._id, role: this.role },
    process.env.JWT_SECRET,
    { expiresIn: process.env.JWT_EXPIRE || '30d' }
  );
};

// ============================================
// CHECK IF PASSWORD CHANGED AFTER TOKEN ISSUED
// ============================================
userSchema.methods.changedPasswordAfter = function (JWTTimestamp) {
  if (this.passwordChangedAt) {
    const changedTimestamp = parseInt(
      this.passwordChangedAt.getTime() / 1000, 10
    );
    return JWTTimestamp < changedTimestamp;
  }
  return false;
};

module.exports = mongoose.model('User', userSchema);
```

**middleware/auth.js**
```javascript
const jwt = require('jsonwebtoken');
const User = require('../models/User');
const AppError = require('../utils/AppError');
const asyncHandler = require('./asyncHandler');

// ============================================
// PROTECT ROUTES (Authentication)
// ============================================
exports.protect = asyncHandler(async (req, res, next) => {
  let token;

  // Check for token in headers
  if (req.headers.authorization && req.headers.authorization.startsWith('Bearer')) {
    token = req.headers.authorization.split(' ')[1];
  }
  // Or check in cookies
  else if (req.cookies && req.cookies.token) {
    token = req.cookies.token;
  }

  if (!token) {
    return next(new AppError('Not authorized to access this route. Please log in.', 401));
  }

  try {
    // Verify token
    const decoded = jwt.verify(token, process.env.JWT_SECRET);

    // Check if user still exists
    const currentUser = await User.findById(decoded.id);
    if (!currentUser) {
      return next(new AppError('The user belonging to this token no longer exists.', 401));
    }

    // Check if user changed password after token was issued
    if (currentUser.changedPasswordAfter(decoded.iat)) {
      return next(new AppError('User recently changed password. Please log in again.', 401));
    }

    // Grant access
    req.user = currentUser;
    next();
  } catch (error) {
    return next(new AppError('Not authorized. Invalid token.', 401));
  }
});

// ============================================
// AUTHORIZE BY ROLE (Authorization)
// ============================================
exports.authorize = (...roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return next(
        new AppError(
          `User role '${req.user.role}' is not authorized to access this route`,
          403
        )
      );
    }
    next();
  };
};
```

**controllers/authController.js**
```javascript
const User = require('../models/User');
const AppError = require('../utils/AppError');
const asyncHandler = require('../middleware/asyncHandler');

// Helper: Create token and send response
const sendTokenResponse = (user, statusCode, res) => {
  const token = user.generateAuthToken();

  const options = {
    expires: new Date(
      Date.now() + (parseInt(process.env.JWT_COOKIE_EXPIRE) || 30) * 24 * 60 * 60 * 1000
    ),
    httpOnly: true,
    secure: process.env.NODE_ENV === 'production',
    sameSite: 'strict'
  };

  res.status(statusCode)
    .cookie('token', token, options)
    .json({
      success: true,
      token,
      user: {
        id: user._id,
        name: user.name,
        email: user.email,
        role: user.role
      }
    });
};

// @desc    Register user
// @route   POST /api/auth/register
// @access  Public
exports.register = asyncHandler(async (req, res, next) => {
  const { name, email, password, role } = req.body;

  // Check if user exists
  const existingUser = await User.findOne({ email });
  if (existingUser) {
    return next(new AppError('Email already registered', 400));
  }

  const user = await User.create({
    name,
    email,
    password,
    role: role || 'user'  // Don't let users set admin role
  });

  sendTokenResponse(user, 201, res);
});

// @desc    Login user
// @route   POST /api/auth/login
// @access  Public
exports.login = asyncHandler(async (req, res, next) => {
  const { email, password } = req.body;

  // Validate
  if (!email || !password) {
    return next(new AppError('Please provide email and password', 400));
  }

  // Find user and include password (normally excluded)
  const user = await User.findOne({ email }).select('+password');

  if (!user) {
    return next(new AppError('Invalid credentials', 401));
  }

  // Check password
  const isMatch = await user.comparePassword(password);
  if (!isMatch) {
    return next(new AppError('Invalid credentials', 401));
  }

  sendTokenResponse(user, 200, res);
});

// @desc    Logout / clear cookie
// @route   POST /api/auth/logout
// @access  Private
exports.logout = asyncHandler(async (req, res, next) => {
  res.cookie('token', 'none', {
    expires: new Date(Date.now() + 10 * 1000),  // 10 seconds
    httpOnly: true
  });

  res.status(200).json({ success: true, message: 'Logged out successfully' });
});

// @desc    Get current logged-in user
// @route   GET /api/auth/me
// @access  Private
exports.getMe = asyncHandler(async (req, res, next) => {
  const user = await User.findById(req.user.id);

  res.status(200).json({ success: true, data: user });
});

// @desc    Update password
// @route   PUT /api/auth/updatepassword
// @access  Private
exports.updatePassword = asyncHandler(async (req, res, next) => {
  const user = await User.findById(req.user.id).select('+password');

  // Check current password
  if (!(await user.comparePassword(req.body.currentPassword))) {
    return next(new AppError('Current password is incorrect', 401));
  }

  user.password = req.body.newPassword;
  user.passwordChangedAt = Date.now();
  await user.save();

  sendTokenResponse(user, 200, res);
});
```

**routes/authRoutes.js**
```javascript
const express = require('express');
const router = express.Router();
const { protect } = require('../middleware/auth');
const {
  register, login, logout, getMe, updatePassword
} = require('../controllers/authController');

router.post('/register', register);
router.post('/login', login);
router.post('/logout', protect, logout);
router.get('/me', protect, getMe);
router.put('/updatepassword', protect, updatePassword);

module.exports = router;
```

**Protected routes example:**
```javascript
// routes/userRoutes.js
const { protect, authorize } = require('../middleware/auth');

// All routes below this require authentication
router.use(protect);

router.route('/')
  .get(getUsers)                           // Any authenticated user
  .post(authorize('admin'), createUser);    // Admin only

router.route('/:id')
  .get(getUser)
  .put(authorize('admin', 'moderator'), updateUser)
  .delete(authorize('admin'), deleteUser);
```

---

## Chapter 13: File Upload

### 📖 Theory
Express doesn't handle file uploads natively. **Multer** is the most popular middleware for handling `multipart/form-data` (file uploads).

```bash
npm install multer sharp
```

### 💻 Example

**middleware/upload.js**
```javascript
const multer = require('multer');
const path = require('path');
const AppError = require('../utils/AppError');

// ============================================
// OPTION 1: Disk Storage (save to folder)
// ============================================
const diskStorage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, 'public/uploads/');
  },
  filename: function (req, file, cb) {
    // Generate unique filename
    const uniqueSuffix = `${Date.now()}-${Math.round(Math.random() * 1E9)}`;
    const ext = path.extname(file.originalname);
    cb(null, `${file.fieldname}-${uniqueSuffix}${ext}`);
    // Result: photo-1698234567890-123456789.jpg
  }
});

// ============================================
// OPTION 2: Memory Storage (for processing before saving)
// ============================================
const memoryStorage = multer.memoryStorage();

// ============================================
// FILE FILTER (validate file types)
// ============================================
const imageFilter = (req, file, cb) => {
  const allowedTypes = /jpeg|jpg|png|gif|webp/;
  const extname = allowedTypes.test(path.extname(file.originalname).toLowerCase());
  const mimetype = allowedTypes.test(file.mimetype);

  if (extname && mimetype) {
    cb(null, true);
  } else {
    cb(new AppError('Only image files (jpeg, jpg, png, gif, webp) are allowed!', 400), false);
  }
};

const documentFilter = (req, file, cb) => {
  const allowedTypes = /pdf|doc|docx|txt|xlsx/;
  const extname = allowedTypes.test(path.extname(file.originalname).toLowerCase());

  if (extname) {
    cb(null, true);
  } else {
    cb(new AppError('Only document files are allowed!', 400), false);
  }
};

// ============================================
// UPLOAD CONFIGURATIONS
// ============================================

// Single image upload
const uploadSingleImage = multer({
  storage: diskStorage,
  fileFilter: imageFilter,
  limits: {
    fileSize: 5 * 1024 * 1024  // 5MB max
  }
}).single('photo');  // 'photo' is the field name

// Multiple images upload
const uploadMultipleImages = multer({
  storage: diskStorage,
  fileFilter: imageFilter,
  limits: { fileSize: 5 * 1024 * 1024 }
}).array('photos', 5);  // max 5 files

// Mixed fields upload
const uploadMixed = multer({
  storage: diskStorage,
  limits: { fileSize: 10 * 1024 * 1024 }
}).fields([
  { name: 'avatar', maxCount: 1 },
  { name: 'documents', maxCount: 3 },
  { name: 'gallery', maxCount: 10 }
]);

// Memory storage (for image processing)
const uploadForProcessing = multer({
  storage: memoryStorage,
  fileFilter: imageFilter,
  limits: { fileSize: 5 * 1024 * 1024 }
}).single('photo');

module.exports = {
  uploadSingleImage,
  uploadMultipleImages,
  uploadMixed,
  uploadForProcessing
};
```

**controllers/uploadController.js**
```javascript
const path = require('path');
const sharp = require('sharp');
const asyncHandler = require('../middleware/asyncHandler');
const AppError = require('../utils/AppError');

// @desc    Upload single image
// @route   POST /api/upload/single
exports.uploadSingle = asyncHandler(async (req, res, next) => {
  if (!req.file) {
    return next(new AppError('Please upload a file', 400));
  }

  res.status(200).json({
    success: true,
    message: 'File uploaded successfully',
    data: {
      filename: req.file.filename,
      originalname: req.file.originalname,
      mimetype: req.file.mimetype,
      size: `${(req.file.size / 1024).toFixed(2)} KB`,
      path: `/uploads/${req.file.filename}`,
      url: `${req.protocol}://${req.get('host')}/uploads/${req.file.filename}`
    }
  });
});

// @desc    Upload multiple images
// @route   POST /api/upload/multiple
exports.uploadMultiple = asyncHandler(async (req, res, next) => {
  if (!req.files || req.files.length === 0) {
    return next(new AppError('Please upload at least one file', 400));
  }

  const files = req.files.map(file => ({
    filename: file.filename,
    originalname: file.originalname,
    size: `${(file.size / 1024).toFixed(2)} KB`,
    url: `${req.protocol}://${req.get('host')}/uploads/${file.filename}`
  }));

  res.status(200).json({
    success: true,
    count: files.length,
    data: files
  });
});

// @desc    Upload and resize image
// @route   POST /api/upload/resize
exports.uploadAndResize = asyncHandler(async (req, res, next) => {
  if (!req.file) {
    return next(new AppError('Please upload a file', 400));
  }

  const filename = `photo-${Date.now()}.jpeg`;

  // Process image with Sharp
  await sharp(req.file.buffer)
    .resize(500, 500, {
      fit: 'cover',
      position: 'center'
    })
    .toFormat('jpeg')
    .jpeg({ quality: 90 })
    .toFile(`public/uploads/${filename}`);

  res.status(200).json({
    success: true,
    message: 'Image uploaded and resized',
    data: {
      filename,
      url: `${req.protocol}://${req.get('host')}/uploads/${filename}`
    }
  });
});
```

**routes/uploadRoutes.js**
```javascript
const express = require('express');
const router = express.Router();
const {
  uploadSingleImage,
  uploadMultipleImages,
  uploadForProcessing
} = require('../middleware/upload');
const {
  uploadSingle,
  uploadMultiple,
  uploadAndResize
} = require('../controllers/uploadController');

router.post('/single', uploadSingleImage, uploadSingle);
router.post('/multiple', uploadMultipleImages, uploadMultiple);
router.post('/resize', uploadForProcessing, uploadAndResize);

module.exports = router;
```

---

## Chapter 14: Data Validation with Express-Validator

### 📖 Theory
While Mongoose provides schema-level validation, you often need to validate incoming request data **before** it reaches your database. **express-validator** provides a comprehensive set of validators and sanitizers.

```bash
npm install express-validator
```

### 💻 Example

**middleware/validators.js**
```javascript
const { body, param, query, validationResult } = require('express-validator');

// ============================================
// VALIDATION RESULT HANDLER
// ============================================
const validate = (req, res, next) => {
  const errors = validationResult(req);

  if (!errors.isEmpty()) {
    return res.status(400).json({
      success: false,
      errors: errors.array().map(err => ({
        field: err.path,
        message: err.msg,
        value: err.value
      }))
    });
  }

  next();
};

// ============================================
// USER VALIDATION RULES
// ============================================
const registerValidation = [
  body('name')
    .trim()
    .notEmpty().withMessage('Name is required')
    .isLength({ min: 2, max: 50 }).withMessage('Name must be between 2-50 characters')
    .matches(/^[a-zA-Z\s]+$/).withMessage('Name can only contain letters and spaces'),

  body('email')
    .trim()
    .notEmpty().withMessage('Email is required')
    .isEmail().withMessage('Please provide a valid email')
    .normalizeEmail(),

  body('password')
    .notEmpty().withMessage('Password is required')
    .isLength({ min: 8 }).withMessage('Password must be at least 8 characters')
    .matches(/\d/).withMessage('Password must contain at least one number')
    .matches(/[A-Z]/).withMessage('Password must contain at least one uppercase letter')
    .matches(/[!@#$%^&*]/).withMessage('Password must contain at least one special character'),

  body('confirmPassword')
    .notEmpty().withMessage('Please confirm your password')
    .custom((value, { req }) => {
      if (value !== req.body.password) {
        throw new Error('Passwords do not match');
      }
      return true;
    }),

  body('age')
    .optional()
    .isInt({ min: 13, max: 120 }).withMessage('Age must be between 13 and 120'),

  body('phone')
    .optional()
    .isMobilePhone('any').withMessage('Please provide a valid phone number'),

  body('website')
    .optional()
    .isURL().withMessage('Please provide a valid URL'),

  validate  // Final middleware to check results
];

const loginValidation = [
  body('email')
    .trim()
    .notEmpty().withMessage('Email is required')
    .isEmail().withMessage('Please provide a valid email'),

  body('password')
    .notEmpty().withMessage('Password is required'),

  validate
];

// ============================================
// PRODUCT VALIDATION RULES
// ============================================
const createProductValidation = [
  body('name')
    .trim()
    .notEmpty().withMessage('Product name is required')
    .isLength({ min: 2, max: 100 }).withMessage('Name must be 2-100 characters'),

  body('price')
    .notEmpty().withMessage('Price is required')
    .isFloat({ min: 0.01 }).withMessage('Price must be greater than 0')
    .toFloat(),

  body('category')
    .notEmpty().withMessage('Category is required')
    .isIn(['electronics', 'clothing', 'books', 'food', 'other'])
    .withMessage('Invalid category'),

  body('description')
    .optional()
    .trim()
    .isLength({ max: 1000 }).withMessage('Description cannot exceed 1000 characters'),

  body('stock')
    .optional()
    .isInt({ min: 0 }).withMessage('Stock must be a non-negative integer')
    .toInt(),

  body('tags')
    .optional()
    .isArray().withMessage('Tags must be an array'),

  body('tags.*')
    .optional()
    .trim()
    .isString().withMessage('Each tag must be a string'),

  validate
];

// ============================================
// PARAM VALIDATION
// ============================================
const validateMongoId = [
  param('id')
    .isMongoId().withMessage('Invalid ID format'),
  validate
];

// ============================================
// QUERY VALIDATION
// ============================================
const validatePagination = [
  query('page')
    .optional()
    .isInt({ min: 1 }).withMessage('Page must be a positive integer')
    .toInt(),

  query('limit')
    .optional()
    .isInt({ min: 1, max: 100 }).withMessage('Limit must be between 1 and 100')
    .toInt(),

  query('sort')
    .optional()
    .isIn(['name', '-name', 'price', '-price', 'createdAt', '-createdAt'])
    .withMessage('Invalid sort field'),

  validate
];

module.exports = {
  registerValidation,
  loginValidation,
  createProductValidation,
  validateMongoId,
  validatePagination
};
```

**Usage in routes:**
```javascript
const express = require('express');
const router = express.Router();
const {
  registerValidation,
  loginValidation,
  validateMongoId,
  validatePagination
} = require('../middleware/validators');

router.post('/register', registerValidation, authController.register);
router.post('/login', loginValidation, authController.login);
router.get('/users', validatePagination, userController.getUsers);
router.get('/users/:id', validateMongoId, userController.getUser);
```

---

# PHASE 3: ADVANCED

---

## Chapter 15: Advanced Mongoose - Relationships & Population

### 📖 Theory
MongoDB is NoSQL but we often need relationships:
- **Referencing (Normalization):** Store ObjectId reference to another document
- **Embedding (Denormalization):** Store the related data directly inside the document
- **Virtual Populate:** Create a virtual field that references another collection

### 💻 Example: Blog App with Relationships

**models/User.js**
```javascript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  avatar: { type: String, default: 'default.png' }
}, {
  timestamps: true,
  toJSON: { virtuals: true },
  toObject: { virtuals: true }
});

// Virtual populate: Get all posts by this user (no data stored)
userSchema.virtual('posts', {
  ref: 'Post',           // The model to populate
  localField: '_id',     // Field in User
  foreignField: 'author', // Field in Post
  justOne: false          // We expect multiple posts
});

// Virtual populate: Get post count
userSchema.virtual('postCount', {
  ref: 'Post',
  localField: '_id',
  foreignField: 'author',
  count: true             // Only return count
});

module.exports = mongoose.model('User', userSchema);
```

**models/Post.js**
```javascript
const mongoose = require('mongoose');

const postSchema = new mongoose.Schema({
  title: {
    type: String,
    required: [true, 'Post title is required'],
    trim: true
  },
  content: {
    type: String,
    required: [true, 'Post content is required']
  },
  slug: {
    type: String,
    unique: true
  },
  // REFERENCE to User
  author: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: [true, 'Post must belong to a user']
  },
  // REFERENCE to Category
  category: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Category'
  },
  tags: [String],
  // EMBEDDED Sub-document (denormalized)
  metadata: {
    readTime: Number,
    wordCount: Number,
    views: { type: Number, default: 0 },
    likes: { type: Number, default: 0 }
  },
  status: {
    type: String,
    enum: ['draft', 'published', 'archived'],
    default: 'draft'
  },
  publishedAt: Date,
  featuredImage: String
}, {
  timestamps: true,
  toJSON: { virtuals: true },
  toObject: { virtuals: true }
});

// Virtual: comments for this post
postSchema.virtual('comments', {
  ref: 'Comment',
  localField: '_id',
  foreignField: 'post'
});

// Pre-save: Generate slug
postSchema.pre('save', function (next) {
  if (this.isModified('title')) {
    this.slug = this.title
      .toLowerCase()
      .replace(/[^a-zA-Z0-9]/g, '-')
      .replace(/-+/g, '-')
      .replace(/^-|-$/g, '');
  }

  // Calculate metadata
  if (this.isModified('content')) {
    const words = this.content.split(/\s+/).length;
    this.metadata.wordCount = words;
    this.metadata.readTime = Math.ceil(words / 200);  // 200 words per minute
  }

  next();
});

// Static: Find published posts
postSchema.statics.findPublished = function () {
  return this.find({ status: 'published' }).sort('-publishedAt');
};

// Index for better query performance
postSchema.index({ author: 1, status: 1 });
postSchema.index({ slug: 1 });
postSchema.index({ tags: 1 });
postSchema.index({ title: 'text', content: 'text' });

module.exports = mongoose.model('Post', postSchema);
```

**models/Comment.js**
```javascript
const mongoose = require('mongoose');

const commentSchema = new mongoose.Schema({
  content: {
    type: String,
    required: [true, 'Comment cannot be empty'],
    maxlength: [500, 'Comment cannot exceed 500 characters']
  },
  post: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Post',
    required: true
  },
  user: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'User',
    required: true
  },
  // Self-referencing for nested replies
  parentComment: {
    type: mongoose.Schema.Types.ObjectId,
    ref: 'Comment',
    default: null
  },
  likes: { type: Number, default: 0 }
}, { timestamps: true });

// Always populate user when querying comments
commentSchema.pre(/^find/, function (next) {
  this.populate({
    path: 'user',
    select: 'name avatar'
  });
  next();
});

module.exports = mongoose.model('Comment', commentSchema);
```

**controllers/postController.js**
```javascript
const Post = require('../models/Post');
const asyncHandler = require('../middleware/asyncHandler');
const AppError = require('../utils/AppError');

// @desc    Get all posts with population
exports.getPosts = asyncHandler(async (req, res) => {
  const posts = await Post.find({ status: 'published' })
    .populate({
      path: 'author',
      select: 'name avatar email'  // Only get these fields
    })
    .populate({
      path: 'category',
      select: 'name'
    })
    .populate({
      path: 'comments',
      select: 'content user createdAt',
      options: { sort: { createdAt: -1 }, limit: 5 },
      populate: {
        path: 'user',
        select: 'name avatar'  // Nested population
      }
    })
    .sort('-publishedAt')
    .lean();  // Return plain JS object (faster)

  res.json({ success: true, count: posts.length, data: posts });
});

// @desc    Get single post by slug
exports.getPostBySlug = asyncHandler(async (req, res, next) => {
  const post = await Post.findOne({ slug: req.params.slug })
    .populate('author', 'name avatar email')
    .populate('comments');

  if (!post) {
    return next(new AppError('Post not found', 404));
  }

  // Increment view count
  post.metadata.views += 1;
  await post.save({ validateBeforeSave: false });

  res.json({ success: true, data: post });
});

// @desc    Create post
exports.createPost = asyncHandler(async (req, res) => {
  req.body.author = req.user.id;

  const post = await Post.create(req.body);

  // Populate author before sending response
  await post.populate('author', 'name avatar');

  res.status(201).json({ success: true, data: post });
});

// @desc    Get user with their posts
exports.getUserWithPosts = asyncHandler(async (req, res) => {
  const User = require('../models/User');
  
  const user = await User.findById(req.params.userId)
    .populate({
      path: 'posts',
      match: { status: 'published' },  // Only published posts
      options: { sort: { createdAt: -1 } },
      select: 'title slug metadata.views createdAt'
    })
    .populate('postCount');

  res.json({ success: true, data: user });
});

// @desc    Aggregation: Get post statistics
exports.getPostStats = asyncHandler(async (req, res) => {
  const stats = await Post.aggregate([
    { $match: { status: 'published' } },
    {
      $group: {
        _id: '$author',
        totalPosts: { $sum: 1 },
        totalViews: { $sum: '$metadata.views' },
        totalLikes: { $sum: '$metadata.likes' },
        avgReadTime: { $avg: '$metadata.readTime' }
      }
    },
    {
      $lookup: {
        from: 'users',
        localField: '_id',
        foreignField: '_id',
        as: 'authorInfo'
      }
    },
    { $unwind: '$authorInfo' },
    {
      $project: {
        _id: 0,
        author: '$authorInfo.name',
        totalPosts: 1,
        totalViews: 1,
        totalLikes: 1,
        avgReadTime: { $round: ['$avgReadTime', 1] }
      }
    },
    { $sort: { totalPosts: -1 } }
  ]);

  res.json({ success: true, data: stats });
});
```

---

## Chapter 16: Advanced Middleware Patterns

### 📖 Theory
Beyond basic middleware, advanced patterns include factory functions, composable middleware, conditional middleware, and middleware chains with dependency injection.

### 💻 Example

```javascript
const express = require('express');
const app = express();

app.use(express.json());

// ============================================
// 1. FACTORY MIDDLEWARE (Configurable)
// ============================================

// Rate limiter factory
const createRateLimiter = ({ windowMs = 60000, max = 10, message = 'Too many requests' }) => {
  const requests = new Map();

  return (req, res, next) => {
    const key = req.ip;
    const now = Date.now();
    const windowStart = now - windowMs;

    if (!requests.has(key)) {
      requests.set(key, []);
    }

    // Clean old requests
    const userRequests = requests.get(key).filter(time => time > windowStart);
    requests.set(key, userRequests);

    if (userRequests.length >= max) {
      return res.status(429).json({
        success: false,
        message,
        retryAfter: Math.ceil((userRequests[0] + windowMs - now) / 1000)
      });
    }

    userRequests.push(now);
    next();
  };
};

// Usage with different configs
app.use('/api', createRateLimiter({ windowMs: 60000, max: 100 }));
app.use('/api/auth', createRateLimiter({ windowMs: 900000, max: 5, message: 'Too many auth attempts' }));

// ============================================
// 2. CACHING MIDDLEWARE
// ============================================

const createCache = (duration = 300) => {
  const cache = new Map();

  return (req, res, next) => {
    // Only cache GET requests
    if (req.method !== 'GET') {
      return next();
    }

    const key = req.originalUrl;
    const cached = cache.get(key);

    if (cached && Date.now() - cached.timestamp < duration * 1000) {
      console.log(`Cache HIT: ${key}`);
      return res.json({ ...cached.data, cached: true });
    }

    console.log(`Cache MISS: ${key}`);

    // Override res.json to capture response
    const originalJson = res.json.bind(res);
    res.json = (body) => {
      cache.set(key, { data: body, timestamp: Date.now() });
      return originalJson(body);
    };

    next();
  };
};

app.use('/api/products', createCache(60));  // Cache for 60 seconds

// ============================================
// 3. CONDITIONAL MIDDLEWARE
// ============================================

const conditionalMiddleware = (conditionFn, middleware) => {
  return (req, res, next) => {
    if (conditionFn(req)) {
      return middleware(req, res, next);
    }
    next();
  };
};

// Only log POST and PUT requests
const verboseLogger = (req, res, next) => {
  console.log('VERBOSE LOG:', {
    method: req.method,
    url: req.url,
    body: req.body,
    headers: req.headers
  });
  next();
};

app.use(conditionalMiddleware(
  (req) => ['POST', 'PUT', 'PATCH'].includes(req.method),
  verboseLogger
));

// ============================================
// 4. COMPOSABLE MIDDLEWARE PIPELINE
// ============================================

const compose = (...middlewares) => {
  return (req, res, next) => {
    const execute = (index) => {
      if (index >= middlewares.length) return next();

      const middleware = middlewares[index];
      try {
        middleware(req, res, (err) => {
          if (err) return next(err);
          execute(index + 1);
        });
      } catch (err) {
        next(err);
      }
    };

    execute(0);
  };
};

const sanitizeBody = (req, res, next) => {
  if (req.body) {
    Object.keys(req.body).forEach(key => {
      if (typeof req.body[key] === 'string') {
        req.body[key] = req.body[key].trim();
      }
    });
  }
  next();
};

const addTimestamp = (req, res, next) => {
  req.processedAt = new Date().toISOString();
  next();
};

const logRequest = (req, res, next) => {
  console.log(`[${req.processedAt}] ${req.method} ${req.url}`);
  next();
};

// Compose multiple middleware into one
const requestPipeline = compose(sanitizeBody, addTimestamp, logRequest);
app.use(requestPipeline);

// ============================================
// 5. REQUEST VALIDATION MIDDLEWARE FACTORY
// ============================================

const validateSchema = (schema) => {
  return (req, res, next) => {
    const errors = [];

    Object.entries(schema).forEach(([field, rules]) => {
      const value = req.body[field];

      if (rules.required && (value === undefined || value === null || value === '')) {
        errors.push(`${field} is required`);
        return;
      }

      if (value !== undefined) {
        if (rules.type && typeof value !== rules.type) {
          errors.push(`${field} must be of type ${rules.type}`);
        }
        if (rules.minLength && value.length < rules.minLength) {
          errors.push(`${field} must be at least ${rules.minLength} characters`);
        }
        if (rules.maxLength && value.length > rules.maxLength) {
          errors.push(`${field} must be at most ${rules.maxLength} characters`);
        }
        if (rules.min && value < rules.min) {
          errors.push(`${field} must be at least ${rules.min}`);
        }
        if (rules.enum && !rules.enum.includes(value)) {
          errors.push(`${field} must be one of: ${rules.enum.join(', ')}`);
        }
        if (rules.pattern && !rules.pattern.test(value)) {
          errors.push(`${field} format is invalid`);
        }
      }
    });

    if (errors.length > 0) {
      return res.status(400).json({ success: false, errors });
    }

    next();
  };
};

// Usage
app.post('/api/products', validateSchema({
  name: { required: true, type: 'string', minLength: 2, maxLength: 100 },
  price: { required: true, type: 'number', min: 0.01 },
  category: { required: true, type: 'string', enum: ['electronics', 'clothing', 'books'] }
}), (req, res) => {
  res.json({ success: true, data: req.body });
});

// ============================================
// ROUTES
// ============================================

app.get('/api/products', (req, res) => {
  res.json({
    success: true,
    processedAt: req.processedAt,
    data: [
      { id: 1, name: 'Laptop', price: 999.99 },
      { id: 2, name: 'Phone', price: 699.99 }
    ]
  });
});

app.listen(3000, () => console.log('Server running on port 3000'));
```

---

## Chapter 17: WebSockets with Socket.io

### 📖 Theory
HTTP is request-response based (client always initiates). **WebSockets** provide full-duplex communication — both client and server can send data anytime. Perfect for:
- Real-time chat
- Live notifications
- Live dashboards
- Gaming
- Collaboration tools

```bash
npm install socket.io
```

### 💻 Example: Real-time Chat Application

**server.js**
```javascript
const express = require('express');
const http = require('http');
const { Server } = require('socket.io');
const path = require('path');

const app = express();
const server = http.createServer(app);
const io = new Server(server, {
  cors: {
    origin: '*',
    methods: ['GET', 'POST']
  }
});

app.use(express.static(path.join(__dirname, 'public')));

// ============================================
// IN-MEMORY DATA STORE
// ============================================
const users = new Map();      // socketId -> userInfo
const rooms = new Map();      // roomName -> Set of socketIds
const messageHistory = {};    // roomName -> messages[]

// ============================================
// SOCKET.IO CONNECTION HANDLING
// ============================================
io.on('connection', (socket) => {
  console.log(`User connected: ${socket.id}`);

  // ====== JOIN ROOM ======
  socket.on('joinRoom', ({ username, room }) => {
    // Store user info
    users.set(socket.id, { username, room });

    // Join the room
    socket.join(room);

    // Initialize room if needed
    if (!rooms.has(room)) {
      rooms.set(room, new Set());
      messageHistory[room] = [];
    }
    rooms.get(room).add(socket.id);

    // Welcome current user
    socket.emit('message', {
      user: 'System',
      text: `Welcome to room "${room}", ${username}!`,
      time: new Date().toLocaleTimeString()
    });

    // Send message history
    socket.emit('messageHistory', messageHistory[room].slice(-50));

    // Notify others in room
    socket.to(room).emit('message', {
      user: 'System',
      text: `${username} has joined the chat`,
      time: new Date().toLocaleTimeString()
    });

    // Send updated user list
    io.to(room).emit('roomUsers', {
      room,
      users: [...rooms.get(room)].map(id => users.get(id)).filter(Boolean)
    });
  });

  // ====== CHAT MESSAGE ======
  socket.on('chatMessage', (msg) => {
    const user = users.get(socket.id);
    if (!user) return;

    const message = {
      user: user.username,
      text: msg,
      time: new Date().toLocaleTimeString(),
      timestamp: Date.now()
    };

    // Store in history
    if (messageHistory[user.room]) {
      messageHistory[user.room].push(message);
      // Keep only last 100 messages
      if (messageHistory[user.room].length > 100) {
        messageHistory[user.room].shift();
      }
    }

    // Send to everyone in room
    io.to(user.room).emit('message', message);
  });

  // ====== TYPING INDICATOR ======
  socket.on('typing', () => {
    const user = users.get(socket.id);
    if (user) {
      socket.to(user.room).emit('typing', { username: user.username });
    }
  });

  socket.on('stopTyping', () => {
    const user = users.get(socket.id);
    if (user) {
      socket.to(user.room).emit('stopTyping', { username: user.username });
    }
  });

  // ====== DISCONNECT ======
  socket.on('disconnect', () => {
    const user = users.get(socket.id);

    if (user) {
      // Remove from room
      if (rooms.has(user.room)) {
        rooms.get(user.room).delete(socket.id);
      }

      // Notify room
      io.to(user.room).emit('message', {
        user: 'System',
        text: `${user.username} has left the chat`,
        time: new Date().toLocaleTimeString()
      });

      // Update user list
      if (rooms.has(user.room)) {
        io.to(user.room).emit('roomUsers', {
          room: user.room,
          users: [...rooms.get(user.room)].map(id => users.get(id)).filter(Boolean)
        });
      }

      users.delete(socket.id);
    }

    console.log(`User disconnected: ${socket.id}`);
  });
});

// ============================================
// REST API ENDPOINTS
// ============================================
app.get('/api/rooms', (req, res) => {
  const roomList = [];
  rooms.forEach((members, name) => {
    roomList.push({ name, memberCount: members.size });
  });
  res.json({ success: true, data: roomList });
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

**public/index.html**
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Express Chat</title>
  <style>
    * { margin: 0; padding: 0; box-sizing: border-box; }
    body { font-family: Arial, sans-serif; background: #1a1a2e; color: #fff; height: 100vh; }
    .join-container { display: flex; justify-content: center; align-items: center; height: 100vh; }
    .join-form { background: #16213e; padding: 30px; border-radius: 10px; width: 350px; }
    .join-form h2 { text-align: center; margin-bottom: 20px; color: #e94560; }
    .join-form input, .join-form button { width: 100%; padding: 12px; margin: 8px 0; border: none; border-radius: 5px; font-size: 16px; }
    .join-form input { background: #0f3460; color: #fff; }
    .join-form button { background: #e94560; color: #fff; cursor: pointer; font-weight: bold; }
    .chat-container { display: none; height: 100vh; }
    .sidebar { width: 250px; background: #16213e; padding: 20px; float: left; height: 100%; }
    .chat-main { margin-left: 250px; height: 100%; display: flex; flex-direction: column; }
    .chat-header { background: #0f3460; padding: 15px; font-size: 18px; }
    .chat-messages { flex: 1; overflow-y: auto; padding: 20px; background: #1a1a2e; }
    .message { margin: 10px 0; padding: 10px 15px; background: #16213e; border-radius: 8px; max-width: 70%; }
    .message.system { background: #0f3460; text-align: center; max-width: 100%; font-style: italic; color: #aaa; }
    .message .meta { font-size: 12px; color: #e94560; margin-bottom: 5px; }
    .chat-form { display: flex; padding: 15px; background: #16213e; }
    .chat-form input { flex: 1; padding: 12px; border: none; border-radius: 5px 0 0 5px; background: #0f3460; color: #fff; font-size: 16px; }
    .chat-form button { padding: 12px 25px; border: none; border-radius: 0 5px 5px 0; background: #e94560; color: #fff; cursor: pointer; font-size: 16px; }
    .typing-indicator { padding: 5px 20px; font-style: italic; color: #888; font-size: 13px; }
    .user-list { list-style: none; }
    .user-list li { padding: 8px; margin: 4px 0; background: #0f3460; border-radius: 5px; }
    h3 { color: #e94560; margin-bottom: 15px; }
  </style>
</head>
<body>
  <!-- Join Form -->
  <div class="join-container" id="joinContainer">
    <div class="join-form">
      <h2>💬 Express Chat</h2>
      <input type="text" id="username" placeholder="Enter your name..." required>
      <input type="text" id="room" placeholder="Enter room name..." required>
      <button onclick="joinRoom()">Join Chat</button>
    </div>
  </div>

  <!-- Chat Interface -->
  <div class="chat-container" id="chatContainer">
    <div class="sidebar">
      <h3>🏠 Room: <span id="roomName"></span></h3>
      <h3>👥 Users</h3>
      <ul class="user-list" id="userList"></ul>
    </div>
    <div class="chat-main">
      <div class="chat-header">Express.js Real-Time Chat</div>
      <div class="chat-messages" id="chatMessages"></div>
      <div class="typing-indicator" id="typingIndicator"></div>
      <form class="chat-form" onsubmit="sendMessage(event)">
        <input type="text" id="msgInput" placeholder="Type a message..." autocomplete="off">
        <button type="submit">Send</button>
      </form>
    </div>
  </div>

  <script src="/socket.io/socket.io.js"></script>
  <script>
    const socket = io();
    let typingTimeout;

    function joinRoom() {
      const username = document.getElementById('username').value.trim();
      const room = document.getElementById('room').value.trim();
      if (!username || !room) return alert('Please fill in all fields');

      socket.emit('joinRoom', { username, room });

      document.getElementById('joinContainer').style.display = 'none';
      document.getElementById('chatContainer').style.display = 'block';
      document.getElementById('roomName').textContent = room;
      document.getElementById('msgInput').focus();
    }

    function sendMessage(e) {
      e.preventDefault();
      const input = document.getElementById('msgInput');
      const msg = input.value.trim();
      if (!msg) return;

      socket.emit('chatMessage', msg);
      socket.emit('stopTyping');
      input.value = '';
      input.focus();
    }

    // Typing indicator
    document.getElementById('msgInput').addEventListener('input', () => {
      socket.emit('typing');
      clearTimeout(typingTimeout);
      typingTimeout = setTimeout(() => socket.emit('stopTyping'), 1000);
    });

    // Listen for messages
    socket.on('message', (data) => {
      const div = document.createElement('div');
      div.className = `message ${data.user === 'System' ? 'system' : ''}`;
      div.innerHTML = data.user === 'System'
        ? `<p>${data.text}</p>`
        : `<div class="meta">${data.user} • ${data.time}</div><p>${data.text}</p>`;
      document.getElementById('chatMessages').appendChild(div);
      div.scrollIntoView({ behavior: 'smooth' });
    });

    // Message history
    socket.on('messageHistory', (messages) => {
      messages.forEach(msg => {
        const div = document.createElement('div');
        div.className = 'message';
        div.innerHTML = `<div class="meta">${msg.user} • ${msg.time}</div><p>${msg.text}</p>`;
        document.getElementById('chatMessages').appendChild(div);
      });
    });

    // Room users
    socket.on('roomUsers', ({ users }) => {
      const list = document.getElementById('userList');
      list.innerHTML = users.map(u => `<li>👤 ${u.username}</li>`).join('');
    });

    // Typing
    socket.on('typing', ({ username }) => {
      document.getElementById('typingIndicator').textContent = `${username} is typing...`;
    });

    socket.on('stopTyping', () => {
      document.getElementById('typingIndicator').textContent = '';
    });
  </script>
</body>
</html>
```

---

## Chapter 18: API Security Best Practices

### 📖 Theory
Security is critical for any production API. Here are the main threats and how to counter them:

| Threat | Solution |
|--------|----------|
| XSS (Cross-Site Scripting) | Helmet, input sanitization |
| SQL/NoSQL Injection | Input validation, mongo-sanitize |
| CSRF | CORS, CSRF tokens |
| Brute Force | Rate limiting |
| Data Exposure | Select fields, hide sensitive data |
| Parameter Pollution | hpp middleware |

```bash
npm install helmet cors express-rate-limit express-mongo-sanitize xss-clean hpp
```

### 💻 Example: Secure Express App

```javascript
const express = require('express');
const helmet = require('helmet');
const cors = require('cors');
const rateLimit = require('express-rate-limit');
const mongoSanitize = require('express-mongo-sanitize');
const hpp = require('hpp');

const app = express();

// ============================================
// 1. SET SECURITY HTTP HEADERS
// ============================================
app.use(helmet());
// Adds headers like:
// X-Content-Type-Options: nosniff
// X-Frame-Options: DENY
// X-XSS-Protection: 1; mode=block
// Strict-Transport-Security
// And more...

// ============================================
// 2. CORS - Control which domains can access your API
// ============================================
const corsOptions = {
  origin: function (origin, callback) {
    const whitelist = [
      'http://localhost:3000',
      'http://localhost:3001',
      'https://yourproddomain.com'
    ];

    // Allow requests with no origin (mobile apps, Postman)
    if (!origin || whitelist.includes(origin)) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
  credentials: true,
  methods: ['GET', 'POST', 'PUT', 'DELETE', 'PATCH'],
  allowedHeaders: ['Content-Type', 'Authorization'],
  exposedHeaders: ['X-Total-Count'],
  maxAge: 86400
};

app.use(cors(corsOptions));

// ============================================
// 3. RATE LIMITING
// ============================================

// Global: 100 requests per 15 minutes
const globalLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 100,
  message: { success: false, message: 'Too many requests. Try again later.' },
  standardHeaders: true,
  legacyHeaders: false,
  // Skip rate limiting for whitelisted IPs
  skip: (req) => {
    const whitelist = ['127.0.0.1'];
    return whitelist.includes(req.ip);
  }
});
app.use('/api', globalLimiter);

// Auth: 5 requests per 15 minutes
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: { success: false, message: 'Too many login attempts.' }
});
app.use('/api/auth/login', authLimiter);

// ============================================
// 4. BODY PARSER WITH SIZE LIMITS
// ============================================
app.use(express.json({ limit: '10kb' }));  // Limit body size to prevent DOS
app.use(express.urlencoded({ extended: true, limit: '10kb' }));

// ============================================
// 5. DATA SANITIZATION - Against NoSQL Injection
// ============================================
// Prevents: { "email": { "$gt": "" }, "password": "anything" }
app.use(mongoSanitize());

// ============================================
// 6. DATA SANITIZATION - Against XSS
// ============================================
// Custom XSS sanitizer (since xss-clean is deprecated)
const sanitizeInput = (req, res, next) => {
  const sanitize = (obj) => {
    for (let key in obj) {
      if (typeof obj[key] === 'string') {
        obj[key] = obj[key]
          .replace(/</g, '&lt;')
          .replace(/>/g, '&gt;')
          .replace(/&/g, '&amp;')
          .replace(/"/g, '&quot;')
          .replace(/'/g, '&#x27;');
      } else if (typeof obj[key] === 'object') {
        sanitize(obj[key]);
      }
    }
  };

  if (req.body) sanitize(req.body);
  if (req.query) sanitize(req.query);
  if (req.params) sanitize(req.params);

  next();
};

app.use(sanitizeInput);

// ============================================
// 7. PREVENT HTTP PARAMETER POLLUTION
// ============================================
// Prevents: /api/users?sort=name&sort=email (duplicate params)
app.use(hpp({
  whitelist: ['price', 'rating', 'tags']  // Allow duplicates for these
}));

// ============================================
// 8. ADDITIONAL SECURITY HEADERS
// ============================================
app.use((req, res, next) => {
  // Prevent clickjacking
  res.setHeader('X-Frame-Options', 'DENY');
  // Prevent MIME type sniffing
  res.setHeader('X-Content-Type-Options', 'nosniff');
  // Referrer policy
  res.setHeader('Referrer-Policy', 'strict-origin-when-cross-origin');
  // Permissions policy
  res.setHeader('Permissions-Policy', 'camera=(), microphone=(), geolocation=()');

  next();
});

// ============================================
// 9. SECURE SENSITIVE ROUTES
// ============================================
app.get('/api/users', (req, res) => {
  // Never expose sensitive data
  const users = [
    { id: 1, name: 'John', email: 'john@email.com', password: '$2a$12$...', resetToken: 'abc' }
  ];

  // Strip sensitive fields
  const safeUsers = users.map(({ password, resetToken, ...safe }) => safe);

  res.json({ success: true, data: safeUsers });
});

// ============================================
// 10. HANDLE UNHANDLED ROUTES
// ============================================
app.all('*', (req, res) => {
  res.status(404).json({
    success: false,
    message: `Can't find ${req.originalUrl} on this server`
  });
});

app.listen(3000, () => console.log('Secure server running on port 3000'));
```

---

## Chapter 19: Testing Express APIs

### 📖 Theory
Testing ensures your API works correctly and prevents regressions. Types:
- **Unit Tests:** Test individual functions/middleware
- **Integration Tests:** Test routes with database
- **E2E Tests:** Test the full flow

```bash
npm install --save-dev jest supertest
```

**Update package.json:**
```json
{
  "scripts": {
    "test": "jest --verbose --forceExit --detectOpenHandles",
    "test:watch": "jest --watch"
  },
  "jest": {
    "testEnvironment": "node",
    "testMatch": ["**/__tests__/**/*.test.js"]
  }
}
```

### 💻 Example

**app.js (Separate app from server for testing)**
```javascript
const express = require('express');
const app = express();

app.use(express.json());

let todos = [
  { id: 1, title: 'Learn Express', completed: false },
  { id: 2, title: 'Build API', completed: false }
];
let nextId = 3;

// GET all todos
app.get('/api/todos', (req, res) => {
  let result = [...todos];

  if (req.query.completed !== undefined) {
    result = result.filter(t => t.completed === (req.query.completed === 'true'));
  }

  res.json({ success: true, count: result.length, data: result });
});

// GET single todo
app.get('/api/todos/:id', (req, res) => {
  const todo = todos.find(t => t.id === parseInt(req.params.id));

  if (!todo) {
    return res.status(404).json({ success: false, message: 'Todo not found' });
  }

  res.json({ success: true, data: todo });
});

// POST create todo
app.post('/api/todos', (req, res) => {
  const { title } = req.body;

  if (!title || title.trim() === '') {
    return res.status(400).json({ success: false, message: 'Title is required' });
  }

  const newTodo = {
    id: nextId++,
    title: title.trim(),
    completed: false
  };

  todos.push(newTodo);
  res.status(201).json({ success: true, data: newTodo });
});

// PUT update todo
app.put('/api/todos/:id', (req, res) => {
  const index = todos.findIndex(t => t.id === parseInt(req.params.id));

  if (index === -1) {
    return res.status(404).json({ success: false, message: 'Todo not found' });
  }

  todos[index] = { ...todos[index], ...req.body, id: todos[index].id };
  res.json({ success: true, data: todos[index] });
});

// DELETE todo
app.delete('/api/todos/:id', (req, res) => {
  const index = todos.findIndex(t => t.id === parseInt(req.params.id));

  if (index === -1) {
    return res.status(404).json({ success: false, message: 'Todo not found' });
  }

  const deleted = todos.splice(index, 1);
  res.json({ success: true, data: deleted[0] });
});

// Export for testing (don't call listen here)
module.exports = app;
```

**server.js**
```javascript
const app = require('./app');
const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server running on port ${PORT}`));
```

**__tests__/todos.test.js**
```javascript
const request = require('supertest');
const app = require('../app');

describe('Todo API', () => {
  // ============================================
  // GET /api/todos
  // ============================================
  describe('GET /api/todos', () => {
    it('should return all todos', async () => {
      const res = await request(app).get('/api/todos');

      expect(res.statusCode).toBe(200);
      expect(res.body.success).toBe(true);
      expect(res.body.data).toBeInstanceOf(Array);
      expect(res.body.count).toBeGreaterThan(0);
    });

    it('should filter by completed status', async () => {
      const res = await request(app).get('/api/todos?completed=false');

      expect(res.statusCode).toBe(200);
      res.body.data.forEach(todo => {
        expect(todo.completed).toBe(false);
      });
    });
  });

  // ============================================
  // GET /api/todos/:id
  // ============================================
  describe('GET /api/todos/:id', () => {
    it('should return a single todo', async () => {
      const res = await request(app).get('/api/todos/1');

      expect(res.statusCode).toBe(200);
      expect(res.body.success).toBe(true);
      expect(res.body.data).toHaveProperty('id', 1);
      expect(res.body.data).toHaveProperty('title');
    });

    it('should return 404 for non-existent todo', async () => {
      const res = await request(app).get('/api/todos/9999');

      expect(res.statusCode).toBe(404);
      expect(res.body.success).toBe(false);
      expect(res.body.message).toBe('Todo not found');
    });
  });

  // ============================================
  // POST /api/todos
  // ============================================
  describe('POST /api/todos', () => {
    it('should create a new todo', async () => {
      const newTodo = { title: 'Test Todo' };

      const res = await request(app)
        .post('/api/todos')
        .send(newTodo)
        .set('Content-Type', 'application/json');

      expect(res.statusCode).toBe(201);
      expect(res.body.success).toBe(true);
      expect(res.body.data.title).toBe('Test Todo');
      expect(res.body.data.completed).toBe(false);
      expect(res.body.data).toHaveProperty('id');
    });

    it('should return 400 if title is missing', async () => {
      const res = await request(app)
        .post('/api/todos')
        .send({})
        .set('Content-Type', 'application/json');

      expect(res.statusCode).toBe(400);
      expect(res.body.success).toBe(false);
    });

    it('should return 400 if title is empty string', async () => {
      const res = await request(app)
        .post('/api/todos')
        .send({ title: '   ' })
        .set('Content-Type', 'application/json');

      expect(res.statusCode).toBe(400);
    });
  });

  // ============================================
  // PUT /api/todos/:id
  // ============================================
  describe('PUT /api/todos/:id', () => {
    it('should update a todo', async () => {
      const res = await request(app)
        .put('/api/todos/1')
        .send({ title: 'Updated Todo', completed: true });

      expect(res.statusCode).toBe(200);
      expect(res.body.data.title).toBe('Updated Todo');
      expect(res.body.data.completed).toBe(true);
    });

    it('should return 404 for non-existent todo', async () => {
      const res = await request(app)
        .put('/api/todos/9999')
        .send({ title: 'Updated' });

      expect(res.statusCode).toBe(404);
    });
  });

  // ============================================
  // DELETE /api/todos/:id
  // ============================================
  describe('DELETE /api/todos/:id', () => {
    it('should delete a todo', async () => {
      const res = await request(app).delete('/api/todos/2');

      expect(res.statusCode).toBe(200);
      expect(res.body.success).toBe(true);
      expect(res.body.data.id).toBe(2);
    });

    it('should return 404 for non-existent todo', async () => {
      const res = await request(app).delete('/api/todos/9999');

      expect(res.statusCode).toBe(404);
    });
  });
});

// ============================================
// MIDDLEWARE TESTS
// ============================================
describe('Middleware Tests', () => {
  it('should return 404 for unknown routes', async () => {
    const res = await request(app).get('/api/unknown');

    expect(res.statusCode).toBe(404);
  });

  it('should parse JSON body correctly', async () => {
    const data = { title: 'JSON Test' };

    const res = await request(app)
      .post('/api/todos')
      .send(data)
      .expect('Content-Type', /json/);

    expect(res.body.data.title).toBe('JSON Test');
  });
});
```

```bash
# Run tests
npm test
```

---

## Chapter 20: Production Deployment & Best Practices

### 📖 Theory
Production deployment requires attention to performance, security, logging, process management, and reliability.

### 💻 Complete Production Setup

**📁 Final Project Structure**
```
project/
├── src/
│   ├── config/
│   │   ├── config.js
│   │   └── db.js
│   ├── controllers/
│   │   ├── authController.js
│   │   ├── userController.js
│   │   └── productController.js
│   ├── middleware/
│   │   ├── auth.js
│   │   ├── asyncHandler.js
│   │   ├── errorHandler.js
│   │   ├── rateLimiter.js
│   │   ├── validators.js
│   │   └── upload.js
│   ├── models/
│   │   ├── User.js
│   │   ├── Product.js
│   │   └── Order.js
│   ├── routes/
│   │   ├── index.js
│   │   ├── authRoutes.js
│   │   ├── userRoutes.js
│   │   └── productRoutes.js
│   ├── utils/
│   │   ├── AppError.js
│   │   ├── logger.js
│   │   ├── email.js
│   │   └── helpers.js
│   ├── app.js
│   └── server.js
├── __tests__/
│   ├── auth.test.js
│   ├── users.test.js
│   └── products.test.js
├── public/
│   └── uploads/
├── logs/
├── .env
├── .env.example
├── .gitignore
├── package.json
├── ecosystem.config.js    (PM2 config)
└── README.md
```

**src/utils/logger.js**
```javascript
const fs = require('fs');
const path = require('path');

class Logger {
  constructor() {
    this.logDir = path.join(__dirname, '..', '..', 'logs');

    // Create logs directory if it doesn't exist
    if (!fs.existsSync(this.logDir)) {
      fs.mkdirSync(this.logDir, { recursive: true });
    }
  }

  _formatMessage(level, message, meta = {}) {
    return JSON.stringify({
      timestamp: new Date().toISOString(),
      level,
      message,
      ...meta
    }) + '\n';
  }

  _writeToFile(filename, message) {
    const filepath = path.join(this.logDir, filename);
    fs.appendFileSync(filepath, message);
  }

  info(message, meta) {
    const formatted = this._formatMessage('INFO', message, meta);
    console.log(`ℹ️  ${message}`);
    this._writeToFile('app.log', formatted);
  }

  error(message, meta) {
    const formatted = this._formatMessage('ERROR', message, meta);
    console.error(`❌ ${message}`);
    this._writeToFile('error.log', formatted);
    this._writeToFile('app.log', formatted);
  }

  warn(message, meta) {
    const formatted = this._formatMessage('WARN', message, meta);
    console.warn(`⚠️  ${message}`);
    this._writeToFile('app.log', formatted);
  }

  request(req, res, duration) {
    const formatted = this._formatMessage('REQUEST', `${req.method} ${req.originalUrl}`, {
      status: res.statusCode,
      duration: `${duration}ms`,
      ip: req.ip,
      userAgent: req.get('user-agent')
    });
    this._writeToFile('access.log', formatted);
  }
}

module.exports = new Logger();
```

**src/app.js (Production-ready)**
```javascript
const express = require('express');
const path = require('path');
const helmet = require('helmet');
const cors = require('cors');
const compression = require('compression');
const mongoSanitize = require('express-mongo-sanitize');
const hpp = require('hpp');
const cookieParser = require('cookie-parser');

const logger = require('./utils/logger');
const globalErrorHandler = require('./middleware/errorHandler');
const AppError = require('./utils/AppError');

// Import routes
const authRoutes = require('./routes/authRoutes');
const userRoutes = require('./routes/userRoutes');

const app = express();

// ============================================
// GLOBAL MIDDLEWARE STACK
// ============================================

// 1. Security headers
app.use(helmet());

// 2. CORS
app.use(cors({
  origin: process.env.CORS_ORIGIN?.split(',') || '*',
  credentials: true
}));

// 3. Compression
app.use(compression());

// 4. Body parsers
app.use(express.json({ limit: '10kb' }));
app.use(express.urlencoded({ extended: true, limit: '10kb' }));
app.use(cookieParser());

// 5. Data sanitization
app.use(mongoSanitize());
app.use(hpp());

// 6. Static files
app.use(express.static(path.join(__dirname, '..', 'public')));

// 7. Request logging
app.use((req, res, next) => {
  const start = Date.now();

  res.on('finish', () => {
    const duration = Date.now() - start;
    logger.request(req, res, duration);
  });

  next();
});

// 8. Health check
app.get('/health', (req, res) => {
  res.status(200).json({
    status: 'OK',
    uptime: process.uptime(),
    timestamp: new Date().toISOString(),
    memory: process.memoryUsage()
  });
});

// ============================================
// ROUTES
// ============================================
app.get('/', (req, res) => {
  res.json({
    message: 'Express.js API',
    version: '1.0.0',
    docs: '/api-docs'
  });
});

app.use('/api/v1/auth', authRoutes);
app.use('/api/v1/users', userRoutes);

// ============================================
// ERROR HANDLING
// ============================================

// 404 handler
app.all('*', (req, res, next) => {
  next(new AppError(`Can't find ${req.originalUrl} on this server!`, 404));
});

// Global error handler
app.use(globalErrorHandler);

module.exports = app;
```

**src/server.js**
```javascript
const dotenv = require('dotenv');
dotenv.config();

const app = require('./app');
const connectDB = require('./config/db');
const logger = require('./utils/logger');

// Handle uncaught exceptions
process.on('uncaughtException', (err) => {
  logger.error('UNCAUGHT EXCEPTION! 💥 Shutting down...', {
    name: err.name,
    message: err.message,
    stack: err.stack
  });
  process.exit(1);
});

// Connect to Database
connectDB();

const PORT = process.env.PORT || 3000;

const server = app.listen(PORT, () => {
  logger.info(`Server running in ${process.env.NODE_ENV || 'development'} mode on port ${PORT}`);
});

// Handle unhandled promise rejections
process.on('unhandledRejection', (err) => {
  logger.error('UNHANDLED REJECTION! 💥 Shutting down...', {
    name: err.name,
    message: err.message
  });
  server.close(() => {
    process.exit(1);
  });
});

// Graceful shutdown on SIGTERM
process.on('SIGTERM', () => {
  logger.info('SIGTERM RECEIVED. Shutting down gracefully');
  server.close(() => {
    logger.info('Process terminated!');
  });
});
```

**ecosystem.config.js (PM2 Configuration)**
```javascript
module.exports = {
  apps: [{
    name: 'express-api',
    script: './src/server.js',
    instances: 'max',           // Use all CPU cores
    exec_mode: 'cluster',       // Cluster mode for load balancing
    autorestart: true,
    watch: false,
    max_memory_restart: '1G',
    env: {
      NODE_ENV: 'development',
      PORT: 3000
    },
    env_production: {
      NODE_ENV: 'production',
      PORT: 8080
    },
    log_file: './logs/combined.log',
    error_file: './logs/error.log',
    out_file: './logs/out.log',
    merge_logs: true,
    log_date_format: 'YYYY-MM-DD HH:mm:ss Z'
  }]
};
```

**Deployment Commands:**
```bash
# Install PM2 globally
npm install -g pm2

# Start with PM2
pm2 start ecosystem.config.js --env production

# Monitor
pm2 monit

# View logs
pm2 logs

# Restart
pm2 restart express-api

# Stop
pm2 stop express-api

# Auto-start on system reboot
pm2 startup
pm2 save
```

**.env.example**
```env
# Server
NODE_ENV=development
PORT=3000

# Database
DB_URI=mongodb://localhost:27017/myapp

# JWT
JWT_SECRET=your_jwt_secret_here_change_in_production
JWT_EXPIRE=30d
JWT_COOKIE_EXPIRE=30

# CORS
CORS_ORIGIN=http://localhost:3000,http://localhost:3001

# Rate Limiting
RATE_LIMIT_WINDOW=15
RATE_LIMIT_MAX=100

# File Upload
MAX_FILE_SIZE=5242880
FILE_UPLOAD_PATH=./public/uploads
```

---

## 📊 Quick Reference Cheatsheet

```
┌─────────────────────────────────────────────────────────┐
│                   EXPRESS.JS CHEATSHEET                  │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  BASIC SETUP                                            │
│  const express = require('express');                    │
│  const app = express();                                 │
│  app.listen(3000);                                      │
│                                                         │
│  METHODS                                                │
│  app.get(path, handler)                                 │
│  app.post(path, handler)                                │
│  app.put(path, handler)                                 │
│  app.patch(path, handler)                               │
│  app.delete(path, handler)                              │
│  app.all(path, handler)                                 │
│  app.use(middleware)                                     │
│                                                         │
│  REQUEST (req)                                          │
│  req.params    → /users/:id → { id: '1' }              │
│  req.query     → /users?age=25 → { age: '25' }         │
│  req.body      → POST body (needs parser)               │
│  req.headers   → Request headers                        │
│  req.method    → GET, POST, etc.                        │
│  req.path      → URL path                               │
│  req.ip        → Client IP                              │
│  req.cookies   → Cookies (needs cookie-parser)          │
│                                                         │
│  RESPONSE (res)                                         │
│  res.json({})          → Send JSON                      │
│  res.send('text')      → Send text/HTML                 │
│  res.status(404)       → Set status code                │
│  res.redirect('/url')  → Redirect                       │
│  res.render('view', {})→ Render template                │
│  res.download(file)    → Download file                  │
│  res.cookie(name, val) → Set cookie                     │
│  res.clearCookie(name) → Clear cookie                   │
│                                                         │
│  MIDDLEWARE                                              │
│  app.use(fn)                → Global                    │
│  app.use('/path', fn)       → Path-specific             │
│  app.get('/p', fn1, fn2)    → Route-specific            │
│  app.use((err,req,res,next))→ Error handler             │
│                                                         │
│  ROUTER                                                 │
│  const router = express.Router();                       │
│  router.route('/').get(fn).post(fn);                    │
│  app.use('/api', router);                               │
│                                                         │
│  STATUS CODES                                           │
│  200 OK          201 Created     204 No Content         │
│  400 Bad Request 401 Unauthorized 403 Forbidden         │
│  404 Not Found   422 Unprocessable 429 Too Many         │
│  500 Server Error                                       │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## 🗺️ Learning Path Summary

```
Phase 1 (Beginner) - Weeks 1-2
├── Ch 1: Setup & First Server
├── Ch 2: Request & Response
├── Ch 3: Routing Basics
├── Ch 4: Params, Query, Body
├── Ch 5: Middleware ⭐ (Most Important)
├── Ch 6: Express Router
└── Ch 7: Static Files & Templates

Phase 2 (Intermediate) - Weeks 3-5
├── Ch 8: Third-Party Middleware
├── Ch 9: Environment Variables
├── Ch 10: MongoDB & Mongoose ⭐
├── Ch 11: Error Handling (Pro)
├── Ch 12: Authentication (JWT) ⭐
├── Ch 13: File Upload
└── Ch 14: Data Validation

Phase 3 (Advanced) - Weeks 6-8
├── Ch 15: Mongoose Relationships
├── Ch 16: Advanced Middleware Patterns
├── Ch 17: WebSockets (Socket.io)
├── Ch 18: API Security ⭐
├── Ch 19: Testing (Jest + Supertest)
└── Ch 20: Production Deployment ⭐
```

> 💡 **Tip:** Build a complete project after finishing each phase. Phase 1 → Todo API, Phase 2 → Blog API with Auth, Phase 3 → Full E-commerce API.
