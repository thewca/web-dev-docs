---
title: Caching and DB Optimizations
layout: default
parent: Wiki Imports
---

This is a list of things we are currently caching or using the Read replica for instead of using the primary database:

# HTML Request Caches
These cache full or partial HTML requests or json objects using the built in Rails Cache

## API
- /v0/records: json, no expiration date set

## /results
- /results/records: HTML fragment, no expiration date set
- /results/rankings: HTML fragment, no expiration date set

## /competitions
- public wcif: json is cached for 5 minutes
- by_person: HTML fragment, no expiration date set

## /persons
- _results_by_event: HTML fragment, no expiration date set

## /registrations
- index_html: HTML fragment, no expiration date set. This works by using "users updated last" as part of the cache key

# Model Caches

## Cachable
This is a module which caches SQL query results by saving them in a class variable, this is used for Continent, Country, Event, Format, Round_type and Team.

## CachedResult
This is a Class which caches a SQL Query result for Results used in /results/records and /results/rankings
