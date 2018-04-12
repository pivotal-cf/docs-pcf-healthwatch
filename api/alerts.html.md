---
title: Configuring PCF Healthwatch Alerts
owner: PCF Healthwatch
---

This topic describes how to use Pivotal Cloud Foundry (PCF) Healthwatch API to retrieve and configure alert configurations.

Currently, two main types of alert configurations are supported: Out-of-the-Box and Deployment-specific. Through this API, consumers can do the following:

- View current alert configurations
- Update Out-of-the-Box threshold values
- Create deployment-specific configurations based on existing Out-of-the-Box configurations

##<a id='prerequisites'></a>Prerequisites/Assumptions

The steps in this document assume that you can [generate bearer tokens for a UAA client](https://docs.cloudfoundry.org/uaa/uaa-user-management.html) with the `healthwatch.read` (`GET` only) and `healthwatch.admin` (both `GET` and `POST`) scopes.

##<a id='info'></a>Healthwatch API Status

Test the availability of the Healthwatch API by hitting the `/info` endpoint with a `GET` request:

```
curl -G healthwatch-api.SYSTEM-DOMAIN/info
```

The expected response is a `200`/`OK` with the message `"HAPI is happy"`.

##<a id='get'></a>View all alert configurations
### `GET /alert-configurations`

To view a list of alert configurations, send a `GET` request to the `/alert-configurations` endpoint:

```
curl -G healthwatch-api.SYSTEM-DOMAIN/v1/alert-configurations \
     -H "Authorization: Bearer MY_TOKEN"
```

This returns a JSON array of alert configurations:

```
[
    {
        "query": "origin == 'some_origin' and name == 'Some.Metric.Name'",
        "threshold": {
            "critical": 95,
            "warning": 85,
            "type": "UPPER"
        }
    },
    {
        "query": "origin == 'some_origin' and name == 'Another.Metric.Name'",
        "threshold": {
            "critical": 9,
            "warning": 28,
            "type": "LOWER"
        }
    },
    {
        "query": "origin == 'another_origin' and name == 'Some.Metric.Name'",
        "threshold": {
            "critical": 1,
            "type": "EQUALITY"
        }
    }
]
```

The `query` and `threshold` properties are covered in detail below.

##<a id='get_with_query'></a>View specific alert configurations
### `GET /alert-configurations?q=...`

To narrow the results of a `GET` request, add a `query` to the URL in a parameter named `q`:

```
curl -G "healthwatch-api.SYSTEM-DOMAIN/v1/alert-configurations"  \
    --data-urlencode "q=origin == 'some_origin' and name == 'Some.Metric.Name'" \
    -H "Authorization: Bearer MY_TOKEN"
```

This returns a JSON array of alert configurations, filtered against the provideded `query`:

```
[
    {
        "query": "origin == 'some_origin' and name == 'Some.Metric.Name'",
        "threshold": {
            "critical": 95,
            "warning": 85,
            "type": "UPPER"
        }
    }
]
```

##<a id='post'></a>Updating alert configurations

### `POST /alert-configurations`

To update an existing alert configuration, make a `POST` request to the `alert_configurations` endpoint with the updated data:

```
curl "healthwatch-api.SYSTEM-DOMAIN/v1/alert-configurations"  \
      --data "{\"query\":\"origin == 'some_origin' and name == 'Some.Metric.Name'\",\"threshold\":{\"critical\":90,\"warning\":80,\"type\":\"UPPER\"}}" \
      -H "Authorization: Bearer MY_TOKEN" \
      -H "Content-Type: application/json"
```

The updated alert configuration is echoed back in the response:

```
{
    "query": "origin == 'some_origin' and name == 'Some.Metric.Name'",
    "threshold": {
        "critical": 95,
        "warning": 85,
        "type": "UPPER"
    }
}
```

##<a id='queries'></a>Queries

The `query` field is used in two ways:

* `GET` requests: The `query` filters the alert configurations being queried.
* `POST` requests: The `query` specifies the alert configuration being updated.

In both cases, the `query` associated with an alert configuration denotes the trigger conditions for the alert.

A well-formed `query` is a valid [Spring Expression Language (SpEL)](https://docs.spring.io/spring/docs/4.3.14.RELEASE/spring-framework-reference/html/expressions.html) expression that uses only the equality, `"=="`, and conjunction, `"and"`, operators. For example:

```
"origin == 'some_origin' and name == 'Some.Metric.Name'"  # valid
"origin  > 'some_origin' and name == 'Some.Metric.Name'"  # invalid (uses '>')
"origin == 'some_origin'  or name == 'Some.Metric.Name'"  # invalid (uses 'or')
```

The following fields can be queried:

- `origin`&nbsp;&nbsp;&nbsp;*(required)*
- `name`&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;*(required)*
- `job`
- `deployment`

##<a id='thresholds'></a>Thresholds

The `threshold` field contains a threshold `type`, as well as `critical` and `warning` threshold values. The `type` can be `"UPPER"`, `"LOWER"`, or `"EQUALITY"`.

Alert configurations whose thresholds are of the `UPPER` type trigger their alerts when the actual metric value is *above* the `warning` value, and again when above the `critical` value.

Thresholds with the `LOWER` type work the same way, except the alerts trigger when the metric falls *below* the thresholds.

The `EQUALITY` alerts trigger when the metric value is *not exactly* equal to the `critical` threshold. These alerts do not have `warning` thresholds.

##<a id='defaults'></a>Supported Alerts

Healthwatch ships with the following configurable alerts:

| Description                                                    | Metric Name                                        | Origin                        | Job            | Threshold Type | Critical     | Warning    | Notes                                                                                                                              |
|----------------------------------------------------------------|----------------------------------------------------|-------------------------------|----------------|----------------|--------------|------------|------------------------------------------------------------------------------------------------------------------------------------|
| Performance Alert: Active Locks Held                           | ActiveLocks	                                      | locket	                      |                | EQUALITY       | 4            |            |                                                                                                                                    |
| Performance Alert: Active Presences Held                       | ActivePresences	                                  | locket	                      |                | UPPER          | 200          | 150        |                                                                                                                                    |
| Performance Alert: Auctioneer Time to Fetch Cell State         | AuctioneerFetchStatesDuration                      | auctioneer                    |                | UPPER          | 5000000000   | 2000000000 |                                                                                                                                    |                                  
| Performance Alert: App Instances Placement Failures Rate       | AuctioneerLRPAuctionsFailed                        | auctioneer                    |                | UPPER          | 1            | .5         |                                                                                                                                    |              
| Performance Alert: App Instance Starts Rate                    | AuctioneerLRPAuctionsStarted                       | auctioneer                    |                | UPPER          | 100          | 50         |                                                                                                                                    |                 
| Performance Alert: Task Placement Failures Rate                | AuctioneerTaskAuctionsFailed                       | auctioneer                    |                | UPPER          | 1            | .5         |                                                                                                                                    |               
| Performance Alert: Router Exhausted Connections                | backend_exhausted_conns                            | gorouter                      |                | UPPER          | 10           | 5          |                                                                                                                                    |        
| Performance Alert: Number of Router 502 Bad Gateways           | bad_gateways                                       | gorouter                      |                | UPPER          | 20           | 10         |                                                                                                                                    |  
| Performance Alert: BBS Time to Run LRP Convergence             | ConvergenceLRPDuration                             | bbs                           |                | UPPER          | 20000000000  | 10000000000|                                                                                                                                    |                     
| Performance Alert: Number of Crashed App Instances             | CrashedActualLRPs                                  | bbs                           |                | UPPER          | 20           | 10         |                                                                                                                                    |  
| Performance Alert: Cloud Controller and Diego in Sync          | Diego.AppsDomainSynced                             | bbs                           |                | EQUALITY       | 1            |            |                                                                                                                                    |         
| Scaling Alert: Number of Available Free Chunks of Cell Memory  | Diego.AvailableFreeChunks                          | healthwatch                   |                | LOWER          | 50           | 100        |                                                                                                                                    |
| Performance Alert: Rate of Change in Running App Instances     | Diego.LRPsAdded.1H                                 | healthwatch                   |                | UPPER          | 100          | 50         |                                                                                                                                    |        
| Scaling Alert: Remaining Cell Disk Available                   | Diego.TotalAvailableDiskCapacity.5M                | healthwatch                   |                | LOWER          | 3584         | 8192       |                                                                                                                                    |
| Scaling Diego: Remaining Cell Memory Available                 | Diego.TotalAvailableMemoryCapacity.5M              | healthwatch                   |                | LOWER          | 32768        | 65536      |                                                                                                                                    |
| Scaling Alert: Cell Container Capacity Available               | Diego.TotalPercentageAvailableContainerCapacity.5M | healthwatch                   |                | LOWER          | 0.35         | 0.4        |                                                                                                                                    |
| Scaling Alert: Cell Disk Available                             | Diego.TotalPercentageAvailableDiskCapacity.5M      | healthwatch                   |                | LOWER          | 0.35         | 0.4        |                                                                                                                                    |
| Scaling Alert: Cell Memory Available                           | Diego.TotalPercentageAvailableMemoryCapacity.5M    | healthwatch                   |                | LOWER          | 0.35         | 0.4        |                                                                                                                                    |
| Scaling Alert: Doppler Message Rate Capacity                   | Doppler.MessagesAverage.1M                         | healthwatch                   |                | UPPER          | 1000000      | 800000     |                                                                                                                                    |
| Performance Alert: Router File Descriptors                     | file_descriptors                                   | gorouter                      |                | UPPER          | 60000        | 50000      |                                                                                                                                    |        
| Scaling Alert: Log Transport Loss Rate                         | Firehose.LossRate.1M                               | healthwatch                   |                | UPPER          | 0.01         | 0.005      |                                                                                                                                    |
| Performance Alert: PAS MySQL Galera Cluster Size               | Galera.TotalPercentageHealthyNodes                 | healthwatch                   |                | LOWER          | .3332        | .9999      |                                                                                                                                    |                                
| Performance Alert: Router Handling Latency                     | latency                                            | gorouter                      |                | UPPER          | 150          | 100        |                                                                                                                                    |    
| Performance Alert: Locks Held by BBS                           | LockHeld                                           | bbs                           |                | EQUALITY       | 1            |            |                                                                                                                                    |
| Performance Alert: Locks Held by Auctioneer                    | LockHeld                                           | auctioneer                    |                | EQUALITY       | 1            |            |                                                                                                                                    |
| Performance Alert: More App Instances Than Expected            | LRPsExtra                                          | bbs                           |                | UPPER          | 10           | 5          |                                                                                                                                    | 
| Performance Alert: Fewer App Instances Than Expected           | LRPsMissing                                        | bbs                           |                | UPPER          | 10           | 5          |                                                                                                                                    |  
| Performance Alert: Time Since Last Route Register Received     | ms_since_last_registry_update                      | gorouter                      |                | UPPER          | 30000        | 30000      |                                                                                                                                    |                     
| Performance Alert: PAS MySQL Server Availability               | /mysql/available                                   | mysql                         | mysql,database | EQUALITY       | 1            |            |                                                                                                                                    |
| Performance Alert: PAS MySQL Galera Cluster Node Readiness     | /mysql/galera/wsrep_ready	                      | mysql	                      | mysql,database | LOWER          | 1            | 0.9999     |                                                                                                                                    |
| Performance Alert: Cell Rep Time to Sync                       | RepBulkSyncDuration                                | rep                           |                | UPPER          | 10000000000  | 5000000000 |                                                                                                                                    |                   
| Performance Alert: BBS Time to Handle Requests                 | RequestLatency                                     | bbs                           |                | UPPER          | 10000000000  | 5000000000 |                                                                                                                                    |                   
| Performance Alert: Number of Router 5XX Server Errors          | responses.5xx                                      | gorouter                      |                | UPPER          | 20           | 10         |                                                                                                                                    |  
| Performance Alert: Route Emitter Time to Sync                  | RouteEmitterSyncDuration                           | route_emitter                 |                | UPPER          | 10000000000  | 5000000000 |                                                                                                                                    |                                
| Scaling Alert: Syslog Adapter Capacity                         | SyslogDrain.Adapter.BindingsAverage.5M             | healthwatch                   |                | UPPER          | 500          | 450        |                                                                                                                                    |
| Scaling Alert: Syslog Adapter Loss Rate                        | SyslogDrain.Adapter.LossRate.1M                    | healthwatch                   |                | UPPER          | 0.1          | 0.01       |                                                                                                                                    |
| Scaling Alert: Reverse Log Proxy Loss Rate                     | SyslogDrain.RLP.LossRate.1M                        | healthwatch                   |                | UPPER          | 0.1          | 0.01       |                                                                                                                                    |
| Scaling Alert: Router Instance CPU                             | system.cpu.user                                    | bosh-system-metrics-forwarder | router         | UPPER          | 70           | 60         |                                                                                                                                    |
| Scaling Alert: UAA Instance CPU                                | system.cpu.user                                    | bosh-system-metrics-forwarder | uaa,control *  | UPPER          | 90           | 80         |                                                                                                                                    |
| Performance Alert: VM CPU                                      | system.cpu.user                                    | bosh-system-metrics-forwarder |                | UPPER          | 95           | 85         |                                                                                                                                    |
| Performance Alert: VM Ephemeral Disk Used                      | system.disk.ephemeral.percent                      | bosh-system-metrics-forwarder |                | UPPER          | 90           | 80         |                                                                                                                                    |
| Performance Alert: VM Persistent Disk Used                     | system.disk.persistent.percent                     | bosh-system-metrics-forwarder |                | UPPER          | 90           | 80         |                                                                                                                                    |
| Performance Alert: VM Disk Used                                | system.disk.system.percent                         | bosh-system-metrics-forwarder |                | UPPER          | 90           | 80         |                                                                                                                                    |
| Performance Alert: VM Health Check Recovery                    | system.healthy                                     | bosh-system-metrics-forwarder |                | EQUALITY       | 1            |            |                                                                                                                                    |
| Performance Alert: VM Memory Used                              | system.mem.percent                                 | bosh-system-metrics-forwarder |                | UPPER          | 90           | 80         |                                                                                                                                    |
| Performance Alert: Router Throughput                           | total_requests                                     | gorouter                      |                | UPPER          | 125000       | 100000     |                                                                                                                                    |          
| Performance Alert: Number of Router Routes Registered          | total_routes                                       | gorouter                      |                | UPPER          | 200          | 100        |                                                                                                                                    |    
| Performance Alert: Unhealthy Cells                             | UnhealthyCell                                      | rep                           |                | EQUALITY       | 0            |            | We are alerting by cell for this metric.  We will notify at a critical level when any Diego Cell has been unhealthy for 5 minutes. |
                      


You can learn more about metrics here: [PCF Healthwatch Metrics](../metrics.html).

**This handles two jobs to accommodate different job names between PAS and SRT*

##<a id='errors'></a>Errors

This section lists common error messages and their causes.

---
**Error Message**: "Unsupported condition in query expression"

**Possible Cause**: Key in query string is something other than `name`, `origin`, `job`, or `deployment`

---
**Error Message**: "Unsupported query expression"

**Possible Causes**: 

- Query string does not include AT LEAST `name` and `origin`
- Found operator other than `and` or `==`
- Expression in query string not in format of `property == 'value' and ...`

---
**Error Message**: "Invalid query expression"

**Possible Cause**: Invalid query string format

---
**Error Message**: "Invalid threshold type for metric 'some_origin.some_name'")

**Possible Cause**: Given threshold type does not match expected (see table above)

---
**Error Message**: "Must provide a warning threshold for an Upper/Lower Threshold"

**Possible Cause**: Threshold missing required values for type

---
**Error Message**: "name = '${alertConfiguration.name}' and origin = '${alertConfiguration.origin}' is not a supported alert configuration"

**Possible Cause**: Alert configuration does not exist for the targeted metric

##<a id='Example'></a>Walkthrough Example

A best practice deployment of Cloud Foundry includes at least three availability zones (AZs). For these types of deployments, Pivotal recommends that you have enough capacity to suffer failure of an entire AZ.

By default, Healthwatch sends an alert if the `Diego.TotalPercentageAvailableMemoryCapacity.5M` metric falls below 35%, or one in three.

However, if your environment has been scaled up to five AZs you may wish to adjust the alert configuration accordingly to 20%, or more in five.

```
uaac token client get <my_healthwatch_admin_client> -s <my_healthwatch_admin_secret>
export token=$(uaac context | grep access_token | awk '{print $2}')

curl "healthwatch-api.SYSTEM-DOMAIN/v1/alert-configurations"  \
      --data "{\"query\":\"origin == 'healthwatch' and name == 'Diego.TotalPercentageAvailableMemoryCapacity.5M'\",\"threshold\":{\"critical\":0.2,\"warning\":0.3,\"type\":\"LOWER\"}}" \
      -H "Authorization: Bearer ${token}" \
      -H "Content-Type: application/json"
```

The response body contains the updated alert configuration. You can confirm the change with a `GET` request:

```
curl -G "healthwatch-api.SYSTEM-DOMAIN/v1/alert-configurations"  \
    --data-urlencode "q=origin == 'healthwatch' and name == 'Diego.TotalPercentageAvailableMemoryCapacity.5M'" \
    -H "Authorization: Bearer ${token}"
```
