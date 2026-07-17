# DataHub Multi-Source Data Platform

[中文](README.md) | **English**

DataHub is an integrated data access and data service platform for engineering, data, integration, and operations teams. It brings multi-source connection management, dataset modeling, Open API publishing, credential governance, data migration, relationship graphs, OCR recognition, and a developer toolbox together in one workspace. It works out of the box, supports one-command containerized deployment, and fits internal data service platforms, data middle-platform tooling, and enterprise data governance centers.

## Why DataHub

- **All-in-one data workspace** — 20+ data source types under one roof. No more switching between tools for databases, NoSQL, search engines, object storage, message brokers, and file transfer.
- **Data to API in three steps** — Connect a source → Create a dataset → Publish an Open API. Built-in versioning, AK/SK credentials, rate limiting, and call logs — no need to build your own gateway.
- **Zero-friction deployment** — One-command Docker deployment with an embedded H2 database. No external dependencies needed to get started; switch to PostgreSQL in production for better performance and reliability.
- **Multi-theme & multi-tab workspace** — Light and dark theme support. Open multiple data sources in parallel tabs to boost productivity.
- **Data lineage & relationship graphs** — Visual exploration of relationships between data sources with snap alignment, drag-and-drop nodes, and inline data preview.
- **Enterprise data migration** — Cross-source migration with table mapping, field mapping, filters, table creation strategies, execution logs, and support for custom SQL and datasets as migration sources.
- **Built-in developer toolbox** — HTTP debugger, JSON/XML formatter, Base64, QR code, Cron expression builder, URL encoder/decoder, and text diff — everyday tools at your fingertips.
- **OCR text recognition** — Integrated OCR service for image text extraction, extending data collection capabilities.
- **Multi-platform delivery** — Available as Docker container and desktop installers (macOS / Windows), covering server, intranet, and offline scenarios.

## Screenshots

> The screenshots are captured from a local environment with anonymized demo data.

| Data Source Management | Dataset Management |
| --- | --- |
| ![Data Source Management](docs/images/datahub-datasource.png) | ![Dataset Management](docs/images/datahub-dataset.png) |

| Open API Management | Toolbox |
| --- | --- |
| ![Open API Management](docs/images/datahub-openapi.png) | ![Toolbox](docs/images/datahub-toolbox.png) |

| System Settings | Data Migration |
| --- | --- |
| ![System Settings](docs/images/datahub-settings.png) | ![Data Migration](docs/images/datahub-migration.png) |

| Relationship Graph | OCR Service |
| --- | --- |
| ![Relationship Graph](docs/images/datahub-graph.png) | ![OCR Service](docs/images/datahub-ocr.png) |

| Login | Dark Theme |
| --- | --- |
| ![Login](docs/images/datahub-login.png) | ![Dark Theme](docs/images/datahub-dark-theme.png) |

## Key Features

| Area | Capabilities |
| --- | --- |
| Data Sources | Groups, connection tests, typed configuration, dedicated workbenches, import/export, card/list views |
| Data Browsing | Schema browsing, SQL editor, paginated queries, DDL/DML, data copy, table creation |
| Import Sources | Excel, CSV, HTTP API preview, local mirroring, re-import, progress tracking |
| Datasets | SQL / ES DSL / Mongo Pipeline, field mapping, variables, cache TTL, query limits |
| Open APIs | Publishing, versioning, pagination, OpenAPI / Markdown / Postman / curl export |
| Credentials | AK/SK, groups, expiration, IP whitelist, rate-limit override, statistics |
| Migration | Jobs, table mapping, field mapping, filters, table creation strategy, custom SQL source, monitoring |
| Relationship Graphs | Data lineage visualization, snap alignment, drag-and-drop, data preview, graph scanning |
| OCR | Image text recognition, multi-engine support, service configuration |
| Toolbox | HTTP, JSON, XML, Base64, QR Code, Cron, URL, text diff |
| System Settings | Global variables, cache strategy, query limits, graph scanning, OCR service, download preferences, feedback |

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

## Architecture

```text
┌─────────────────────────────────────────────────────────────┐
│                         Admin UI                            │
│        Vue 3 + TypeScript + Element Plus                    │
│            Web / Desktop Client / Docker                    │
└────────────────────────────┬────────────────────────────────┘
                             │ REST / SSE / WebSocket
┌────────────────────────────▼────────────────────────────────┐
│                    Spring Boot Service                      │
│ Data Source | Dataset | API Publishing | Migration | Graph | OCR │
└───────────────┬──────────────┬──────────────┬───────────────┘
                │              │              │
        Metadata Store    File / HTTP Mirror   External Sources
      PostgreSQL / H2       Local Storage       DB / ES / MQ / OSS
```

## Deployment

### Docker (Recommended)

The fastest way to get started — no dependencies required.

**1. Create `docker-compose.yml`**

```yaml
services:
  app:
    image: data-collect:latest
    ports:
      - "${APP_PORT:-8123}:8123"
    environment:
      SPRING_PROFILES_ACTIVE: embedded
      SERVER_PORT: 8123
      SPRING_DATASOURCE_URL: jdbc:h2:file:/app/data/datasource;AUTO_SERVER=TRUE;MODE=PostgreSQL;DATABASE_TO_LOWER=TRUE;INIT=CREATE SCHEMA IF NOT EXISTS PUBLIC\;CREATE DOMAIN IF NOT EXISTS JSONB AS JSON
      APP_STORAGE_PATH: /app/backend-data
      JAVA_OPTS: ${JAVA_OPTS:--Xms256m -Xmx512m}
    volumes:
      - h2-data:/app/data
      - app-data:/app/backend-data
    restart: unless-stopped

volumes:
  h2-data:
  app-data:
```

**2. Start the service**

```bash
docker compose up -d
```

**3. Access the app**

Open `http://localhost:8123` in your browser and register an account with your email.

**4. Environment variables**

| Variable | Default | Description |
| --- | --- | --- |
| `APP_PORT` | `8123` | Host port mapping |
| `JAVA_OPTS` | `-Xms256m -Xmx512m` | JVM memory options |

**5. Data persistence**

Docker Compose creates two named volumes by default:

- `h2-data` — Metadata database files (source configs, users, datasets, API settings, etc.)
- `app-data` — File-based data source mirror storage (Excel, CSV, HTTP API imports)

**6. Stop and clean up**

```bash
# Stop the service (keep data)
docker compose down

# Stop and remove volumes (delete all data)
docker compose down -v
```

### Desktop Client

Available as macOS (DMG) and Windows (MSI / EXE) installers with a bundled runtime — double-click to run. Ideal for offline and single-machine use.

Register an account with your email on first launch.

## Notes

- Register with your email on first use. Login supports both password and email verification code.
- Docker mode uses an embedded H2 database by default. For production, switch to PostgreSQL for better concurrency performance.
- The desktop client stores data locally using H2. Back up your data before uninstalling.
