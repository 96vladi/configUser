# Central Configuration for Microservices

This folder contains the central configuration files used by the microservices in this repository. It holds the shared configuration (application.yml and service-specific YAMLs) that the services use when bootstrapping and when the Config Server is running.

The steps below explain how to prepare environment variables, start the infrastructure with Docker Compose, verify databases, and bring up services in the recommended order.

PREREQUISITES
- Docker and Docker Compose installed and running.
- Java and/or runtimes required by the microservices available if you plan to run services locally.
- Access to modify .env files for each microservice and this repository's main .env (if present).

STEP-BY-STEP STARTUP GUIDE

1) Configure environment variables
 - For each microservice project, copy or rename `.env.example` to `.env` and fill in the required values (database URLs, credentials, ports, API keys, etc.).
 - Also set the `.env` for the central configuration or for this folder if there is a `.env.example` here.

2) Start infrastructure with Docker Compose
 - From the repository root (where `docker-compose.yml` is located), run:
	 docker compose up -d
 - This will start the configured infrastructure containers (databases, message brokers, registry, etc.).

3) Verify databases
 - After the containers are up, verify the relational databases have been created (example SQL to run against your Postgres instance):
	 CREATE DATABASE tg_user_service_db;
	 CREATE DATABASE tg_program_admin_db;
	 CREATE DATABASE tg_assignments_db;
 - For MongoDB, verify the `tg_evaluations_db` database exists (you can use the mongo shell or a GUI client).

4) Start the microservices (recommended order)
 - Start services in the following order to ensure discovery and configuration are available:
	 1. Registry Service (port 8761) — service discovery
	 2. Config Server (port 8888) — central configuration
	 3. API Gateway (port 8080)
	 4. Then start the remaining microservices (user-service, program-admin-service, notification-service, evaluation-service, etc.).

	Start each service using your usual command (for Spring Boot projects, for example):
	 ./mvnw spring-boot:run
	 or
	 ./gradlew bootRun
	 or run the packaged jar with `java -jar`.

VERIFICATION
- Check the registry UI (http://localhost:8761) to confirm services register.
- Check Config Server (http://localhost:8888/) endpoints to confirm it serves configuration.
- Check API Gateway (http://localhost:8080/) to confirm routing to downstream services.

TROUBLESHOOTING
- If a service fails to start, check logs for connection errors to the Config Server or the databases.
- Ensure the `.env` variables (especially database credentials and hostnames) match the docker-compose network and container names.
- If databases are not created automatically, create them manually using the SQL statements above against your Postgres instance.

SECURITY NOTE
- Do not commit `.env` files with secrets to version control. Keep credentials safe and use secret management for production.

