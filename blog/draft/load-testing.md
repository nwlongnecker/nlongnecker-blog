# Load Testing
_Under Construction - last updated February 27, 2023_

This page covers things to think about when running a load test.

“Standard” load testing has the goal of ensuring your service can handle a specified amount of load. This is typically done as part of our annual holiday prep, to ensure we’re confident the system can handle expected holiday traffic. This is extremely important to validate, but still leaves a few questions unanswered that are important for service owners to answer:

How close are we to the limit of what the service can handle?

Would the service behave differently under extended periods of load (days, weeks)?

What is the service’s constrained resource? Could the service get better performance (more load, faster response time) with some configuration tweaks?

This page will cover the questions to ask yourself when running a standard load test, then talk through additional things that can be done to find answers to the above questions.

How much load?
The first thing to determine is how much load your service expects for holiday. A good rule of thumb is that site traffic will increase 3-4x during holiday, relative to standard traffic levels. When checking traffic on your service:

Our traffic varies wildly over the course of a day and depending on the day of the week, so generally you want to look over a longer period of time (eg. a week) to find peak traffic levels. Ensure you’re looking at the number of requests per minute - if you amortize the number of requests in an hour or a day you may miss spikes. Your service needs to be able to handle the highest traffic spike we might see during holiday - not just the average number of requests per day (which would be far lower).

Some services have a mixture of traffic from synthetic sources and from site traffic. Consider how much of the traffic on your service time will scale with site traffic vs will remain constant. For example, traffic from build jobs should remain constant through holiday - we won’t be building more frequently.

With the knowledge that site traffic is likely to increase 3-4x, identify a target load that you want to validate your service can handle. It’s generally good to give yourself a little headroom in case traffic increases more than expected, so load testing to 5x site traffic may be a good idea.

Writing the test
When you write your load test, you want to ensure the load test is representative of traffic your service will experience during normal site operation. You can write a test that will hit the same url every time, and get up to the target number of requests per minute, but that single request could be cached or not exercising the full functionality of the service. Some requests may take longer than others, which might mean the service could handle 200 rpm on one endpoint but only 50 rpm on another. Since some endpoints on a service may behave differently, you want to ensure you get a large variety of requests to mimic production traffic.

Running the test
Before you run your load test, be aware that load testing is testing the limits of your service. If something goes wrong, the service may slow down or go down, so caution is necessary. Sometimes it is prudent to do a load test in staging first. It’s important to also do your load test in prod to verify that there isn’t some infrastructure difference that lowers your capacity, but testing in staging first can be useful to identify most issues.

Additionally, it’s important to ensure the domain you’re doing your load test against points to the origin of your service, not against any CDN that your service may have. You want to test the capacity of the service, and that’s harder to do if the CDN is helping shield it from load.

You probably also want to inform any downstream services (services that your service depends on) that you’re about to run a load test and about how much load they should expect to see.

Using Locust
Locust has historically been MTT’s load testing tool of choice. It’s easy to get set up and python is a relatively intuitive language. It does have a few things to be aware of:

In Locust, you specify the number of users, not a target requests per second (RPS) and each user will make a request, wait for a response, wait a period of time, then make another request. This means that your test’s RPS is dependent on the response time of the service. If your service takes longer to respond, there will be more time between requests, and so fewer requests per second.

Since you need to specify a number of users rather than target RPS, you’ll need to guess a bit about how many users will be needed to get to the right ballpark. It doesn’t need to be exact.

Locust reports requests in requests per second, not requests per minute. You’ll need to convert between them.

Locust (in it’s basic configuration) runs on your local machine. If you are unable to reach the target load for your test, it could be due to hitting bottlenecks on your machine (CPU limits, I/O limits) rather than service limits. These types of problems (depending on the cause) could be solved by rewriting the test in a different way, or switching to a distributed load test (which is doable in locust, but requires more setup).

Observing the impact of the test
It’s important to observe the health of your service during the test so you understand how your service responds to the added load. Make sure you have and are watching dashboards monitoring:

