- [official docs](https://prometheus.io/docs/introduction/overview/)
  - When does it not fit? Prometheus values reliability. You can always view what statistics are available about your system, even under failure conditions. If you need 100% accuracy, such as for per-request billing, Prometheus is not a good choice as the collected data will likely not be detailed and complete enough. In such a case you would be best off using some other system to collect and analyze the data for billing, and Prometheus for the rest of your monitoring.

- [setup machine monitoring](https://www.youtube.com/watch?v=WUkNnY65htQ)
   - how to setup node_exporter
   - shows prometheus server 
   - shows built in graphing
   - shows targets
   - built in console
