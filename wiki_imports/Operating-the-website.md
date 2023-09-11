---
title: Operating the Website
layout: default
parent: Wiki Imports
---



Here you can find the most common operations necessary to keep the website running. This is relevant to WST members only.

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