Throughput of your service

Response time of your service

Error rate of your service

CPU utilization of your service

Memory utilization of your service

Additionally, if you have tools like NewRelic with tracing capabilities, those can give you better insight into what’s happening inside your service and where any slowdowns may be happening.

Locust will also show dashboards with throughput and error rate, but it’s useful to have these metrics from multiple places to better understand what's happening during the test.

It can also be good to check for scaling events to ensure that your service appropriately responds to high throughput.

How close to the limit?
After running a load test, you’ve established that your service can comfortably handle a certain amount of load. However, we don’t know how close that is to the limit of what the service can handle. Maybe it could handle an extra 5% load before degrading, or maybe it could handle an extra 10x load before degrading. If your service can handle 10x load, it may be overprovisioned and you may be able to downsize the service nodes to save some money.

Understanding the capacity of your service can be useful when doing risk assessments and understanding when it would be useful to take action if traffic is significantly higher than expected.

The naive way to test the limit of a service is to run a load test, steadily increasing the load until the service falls over. This will tell you the limit of what it can handle, but typically requires a lot of load (more than can be generated by a single machine running locust), and will take down your production service. Alternatively, you could load test against a single node or pod, with no autoscaling, and just push that pod until performance degrades significantly. That should tell you how much your service can handle per pod, and theoretically the maximum capacity of your service if you multiply by the number of pods (assuming perfect horizontal scalability).

Checking for resource exhaustion
Some services may experience resource exhaustion when run at load for extended periods of time, eg. if they have memory leaks, or if they write logs to disk and would fill up the disk. Checking for these is relatively straightforward; just run the load test for an extended period like overnight, and check the memory utilization and disk utilization over time. If it trends upwards, then you’ve discovered a potential problem. If memory utilization gets too high, it can either cause Out of Memory errors, which will cause the node to crash, or it can cause the node to start using swap memory by moving memory to/from the disk. Using swap is generally a very bad thing for servers under load, because most operations become significantly slower, so your application can quickly degrade and collapse under the load.

Understanding the constrained resource and scaling properties
Other things that are good practice to do and important to think about during everyday development is to understand what your service's scaling properties are. What is the constrained resource? Some services use a lot of compute, so they're CPU bound. Other services use lots of memory, so they're memory bound. Other services make lots of service calls, so they're I/O bound. Understanding the scaling properties of your service is important so you right size your instances (which just means picking the appropriate amount of compute and memory for your containers/instances to get the most bang for your buck) and so you can autoscale on the appropriate metrics. If your service has an autoscaling policy set up so it will add additional nodes if the CPU goes above 50% utilization, but it's actually an I/O bound service, then you run the risk of hitting the limit of what your service can handle (eg. run out of sockets, doesn't have enough network bandwidth) without triggering your autoscaling policy. In that case, it doesn't matter that you have autoscaling, your service still goes down. So it's important to know what metric limits your service from handling additional traffic and watch that metric, alert on it, and autoscale based on it.

So the question there is: how do you figure out what your service's scaling properties are?

You need to send enough traffic at the service to take it down, then figure out why it failed. What was the resource that ran out first?

Usually the constrained resource for a service is one of: CPU, Memory, or I/O. CPU and Memory are relatively easy to monitor with built in AWS tools or NewRelic, but I/O is generally harder to monitor, since there are several places it could fail. For example, the service may run out of sockets, or may run out of bandwidth.

When you understand your constrained resource, you can right-size your instances and appropriately configure your autoscaling policy. If your service is memory-bound, you can switch to an instance size that is memory-optimized, to allow the service to handle more load with fewer wasted resources. If you change your instance size, you should always re-run your load test to see if your constrained resource has changed.

Once you have determined your constrained resource, you can configure your autoscaling policy to scale when that resource is in danger of being exhausted. This will ensure that you scale when needed and not unnecessarily. Additionally, you know which resource is most important to configure alerts for, because that’s the resource that gives you warning signs if your service is about to go down.