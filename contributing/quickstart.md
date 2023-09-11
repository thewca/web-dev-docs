---
title: Quickstart
parent: Contributing
layout: default
nav_order: 1
---

{: .info}
> This assumes you are an experienced developer with the necessary dependencies - refer to [Detailed Setup Guide](./detailed_setup_guide) if you need more information.

There are 3 steps to run the WCA website locally with docker[^1]:

## (1) GET THE SERVER RUNNING
1. Clone and navigate to the repo
2. Run using docker[^2] with `docker-compose up` (or `docker compose up`, depending on your docker compose config)

Now the website will run locally. You have some options on how to set up the database and run tests.

## (2) DATABASE SETUP
There are two options here - a "test" database with generated data, or the production database (with sensitive information removed).
1. **Test database**: Quick to set up, but may not mirror real-world data well. `bin/rake db:reset`
2. **Exported database**: Takes 60-90 minutes to set up, but uses actual data from the live website. `bin/rake db:load:development`

## (3) RUNNING TESTS
Two options - running while your docker server is running, or running tests independently. 

### **RUNNING INDEPENDENTLY**
```
docker-compose exec wca_on_rails bash -c "RAILS_ENV=test bin/rake db:reset && RAILS_ENV=test bin/rake assets:precompile && bin/rspec"
```

### **RUNNING WHILE SERVER IS LIVE**
With your docker server running:
1. Connect to the `wca_on_rails` container using `docker exec -it {container_id} bash`
    1. _Use `docker ps` to list containers and their id's_
2. Run `bin/rspec` from inside the container's terminal

----

[^1]: The website can also be run with `rails server` - see [Running with Rails](/guides/running_with_rails)
[^2]: If you're using Visual Studio Code to develop, you can [attach it to the Docker container](https://code.visualstudio.com/docs/remote/containers) so that your extensions can take advantage of the Ruby environment, and so the terminal runs from inside the container
