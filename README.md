# java-app — Payment Service (skeleton)

This repository is the payment service for the project — currently provided as a small Java application skeleton. It contains source, a Dockerfile, and simple scripts to build and run the service locally or in a container.

This README documents how to build, run, and extend the payment service. The current implementation is a minimal placeholder (see `src/Main.java`) that prints a greeting; use it as a starting point for the real payment logic and HTTP endpoints.

## Quick facts

- Language: Java
- Java version: 17 (see `pom.xml` and Dockerfile)
- Build system: Maven
- Image: multi-stage Dockerfile using Eclipse Temurin 17

## Contents

- `src/` — Java sources (currently `Main.java`)
- `pom.xml` — Maven configuration
- `Dockerfile` — multi-stage build for container image
- `run.sh` — convenience script to compile and run locally
- `ci-commands.sh` — helper commands used by CI / GitHub Actions

## Requirements

- Java 17 (JDK) to build and run locally
- Maven 3.6+ to build via `mvn`
- Docker (optional) to build and run container images

## Build

Build with Maven:

```bash
mvn -q -DskipTests package
```

This will compile sources and produce a jar in `target/` according to the `pom.xml`.

Alternatively, compile and run the lightweight class files (this project uses `src` as the source dir):

```bash
./run.sh [args...]
# or
javac -d out src/Main.java
java -cp out Main [name]
```

## Run (Docker)

Build the image and run a container:

```bash
docker build -t java-app:latest .
docker run --rm java-app:latest
```

The included `Dockerfile` is a multi-stage image that compiles the sources in the builder stage and then runs the compiled classes in a slim runtime image.

## Service behavior / Endpoints

Currently the repository is a skeleton payment service. The current `Main` class is a placeholder CLI program that prints a greeting and echoes arguments. When you implement the real payment service, consider the following minimal contract to document here:

- Inputs: JSON payloads or HTTP POST requests containing payment details (amount, currency, payer_id, method, metadata)
- Outputs: HTTP 200/201 on success with a payment id; 4xx on client errors; 5xx on server errors
- Idempotency: support idempotency keys for safe retries
- Security: authenticate requests (mutual TLS / OAuth / API keys) and log minimal sensitive data

Add actual endpoints and API documentation (OpenAPI/Swagger) when the HTTP layer is implemented.

## Configuration & Environment variables

The real payment service should accept configuration via environment variables. Example variables to implement:

- PAYMENT_DB_URL — JDBC URL for persistent storage
- PAYMENT_API_KEY — key used to authenticate calls to downstream providers
- LOG_LEVEL — logging verbosity (INFO, DEBUG, ERROR)
- PORT — HTTP port to serve requests

For local development you can set these in the shell or use a `.env` managed by your tooling.

## Security notes

- The `pom.xml` in this repository currently lists a few older dependency versions (see file). Some of these versions are known to contain CVEs (for example older Log4j and Jackson versions). Before production use, update all dependencies to maintained versions and run dependency security scanning.
- Do not log full payment payloads or sensitive PAN (primary account number) data. Use tokenization and redact logs.

## CI / CD

The repo includes `ci-commands.sh` which contains helpful commands used by CI workflows to build, push images, and run scans. Integrate with GitHub Actions or your CI provider to:

- Build and test on pull requests
- Run dependency & static analysis scans
- Build and publish container images on merge to `main`

## Contributing

1. Create a branch for your feature: `git checkout -b feat/payment/your-feature`
2. Implement a small, testable change (keep commits focused)
3. Add unit tests and, when applicable, an API contract (OpenAPI)
4. Open a PR against `main`, include a short description and testing steps

## Running locally (example)

Build and run locally:

```bash
mvn -q -DskipTests package
java -cp target/java-app-1.0.0.jar Main
```

Run via Docker (development):

```bash
docker build -t bsalunke/java-app:dev .
docker run --rm bsalunke/java-app:dev
```

If you want to iterate quickly without Maven, use `./run.sh`.

## Next steps (recommended)

- Replace the placeholder `Main` with an HTTP server (e.g., Spring Boot, Micronaut, or a lightweight HTTP server)
- Add request validation, persistence for payments, and integration with at least one payment gateway (mock first)
- Add automated tests (unit + integration) and a basic OpenAPI spec
- Upgrade dependencies and add a dependency-check step in CI

## Contact

Maintainer: bsalunke
File issues or PRs in this repository.

## License

This project does not include a license file. Add a LICENSE file (for example, MIT or Apache-2.0) to make the terms explicit.
