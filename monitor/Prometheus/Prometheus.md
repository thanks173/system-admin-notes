# Prometheus

## Overview

* Open-source systems monitoring and alerting toolkit
* Originally built at SoundCloud, now a standalone project
* Written in Go

## Feature

### Pros

* Build-in time series database, with good performance (compare to InfluxDB)
* Built-in scraping, storing, querying, graphing, and alerting based on time series data
* Flexible query language, easier to understand (than SQL)
* Collect data via a pull model over HTTP, data pushing is also supported (allow centralized control of how polling is done)
* Targets can be discovered via service discovery
* Configuration is done through YAML files

### Cons

* No plan in clustering or replicating, offering federation model instead (identical Prometheus servers separate machines)
* No plan in security implement
* Prometheus's local storage is not meant as durable long-term storage

## Visualization

* Build in expression browser, with visualization in a table or graphed over time
* Recommended to use Grafana

## Storage

* Gathered samples are grouped into blocks of two hours
* Uses about 1-2 bytes per sample
* Allow integrating with remote long-term storage systems

## Security

* Prometheus and its components do not provide any server-side authentication, authorisation or encryption, no future plan about any of these
* It is presumed that untrusted users have access to the Prometheus HTTP endpoint and logs. They have access to all time series information contained in the database, plus a variety of operational/debugging information
* Still support both auth and SSL on the scraping side

## Alert