# Direct Edge MQTT → AWS IoT → Lambda → RDS (PostgreSQL)

End-to-end industrial IoT data ingestion architecture for securely storing edge telemetry in a relational database. Sensor data is published directly from edge devices (MQTT) → AWS IoT Core → Lambda → Amazon RDS (PostgreSQL).

This repo contains a detailed document for setup Direct Edge MQTT → AWS IoT → Lambda → RDS (PostgreSQL).

## Why this architecture
- Direct edge-to-cloud communication (MQTT over TLS + X.509)
- Secure device authentication and fine-grained policies
- Serverless ingestion & validation (AWS Lambda)
- Reliable relational storage for analytics and reporting (PostgreSQL)
- Scales to thousands–millions of devices

## Architecture overview
Edge Device (STM32 / ESP32 / RTU)
  • MQTT client publishes JSON payloads (mutual TLS / certs)

AWS IoT Core
  • Device auth, IoT Rules to route messages

AWS Lambda
  • Parse, validate, transform, insert into RDS

Amazon RDS (PostgreSQL)
  • Persistent relational storage for dashboards, analytics, and compliance

## Example payload
```json
{
  "device_id": "wellpad-01",
  "timestamp": "2026-01-14T10:32:45Z",
  "temperature": 72.4,
  "pressure": 1540.2,
  "flow_rate": 28.1
}
```

## Components & responsibilities
- edge-device/: firmware examples and MQTT publisher samples (esp32, stm32)
- lambda/: Lambda handler, dependencies, DB helper code
  - handler.py — main Lambda entry
  - requirements.txt — Python deps
  - db/ — DB helpers and SQL
- infrastructure/: IaC for IoT, RDS, IAM
- sql/: schema and migration scripts
- diagrams/: architecture diagrams

## Quickstart (high level)
1. Provision RDS PostgreSQL (private subnet recommended).
2. Store DB credentials in AWS Secrets Manager.
3. Deploy Lambda (with VPC access to RDS) and grant access to Secrets Manager.
4. Create AWS IoT Thing(s) and provision X.509 device certificates.
5. Create an IoT Rule that invokes the Lambda for relevant MQTT topics.
6. Publish test JSON payloads from an edge client to the topic.

## Security best practices
- Use mutual TLS with per-device X.509 certs.
- Restrict device permissions with fine-grained IoT policies.
- Store DB credentials securely in Secrets Manager; rotate regularly.
- Lambda runs with minimum required IAM permissions.
- Place RDS in private subnets and restrict inbound via security groups.

## Typical use cases
- Oil & Gas: wellhead telemetry, pipeline monitoring
- Energy & Utilities: substation telemetry, load monitoring
- Agriculture: soil, weather, irrigation sensors
- Industrial: predictive maintenance, equipment health

## Deployment notes
- Lambda must have network access (VPC routes + security groups) to connect to RDS.
- Keep payload validation in Lambda to avoid bad rows in DB.
- Consider batching or a buffering layer if message volume spikes.
- For large-scale time-series workloads, evaluate TimescaleDB or partitioning strategies.

## Contributing
PRs welcome. Follow repo style and include tests for logic changes.

## License
MIT License — free to use, modify, and adapt.

## Contact / Services
Firmware • Cloud • Software • IoT Architecture
👉 https://sonysunny.com
👉 https://sonysunny.com/#services