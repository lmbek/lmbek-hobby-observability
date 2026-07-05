# Observability

Monitoring, logging, and alerting stack for the LMBEK Hobby Platform. Connects directly to services, bypassing the proxy.

## Quick Start

Make sure the orchestrators proxy stack is running first (creates the `orchestrators_hobby-net` network), then:

```bash
docker compose up -d
```

Stop everything:

```bash
docker compose down
```

## Services

| Service          | URL                      | Description                                      |
|------------------|--------------------------|--------------------------------------------------|
| Prometheus       | `http://localhost:9090`   | Metrics collection and storage                   |
| Grafana          | `http://localhost:3000`   | Dashboards (admin/admin)                         |
| Grafana Alloy    | `http://localhost:12345`  | Telemetry collector — Docker metrics and logs    |
| Loki             | `http://localhost:3100`   | Log aggregation                                  |

## Dashboards

Pre-provisioned Grafana dashboards:

- **Docker Container Monitoring** — CPU usage, memory usage (absolute and %), network RX/TX, disk I/O, running container count, and summary stats per container.
- **Container Logs** — Live stdout/stderr log stream from all containers, log volume by container, and filtered error log view.

## Structure

```
observability/
├── docker-compose.yml
├── prometheus/
│   └── prometheus.yml                          # Scrape targets
├── grafana/
│   ├── provisioning/
│   │   ├── datasources/
│   │   │   └── datasources.yml                 # Prometheus + Loki datasources
│   │   └── dashboards/
│   │       └── dashboards.yml                  # Dashboard provider config
│   └── dashboards/
│       ├── docker-monitoring.json              # CPU, RAM, network, disk I/O
│       └── container-logs.json                 # stdout/stderr log viewer
├── alloy/
│   └── config.alloy                            # Grafana Alloy pipeline config
└── README.md
```

## Architecture

- **Grafana Alloy** discovers Docker containers via the Docker socket, scrapes metrics and collects stdout/stderr logs.
- Metrics are forwarded to **Prometheus**, logs to **Loki**.
- **Grafana** queries both datasources and serves pre-provisioned dashboards.
- All observability services connect to the orchestrators' `hobby-net` network directly (bypassing the Traefik proxy).
