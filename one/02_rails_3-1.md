!SLIDE bullets incremental
# Rails 3.1 Asset Pipeline #

* Javascript
* Fonts
* Images
* Stylesheets

!SLIDE bullets incremental
# Asset Compilation #

* Compiling locally.
* Compiling during slug compilation.
* Compiling during runtime.

!SLIDE commandline
# Compiling locally #

    $ RAILS_ENV=production rake assets:precompile
    $ git add public/assets
    $ git commit -m "vendor compiled assets"
    
!SLIDE commandline incremental
# Compiling locally #

    $ git push heroku master
    ...
    -----> Preparing Rails asset pipeline
           Detected manifest.yml, ...

!SLIDE bullets
# Compiling locally #

* js interpreter is not required during runtime
* assets built into each slug

!SLIDE bullets incremental
# Compiling locally (Caveats) #

* terrible dx
* managing assets via git :(
* bloated repo

!SLIDE commandline
# Compiling during slug comp #

    $ git push heroku master
    -----> Preparing Rails asset pipeline
           Running: rake assets:precompile

!SLIDE bullets
# Compiling during slug comp #

* js interpreter is not required during runtime
* assets built into each slug

!SLIDE bullets
# Compiling during slug compilation (Caveats) #

* app's config vars not present
* rake assets:precompile => assets:environment/environment

!SLIDE commandline
# Compiling during runtime #

    $ git push heroku master
    ...
    -----> Preparing Rails asset pipeline
           Running: rake assets:precompile
           ...
           Precompiling assets failed, ...
           Injecting rails31_enable_runtime_a...

!SLIDE bullets incremental
# Compiling during runtime #

* node.js installed on the runtime
* [asset runtime plugin](https://github.com/hone/rails31_enable_runtime_asset_compilation)

!SLIDE bullets
# Compiling during runtime (Caveats) #

* assets compiled an first request => slow

!SLIDE bullets
# JS Interpreters #

* [execjs](https://github.com/sstephenson/execjs)
* therubyracer
* node.js

