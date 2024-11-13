# SRE Principles SLI

## Ways to measure SLI
![SRE](https://vettom-images.s3.eu-west-1.amazonaws.com/generic/sre.jpg){: style="height:100px;width:150px" align=right }
- Request logs
    Processing request logs can identify result/success rate of complex requests being processed by your servers. It is possible to add logic in the code to identify good transactions. 

    Often require lots of engineering effort to add custom codes. Also this metric cannot measure requests that does not reach your system

- Application metrics
    Can capture measurement of individual requests and does not have measurement latency as the logs. 

- Front-end infra metrics (Loadbalancer)
    Often this is source of metrics with in your control and is closest to user. All requests are logged and often easy to measure. 

    LB are stateless so there is no way of tracking sessions or complex transactions. 

- Synthetic Clients
    Able to replicate user journeys and validate transactions complete successfully. Synthetic is often replicate limited part of user experience. 

- Client-side Instrumentation
    Best way to measure actual user experience, however this can have latency issues, can impact mobile device battery life and user trust.


## SLI equation why use %
 > SLI = "Good Events / "Valid Events" 
 * SLI fall between 0-100% easy to understand
 * Consistent format, so SLO and other calculations can be written with same logic.

##  Request/Response SLI
- Availability
  Proportion of valid requests served successfully. Definition of success and valid requests can vary depending on systems. For a VM, it may be how many minutes the VM is available over ssh. 

- Latency
  Portion of valid requests served faster than a threshold. Identify the valid requests. Defining thresholds requires identifying how the measurement reflects user experience.

- Freshness
  Portion of valid data updated most recently than a threshold. Identify  which of the data qualifies valid data, and when the timing of start and top of freshness time period. For batch it is an approximation of time since last successfully processing. 

  - Correctness 
    Portion of valid data is producing correct output. To measure correctness, method of measuring correctness must be independent of system generating data. Common strategy is to perform *Golden Input* data that will produce predictable outcome. 

- Coverage
   Portion of valid data processed successfully. Count valid request and validate how many of the were success. May even require to query sources to verify the number of records for example. 

- Throughput
   Portion of time where data processing rate is faster than a threshold. This measures rates of events over time. Usually measured in processed bytes per second or minute. Often multiple threshold may be required. 


## Why limit number of SLI
* Not all metrics makes good SLIs
* More SLI result in highest cognitive load.
* More SLI can potentially provide conflicting metrics, increasing resolution time.

> Aggregate and reduce number of SLI metrics
