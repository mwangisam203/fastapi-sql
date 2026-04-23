# 🚀 FastAPI User Management API (SQLite + SQLAlchemy)

This project is a simple **backend API** built using FastAPI and SQLAlchemy to perform full CRUD operations on a `User` database.

It demonstrates how to:

* Connect FastAPI to a database (SQLite)
* Define database models using SQLAlchemy
* Validate request/response data using Pydantic
* Perform CRUD operations (Create, Read, Update, Delete)
* Use dependency injection for database sessions

---

# 🧠 How the Application Works

## 1. FastAPI App Initialization

```python
app = FastAPI(title="SQL Integration - Learning best hacks")
```

* Initializes the FastAPI application
* Sets a custom title visible in `/docs`

---

## 2. Database Setup (SQLite + SQLAlchemy)

```python
engine = create_engine("sqlite:///users.db", connect_args={"check_same_thread": False})
```

* Creates a connection to a local SQLite database (`users.db`)
* `check_same_thread=False` allows multiple threads (required for FastAPI)

```python
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
```

* Creates a session factory for interacting with the database

```python
Base = declarative_base()
```

* Base class used to define database models

---

## 3. Database Model (Table Definition)

```python
class User(Base):
    __tablename__ = "users"
```

* Defines a table named `users`

```python
id = Column(Integer, primary_key=True, index=True)
name = Column(String, nullable=False)
email = Column(String, nullable=False)
role = Column(String, nullable=False)
```

* Columns:

  * `id`: unique identifier (primary key)
  * `name`: user name (required)
  * `email`: user email (required)
  * `role`: user role (required)

```python
Base.metadata.create_all(bind=engine)
```

* Automatically creates the database table if it does not exist

---

## 4. Pydantic Models (Data Validation)

### Request Model

```python
class CreateUser(BaseModel):
    name: str
    email: str
    role: str
```

* Defines the structure of incoming data when creating/updating a user

### Response Model

```python
class UserResponse(BaseModel):
```

* Defines how data is returned to the client

```python
class Config:
    from_attributes = True
```

* Allows conversion from SQLAlchemy objects → JSON responses

---

## 5. Database Dependency Injection

```python
def get_db():
```

* Creates a database session per request

```python
yield db
```

* Provides the session to endpoints

```python
db.close()
```

* Ensures the connection is closed after the request

---

# 🌐 API Endpoints

## Root Endpoint

```http
GET /
```

Returns:

```json
{
  "message": "Introduction to FastAPI with SQL"
}
```

---

## Create User

```http
POST /users/
```

* Creates a new user
* Checks if email already exists

### Example Request:

```json
{
  "name": "John",
  "email": "john@example.com",
  "role": "admin"
}
```

### Key Logic:

* Prevents duplicate users using email
* Saves user to database
* Returns created user

---

## Get Single User

```http
GET /users/{user_id}
```

* Fetches a user by ID

### Behavior:

* Returns 404 if user does not exist

---

## Get All Users

```http
GET /users
```

* Returns all users in the database

---

## Update User (Full Update)

```http
PUT /user/{user_id}
```

* Updates all fields of a user

### How it works:

```python
for field, value in user.dict().items():
    setattr(db_user, field, value)
```

* Dynamically updates each field

### Note:

* Requires all fields (`name`, `email`, `role`)
* Returns error if user does not exist

---

## Delete User

```http
DELETE /user/{user_id}
```

* Deletes a user from the database

### Behavior:

* Returns 404 if user not found
* Permanently removes the record

---

# ⚙️ How to Run the Project

## 1. Install dependencies

```bash
pip install fastapi uvicorn sqlalchemy pydantic
```

## 2. Run the server

```bash
uvicorn myapi:app --reload
```

## 3. Open API Docs

* Swagger UI: http://127.0.0.1:8000/docs
* ReDoc: http://127.0.0.1:8000/redoc

---

# ⚠️ Important Notes

* Uses SQLite (local file: `users.db`)
* No authentication implemented
* Email uniqueness is handled manually (not enforced at DB level)
* Update endpoint requires full data (no partial updates yet)

---

# 🚀 Possible Improvements

* Add email validation (`EmailStr`)
* Enforce unique email in database
* Add PATCH (partial updates)
* Add pagination (`limit`, `skip`)
* Add search/filter functionality
* Move code into separate files (`models`, `schemas`, `routes`)
* Add authentication (JWT)

---

# 🧠 What This Project Demonstrates

* FastAPI routing and request handling
* SQLAlchemy ORM usage
* Dependency injection (`Depends`)
* Data validation with Pydantic
* CRUD operations in a real backend system

---

# 👤 Author

Backend Engineer in progress 🚀
Building real-world APIs with FastAPI and SQLAlchemy
