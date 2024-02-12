---
title: TDD on the Monolith
layout: default
parent: Guides
---

* *TDD:* Test-Driven Development
* *Monolith:* The main worldcubeassociation repo ("monolith" as opposed to "microservices")

### Why do TDD?

Automated tests are an essential part of being able to work on the WCA's codebase with confidence. We don't need to worry about what unforseen consequences a change we make might have, we just need to run the tests and see whether or not they pass. In an open-source system with many different contributors, this is crucial. 

- Tests are crucial for our development workflow. They have to be written at some point, and writing them while developing is easier than writing them at the end when you just want to ship the PR already. 
- We tend to write more and better tests when testing during development than after it, as our code is written with testing in mind - meaning we are more likely to use patterns like loose coupling and dependency injection.

### Challenges of TDD in the monolith

In TDD, you want to run tests every few minutes when developing or refactoring, and you want quick feedback from the tests - ideally, executing in under 10 seconds. The larger suite should then be run after each significant TDD workflow.

Running the monolith's tests locally is far too slow, for 2 reasons:
1. Size of test suite - 6000 tests take a long time to run, no way around that
2. Long startup time for tests

Why the long startup time? 
- Long compilation time for RSpec tests? 
- Takes a long time to load testing plugins? 
