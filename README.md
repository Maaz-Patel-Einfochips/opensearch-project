# OpenSearch Observability Stack

This repository provides everything needed to deploy a complete **OpenSearch Observability Stack** on Kubernetes, including OpenSearch, OpenSearch Dashboards, Filebeat, and Logstash.


## What is OpenSearch?

[OpenSearch](https://opensearch.org/) is an open-source search, analytics, and observability suite. It provides capabilities such as full-text search, log analytics, metrics monitoring, and powerful dashboarding — all with open-source licensing and vendor neutrality.

**Key Features:**

- Real-time log and metrics ingestion and querying
- Dashboard UI for analysis and alerting
- Scalable and secure with RBAC and TLS
- Plugin support and open APIs

## Repository Structure

```text
.
├── charts/                      # Helm charts (for reference only)
│   ├── data-prepper/            # Helm chart for Data Prepper
│   ├── opensearch/              # Helm chart for OpenSearch
│   ├── opensearch-dashboards/  # Helm chart for Dashboards
│   └── README.md
├── opensearch/                 # Installation guide for OpenSearch
├── opensearch-dashboards/     # Installation guide for Dashboards
└── README.md                   # This file
```

**Note:** The `charts/` directory contains Helm charts for reference only.

## How to Install
To install the OpenSearch Stack, follow the instructions provided in the respective directories:

<a href="./opensearch/README.md">`opensearch/README.md`</a>– For installing OpenSearch Cluster

<a href="./opensearch-dashboards/README.md">`opensearch-dashboards/README.md`</a> – For installing OpenSearch Dashboards

Each of these guides provides:

- Pre-configured values
- Helm commands
- Required prerequisites
- Configuration examples

## Components Overview

| Component       | Purpose                                         |
|-----------------|-------------------------------------------------|
| **OpenSearch**  | Backend engine for logs, metrics, and search    |
| **Dashboards**  | UI for data visualization and alerting          |
| **Data Prepper**| Ingest and transform logs                       |
| **Filebeat**    | Ship logs from Kubernetes nodes                 |
| **Logstash**    | Optional log processor for advanced use cases   |
