# When to Rearchitect a Component
_Under Construction - last updated February 27, 2023_

Identify any user stories that are difficult or impossible to achieve with the current architecture.

If you _had_ to implement it in the current architecture, no matter the cost, what would you do? Walk through what would be required, it’s possible it’s not as much work as expected.

If it is achievable except for one edge case, separate that out into its own user story. What’s the value of that edge case? Could you push back on just that?

Could this specific use case be hard-coded? Do you need a generic solution, or is there only one practical application of this feature for the foreseeable future?

How important is this use case? Could/Should we push back on the requirement?

If you could change the architecture to support this user story, what would need to change? How much investment is that compared to implementing it in the current architecture? How much tech debt do we incur either way?

Do not conflate multiple user stories when justifying an architecture change. **A single user story should be enough justification _by itself_ to make the change.** If your justification is “it makes all these kind-of hard things easier,” but all those things could each be solved separately in the current architecture, you should solve it in the current architecture.

If you determine an architecture change is really required, lay out increments and a roadmap for moving to the new architecture. How many of those increments are needed to support your single user story? How many can be left for later?
