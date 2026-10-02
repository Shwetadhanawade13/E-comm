# Validation & Security Engineering Guide

This document outlines the validation rules, security practices, and defensive programming patterns implemented across the frontend and backend tiers.

---

## 1. Validation Matrix

| Domain | Entity / Field | Frontend Validation | Backend Validation (Mongoose / Controller) | Failure Action |
| :--- | :--- | :--- | :--- | :--- |
| **Auth** | `name` | Non-empty, 2-50 chars | `required`, `trim: true`, length constraints | `400 Bad Request` |
| **Auth** | `email` | HTML5 email regex pattern | Lowercase, unique index, regex validation | `400 Bad Request` ("Email already in use" or "Invalid email") |
| **Auth** | `password` | Min 6 characters | Min 6 characters, hashed before save | `400 Bad Request` |
| **Auth** | `confirmPassword` | Matches `password` exactly | Checked in `register` controller before hashing | `400 Bad Request` ("Passwords do not match") |
| **Category** | `name` | Non-empty, 2-50 chars | `required`, `unique: true`, `trim: true` | `400 Bad Request` ("Category name already exists") |
| **Category** | `description`| Max 250 chars | `trim: true`, max 250 chars | `400 Bad Request` |
| **Product** | `name` | Non-empty, 2-120 chars | `required`, `trim: true` | `400 Bad Request` |
| **Product** | `price` | Positive number (`min="0.01"`) | `required`, `min: [0.01, 'Must be positive']` | `400 Bad Request` |
| **Product** | `stock` | Integer (`min="0"`) | `required`, `min: [0, 'Cannot be negative']` | `400 Bad Request` |
| **Product** | `category` | Must select existing item | Must be valid ObjectId pointing to existing Category | `400 Bad Request` ("Invalid category reference") |
| **Product** | `image` | Valid URL string | `required`, trimmed string | `400 Bad Request` |
| **Order** | `cartItems` | Length > 0 | Array validation: at least 1 item; valid Product IDs | `400 Bad Request` |
| **Order** | `quantity` | `1 <= qty <= product.stock` | Verified against MongoDB live inventory | `400 Bad Request` ("Insufficient stock") |
| **Order** | `shippingAddress`| All fields non-empty | Subdocument validation: `name`, `phone`, `address`, `city`, `pincode` | `400 Bad Request` |

---

## 2. The Golden Rule: Authoritative Server Pricing

### 2.1 The Vulnerability (Client-Side Tampering)
In an insecure application, a malicious actor could intercept or alter the client request payload:
```json
// TAMPERED REQUEST FROM CLIENT
{
  "cartItems": [
    { "productId": "651a302c2e5c8e31a9b1c020", "quantity": 1, "price": 0.01 } // Real price: $699.99
  ]
}
```
If the backend calculates `totalAmount = item.price * item.quantity` from the request, the user would purchase a $700 item for 1 cent.

### 2.2 The Implementation Defense
The backend **completely ignores** any `price` field sent by the client. It uses only `productId` and `quantity`:

```javascript
// server/controllers/orderController.js
let totalAmount = 0;
const snapshotProducts = [];

for (const item of cartItems) {
  // 1. Fetch source of truth from MongoDB
  const product = await Product.findById(item.productId);
  if (!product) {
    return res.status(404).json({ success: false, message: `Product not found: ${item.productId}` });
  }

  // 2. Validate current stock
  if (product.stock < item.quantity) {
    return res.status(400).json({
      success: false,
      message: `Insufficient stock for '${product.name}'. Available: ${product.stock}, requested: ${item.quantity}`,
    });
  }

  // 3. Calculate price using authoritative database value
  const itemTotal = product.price * item.quantity;
  totalAmount += itemTotal;

  // 4. Create snapshot for order
  snapshotProducts.push({
    product: product._id,
    name: product.name,
    price: product.price,
    quantity: item.quantity,
    image: product.image,
  });

  // 5. Decrement inventory
  product.stock -= item.quantity;
  await product.save();
}

// 6. Round total to 2 decimal places to avoid floating point anomalies
totalAmount = Math.round(totalAmount * 100) / 100;
```

---

## 3. Stock Concurrency & Inventory Guard

1. **Non-Negative Invariant:** The Product schema specifies `min: [0, 'Stock cannot be negative']`. If an atomic update attempts to set stock below 0, Mongoose validation triggers an error.
2. **Immediate Decrement:** Inventory is decremented during the creation phase of the order, preventing concurrent users from purchasing the same remaining units.
3. **Cart Clamping on Client:** The React client disables the `+` increment button as soon as `cartQty === product.stock`, guiding users within valid limits before they hit the server.

---

## 4. JWT & Password Cryptographic Hardening

1. **Bcrypt Salt Rounds:** Work factor of 10 provides balanced security against hash table & brute-force dictionary attacks.
2. **Timing Attack Protection:** `bcrypt.compare` uses constant-time string comparisons to neutralize side-channel timing attacks.
3. **Token Secrets & Expiry:**
   - Secret key configured via environment variable `JWT_SECRET`.
   - Token validity restricted to `7d` (7 days) with `jwt.sign({ id, role }, secret, { expiresIn: '7d' })`.
   - Expired or tampered tokens return an explicit `401 Unauthorized` causing client-side session cleanup.
4. **Password Exclusion:** The User Mongoose schema or query logic uses `.select('-password')` so that password hashes are never serialized into API responses.

---

## 5. Protected Route Access Control (RBAC)

Two middleware guards govern access:
- **Level 1 (Authenticated Customer):** `authMiddleware` ensures the caller possesses a valid, non-expired JWT.
- **Level 2 (Administrator):** `adminMiddleware` ensures `req.user.role === 'admin'`. Unauthorized non-admin callers receive `403 Forbidden`.

```javascript
// Express Route Guarding Example
router.post('/categories', protect, requireAdmin, createCategory);
router.post('/products', protect, requireAdmin, createProduct);
router.get('/admin/orders', protect, requireAdmin, getAllOrders);
```

---

## 6. Centralized Error Handling

The Express application registers a centralized error-handling middleware (`middleware/errorMiddleware.js`) at the end of the middleware stack:
- Intercepts uncaught exceptions and Mongoose validation errors.
- Formats consistent error responses:
  ```json
  {
    "success": false,
    "message": "Error description message"
  }
  ```
- Prevents database error internals, MongoDB connection URIs, or server stack traces from leaking to the frontend in production.
