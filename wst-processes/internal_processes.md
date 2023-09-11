---
layout: default
parent: WST Processes
title: General
---


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
