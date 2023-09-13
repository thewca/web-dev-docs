---
title: Detailed Contributing Guide
parent: Contributing
layout: default
---

{: .help}
> We could use contributions to add content/clarity to all h1's defined in this article.

# (1) Running the website on your machine

This is an essential step of contributing to the WCA website, as you'll want to see the effect of the changes you make to the code.

## 1. Clone this repo! (And navigate into it)
1. `git clone https://github.com/thewca/worldcubeassociation.org`
2. `cd worldcubeassociation.org`

## 2. Run using Docker

1. Install [Docker](https://docs.docker.com/get-docker/) (remember to complete the [Linux post-install steps](https://docs.docker.com/engine/install/linux-postinstall/) if you're on Linux)
2. Install docker-compose. The best way to get an up-to-date version is to get it from their [releases page](https://github.com/docker/compose/releases)
3. Navigate into the repository's main directory (`worldcubeassociation.org`)
4. To start the server at `http://localhost:3000`, run `docker-compose up` (or `docker compose up`, depending on your install). To bring it down, `docker-compose down` (or just press ctrl + c in the same terminal)
5. To run tests, run `docker-compose exec wca_on_rails bash -c "RAILS_ENV=test bin/rake db:reset && RAILS_ENV=test bin/rake assets:precompile && bin/rspec"`
6. If you're using Visual Studio Code to develop, you can [attach it to the Docker container](https://code.visualstudio.com/docs/remote/containers) so that your extensions can take advantage of the Ruby environment and so the terminal runs from inside the container

## 3. Set up the database

TODO

# (2) Find an issue you'd like to contribute to

You can look at our [good first issues](), find one that interests you, and add a comment saying that you'd like to work on the issue. 

# (3) Create a new branch

# (4) Commit and push your changes

# (5) When you're ready, open a PR (or start with a draft)
