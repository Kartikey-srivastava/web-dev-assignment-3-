# Lab 3 — Inventory & Data Management System

A RESTful backend for managing a store/warehouse product catalogue built with **Node.js**, **Express.js**, **MongoDB**, and **Mongoose**.

---

## 🚀 Setup

```bash
# 1. Install dependencies
npm install

# 2. Configure environment
cp .env.example .env
# Edit .env and set your MONGO_URI

# 3. Start the server
npm start          # production
npm run dev        # development (auto-reload with nodemon)
```

> **Requires:** Node.js ≥ 18, a running MongoDB instance (local or Atlas)

---

## 📁 Project Structure

```
src/
├── config/
│   └── db.js                  # MongoDB connection (Mongoose)
├── models/
│   └── Product.js             # Mongoose schema & validation
├── controllers/
│   └── productController.js   # Business logic for all routes
├── routes/
│   └── productRoutes.js       # Express router
├── middleware/
│   ├── errorHandler.js        # Centralized error handling
│   └── validateInput.js       # Input validation middleware
└── server.js                  # App entry point
```

---

## 🔌 API Reference

Base URL: `http://localhost:5000`

### Products

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/products` | Get all products (filtering, sorting, pagination) |
| `POST` | `/api/products` | Create a new product |
| `GET` | `/api/products/:id` | Get a single product by ID |
| `PUT` | `/api/products/:id` | Update product details |
| `DELETE` | `/api/products/:id` | Delete a product |
| `PATCH` | `/api/products/:id/stock` | Adjust stock (restock or sale) |

### Reports (Aggregation)

| Method | Endpoint | Description |
|--------|----------|-------------|
| `GET` | `/api/products/reports/low-stock` | Products at or below their low-stock threshold |
| `GET` | `/api/products/reports/category-summary` | Category-wise inventory summary |

---

## 📋 Query Parameters — `GET /api/products`

| Parameter | Type | Description |
|-----------|------|-------------|
| `category` | String | Filter by category (e.g. `Electronics`) |
| `minPrice` | Number | Minimum price filter |
| `maxPrice` | Number | Maximum price filter |
| `inStock` | Boolean | `true` → only products with stock > 0 |
| `search` | String | Substring search on name & description |
| `sortBy` | String | Field to sort by (default: `createdAt`) |
| `order` | String | `asc` or `desc` (default: `desc`) |
| `page` | Number | Page number (default: `1`) |
| `limit` | Number | Results per page (default: `10`, max: `100`) |

**Example:**
```
GET /api/products?category=Electronics&minPrice=100&sortBy=price&order=asc&page=1&limit=5
```

---

## 📦 Product Schema

```json
{
  "name":              "string (required, 2–100 chars)",
  "sku":               "string (required, unique, uppercase, alphanumeric+hyphen)",
  "category":          "Electronics | Clothing | Food | Furniture | Sports | Books | Toys | Other",
  "description":       "string (optional, max 500 chars)",
  "price":             "number (required, ≥ 0)",
  "stock":             "number (default: 0, ≥ 0)",
  "lowStockThreshold": "number (default: 10)",
  "supplier":          "string (optional)",
  "isActive":          "boolean (default: true)"
}
```

---

## 🔧 Sample Requests

### Create a Product
```bash
curl -X POST http://localhost:5000/api/products \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Wireless Headphones",
    "sku": "WH-001",
    "category": "Electronics",
    "description": "Noise-cancelling over-ear headphones",
    "price": 149.99,
    "stock": 50,
    "lowStockThreshold": 10,
    "supplier": "AudioCorp"
  }'
```

### Restock a Product
```bash
curl -X PATCH http://localhost:5000/api/products/<id>/stock \
  -H "Content-Type: application/json" \
  -d '{ "type": "restock", "quantity": 20 }'
```

### Record a Sale
```bash
curl -X PATCH http://localhost:5000/api/products/<id>/stock \
  -H "Content-Type: application/json" \
  -d '{ "type": "sale", "quantity": 3 }'
```

### Low-Stock Report
```bash
curl http://localhost:5000/api/products/reports/low-stock
```

### Category Summary
```bash
curl http://localhost:5000/api/products/reports/category-summary
```

---

## ✅ Features Implemented

- [x] Mongoose schema with field-level validation (required, min/max, enum, regex, unique)
- [x] Complete CRUD — Create, Read (all + single), Update, Delete
- [x] Query-string filtering (category, price range, inStock, text search)
- [x] Sorting and pagination
- [x] Stock adjustment: `restock` and `sale` with guard against overselling
- [x] Aggregation — Low-stock alert report
- [x] Aggregation — Category-wise inventory summary (total products, stock, value, avg price)
- [x] Centralized error handling middleware
- [x] Input validation middleware
- [x] Environment variable management with `dotenv`
