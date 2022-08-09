---
layout: post
title:  "Prometheus - Useful promQL queries"
date:   2022-08-08 09:28:49 +0700
categories: prometheus
---


## Useful Recording Queries 
 - Recording queries are run at intervals and the resulting variable can then be used in other queries. 

### Inbound & Outbound Traffic % on interfaces:
```
# Outbound
- record: core:rate:percentage:outbound
    expr: rate(ifHCOutOctets{ifAlias=~'Core-.*', ifDescr=~".*/.*"}[10m])*8/1000/1000 / ifHighSpeed{ifAlias=~'Core-.*', ifDescr=~".*/.*"}

# Inbound
- record: core:rate:percentage:inbound
    expr: rate(ifHCInOctets{ifAlias=~'Core-.*', ifDescr=~".*/.*"}[10m])*8/1000/1000 / ifHighSpeed{ifAlias=~'Core-.*', ifDescr=~".*/.*"}
```

### Top 20 interfaces with highest % outbound traffic, past 60 minutes:
```
- record: core:rate20:top20
    expr: topk(20, max_over_time(core:rate:percentage:outbound[60m]))
```

### Calculates outbound 95th over the past 30 days on interfaces:
```
- record: core:95th:30d
    expr: quantile_over_time(0.95, rate(ifHCOutOctets{ifAlias=~'Core-.*', ifDescr=~".*/.*"}[5m])[30d:5m])*8/1000/1000 / ifHighSpeed{ifAlias=~'Core-.*', ifDescr=~".*/.*"}
```