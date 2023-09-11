---
title: Dependabot PR's
layout: default
parent: Wiki Imports
---


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
