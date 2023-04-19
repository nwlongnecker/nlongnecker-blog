# Getting started with mob programming
_Last updated April 18, 2023_

This document lays out the baseline rules I recommend for a team who wants to experiment with mob programming. This document summarizes and will try to stick as closely as it can to the rules Woody Zuill and Llewellyn Falco both recommended when I had the opportunity to watch some of their talks and mob program with each of them in 2017.

The general principle of mob programming is that there are many people, but only one computer. All code written must go through that single computer. This article gives a good overview of how and why mob programming was discovered and why it might be a good idea: [Mob Programming - A Whole Team Approach by Woody Zuill](https://www.agilealliance.org/resources/experience-reports/mob-programming-agile2014/)

## 1. Strong Driver-Navigator Style Mobbing
In the mob, there are three roles: Driver, Navigator, and Observer. Participants will cycle through all three of the roles over the course of the mob. The basic rule is that “for an idea to go from your head into the computer it MUST go through someone else’s hands.”

In this case, “strong” just means “strict.” Many of the benefits of mob programming are due to the fact that there is constant verbal communication about what we are working on, and if we don’t start out strict with these rules to start with then it’s easy to fall into a pattern where one person codes while everyone else watches, and we want to avoid that as that style of mob programming is significantly less effective.

### Driver
There is exactly one driver. The driver is at the keyboard, in control of the computer. However, the driver is dumb. They can’t put any of their own ideas into the computer. They must trust in and follow the instructions of their navigator. If the navigator isn’t sure what to do or doesn’t give any instructions, the driver does nothing and waits at the keyboard, even if they have an idea. This helps to ensure that any ideas that are being worked on must be verbalized before being typed.

### Navigator
There is exactly one navigator. The navigator is the sole person in charge of what the mob is currently doing. They express their intentions verbally, at the highest level of abstraction that is appropriate for the current driver. If the current driver is non-technical (yes, you can mob with non-technical people), that could mean telling them exactly which keys to press, but with more technical folks the navigator could describe lines, functions, or classes to be implemented.

### Observer
All remaining participants are observers. Observers are part of the mob, and should remain focused and engaged, learning from the ideas of navigators and thinking about how to improve the solution, but should refrain from speaking unless the navigator asks for input from them. This helps to ensure that every participant in the mob gets a turn as navigator, even if they’re more timid, and prevents one or two people from dominating the mob.

Drivers should help to reinforce this pattern by ignoring instructions from the observers. If an observer’s idea should be implemented by the driver, the navigator should tell the driver to do that.

-------

Sometimes it may be appropriate for someone to jump over to a whiteboard (or miro diagram when remote) to express their idea for how to address the current problem to he rest of the mob, rather than trying to express their ideas verbally or through code.

Once people are good about staying in their roles and ensuring everyone has a chance to contribute their ideas, it’s okay to relax the “strong” distinction between observer and navigator, and instead start to have one driver and everyone else as navigators. However, if someone starts to dominate the mob, or if one of the more timid members of the group aren’t getting a chance to share their ideas, it can be good to reset back to the “strong” distinction between observer and navigator for a bit alleviate those problems.

## 2. Rotate Frequently
Different articles will recommend different rotation speeds for mob programming, I’ve seen recommendations ranging from 4-5 minutes to 7 minutes to 10-15 minutes, but a common theme is that folks who are newer to mobbing should use shorter rotation speeds. For your first few mobbing sessions, I recommend rotating every 4 minutes.

Why? Mobbing is all about keeping everyone engaged in and thinking about the problem space. Rotating quickly ensures everyone gets a chance to do every role, and observe everyone else in each role, several times over the course of the mob.

Additionally, when you only get 4 minutes in the Navigator seat, you’re encouraged to make the most of the time. The short time helps keep things moving, encourages people to express their ideas so everyone’s on the same page so the next navigator can pick up where the previous person left off, and limits the time that the mob is stuck if a navigator isn’t sure how best to approach the problem.

## 3. Rotation sequence
Order matters! The navigator should become the next driver, and the driver should become an observer next, and the person who has been an observer longest should become the next navigator. This helps to ensure that:

The person who was just the navigator can’t continue to backseat navigate (since they’re the driver, they can’t contribute ideas, instead they must just ask clarifying questions), so this forces them to clearly express their ideas to the rest of the team so the mob can (and wants to/understands why they should) continue down the same path when they’re no longer navigating.

Observers are encouraged to stay engaged by the imminent “threat” of being on the spot as the navigator (in a 5 person mob, in 12 minutes or less).

## 4. Retro afterwards
In line with Woody's article on mob programming linked at the beginning of this page, at the end of the mob session we should have a mini retro and think about what worked and what didn't work and what we want to do the same or differently next time (or never try this again).

-------

## Other general ideas to keep in mind while mobbing
Mobbing is a great time to align on code quality standards, and teach people about helpful tools, plugins, and keyboard shortcuts. The entire mob picks up on how to use those tools and keyboard shortcuts remarkably fast after seeing one or two of their teammates use it successfully.

If the development environment isn’t quite right, or needs a tool or plugin installed, it’s a worthwhile use of time during the mob to set that up. It only needs to be set up once during the mob, and once it’s there you will learn how to use it effectively and speed up your day to day development, saving time overall.

## Mobbing remotely
I have used visual studio code's live share feature effectively to run remote mobbing sessions. It allows the mob to quickly switch between drivers while remote, allows access to the host's localhost, and allows sharing read/write terminals for committing code and executing tests.
