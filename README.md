# Mini E-Commerce Demo Project (MERN Stack)

A lightweight, modern, and clean **Mini E-Commerce Demo Web Application** built with the **MERN Stack** (MongoDB, Express.js, React.js, Node.js), styled with **Tailwind CSS**, and secured using **JWT & bcrypt**.

---

## 📌 Project Overview

This project is a streamlined, full-stack e-commerce application designed to demonstrate the complete lifecycle of an online store:
- **Admin Flow:** Manage product catalog (Categories & Products) and track customer orders.
- **Customer Flow:** Browse products, filter by categories, search, manage cart, place Cash-On-Delivery (COD) orders, and view past order history.
- **Security & Integrity:** Server-side price verification, inventory stock deduction, and role-based JWT authentication.

> **Scope Note:** This is deliberately scoped as a clean mini-project. It intentionally omits unnecessary complexities such as payment gateways, wishlist, product reviews, coupons, suppliers, multi-vendor systems, or advanced analytics.

---

## 🛠️ Tech Stack

| Layer | Technology | Details |
| :--- | :--- | :--- |
| **Frontend** | React.js (v18+) + Vite | Fast single-page application with JavaScript (ES6+) |
| **Styling** | Tailwind CSS | Modern, utility-first responsive styling |
| **Icons & UI Feedback** | Lucide React / React Hot Toast | Clean icons and interactive toast alerts |
| **HTTP Client** | Axios | Configured with base URL and JWT request interceptors |
| **Backend** | Node.js + Express.js | Modular REST API server with routing & middleware |
| **Database** | MongoDB + Mongoose | Document database with schema validation & references |
| **Authentication** | JWT (JSON Web Tokens) + bcryptjs | Secure password hashing and token-based authorization |

---

## 📂 Project Architecture & Structure

The repository is structured into two main sub-projects: `/client` for the frontend and `/server` for the backend.

```text
E-comm/
├── docs/                             # Comprehensive project documentation
│   ├── ARCHITECTURE.md               # Detailed system design & component structure
│   ├── AUTHENTICATION.md             # JWT, bcrypt, and role authorization flow
│   ├── DATABASE_MODELS.md            # Mongoose schemas and relationships
│   ├── API_SPECIFICATION.md          # REST API endpoints, payloads & responses
│   ├── ADMIN_PANEL.md                # Admin dashboard UX & status transitions
│   ├── PUBLIC_WEBSITE.md             # Customer storefront pages & cart logic
│   ├── VALIDATION_AND_SECURITY.md    # Data validation and security rules
│   └── DEMO_WALKTHROUGH.md           # Step-by-step end-to-end testing script
│
├── client/                           # React.js + Vite Frontend
│   ├── public/                       # Static public assets
│   ├── src/
│   │   ├── assets/                   # Images and branding assets
│   │   ├── components/               # Reusable UI components
│   │   │   ├── common/               # Navbar, Footer, Modal, Loader, Button
│   │   │   ├── admin/                # AdminSidebar, CategoryModal, ProductModal
│   │   │   └── shop/                 # ProductCard, CategoryFilter, CartItem
│   │   ├── context/                  # AuthContext, CartContext
│   │   ├── pages/
│   │   │   ├── Home.jsx              # Landing page
│   │   │   ├── Products.jsx          # Catalog with filter & search
│   │   │   ├── ProductDetails.jsx    # Single product details & stock info
│   │   │   ├── Cart.jsx              # Cart page with stock check
│   │   │   ├── Checkout.jsx          # COD Shipping form
│   │   │   ├── MyOrders.jsx          # Customer order history
│   │   │   ├── Login.jsx             # Customer / Admin Login
│   │   │   ├── Register.jsx          # Customer Registration
│   │   │   └── admin/
│   │   │       ├── AdminDashboard.jsx
│   │   │       ├── AdminCategories.jsx
│   │   │       ├── AdminProducts.jsx
│   │   │       └── AdminOrders.jsx
│   │   ├── services/                 # Axios API service modules (api.js, authService.js, etc.)
│   │   ├── App.jsx                   # Main application routes & providers
│   │   ├── main.jsx                  # React DOM mount point
│   │   └── index.css                 # Tailwind CSS directives
│   ├── package.json
│   ├── tailwind.config.js
│   └── vite.config.js
│
└── server/                           # Node.js + Express Backend
    ├── config/                       # Database connection (db.js)
    ├── controllers/                  # Request handlers
    │   ├── authController.js
    │   ├── categoryController.js
    │   ├── productController.js
    │   └── orderController.js
    ├── middleware/                   # Auth & error handling middlewares
    │   ├── authMiddleware.js         # JWT verification
    │   ├── adminMiddleware.js        # Admin role enforcement
    │   └── errorMiddleware.js       # Centralized error handler
    ├── models/                       # Mongoose Data Models
    │   ├── User.js
    │   ├── Category.js
    │   ├── Product.js
    │   └── Order.js
    ├── routes/                       # Express API route definitions
    │   ├── authRoutes.js
    │   ├── categoryRoutes.js
    │   ├── productRoutes.js
    │   └── orderRoutes.js
    ├── utils/                        # Token generation & helper utilities
    ├── server.js                     # Express app initialization & server entry point
    └── package.json
```

