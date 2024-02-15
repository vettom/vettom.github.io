# SRE Principles SLI

## Ways to measure SLI

- Request logs
    Processing requst logs can identify result/success rate of complex requestes being processed by your servers. It is possible to add logic in the code to identify good transactions. 

    Often require lots of engineering effort to add custom codes. Also this metric cannot measure requsts that does not reach your system

- Application metrics
    Can capture measurement of individual requests and does not have measurement latency as the logs. 

- Front-end infra metrics (Loadbalancer)
    Often this is source of metrics with in your control and is closesest to user. All requests are logged and often easy to measure. 

    LB are stateless so there is no way of tracking sessions or complex transactions. 

- Synthetic Clients
    Able to replicate user journeys and validate transactios complete successfully. Synthetic is often replicate limited part of user experience. 

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
  Portion of valid requests served faster than a threshold. Identify the valid requests. Defining thresholds requirs indentifying how the measurement reflects user experience.

- Freshness
  Portion of valid data updated most recently than a threshold. Identify  which of the data qualifyas valid data, and when the timing of start and top of freshness time period. For batch it is an approximation of time since last successfuly processing. 

  - Correctness 
    Portion of valid data is producting correct output. To measure correctness, method of measuring correctness must be independent of system generating data. Common strategy is to perform *Golden Input* data that will produce preditcable outcome. 

- Coverage
   Portiion of valid data processed succesfully. Count valid request and validate how many of the were success. May even require to query sources to verify the number of recors for example. 

- Throughput
   Portion of time where data processing rate is faster than a threshold. This measures rates of events over time. Usually measured in processed bytes per second or minute. Often multiple threshold may be required. 


## Why limit numbr of SLI
* Not all metrics makes good SLIs
* More SLI result in highet cognitive load.
* More SLI can potentially provide conflicting metrics, increasing resolution time.

> Aggregate and reduce number of SLI metrics
