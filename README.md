# Nginx WAF + ELK Stack

Local lab environment with **Nginx + ModSecurity CRS** in front of **OWASP Juice Shop**, with WAF audit logs collected and parsed by the **ELK Stack**.

## Deploy scheme

![](/scheme.svg)

## Stack

- **Nginx WAF** — `owasp/modsecurity-crs:nginx`
- **Backend app** — OWASP Juice Shop
- **Logstash** — receives ModSecurity audit logs via Docker syslog driver
- **Elasticsearch** — stores parsed and normalized WAF events
- **Kibana** — provides search and visualization for WAF events

## Log flow

```text
Users → Nginx WAF → Juice Shop
              ↓
      ModSecurity audit logs
              ↓
      Docker syslog driver
              ↓
          Logstash
              ↓
        Elasticsearch
              ↓
           Kibana
```

## Installation

```bash
docker compose up -d logstash
docker compose up -d
```

## Services and ports

| Service | URL / Endpoint | Description |
|---|---|---|
| WAF / Juice Shop | http://localhost | Application entry point through Nginx WAF |
| Elasticsearch | http://localhost:9200 | Stores parsed ModSecurity WAF events |
| Kibana | http://localhost:5601 | Web UI for searching and visualizing events |
| Logstash syslog input | tcp://localhost:5514 | Receives WAF logs from Docker syslog driver |

## Notes

* ModSecurity audit logs are written to stdout
* Docker syslog logging driver forwards container logs to Logstash
* Logstash parses RFC5424 syslog messages and extracts ModSecurity JSON fields
* Elasticsearch stores WAF events in indices named `modsecurity-YYYY.MM.dd`
* Kibana uses the `modsecurity-*` data view to search and visualize events