---

## 📖 Detailed Documentation

| Document | Description |
| :--- | :--- |
| [**docs/ARCHITECTURE.md**](docs/ARCHITECTURE.md) | High-level system architecture, client-server data flow, and directory breakdown. |
| [**docs/DATABASE_MODELS.md**](docs/DATABASE_MODELS.md) | Mongoose schema definitions, field types, validation constraints, and ER relationships. |
| [**docs/API_SPECIFICATION.md**](docs/API_SPECIFICATION.md) | Exhaustive REST API endpoints, request bodies, query params, and status codes. |
| [**docs/AUTHENTICATION.md**](docs/AUTHENTICATION.md) | JWT token creation, password hashing with bcrypt, and role-based route protection. |
| [**docs/ADMIN_PANEL.md**](docs/ADMIN_PANEL.md) | Admin dashboard UI, category CRUD, product CRUD, and order status lifecycle. |
| [**docs/PUBLIC_WEBSITE.md**](docs/PUBLIC_WEBSITE.md) | Customer store pages, category filtering, search, cart limits, and checkout UI. |
| [**docs/VALIDATION_AND_SECURITY.md**](docs/VALIDATION_AND_SECURITY.md) | Backend vs frontend validation, stock deduction safety, and authoritative pricing. |
| [**docs/DEMO_WALKTHROUGH.md**](docs/DEMO_WALKTHROUGH.md) | Step-by-step demonstration scenario from Admin setup to Customer order placement. |
| [**docs/IMPLEMENTATION_PLAN.md**](docs/IMPLEMENTATION_PLAN.md) | Phased coding roadmap, milestones, and testing checklists for MERN development. |

---

## ⚡ Main Demo Flow

```text
Admin Login
   ↓
Add Category (e.g., Electronics, Fashion, Shoes)
   ↓
Add Product (Image, Name, Price, Category, Stock)
   ↓
Product appears on Public Website
   ↓
Customer Registers / Logs in
   ↓
Browse Products (Search & Filter by Category)
   ↓
Add to Cart (Stock limit enforced)
   ↓
Checkout (Enter Name, Phone, Address, City, Pincode)
   ↓
Place Order (Cash on Delivery)
   ↓
Backend validates real DB price & reduces product stock
   ↓
Order saved in MongoDB & Cart cleared
   ↓
Customer views order under "My Orders"
   ↓
Admin sees order in Admin Dashboard and updates status:
(Pending → Confirmed → Shipped → Delivered)
```

---

## 📋 API Overview

### Authentication
* `POST /api/auth/register` — Register customer
* `POST /api/auth/login` — Login customer or admin

### Categories
* `GET /api/categories` — List all categories (Public)
* `POST /api/categories` — Add category (Admin)
* `PUT /api/categories/:id` — Update category (Admin)
* `DELETE /api/categories/:id` — Delete category (Admin)

### Products
* `GET /api/products` — List products (Public, supports `?category=...&search=...`)
* `GET /api/products/:id` — Get single product (Public)
* `POST /api/products` — Create product (Admin)
* `PUT /api/products/:id` — Update product (Admin)
* `DELETE /api/products/:id` — Delete product (Admin)

### Orders
* `POST /api/orders` — Place order (Auth customer, validates price & stock)
* `GET /api/orders/my-orders` — Customer orders (Auth customer)
* `GET /api/admin/orders` — View all store orders (Admin)
* `PATCH /api/admin/orders/:id/status` — Update order status (Admin)

---

## 🚀 Environment Variables Guide

### Server (`server/.env`)
```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/mini_ecomm
JWT_SECRET=super_secret_jwt_key_123456
CLIENT_URL=http://localhost:5173
```

### Client (`client/.env`)
```env
VITE_API_BASE_URL=http://localhost:5000/api
```

---

## 🔒 Security Principles Enforced

1. **Authoritative Server Pricing:** The frontend cart prices are display-only. The server looks up current product prices directly in MongoDB when calculating order totals.
2. **Stock Verification & Atomic Deduction:** An order is rejected if requested quantity exceeds available stock. When placed, stock is decremented immediately.
3. **Password Security:** Passwords are never stored in plain text. Hashed with `bcryptjs` using a salt work factor of 10.
4. **Role-Based Access Control:** Admin endpoints verify that the authenticated JWT contains an `admin` role before processing sensitive operations.