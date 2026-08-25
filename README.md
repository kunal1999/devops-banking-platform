# devops-banking-platform

A fully containerized, 6-service microservices banking platform (Python + Java), 
with a working web UI, PostgreSQL databases, JWT authentication, and real 
service-to-service communication — running entirely locally via Docker Compose. 
Built around Google's open-source Bank of Anthos demo app.

**App code**: unmodified, from [GoogleCloudPlatform/bank-of-anthos](https://github.com/GoogleCloudPlatform/bank-of-anthos) 
(Apache 2.0), included as a git submodule.
**Everything else** — Dockerfiles, Docker Compose environment, Kubernetes 
manifests, CI/CD, monitoring — built from scratch, by me.

## Architecture

<p align="center">
  <img src="./architecture diagram.png" alt="Banking Platform Architecture" width="100%">
</p>


## 🏗️ Architecture

```mermaid
graph TB

    %% =========================
    %% Presentation Tier
    %% =========================
    subgraph Presentation["🎨 Presentation Tier"]
        FE["🌐 Frontend<br/>Python / Flask"]
    end

    %% =========================
    %% Application Tier
    %% =========================
    subgraph Application["⚙️ Application / Logic Tier"]

        US["🐍 Userservice<br/>Python<br/>Auth • JWT"]

        CT["🐍 Contacts<br/>Python<br/>Linked Accounts"]

        LW["☕ Ledgerwriter<br/>Java<br/>Validate & Write Transactions"]

        BR["☕ Balancereader<br/>Java<br/>Account Balances"]

        TH["☕ Transactionhistory<br/>Java<br/>Transaction History"]

    end

    %% =========================
    %% Data Tier
    %% =========================
    subgraph Data["🗄️ Data Tier"]

        ADB[("🐘 Accounts DB<br/>PostgreSQL")]

        LDB[("🐘 Ledger DB<br/>PostgreSQL")]

    end

    %% =========================
    %% Frontend → Microservices
    %% =========================
    FE -->|"HTTP :8080"| US
    FE -->|"HTTP :8081"| CT
    FE -->|"HTTP :8082"| LW
    FE -->|"HTTP :8083"| BR
    FE -->|"HTTP :8084"| TH

    %% =========================
    %% Microservices → Databases
    %% =========================
    US -->|"SQL"| ADB
    CT -->|"SQL"| ADB

    LW -->|"SQL"| LDB
    BR -->|"SQL"| LDB
    TH -->|"SQL"| LDB

    %% =========================
    %% Service-to-Service Dependency
    %% =========================
    LW -.->|"Balances API :8083"| BR

    %% =========================
    %% Styling
    %% =========================
    classDef frontend fill:#dbeafe,stroke:#2563eb,stroke-width:2px,color:#1e3a8a
    classDef python fill:#dcfce7,stroke:#16a34a,stroke-width:2px,color:#14532d
    classDef java fill:#ffedd5,stroke:#ea580c,stroke-width:2px,color:#7c2d12
    classDef database fill:#ede9fe,stroke:#7c3aed,stroke-width:2px,color:#4c1d95

    class FE frontend
    class US,CT python
    class LW,BR,TH java
    class ADB,LDB database
```

### 🔗 Communication

| Connection                       | Type         | Purpose                             |
| -------------------------------- | ------------ | ----------------------------------- |
| `frontend → userservice`         | HTTP         | Authentication & user operations    |
| `frontend → contacts`            | HTTP         | Contact / linked account operations |
| `frontend → ledgerwriter`        | HTTP         | Create transactions                 |
| `frontend → balancereader`       | HTTP         | Retrieve balances                   |
| `frontend → transactionhistory`  | HTTP         | Retrieve transaction history        |
| `userservice → accounts-db`      | SQL          | User/account data                   |
| `contacts → accounts-db`         | SQL          | Contact/account data                |
| `ledgerwriter → ledger-db`       | SQL          | Write transactions                  |
| `balancereader → ledger-db`      | SQL          | Read balances                       |
| `transactionhistory → ledger-db` | SQL          | Read transaction history            |
| `ledgerwriter → balancereader`   | Internal API | Balance validation                  |

> **Note:** Solid arrows represent API/database communication. Dashed arrows represent **service-to-service dependencies**.

3-tier architecture: a Flask frontend, five backend microservices (Python + Java) 
handling authentication and transactions, and two Postgres databases.

## Progress

- [x] Multi-stage Dockerfiles for all 6 services (`userservice`, `contacts`, 
      `ledgerwriter`, `balancereader`, `transactionhistory`, `frontend`)
- [x] Local docker-compose environment: both databases running with schema 
      and demo data auto-loaded on startup
- [x] All 6 services running together via Compose, fully verified end-to-end
- [x] JWT-based authentication verified end-to-end (login → signed token → 
      verified across services)
- [x] Real service-to-service communication working across the whole stack
- [ ] Kubernetes manifests
- [ ] CI/CD pipeline (GitHub Actions)
- [ ] Monitoring (Prometheus/Grafana)
- [ ] Canary deployments (Argo Rollouts) + cost-aware autoscaling (Karpenter) on AWS

## Tech stack

Docker, Docker Compose, PostgreSQL, Python (Flask), Java (Spring Boot/Maven), 
Kubernetes (planned), GitHub Actions (planned), Prometheus/Grafana (planned)

## Running locally

**Prerequisites**: Docker Desktop, Git

```bash
git clone --recurse-submodules https://github.com/<your-username>/devops-banking-platform.git
cd devops-banking-platform
docker compose up --build
```

This starts all 6 services and both databases (schema + demo data pre-loaded). 
Once running:
