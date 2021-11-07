# Sudo-Mask
[![Shell check](https://github.com/XenonTheInertG/Sudo-Mask/actions/workflows/shell_check.yml/badge.svg)](https://github.com/XenonTheInertG/Sudo-Mask/actions/workflows/shell_check.yml)

Fake sudo mask buildpack for heroku(Heroku Root buildpack)
- As we all know heroku doesn't let users to use Sudo commands or they don't have a support for sudo. Recently i was making a bot & deployed it to heroku but the problem was that it needed root. I searched in google & found Fabioking's fakechroot buildpack. Tried to install it but it was broken af. It was a buildpack from 2013,so i made my own buildpack:)


- `Heroku buildpack that creates a chroot jail, and allows users to run commands as (fake)**root** inside it.`


# Applying the buildpack

--------------------------------------------------
NOTE: Make sure you are logged in with heroku CLI
--------------------------------------------------
```bash
$ heroku create --buildpack https://github.com/XenonTheInertG/Sudo-Mask.git
```
- Copy this code to create a heroku app with this buildpack
```bash
$ heroku buildpacks:add https://github.com/XenonTheInertG/Sudo-Mask.git
```
- And this is to add to existing app and make sure you are connected to the app if not make sure you are by
```bash
$ heroku git:remote -a (app-name-here)
```
# Alternative method:
Or simply deploy the app first,then go to settings. Scroll down. 
> Add Buildpack

>paste : `https://github.com/XenonTheInertG/Sudo-Mask`


## Usage

    $ ls
    Gemfile      Gemfile.lock web.rb

    $ heroku create -s cedar -b http://github.com/XenonTheInertG/Sudo-Mask.git

    $ git push heroku master
    wait...

    $ heroku run bash

    (dyno)$ id
    uid=28171(u28171) gid=28171

    (dyno)$ fakesu

    (dyno)# id
    uid=0(root) gid=0(root)

    (dyno)# whoami
    root

    (dyno)# ls ~
    Gemfile  Gemfile.lock  bin  web.rb


### Default process type (console)

    $ heroku run console

    (dyno)# whoami
    root

### -c option for custom commands as (fake)root

    $ heroku run bash

    (dyno)$ fakesu -c id
    uid=0(root) gid=0(root)

    (dyno)$ fakesu -c touch /etc/file-created-as-root

    (dyno)$ fakesu -c ls -l /etc/file-created-as-root
    -rw------- 1 root root 0 Jan 30 09:50 /etc/file-created-as-root


## Why would I need such a crazy thing?

### Because I want to build and run my own system packages

e.g.: a different version of ruby:

    $ heroku run console

    (dyno)# cd /tmp

    (dyno)# curl -O http://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.2-pXXX.tar.gz

    (dyno)# tar zxvf ruby-1.9.2-pXXX.tar.gz

    (dyno)# cd ruby-1.9.2-pXXX

    (dyno)# ./configure --prefix=/usr/local optflags="-O3" debugflags="-g3 -ggdb"

    (dyno)# make

    (dyno)# make install


### Because I have system dependencies (e.g. libzmq-dev)

    $ heroku run console
    
    (dyno)# cat Gemfile
    source :rubygems
    gem "zmq"
    ...

    (dyno)# echo "deb http://ppa.launchpad.net/chris-lea/libpgm/ubuntu lucid main" >> /etc/apt/sources.list

    (dyno)# echo "deb http://ppa.launchpad.net/chris-lea/zeromq/ubuntu lucid main" >> /etc/apt/sources.list

    (dyno)# apt-get update

    (dyno)# apt-get install libzmq-dbg libzmq-dev libzmq1

    (dyno)# bundle install
    Fetching source index for http://rubygems.org/
    ...
    Installing zmq (2.1.4) with native extensions 
    Using bundler (1.0.21) 

In this case, your application processes (Procfile) would need to be started inside the chroot jail, where the system dependencies were installed.

`fakesu -c` can be used for that, e.g.: `fakesu -c bundle exec thin -p $PORT ...`


### Because I want to install and run **services**

    $ heroku run console

    (dyno)# apt-get install nginx

    (dyno)# env | grep PORT
    PORT=59222

    (dyno)# vim /etc/nginx/sites-available/default
    edit the "listen 80" line and change it to the port given by the previous command

    (dyno)# /etc/init.d/nginx restart


Using another terminal, it is now possible to [create routes](https://github.com/JacobVorreuter/heroku-routing):

    $ heroku ps
    Process  State      Command  
    -------  ---------  -------  
    run.1    up for 1m  bash    

    $ heroku routes:create
    Creating route... done
    tcp://route.heroku.com:28392

    $ heroku routes:attach tcp://route.heroku.com:28392 run.1

    $ open http://route.heroku.com:28392


### Because it can be combined with other buildpacks

TODO, using the [heroku-multi-buildpack](https://github.com/ddollar/heroku-buildpack-multi)


### Because I want to be the (fake)root of my dyno :)
# Contributors
This buildpack is based on  [fakesu](https://github.com/fabiokung/heroku-buildpack-fakesu)
