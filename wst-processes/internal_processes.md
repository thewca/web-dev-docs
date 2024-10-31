---
layout: default
parent: WST Processes
title: Internal Processes
---

# Accessing Database Backups

## Current - AWS Backups

Backups are stored in S3 as versions of the `cubing.sql` file. 

QUICK LINK: https://s3.console.aws.amazon.com/s3/object/wca-backups?region=us-west-2&prefix=latest%2Fwca_db%2Fcubing.sql&tab=versions
1. Navigate to AWS console
2. S3 -> Buckets -> wca-backups -> latest
3. Toggle "Show versions" to see a version history, and download the one you need
4. Bear in mind that you can just grep this file for basic data - you don't always need to load it into a SQL client

# Add/Remove Someone to/from WST

1. Add them to the Software Team on the WCA website: <https://www.worldcubeassociation.org/teams/2/edit>.
2. Send an email to the Board (so that the change is included in the staff newsletter).
3. VESTIGIAL? ~~Add them to the wca-admin@googlegroups.com Google Group: <https://groups.google.com/forum/#!managemembers/wca-admin/add>.~~
4. Add them to the WCA Software Team GitHub team: <https://github.com/orgs/thewca/teams/software-team/members>.
5. (If necessary) Add them to the developers team of the wca Organisation on NPM: <https://www.npmjs.com/settings/wca/teams/team/developers/users>
6. Add them to the WCA slack: <https://worldcubeassociation.slack.com/admin>. Make sure they are in the `#software` channel.
7. Have them send out an introduction email to software@worldcubeassociation.org with a favorite Youtube video and some goals for what they hope to accomplish while on the team.

When someone leaves the software team, they should be removed from all of the above.

# WCA Email Management

We use Google GSuite Groups for our mailing lists. This is way better than free Google Groups, because it gives us an API to automate group membership. It also saves us some hassle setting up redirects. Steps involved in creating a group:

1. Create the group on the GSuite admin panel. See how to [Create a group](https://docs.google.com/document/d/1tFuNMyUcOwsYiGxN8OLG2w41AsLGZpKN-_5GcRGCr2g/edit)
2. Update [SyncMailingListsJob].(https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/app/jobs/sync_mailing_lists_job.rb) if you want membership of the group to be automated. You can see an example of this in [PR 3803](https://github.com/thewca/worldcubeassociation.org/pull/3803).

### Misc documentation

- Relevant code: [SyncMailingListsJob]
- [WCA Suite Rollout Plan](https://docs.google.com/document/d/1dx9DsVwD1RUBmT3dmNahARYRKTCiN0Nq9jGRBJsoyHs/edit?ts=5bf0785b#)
- [Automating GSuite group membership](https://docs.google.com/spreadsheets/d/1I2l_ht2NhcXurG9wezludKNb6ZFjpuWS4jrF_h8DaIw/edit?disco=AAAACUXPzDE&ts=5bef0e17#gid=0)

[SyncMailingListsJob]: https://github.com/thewca/worldcubeassociation.org/blob/master/WcaOnRails/app/jobs/sync_mailing_lists_job.rb

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

