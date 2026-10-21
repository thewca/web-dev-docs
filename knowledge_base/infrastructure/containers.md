---
title: Containerized Services
parent: Infrastructure
layout: default
---

# Images
> Don't manually push images to the ECR Repository, this might cause the Rails Image to get out of sync with the Sidekiq image
> Instead always use the GitHub Workflows which pushes both at the same time
> 
There are two Dockerfiles, one for the main website and one for the Sidekiq Server which differ marginally by dependencies and entrypoint.
Dockerfiles are the same for staging and production, but we still tag them separately so we can test changes on staging.
# Services
## Production
Production is divided into two services, the main Rails Service and an auxiliary service running Sidekiq and PHPMyAdmin.
This is done, so we can scale production while only scaling the Rails Container.
## Staging
Staging only runs a single service containing all 3 containers.
# Deployment
## Production
GitHub Releases are used to build new images for production.
To ensure we don't deploy broken code and a smooth transition we use Blue Green Deployment.
Whenever a new image is pushed to the `latest` tag in our `wca-on-rails` image repository a Pipeline is triggered which starts a new task in our production service.
When the service successfully comes up healthy and stays healthy for 5 minutes, the traffic is diverted to the new tasks and the old one is terminated.
## Staging
Staging is deployed manually and always uses the last image tagged with the `staging` tag in our `wca-on-rails` image repository. To deploy staging you can type `@the-wca-bot deploy staging` into the PR you want to deploy to staging, 