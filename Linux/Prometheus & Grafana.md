Here's a basic overview of how Prometheus works:

1. **Instrumentation**: Instrumenting typically involves adding Prometheus client libraries to your codebase to expose metrics that Prometheus can scrape. These libraries provide functions and methods to create and expose metrics such as counters, gauges, and histograms.
    
2. **Scraping**: Prometheus scrapes metrics endpoints on these targets (e.g., applications, services, or servers) at regular intervals. The targets expose metrics in a format that Prometheus understands, typically over HTTP.
    
3. **Data Storage**: The scraped metrics are stored locally in a time-series database (TSDB). Prometheus uses a custom time-series database that is optimized for high availability and fast querying.
    
4. **Querying**: You can query the stored metrics using PromQL. PromQL allows you to aggregate, filter, and manipulate the metrics to create meaningful dashboards, graphs, and alerts.
    
5. **Alerting**: Prometheus has a built-in alerting mechanism that allows you to define alerting rules based on your metrics. If an alert condition is met, Prometheus can send notifications to various alerting systems (e.g., email, Slack, PagerDuty).
    
6. **Visualization**: While Prometheus itself does not provide visualization, it integrates with Grafana, a popular open-source visualization tool. Grafana can query Prometheus for metrics and create dashboards and graphs based on these metrics.
    

Overall, Prometheus provides a powerful and flexible monitoring solution for collecting, storing, querying, and alerting on metrics from your applications and infrastructure.

- you can use the **prometheus-fastapi-instrumentator** library, which provides middleware for instrumenting your FastAPI application with Prometheus metrics.
  
```python
  Instrumentator().instrument(app).expose(app)
```

-----------------------------------------------

Setting Alert: 

![[Pasted image 20240430162811.png]]

Call or messege in case of Alert:
![[Pasted image 20240430162905.png]]
-----------------------------
![[Pasted image 20240504134319.png]]
to create graphana, this should be true  
then push to sre-ops
-----------------------
PromQL (Prometheus Query Language) expression is used to query and aggregate time series data from Prometheus metrics.

sum: sums the metric on different VMs that are running the application and receive the metrics

rate: units by the time

-------------------------------------------

![[Pasted image 20240504134505.png]]
when executed with no promql command, the metric variables will be shown.

-------------------------
![[Pasted image 20240504140315.png]]