# JWT Authentication with Spring Security 6

A production-ready JWT authentication API built with Spring Boot 3 and Spring Security 6. Demonstrates stateless authentication using JSON Web Tokens with role-based access control.

## Features

- User registration and login with JWT token response
- BCrypt password hashing
- Stateless session management (no server-side sessions)
- Role-based access control (`USER`, `ADMIN`)
- Custom JWT filter integrated into Spring Security filter chain
- H2 in-memory database (swap for PostgreSQL in production)

## Tech Stack

| Layer | Technology |
|---|---|
| Framework | Spring Boot 3.2 |
| Security | Spring Security 6 |
| Auth | JWT (jjwt 0.12.3) |
| Database | H2 (dev) / PostgreSQL (prod) |
| ORM | Spring Data JPA |
| Java | 17+ |

## Getting Started

```bash
git clone https://github.com/prasenjitdebnath/jwt-auth-spring-boot.git
cd jwt-auth-spring-boot
./mvnw spring-boot:run
```

Server starts on `http://localhost:8080`

## API Reference

### Register
```http
POST /api/v1/auth/register
Content-Type: application/json

{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john@example.com",
  "password": "secret123"
}
```
Response: `{ "token": "eyJ...", "email": "john@example.com", "role": "USER" }`

### Login
```http
POST /api/v1/auth/login
Content-Type: application/json

{ "email": "john@example.com", "password": "secret123" }
```

### Access protected endpoint
```http
GET /api/v1/me
Authorization: Bearer <token>
```

### Admin-only endpoint
```http
GET /api/v1/admin/dashboard
Authorization: Bearer <admin-token>
```

## Project Structure

```
src/main/java/com/example/jwtauth/
├── JwtAuthApplication.java
├── config/
│   ├── SecurityConfig.java       # Spring Security filter chain
│   └── ApplicationConfig.java    # Auth provider, password encoder
├── security/
│   ├── JwtService.java           # Token generation & validation
│   └── JwtAuthFilter.java        # Validates JWT per request
├── auth/
│   ├── AuthController.java       # /register, /login
│   ├── AuthService.java
│   ├── AuthRequest.java
│   ├── AuthResponse.java
│   └── UserController.java       # Protected endpoints
└── user/
    ├── User.java                 # JPA entity + UserDetails
    ├── Role.java
    └── UserRepository.java
```

## How It Works

1. **Register** → BCrypt hash → save to DB → JWT issued
2. **Login** → verify via `AuthenticationManager` → JWT issued
3. **Request** → `JwtAuthFilter` reads Bearer token → validates → sets `SecurityContext`
4. **Authorize** → Spring Security checks roles in `SecurityConfig`

## License

MIT
