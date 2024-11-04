# Chaos Testing: A Path To Operational Excellence
_Last updated Septemner 23, 2024_

As we move towards a distributed software ecosystem, our incident response and SRE capabilities also need to become more distributed across the engineering org. A centralized SRE team will simply not be able to effectively manage all reliability engineering tasks and incidents across the number of new software components we will have. The engineering teams who manage those components will need to do some, most, or all of the reliability engineering and incident response to ensure their systems are production ready and maintain a healthy production environment.

## Production Readiness

Two key aspects of production readiness are **observability** and **incident response**. Both are required components in achieving a robust system.

### Observability

Observability is the ability to understand the state of a system or application by analyzing its outputs, logs, and performance metrics. It’s easy to create dashboards that provide information about the system, but understanding which graphs and visualizations are most useful during a production incident, whether a system’s dashboards have sufficient coverage of all edge cases, and which information to include in log messages is an iterative process that takes time to get right for each system. To get really good observability that we can be confident in, we need to use our observability tools on a regular basis and demonstrate that many different types of issues with the system are diagnosable with the observability tools we have in place.

### Incident Response

Incident response is the process we use to respond to production incidents - outages, behavior degradations, cyber attacks, etc. Incident response includes the detection of the incident - whether we were automatically alerted about it or if someone had to manually discover it - through mitigation and resolution of the incident.

Two key parts of incident response are **alerts** and **runbooks**. Alerts send push notifications to the developers on call in case something goes wrong, and runbooks are instructions for what to do upon receipt of those alerts. Similar to dashboards for observability, it’s easy to create some alerts and runbooks, but understanding which alerts are most useful, ensuring that alerts have coverage of all edge cases and fire if and only if there’s an issue, and ensuring that runbooks are consumable and provide the right information to diagnose and mitigate a variety of incidents is an iterative process that takes time and effort to get right. In addition to setting up alerts and runbooks, we need to regularly exercise them and validate that the alerts fire when appropriate and that runbooks remain up to date and that both have good coverage of edge cases.

## Validating Production Readiness

Once we have set up metrics, dashboards, logs, alerts, and runbooks for a system, we have made a lot of good progress towards production readiness, but we need some way to validate that those tools actually achieve the goals we need them to: allowing us to detect and diagnose production incidents. We can construct carefully controlled tests to validate parts of our production readiness, but achieving true confidence requires end-to-end testing of the system to identify any unexpected issues.

End-to-end testing to validate the efficacy of our metrics, dashboards, logs, alerts, and runbooks can be done in two ways: waiting for a real production incident, or artificially inducing one.

Artificially inducing a production incident? That sounds crazy, right? Why would we intentionally degrade our customer’s experience?

→ Yes, artificially inducing a production incident can cause customer impact and therefore has a chance of costing the company money, but it has a couple notable benefits:
* The incident is time-boxed. Because we caused the incident, we also know how to fix it. If we find that our observability is insufficient for us to diagnose the problem, we have learned something very valuable and can still fix the problem. If a similar incident had happened organically, we would be lacking the tools to diagnose the issue and therefore wouldn’t know what was wrong or how to fix it, and that could cause the issue to persist much longer than it otherwise needed to.
* The incident happens at a time of our choosing. We can guarantee that the incident happens at a low-traffic time to minimize external impact. Additionally, we can guarantee that the incident will happen at a convenient time so key people are available and able to learn optimally from the event.

By contrast, if we wait for a real production incident, we don’t have control over when it happens or how long it lasts, which has the potential to delay resolution and magnify the impact significantly.

Inducing production incidents in this way is an industry standard practice, similar to white-hat hacking in the cybersecurity industry, and is called **Chaos Testing** (also called **Chaos Engineering Game Days**).

## Chaos Testing

Chaos testing is, essentially, **pressure testing our incident response procedures** and **validating the production readiness of our systems**. When we chaos test, we intentionally break systems in production to validate that we are able to detect, identify, and mitigate those types of failures in a timely fashion. Chaos testing is one way to follow the [principles of chaos engineering](https://principlesofchaos.org/).

Chaos testing can start out as highly controlled game days, where everyone knows what is going to break, what alerts should fire, and what dashboards should show the impact while we familiarize ourselves with the practice, but ideally it would evolve into true game days, where one member of the team breaks something and the rest of the team receives an alert and tries to figure out what happened and resolve it as quickly as possible.

Once the game day has happened, it should be treated like any other production incident, meaning a blameless postmortem should be run to review the incident timeline, determine the time to detection and time to resolution, and review what went well about our response, what could be improved, where we got lucky, where we got unlucky, and identify any action items for things we want to improve to make future incidents smoother.

### Getting Started with Chaos Testing

Chaos testing is a way to incentivize a team to level up the observability of their systems. It helps to get the engineers thinking about ways that their systems might break and how they can ensure observability into those cases, and helps them gain some experience with addressing production incidents in a controlled way, so when real outages happen they’re already experienced in using the observability tools and can solve the issues efficiently.

As a prerequisite to running these types of game days, teams will need to have already invested in their service’s observability, runbooks, and alerts - they should have an expectation that their system is already production ready and this is a way to validate their work.

Ideally, having an established process for chaos testing/game days in place will give more tangible goals for production readiness: to feel confident about running a chaos test next quarter, this quarter we’ll need to prepare by investing in our metrics, logging, and alerts.

#### Goal

These are some sample goals for a chaos testing pilot:
* Validate production readiness (metrics, dashboards, logs, alerts, and runbooks) of one of our systems
* Establish and iterate on a postmortem process to get optimal learnings from each game day
* Establish and document a game day process for future use

#### Example tests

* Kill a kubernetes pod. Kill multiple kubernetes pods. Kill all kubernetes pods at the same time. (This should all self-heal, and may not even cause an incident, but it’s probably worth testing anyway).
* Manually scale a service’s number of pods down to 0 (or 1).
* Block some/all traffic from reaching the service.
* Artificially constrain the resources on one or more of the pods - eg. execute a malicious script on a pod that consumes all the CPU or memory.
* Artificially increase load to the service - either with good or bad traffic - does the team have sufficient observability to identify the source of the traffic and block it?
  * Change the traffic profile of the service - repeatedly call the most resource intensive endpoint
* Mess with the connection between the service and one of its dependent services (eg. database, redis cache, or downstream service) by intercepting network traffic. This would take some effort to set up, but if we could set up a generic solution to enable this type of testing it would allow us to test many different types of distributed system outages.
  * What happens if the traffic returns errors?
  * What happens if the traffic times out?
  * What happens if the traffic returns successfully, but after a delay?
  * What happens if DNS cannot resolve the dependency (while the service is running)?
