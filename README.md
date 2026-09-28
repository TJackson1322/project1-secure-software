# Project 1: Chapters 1–4 — Secure Software Design

A small Spring Boot application from the Secure Software Development project. It demonstrates authenticated employee endpoints, repository-based database access, and local HTTPS setup.

## Requirements

- Java 17 or newer
- Maven 3.8 or newer

## Run locally

```bash
mvn spring-boot:run
```

The demo uses an in-memory H2 database. Employee endpoints require HTTP Basic authentication. For local testing, the defaults are username `admin` and password `change-this-password`; override them with `APP_USERNAME` and `APP_PASSWORD` environment variables. Do not use these demo defaults in a deployed or production system.

Example request:

```bash
curl -u admin:change-this-password http://localhost:8080/api/employees
```

Endpoints:

- `GET /api/employees` — list employees
- `GET /api/employees/{id}` — look up an employee by ID
- `GET /api/employees/search?email=user@example.com` — look up an employee by email

The project starts with an empty in-memory database. No employee records are seeded.

## Optional local HTTPS

The paper describes using HTTPS on port 8443. For local development, create a self-signed PKCS12 keystore (do not commit the generated `.p12` file):

```bash
keytool -genkeypair -alias secureapp -keyalg RSA -keysize 2048 \
  -storetype PKCS12 -keystore src/main/resources/secureapp.p12 -validity 3650
```

Then add these settings to `src/main/resources/application.properties`, replacing the environment variable value locally as appropriate:

```properties
server.port=8443
server.ssl.enabled=true
server.ssl.key-store=classpath:secureapp.p12
server.ssl.key-store-type=PKCS12
server.ssl.key-store-password=${KEYSTORE_PASSWORD}
server.ssl.key-alias=secureapp
```

Set `KEYSTORE_PASSWORD` in your shell before running the application. A self-signed certificate is for local testing only; browsers will warn that it is not trusted.

## Security notes

- `EmployeeRepository.findByEmail` lets Spring Data JPA bind the email value instead of concatenating user input into SQL.
- `SecurityConfig` requires authentication for all `/api/employees/**` endpoints.
- Credentials and keystore files are not committed as secrets. Change the demo password before sharing or running outside a local classroom environment.
- This sample is educational and is not a production-ready security configuration.
