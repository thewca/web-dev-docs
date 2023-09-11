---
title: Managing Deployments
parent: WST Processes
layout: default
---

# Troubleshooting Production

The website is mostly Rails and PHP code, with a layer of nginx in front of them. There's nothing special about our nginx setup, so the internet is your friend if it looks like there's an nginx problem. It's very rare that anyone goes wrong with nginx though.

Rails pages are newer bootstrappy looking pages that have a navbar with sign in and sign out functionality.
PHP pages tend to end in `.php`, and have an older navbar. Lots of Results Team scripts are still running in PHP.

## Debugging PHP

Check relevant error logs:

```
~ @production> tail -f /var/log/nginx/error.log
```

You might get lucky and see an obvious error such as this:

```
2019/01/16 16:40:08 [error] 19491#0: *28988138 connect() to unix:/var/run/php5-fpm.cubing.sock failed (11: Resource temporarily unavailable)
```

To which we can simply restart php5-fpm:

```
~ @production> sudo service php5-fpm restart
php5-fpm stop/waiting
php5-fpm start/running, process 10458
~ @production> 
```

## Debugging Rails

We run our Rails code on Unicorn. Rails logs are at `/home/cubing/worldcubeassociation.org/WcaOnRails/log/production.log`, and Unicorn logs are at `/home/cubing/worldcubeassociation.org/WcaOnRails/log/unicorn-production.log`.

Sometimes Unicorn crashes due to low memory. Unfortunately, we currently don't recover from this situation (we should probably run Unicorn under supervisord, as we do for delayed-job). If this happens, you'll see that there are no Unicorn processes:

```
~ @production> ps -efw | grep -i unicorn
cubing   14779 14546  0 17:56 pts/3    00:00:00 grep -i unicorn
~ @production>
```

You can start Unicorn back up by doing a deploy:

```
~ @production> /home/cubing/worldcubeassociation.org/scripts/deploy.sh restart_app
...
```

New Relic can also be a useful tool for getting some insight into Rails.

# Cost Profile

Current expenses:

- **AWS** *
- **Tarsnap**
- **Google Domains**

The non-AWS expenses are all pretty minor (they all add up to less than $50 per year) expenses.

*We could potentially save some money by getting a reserved instance (instead of on demand), but that does lock us into that particular instance for one or three years. We could also investigate other cloud service providers (but we'd need to find a good alternative to Amazon SES). For example, DigitalOcean might actually be cheaper for our purposes.

## generating cost reports

go to https://console.aws.amazon.com/billing/home?region=us-west-2#/costexplorer
and click Launch Cost Explorer.

On the advanced options > include costs related to uncheck "Credits".

The rest behaves as you would expect 

some example decent configs are 
 - under time range put in some forecast
![](http://i.imgur.com/5q1zDRR.png)
 - grouping costs by instance type
![](http://i.imgur.com/yC1GIPA.png)

# Useful Commands to Know

How to run migrations on production or staging after deploying the latest code:

`~/worldcubeassociation.org/WcaOnRails @production> RAILS_ENV=production bin/rake db:migrate`

## General information

Password for all WCA accounts is "wca".
Birthdate is 1966-04-04.

All WCA ID for test accounts start with "2003FLEI".
With a fresh reset, the first 8 accounts (2003FLEI01 to 2003FLEI08) are board members, others are delegates.

## Secrets
- Production secrets are stored in an encrypted chef [data bag](https://docs.chef.io/data_bags.html) at `chef/data_bags/secrets/production.json`.
  - Show secrets: `knife data bag show secrets production -c /etc/chef/solo.rb --secret-file secrets/my_secret_key`
  - Edit secrets: `knife data bag edit secrets production -c /etc/chef/solo.rb --secret-file secrets/my_secret_key`

**Note:** in order to do this locally, you need to fetch the `secrets/my_secret_key` key from production, as well as fetch and edit the chef configuration file `/etc/chef/solo.rb` from production.

## Finding your way using vagrant

The following sections assumes you are familiar with basic unix shell operations.

### Accessing the VM

When you put the VM up using `vagrant up noregs`, it will start the Rails application in a [screen](https://www.gnu.org/software/screen/) session, making the website available on [http://localhost:2331](http://localhost:2331).

You can ssh to the VM using `vagrant ssh noregs`.

### Accessing the screen session

Once you ssh-ed to the VM, you can resume the screen session using :
`screen -r`

From there you can use `^a "` (Ctrl-A + ") to list the windows in the session. The shell running the Rails application is the one named "run".

### Updating the database

If the last changes you fetched change the db schema, you will have to migrate the application.

For this you have to go to the application's root directory ("WcaOnRails") of the git repository.
It's a shared folder between the host and the VM, and it's mounted on `/vagrant/WcaOnRails` inside the VM.
(You can also just switch to the "dev" window in the screen session, which is shell in this directory)

From there you see what migrations are pending using :

```
RACK_ENV=development DATABASE_URL=mysql2://root:pentagon-pouncing-flared-trusted@localhost/cubing bundle exec rake db:migrate:status
```

And apply them using :

```
RACK_ENV=development DATABASE_URL=mysql2://root:pentagon-pouncing-flared-trusted@localhost/cubing bundle exec rake db:migrate
```


### Restarting the application

This is necessary if you changed the configuration files.
You can just stop the running application in the shell and go back in the history to get the command, for the record here it is :

```
RACK_ENV=development DATABASE_URL=mysql2://root:pentagon-pouncing-flared-trusted@localhost/cubing bundle exec rails server
```

### Running tests and filling the database

Wonder if you changes will pass the travis build ?

Go to the application directory and set up a test database:

```
RACK_ENV=test DATABASE_URL=mysql2://root:pentagon-pouncing-flared-trusted@localhost/cubing_test bundle exec rake db:reset
```

Then go to the application directory and run the testsuite yourself using :

```
RACK_ENV=test DATABASE_URL=mysql2://root:pentagon-pouncing-flared-trusted@localhost/cubing_test bundle exec rspec
```

### Looking up routes

If you modified the application routes, you probably want to check the existing routes and check that you didn't break anything.
From the application directory you can display the current routes using :

```
bundle exec rake routes
```

### Debugging

An easy way to inspect the application state is to use `byebug`.
You can basically put "byebug" somewhere in the code (preferably where you think it breaks :wink: ), and the application will stop on this point and open a `byebug` prompt in the application shell.
If you already know `gdb` the features and usage are similar, please take a look at the [guide](https://github.com/deivid-rodriguez/byebug/blob/master/GUIDE.md) for a detailed overview.

### Clearing Cache

The server uses filestore for caching and we'll need to periodically clear the cache.

Running `df -h` will show 100% usage on `/`. When this happens, we'll  need to run the Rake command:  `RACK_ENV=production bin/rake tmp:cache:clear` 
