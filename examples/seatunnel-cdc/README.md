# Apache SeaTunnel CDC — Architecture, Testing and Planned Transformations

> **Technical example · Primorac Consulting Technical Experience & Engineering Portfolio**  
> **Status:** Cases 1 and 2 have documented local test results; Case 3 is a design proposal, **not implemented or tested**.  
> **Evidence baseline:** Internal project documentation and test results verified through **3 October 2026**. No fresh runtime execution is implied by this portfolio document.

## Overview

This proof of concept (PoC) evaluates two [Apache SeaTunnel](https://seatunnel.apache.org/) configurations for replicating MySQL changes to PostgreSQL: direct CDC and CDC through Kafka. The documented local tests cover initial snapshot, INSERT/UPDATE/DELETE propagation, data consistency, restart and recovery, replay, connector configuration and selected performance measurements.

A proposed **Case 3** would extend the exercise from table replication to maintaining a single derived row for each parent record in a three-table source model. The example data model and implementation approach are **still under design consideration**. Case 3 has not been implemented or tested.

This document describes an engineering PoC conducted on a local workstation. It is **not** a runnable distribution of the private source repository. The excerpts are taken from reviewed configuration and test files, with any abridgement indicated. The complete scripts, credentials and generated runtime artifacts are not published here.

### At a glance

| Case | Architecture | Purpose | Status |
| --- | --- | --- | --- |
| **1 — Direct CDC** | MySQL → SeaTunnel → PostgreSQL | Basic snapshot, ongoing CDC, fidelity and recovery | **Tested** |
| **2 — Kafka CDC** | MySQL → SeaTunnel → Kafka → SeaTunnel → PostgreSQL | Durable event layer, replay, independent consumers, recovery and benchmarks | **Tested** |
| **3 — Multi-table consolidation** | `invoice` + `invoice_line` + `payment` → one parent-keyed target | Evaluate incremental consolidation of related CDC tables | **Planned; design under consideration** |

---

## 1. Architectures

### Case 1 — Direct change data capture

```text
MySQL 8.4
    |
    | initial snapshot + row-level CDC
    v
Apache SeaTunnel 2.3.12
    |
    v
PostgreSQL 18.1
```

The SeaTunnel MySQL CDC source reads an initial snapshot and subsequent changes from MySQL. A JDBC sink maintains the PostgreSQL target.

This configuration has no intermediate message broker. Tests cover row-level data consistency, INSERT/UPDATE/DELETE replication and recovery from persisted checkpoint state.

A key limitation is the absence of an independent, retained Kafka event log for rebuilding another downstream consumer.

### Case 2 — Kafka-mediated change data capture

```text
MySQL 8.4
    |
    | snapshot + CDC
    v
SeaTunnel 2.3.12 — Job 1 (MySQL CDC -> Kafka)
    |
    v
Apache Kafka 3.8.1 (KRaft)
    |
    v
SeaTunnel 2.3.12 — Job 08 (Kafka -> SQL transform -> JDBC)
    |
    v
PostgreSQL 18.1
```

Kafka introduces a retained event layer. Downstream consumer groups can independently read events, and retained history can be replayed to rebuild a destination. The pipeline can also report Kafka offsets and consumer lag.

**Important boundary:** Kafka retention limits replay. The PoC uses a seven-day Kafka retention setting; replay is not possible for records that have expired. The active sink is **Job 08**; older Job 02 and diagnostic Jobs 03–07 are not the current baseline.

The existing Job 08 already performs a *small SQL transformation*: it normalizes variable-length fractional seconds in MySQL `TIMESTAMP(6)` values represented in Debezium JSON. This is different from the multi-table, stateful CDC transformations proposed for Case 3.

---

## 2. Technology stack and environment

Versions below describe the reviewed `develop` branch of the private PoC, not an assertion about the newest available releases.

| Component | Version / implementation | Role |
| --- | --- | --- |
| MySQL | **8.4** | Source database; row-based binary logging and GTID-enabled CDC |
| Apache SeaTunnel | **2.3.12** | CDC sources, Kafka source/sink, SQL transformation and JDBC sink; Zeta engine |
| Apache Kafka | **3.8.1**, single-node **KRaft** | Event broker for Case 2 |
| PostgreSQL | **18.1** | Target database |
| Docker Engine | **28.3.3** | Local container runtime; version rechecked on 18 October 2026 |
| Docker Compose | **2.39.1** | Case-specific service orchestration; version rechecked on 18 October 2026 |
| Java — connector build | **Eclipse Temurin JDK 11** in `maven:3.9.9-eclipse-temurin-11` | Compile patched Case 2 Kafka connector and run its targeted Java test |
| Java — SeaTunnel container | Existing Java installation, or fallback **OpenJDK 11 JRE** installed by the startup script | Run the SeaTunnel Zeta engine; actual runtime `java -version` was not independently archived |
| MySQL JDBC driver | **8.4.0** | JDBC dependency |
| PostgreSQL JDBC driver | **42.7.4** | JDBC dependency |
| Shell, Python, SQL | Bash-based automation, Python benchmark generator, SQL checks | Test orchestration and data validation |

### Local test host

The documented Case 1 and Case 2 functional and benchmark runs were carried out in a **local Docker Compose environment**, not on a cloud cluster.

The local test environment used **Ubuntu Desktop 24.04 LTS**, an **AMD Ryzen 9** CPU, **64 GB RAM**, an **NVMe SSD**, **Docker Engine 28.3.3** and **Docker Compose 2.39.1**. The Docker versions were confirmed on the same host on **18 October 2026** using `docker version` and `docker compose version`. According to the system operator, the Docker installation and host setup had not changed during the preceding months; these versions are therefore recorded as the versions **used for the documented local tests**. The individual historical test reports did not capture Docker version output at execution time. These are host-level specifications; the test reports do not contain complete, per-run records of container resource allocation or utilization.

The Case 2 benchmark results below therefore describe one local PoC setup and must not be extrapolated into cloud capacity or production SLAs.

### Next planned test phase — Cloud evaluation and scaling

**The next planned testing phase is to run Cases 1 and 2 on a cloud platform** and evaluate their behavior under larger and varied workloads. The cloud provider, deployment topology and test dataset sizes are **not yet selected**.

The planned measurements are:

- **Throughput:** source change-generation rate, ingestion rate and end-to-end convergence at increasing workloads.
- **Kafka lag:** backlog accumulation and catch-up behavior under different load levels.
- **CPU and memory:** resource utilization of SeaTunnel, Kafka, MySQL and PostgreSQL.
- **Kafka partitions and parallelism:** the effects of different partition counts and processing capacity, subject to the connector's and workload's actual scaling constraints.
- **Checkpoints and recovery:** checkpoint duration and stability, restart/resume behavior and convergence after interruptions.
- **Data correctness:** complete source/target comparison at each tested workload.

Results will be recorded separately from the existing local test baseline, including cloud instance types, resource allocations, configuration, workload definitions and measured values.

**Status: Planned; no cloud deployment, execution, scalability measurements or performance conclusions are reported in this document.**

### Runtime isolation

The two cases are deliberately kept in separate Compose projects, with separate ports, networks, credentials, volumes, jobs and checkpoint histories.

| Runtime resource | Case 1 | Case 2 |
| --- | --- | --- |
| Compose project | `seatunnel-cdc-poc` | `seatunnel-cdc-case02` |
| MySQL host port | `3307` | `3308` |
| PostgreSQL host port | `5434` | `5435` |
| Kafka host port | Not used | `19092` |
| Services | MySQL, PostgreSQL, SeaTunnel | MySQL, PostgreSQL, Kafka, Kafka initialization, SeaTunnel |

### Docker volumes and persistent state

Named volumes preserve the state that matters for restart and recovery:

| Volume role | Reason for persistence |
| --- | --- |
| `mysql_data` | Source rows and binary-log state |
| `postgres_data` | Destination rows and database state |
| `kafka_data` — Case 2 | Retained event log and Kafka state |
| `seatunnel_checkpoints` | Savepoints/checkpoints needed for supported recovery |
| `seatunnel_connectors` and `seatunnel_lib` | Installed connector and library artifacts |
| `seatunnel_logs` | SeaTunnel runtime logs |

Case 2 has explicit `seatunnel-cdc-case02_*` volume names; Case 1 uses its own Compose namespace. Configuration and job files are bind-mounted read-only where practical.

**Operational consideration:** an empty list of currently running jobs does **not** prove that persistent recovery state has disappeared. Resetting test data or removing Docker volumes is not a normal recovery procedure. The project's scripts therefore distinguish deliberate initialization from checkpoint-based resume.

---

## 3. Test strategy and documented outcomes

Tests were designed to check **data correctness**, not merely whether the containers or streaming jobs were running. The test suite uses deterministic source data, compares source and target values, records evidence, and distinguishes automatic recovery from operator-assisted checkpoint resume.

### S01–S08 scenario matrix

| ID | Test objective | Case 1 | Case 2 |
| --- | --- | --- | --- |
| **S01** | Initial snapshot, row count and value comparison | **PASS** | **PASS** |
| **S02** | Ongoing INSERT, UPDATE, DELETE, NULL and type fidelity | **PASS** | **PASS** |
| **S03** | PostgreSQL interruption and recovery | **Verified with limits** | **Verified with limits** |
| **S04** | Kafka replay and destination reconstruction | N/A | **PASS** |
| **S05** | Independent Kafka consumer groups | N/A | **PASS** |
| **S06** | Controlled/unplanned restart and checkpoint recovery | **PASS with supported resume** | **PASS with supported resume** |
| **S07** | Schema evolution | **PASS in isolated test** | **Not supported on current Kafka path** |
| **S08** | Isolated performance and final data integrity | **PASS** | **PASS** |

**Evidence qualification:** These are the historical, documented scenario results summarized in the private project's `docs/testing.md` and `docs/project-status.md`, with an evidence baseline through 3 October 2026. They are **not** new test executions of the environment at the time of publication. Static repository checks, build-time connector tests, and end-to-end runtime scenarios are distinct forms of evidence.

### Example A — Validate the initial snapshot (S01)

The actual Case 1 test waits for row-level convergence and fails if the snapshot is incomplete or values differ:

```bash
if ! wait_case01_convergence snapshot 120 "${ARTIFACT_DIR}/verification.log"; then
  record_row_evidence "${EVIDENCE_FILE}" FINAL
  fail "initial snapshot full row comparison failed"
fi
write_test_evidence "${EVIDENCE_FILE}" FULL_ROW_COMPARISON PASS
```

The checker compares **complete row values**, not only row counts. In the initial Case 1 fixture, the snapshot contract expects three rows.

*Source excerpt: `case-01-direct/tests/s01_initial_snapshot.sh`.*

### Example B — Validate CDC changes (S02)

The test generates deterministic INSERT, UPDATE, DELETE and NULL operations, then waits for full source/target convergence:

```bash
"${CASE01_DIR}/scripts/generate-cdc.sh" > "${ARTIFACT_DIR}/generator.log" 2>&1

if ! wait_case01_convergence compare 120 "${ARTIFACT_DIR}/verification.log"; then
  fail "continuous CDC full row comparison failed"
fi
```

It also explicitly checks semantic outcomes, including an updated status, deletion of a row, insertion of new rows, and preservation of a NULL value. The Case 1 CDC fixture expects four final rows.

*Source excerpt: `case-01-direct/tests/s02_continuous_cdc_dml.sh`.*

### Example C — Independent consumers (S05, Case 2)

The Case 2 test creates a separate Kafka consumer group. A subsequent read must advance the separate group's offset while the active sink consumer group remains unchanged:

```bash
(( second_current > first_current )) || fail "independent group offset did not advance"
[ "${active_before}" = "${active_after}" ] || fail "active group offsets changed"
```

The short excerpt above is **abridged for presentation**; the actual test also checks topic boundaries and source/target consistency. The test checks that reading through a separate consumer group does not change the primary sink group offsets.

*Based on `case-02-kafka/tests/s05_independent_consumers.sh`; comments and failure text shortened for readability.*

### Example D — Performance testing (S08, Case 2)

The benchmark runs on isolated Kafka topics, consumer groups, tables and SeaTunnel jobs, without resetting the normal Case 2 baseline.

| Workload | CDC updates | Requested rate | Measured generation rate | Final Kafka lag | Final integrity |
| --- | ---: | ---: | ---: | ---: | --- |
| Run 1 | 200 | 10 events/s | **10.037 events/s** | **0** | **PASS** |
| Run 2 | 2,000 | 100 events/s | **99.906 events/s** | **0** | **PASS** |

The final recorded run also reported `TIMESTAMP_PRECISION_0_6=PASS` and `RUN_FAILURES=0`.

The test calculates the **measured** event-generation rate rather than presenting the requested setting as an observed throughput result:

```bash
actual_rate="$(awk -v e="$((rate * seconds))" -v d="${gen_s}" \
  'BEGIN { if (d+0<=0) print 0; else printf "%.3f", e/d }')"
```

*Source excerpt: `case-02-kafka/tests/s08_performance_benchmark.sh`.*

**Interpretation:** These are measured event-generation rates and correctness results for a specific local PoC workload. They are **not** a generalized PostgreSQL sink capacity measurement, a latency SLA, or a production-scale benchmark.

### Failure and recovery findings (S03/S06)

- Short PostgreSQL interruptions could recover automatically in the tested conditions.
- Longer interruptions could lead to a failed SeaTunnel job; explicit resume from persisted checkpoint state restored processing in the documented tests.
- Restarting the SeaTunnel engine is **not** equivalent to automatically resuming every streaming job.
- Case 2 keeps separate checkpoint histories for Job 1 and Job 08.
- Therefore the supported conclusion is **checkpoint-based recoverability**, not guaranteed automatic recovery from every infrastructure failure.

### Schema evolution boundary (S07)

Case 1 demonstrated schema evolution in a special isolated direct-CDC configuration with schema changes enabled. The current Case 2 sink uses a static Kafka field contract and fixed SQL/JDBC mapping. General transparent schema evolution in Case 2 is **not implemented**.

---

## 4. Engineering issue: Kafka offset management patch

### The problem

Case 2 identified a configuration issue in the SeaTunnel **2.3.12 Kafka source connector**. When SeaTunnel is responsible for committing Kafka offsets on successful checkpoints, Kafka's independent auto-commit must not compete with that checkpoint-managed state.

The reviewed upstream-derived code constructed the consumer's `enable.auto.commit` value from `commit_on_checkpoint` without inverting the boolean. That created the wrong combination for checkpoint-based commits.

### Intended configuration

The active Kafka-to-PostgreSQL job uses:

```hocon
commit_on_checkpoint = true
kafka.config = {
  auto.offset.reset = "earliest"
  enable.auto.commit = "false"
}
```

### Correction and validation

The targeted connector modification makes Kafka auto-commit the **inverse** of SeaTunnel's checkpoint-commit setting:

```java
props.setProperty(
    ConsumerConfig.ENABLE_AUTO_COMMIT_CONFIG,
    String.valueOf(!kafkaSourceConfig.isCommitOnCheckpoint()));
```

The patch also extracts Kafka consumer property construction into a helper and adds a unit test for both boolean values of `commit_on_checkpoint`.

The custom Case 2 Docker build:

1. Downloads the **pinned SeaTunnel 2.3.12 source archive**.
2. Verifies the upstream source and local patch using **SHA-256** values from `kafka-fix.lock`.
3. Checks that the patch applies and compiles the Kafka connector.
4. Runs the targeted `KafkaSourceConfigTest`.
5. Stores the resulting JAR and its generated checksum in an image-owned artifact directory.
6. Verifies the connector artifact when it is installed into the runtime.

The relevant custom image is `seatunnel-cdc-case02:2.3.12-kafka-fix`. The build stage uses Maven 3.9.9 and a Java 11 Temurin environment. The generated JAR checksum is tied to the **actual successful build artifact**; the PoC does not claim byte-identical output across arbitrary build machines.

**Attribution:** This correction is a local patch to an Apache SeaTunnel connector, not a claim of authorship of the original SeaTunnel project. The full upstream-derived source and patch remain in the private engineering repository; only a small illustrative excerpt is presented here.

### Separate issue: MySQL timestamp precision

The Kafka-mediated pipeline also encountered Debezium JSON representations of `TIMESTAMP(6)` values in which trailing fractional zeroes could be omitted. A fixed six-digit timestamp parser could reject valid inputs such as `.1` or `.1234`.

The current Job 08 SQL transformation pads the visible fractional part to six digits before timestamp parsing. The documented regression test covered effective fractional precision from **0 through 6 digits**, and all seven cases passed.

This documents a timestamp-format mismatch between the source value, the event serialization and sink-side parsing.

---

## 5. Case 3 — Multi-table CDC consolidation (design under consideration)

**Status: Planned. The source model, target contract, implementation approach and test plan are still under consideration. No Case 3 pipeline has been implemented or tested.**

### Background and proposed scope

The technical requirement originates from a parent/child integration pattern used in an earlier enterprise context: a parent financial transaction (`fintran`) and related child records (`payment` and `payment_due`). The parent records remain in the resulting table; child information is incorporated into the corresponding parent result. The original business schema and data are **not** published here.

For a public demonstration, an **alternative generic invoice model** is being considered. This is **a candidate PoC design, not the agreed final Case 3 design**.

### Candidate example — Invoice consolidation

```text
invoice
  invoice_id (PK)
      |
      +-- invoice_line
      |     invoice_line_id (PK)
      |     invoice_id (FK)
      |
      +-- payment
            payment_id (PK)
            invoice_id (FK)

         CDC from all three tables
                    |
                    v
             invoice_summary
          one row per invoice_id
```

Under this proposed contract, every `invoice` row is represented in `invoice_summary`, including invoices with no associated lines or payments. Line-item and payment changes cause the corresponding derived invoice row to be recalculated or updated.

Possible output fields include `invoice_id`, `customer_id`, `invoice_total`, `amount_paid`, `outstanding_balance`, `line_count` and `last_changed_at`. These columns and their financial meanings are **illustrative only**. Handling of taxes, refunds, reversals, currency, cancellation and payment status must be specified before implementation.

The `payment` table above is a **synthetic PoC entity**, not a claim to reproduce an identically named table in a published sample database.

### Related published data model

[Microsoft WideWorldImporters](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases/wide-world-importers) includes documented entities such as `Sales.Invoices`, `Sales.InvoiceLines` and `Sales.CustomerTransactions`. The latter can reference an invoice through `InvoiceID`, which is optional in that model. These entities provide a publicly available **relational reference**, not an existing CDC solution for Case 3.

If the invoice example is selected, we would define our own compact **MySQL schema and synthetic records**, rather than assume the full SQL Server model or financial rules can be reused unchanged. See the [WideWorldImporters sample](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases/wide-world-importers) and the [InvoiceLines table definition](https://github.com/microsoft/sql-server-samples/blob/master/samples/databases/wide-world-importers/wwi-ssdt/wwi-ssdt/Sales/Tables/InvoiceLines.sql).

### Illustrative reference query — Not implemented or executed

The query below uses the **proposed synthetic** `invoice`, `invoice_line` and `payment` fields. Each one-to-many child table is aggregated separately to avoid multiplying amounts through a direct join.

```sql
SELECT i.invoice_id,
       COALESCE(l.invoice_total, 0) AS invoice_total,
       COALESCE(p.amount_paid, 0) AS amount_paid,
       COALESCE(l.invoice_total, 0)
         - COALESCE(p.amount_paid, 0) AS outstanding_balance
FROM invoice AS i
LEFT JOIN (
    SELECT invoice_id,
           SUM(quantity * unit_price) AS invoice_total
    FROM invoice_line
    GROUP BY invoice_id
) AS l ON l.invoice_id = i.invoice_id
LEFT JOIN (
    SELECT invoice_id,
           SUM(payment_amount) AS amount_paid
    FROM payment
    GROUP BY invoice_id
) AS p ON p.invoice_id = i.invoice_id;
```

This query represents a **candidate reference calculation**, not an implemented incremental CDC transform. It deliberately omits real-world accounting rules. For example, three line items and two payments would produce six rows under a naïve three-table join, risking inflated aggregate values.

### Candidate approaches to investigate

| Approach | Questions for design and testing |
| --- | --- |
| **A. CDC → PostgreSQL staging → incremental SQL** | How to identify affected parent keys, recompute the result and apply idempotent updates |
| **B. CDC → Kafka → stateful streaming processor** | How to maintain multi-table join state, handle deletes, event ordering and checkpoint-based recovery |
| **C. SeaTunnel-native transformation** | Whether SeaTunnel 2.3.12 provides the required stateful multi-source join and aggregation semantics; support must be verified, not assumed |

A processor such as Apache Flink SQL could be assessed under approach B, but no processor or approach has been selected.

### Proposed correctness tests

If this design proceeds, the tests should verify:

1. **Initial consolidation:** exactly one derived row for each parent, including parents without children.
2. **INSERT/UPDATE/DELETE:** changes to either child table update the appropriate derived parent row without duplicates.
3. **Multiple child rows:** totals and counts remain correct for one-to-many relationships; no join fan-out.
4. **Parent changes:** clear rules for parent deletion and permitted foreign-key reassignment.
5. **Ordering and transactions:** late or out-of-order events and changes within the same source transaction converge correctly.
6. **Restart and replay:** checkpoint recovery and, where Kafka is used, retained-event replay converge to the same reference result.
7. **Verification:** compare all derived row values against an independently calculated SQL reference result, not only job state or row counts.

**Design decision pending:** whether to proceed with the invoice example, a synthetic `fintran` / `payment` / `payment_due` model, or another small model with the same parent/child transformation requirements. Neither the exact schema nor the implementation technology is finalized.

---

## 6. Documented test scope and limitations

**Covered by the documented local test record:**

- Two operational CDC topologies using Apache SeaTunnel 2.3.12.
- Snapshot and continuous row-change processing into PostgreSQL.
- Full row-value checks and scenario-oriented automated verification.
- Kafka event replay, independent consumers and offset inspection in Case 2.
- Supported checkpoint-based recovery with documented operational limits.
- The Kafka connector modification, checksum-controlled build procedure and targeted unit-test definition.
- A timestamp serialization/parsing correction with documented regression coverage.

**Not claimed:**

- Production-grade high availability or unrestricted exactly-once end-to-end guarantees.
- Automatic job recovery under every failure condition.
- Transparent schema evolution in the current Kafka-mediated path.
- Replay beyond Kafka's configured retention.
- Production-scale throughput certification or cloud scalability results; cloud scaling tests are the **next planned testing phase**.
- A working or tested implementation of Case 3.
- Public availability of the complete private PoC source or historical runtime artifacts.

## 7. Source provenance, scope and future public code

The engineering PoC is maintained in the private `primoracconsulting/seatunnel-cdc-poc` repository, with current documentation on the `develop` branch. The test descriptions above come from its `docs/testing.md` and `docs/project-status.md`; reviewed code excerpts come from its Case 1 and Case 2 test scripts, Job 08 configuration, Docker build and Kafka connector patch.

This public README summarizes a local engineering PoC; it is not a runnable replacement for the private repository. Excerpts have been selected to illustrate technical decisions without disclosing local credentials, full runtime state or unnecessary internal implementation detail. Apache SeaTunnel and other third-party components retain their own authorship and licensing.

Future public examples may include a small self-contained dataset, SQL reference queries, demonstration configurations and selected reusable test code **after** their technical correctness, confidentiality and licence compatibility have been reviewed.

## 8. Questions and feedback

Technical questions, comments and suggestions are welcome through the [Technical Portfolio GitHub Issues](https://github.com/primoracconsulting/technical-portfolio/issues).

**Primorac Consulting** · [Website](https://primoracconsulting.com) · [Technical Portfolio](https://github.com/primoracconsulting/technical-portfolio)
