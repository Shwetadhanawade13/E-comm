# REST API Specification

This document details the complete REST API interface for the Mini E-Commerce project, including authentication requirements, request parameters, sample JSON payloads, and response status codes.

---

## Base URL
```text
http://localhost:5000/api
```

## Standard Headers
- **JSON Content:** `Content-Type: application/json`
- **Authenticated Requests:** `Authorization: Bearer <jwt_token>`

---

## 1. Authentication Endpoints

### 1.1 Register Customer
Creates a new customer account and returns user details with a JWT token.

- **Method:** `POST`
- **URL:** `/auth/register`
- **Access:** Public

#### Request Body
```json
{
  "name": "Jane Doe",
  "email": "jane@example.com",
  "password": "password123",
  "confirmPassword": "password123"
}
```

#### Success Response (`201 Created`)
```json
{
  "success": true,
  "message": "User registered successfully",
  "token": "eyJhbGciOiJIUzI1NiIsIn...",
  "user": {
    "_id": "651a1f0a2e5c8e31a9b1c001",
    "name": "Jane Doe",
    "email": "jane@example.com",
    "role": "customer"
  }
}
```

#### Error Responses
- `400 Bad Request`: Validation failure (passwords do not match, email already exists, invalid email, password length < 6).

---

### 1.2 Login (Customer or Admin)
Authenticates credentials and returns a signed JWT token.

- **Method:** `POST`
- **URL:** `/auth/login`
- **Access:** Public

#### Request Body
```json
{
  "email": "admin@example.com",
  "password": "adminpassword"
}
```

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "message": "Login successful",
  "token": "eyJhbGciOiJIUzI1NiIsIn...",
  "user": {
    "_id": "651a1f0a2e5c8e31a9b1c002",
    "name": "Store Admin",
    "email": "admin@example.com",
    "role": "admin"
  }
}
```

#### Error Responses
- `400 Bad Request`: Email and password are required.
- `401 Unauthorized`: Invalid email or password.

---

## 2. Category Endpoints

### 2.1 Get All Categories
Retrieves all available product categories.

- **Method:** `GET`
- **URL:** `/categories`
- **Access:** Public

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "count": 3,
  "categories": [
    {
      "_id": "651a201b2e5c8e31a9b1c010",
      "name": "Electronics",
      "description": "Smartphones, laptops, and gadgets",
      "createdAt": "2026-10-02T10:00:00.000Z"
    },
    {
      "_id": "651a201b2e5c8e31a9b1c011",
      "name": "Fashion",
      "description": "Apparel, clothing, and wear",
      "createdAt": "2026-10-02T10:05:00.000Z"
    },
    {
      "_id": "651a201b2e5c8e31a9b1c012",
      "name": "Shoes",
      "description": "Sneakers, boots, and casual footwear",
      "createdAt": "2026-10-02T10:10:00.000Z"
    }
  ]
}
```

---

### 2.2 Create Category
Adds a new product category.

- **Method:** `POST`
- **URL:** `/categories`
- **Access:** Admin only (`Bearer <token>`)

#### Request Body
```json
{
  "name": "Home Appliances",
  "description": "Kitchen and living room electric appliances"
}
```

#### Success Response (`201 Created`)
```json
{
  "success": true,
  "message": "Category created successfully",
  "category": {
    "_id": "651a201b2e5c8e31a9b1c013",
    "name": "Home Appliances",
    "description": "Kitchen and living room electric appliances",
    "createdAt": "2026-10-02T10:15:00.000Z"
  }
}
```

---

### 2.3 Update Category
Updates an existing category.

- **Method:** `PUT`
- **URL:** `/categories/:id`
- **Access:** Admin only (`Bearer <token>`)

#### Request Body
```json
{
  "name": "Home & Kitchen Appliances",
  "description": "Modern kitchen and home gadgets"
}
```

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "message": "Category updated successfully",
  "category": {
    "_id": "651a201b2e5c8e31a9b1c013",
    "name": "Home & Kitchen Appliances",
    "description": "Modern kitchen and home gadgets",
    "updatedAt": "2026-10-02T10:20:00.000Z"
  }
}
```

---

### 2.4 Delete Category
Deletes a category by ID.

- **Method:** `DELETE`
- **URL:** `/categories/:id`
- **Access:** Admin only (`Bearer <token>`)

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "message": "Category deleted successfully"
}
```

