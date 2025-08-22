---
title: Managing Deployments
parent: WST Processes
layout: default
---

# SSH to production

This process will automatically connect to the running production container. 
Make sure you have the AWS CLI and the Session Manager Plugin installed

```shell
aws ecs execute-command --cluster wca-on-rails --task $(aws ecs list-tasks --cluster wca-on-rails --service-name wca-on-rails-prod --query 'taskArns[*]' --output text) --container rails-production --interactive --command '/bin/bash'
```

# Troubleshooting Production

The website is mostly Rails and Javascript.

Rails pages are newer bootstrappy looking pages that have a navbar with sign in and sign out functionality.

## Debugging Rails

We run our Rails code on Unicorn. Rails logs are in Cloudwatch as part of the `wca-on-rails` cluster, the `wca-on-rails-prod` service and `rails-production` container

[New Relic](https://one.newrelic.com/nr1-core/apm/overview/MTA2ODk1NHxBUE18QVBQTElDQVRJT058MTAzODUxNjM?account=1068954&duration=1800000&state=5b1b3f6a-f807-c41e-09cf-3c258213c127) can also be a useful tool for getting some insight into Rails - particularly for searching logs.

# Generating cost reports

Go to https://console.aws.amazon.com/billing/home?region=us-west-2#/costexplorer
and click Launch Cost Explorer.

On the advanced options > include costs related to uncheck "Credits".

The rest behaves as you would expect 

some example decent configs are 
 - under time range put in some forecast
![](/assets/images/cost_forecast.png)
 - grouping costs by instance type
![](/assets/images/group_by_instance_type.png)

# Useful Commands to Know

# General information

## Secrets
Secrets are stored in Hashicorp Vault. Our ECS tasks authenticate to Vault using their IAM Task Roles. 
These roles are mapped to a specific set of permissions.
You can read more about our Vault setup in the [Google Doc](https://docs.google.com/document/d/1ZszGawG70oZaTrXu5-gKxfS8HwwKG_0U9b-CQjKQ6RE/edit#heading=h.t6tsp7w7uomr)

# Operating the Website

Here you can find the most common operations necessary to keep the website running.

## Staging

We have another container with production-like setup, but used only for testing.
You can establish the connection in the same way, just use the staging service instead.

```shell
aws ecs execute-command --cluster wca-on-rails --task $(aws ecs list-tasks --cluster wca-on-rails --service-name wca-on-rails-staging --query 'taskArns[*]' --output text | awk -F/ '{print $NF}') --container rails-staging --interactive --command "/bin/bash"
```

## Checking the state of the app

We use health checks that automatically restart the server when it can't serve requests for a couple minutes.
You can check the metrics in the [health](https://us-west-2.console.aws.amazon.com/ecs/v2/clusters/wca-on-rails/services/wca-on-rails-prod/health?region=us-west-2) section of ecs

## (Re)starting the website

❗ *That's the most common way of fixing "the website is down" issues.*

The Serer is automatically restarted when the website is down

## Talking to the database
We use phpmyadmin to talk to the database. If you have access it, you can find [the link](https://www.worldcubeassociation.org/results/database/) for it in the main menu under your avatar. 
You can find the username and generate a password in the [admin panel](https://www.worldcubeassociation.org/admin/generate_db_token)

### Looking at currently running queries

If the website feels overly slow as a whole, it may be cased by some expensive
queries taking a long time to execute. You can see the currently running queries like so:

```shell
mysql> show processlist;
+-------+-----------------+---------------------+--------+---------+---------+------------------------+----------------------------------------------------------------+
| Id    | User            | Host                | db     | Command | Time    | State                  | Info                                                           |
+-------+-----------------+---------------------+--------+---------+---------+------------------------+----------------------------------------------------------------+
|     4 | event_scheduler | localhost           | NULL   | Daemon  | 2892897 | Waiting on empty queue | NULL                                                           |
|  7423 | rdsadmin        | localhost:9799      | NULL   | Sleep   |      15 |                        | NULL                                                           |
| 30384 | root            | 172.31.26.116:57371 | cubing | Sleep   |       1 |                        | NULL                                                           |
| 30385 | root            | 172.31.26.116:57373 | cubing | Sleep   |       4 |                        | NULL                                                           |
| 30389 | root            | 172.31.26.116:57386 | cubing | Sleep   |      16 |                        | NULL                                                           |
| 30390 | root            | 172.31.26.116:57388 | cubing | Sleep   |      45 |                        | NULL                                                           |
| 31174 | root            | 172.31.26.116:57968 | cubing | Sleep   |      66 |                        | NULL                                                           |
| 31176 | root            | 172.31.26.116:57972 | cubing | Query   |       0 | starting               | show processlist                                               |
| 31177 | root            | 172.31.26.116:57973 | cubing | Sleep   |     248 |                        | NULL                                                           |
| 31179 | root            | 172.31.26.116:57974 | cubing | Sleep   |       6 |                        | NULL                                                           |
| 31182 | root            | 172.31.26.116:57976 | cubing | Query   |     111 | executing              | SELECT BENCHMARK(1000000000, MD5('example complex operation')) |
+-------+-----------------+---------------------+--------+---------+---------+------------------------+----------------------------------------------------------------+
11 rows in set (0.01 sec)
```

The time is shown in seconds. Above you can see an example of a query in the *executing* state
that is taking `111 s` already. You can see the actual query in the last column to
verify if it looks suspicious.

A long-taking query usually implies some performance issues (e.g. a heavily accessed page that
runs a complex query with no proper indexing), so it's best to investigate where the query came
from and how can the performance be improved. If the database struggles because of many complex
queries, then you may help it by killing some of them using their identifier:

```shell
mysql> kill 31182;
Query OK, 0 rows affected (0.00 sec)
```

Now you can see the query is gone:

```shell
mysql> show processlist;
+-------+-----------------+---------------------+--------+---------+---------+------------------------+------------------+
| Id    | User            | Host                | db     | Command | Time    | State                  | Info             |
+-------+-----------------+---------------------+--------+---------+---------+------------------------+------------------+
|     4 | event_scheduler | localhost           | NULL   | Daemon  | 2892925 | Waiting on empty queue | NULL             |
|  7423 | rdsadmin        | localhost:9799      | NULL   | Sleep   |      13 |                        | NULL             |
| 30384 | root            | 172.31.26.116:57371 | cubing | Sleep   |       4 |                        | NULL             |
| 30385 | root            | 172.31.26.116:57373 | cubing | Sleep   |       2 |                        | NULL             |
| 30389 | root            | 172.31.26.116:57386 | cubing | Sleep   |      44 |                        | NULL             |
| 30390 | root            | 172.31.26.116:57388 | cubing | Sleep   |      13 |                        | NULL             |
| 31174 | root            | 172.31.26.116:57968 | cubing | Sleep   |      94 |                        | NULL             |
| 31176 | root            | 172.31.26.116:57972 | cubing | Query   |       0 | starting               | show processlist |
| 31177 | root            | 172.31.26.116:57973 | cubing | Sleep   |     276 |                        | NULL             |
| 31179 | root            | 172.31.26.116:57974 | cubing | Sleep   |      34 |                        | NULL             |
+-------+-----------------+---------------------+--------+---------+---------+------------------------+------------------+
10 rows in set (0.00 sec)
```

You can also check what the longest running queries are in the RDS Console.

## Looking into the logs

{: .help}
> This can be done via NewRelic now.

There are several log files you may look into:

* New Relic
* Cloudwatch

# Staging

We have another server with production-like setup, but used only for testing.
You can establish through by connecting to the staging instance listed in our EC2 instances.

## Updating the Staging Database

This is done using a similar process to getting the developer database for local development. 

1. Connect to the staging server
2. Make sure you're the `cubing` user: `sudo su cubing` 
3. Switch to the WcaOnRails folder: `cd worldcubeassociation/WcaOnRails`
4. Load the developer database: `RAILS_ENV=production bin/rake db:load:development`
    1. NOTE: This takes a very long time (~60 minutes) to execute.
5. Run a database migration: `RAILS_ENV=production bin/rake db:migrate`
