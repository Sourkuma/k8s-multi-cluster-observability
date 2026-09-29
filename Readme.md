# Multi-Cluster Kubernetes Observability Stack

An enterprise-grade, centralized observability architecture for multi-cluster Kubernetes environments. This project utilizes the **kube-prometheus-stack** and **Loki** to aggregate metrics and logs from multiple worker clusters into a single primary observation cluster.

## 🏗️ Architecture Overview

The architecture follows a hub-and-spoke model. Secondary (worker) clusters collect local metrics and logs and forward them securely over the internet to the primary cluster using **Prometheus Remote Write** and **Promtail Remote Push**.

```mermaid
graph TD
    subgraph Secondary Cluster ["Secondary Cluster (e.g., python_production)"]
        PromSecondary["Prometheus"]
        PromtailSecondary["Promtail"]
    end

    subgraph Primary Cluster ["Primary Cluster (Central Observability)"]
        PromPrimary["Prometheus Primary"]
        LokiGateway["Loki (SimpleScalable)"]
        Grafana["Grafana Dashboard & Alerting"]
    end

    CloudStorage[("Azure Blob Storage")]

    PromSecondary -- "Remote Write (Auth)" --> PromPrimary
    PromtailSecondary -- "Push Logs" --> LokiGateway
    
    Grafana -- "Query Metrics" --> PromPrimary
    Grafana -- "Query Logs" --> LokiGateway
    
    LokiGateway -- "Long-term TSDB Storage" --> CloudStorage