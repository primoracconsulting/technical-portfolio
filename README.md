# Primorac Consulting

### Technical Experience & Engineering Portfolio

**Enterprise Data Engineering · Database Modernization · Distributed Systems · Applied Mathematics · Machine Learning**

Welcome to the technical portfolio of [Primorac Consulting](https://primoracconsulting.com), an independent technology consultancy led by Damir Primorac.

This repository documents professional engineering experience, selected development projects, technical studies, and practical experimentation with algorithms, programming languages, frameworks, and development environments.

The purpose is to demonstrate both **real-world engineering experience** and **continuing technical learning** through practical work.

---

## 1. Professional Background

Damir Primorac is a database migration architect and technology consultant with more than 35 years of experience across enterprise databases, software engineering, and information systems.

His professional background spans IBM mainframe environments, banking systems, pharmaceutical supply-chain applications, public-sector information systems, insurance platforms, and modern cloud data architectures.

He holds a **Master of Mathematics in Mathematical Information Science and Statistics** from the University of Zagreb. His technical interests connect software engineering, data processing, mathematical modelling, and statistical methods.

Core areas of professional experience include:

- Enterprise database architecture, migration, and modernization
- Data integration, Change Data Capture (CDC), and distributed processing
- Database performance analysis and SQL optimization
- Metadata-driven automation and data transformation
- System integration, testing, reconciliation, and technical leadership
- Applied mathematics, statistical modelling, and machine-learning experimentation

---

## 2. Enterprise Engineering Experience

### Large-Scale Database Migration

**IBM DB2 → PostgreSQL / Amazon Aurora**

Principal architect and technical lead of a metadata-driven migration framework developed for the modernization of an enterprise insurance Commission and Rewards System.

Project scope included:

- Eight DB2 source databases
- Approximately 1,400 inventoried source tables
- Approximately 400 selected migration tables
- Complex data-model and business-semantic transformations
- Integration of initial snapshots and ongoing CDC processing

The architecture combined **Debezium, Kafka, Kafka Connect, PostgreSQL staging, Apache Spark, Scala, and Spark SQL**.

Personal engineering contributions included architectural design, DB2 metadata analysis and extraction, connector-configuration generation utilities, migration automation, and Scala/Spark SQL transformation development.

Java/Spring orchestration components were developed by another member of the engineering team.

The framework underwent local, integration, and AWS cloud testing and was handed over to the client for production execution. **A full production-scale cutover outcome is not claimed.**

### Additional Database Modernization Experience

**IBM DB2 → Azure PostgreSQL**

Participation in a separate insurance-platform modernization programme, including migration scripts, data transformations, integrity controls, and direct participation in production migration and cutover activities.

### Database Performance and Enterprise Systems

Long-term experience with IBM DB2, PostgreSQL, Oracle, and related enterprise platforms, including SQL performance tuning, indexing, physical data modelling, database administration, and integration with business applications.

---

## 3. Engineering Projects and Technical Prototypes

This section presents selected projects, prototypes, and development experiments. Their documentation and validation status vary; detailed descriptions will distinguish verified outcomes from work in progress.

### Apache SeaTunnel CDC

Practical investigation of data pipelines using Apache SeaTunnel, MySQL, PostgreSQL, Kafka, and Docker Compose.

Topics include snapshot processing, Change Data Capture, streaming pipelines, connector configuration, checkpointing, and recovery behaviour.

The work includes independently configured demonstrations and an evolving CDC proof-of-concept project.

### LedgerLake

An evolving data engineering and lakehouse demonstration project exploring transactional data generation, data integration, distributed processing, and analytical data architectures.

Technologies and architectural components include Java, Spring Boot, PostgreSQL, Apache Spark, Delta Lake, and containerized development environments.

### Spark and Delta Lake — Medallion Architecture

Practical Java/Spark experimentation with Bronze, Silver, and Gold data-processing layers.

Topics include data validation, cleansing, deduplication, quarantine handling, transformations, and aggregated analytical outputs.

### Mathematical Models in Scala

Practical work adapting mathematical modelling examples from Python-oriented educational material into Scala.

Examples include Linear Discriminant Analysis (LDA) and Principal Component Analysis (PCA), using Scala, sbt, and Breeze.

The emphasis is on understanding mathematical algorithms, working with numerical libraries, adapting implementations, and exploring data generation, model training, and prediction workflows.

*Detailed project documentation, technical decisions, and implementation status will be provided in dedicated portfolio documents.*

---

## 4. Technical Learning and Practical Experiments

Continuous learning has been an important part of this engineering career.

Over the years, technical books, academic materials, open-source projects, and software documentation have provided starting points for installing, configuring, executing, testing, modifying, and adapting practical examples.

These activities have supported the development of knowledge of algorithms, numerical methods, programming languages, software libraries, and distributed computing environments.

### Mathematics and Statistics

Topics represented in the technical studies include:

- Linear algebra and matrix operations
- Probability and statistical distributions
- Statistical inference and regression
- Numerical optimization and gradient descent
- Dimensionality reduction
- Mathematical modelling and computational methods

### Machine Learning

Algorithms and techniques represented in the study materials include:

- Linear and logistic regression
- Decision trees and random forests
- k-Nearest Neighbors and Naive Bayes
- Support Vector Machines
- K-Means and hierarchical clustering
- Principal Component Analysis
- Neural networks and deep learning
- Recommendation systems
- Reinforcement learning
- Model evaluation and feature engineering

### Graph Algorithms

Technical materials cover areas including:

- PageRank and graph centrality
- Shortest-path and graph traversal algorithms
- Community detection
- Graph-based recommendations
- Link prediction

### Distributed Data Processing

Practical study and experimentation with:

- Apache Spark and Spark SQL
- Spark MLlib and GraphX
- Apache Kafka and Kafka Connect
- Apache Flink and streaming SQL
- Debezium and Change Data Capture
- Distributed data transformation and processing pipelines

**Scope note:** The presence of an algorithm in these study materials does not imply original authorship or independently verified execution of every implementation. Individual catalogues will distinguish source examples, confirmed practical work, and personal adaptations.

### Books and References

The technical studies draw on books, published examples, open-source projects, and independent experimentation. A **consolidated Books & References catalogue** will be maintained in the *Learning & Experiments* section as the portfolio develops.

The algorithm catalogues will focus on concepts, languages, frameworks, execution environments, and the nature of practical work, rather than repeating a bibliographic citation for every algorithm.

---

## 5. Programming Languages, Frameworks and Environments

The following technologies represent professional experience, development activities, or practical studies at different levels of depth.

| Category | Technologies |
| --- | --- |
| Programming languages | Scala, Java, Python, SQL, FORTRAN |
| Enterprise databases | IBM DB2, PostgreSQL, Oracle, SQL Server |
| Distributed processing | Apache Spark, Spark SQL, Spark MLlib, Apache Flink |
| Data integration | Apache Kafka, Kafka Connect, Debezium, Apache SeaTunnel |
| Machine learning and numerical computing | scikit-learn, TensorFlow, Keras, Breeze, KNIME |
| Analytical and graph technologies | Delta Lake, Neo4j, GraphX |
| Development tools | IntelliJ IDEA, Jupyter, Maven, sbt, Git |
| Infrastructure | Linux, Docker, Docker Compose |
| Cloud platforms | AWS, Microsoft Azure |
| AI-assisted engineering | OpenAI Codex, Claude Code, GitHub Copilot |

Technology inclusion does **not** imply identical levels of expertise. Detailed project and experiment documentation will identify the context and type of usage.

---

## 6. Source Attribution and Authorship

**An important distinction is made between original engineering work and educational experimentation.**

This portfolio includes knowledge and practical experience gained by studying, installing, running, testing, modifying, and extending examples from technical books and open-source projects.

Original authors, contributors, and publishers retain credit for their respective work.

A consolidated bibliography will document the principal educational sources. Where individual code examples are eventually published, their documentation will additionally identify the relevant source, author, and licence, as appropriate.

Educational experiment descriptions aim to identify:

- The algorithm or technical concept studied
- The programming language and libraries used
- The development and execution environment
- The nature of the practical work performed
- Independently developed changes or extensions, where established

The objective is to document **acquired knowledge, technical understanding, and hands-on experience**, not to claim authorship of third-party educational source code.

Similarly, professional project descriptions distinguish personal engineering contributions from work carried out by other team members.

---

## 7. Selected Code Examples — Future Development

Selected self-contained code examples may be added later to illustrate technical concepts and engineering practices.

Potential areas include:

- Scala implementations or adaptations of mathematical and statistical algorithms
- Spark SQL and distributed data-processing techniques
- Database transformations and SQL optimization
- CDC pipeline configurations and data integration patterns
- Python-based analytical and machine-learning experiments

Examples will be reviewed for source attribution, licensing, confidentiality, and suitability for public distribution.

Proprietary client implementations, confidential business information, and copyrighted materials without appropriate redistribution rights will not be published.

---

## 8. Portfolio Organization

The portfolio is being developed around four complementary areas:

**Engineering Projects** — Professional architecture experience, independent development projects, technical prototypes, and testing approaches.

**Learning & Experiments** — Algorithms, mathematical techniques, educational studies, practical investigations, and a central Books & References catalogue.

**Technology Stack** — Programming languages, frameworks, databases, infrastructure, and development environments.

**Code Examples** — Selected illustrative implementations that may be published in future revisions.

Documentation and navigation links will be added incrementally as the corresponding pages are created and reviewed.

---

## 9. Contact

**Primorac Consulting**

Enterprise Database Migration · Data Modernization · Data Engineering

Website: [primoracconsulting.com](https://primoracconsulting.com)

LinkedIn: [Damir Primorac](https://www.linkedin.com/in/damirprimorac/)

---

*This portfolio is maintained as a record of professional engineering experience, practical technical studies, and continuous development. Project descriptions and implementation-status information are based on documented evidence and are updated as the work evolves.*
