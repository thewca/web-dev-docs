---
title: Overview
layout: home
nav_order: 1
---

## Quick Links

- [Quickstart]

## Contributing to the Docs

Our documentation is [open-source]! You can submit corrections or add documentation - but open an issue to discuss larger contributions first.

## About

The [World Cube Association] ("WCA") governs twisty puzzle (think: Rubik's Cube) competitions around the world. There are a number of software resources which enable it to do this.

The code for these websites is open-source and any contributors are welcome to submit PR's. The [WCA Software Team] ("WST") is responsible for maintaining and driving development on WCA's software. See below for a list of software being developed.

## Contacting WST

Remember, our repos are open-source - you don't have to be a member of WST to contribute!

* **Open an issue**: Create a link in the relevant repo - "WCA Software Resources" below contains links to all repos.
* **General enquiry**: Submit an email to WST via the WCA's [contact form]
* **Joining WST**: Currently (2023-09-10) WST is not accepting new members. We aim to onboard more volunteers by early 2024.

## WCA Software Resources

### Websites

#### [WorldCubeAssociation](https://www.worldcubeassociation.org/) ([repo](https://github.com/thewca/worldcubeassociation.org))
- The main website for the WCA - commonly referred to as the "Monolith".
- Primarily uses Ruby on Rails, transitioning to a React-based frontend.

#### [WCA Live](live.worldcubeassociation.org/) ([repo](https://github.com/thewca/wca-live/)):
- A website for capturing and displaying live results at WCA-sanctioned competitions.
- Primarily uses Elixir and React, transitioning to a Rails-based backend with Elixir only used for serving results data to clients.

#### [WCA Statistics](https://statistics.worldcubeassociation.org/) ([repo](https://github.com/thewca/statistics)):
- Provides various interesting statistics outside the scope of the main website. Also provides an interface for SQL queries to be made against a "developer export" of the database.
- Python backend, Javascript frontend.

#### [Scrambles matcher](https://scrambles-matcher.worldcubeassociation.org/) ([repo](https://github.com/thewca/scrambles-matcher))
- Used by delegates to match scrambles to rounds and groups
- Under a feature freeze (will be migrated to an integrated scramble management tool)
- Written in Javascript 


### Other Software

#### TNoodle ([repo](https://github.com/thewca/tnoodle))
- Used by delegates to generate scrambles for competitions
- Under a feature freeze (will be migrated to an integrated scramble management tool)
- Written in Java

---

[World Cube Association]: https://www.worldcubeassociation.org/about
[WCA Software Team]: https://www.worldcubeassociation.org/about
[WST]: https://www.worldcubeassociation.org/teams-committees#WST
[open-source]: https://github.com/thewca/web-dev-docs
[Quickstart]: /contributing/quickstart
[contact form]: https://www.worldcubeassociation.org/contact/website
