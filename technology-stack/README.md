# Technology Stack

> **Primorac Consulting — Technical Experience & Engineering Portfolio**  
> **Document version:** 1.2 · Published 18 October 2026  
> **Scope:** Professional engineering experience, hands-on prototypes, and technical studies. The technologies listed below have different histories and depths of use.

## Overview

This document records technologies used across enterprise information systems, database administration and modernization, data integration, engineering prototypes, and technical studies. It is organized by **engineering context**, rather than by self-assessed proficiency ratings.

Three contexts are distinguished throughout:

- **Professional engineering:** Technologies used in client or employer projects, operational systems, architecture, implementation, administration, testing, or technical support.
- **Hands-on prototype:** Technologies installed, configured, programmed, and/or tested in independent proof-of-concept work. A prototype is not presented as a production deployment.
- **Technical study:** Technologies or algorithms explored through books, practical exercises, technical evaluations, or adapted examples. Listing a technology does not imply that every associated example was implemented or tested.

Dates, versions, and operational results are stated only when relevant to a documented project. Technology names are not intended as a claim of equal expertise across the entire list.

---

## 1. Enterprise Databases and SQL

| Technology / platform | Context | Engineering activities |
| --- | --- | --- |
| **IBM DB2 — LUW** | Professional engineering | Database design and administration, SQL performance analysis, source metadata extraction, schema assessment, and enterprise migration from DB2 to PostgreSQL. |
| **IBM DB2 — mainframe** | Professional engineering · legacy | Production database administration in IBM mainframe environments; historical systems engineering. |
| **PostgreSQL** | Professional engineering; prototypes | Database design, SQL transformation, staging and destination structures, migration controls, data reconciliation, and CDC target testing. |
| **Amazon Aurora PostgreSQL** | Professional engineering · cloud testing | Target technology in an insurance modernization framework; included in AWS-managed-service validation. |
| **Azure Database for PostgreSQL** | Professional engineering | Target platform in a separate DB2 modernization programme, including participation in the production migration and cutover. |
| **Oracle** | Professional experience | Relational database experience across earlier enterprise work; specific project details are not expanded in this portfolio. |
| **Microsoft SQL Server** | Prior exposure / listed technical skill | Relational database technology included in the professional skills inventory; no specific delivery claim is made here. |

**Related methods:** relational modelling, physical database design, indexing, SQL optimization, JDBC-based data movement, schema migration, data reconciliation, and integrity controls.

### Documented example: metadata-driven DB2 modernization

An insurance Commission and Rewards System modernization project involved **eight DB2 source databases**, approximately **1,400 inventoried tables**, and approximately **400 selected migration tables**. The framework combined CDC capture and PostgreSQL staging with Spark SQL transformations. Local, integration, and AWS tests were performed; the project case study does **not** claim a completed production cutover for this framework.

---

## 2. Data Engineering and Distributed Processing

| Technology | Context | Engineering activities |
| --- | --- | --- |
| **Apache Spark** | Professional engineering; prototypes | Migration batch processing, Spark SQL execution, distributed data transformations, partitioning and performance tuning. |
| **Spark SQL** | Professional engineering | Schema conversion, multi-table joins, business-rule transformations, reconciliation, and source-to-target mapping. |
| **Scala** | Professional engineering; prototypes; study | DB2 metadata tools, migration automation, connector-configuration generation, Spark transformations, and mathematical/algorithmic exercises. |
| **Apache Spark Structured Streaming** | Professional engineering | Streaming-based migration orchestration/execution architecture and checkpointed processing within the migration framework. Personal contributions centred on architecture, Scala utilities, and transformation SQL; Java/Spring orchestration was implemented by another team developer. |
| **Delta Lake / lakehouse techniques** | Hands-on prototype / ongoing engineering study | Bronze/Silver/Gold processing, validation, cleansing, and analytical pipeline experimentation in the LedgerLake project. |
| **Apache Flink** | Technical study | Streaming and SQL-based processing concepts; not presented as a deployed component of the documented SeaTunnel CDC cases. |

**Engineering topics:** data-model transformation, selective caching, broadcast joins, JDBC fetch strategies, workload segmentation, incremental processing, restartability, and test-based reconciliation.

---

## 3. Change Data Capture and Event Integration

| Technology | Context | Engineering activities |
| --- | --- | --- |
| **Debezium** | Professional engineering | DB2 snapshot and CDC source-connector integration in enterprise migration frameworks. The SeaTunnel PoC uses a Debezium-compatible JSON event format, but not a separate Debezium service. |
| **Apache Kafka** | Professional engineering; prototypes | CDC transport, event buffering, consumer groups, offset observation, retained-event replay, and integration testing. |
| **Kafka Connect** | Professional engineering | Source/sink connector configuration and movement of CDC data into PostgreSQL staging. |
| **Apache SeaTunnel** | Hands-on prototype | Two locally tested MySQL-to-PostgreSQL CDC architectures: direct and Kafka-mediated. Includes initial snapshot, DML propagation, restart/recovery, replay, and performance scenarios. |
| **Kafka KRaft** | Hands-on prototype | Single-node Kafka deployment and test infrastructure in the SeaTunnel Case 2 environment. |

