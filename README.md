# Uptime-Check-for-Internal-Services-Using-Blackbox-exporter-and-Prometheus

I came across blackbox exporter while I was searching for a solution to check uptime for my internal services over http.

I have some services which exposes health check endpoint internally. I would like to have an observability for these services along with alerting.

I found blackbox exporter is very apt for the above use case as I can use prometheus for monitoring, grafana for visualisation and alertmanager for alerting.

Blackbox Exporter can probe endpoints over HTTP, HTTPS, DNS, TCP, and ICMP.

## Installation

Blackbox exporter can be installed using binary or docker. — https://github.com/prometheus/blackbox_exporter.

The default port is 9115 .

Visiting http://localhost:9115/probe?target=google.com&module=http_2xx will return metrics for a HTTP probe against google.com. The probe_success metric indicates if the probe succeeded. Adding a debug=true parameter will return debug information for that probe.

You can read more about the configuration here — https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md

## Prometheus Configuration

Prometheus Configuration would look like this —

```
scrape_configs:
  - job_name: 'blackbox'
    metrics_path: /probe
    params:
      module: [http_2xx]  # Look for a HTTP 200 response.
    static_configs:
      - targets:
        - http://prometheus.io    # Target to probe with http.
        - https://prometheus.io   # Target to probe with https.
        - http://example.com:8080 # Target to probe with http on port 8080.
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - target_label: __address__
        replacement: <black-box-url>:9115  
```

Here, in targets we we would give the endpoint that we would like to monitor. The module [http_2xx] will look for a HTTP 200 response.

## Conclusion

Blackbox exporter is a great tool to monitor your api. You can visualise these metrics through grafana and set up alerts as well.
Hope this helps! Happy monitoring!
