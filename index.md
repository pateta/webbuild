# webbuild #

webbuild is a docker container that runs your frontend build tasks. It
was designed to have them running in a clean predefined environment yet
being customizable

The container can be used using briefbote/webbuild

## Inside ##

There are three flavors of this container:

* PHP 5.5
* PHP 5.6
* PHP 7.0

You can use specific php versions by using them as a tag:

`docker run briefbote/webbuild:55` for PHP5.5 for example.

The `latest` tag points to the PHP7.0 version

Build expects `/src` containing the source code and `/app` containing the release after all build steps have run.

Git is installed within the container.

The package build-essential is installed within the container.

### RUBY ###

RUBY builds are now deprecated. Plase inherit from the image and install Ruby in a prebuild.sh.

## Execution ##

NODEJS will be installed using nvm. It is respecting .nvmrc. After that, grunt, gulp and bower are installed

### Customization

#### Not required build tools

You may want to turn off build tools. To do this you need to create a .webbuild/variables.sh file in the base directory. There you can set the following variables:

|Variable|Build tool|Value to turn off|
|---|---|---|
|RUNULP|gulp|!= 1|
|RUNGRUNT|grunt|!= 1|
|RUNBOWER|bower|!= 1|
|RUNCOMPOSER|composer|!= 1|

Earlier versions allowed disabling by passing environment variables. Those environment variables are deprecated and will be disabled in the future.

|Variable|Build tool|
|---|---|
|NOGULP|gulp|
|NOGRUNT|grunt|
|NOBOWER|bower|
|NOCOMPOSER|composer|

Setting such a variable will disable installation ( except of composer which is always installed ) and execution.

Doing this will speed up the build process.

## ONBUILD ##

There are two ONBUILD trigger. They copy `prebuild.sh` respectively `postbuild.sh` into the newly image. Therefore 
the following Dockerfile will allow you add standard customizations according to your needs:

    FROM briefbote/webbuild:latest
    MAINTAINER Test <donotreply@test.de>

## BASE directory ##

The base directory ( the directory where the entrypoint runs the build steps ) is `/src` except when
`/src/src` exists

### Custom base directory

If your base directory follows not the convention use the environment variable `SETBASEDIR` to change it.

## Build steps ##

1. Loading `$BASEDIR/.webbuild/variables.sh` if it exists
2. `nvm install 4` or `nvm install` in .webbuild/ is .nvmrc exists there
3. `/exec/prebuild.sh` if it exists
4. `$BASEDIR/.webbuild/prebuild.sh` if it exists
5. `npm install` if `$BASE/package.json` exists
6. `bower` if `$BASE/bower.json` exists
7. `composer` without dev dependencies if `$BASE/composer.json` exists
8. `grunt` if `$BASE/Gruntfile` exists
9. `gulp` if `$BASE/gulpfile.js` exists
10. `$BASE/.webbuild/custom.sh` if it exists, fallback `$BASE/custom.sh` (deprecated)
11. `$BASEDIR/.webbuild/post4. `$BASEDIR/.webbuild/prebuild.sh if it exists`build.sh` if it exists
12. `/exec/postbuild.sh` if it exists
13. Use `/src/build` as `/app` if `/app` is empty
14. Use `/src/release` as `/app` if `/app` is empty

Currently a `$BASE/.nvmrc` is still respected.

## Return codes ##

Non zero exit codes will be returned when one of the build steps are failing or `/app` is empty after step 10

## Usage ##

Assume you want to build a web application in the current directory containing the sources within the `src` subfolder
and you want to place the build in the `app` subfolder.

Then you can run the build like this:

    docker run -t briefbote/webbuild:latest -v $PWD/src:/src -v $PWD/app:/app

### Samples

#### simple

A very simplistic sample of it is in the `samples/simple` subfolder of this repository (note that `test/app/` is in the `.gitignore` file).

To run the sample change to the test directory and run `run.sh`. It (re)creates the app subfolder ands starts a build
that actually does nothing more then to copy the `index.html` using a custom.sh into the `/app` folder

## Code ##

Code is open source under the Apache 2.0 License. You can obtain it at https://github.com/sascha-andres/webbuild

If you want to contribute feel free to open an issue

## on hub.docker.com ##

https://hub.docker.com/r/briefbote/webbuild/

## Help

Feel free to contact me by creating an issue on https://github.com/sascha-andres/webbuild/issues.
You can connect to me using Twitter at https://twitter.com/livingit_de.

## History ##

History is now edited on GitHub WIKI: https://github.com/sascha-andres/webbuild/wiki/History