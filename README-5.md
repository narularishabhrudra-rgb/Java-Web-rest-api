# Spring Boot User Management REST API

A REST API for managing users, built with Spring Boot, Spring Data JPA (Hibernate) and Bean Validation. It follows the MVC layered pattern and was tested with Postman.

## Features
- Full CRUD for users over HTTP with JSON requests and responses
- Layered architecture: Controller, Service, Repository
- JPA/Hibernate persistence (in-memory H2 by default, MySQL supported)
- Input validation with clear field-level messages
- Global exception handling with consistent JSON error responses

## Architecture

```
Client (Postman)
      |
UserController      HTTP routing, request/response JSON
      |
UserService         business rules (duplicate email check, not-found handling)
      |
UserRepository      Spring Data JPA, talks to the database via Hibernate
      |
Database (H2 / MySQL)
```

| Package | Responsibility |
|---|---|
| `controller` | REST endpoints (`@RestController`) |
| `service` | Business logic and transactions |
| `repository` | Database access (`JpaRepository`) |
| `model` | `User` entity with validation rules |
| `exception` | Custom exceptions, `ErrorResponse`, `@RestControllerAdvice` handler |

## Endpoints

| Method | URL | Description | Success |
|---|---|---|---|
| GET | `/api/users` | List all users | 200 |
| GET | `/api/users/{id}` | Get one user | 200 |
| POST | `/api/users` | Create a user | 201 + Location header |
| PUT | `/api/users/{id}` | Update a user | 200 |
| DELETE | `/api/users/{id}` | Delete a user | 204 |

Example request body (POST and PUT):
```json
{
  "name": "Asha Rao",
  "email": "asha@example.com",
  "age": 21
}
```

## Validation and error handling

| Situation | Status |
|---|---|
| Invalid field (blank name, bad email, age outside 1-120) | 400 with `fieldErrors` |
| Malformed JSON or non-numeric id | 400 |
| User id does not exist | 404 |
| Email already used by another user | 409 |

Example validation error:
```json
{
  "timestamp": "2026-11-01T10:15:30",
  "status": 400,
  "error": "Bad Request",
  "message": "Validation failed",
  "fieldErrors": {
    "email": "Email must be a valid address",
    "age": "Age must be at least 1"
  }
}
```

## Run locally

Requirements: JDK 17+ and Maven.

```bash
mvn spring-boot:run
```

The API starts at `http://localhost:8080`. With the default H2 database, data is cleared on restart. The H2 console is at `http://localhost:8080/h2-console` (JDBC URL `jdbc:h2:mem:userdb`, user `sa`, empty password).

### Use MySQL instead
1. Create a database: `CREATE DATABASE userdb;`
2. In `application.properties`, replace the H2 datasource lines with the commented MySQL lines.
3. Set `DB_USER` and `DB_PASSWORD` as environment variables so no password is stored in the repo.

## Testing with Postman
Import `postman_collection.json` into Postman. It contains one request per endpoint, plus examples for validation and not-found errors. The `baseUrl` variable defaults to `http://localhost:8080`.

Suggested order: Create user, Get all, Get by id, Update, Create with invalid data, Create duplicate email, Delete, Get deleted user (404).

## Sample output
_Add Postman screenshots here (create, get, validation error, 404)._
