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