**Selected technical example:** [Apache SeaTunnel CDC — Architecture, Testing and Planned Transformations](../examples/seatunnel-cdc/README.md).

The SeaTunnel example uses **SeaTunnel 2.3.12**, **MySQL 8.4**, **PostgreSQL 18.1**, and, for Case 2, **Kafka 3.8.1**. Its documented tests ran locally. Cloud-based scale testing is the **next planned test phase**, not a completed result. A third case exploring multi-table CDC consolidation remains under design consideration.

---

## 4. Programming Languages and Development

| Language / technology | Context | Usage |
| --- | --- | --- |
| **SQL** | Professional engineering | Query tuning, database administration, migration logic, transformation, integrity validation, and reporting. |
| **Scala** | Professional engineering; prototypes; study | DB2 metadata and migration tooling, Apache Spark, mathematical and analytical implementations. |
| **Java** | Earlier professional programming; prototype build/testing | Java programming background and Java-based runtime/tooling familiarity. The documented enterprise migration application's Java/Spring components were written by another developer. |
| **Python** | Prototypes; technical study | Automation, local analytical exercises, test and benchmark tooling. |
| **FORTRAN** | Historical professional/academic experience | Early programming and numerical-computing work. |
| **Pascal** | Historical programming experience | Earlier programming-language work. |
| **Shell / Bash** | Engineering prototypes; operations | Docker/SeaTunnel administration helpers, reproducible environment setup, scripted tests and validation. |

**Development tools:** Git, GitHub, Azure Repos, IntelliJ IDEA, Maven, sbt, Jupyter, and associated command-line environments. These tools were used in different projects and periods; no claim is made that all were used together.

---

## 5. Cloud Platforms and Delivery

### Amazon Web Services (AWS)

| Service | Documented context |
| --- | --- |
| **Amazon Aurora PostgreSQL** | Destination database used during cloud validation of the enterprise migration framework. |
| **Amazon MSK / MSK Connect** | Managed Kafka and connector services used in migration-framework cloud testing. |
| **Amazon EMR** | Spark execution environment in the framework's AWS test path. |
| **Amazon S3** | Supporting AWS storage technology in the documented framework environment. |
| **Amazon EC2** | Self-hosted agent environment supporting Azure DevOps integration workflows. |
| **AWS Secrets Manager / CloudWatch** | Services included in the migration project technology footprint. |

### Microsoft Azure

- **Azure Database for PostgreSQL:** target platform in a separate migration programme with production-cutover participation.
- **Azure DevOps / Azure Repos:** source control, pipeline-driven integration testing, and controlled promotion of the migration framework.

**Scope distinction:** the large DB2-to-Aurora framework passed through local, CI/integration, and AWS-managed-service validation. The published case study does not assert an end-to-end production cutover or production-scale performance certification for that framework.

**Planned work:** the independent SeaTunnel CDC prototype has **not** been tested on a cloud platform. Its next evaluation phase is planned to compare throughput, Kafka lag, CPU/memory use, Kafka partitions, checkpoint behavior, recovery, and data correctness under increased workloads.

---

## 6. Infrastructure, Containers and Engineering Operations

| Technology / practice | Context |
| --- | --- |
| **Linux / Ubuntu** | Local engineering and development, services, shell tools, Docker, and database environments. |
| **Docker / Docker Compose** | Local multi-service PoCs; earlier migration-framework development and testing; separated containers, persistent volumes, networks, configuration, and health checks. |
| **Git / GitHub / Azure Repos** | Source and configuration versioning in independent prototypes and professional projects. |
| **Azure DevOps pipelines** | Integration and deployment-test automation in the enterprise migration project. |
| **Flyway** | Migration-framework database, staging, and control-structure provisioning. |
| **JDBC** | Data access, data movement, connectors, and distributed Spark processing against relational sources/targets. |
| **Testing and reconciliation** | Source/target comparison, row and key checks, transformation verification, restart/recovery testing, and bounded performance measurements. |

**Documented local example:** the SeaTunnel CDC PoC was tested on Ubuntu Desktop 24.04 LTS with an AMD Ryzen 9 CPU, 64 GB RAM, an NVMe SSD, **Docker Engine 28.3.3**, and **Docker Compose 2.39.1**. The Docker versions were checked on 18 October 2026. According to the system operator, the host installation had not changed during the preceding months; individual earlier test reports did not include a Docker version capture.

---

## 7. Legacy Platforms & Technologies

These technologies belong to earlier phases of professional experience and are included to identify the source-system environments encountered in database engineering and modernization work. Their inclusion is **not** a claim of current hands-on work with every platform.

