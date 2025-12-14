# Real-Time Transport Analytics

End-to-end streaming pipeline that processes live vehicle delay events
and computes near real-time delay metrics using Kafka, Spark Structured Streaming,
and Delta Lake.

## Tech Stack
- Apache Kafka
- Apache Spark (Structured Streaming)
- Delta Lake
- Docker & Docker Compose
- Grafana

## Architecture (High Level)
Kafka → Spark → Delta Lake (Bronze / Silver / Gold) → Grafana → Alerts

