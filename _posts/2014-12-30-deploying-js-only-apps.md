---
layout: post
title: ! 'Deploying JavaScript Only Apps with Flightplan'
excerpt: Have an front-end JavaScript only application that you need to deploy? Check out Flightplan.
categories:
- Programming
tags:
- javascript
- deployment
status: publish
type: post
published: true
---
We have an application that is purely JavaScript (AngularJS), HTML and CSS that I was trying to figure out how to easily
deploy. This application has cleanly separated JavaScript modules and uses SASS, so we have a nice little build system
using Gulp that compiles, combines, and exports all the code from a `src/` to a `dist/` folder. 

What I didn't want to do is deploy the entire codebase to a server and, using all the Node dependencies there, build
the application and point the webserver at a `dist/` folder. What I simply wanted to do was take a copy of the built
`dist/` folder and deploy only that to the server.

This makes my only dependency on that server Nginx, which serves up nice, hot, cached HTML, CSS and JavaScript.

This left out Capistrano, whose strong suit (and maybe only suit) involved deploying from SCM. I then found
[Flightplan](https://github.com/pstadler/flightplan), which provides a mechanism for running system commands locally
or remotely. It seemed perfect.

## Setting up Deployment

In `flightplan.js` in the root of the project, I built out the following deployment script:

{% highlight javascript %}
var plan = require('flightplan');

var config = {
  projectDir: '/path/to/whatchuwant/to/deploy/to',
  keepReleases: 5
};

plan.target('staging', {
  host: 'somewhere.yourdomain.com',
  username: 'you',
  agent: process.env.SSH_AUTH_SOCK
});

plan.local('deploy', function (local) {
  local.log('Building project...');
  local.exec('./node_modules/.bin/gulp build');
});

plan.remote('deploy', function(remote) {
  config.deployTo = config.projectDir + '/releases/' + (new Date().getTime());
  remote.log('Creating webroot');
  remote.exec('mkdir -p ' + config.deployTo);
});

plan.local('deploy', function (local) {
  local.log('Transferring source code');
  local.transfer('dist/', config.deployTo + '/');
});

plan.remote('deploy',function (remote) {
  remote.log('Linking to new release');
  remote.exec('ln -nfs ' + config.deployTo + ' ' + 
    config.projectDir + '/current');

  remote.log('Checking for stale releases');
  var releases = getReleases(remote);

  if (releases.length > config.keepReleases) {
    var removeCount = releases.length - config.keepReleases;
    remote.log('Removing ' + removeCount + ' stale release(s)');

    releases = releases.slice(0, removeCount);
    releases = releases.map(function (item) {
      return config.projectDir + '/releases/' + item;
      });

    remote.exec('rm -rf ' + releases.join(' '));
  }
});

function getReleases(remote) {
  var releases = remote.exec('ls ' + config.projectDir + 
    '/releases', {silent: true});

  if (releases.code === 0) {
    releases = releases.stdout.trim().split('\n');
    return releases;
  }

  return [];
}
{% endhighlight %}

Flightplan will run these groups of commands in order when we run:

    ./node_modules/.bin/fly deploy:staging

This is following, for the most part, how Capistrano runs deployments. We're creating a new directory in
`releases/` with a name of the current timestamp. We transfer all of our files in `dist/` over into this directory, and
then symlink `current` to point to `releases/[timestamp]`. `current` is where Nginx should point to deliver the site.

Finally, we check how many releases are in `releases/` and delete any more than the 5 most recent. 

It's pretty simple, just running a bunch of commands on the server, and it's fast.

## Rolling Back Bad Code

If we need a rollback task, that's easy too:

{% highlight javascript %}
plan.remote('rollback', function(remote) {
  remote.log('Rolling back release');
  var releases = getReleases(remote);
  if (releases.length > 1) {
    var oldCurrent = releases.pop();
    var newCurrent = releases.pop();
    remote.log('Linking current to ' + newCurrent);
    remote.exec('ln -nfs ' + config.projectDir + '/releases/' + newCurrent + ' '
      + config.projectDir + '/current');

    remote.log('Removing ' + oldCurrent);
    remote.exec('rm -rf ' + config.projectDir + '/releases/' + oldCurrent);
  }

});
{% endhighlight %}

And we can run this with:

    ./node_modules/.bin/fly rollback:staging

## Deploying from SCM

If you need to deploy from SCM, rather than deploying a local folder, you can do that too. You can do it remotely, as Capistrano does, or
you can do a fresh clone locally, build the source, and then deploy the compiled code to the server.

Something like:

{% highlight javascript %}

var config = {
  // ...
  source: 'git@github.com:supercoolbro/awesomeapp.git';
};

plan.local('deploy-scm', function (local) {
  var source = config.source;

  config.tmp = 'tmp/' + (new Date().getTime());

  local.log('Checking out source code with branch [master]...');
  local.exec('mkdir -p ' + config.tmp);
  local.exec('git clone --depth=1 ' + source + ' ' + config.tmp, {silent: true});

  local.log('Installing dependencies...');
  local.exec('cd ' + config.tmp + ' && npm i', {silent: true});
  local.log('Building project...');
  local.exec('cd ' + config.tmp + ' && npm run build', {silent: true});
});

plan.remote('deploy-scm', function (remote) {
  config.tmp = 'tmp/1419956963582';
  config.deployTo = config.projectDir + '/releases/' + (new Date().getTime());
  remote.log('Creating webroot');
  remote.exec('mkdir -p ' + config.deployTo);
});

plan.local('deploy-production', function (local) {
  local.log('Transferring source code');
  local.transfer(config.tmp + '/dist/', config.deployTo + '/');
});

plan.remote('deploy-scm',function (remote) {
  remote.log('Linking to new release');
  remote.exec('ln -nfs ' + config.deployTo + ' ' + config.projectDir + '/current');

  remote.log('Checking for stale releases');
  var releases = getReleases(remote);

  remote.log('Found ' + releases.length + ' releases');

  if (releases.length > config.keepReleases) {
    remote.log('Removing ' + (releases.length - config.keepReleases) + ' stale release(s)');

    releases = releases.slice(0, releases.length - config.keepReleases);
    releases = releases.map(function (item) {return config.projectDir + '/releases/' + item;});

    remote.exec('rm -rf ' + releases.join(' '));
  }
});
{% endhighlight %}

We can run this with

    ./node_modules/.bin/fly deploy-scm:staging

Now your remote server is clean, simple, and serving up static assets without any Node dependencies.

## Deploying full Apps

Need to deploy a full stack app? You can do that too! Simply follow the route of Capistrano and modify the recipe above to do the
checkout remotely instead of locally.

Flightplan is pretty sweet. Check it out.
