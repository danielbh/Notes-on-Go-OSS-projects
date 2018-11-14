Time series database with alerts...

https://github.com/prometheus/prometheus

20k stars

besides kuberenetes its considered a "graduated" cncf solution.

Difference between prometheus and influxDB: https://prometheus.io/docs/introduction/comparison/#prometheus-vs.-influxdb

Where InfluxDB is better:

- If you're doing event logging.
- Commercial option offers clustering for InfluxDB, which is also better for long term data storage.
- Eventually consistent view of data between replicas.

Where Prometheus is better:

- If you're primarily doing metrics.
- More powerful query language, alerting, and notification functionality.
- Higher availability and uptime for graphing and alerting.
