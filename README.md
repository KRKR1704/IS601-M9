# FastAPI + PostgreSQL + pgAdmin (Docker Compose Project)
# Author: Roopesh Kumar Reddy Kaipa
# Date: 11/03/2025
---

## **Project Description**

This project demonstrates how to set up a **FastAPI application** connected to a **PostgreSQL database** with an optional **pgAdmin** interface using **Docker Compose**.  
You will:

- Configure and run all services via a single `docker-compose.yml`.
- Access PostgreSQL through **pgAdmin** at `http://localhost:5050`.
- Create, insert, query, update, and delete records using **SQL commands**.
- Understand and demonstrate **one-to-many relationships** and **foreign keys** between tables.

The goal is to integrate a modern backend (FastAPI) with relational data management using Dockerized infrastructure.

---

## **How to Run the Project**

### Step 1 — Start Docker Services

Ensure **Docker Desktop** is running, then build and start all containers:

```bash
docker-compose up --build
```

> This command builds images (if needed) and runs FastAPI, PostgreSQL, and pgAdmin together.

### Step 2 — Access pgAdmin

Open your browser and navigate to:

```
http://localhost:5050
```

- **Default Credentials** (from `docker-compose.yml`):
  - Email: `admin@admin.com`
  - Password: `admin`

Add a **new server connection** with:
- **Host** → `db`
- **Port** → `5432`
- **Username** → `postgres`
- **Password** → `postgres`

### Step 3 — Open the Query Tool

In pgAdmin:

1. Connect to the server.
2. Expand your target database (e.g., `fastapi_db`).
3. Open the **Query Tool** to execute SQL scripts.

### Step 4 — Run SQL Commands

Execute the following SQL blocks in order.

#### (A) Create Tables
```sql
CREATE TABLE users (
    id SERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE calculations (
    id SERIAL PRIMARY KEY,
    operation VARCHAR(20) NOT NULL,
    operand_a FLOAT NOT NULL,
    operand_b FLOAT NOT NULL,
    result FLOAT NOT NULL,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    user_id INTEGER NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

#### (B) Insert Records
```sql
INSERT INTO users (username, email)
VALUES 
('alice', 'alice@example.com'),
('bob', 'bob@example.com');

INSERT INTO calculations (operation, operand_a, operand_b, result, user_id)
VALUES
('add', 2, 3, 5, 1),
('divide', 10, 2, 5, 1),
('multiply', 4, 5, 20, 2);
```

#### (C) Query Data
```sql
-- Retrieve all users
SELECT * FROM users;

-- Retrieve all calculations
SELECT * FROM calculations;

-- Join both tables
SELECT u.username, c.operation, c.operand_a, c.operand_b, c.result
FROM calculations c
JOIN users u ON c.user_id = u.id;
```

#### (D) Update a Record
```sql
UPDATE calculations
SET result = 6
WHERE id = 1;
```

#### (E) Delete a Record
```sql
DELETE FROM calculations
WHERE id = 2;
```

---

## **Project Setup (Detailed)**

### 1️⃣ Clone the Repository

```bash
git clone <your-repository-url>
cd <repository-folder>
```

### 2️⃣ Install Docker and Docker Compose

- **Windows/Mac:** Install [Docker Desktop](https://www.docker.com/products/docker-desktop/)
- **Linux:** Follow [Docker Engine Guide](https://docs.docker.com/engine/install/)

Verify installation:

```bash
docker --version
docker compose version
```

### 3️⃣ Check the `docker-compose.yml`

Ensure it defines the three services:
- **FastAPI** → web app container  
- **db** → PostgreSQL container  
- **pgadmin** → web admin interface  

Example minimal structure:

```yaml
version: '3.9'
services:
  web:
    build: .
    ports:
      - "8000:8000"
    depends_on:
      - db

  db:
    image: postgres:14
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: fastapi_db
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  pgadmin:
    image: dpage/pgadmin4
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@admin.com
      PGADMIN_DEFAULT_PASSWORD: admin
    ports:
      - "5050:80"

volumes:
  postgres_data:
```

### 4️⃣ Build and Launch Containers

```bash
docker-compose up --build
```

Check logs for confirmation:
> “Database system is ready to accept connections”

### 5️⃣ Verify FastAPI and pgAdmin Access

- FastAPI → [http://localhost:8000/docs](http://localhost:8000/docs)
- pgAdmin → [http://localhost:5050](http://localhost:5050)

---

## **Understanding the Database Relationship**

This project demonstrates a **one-to-many** relationship:

\[
\text{One User} \longrightarrow \text{Many Calculations}
\]

- The foreign key constraint:
  ```sql
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
  ```
  ensures referential integrity.  
- Deleting a user automatically deletes all related calculations (`ON DELETE CASCADE`).

Example test:
```sql
DELETE FROM users WHERE id = 1;
SELECT * FROM calculations;
```
→ All calculations of user 1 are also removed.

---

## **Useful Commands Summary**

| Purpose                      | Command                                   |
| ----------------------------- | ----------------------------------------- |
| Build and start containers    | `docker-compose up --build`               |
| Stop containers               | `docker-compose down`                     |
| List running containers       | `docker ps`                               |
| Enter PostgreSQL shell        | `docker exec -it <db-container> psql -U postgres` |
| View container logs           | `docker logs <container_name>`            |
| Remove unused images/volumes  | `docker system prune -a`                  |

---

## **References**

- [FastAPI Documentation](https://fastapi.tiangolo.com/)
- [PostgreSQL Docs](https://www.postgresql.org/docs/)
- [pgAdmin Docs](https://www.pgadmin.org/docs/)
- [Docker Compose Reference](https://docs.docker.com/compose/)

---
