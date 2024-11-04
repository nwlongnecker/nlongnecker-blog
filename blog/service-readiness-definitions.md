# Service Readiness Definitions
_Last updated November 4, 2024_

I have in the past used service readiness definitions as a tool for communicating to the business the state of a service and making clear the risks of not investing in further hardening. Below is a sample set of definitions I've used for a past team.

## In development
* **Not for production use**
* The API may change without notice
* No guarantees about uptime, responsiveness, or load capacity - The service may go down or become unresponsive without warning

## Beta
* **Not recommended for production use**
  * Discuss use cases and exceptions with the owning team
* Known and unknown bugs are expected
* API changes are versioned
* Functionality, for the happy path, generally works as expected
* Bug fixes will not be prioritized as production issues

## Production Ready
* **Service is ready for consumption by non-critical production systems** - critical/essential systems should still use discretion and evaluate the maturity of the system before consuming it
  * Engage with owning team for any P1 service usage
* API changes are versioned
* The service has some guarantee about uptime - Outages are considered an incident for the owning team and addressed with appropriate priority
* The service has some guarantee about amount of load it can handle before degrading
* The service has some guarantee about the response time of the service
* The service has dashboards showing key application metrics
* The service has alerts

## Battle Tested
* **Service is ready for consumption by any production system**
* The service has operated in production, handling notable load, for at least one calendar year
* The service has a well-defined SLOs based on historical record including the following stats:
  * Uptime characteristics (eg. 99.9% uptime - up to 8.77 hours of downtime per year)
  * Load capacity & documentation indicating behavior when close to/over the limit (eg. Can handle up to 20,000 rpm before degrading, can handle up to 25,000 rpm with degraded response time before falling over)
  * Response time (eg. 50th percentile, 90th percentile, and 99th percentile response time)
* The service’s constrained resource is understood and documented and instances are right-sized
* The service has robust alerts that have been proven to detect issues with appropriate timeliness
* The service has experienced multiple outages (either organic or artificial) that have exercised the service’s alerts, Runbooks, and incident response procedures
