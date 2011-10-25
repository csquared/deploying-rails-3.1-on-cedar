!SLIDE bullets
# Appendix A: Linux Deploys #

## `foreman export`


!SLIDE commandline incremental
# `foreman export` 
## upstart - event-based init daemon (Ubuntu)

    $ foreman export upstart /etc/init
    [foreman export] writing: /etc/init/testapp.conf
    [foreman export] writing: /etc/init/testapp-web.conf
    [foreman export] writing: /etc/init/testapp-web-1.conf
    [foreman export] writing: /etc/init/testapp-worker.conf
    [foreman export] writing: /etc/init/testapp-worker-1.conf
    [foreman export] writing: /etc/init/testapp-clock.conf
    [foreman export] writing: /etc/init/testapp-clock-1.conf


!SLIDE commandline 
# `foreman export` 

    $ start testapp
    $ stop testapp-clock
    $ restart testapp-worker-1

!SLIDE commandline incremental
#`forman export`
## inittab - sysv-compatible init process

    $ foreman export inittab
    # ----- foreman testapp processes -----
    TE01:4:respawn:/bin/su - testapp -c 
     'PORT=5000 bundle exec thin start -p $PORT >> /var/log/testapp/web-1.log 2>&1'
    TE02:4:respawn:/bin/su - testapp -c 
    'PORT=5100 bundle exec rake resque:work QUEUE=* >> /var/log/testapp/worker-1.log 2>&1'
    TE03:4:respawn:/bin/su - testapp -c 
    'PORT=5200 bundle exec rake resque:scheduler >> /var/log/testapp/clock-1.log 2>&1'
    # ----- end foreman testapp processes -----


.notes We've been working with the Rumbelabs guys to help with getting the assets on a CDN during slug compilation. Right now it supports S3 and Cloudfront. Here's a link to the gem and a devcenter article if you're interested.
!SLIDE bullets
# Appendix B: Asset Sync #

* [gem](https://github.com/rumblelabs/asset_sync)
* uploads to S3/Cloudfront
* [devcenter](http://devcenter.heroku.com/articles/cdn-asset-host-rails31)

!SLIDE
# Questions?
* [devcenter](http://devcenter.heroku.com/articles/cdn-asset-host-rails31)
