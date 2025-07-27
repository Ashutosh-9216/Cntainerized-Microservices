🧩 Flask Microservices with Docker Compose
This project demonstrates a simple microservices architecture using Flask, Docker Compose, PostgreSQL, and Redis.

🎯 Project Goal
The goal of this project is to design and implement a basic microservices-based architecture using Docker Compose, where:

Each microservice performs a specific function in isolation.
Services communicate with each other over a custom Docker network.
Flask is used for creating lightweight APIs.
PostgreSQL is used for persistent storage of user registration data.
Redis is used for caching user information to reduce DB load and improve performance.
The entire architecture is containerized to simulate real-world DevOps/backend engineering practices.
⚙️ What I Built
A modular, two-service architecture with:

🔹 Flask – Lightweight REST APIs for both services
🔹 PostgreSQL – For reliable user data persistence
🔹 Redis – For blazing-fast caching
🔹 Docker Compose – To spin up and connect everything seamlessly
📦 Services Overview
Service	Description	Port
user-service	Handles user registration using PostgreSQL	5000
data-service	Serves user data with Redis caching	5001
postgres	PostgreSQL database for user data	5432
redis	Redis cache for fast data access	6379
📂 Folder Structure

.
├── data-service/
│   └── app.py
├── user-service/
│   └── app.py
├── init.sql
├── docker-compose.yml
└── README.md

🚀 Getting Started
1️⃣ Prerequisites
Docker
Docker Compose
2️⃣ Clone the Repository
git clone https://github.com/Krutika09/02-Containerized-Microservices.git
cd 02-Containerized-Microservices
3️⃣ Run All Services
docker-compose up --build
image
This will:

Build and start all services.
Set up a PostgreSQL DB with a users table via init.sql.
Launch Redis, user-service on port 5000, and data-service on 5001.
🔧 API Endpoints
✅ User Registration (user-service)
Endpoint: POST /register
URL: http://localhost:5000/register
Payload:
{
  "name": "Rose",
  "email": "rose@example.com"
}
Sample cURL Command:

curl -X POST http://localhost:5000/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Rose", "email":"rose@example.com"}'
📥 Get Cached User Info (data-service)
Endpoint: GET /user/<name>
URL: http://localhost:5001/user/Rose
Behavior: Returns cached user info if available; else retrieves from DB and caches it.
Sample cURL Command:

curl http://localhost:5001/user/Rose
🗃️ Database Initialization
The postgres container loads an initial SQL file from:

./init.sql → /docker-entrypoint-initdb.d/init.sql
Example init.sql:
CREATE TABLE IF NOT EXISTS users (
    id SERIAL PRIMARY KEY,
    name TEXT NOT NULL,
    email TEXT NOT NULL
);
Alternative (with VARCHAR size limits):

CREATE TABLE IF NOT EXISTS users (
  id SERIAL PRIMARY KEY,
  name VARCHAR(100),
  email VARCHAR(100)
);
✅ Verifying Functionality
✅ Test 1: Is user-service connected to PostgreSQL?
Step 1: Check logs
docker-compose logs user-service
Step 2: Register a user
curl -X POST http://localhost:5000/register \
  -H "Content-Type: application/json" \
  -d '{"name":"Rose", "email":"rose@example.com"}'
image
Expected Response:

{"message": "User registered"}
Step 3: Verify PostgreSQL entry
docker exec -it <postgres-container-id-or-name> psql -U postgres -d users
Inside Postgres, run:

SELECT * FROM users;
image
✅ Test 2: Is data-service connected to Redis?
curl http://localhost:5001/user/Rose
Expected on First Request:

{
  "name": "Rose",
  "cached": false,
  "info": "User info for Rose"
}
Expected on Second Request:

{
  "name": "Rose",
  "cached": true,
  "info": "User info for Rose"
}
Because Redis is caching the response by name.

✅ Bonus: Check if Redis is storing keys
docker exec -it <redis-container-id-or-name> redis-cli
Then run:

keys *
get Rose
image
✅ Summary of Manual Test Commands
What	Command
Start services	docker-compose up --build
Register a user	curl -X POST http://localhost:5000/register -H "Content-Type: application/json" -d '{"name":"Alice", "email":"alice@example.com"}'
Verify DB entry	docker exec -it <postgres> psql -U postgres -d users
Fetch from data-service	curl http://localhost:5001/user/Alice
Check Redis keys	docker exec -it <redis> redis-cli → keys *, get Alice
