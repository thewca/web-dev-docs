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

1. `Comment @thewca-bot deploy staging on the PR`

# How to merge and deploy a PR to prod

The deployment pipeline is automatically triggered on github releases alternative you can follow these steps.

1. `Click the Button at https://github.com/thewca/worldcubeassociation.org/actions/workflows/deploy-on-release.yml`

# Deploying Regulations

Regulations are automatically deployed on push to the regulations repos