---

## 3. Product Endpoints

### 3.1 Get All Products (Filter & Search)
Lists products with optional filtering by category name/ID and keyword search.

- **Method:** `GET`
- **URL:** `/products`
- **Query Parameters:**
  - `category`: Category name or Category ID (e.g. `?category=electronics`)
  - `search`: Keyword matching product name or description (e.g. `?search=phone`)
- **Example:** `/products?category=electronics&search=phone`
- **Access:** Public

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "count": 1,
  "products": [
    {
      "_id": "651a302c2e5c8e31a9b1c020",
      "name": "Wireless Smartphone Pro",
      "description": "High resolution display with fast charging",
      "price": 699.99,
      "image": "https://images.unsplash.com/photo-1511707171634-5f897ff02aa9?w=600",
      "category": {
        "_id": "651a201b2e5c8e31a9b1c010",
        "name": "Electronics"
      },
      "stock": 15,
      "createdAt": "2026-10-02T10:30:00.000Z"
    }
  ]
}
```

---

### 3.2 Get Single Product
Fetches detailed product record by ID.

- **Method:** `GET`
- **URL:** `/products/:id`
- **Access:** Public

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "product": {
    "_id": "651a302c2e5c8e31a9b1c020",
    "name": "Wireless Smartphone Pro",
    "description": "High resolution display with fast charging",
    "price": 699.99,
    "image": "https://images.unsplash.com/photo-1511707171634-5f897ff02aa9?w=600",
    "category": {
      "_id": "651a201b2e5c8e31a9b1c010",
      "name": "Electronics"
    },
    "stock": 15,
    "createdAt": "2026-10-02T10:30:00.000Z"
  }
}
```

---

### 3.3 Create Product
Adds a new product to inventory.

- **Method:** `POST`
- **URL:** `/products`
- **Access:** Admin only (`Bearer <token>`)

#### Request Body
```json
{
  "name": "Running Sports Shoes",
  "description": "Lightweight breathable running shoes with rubber sole",
  "price": 89.99,
  "image": "https://images.unsplash.com/photo-1542291026-7eec264c27ff?w=600",
  "category": "651a201b2e5c8e31a9b1c012",
  "stock": 25
}
```

#### Success Response (`201 Created`)
```json
{
  "success": true,
  "message": "Product created successfully",
  "product": {
    "_id": "651a302c2e5c8e31a9b1c021",
    "name": "Running Sports Shoes",
    "description": "Lightweight breathable running shoes with rubber sole",
    "price": 89.99,
    "image": "https://images.unsplash.com/photo-1542291026-7eec264c27ff?w=600",
    "category": "651a201b2e5c8e31a9b1c012",
    "stock": 25,
    "createdAt": "2026-10-02T10:35:00.000Z"
  }
}
```

---

### 3.4 Update Product
Updates an existing product's information.

- **Method:** `PUT`
- **URL:** `/products/:id`
- **Access:** Admin only (`Bearer <token>`)

#### Request Body
```json
{
  "name": "Running Sports Shoes - Red Edition",
  "price": 94.99,
  "stock": 30
}
```

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "message": "Product updated successfully",
  "product": {
    "_id": "651a302c2e5c8e31a9b1c021",
    "name": "Running Sports Shoes - Red Edition",
    "price": 94.99,
    "stock": 30
  }
}
```

---

### 3.5 Delete Product
Removes a product from the database.

- **Method:** `DELETE`
- **URL:** `/products/:id`
- **Access:** Admin only (`Bearer <token>`)

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "message": "Product deleted successfully"
}
```

---

## 4. Order Endpoints

### 4.1 Create Order (Checkout)
Validates stock, verifies real prices from MongoDB, creates the order, and reduces inventory.

- **Method:** `POST`
- **URL:** `/orders`
- **Access:** Authenticated Customer (`Bearer <token>`)

