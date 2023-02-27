# Leading a Project
_Last updated February 27, 2023_

This document describes a general process for how one might lead a project. This document is primarily geared towards engineers that are looking to lead one of their first few projects, but I have found the general approach to be useful to reference regardless of my current experience level.

## Steps in breaking down a project
The following are my recommended steps to follow when breaking down a project.

### Step 1: Identify use cases
This is the **what** of the project - what are we trying to accomplish. The use cases should generally be provided by the person who conceived of the project, whether that be a stakeholder, product manager, tech lead, or engineer, though formalizing and documenting them may still be the project lead’s responsibility, and may require gathering input from multiple stakeholders.

Note that use cases may have varying importance, some may be “nice to have” while others may be “the project isn’t worth doing if we don’t have this.” Remember to capture that in case you need to make tradeoffs later.

### Step 2: Distill requirements
Based on the use cases, establish a list of requirements for the project. The requirements should capture all the use cases, and may additionally capture technical constraints or design principles that you want to follow.

Additionally, track which use case(s) each requirement came from in case you need to make tradeoffs later.

### Step 3: Design a solution
Design and document a solution that fulfills the requirements. Usually this will include both a written document and an architecture diagram. This should capture the ideal end state in enough detail to be confident that you’ve thought through the important edge cases and any challenges that might require you to go back to the drawing board, but it doesn’t need to exhaustively cover all of the “nice to have” features. It should build your confidence that the design is flexible and extensible enough that those features could be added without much trouble. **The output of this step should be a design document, not code.**

Especially if there are technical concerns about the feasibility or maintenance overhead of parts of the project, it may be appropriate to do a proof of concept during this stage. The purpose of the proof of concept is to prove that the idea or approach you were concerned about will work and doesn’t have hidden surprises. Once you’ve determined that, the proof of concept has fulfilled its purpose and should usually be discarded.

You should have one or more design reviews at the end of this phase, soliciting feedback from your team, technical lead, and/or stakeholders, to ensure they approve of your intended direction.

### Step 4: Define increments
Once the ideal end state of the project is documented, break the project down into increments, factoring in technical effort and relative priority of use cases to ensure delivery of a version of the project that provides value (solves the critical use cases) as early as possible.

### Step 5: Break down the increments
Now that the increments have been defined, they can be broken down. Depending on the level you’re operating at, this could mean creating tasks, tickets, or epics, which can then be further broken down as needed. It may be appropriate to do this step with your team during a grooming session.

### Step 6: Implement
This should be the first step where you write code that will run in production. During this phase, you may need to adjust your increments or revise your plan if you find new information that wasn’t considered during the design phase. When adjusting your design, remember to keep sight of how the design ties back to the requirements and use cases (especially the relative importance of the use cases), and don’t be afraid to revisit the overall design if the team gets stuck.

## Process
General process tips when leading a project.

### Checkpoints
When leading a project, particularly if you’re new to it, it is a good idea to have design reviews or checkpoints with your tech lead as you conclude each of the above steps. This gives you an opportunity to get another perspective, validate your thinking thus far, and gives them a chance to ask questions about things you may not have thought of or poke holes in your ideas before you spend too much time building on them.

### When to share with the team
Every team is different, so collaboration will vary based on team, but at a minimum you will probably want to have two context-sharing sessions with your team about the project before they see any tickets for the project in a planning session.

The first context-sharing session should share the goal of the project, meaning the use cases (they can be summarized/categorized) and requirements, and then also share the the intended design of the project. This allows the team to understand the project conceptually and ask questions/offer suggestions regarding the design.

The second context-sharing session before implementation should share the goal of each increment for the project, then the breakdown of work to achieve that goal (likely just for the first increment). This allows the team to understand the short term goal in the context of the longer term goal from the first session and then ask questions/offer suggestions regarding the implementation.

The two sessions should be on separate days as it can be overwhelming and the feedback will not be as good if it’s all at once.

### The importance of context
A large part of leading a project is ensuring the other people working on the project have the context to do their job effectively and make decisions about the project. This is why it is important to document the use cases, requirements, design, and intended increments, and have multiple context-sharing sessions with the team before starting implementation rather than jumping directly into coding and asking the team to help out with something you already wrote half of (I’ve done this and it doesn’t go well).

It's a lot easier to get general feedback about the approach from people based on a diagram than based on code, and easier to explore other options since we're not yet as invested in a particular approach.

It's very helpful for your team to be involved from the start, before any code is written. That way, they understand the reasoning behind the approach. I've seen several projects fail or go off the rails because someone had a great idea, wrote a bunch of code to implement it, then either:

Nobody else knew exactly what it did and it was too intimidating to learn about, so they were stuck being the sole owner of the project, and basically the only person who could maintain or update it.

The team who picked it up didn't understand the whys of the approach and strayed from the initial requirements and design principles, so the project ended up not solving the initial use cases very well.

### Retrospectives
Retrospectives are an important tool to reflect on what’s working and what should be changed, and for the project lead to receive feedback from the team about how things are going. Regular retrospectives can help capture learnings and course-correct if needed.