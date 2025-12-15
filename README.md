# Real-Time Transport Analytics

## Executive Summary
This system delivers near real-time visibility into transport delays by processing live vehicle and weather streams at scale. It is designed as a production-grade streaming analytics platform using a modern lakehouse architecture. The focus is on data reliability, low-latency insights, and operational readiness rather than experimentation or demo-only pipelines.

---

## Business Problem & Why It Matters
Urban transport systems generate continuous streams of GPS and delay events. On their own, these signals are noisy and hard to act on. When combined with real-time weather data, however, they become highly predictive of congestion and service degradation.

Operations teams need timely, trustworthy metrics such as average delay per route to:
- Detect systemic issues early
- Trigger alerts before SLAs are breached
- Support data-driven operational decisions

Batch analytics or ad-hoc dashboards are insufficient. This problem requires a streaming-first, fault-tolerant architecture with clear data contracts and quality guarantees.

---

## Architecture Overview
The system is built around a real-time lakehouse architecture, separating ingestion, processing, storage, and consumption concerns.

![Architecture Diagram](![Architecture diagram](https://github.com/user-attachments/assets/02e09594-e4f7-4811-b3d1-197328e5f5c0)
)

Key characteristics:
- Event-driven ingestion via Kafka
- Stateful stream processing with Spark Structured Streaming
- Medallion (Bronze/Silver/Gold) data modeling using Delta Lake
- Near real-time analytics and alerting via Grafana

The platform is designed to run in the EU-Central-1 (Frankfurt) region to align with GDPR and data residency requirements.

---

## Data Flow: Bronze → Silver → Gold

### 1. Ingestion & Bronze Layer
- Vehicle GPS/delay events and weather events are ingested as independent Kafka topics.
- Raw events are written to the Bronze layer as append-only Delta tables.
- Data is stored in its original schema (JSON) to preserve full fidelity and support reprocessing.

**Purpose:** Immutable system of record for all incoming events.

---

### 2. Processing & Silver Layer
- Spark Structured Streaming consumes both topics.
- A stream–stream join enriches vehicle events with weather context.
- Data quality rules are applied:
  - Schema validation
  - Null and range checks
  - Timestamp consistency
- Invalid or late events can be isolated without breaking downstream consumers.

**Purpose:** Trusted, query-ready datasets with enforced data contracts.

---

### 3. Aggregation & Gold Layer
- Windowed aggregations (5-minute sliding windows) compute KPIs such as average delay per route.
- Results are written to Gold tables optimized for analytics and dashboards.

**Purpose:** Business-level metrics with low latency and high reliability.

---

## Key Technical Decisions

### Kafka for Ingestion
Kafka provides durable, scalable event ingestion and decouples data producers from downstream processing. Separate topics per domain allow independent scaling and schema evolution.

### Spark Structured Streaming
Chosen for its strong support for:
- Stateful streaming
- Event-time processing and windowing
- Exactly-once semantics with Delta Lake

This mirrors patterns used in production-grade data platforms.

### Delta Lake Lakehouse
Delta Lake enables:
- ACID guarantees on streaming data
- Schema evolution without pipeline breakage
- Time travel and reprocessing capabilities

This significantly reduces operational risk compared to raw file-based storage.

### Medallion Architecture
The Bronze/Silver/Gold model enforces clear ownership boundaries, simplifies debugging, and aligns with how real-world data platforms are operated at scale.

---

## What Makes This Project Stand Out
- End-to-end streaming architecture, not isolated components
- Real-time joins and windowed aggregations, not trivial transformations
- Explicit handling of data quality, schema evolution, and operational concerns
- Designed as an internal analytics system, not a tutorial pipeline

The focus is on correctness, maintainability, and production-readiness.

---

## Running the System Locally (High-Level)
- Docker Compose is used to orchestrate Kafka, Spark, Delta Lake storage, and Grafana.
- Kafka topics are created at startup.
- Spark streaming jobs consume events and write to Delta tables.
- Grafana connects to Gold tables to visualize metrics and trigger alerts.

Detailed commands are intentionally omitted to keep the focus on system design rather than setup mechanics.

---

## Key Learnings
- Streaming systems require explicit design around time, state, and data correctness.
- Data quality enforcement is a first-class concern, not an afterthought.
- Lakehouse architectures significantly simplify streaming analytics when compared to traditional Lambda setups.
- Production data engineering is as much about operational safety as it is about code.

---
