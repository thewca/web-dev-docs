---
title: Running Server with Rails
layout: default
parent: Guides
---

{: .help}
> After Monolith Improvements, we should consider whether to deprecate this guide. If we keep it, this page needs some formatting cleanup. 

### Run directly with Ruby (lightweight, but it involves manual installation which happens at your own risk and we won't be able to assist with detailed troubleshooting)

- Ensure you have the correct [Ruby version](./.ruby-version) installed. We recommend using a Ruby version manager like [rvm](https://rvm.io/rvm/install) or [rbenv](https://github.com/rbenv/rbenv). They should both read the `.ruby-version` file to use the correct version (`rvm current` or `rbenv version` to confirm).
- Ensure [Bundler 2](https://bundler.io/v2.0/guides/bundler_2_upgrade.html) is installed
  - To update from bundler 1:
    ```
    gem update --system
    bundle update --bundler
    ```
  - Or, if you haven't installed bundler previously:
    ```
    gem update --system
    gem install bundler
    ```
- Set up git pre-commit hook. Optional, but very useful.
  ```shell
  (cd WcaOnRails; bundle install && bin/yarn && bundle exec overcommit --install)
  ```
  If some changes are made to this hook, you will have to update it running this command from the repository's root directory: `BUNDLE_GEMFILE=WcaOnRails/Gemfile bundle exec overcommit --sign`.
- Install [MySQL 8.0](https://dev.mysql.com/doc/refman/8.0/en/linux-installation.html), and set it up with a user with username "root" with an empty password.
  If it poses problems, try the following:
  ```shell
  # Run MySQL CLI as administrator and set an empty password for the root user:
  sudo mysql -u root
  ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '';
  ```
- Install dependencies and load development database.
  1. `cd WcaOnRails/`
  2. [Install Node.js](https://nodejs.org/en/) and [yarn](https://yarnpkg.com/en/docs/install), we need them for our javascript assets.
  Feel free to take a look at our [chef recipe](https://github.com/thewca/worldcubeassociation.org/blob/master/chef/site-cookbooks/wca/recipes/default.rb#L6-L23)
  for the accurate versions we use and how we install them.
  Please note that other versions may work, but it is not guaranteed.
  3. Install `libyaml` using your package manager of choice, eg. [Mac](https://formulae.brew.sh/formula/libyaml) or [Ubuntu](https://launchpad.net/ubuntu/+source/libyaml)
  4. `bundle install && bin/yarn`
  5. `bin/rake db:load:development` - Download and import the [developer's database export](https://github.com/thewca/worldcubeassociation.org/wiki/Developer-database-export). Depending on your computer it may take a long time. Alternatively you can run `bin/rake db:reset` which will create the database and seed it with random data (it's way faster, but less representative of our website content).
  6. `bin/rails server` - Run rails. The server will be accessible at localhost:3000
- Run tests.
  1. `RAILS_ENV=test bin/rake db:reset` - Set up test database.
  2. `RAILS_ENV=test bin/rake assets:precompile` - Compile some assets needed for tests to run.
  3. `bin/rspec` - Run tests.
- [Mailcatcher](http://mailcatcher.me/) is a good tool for catching emails in development.
