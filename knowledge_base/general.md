---
title: General
layout: default
parent: Knowledge Base
---

# Dependabot PR's

For handling Dependabot PRs, we generally rely on our test suite. The exact procedure depends on the kind of update, but the tests are always the first and most important indicator for merging.

There is usually a changelog contained in the PR description, to help you decide whether to go ahead with the merge. For more concrete rules, refer to the listing below.

## Bugfix version bumps
For example, `1.1.3` --> `1.1.4` (last digit)

Merge these at will if the test suite checks out.

## Minor version bumps
For example, `6.0.4` --> `6.1.0` (middle digit)

Merge these at will if the test suite checks out.
Exception: If it's a crucial part of our infrastructure like `rails` or `typescript` or similar, keep an eye on the changelog.

## Major version bumps
For example, `3.2.1` --> `4.0.0` (first digit)

You should skim the changelog for any breaking changes that could be relevant.
If it's a crucial part of our infrastructure (see above), get a second pair of eyes to review.

# Developer Database Export

{: .help}
> This can be organized under a more general "Accessing WCA Data" guide

## Are you a third party developer?

We maintain two database exports:

1. The well known public database export: https://www.worldcubeassociation.org/export/results
2. The not so well known developer database export: https://www.worldcubeassociation.org/wst/wca-developer-database-dump.zip

The "public database export" is used by tons of software, and is great if you want to run statistics against all the results ever achieved in competition. We try not to change it, and consequently it does not have some of the new interesting data that we've started tracking our database over the years.

The "developer database export" confusingly enough, is **also** publicly accessible. It is a production database dump with sensitive data (birthdays, emails, etc) removed. The schema should match [exactly what's in master's `structure.sql`](https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/db/structure.sql), so naturally, its schema changes with every database migration. This dump is used by WCA Software Team members for website development purposes, and third party developers who want to do interesting data analyses not possible with the public database export (for an example, see @jonatanklosko's awesome [wca_statistics](https://github.com/jonatanklosko/wca_statistics) project).

## Are you investigating bugs in the developer export?

See [lib/database_dumper.rb](https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/lib/database_dumper.rb) and the [relevant test](https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/spec/lib/database_dumper_spec.rb) and the [corresponding rake file](https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/lib/tasks/db.rake).

There are two commands to deal with the developer database export:

1. `bin/rake db:dump:development` - Generates a dump of our database with sensitive information stripped, safe for public viewing.
2. `bin/rake db:load:development` - Download and import the publicly accessible database dump from the production server.

`db:dump:development` automatically [runs every 3 days](https://github.com/thewca/worldcubeassociation.org/blob/891553f59cc9f2e66f87bba03302c2333f3a285d/WcaOnRails/app/jobs/dump_developer_database.rb#L9) in production.

# Payments with Stripe

{: .warning}
> WST has recently implemented a new payments system using the Stripe API. This information may be out of date.

## Stripe Connect
We use [Stripe Connect](https://stripe.com/docs/connect). The WCA has a Stripe account referred to as the Platform account. Organizers of competitions need to have their own Stripe account. When creating a competition the organizer can connect their Stripe account to the WCA Stripe account via the WCA website. Once the organizer's Stripe account is connected, the WCA Stripe account can make charges, and issue refunds on behalf of the organizer's Stripe account.

## Testing
To test this functionality the WCA has 2 Stripe accounts. One is used as the platform account, and one is used as a test organizer's account. This test Stripe account has been connected to the platform account via the WCA website running in a development environment. This allows the tests to issue actual API calls to Stripe. The test Stripe account details are in a trait called entry_fee in the competitions.rb factory. Any testing of payments need to create a competition using this entry_fee trait so that calls to Stripe will succeed.

# State of the Website

## Our software

- [New Rails codebase](https://github.com/thewca/worldcubeassociation.org/tree/master/WcaOnRails)
  - User account system (with OAuth!)
  - [`/results/admin/`](https://www.worldcubeassociation.org/results/admin/) - Results administration panels. Access is enforced by Nginx through our [Rails api](https://github.com/thewca/worldcubeassociation.org/blob/7ad2cc53a14e0da10a39f215aabecc98c5bcd85a/chef/site-cookbooks/wca/templates/worldcubeassociation.org.conf.erb#L53).
    - [`/results/admin/phpMyAdmin/`](https://www.worldcubeassociation.org/results/admin/phpMyAdmin/)
  - [`/results/`](https://www.worldcubeassociation.org/results/)
- [`/regulations/`](https://www.worldcubeassociation.org/regulations/) - [Regulations](https://www.worldcubeassociation.org/regulations/)

[This milestone](https://github.com/thewca/worldcubeassociation.org/issues?q=is%3Aopen+is%3Aissue+milestone%3A%22Drop+PHP%22) tracks our progress in porting our PHP code to Ruby on Rails.

## Backups

We use the excellent Tarsnap for backups. See [Chef recipe](https://github.com/thewca/worldcubeassociation.org/blob/master/chef/site-cookbooks/wca/recipes/backup.rb).

## Payments

- See [worldcubeassociation.org secrets](https://docs.google.com/document/d/1x2on0licYZPI2WoYFSgFi0pb59gA1YToh1PFcOLb4lY)

# Updating TNoodle

If the updating affected `tnoodle-lib`, WRC needs to approve the changes.

Whenever a new version of TNoodle is ready to be released, there are a bunch of changes to do to the website.

First of all we need to put the new binary in the `regulations-data`, and move the old one to the `old` directory. See [this](https://github.com/thewca/worldcubeassociation.org/pull/938) PR for an example.

Then we need to update the api (and its spec) to reflect the change, and the scramble page. See [this](https://github.com/thewca/worldcubeassociation.org/pull/939/files) PR for an example.

Then someone from the WRC has to make a post on the website announcing the change. See [this](https://www.worldcubeassociation.org/posts/tnoodle-0-11-3) post for an example.
The preferred day for TNoodle releases is Monday.

# WCIF: WCA Competition Interchange Format

This is currently under rapid flux. See our [Google Drive folder](https://drive.google.com/drive/folders/13h6RCmD-wnzTmKOFMJFgvY3M4GjZTuI4).

# Why do we have both users and persons? 

If you look at our [database schema](https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/db/structure.sql), you'll notice that there are 2 tables: one for "users", and one for "persons". The persons table has existed since the start of the WCA, it tracks everyone who has competed. If you have competed, you will have an entry in the persons table. We introduced the users table in early 2015 when the Rails port began. Users are people who have created an account on the website*. As part of creating an account on the website, users are asked if they have competed before, and if they have, they are prompted to claim a WCA ID. They have to enter their birthday and it must match the birthday in the persons table for them to make this claim. Furthermore, this claim must be approved by a WCA delegate of their choice. After approval, their user will have a `wca_id` field.


*With the notable exception of some "dummy accounts" we created to keep profile pictures working. These dummy accounts have been a continuous source of pain, and we will probably get rid of them someday.
