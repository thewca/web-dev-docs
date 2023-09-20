---
title: Merging and Deploying
parent: WST Processes
layout: default
---


# Policy for merging code

Everyone (even the software team leader =)) must send in a PR for every change they make. If the PR addresses a critical issue or [is just updating a translation](https://github.com/thewca/worldcubeassociation.org/pull/1144#issuecomment-275124727), then you can merge it up immediately, but otherwise, you should wait for someone to comment with a LGTM before merging up your own PR.

If you're not getting a LGTM, but you have responded to all the comments on your PR, please use your best judgement how to proceed. Two reasonable ways to handle this:

1. If I know these changes are controversial, or I'm not 100% in love with the way I implemented something, I'll hound people to look at my code and give me comments.
2. If you're confident these changes are innocuous and you're happy with your implementation, pick a reasonable date in the future, and add a comment to your PR telling people you plan to merge your code on that date unless someone speaks up.

The only time you should merge up someone else's PR is if it's a PR from someone who is not a member of the WCA Software Team.

**Do not merge up your code until you're ready to deploy it.**

**If your code changes documented API in a backwards incompatible way (such as our [public database export](https://www.worldcubeassociation.org/results/misc/export.html)), email software-public@worldcubeassociation.org with a description of the changes.**

**If your code contains a migration, please run it on staging before running it on production.**

**PRs that have gone 3 months without activity are subject to being closed.**


# How to deploy a branch to staging

1. `ssh cubing@staging.worldcubeassociation.org`
2. `cd worldcubeassociation.org`
2. `git remote add GITUSERNAME https://github.com/GITUSERNAME/worldcubeassociation.org.git`
3. `git fetch GITUSERNAME && git switch GITUSERNAME/BRANCHNAME`
4. `scripts/deploy.sh rebuild_rails`
   - See instructions below for if you're deploying something that isn't just a Rails code change (such as migrations, chef, regulations, etc...)

# How to merge and deploy a PR to prod

If you have access to <https://github.com/thewca/deploy>, you deploy a simple code/content change to production directly from GitHub. Note that this does not run migrations at the moment.

Otherwise, use the following steps:

1. You can click the green merge button, but if you'd rather keep the commit history clean, you can also push via the command line and do a FF if you want.
2. Deploy your code to the production server: `ssh cubing@worldcubeassociation.org worldcubeassociation.org/scripts/deploy.sh pull_latest rebuild_regs rebuild_rails`. (you can get the ssh password over on our [secrets doc](https://docs.google.com/document/d/1WYYUg5RukgCSA7Mt6xfQ1ZCpYzax8ksekcp8PMMBSOg), feel free to run `ssh-copy-id` so you can do passwordless SSH in the future. If this feels unnecessary to you, it is, please check out [issue #989](https://github.com/thewca/worldcubeassociation.org/issues/989)).
  - Note: This command needs to be run twice in the event `deploy.sh` changes. See https://github.com/thewca/worldcubeassociation.org/issues/651.
3. Run any migrations if necessary.
  - To check if there are any pending migrations, run the following on our production server: `RAILS_ENV=production bin/rake db:migrate:status`
  - To run all pending migrations, run the following on our production server:  `RAILS_ENV=production bin/rake db:migrate`
  - To restart the application after migrations, run the following on our production server:  `~/worldcubeassociation.org @production> ./scripts/deploy.sh restart_app`
4. Run chef if necessary (basically, whenever you change something in the `chef/` directory. If you don't know what this is all about, then you probably don't need to run chef!), with the following command on our production server: `chef-solo --legacy-mode --config "/etc/chef/solo.rb" -o 'role[wca]' -l debug -L /tmp/chef-debug.log`. The `-l` and `-L` options are for debugging, it might be nice to have a log of what happened in case it didn't do what we expected.
5. Reset cache if necessary. Most changes don't require this. `RAILS_ENV=production bin/rake tmp:cache:clear`
6. Depending on the nature of this change, send an email to the relevant parties. Always cc software@ when emailing other teams. **In particular, the Board would like to be notified of all non trivial UI changes.**

# Deploying Regulations

To deploy regulation changes (usually necessary when regulation translations are updated), run the following:

```shell
~ @staging> worldcubeassociation.org/scripts/deploy.sh pull_latest rebuild_regs
```


