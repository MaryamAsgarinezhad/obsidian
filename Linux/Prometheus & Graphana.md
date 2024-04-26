Here's a basic overview of how Prometheus works:

1. **Instrumentation**: Instrumenting typically involves adding Prometheus client libraries to your codebase to expose metrics that Prometheus can scrape. These libraries provide functions and methods to create and expose metrics such as counters, gauges, and histograms.
    
2. **Scraping**: Prometheus scrapes metrics endpoints on these targets (e.g., applications, services, or servers) at regular intervals. The targets expose metrics in a format that Prometheus understands, typically over HTTP.
    
3. **Data Storage**: The scraped metrics are stored locally in a time-series database (TSDB). Prometheus uses a custom time-series database that is optimized for high availability and fast querying.
    
4. **Querying**: You can query the stored metrics using PromQL. PromQL allows you to aggregate, filter, and manipulate the metrics to create meaningful dashboards, graphs, and alerts.
    
5. **Alerting**: Prometheus has a built-in alerting mechanism that allows you to define alerting rules based on your metrics. If an alert condition is met, Prometheus can send notifications to various alerting systems (e.g., email, Slack, PagerDuty).
    
6. **Visualization**: While Prometheus itself does not provide visualization, it integrates with Grafana, a popular open-source visualization tool. Grafana can query Prometheus for metrics and create dashboards and graphs based on these metrics.
    

Overall, Prometheus provides a powerful and flexible monitoring solution for collecting, storing, querying, and alerting on metrics from your applications and infrastructure.