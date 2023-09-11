---
title: Deployment Overview
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

1. `ssh cubing@staging.worldcubeassociation.org`
2. `cd worldcubeassociation.org`
2. `git remote add GITUSERNAME https://github.com/GITUSERNAME/worldcubeassociation.org.git`
3. `git fetch GITUSERNAME && git switch GITUSERNAME/BRANCHNAME`
4. `scripts/deploy.sh rebuild_rails`
   - See instructions below for if you're deploying something that isn't just a Rails code change (such as migrations, chef, regulations, etc...)

# How to merge and deploy a PR to prod

If you have access to <https://github.com/thewca/deploy>, you deploy a simple code/content change to production directly from GitHub. Note that this does not run migrations at the moment.

Otherwise, use the following steps:

1. You can click the green merge button, but if you'd rather keep the commit history clean, you can also push via the command line and do a FF if you want.
2. Deploy your code to the production server: `ssh cubing@worldcubeassociation.org worldcubeassociation.org/scripts/deploy.sh pull_latest rebuild_regs rebuild_rails`. (you can get the ssh password over on our [secrets doc](https://docs.google.com/document/d/1WYYUg5RukgCSA7Mt6xfQ1ZCpYzax8ksekcp8PMMBSOg), feel free to run `ssh-copy-id` so you can do passwordless SSH in the future. If this feels unnecessary to you, it is, please check out [issue #989](https://github.com/thewca/worldcubeassociation.org/issues/989)).
  - Note: This command needs to be run twice in the event `deploy.sh` changes. See https://github.com/thewca/worldcubeassociation.org/issues/651.
3. Run any migrations if necessary.
  - To check if there are any pending migrations, run the following on our production server: `~/worldcubeassociation.org/WcaOnRails @production> RAILS_ENV=production bin/rake db:migrate:status`
  - To run all pending migrations, run the following on our production server:  `~/worldcubeassociation.org/WcaOnRails @production> RAILS_ENV=production bin/rake db:migrate`
  - To restart the application after migrations, run the following on our production server:  `~/worldcubeassociation.org @production> ./scripts/deploy.sh restart_app`
4. Run chef if necessary (basically, whenever you change something in the `chef/` directory. If you don't know what this is all about, then you probably don't need to run chef!), with the following command on our production server: `chef-solo --legacy-mode --config "/etc/chef/solo.rb" -o 'role[wca]' -l debug -L /tmp/chef-debug.log`. The `-l` and `-L` options are for debugging, it might be nice to have a log of what happened in case it didn't do what we expected.
5. Reset cache if necessary. Most changes don't require this. `RAILS_ENV=production bin/rake tmp:cache:clear`
6. Depending on the nature of this change, send an email to the relevant parties. Always cc software@ when emailing other teams. **In particular, the Board would like to be notified of all non trivial UI changes.**

# SSH to production

After introducing an IAC, the ssh pattern changed a bit. The first thing you need to do is getting the elastic IP associated with the production instance. You can check [running instances]((https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#Instances:instanceState=running)) and select the prod one. Get the elastic IP associated with the server (this is likely to be a 1 time step), then you can ssh using `ssh cubing@x.x.x.x`, where x.x.x.x is the elastic IP.


# Accessing Database Backups

## Current - AWS Backups

Backups are stored in S3 as versions of the `cubing.sql` file. 

QUICK LINK: https://s3.console.aws.amazon.com/s3/object/wca-backups?region=us-west-2&prefix=latest%2Fwca_db%2Fcubing.sql&tab=versions
1. Navigate to AWS console
2. S3 -> Buckets -> wca-backups -> latest
3. Toggle "Show versions" to see a version history, and download the one you need
4. Bear in mind that you can just grep this file for basic data - you don't always need to load it into a SQL client


## Deprecated - tarsnap backups:

We backup the production database to tarsnap weekly.

The easiest way to get to them is by sshing to the production server and running:
```bash
tarsnap --list-archives | sort
```

>...  
wca-backup-20170529_000922  
wca-backup-20170605_001107  
wca-backup-20170612_000909  
wca-backup-20170619_000838  
wca-backup-20170626_000934  
wca-backup-20170703_001047  
wca-backup-20170710_001104  
wca-backup-20170717_001036  
wca-backup-20170724_001003  
wca-backup-20170731_000945


To get the contents of the database from June 12th, you can run the following command:

```bash
tarsnap -Oxf wca-backup-20170612_000909 secrets/wca_db/cubing.sql > /tmp/cubing.sql
```

Now that you have this file the safest is probably to use it locally (do not import it onto staging, or else the whole world will be able to access it via phpMyAdmin on staging!).

Before moving the file to another machine it's best to tar it so that it shrinks to a reasonable size:
```bash
/tmp @production> cd /tmp
/tmp @production> tar -czvf cubing.sql.tar.gz cubing.sql
```

Over on your laptop now, you can scp, extract the file and import it into a newly created database:

```bash
/tmp @yourlaptop> scp worldcubeassociation.org:/tmp/cubing.sql.tar.gz .
cubing.sql.tar.gz                                                       100%  140MB  28.0MB/s   00:05
/tmp @yourlaptop> tar xvf cubing.sql.tar.gz
cubing.sql
/tmp @yourlaptop> mysql
mysql> create database wca_backup_20170612_000909;
Query OK, 1 row affected (0.01 sec)

mysql> use wca_backup_20170612_000909;
Database changed
mysql> source cubing.sql;
```

# Operating the Website

Here you can find the most common operations necessary to keep the website running.

## Getting into the server

You can connect to the WCA server using an SSH client, which establishes
a terminal session and lets you run any commands on the remote machine.
If you're a WST member on the GitHub organization then all the SSH keys
on your GitHub automatically propagate to the WCA server, so that you can
easily access the server.

Assuming a Unix-like it is as simple as:

```shell
ssh cubing@worldcubeassociation.org
```

As a result you should the production prompt and be able to run any commands.

```shell
~ @production>
```

### Staging

We have another server with production-like setup, but used only for testing.
You can establish the connection in the same way, just use the staging address instead.

```shell
ssh cubing@staging.worldcubeassociation.org
```

⚠️ **If you want to try out the commands/tasks described in this document (or any other) please
use the staging server for that, not the production one. You can easily distinguish between
these two by looking at the command line prompt (`~ @staging> ` vs `~ @production> `).**

## Checking the state of the app

The website is essentially a system process responsible for handling incoming requests.
To see if the process(es) are running you can run the following command (list all processes then filter the relevant ones):

```shell
~ @staging> ps aux | grep unicorn
cubing    9115  0.0  4.2 531308 170704 ?       Sl   Dec06   0:07 unicorn master -D -c config/unicorn.rb
cubing    9161  0.0  4.5 599932 182536 ?       Sl   Dec06   0:24 unicorn worker[1] -D -c config/unicorn.rb
cubing    9165  0.0  4.5 599932 182368 ?       Sl   Dec06   0:24 unicorn worker[2] -D -c config/unicorn.rb
cubing    9169  0.0  6.6 666520 268332 ?       Sl   Dec06   0:24 unicorn worker[3] -D -c config/unicorn.rb
cubing   24181  0.0  4.2 599932 172732 ?       Sl   15:07   0:00 unicorn worker[0] -D -c config/unicorn.rb
cubing   24607  0.0  0.0  10472   900 pts/4    S+   15:18   0:00 grep unicorn
```

The second column is the process identifier (useful when you want to manage the process, e.g. terminate it),
whereas the last column is the actual command behind the process.

As you can see there are several processes, four workers and one master process supervising them
(so if any of them goes down, a new process is started).

To terminate the app you can use the `kill` command providing the process identifies (PID) of the master process.
In this case:

```shell
~ @staging> kill 9115
```

Now you can verify that the processes are no longer there:

```shell
~ @staging> ps aux | grep unicorn
cubing   24633  0.0  0.0  10472   900 pts/4    S+   15:19   0:00 grep unicorn
```

Usually that's what you see when the website is down.

## (Re)starting the website

❗ *That's the most common way of fixing "the website is down" issues.*

To start the app (or restart if already running) you can run the deploy script like so:

```shell
~ @staging> worldcubeassociation.org/scripts/deploy.sh restart_app
```

Once this finishes you can verify it's up again:

```shell
~ @staging> ps aux | grep unicorn
cubing   24838 30.6  4.2 530784 170600 ?       Sl   15:30   0:06 unicorn master -D -c config/unicorn.rb
cubing   24848  0.0  4.0 530784 164816 ?       Sl   15:30   0:00 unicorn worker[0] -D -c config/unicorn.rb
cubing   24852  0.0  4.0 530784 164788 ?       Sl   15:30   0:00 unicorn worker[1] -D -c config/unicorn.rb
cubing   24856  0.0  4.0 530784 164888 ?       Sl   15:30   0:00 unicorn worker[2] -D -c config/unicorn.rb
cubing   24860  0.0  4.0 530784 164812 ?       Sl   15:30   0:00 unicorn worker[3] -D -c config/unicorn.rb
cubing   24869  0.0  0.0  10472   900 pts/4    S+   15:31   0:00 grep unicorn
```

## Deploying the website

You can find more details on this subject under [[Merging and deploying|Merging and deploying]].

### Code changes

In most cases deploying new code changes can be done with this command:

```shell
~ @staging> worldcubeassociation.org/scripts/deploy.sh pull_latest rebuild_rails
```

### Updating regulations

To deploy regulation changes (usually necessary when regulation translations are updated), run the following:

```shell
~ @staging> worldcubeassociation.org/scripts/deploy.sh pull_latest rebuild_regs
```

## Talking to the database

You can connect to the MySQL database shell simply by running:

```shell
~ @staging> mysql cubing
```

Now you should see a new CLI prompt and be able to run SQL queries:

```shell
mysql> SELECT COUNT(*) FROM Competitions;
+----------+
| COUNT(*) |
+----------+
|     7202 |
+----------+
1 row in set (0.04 sec)
```

⚠️ **Feel free to play around with the database on the staging server,
however on the production server please use it only when necessary.
Even running some `SELECT` queries may hurt the application performance
if those queries are more complex.**

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

## Looking into the logs

There are several log files you may look into:

* `worldcubeassociation.org/WcaOnRails/log/production.log` - that's a very detailed log of the Rails application,
  it includes SQL queries and details about requests that errored
* `/var/log/nginx/access.log` - this file includes one line per every incoming request,
  so it's sometimes useful for monitoring the current traffic

### Live logs

You may see all new logs printed to the screen as soon as they come by running the following:

```shell
~ @staging> tail -f worldcubeassociation.org/WcaOnRails/log/production.log
```

However, the traffic on the production server is usually high enough, that looking
into the logs this way is not really helpful. You may filter the results by looking
for some keywords:

```shell
~ @staging> tail -f worldcubeassociation.org/WcaOnRails/log/production.log | grep "/competitions"
I, [2020-12-07T16:18:45.271943 #24860]  INFO -- : [44ccfa09-3751-4dc7-b35b-144b248aacd2] Started GET "/competitions" for 159.205.203.185 at 2020-12-07 16:18:45 +0000
```

You may include some context by using something like `grep -C 100` and do all kinds of text filtering.
The above is just a simple example of where you may look for information about the running app.


# Spinning up a new server

Spinning up a new server is now handled by terraform in the wca-iac repo.

## Changing our Google API KEY

You should go to the [developer console](https://console.developers.google.com/apis/credentials?project=wca-website&pli=1) (login with the wca.software google account which is in our credentials document), select the "WCA production key", and add the new server's IP (you should also remove the old server's IP).
