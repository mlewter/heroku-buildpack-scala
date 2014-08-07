Heroku buildpack: Scala, Node, Yeoman, Grunt, and Bower
=========================

This is a [Heroku buildpack](http://devcenter.heroku.com/articles/buildpacks) for Play applications that are leveraging the most excellent play-yeoman plug-in:  https://github.com/tuplejump/play-yeoman.

It may come as no surprise that this is the child of two existing Heroku buildpacks:

1.)  https://github.com/heroku/heroku-buildpack-scala.git
2.)  https://github.com/heroku/heroku-buildpack-nodejs.git

...However, in order to support the customized build process of the play-yeoman plug-in, it also installs Yeoman, Grunt, and Bower.  It's not perfect, but it works.

* Please Note:  The v1 of this buildpack assumes that your UI files are contained in the '<play_application_root>/ui' directory.  This is the default structure if you've used the play-yeoman Typesafe activator:  https://typesafe.com/activator/template/play-yeoman
* For the short-term, I recommend forking this if you're going to use it (I plan on moving it to an alternate GitHub account in the near future)

It uses [sbt](https://github.com/harrah/xsbt/) 0.11.0+.

Usage
-----

Example usage:

    $ ls
    Procfile build.sbt project src

    $ heroku create --buildpack https://github.com/mlewter/heroku-buildpack-scala.git

    $ git push heroku master
    ...
    -----> Heroku receiving push
    -----> Scala app detected
    -----> Building app with sbt
    -----> Running: sbt clean compile stage

The buildpack will detect your app as Scala if it has the project/build.properties and either .sbt or .scala based build config.  It vendors a version of sbt and your popluated .ivy/cache into your slug.  The .ivy2 directory will be cached between builds to allow for faster build times.

Hacking
-------

To use this buildpack, fork it on Github.  Push up changes to your fork, then create a test app with `--buildpack <your-github-url>` and push to it.

For example, to reduce your slug size by not including the .ivy2/cache, you could add the following.

    for DIR in $CACHED_DIRS ; do 
    rm -rf $CACHE_DIR/$DIR 
    mkdir -p $CACHE_DIR/$DIR 
    cp -r $DIR/.  $CACHE_DIR/$DIR 
    # The following 2 lines are what you would add
    echo "-----> Dropping ivy cache from the slug" 
    rm -rf $SBT_USER_HOME/.ivy2 
    
Note: You will need to have your build copy the necessary jars to run your application to a place that will remain included with the slug.


Commit and push the changes to your buildpack to your Github fork, then push your sample app to Heroku to test.  You should see:

    ...
    -----> Dropping ivy cache from the slug

License
-------

Licensed under the MIT License. See LICENSE file.
