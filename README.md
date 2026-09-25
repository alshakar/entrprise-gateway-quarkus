# Reactive Enterprise Integration Gateway

A production-grade, non-blocking, asynchronous enterprise integration gateway built on **Quarkus** ( targeting Java 25 LTS+), powered by **SmallRye Mutiny** and **Eclipse Vert.x**.

This gateway orchestrates calls across dynamic SOAP web services, multi-tenant/multi-auth REST endpoints, and an Oracle Database using non-blocking I/O.

---

## 🏗️ Architecture Overview


```

```
                      ┌───────────────────────────┐
                      │   Client / Ingress API    │
                      └─────────────┬─────────────┘
                                    │
                             X-Request-ID
                                    ▼
                    ┌───────────────────────────────┐
                    │   GatewayResource (JAX-RS)    │
                    │     (Quarkus REST / Mutiny)   │
                    └───────────────┬───────────────┘
                                    │
         ┌──────────────────────────┼──────────────────────────┐
         ▼                          ▼                          ▼

```

┌─────────────────────────┐ ┌─────────────────────────┐ ┌─────────────────────────┐
│   RestGatewayService    │ │   SoapGatewayService    │ │  OracleGatewayService   │
│                         │ │                         │ │                         │
│ • Dynamic Target URLs   │ │ • CXF Async Transport   │ │ • Reactive Oracle Client│
│ • OAuth2 Token Cache    │ │ • WS-Security Header    │ │ • Non-blocking Stored   │
│ • Custom Headers        │ │   Injection             │ │   Procedures & Packages │
└────────────┬────────────┘ └────────────┬────────────┘ └────────────┬────────────┘
│                           │                           │
▼                           ▼                           ▼
External REST APIs           SOAP Web Services             Oracle Database

```

### Key Capabilities
- **Non-blocking End-to-End**: Zero thread-blocking calls from HTTP ingress down to DB connection pools and external webservices.
- **Dynamic REST Routing**: Dynamic endpoint URL dispatch with cached OAuth2 Client Credentials handling using Caffeine + Mutiny `Uni`.
- **Async SOAP Dispatch**: `quarkus-cxf` integration leveraging asynchronous HTTP conduits and dynamic WS-Security `UsernameToken` injection.
- **Reactive Oracle Client**: Execution of PL/SQL packages, functions, and queries using `io.vertx.mutiny.oracleclient.OraclePool`.
- **Structured JSON Observability**: Request tracking through `ContainerRequestFilter` and `ContainerResponseFilter` with correlation IDs (`X-Request-ID`) formatted for AWS CloudWatch ingestion.
- **GraalVM Native Ready**: Engineered for sub-second start times and minimal memory footprint under GraalVM Native Image builds.

---

## 🛠️ Prerequisites

- **JDK 25** (or JDK 21+ LTS)
- **Apache Maven 3.9.x+**
- **GraalVM Native Image** (optional, for native builds)
- **Docker / Container Runtime** (for containerized deployments)

---

## 🚀 Getting Started

### 1. Development Mode (Live Coding)

Start Quarkus in dev mode with live reload enabled:

```bash
./mvnw quarkus:dev

```

Dev UI is accessible at `http://localhost:8080/q/dev`.

### 2. Running Unit & Integration Tests

Execute the test suite (includes WireMock and REST Assured assertions):

```bash
./mvnw clean test

```

---

## 📦 Packaging & Native Compilation

### Standard JVM Compilation

Build a fast-jar package:

```bash
./mvnw clean package
java -jar target/quarkus-app/quarkus-run.jar

```

### Native Executable (GraalVM)

Compile directly to a standalone native binary:

```bash
./mvnw clean package -Dnative

```

To build a native image using a container engine (e.g., Docker/Podman) without installing GraalVM locally:

```bash
./mvnw clean package -Dnative -Dquarkus.native.container-build=true

```

Execute the native binary:

```bash
./target/enterprise-gateway-1.0.0-SNAPSHOT-runner

```

---

## 📊 Endpoints & Testing

### Request Tracing Header

Pass an `X-Request-ID` header with any incoming request. If omitted, the gateway automatically generates a UUID correlation ID for structured log tracing.

* **REST Proxy Endpoint**: `POST /api/v1/gateway/rest`
* **SOAP Proxy Endpoint**: `POST /api/v1/gateway/soap`
* **Oracle DB Execution Endpoint**: `POST /api/v1/gateway/oracle`

Should I proceed with File 2 (`pom.xml`)?

```