#### Request Body
```json
{
  "cartItems": [
    {
      "productId": "651a302c2e5c8e31a9b1c020",
      "quantity": 1
    },
    {
      "productId": "651a302c2e5c8e31a9b1c021",
      "quantity": 2
    }
  ],
  "shippingAddress": {
    "name": "Jane Doe",
    "phone": "+1 555-0199",
    "address": "123 Orchard Street, Apt 4B",
    "city": "Metropolis",
    "pincode": "10001"
  }
}
```

#### Success Response (`201 Created`)
```json
{
  "success": true,
  "message": "Order placed successfully",
  "order": {
    "_id": "651a403d2e5c8e31a9b1c030",
    "user": "651a1f0a2e5c8e31a9b1c001",
    "products": [
      {
        "product": "651a302c2e5c8e31a9b1c020",
        "name": "Wireless Smartphone Pro",
        "price": 699.99,
        "quantity": 1,
        "image": "https://images.unsplash.com/photo-1511707171634-5f897ff02aa9?w=600"
      },
      {
        "product": "651a302c2e5c8e31a9b1c021",
        "name": "Running Sports Shoes - Red Edition",
        "price": 94.99,
        "quantity": 2,
        "image": "https://images.unsplash.com/photo-1542291026-7eec264c27ff?w=600"
      }
    ],
    "totalAmount": 889.97,
    "shippingAddress": {
      "name": "Jane Doe",
      "phone": "+1 555-0199",
      "address": "123 Orchard Street, Apt 4B",
      "city": "Metropolis",
      "pincode": "10001"
    },
    "paymentMethod": "COD",
    "status": "Pending",
    "createdAt": "2026-10-02T10:45:00.000Z"
  }
}
```

#### Error Responses
- `400 Bad Request`: "Product not found", "Insufficient stock for product X", or invalid shipping address.
- `401 Unauthorized`: Missing or invalid token.

---

### 4.2 Get Customer Orders
Returns the list of orders belonging to the logged-in customer.

- **Method:** `GET`
- **URL:** `/orders/my-orders`
- **Access:** Authenticated Customer (`Bearer <token>`)

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "count": 1,
  "orders": [
    {
      "_id": "651a403d2e5c8e31a9b1c030",
      "totalAmount": 889.97,
      "status": "Pending",
      "createdAt": "2026-10-02T10:45:00.000Z",
      "products": [
        {
          "name": "Wireless Smartphone Pro",
          "price": 699.99,
          "quantity": 1,
          "image": "https://images.unsplash.com/photo-1511707171634-5f897ff02aa9?w=600"
        }
      ]
    }
  ]
}
```

---

### 4.3 Get All Store Orders (Admin)
Returns all customer orders across the platform with populated user details.

- **Method:** `GET`
- **URL:** `/admin/orders`
- **Access:** Admin only (`Bearer <token>`)

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "count": 15,
  "orders": [
    {
      "_id": "651a403d2e5c8e31a9b1c030",
      "user": {
        "_id": "651a1f0a2e5c8e31a9b1c001",
        "name": "Jane Doe",
        "email": "jane@example.com"
      },
      "totalAmount": 889.97,
      "status": "Pending",
      "createdAt": "2026-10-02T10:45:00.000Z",
      "shippingAddress": {
        "name": "Jane Doe",
        "phone": "+1 555-0199",
        "city": "Metropolis"
      }
    }
  ]
}
```

---

### 4.4 Update Order Status (Admin)
Updates the current lifecycle status of an order.

- **Method:** `PATCH`
- **URL:** `/admin/orders/:id/status`
- **Access:** Admin only (`Bearer <token>`)

#### Request Body
```json
{
  "status": "Shipped"
}
```
*Valid Status Values:* `Pending`, `Confirmed`, `Shipped`, `Delivered`, `Cancelled`

#### Success Response (`200 OK`)
```json
{
  "success": true,
  "message": "Order status updated to Shipped",
  "order": {
    "_id": "651a403d2e5c8e31a9b1c030",
    "status": "Shipped",
    "updatedAt": "2026-10-02T11:00:00.000Z"
  }
}
```

#### Error Responses
- `400 Bad Request`: "Invalid status transition" or unsupported status value.
- `404 Not Found`: Order ID does not exist.
