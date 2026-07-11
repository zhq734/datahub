# DataHub Multi-Source Data Platform

[中文](README.md) | **English**

DataHub is an integrated data access and data service platform for engineering, data, integration, and operations teams. It combines multi-source connection management, dataset modeling, Open API publishing, credential governance, data migration, configuration import/export, and desktop delivery in one workspace.

## Screenshots

The screenshots are captured from a local environment with anonymized demo data.

| Data Source Management | Dataset Management |
| --- | --- |
| ![Data Source Management](docs/images/datahub-datasource.png) | ![Dataset Management](docs/images/datahub-dataset.png) |

| Open API Management | Toolbox |
| --- | --- |
| ![Open API Management](docs/images/datahub-openapi.png) | ![Toolbox](docs/images/datahub-toolbox.png) |

## Product Positioning

DataHub is more than a database client. It is designed around the complete data service workflow: connect, model, publish, govern, and deliver.

- **Connect**: manage relational databases, NoSQL stores, search engines, object storage, file transfer services, HTTP APIs, and message middleware through one model.
- **Model**: turn raw sources into reusable datasets with SQL, ES DSL, Mongo Pipeline, variables, field mapping, caching, and query limits.
- **Publish**: expose datasets as governed Open APIs with versions, documentation, credentials, white lists, rate limits, and logs.
- **Operate**: support migration jobs, configuration transfer, runtime settings, and daily developer tools.
- **Deliver**: run as a web application or package as a Tauri desktop client with local H2 storage.

## Key Features

| Area | Capabilities |
| --- | --- |
| Data Sources | Groups, connection tests, typed configuration, dedicated workbenches, import/export |
| Data Browsing | Schema browsing, SQL execution, paginated queries, basic DDL/DML |
| Import Sources | Excel, CSV, HTTP API preview, local mirroring, re-import, progress tracking |
| Datasets | SQL / ES DSL / Mongo Pipeline, field mapping, variables, cache TTL |
| Open APIs | API publishing, versioning, pagination, OpenAPI / Markdown / Postman / curl export |
| Credentials | AK/SK, groups, expiration, IP whitelist, rate-limit override, statistics |
| Migration | Jobs, table mapping, field mapping, filters, table creation strategy, logs |
| Toolbox | HTTP, JSON, XML, Base64, QR Code, Cron, URL, text diff |
| Desktop | Tauri shell, bundled JRE, embedded H2, single-machine delivery |

## Supported Sources

| Category | Sources |
| --- | --- |
| Relational Databases | MySQL, PostgreSQL, Oracle, Dameng, Kingbase, GBase8s, Doris |
| NoSQL / Cache | Redis, MongoDB |
| Search | Elasticsearch, OpenSearch |
| Object Storage | MinIO, Alibaba Cloud OSS, Huawei OBS, AWS S3 |
| File / Coordination | FTP, SFTP, ZooKeeper |
| Messaging | Kafka, RocketMQ |
| Import Sources | Excel, CSV, HTTP API |

File-based and HTTP-based sources are imported into a local mirror database first, then reused for queries, dataset modeling, and API publishing.

## Typical Workflow

```text
Connect a data source
    ↓
Create a dataset
    ↓
Configure fields, variables, cache, and query rules
    ↓
Publish an Open API
    ↓
Issue AK/SK, export docs, and monitor call logs
```

The same workspace also supports cross-source migration, configuration import/export, HTTP debugging, and offline desktop delivery.

## Architecture

```text
┌─────────────────────────────────────────────────────────────┐
│                         Admin UI                            │
│        Vue 3 + TypeScript + Vite + Element Plus             │
│                    Web / Tauri Desktop                      │
└────────────────────────────┬────────────────────────────────┘
                             │ REST / SSE / WebSocket
┌────────────────────────────▼────────────────────────────────┐
│                    Spring Boot Service                      │
│ Data Source | Dataset | API Publishing | Migration | Config  │
└───────────────┬──────────────┬──────────────┬───────────────┘
                │              │              │
        Metadata Store    File / HTTP Mirror   External Sources
      PostgreSQL / H2       backend-data/       DB / ES / MQ / OSS
```

## Quick Start

### Requirements

- JDK 17+
- Maven 3.9+
- Node.js 18+, Node.js 20 LTS recommended
- npm 9+
- PostgreSQL 14+ for server mode
- Rust toolchain and Tauri CLI for desktop packaging

### Server Development Mode

1. Create the PostgreSQL metadata database.

```sql
CREATE DATABASE datasource_db;
```

2. Initialize the database schema.

```bash
psql -U postgres -d datasource_db -f backend/src/main/resources/db/pg/schema.sql
for f in backend/src/main/resources/db/pg/migration/*.sql; do
  psql -U postgres -d datasource_db -f "$f"
done
```

3. Configure `backend/src/main/resources/application.yml`.

```yaml
server:
  port: 18123

spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/datasource_db
    username: postgres
    password: your-password
```

4. Run the backend.

```bash
cd backend
mvn spring-boot:run
```

5. Run the frontend.

```bash
cd frontend
npm install
npm run dev
```

Default development URLs:

- Web UI: `http://localhost:3123`
- Backend API: `http://localhost:18123`

### Desktop / Embedded Mode

Embedded mode uses a local H2 metadata database and creates a default admin account:

- Username: `admin`
- Password: `Admin@123`

```bash
cd backend
mvn spring-boot:run -Dspring.profiles.active=embedded
```

Package the desktop app:

```bash
./scripts/build-tauri.sh
```

The script builds the frontend, packages the backend, generates a slim JRE, and injects all required resources into the Tauri application. The current desktop packaging workflow is mainly optimized for macOS, with artifacts written to `dist/`.

## Project Structure

```text
data-collect/
├── backend/                  # Spring Boot backend service
│   └── src/main/
│       ├── java/com/datasource/
│       └── resources/
├── frontend/                 # Vue 3 admin UI and Tauri project
│   ├── src/
│   └── src-tauri/
├── docs/                     # Feature plans and product screenshots
├── scripts/                  # Build and packaging scripts
└── openspec/                 # Change proposals and specifications
```

## Common Commands

```bash
# Backend unit tests
cd backend && mvn test

# Backend full verification
cd backend && mvn verify

# Frontend production build
cd frontend && npm run build

# Tauri desktop packaging
./scripts/build-tauri.sh
```

## Notes

- Some commercial database drivers may depend on enterprise artifact repositories or local Maven cache.
- Server mode uses PostgreSQL, while desktop mode uses an H2 file database.
- `backend/src/main/resources/static/` contains generated frontend assets and should not be maintained manually.
- Download center and related capabilities depend on the Tauri runtime. Browser mode provides the core Web experience.
