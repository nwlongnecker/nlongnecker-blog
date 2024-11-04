# On Call Expectations
_Last updated November 4, 2024_

Several of the teams I've been on haven't previously had on call expectations established, so I've proposed some. I've found the below expectations to work pretty well for the customer-facing e-commerce teams I've been on in the past. In a SaaS/B2B context, a formal SLA would likely be appropriate.

## On Call Expectations
Our goal is to provide timely responsiveness to incidents around the clock, including during weekends and holidays, while also enabling a good quality of life for our team.

Accordingly, this team does not have a formal SLA for our on call rotation. A formal SLA would include guaranteeing that we will respond within a specific timeframe, and to guarantee that we would need to place restrictions on what our engineers could do outside of work while on call, for example they might need to stay within x minutes/hours of a computer with internet access.

Instead, to help maintain our quality of life, we follow a “best effort” approach, and trust our team members to respond when possible. This means that even while we are primary on call we can still travel to or through an area with no phone service, have drinks with friends, step away from our phone for a few hours, or otherwise enjoy our life outside of work. If the primary doesn’t respond, a secondary will be notified, and if neither respond, the whole team will be notified. With each of us making our best effort to respond when possible, at least one of our team members should respond in a timely fashion.

We will reassess this approach if we find ourselves failing to respond to alerts with sufficient timeliness.

### Responder Expectations
When responding to an alert, we are expected to be able to evaluate the impact of an issue and determine whether the issue needs to be resolved immediately or if it can wait until business hours. For the most part, this should involve following runbooks our team has already put together that define concrete actions to take. In the event that the issue needs to be resolved immediately, the on call member should engage an incident commander.

Regardless of whether it was a false alarm or indicated a real issue, after receiving an alert, the responder should (during business hours) put together a short writeup indicating what went wrong, why it went wrong, and the timeline of the incident (how long did it take to detect, identify, and resolve) and organize a blameless postmortem so we can discuss what action items we will take to avoid similar alerts in the future. In the case of false alarms, this may include tuning the alerts or disabling them for staging, while in the case of real production issues, it likely includes addressing a root cause or improving resiliency.

## Personal Notification Settings
The expectation is that each team member has set up personal notification rules that will ensure they are aware that they are being paged if they are near their phone and their phone has service.

Typically, I configure my notification rules as such:
1. Immediately send me an email
2. Immediately send me a text
3. Immediately send me a push notification
4. After 2 minutes, call me
5. After 5 minutes, call me
6. After 10 minutes, call me

Also, I always configure my on call app to allow it to override my system volume settings to ensure I get notified even if my phone is on silent.
