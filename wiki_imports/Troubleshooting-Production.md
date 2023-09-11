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