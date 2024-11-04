# Service Design Principles
_Last updated November 4, 2024_

## Make the core design as generic as possible

Find the core business problem that the service aims to solve, and solve that abstract problem in a "core" module inside the service. Then add layers around it that factor in the restrictions mandated by the business requirements. This approach keeps the technical problems separate from the business requirements and makes both easier to reason about independently and solve. Done correctly, it can enable representing the business requirements expressively in code. Additionally, as these business requirements change, they can be modified (or lifted) to add functionality to the service.

## Design for speed, cacheability, and reliability whenever possible

After pre-loading data on startup, consider whether the service should be able to respond to all read requests without making an off-box network request (except, potentially, to a shared in-memory cache, should that be appropriate based on memory utilization).

If the service doesn't need any live network requests at runtime, could it be pre-generated and cached as static files?

In the event of service downtime, can consumers continue functioning, even if their data becomes stale? What happens if a service's cache is empty?

Consider a CDN, if appropriate for your org's service architecture.

### Smart clients

Some services may merit the use of a client library. In general, I strongly recommend against a client library - if the API is complex enough that a client library is necessary, it is a smell that suggests the API may be leaking some domain concerns and should be redesigned. However, sometimes a client library is appropriate (eg. performance).

After pre-loading data on startup, services using the client library of the service should be able to do everything on-box (without a network request to the service - refreshing data should be done as a background process).

Minimize the data transfer: Clients should be able to specify their use case and only load the data necessary for that use case.

### Table stakes

The service should be deployed across multiple availability zones.

The service should make use of HTTP cache headers: max-age & stale-while-revalidate.

The service should avoid using inputs specific to a particular user when possible, as those impact cacheability.

Compute-intensive operations (eg. transforming database data structure into json/API data transfer data structure) will be memoized.

The liveness probe should verify that data has been fully loaded before it indicates the service is online.

## Design for readability, intuitiveness, and expressiveness of the API over ease of implementation

The API should not reflect the database schema.

## Design with future changes in mind

Version the API. Don't make breaking changes.

If a client library is necessary, most types of changes should be doable exclusively through changing the service itself and should not require changes to the client library, as changing the client library will require other teams to update/deploy a version of their service with the new client and that will slow down rollout.

## Do not constrain the technology choices of clients

Standardize on OpenAPI (or an org-appropriate alternative) as the mechanism for expressing the API, rather than something like a Java interface.

## Make the capabilities offered truly self-service

If it's a read/write service, clients should be able to use the management API to do all relevant CRUD operations without touching any of the service code.

The API should make the source of errors & issues clear, and not require interfacing with service owners to solve problems.

The API should be well documented, intuitive, and easy to use.

## Design for observability

The service owners should know at any given time how many requests the service is receiving per minute, which endpoints are used most frequently, how frequently each client is calling and which endpoints they use most, and how long requests to each endpoint are taking by percentile.

The service owners should be confident that if clients cannot reach the service then they will be notified.
