---
title: Tips and Tricks
parent: Contributing
layout: default
---

# Accessing someone's PR on your local repo

## Using Github CLI (recommended)
1. Install the Github CLI
2. Checkout the relevant PR using `gh pr checkout {pr-number}`

##  Using vanilla git (not recommended)
1. Add their repo: `git remote add {remote-name} {remote-url}`
_{remote-name} is whatever you want to call the remote repo - eg, "coworker-repo". {remote-url} is the repo link you get under Code > Clone on the page of the repo you're trying to add as remote._
2. Run `git branch -a` - you should now be able to see the branches of their remote repo, in addition to the ones from "origin" (which will be your fork of the WST repo)
3. Run `git checkout -b {fork-name} {remote-name}/{fork-name}`

