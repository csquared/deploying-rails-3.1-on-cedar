.notes The asset pipeline supports various types of assets like Javascript (including Coffesescript), CSS, and anything else you want like fonts or images. There's a new folder structure with app/assets, lib/assets, and vendor/assets.
!SLIDE bullets
# Rails 3.1 Asset Pipeline #

* Javascript
* Stylesheets
* Fonts
* Images

.notes There are three ways to get your assets onto heroku. You can compile them locally. Heroku can compile them during the slug compilation. Or you can have them compiled during runtime. Let's step through each one.
!SLIDE bullets incremental
# Asset Compilation #

* Compiling locally.
* Compiling during slug compilation.
* Compiling during runtime.

.notes To compile locally, we need to run the rake task supplied by rails, assets:precompile. This runs sprockets and generates all the assets in a public/assets folder as well as a manifest.yml file which by default in put inside public/assets. We want to make sure to run this in the production environment. Next, we need to add the assets to the git repository. Make sure to use -f, since public/assets is in .gitignore. Then finally, we commit these assets.
!SLIDE commandline incremental
# Compiling locally #

    $ RAILS_ENV=production rake assets:precompile
    $ git add -f public/assets
    $ git commit -m "vendor compiled assets"
    
.notes We can now push to heroku with the assets added. You should see from the push output that heroku detects the manifest.yml generated by the assets:precompile task. When this happens, Heroku will assume that you're managing your own assets and not do anything special.
!SLIDE commandline incremental
# Compiling locally #

    $ git push heroku master
    ...
    -----> Preparing Rails asset pipeline
           Detected manifest.yml, ...

.notes The benefits of doing this is that it always works and you have direct control over your assets. Since the assets are precompiled, you don't need a javascript interpreter on the runtime production boxes. The assets are built into each slug so during runtime they're already available and don't have to be copied manually to each dyno.
!SLIDE bullets
# Compiling locally #

* direct control over assets
* js interpreter is not required during runtime
* assets built into each slug

.notes The major downsides of doing this is the terrible developer experience that comes with it. You have to manually clean and add your assets each time they change. Rails provides a rake task "assets:clean" to do this or you can use git rm -r public/assets. Managing your assets via git isn't fun and a major pain. This will seriously bloat your repo depending on how many assets you have.
!SLIDE bullets incremental
# Compiling locally (Caveats) #

* terrible dx
* managing assets via git :(
* bloated repo

.notes To get Heroku to compile your assets, you just need to not have the manifest.yml file checked into your git repo. Heroku will then run the rake task assets:precompile.
!SLIDE commandline incremental
# Compiling during slug comp #

    $ git push heroku master
    -----> Preparing Rails asset pipeline
           Running: rake assets:precompile

.notes This has similar benefits as handling your assets locally, except you don't have to manage your assets at all and Heroku will handle it.
!SLIDE bullets
# Compiling during slug comp #

* js interpreter is not required during runtime
* assets built into each slug
* don't have to manage your assets

.notes There are some huge caveats with this. Heroku at this time doesn't pass the config vars in cedar to the build environment like it does in cedar. This causes issues because rake assets:precompile depends on assets:environment in Rails 3.1.1 and environment in Rails 3.1.0. There are many things that can cause the environment to fail to load like connecting to external resources. It's a bit better in Rails 3.1.1 because rails now doesn't load initializers during this rake task and railtie initializers have to opt in using :group => :assets.
!SLIDE bullets
# Compiling during slug compilation (Caveats) #

* app's config vars not present
* rake assets:precompile => assets:environment/environment

.notes If the rake task fails to run cleanly when we try to compile, then we will inject a plugin to compile the assets during runtime. This just means flipping a boolean bit. When this happens, you'll see the plugin being installed
!SLIDE commandline incremental
# Compiling during runtime #

    $ git push heroku master
    ...
    -----> Preparing Rails asset pipeline
           Running: rake assets:precompile
           ...
           Precompiling assets failed, ...
           Injecting rails31_enable_runtime_a...

.notes The benefits to this, is that it works despite not being able to compile the assets during compile time.
!SLIDE bullets incremental
# Compiling during runtime #

* [asset runtime plugin](https://github.com/hone/rails31_enable_runtime_asset_compilation)

.notes Since we're compiling the assets during runtime, we vendor node.js into the slug so we can compile your assets. The big downside here is that your assets get compiled at the first request. Depending on how long this takes it can cause a timeout on the first request.
!SLIDE bullets
# Compiling during runtime (Caveats) #

* node.js installed on the runtime
* assets compiled an first request => slow

.notes I just wanted to make a note on Javascript interpreters. Rails 3.1 uses the execjs gem to detect the javascript runtime to use. pLEase don't use therubyracer on heroku. It uses up a lot of memory and can cause your dyno to hit memory limits. We install node.js into every slug regardless if it's needed above. Please use the node.js binary instead.
!SLIDE bullets
# JS Interpreters #

* [execjs](https://github.com/sstephenson/execjs)
* therubyracer
* node.js

