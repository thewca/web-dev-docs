---
title: State of the Website
layout: default
parent: Wiki Imports
---


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
