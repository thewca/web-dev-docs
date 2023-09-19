---
parent: Knowledge Base
title: Testing
layout: default
---

# Current State of Testing 

## Problems with current test suite

1. Intermittent failures
2. Long execution time[^2]
3. Inconsistent testing approach

## Advantages of current test suite

1. High coverage
2. Good use of testing conventions (eg FactoryBot)

## Structure of Tests Directory

# Recommended changes

## Performance improvements
- 


# Testing Philosophy

Much of this is based on the excellent (and free) [Testing Rails](https://books.thoughtbot.com/assets/testing-rails.pdf) by ThoughtBot. 

## What should a testing suite look like?

{: .info}
> Consult "Types of Tests" below for definitions of the different kinds of tests.

Our test suite should (a) run as fast as possible, and (b) have a comprehensive amount of coverage. In order to achieve this, we use a mix of test types (end-to-end, integration and unit).

Generally speaking, a feature should have: 
1. An end-to-end test covering the "happy case" (ie successful outcome)
1. Integration tests covering other important cases (some judgement is needed here, but there shouldn't be more than ~3-4 integration test per major feature)
1. Unit tests covering the granular test cases

End-to-end tests are the slowest tests, so we try to minimize the number of them. Unit tests are the fastest, so we test lots of granular logic with them. Integration tests fall somewhere in the middle

## Types of Tests

### End-to-end tests
1. System tests (feature tests) - these use browser emulator through the Capybara gem to simulate user actions and assert outcomes as they are displayed on the page
1. Request tests - in cases where an API is being tested, request tests are the end-to-end tests. (In these cases we may want to use RSwag to define the happy cases/API schema)

### Integration tests
1. Request tests[1] - these submit a request to an API or route (which goes via routes.rb, to the controller, to the model, and back to the user)
1. Controller tests - tests functions in the controller (these are integration tests because they almost always query the model)

### Unit tests
1. Model tests - these test functions in the model

### Other Rails tests

Other types of Rails tests (routes, views, etc) do not feature in our testing philosophy as they should be covered by other tests.

## Best Practices

1. **Develop with other contributors in mind** - remember that the people running your tests won't have the knowledge and context that you do on the code you've written.
1. **Test one thing at a time** - this makes it easy for other contributors (and you!) to know exactly _why_ a test is failing 
1. **Prefer Test-Driven Development where possible** - it generally helps write better (and more) tests, and better code
1. **First plan test cases, then consult coverage** - 100% coverage doesn't mean much if there's an edge case you haven't written code for


# Checking Coverage

{: .info}
> "Coverage" is a metric for the percentage of lines of code which are tested in your repository. It is a useful indicator for where there might be insufficient testing in our code.

We used SimpleCov for checking out test coverage. It runs automatically with RSpec, and generates coverage-related data files in the `/coverage` directory. These can be viewed by navigating to [Coveralls](https://coveralls.io/), logging in with your Github account and selecting the repository you want to view the coverage metrics for.

----

[^1]: In an API, request tests are end-to-end test (system tests can't exist because there is no rendered view to interact with). However, in a system that renders a page, end-to-end tests would be considered integration tests because they are not testing the full system.

[^2]: Results of test profiling:

Top 10 slowest examples (139.77 seconds, 9.0% of total time):
  I18n does not have unused keys
    20.94 seconds ./spec/i18n_spec.rb:14
  AnonymizePerson generates padded wca id for a year with 99 ANON ids already
    19.63 seconds ./spec/models/anonymize_person_spec.rb:25
  DatabaseDumper dumps the database according to sanitizers
    17.94 seconds ./spec/lib/database_dumper_spec.rb:44
  Competition management when signed in as admin change competition id with validation error
    12.34 seconds ./spec/features/competition_management_spec.rb:86
  create competition tabs creating a new tab
    12.32 seconds ./spec/features/create_competition_tabs_spec.rb:9
  I18n does not have missing keys
    11.55 seconds ./spec/i18n_spec.rb:10
  create competition tabs editing an existing tab
    11.51 seconds ./spec/features/create_competition_tabs_spec.rb:24
  Competition management when signed in as admin clone an existing competition with valid data
    11.46 seconds ./spec/features/competition_management_spec.rb:32
  users can change password
    11.43 seconds ./spec/requests/users_spec.rb:41
  Competition management when signed in as delegate id and cellName changes for short comp name
    10.66 seconds ./spec/features/competition_management_spec.rb:189

Top 10 slowest example groups:
  create competition tabs
    11.91 seconds average (23.83 seconds / 2 examples) ./spec/features/create_competition_tabs_spec.rb:5
  Competition management
    8.72 seconds average (165.59 seconds / 19 examples) ./spec/features/competition_management_spec.rb:5
  Stripe PaymentElement integration
    8.71 seconds average (26.13 seconds / 3 examples) ./spec/features/stripe_integration_spec.rb:5
  Registration management
    7.87 seconds average (7.87 seconds / 1 example) ./spec/features/delegate_report_spec.rb:5
  Registering for a competition
    6.25 seconds average (56.22 seconds / 9 examples) ./spec/features/register_for_competition_spec.rb:5
  My competitions page
    6.12 seconds average (6.12 seconds / 1 example) ./spec/features/my_competitions_spec.rb:5
  Media
    5.51 seconds average (33.04 seconds / 6 examples) ./spec/features/media_spec.rb:5
  cookie law
    4.96 seconds average (9.92 seconds / 2 examples) ./spec/features/cookie_law_spec.rb:5
  Manage team
    4.88 seconds average (4.88 seconds / 1 example) ./spec/features/manage_team_spec.rb:5
  Competition events management
    4.61 seconds average (73.81 seconds / 16 examples) ./spec/features/competition_events_spec.rb:5