| Platform / technology | Historical context |
| --- | --- |
| **IBM mainframe** | Mainframe systems administration, production operation, and database engineering. |
| **IBM VM** | Mainframe operating-system administration during earlier professional work. |
| **IBM MVS/XA** | Mainframe operating-system administration during earlier professional work. |
| **IBM DB2 for mainframe** | Production database administration in mainframe environments. |
| **IBM AS/400** | Earlier platform exposure; specific project scope is not detailed here. |
| **IBM AIX** | Earlier Unix enterprise-platform experience; no current deployment claim. |
| **IBM Merva / SWIFT** | Maintenance and extension of banking messaging systems and related applications in earlier professional work. |
| **Legacy products and utilities** | REXX, PL/I, IBM CSP, JES, JCL, VTAM, CA Roscoe (ROSCOE), TSO/TSO/E, CA Top Secret, IBM VSAM, and mainframe system/data-processing utilities; historical development, batch operations, communications, security, and data access. |

The historical platform background informs source-system assessment, legacy data interpretation, SQL/database performance work, and the planning of transitions toward newer relational and cloud environments.

---

## 8. Analytics, Statistics and Machine Learning

Professional and academic foundations include a **Master of Mathematics in Mathematical Information Science and Statistics** and practical statistical/data-processing work.

| Area / tooling | Context |
| --- | --- |
| **Statistical modelling and numerical methods** | Academic background and practical modelling and analysis. |
| **Spark ML / MLlib** | Machine-learning and numerical-processing studies and practical exercises in the Spark/Scala environment. |
| **Breeze** | Scala numerical-computing and mathematical algorithm exercises. |
| **KNIME** | Exploratory analytics and modelling work. |
| **Python analytical and ML libraries** | Technical-study material and experimentation; specific libraries and executions require example-level attribution. |
| **TensorFlow, Keras, scikit-learn, Neo4j, GraphX** | Technologies appearing in the broader learning inventory; inclusion here does not assert production use or independent verification of every example. |

**Study topics** include regression, classification, dimensionality reduction, numerical optimization, clustering, graph algorithms, and related mathematical methods. Future learning documents may cite individual books, examples, adaptations, and independently executed experiments.

---

## 9. AI-Assisted Engineering and Current Explorations

| Tool / area | Context |
| --- | --- |
| **OpenAI Codex, Claude Code, GitHub Copilot** | AI-assisted analysis, code development, testing, documentation, and technical experiments. |
| **Model Context Protocol (MCP)** | Exploratory database and developer-tool integration prototypes. |
| **LangChain / LangGraph** | Learning and experimentation in progress; no production deployment is claimed. |

AI-assisted development tools are distinguished from the underlying engineering work, and generated output is subject to review and validation.

---

## 10. Selected Supporting Projects

| Project | Role of the technology stack | Status / scope |
| --- | --- | --- |
| **Enterprise DB2 → PostgreSQL migration framework** | IBM DB2, Scala, Spark SQL, Debezium, Kafka, Kafka Connect, PostgreSQL staging, AWS managed services, Azure DevOps | Professional architecture and hands-on Scala/Spark SQL development; local, integration, and cloud testing; no full production cutover claimed for this framework. |
| **Separate DB2 → Azure PostgreSQL migration** | IBM DB2, Azure PostgreSQL, SQL transformation, loading and data-integrity controls | Professional migration work including participation in production execution and cutover. |
| **[Apache SeaTunnel CDC PoC](../examples/seatunnel-cdc/README.md)** | MySQL, Apache SeaTunnel, Kafka, PostgreSQL, Docker, Bash, Java 11 connector build | Local functional, failure/recovery, and benchmark testing; cloud scaling is planned. |
| **LedgerLake** | Spark, Kafka, Debezium, PostgreSQL, Delta Lake, containerized components | Independent evolving data-engineering/lakehouse prototype; project-specific execution status is not asserted here. |
| **Mathematical and ML studies** | Scala, Breeze, Spark MLlib, Python and other libraries | Educational implementations and adaptations; provenance and execution evidence belong to each individual example. |

## Documentation boundaries

- **Versions:** exact version numbers are included where confirmed by project configuration or recorded host checks. Other entries intentionally omit version numbers.
- **Authorship:** participation in a project does not imply personal authorship of each component. In particular, the Java/Spring migration orchestration was developed by a teammate; the documented personal contributions included architecture, Scala tooling, and Spark SQL transformations.
- **Confidentiality:** proprietary code, client-identifying data, and private implementation details are not published in this portfolio.
- **Verification:** professional delivery, locally executed prototypes, studies, and planned cloud work are reported separately.

For questions or technical corrections, use the repository's [GitHub Issues](https://github.com/primoracconsulting/technical-portfolio/issues).

**Primorac Consulting** · [Website](https://primoracconsulting.com) · [Technical Portfolio](https://github.com/primoracconsulting/technical-portfolio)
