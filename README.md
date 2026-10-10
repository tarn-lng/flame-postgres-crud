# Flame + PostgreSQL CRUD Microservice 🚀

An expressive, production-ready REST API CRUD backend written in 100% pure **Tarn**, powered by the **Flame** web framework and the native **PostgreSQL** driver.

---

## Architecture & Security Guarantees

- **Web Framework:** `flame 0.1.0` (expressive routing, JSON serialization, middlewares, CORS, logger)
- **Database Driver:** `postgres 0.2.0` (native Protocol 3.0 over TCP sockets, zero external C dependencies)
- **Parameterized Queries:** All SQL queries use the PostgreSQL Extended Query Protocol (`Parse`, `Bind`, `Describe`, `Execute`, `Sync`). Parameters (`$1, $2, ...`) are transmitted out-of-band as length-prefixed bytes, rendering SQL injection physically impossible.
- **Strict Input Validation:** Path parameters (e.g. `:id`) are strictly parsed and validated (`string.parse_u64`) before any database access; malformed or malicious inputs are rejected immediately with `400 Bad Request`.
- **Connection Pooling:** Uses `postgres.Pool` synchronized via `Mutex<postgres.Pool>` to reuse authenticated TCP sockets across requests, eliminating per-request connection overhead.
- **Database:** PostgreSQL 17 on Docker (`127.0.0.1:5432`)
- **JSON Engine:** Standard library `json` streaming parser and writer
- **Platform:** Linux x86_64, zero garbage collector overhead

---

## API Endpoints

| Method | Endpoint | Description | Response |
|---|---|---|---|
| `GET` | `/health` | Health check & DB connection ping | `200 OK` |
| `GET` | `/products` | List all products | `200 OK` (JSON array) |
| `GET` | `/products/:id` | Get product details by ID | `200 OK` / `400 Bad Request` / `404 Not Found` |
| `POST` | `/products` | Create a new product | `201 Created` / `400 Bad Request` |
| `PUT` | `/products/:id` | Update an existing product | `200 OK` / `400 Bad Request` / `404 Not Found` |
| `DELETE` | `/products/:id` | Delete a product by ID | `200 OK` / `400 Bad Request` / `404 Not Found` |

---

## Quickstart

### 1. Start PostgreSQL with Docker

```bash
docker run -d --name tarn-postgres -p 5432:5432 -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=testdb postgres:17-alpine
```

### 2. Fetch dependencies

```bash
tarn fetch
tarn verify
```

### 3. Build & Run

```bash
tarn build main.tarn -o crud_server
./crud_server
```

---

## Example cURL Commands

### 1. Health check
```bash
curl -i http://127.0.0.1:8080/health
```

### 2. List Products
```bash
curl -i http://127.0.0.1:8080/products
```

### 3. Get Product by ID
```bash
curl -i http://127.0.0.1:8080/products/1
```

### 4. Create a Product (Parameterized)
```bash
curl -i -X POST http://127.0.0.1:8080/products \
  -H "Content-Type: application/json" \
  -d '{"title": "Mechanical Keyboard", "category": "Hardware", "price": "129.99"}'
```

### 5. Update a Product (Parameterized)
```bash
curl -i -X PUT http://127.0.0.1:8080/products/1 \
  -H "Content-Type: application/json" \
  -d '{"title": "Mechanical Keyboard RGB", "category": "Hardware", "price": "149.99"}'
```

### 6. Delete a Product
```bash
curl -i -X DELETE http://127.0.0.1:8080/products/1
```

### 7. Security Test: SQL Injection Immunity
Attempting an injection payload in the URL or payload:
```bash
# Rejected with 400 Bad Request (strict route validation):
curl -i "http://127.0.0.1:8080/products/1%20OR%201=1"

# Safely stored as literal string without altering query structure:
curl -i -X POST http://127.0.0.1:8080/products \
  -H "Content-Type: application/json" \
  -d '{"title": "Hacker Item'\''; DROP TABLE products; --", "category": "Security", "price": "99.99"}'
```
