# Flame + PostgreSQL CRUD Microservice 🚀

An expressive, production-ready REST API CRUD backend written in 100% pure **Tarn**, powered by the **Flame** web framework and the native **PostgreSQL** driver.

---

## Architecture

- **Web Framework:** `flame 0.1.0` (routing, JSON context, middlewares, CORS, logging)
- **Database Driver:** `postgres 0.1.0` (pure wire protocol 3.0 over TCP sockets, zero C dependencies)
- **Database:** PostgreSQL 17 on Docker (`127.0.0.1:5432`)
- **JSON Engine:** Standard library `json` module (streaming parsing and writing)
- **Platform:** Linux x86_64, zero garbage collector overhead

---

## API Endpoints

| Method | Endpoint | Description | Response |
|---|---|---|---|
| `GET` | `/health` | Health check & DB connection ping | `200 OK` |
| `GET` | `/products` | List all products | `200 OK` (JSON array) |
| `GET` | `/products/:id` | Get product details by ID | `200 OK` / `404 Not Found` |
| `POST` | `/products` | Create a new product | `201 Created` |
| `PUT` | `/products/:id` | Update an existing product | `200 OK` / `404 Not Found` |
| `DELETE` | `/products/:id` | Delete a product by ID | `200 OK` / `404 Not Found` |

---

## Quickstart

### 1. Start PostgreSQL with Docker

```bash
docker run -d --name tarn-postgres -p 5432:5432 -e POSTGRES_PASSWORD=postgres -e POSTGRES_DB=testdb postgres:17-alpine
```

### 2. Fetch dependencies

```bash
tarn fetch
```

### 3. Build & Run

```bash
tarn build main.tarn -o crud_server
./crud_server
```

---

## Example cURL Commands

### 1. List Products
```bash
curl -i http://127.0.0.1:8080/products
```

### 2. Get Product by ID
```bash
curl -i http://127.0.0.1:8080/products/1
```

### 3. Create a Product
```bash
curl -i -X POST http://127.0.0.1:8080/products \
  -H "Content-Type: application/json" \
  -d '{"title": "Mechanical Keyboard", "category": "Hardware", "price": "129.99"}'
```

### 4. Update a Product
```bash
curl -i -X PUT http://127.0.0.1:8080/products/1 \
  -H "Content-Type: application/json" \
  -d '{"title": "Mechanical Keyboard RGB", "category": "Hardware", "price": "149.99"}'
```

### 5. Delete a Product
```bash
curl -i -X DELETE http://127.0.0.1:8080/products/1
```
