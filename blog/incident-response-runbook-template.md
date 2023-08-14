# Incident Response Runbook Template
_Last updated August 14, 2023_

This template gives an overview of what information should be available in a Runbook for a service in a distributed system to make it an effective reference for incident response.

Runbooks should be actionable, concise, and discoverable.
* **Actionable**: The Runbook should be specific enough about where to go and what to do that someone unfamiliar with the system could feasibly use it to mitigate an incident even while half asleep.
* **Concise**: Nobody wants to read 15 paragraphs trying to figure out what to do when the site is down. Just include what’s necessary.
* **Discoverable**: Alerts related to this service should include a link to this Runbook in them.

## Severity
Include a short (1-2 sentence) description of what this service does, the impact of it being down, and the priority of bringing it back online, specifically whether a 24/7 response is required.

Include instructions for how to escalate an issue with this service in case someone found this page when trying to diagnose an issue they’re observing with the service (eg. a link to the on-call rotation for paging the team responsible).

## Investigation Steps
This section is a step-by-step guide to diagnosing the failure mode and containing or mitigating the impact.
1. Check the service’s health check to determine whether the service is up and reachable by consumers.
    1. Link to the service’s publicly-facing URL that consumers use. This should include any CDNs or proxies that may exist.
        1. If the service is down, try the next health check.
    2. Link to the service's using a url that goes directly to the service, bypassing the CDN or any proxies.
        1. If the service is down, a reasonable first step might be to restart the service, as it should be harmless and might help.
2. Check the service’s load balancer metrics (request volume, response status codes, response time) for anomalous behavior.
    1. Link to a dashboard that shows request volume, response status codes, and response time as reported by the load balancer, not the service itself. Some types of failure mode may cause the container or server to fail to report metrics, or to report them incorrectly, so it is useful to have visibility into data reported by other hardware to build a reliable picture of what's happening in production. All metrics should be visualized over time, so it is easy to tell at a glance whether there has been a recent and abnormal change.
        1. Irregular changes in **throughput** could indicate issues with other services that consume the service or network issues making it so requests are no longer reaching the service.
            1. Either of these cases are unlikely to indicate an issue with the service, but continue investigating to verify. Check for other active incidents that could be impacting the service.
        2. Irregular changes in **response status codes** could indicate changes in the requests coming to the service (eg. bad requests/requests the service can’t handle), an issue with the service, or an issue with downstream services that the service is not able to gracefully degrade from.
            1. If the error rate extremely close to a particular fraction of the traffic (eg. 33% when the service runs 3 pods), it may indicate that there is a pod in a bad state. Try restarting the service to kill the bad pod and replace it with a new one.
        3. Irregular changes in **response time** could indicate a change to the traffic profile the service is receiving (eg. some endpoints or types of inputs may be significantly faster than others), could indicate slowness in services or components that the service depends on, or could indicate that the service is approaching or hitting a resource bottleneck of some type.
3. Check the service’s system level metrics (CPU, memory, container restarts) for anything anomalous.
    1. Link to a dashboard that shows CPU, memory, and container/server count. All metrics should be visualized over time, so it is easy to tell at a glance whether there has been a recent and abnormal change.
        1. Frequent changes to the **container or server count** may indicate that the the service is hitting terminal errors or failing to respond to its healthcheck (eg. running out of resources and locking up) and so is being replaced by new containers or servers frequently.
        2. **High CPU** or **high memory utilization** (greater than 85-90%) can cause the service to respond more slowly, with errors, or not respond at all.
            1. If the service is using more resources than it requested, it may be a good idea to scale the service up as a short-term fix.
4. Check the service’s application metrics for anything anomalous.
    1. Link to the service’s application metrics dashboard. All metrics should be visualized over time, so it is easy to tell at a glance whether there has been a recent and abnormal change.
        1. For optimal visibility into how the service is operating, this should include the following metrics in addition to any application/use-case specific metrics:
            1. Total log numbers of each level over time
            2. Top log types of each level (error, warn, info) over time
            3. Throughput by API endpoint over time
            4. Throughput by requestor over time
            5. Response time by API endpoint over time
            6. Throughput to each downstream service (including databases) over time
            7. Response time of each downstream service (including databases) over time
            8. Also, response times should be visualized with percentiles - eg. 50th, 80th, 95th, and 99th, not averages.
    2. Check the throughput and response time panels for anomalous activity. Anomalous activity or drastic changes in these panels can indicate changes or issues elsewhere in the distributed system that may be impacting the service.
    3. Check whether the numbers of each log level have changed recently, and the top error log messages to see if they help identify the cause of the issue.
5. Search the logs for examples of the service’s top log messages as they may contain the root cause of the issue.
    1. Include links to any pre-built log queries that may be useful.
6. Check for recent deployments to the service that may have introduced issues.
    1. Identify the time recent deployments were performed and compare the timing with any anomalous behavior on the service dashboards.
    2. If a recent deployment appears to have introduced some anomalous behavior, roll back to the version before that deployment.

## Mitigation Steps
This section contains instructions for action that can be taken to address issues with the service.

### Restart the service
Instructions to restart the service, so all pods are killed and replaced with new ones.
1. Run the job to restart the service: (link)
2. Check whether the service is back up using the health check: (link)

### Redeploy a previous version of the service
Instructions to deploy an old version of the service, in the event that a recent deployment caused an issue.

...

### Scale up the service
Instructions to scale up the service, either increasing the number of pods or requesting more memory or compute for the service.

...

### Clear the cache
Instructions to clear the cache for a given service.

...

## Service Architecture
This section should have a short description of the service architecture along with an architecture diagram, in particular including any CDNs, special networking (eg. proxies), databases, or off-box caches that may be unique to the service. The intention is to give the incident responder a way to quickly understand what components exist and are necessary for service operation.

## Helpful links
The following list should include pretty much any resource that could feasibly be useful during an incident.

### Service links
Links to the service’s health check and/or swagger page. It may also make sense to include a link to a sample “real” request that executes various downstream dependencies if the health check doesn’t exercise those.
* Consumer-facing URL (including any CDNs or proxies)
* Link directly to the service (bypassing CDNs or proxies)
* Link to the service’s staging environment

### Monitoring & operational links
Any and all dashboards that are helpful/relevant for monitoring the service. All dashboards should prefer to show data as time series, so responders can scan for things that changed when the incident began.
* Link to CDN metrics (if the service has a CDN)
* Link to load balancer metrics
* Link to system metrics
* Link to application metrics
* Link to application logs
* Link to on-call rotation and escalation policy

### Development links
This should include links relevant to development & deployment of the service so the incident responder can see what’s changed recently and could feasibly roll back a recent change or perform a hotfix.
* Link to repo
* Link to deployment dashboard
* Link to any supplemental architecture diagrams that may be useful
