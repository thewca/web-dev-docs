---
title: State of the Website
layout: default
parent: Contributing
---

# Where Are We Now

## Our software

- [New Rails codebase](https://github.com/thewca/worldcubeassociation.org/tree/master/WcaOnRails)
  - User account system (with OAuth!)
  - [`/results/admin/`](https://www.worldcubeassociation.org/results/admin/) - Results administration panels. Access is enforced by Nginx through our [Rails api](https://github.com/thewca/worldcubeassociation.org/blob/7ad2cc53a14e0da10a39f215aabecc98c5bcd85a/chef/site-cookbooks/wca/templates/worldcubeassociation.org.conf.erb#L53).
    - [`/results/admin/phpMyAdmin/`](https://www.worldcubeassociation.org/results/admin/phpMyAdmin/)
  - [`/results/`](https://www.worldcubeassociation.org/results/)
- [`/regulations/`](https://www.worldcubeassociation.org/regulations/) - [Regulations](https://www.worldcubeassociation.org/regulations/)

[This milestone](https://github.com/thewca/worldcubeassociation.org/issues?q=is%3Aopen+is%3Aissue+milestone%3A%22Drop+PHP%22) tracks our progress in porting our PHP code to Ruby on Rails.



# What We're Working Towards

# Why Rails?


Great discussion on reddit:
https://www.reddit.com/r/rails/comments/aho9g0/hoping_to_view_rails_magic_through_a_different/
https://medium.com/swlh/the-ups-and-downs-of-rails-magic-5a88c7f68064
https://rubyonrails.org/doctrine


Once you know rails, your fluency in the code goes up MASSIVELY. But, it is a barrier to contributing. 

