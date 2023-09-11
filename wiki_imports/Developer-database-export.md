---
title: Developer Database Export
layout: default
parent: Wiki Imports
---

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
