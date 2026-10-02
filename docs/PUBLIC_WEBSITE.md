# Public Storefront & Customer Experience Specification

This document details the user interface components, page behaviors, shopping cart mechanics, checkout workflows, and styling guidelines for the customer-facing storefront.

---

## 1. Storefront Page Structure & Routes

| Route | Page | Purpose & Key Features |
| :--- | :--- | :--- |
| `/` | **Home** | Welcome banner, highlights, featured categories, quick browsing shortcuts. |
| `/products` | **Products Catalog** | Full product collection with dynamic category pills, keyword search, and responsive grid. |
| `/products/:id` | **Product Details** | High-res image display, product description, live stock indicator, quantity picker, and "Add to Cart". |
| `/cart` | **Cart** | Item breakdown, real-time quantity modifiers bounded by inventory, removal actions, and subtotal. |
| `/checkout` | **Checkout** | Shipping address input (Cash on Delivery), order summary recap, and "Place Order" button. |
| `/my-orders` | **My Orders** | Historical list of orders placed by customer with live status badges. |
| `/login` | **Login** | Dual-purpose login form for customers and administrators. |
| `/register` | **Register** | Customer account registration with password confirmation validation. |

---

## 2. Navigation & Common Elements

### 2.1 Responsive Navbar (`Navbar.jsx`)
- **Brand Logo:** Links to Home (`/`).
- **Navigation Links:** `Home`, `Products`.
- **Cart Icon Button:** Displays live badge counter (`totalItems`). Clicking navigates to `/cart`.
- **User Actions:**
  - *Guest:* Displays `Login` and `Register` buttons.
  - *Customer:* Shows user's first name, `My Orders` link, and `Logout` button.
  - *Admin:* Shows `Admin Dashboard` button and `Logout`.
- **Mobile Menu Toggle:** Hamburger icon toggles collapsible sliding navigation on mobile/tablet viewports.

### 2.2 Global Footer (`Footer.jsx`)
- Clean, minimal footer displaying copyright, quick links, and tech stack attribution.

---

## 3. Product Catalog & Discovery (`Products.jsx`)

### 3.1 Category Filter Bar
Provides clickable category pills with active states:
```text
[ All ]  [ Electronics ]  [ Fashion ]  [ Shoes ]  [ + Other Categories ]
```
- Clicking a category triggers an instant query: `GET /api/products?category=<name>`.
- `All` clears category filtering: `GET /api/products`.

### 3.2 Product Search
- Search input with magnifying glass icon: "Search products by name or description...".
- Dispatches search query to backend: `GET /api/products?search=<keyword>`.
- Can be combined with category filter: `GET /api/products?category=electronics&search=phone`.

### 3.3 Responsive Product Grid & Card (`ProductCard.jsx`)
- **Grid Layout:**
  - Mobile (`<640px`): 1 column (`grid-cols-1`)
  - Tablet (`640px - 1024px`): 2 columns (`grid-cols-2`)
  - Desktop (`>1024px`): 3 or 4 columns (`grid-cols-3 lg:grid-cols-4`)
- **Card Elements:**
  - Aspect-ratio locked product image with hover zoom effect.
  - Category pill label (e.g. `Electronics`).
  - Product name (truncated to 2 lines max).
  - Formatted price: `$XX.XX`.
  - Stock badge:
    - If `stock > 0`: `Add to Cart` button (primary color).
    - If `stock === 0`: `Out of Stock` disabled button (neutral gray).

---

## 4. Shopping Cart Mechanics (`Cart.jsx`)

### 4.1 State Rules in `CartContext`
1. **Adding an Item:**
   - If item not in cart: Add with `quantity: 1`.
   - If item already in cart: Increment quantity by 1, provided `currentQty + 1 <= item.stock`.
   - If `currentQty + 1 > item.stock`: Prevent addition and trigger toast: `"Maximum available stock reached"`.
2. **Adjusting Quantity (`+` / `-` buttons):**
   - Decrement button: Decreases quantity by 1. If quantity reaches 0, item is removed or user is prompted to confirm removal.
   - Increment button: Increases quantity by 1 up to `product.stock`. Disabled when `quantity === product.stock`.
3. **Removing an Item:**
   - Trash icon button immediately removes the item from cart and recalculates totals.
4. **Local Storage Synchronization:**
   - Every cart modification syncs to `localStorage.getItem('ecomm_cart')`.

### 4.2 Cart View Layout
- **Left Column:** List of item rows showing thumbnail, title, unit price, quantity controls `[-] Qty [+]`, subtotal, and remove button.
- **Right Column (Sticky Order Summary):**
  - Items Subtotal: `$XXX.XX`
  - Delivery: `FREE (Cash on Delivery)`
  - Total Payable: `$XXX.XX`
  - "Proceed to Checkout" action button (navigates to `/checkout`).

---

## 5. Checkout & Order Placement (`Checkout.jsx`)

### 5.1 Shipping Address Form
Collects mandatory delivery details:
- **Full Name:** Text (min 2 chars)
- **Phone Number:** Tel / Text (min 7 digits)
- **Address:** Street / Flat details
- **City:** City name
- **Pincode / Postal Code:** Zip code format

### 5.2 Payment Method
- Fixed to **Cash on Delivery (COD)**.
- Informational notice: *"Pay cash upon package arrival at your doorstep."*

### 5.3 Order Placement Pipeline
When the customer clicks **"Place Order (Cash on Delivery)"**:
```text
1. Client-side validation checks all shipping fields.
2. Cart array [{ productId, quantity }] is packaged with shippingAddress.
3. Client issues POST /api/orders.
4. Server verifies stock availability and calculates authentic DB price total.
5. Server creates Order document and reduces stock in Product documents.
6. Server responds with 201 Created.
7. Client clears local Cart state and localStorage ('ecomm_cart').
8. Toast notification: "Order placed successfully! Order ID: #...".
9. Customer redirected to /my-orders page.
```

---

## 6. My Orders (`MyOrders.jsx`)

- Fetches orders from `GET /api/orders/my-orders`.
- Renders a clean timeline / card for each past order:
  - **Header:** Order Number, Date placed, Status Badge (`Pending`, `Confirmed`, `Shipped`, `Delivered`, `Cancelled`).
  - **Body:** Ordered items preview (Thumbnail, Title, Quantity, Price).
  - **Shipping Info:** Name, Contact, Address.
  - **Footer:** Total Amount Paid / Payable (`$XXX.XX [COD]`).
- **Empty State:** If no orders exist, displays shopping bag icon with "You haven't placed any orders yet" and "Start Shopping" button.
