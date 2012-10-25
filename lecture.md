# Flatiron School Lecture Notes

*Deploying your first app.*

## Who am I?

 * Sr DevOps Engineer at Ticket Evolution
 * Systems and Software Engineer in one, specializing in automation and deployment
 * over 10 years of Linux experience

## What this lecture will cover

In this lecture, I will cover the steps necessary for taking the next great app that you build
and have been running locally while developing and get it running on a server so anyone in the
world can access it on the public internet.

You'll learn how to get your first virtualized private server, or VPS, set up for your
application, install your database software, and deploy the app. From there, you'll learn how
to update your application on the server to fix bugs or add features in a way that allows you
to roll back your code in the case of a show-stopping bug.

## Going from development to production

### Virtualization

#### What is it, exactly?

Virtualization (not to be confused with emulation) is a technology which allows you to create
isolated execution environments for multiple operating systems running on the same hardware
and sharing resources such as RAM, disk and network. Most modern CPUs contain enhancements that
enable near-native performance in the virtualized environments for many tasks. A single
virtualized environment is called a virtual machine or VM.

A physical piece of hardware has various limits in terms of what kind of input and output it
can handle. A given processor can only run so many instructions per second, a hard disk can
only spin so fast and read and write at a maximum rate (known as IO or input/output),
and a network interface can only send or recieve so much data per second. When it comes to
virtualization, a single physical computer can be running many virtual machines and because of
this, it can lead to competition when accessing these resources.

For example, on a single piece of hardware with 4 virtual machines, if they all try to read
from different parts of the disk at th same time, that can lead to something known as read
contention. A harddrive is made up of spinning platters with an arm on it that seeks to 
different positions to read and write data, so if it tries to read from different places
on the disk at the same time, each concurrent read will slow down.

A properly managed virtual infrastructure will have multiple disks with special hardware to
enable acceptable performance under these conditions and mitigate IO contention. Also, when
it comes to these VMs, many of the resources are also virtualized, so that the network interfaces,
for instance, actually show up as separate devices to the rest of the network even though there
is only a single, physical device.

#### Why is it so great?

Virtualization has been around for a long time, but it wasn't until recently that the
applications for it became viable for the masses. With the advent of inexpensive, high-performance
and multi-core CPUs, VMWare's commercial products in the enterprise and Xen, which is one (of many)
official Linux kernel addition, has allowed Amazon to create their EC2 cloud offering, so getting your
app up and running on the public internet is faster, cheaper and easier than ever.

In the past, if one wanted to host their website, they had to go to a cheap shared host, where they
may have been heavily restricted in what they could run or get a dedicated server which typically
required an up-front setup cost and several days of lead time to be configured before being ready
to use. Now, with services offered by Linode, Amazon, Rimu, Rackspace and a plethora of other
hosts, you can bring up a server for a couple of days or a couple of hours and take it down for
only a couple bucks. If you want to start over, it's as easy as pushing a button

#### Why isn't everything virtualized?

There are many cases where one may want to squeeze every last CPU cycle or disk read out of their
application that they can. Also, when you have a single system that you want to be very large,
such as 32 CPU cores and 128GB of RAM, the virtualization layer may not be worth the added
administration and performance overhead.

Typically large applications will put their databases on non-virtualized, dedicated hardware
in an attempt to get as much IO and CPU power as they can.

Today, we'll be setting up and deploying our application to a virtualized server, so I won't talk
any more about this.

## Provisioning your server

There are a whole slew of providers out there. In this lecture, I'm going to talk about how to
provision a server on Linode, which, in my opinion is the right balance of price, ease of
administration and quality of service.

 * Signing up for Linode
 * choosing the correct server size

### Choosing your Linux Distribution

Linux is a wildly popular, open-source operating system that was originally written by Linus
Torvalds (also the creator of Git). Because of the varying ways that people use Linux and
because it's so customizable, this has lead to an incredible selection of distributions of
Linux.

### What's a distribution?

When Linux was first released and began mass adoption, there was no easy way to install it. It
required compiling your kernel from source with support for the specific hardware that you were
running it on, including the vendor and model of your motherboard, CPU, video card, network card,
and many other pieces of hardware hidden inside of your PC. Code that was compiled on one person's
computer might not run on another person's due to them installing libraries in different locations
in their filesystem.

To solve this, various users and various companies started pre-packaging Linux installers into
their own distribution. This began to standardize the layout of the filesystems and also led to
additional automation in the form of package managers and pre-compiled applications that would
run, without modification, on that particular distibution.

The first large distribution (or distro) was RedHat, which, currently, is making over $1 Billion
in revenue per year. Although Linux is free, companies, such as RedHat make money selling services
and support for it. Due to the success of RedHat, other companies have cropped up, either making
their own distros or modifying existing ones and releasing it under their name.

Some other examples of Linux distros are:

 * Gentoo
 * Debian
 * Ubuntu (based on Debian)
 * Slackware
 * CentOS (based on RedHat)
 * Suse

In this lecture, we're going to focus on Ubuntu, which is my favourite distro and also one of the
most popular right now.

### Creating your server

We're going to be using Dediserve.com to host our application. Dediserve has been nice enough to
supply us with a pool of resources that we can use to create servers as we need for this class
and they have an easy to use dashboard for creating and managing our servers.

To start, we need to get logged in and create our node. Once there we can choose from various
options for what resources will be available for our server. The great thing about virtualization
over physical hardware is that we have full control over our resource limits. We can define
exactly how much memory, disk and CPU is available to it and also choose from a variety of
Linux distributions, all using software, and without the need to physically install any hardware.

For this class, we'll be creating our nodes with the following settings:

 * Ubuntu Linux 12.04 x64
 * 2GB of RAM
 * 2 CPUs
 * 16GB Disk
 * 2GB Swap

The memory and disk sizes may seam small to you, considering that most of your phones probably
have more capacity, but when it comes to running a server, without a graphical interface and
without media files such as video or MP3s, the need for large amounts of storage is drastically
reduced.

Ubuntu numbers their releases in the form of YEAR.MONTH and they have 2 releases per year; one
in April and one in October. We'll be installing the 64-bit version of this previous April's
release of Ubuntu on our machine.

The 2GB of swap refers to a special area of disk that is used to store data when memory runs
low. This way, if your application exhausts the 2GB of RAM that we've allocated, it won't cause
the machine to completely fail, but rather, the operating system will offload some of the less
frequently accessed data to swap to make room for new data. Because the swap is located on disk,
it's much slower to access than when it's in RAM. Also, when things are transfered in and
out of swap, it can slow things down, but overall, it's better for the system.

You should adjust the sliders and choose from the menues in the form to match the above settings
and choose a name for your server and a password. These are both arbitrary, but don't forget the
password.

After a couple of minutes, the server will be available to you to connect to and Dediserve will
display what the IP of the machine is.

### Connecting to your server for the first time

We will use SSH to connect to our server. SSH stands for Secure SHell and is a way to remotely
administer a server via the commandline. Once connected, you can navigate the server's filesystem,
start and stop services and run scripts just like you were on your local machine, only everything
you're doing is on the remote VM.

When configuring your machine, Dediserve created a root user for you. Also known as the superuser,
the root user has full access to everything and it isn't restricted by permissions in the same
ways that a regular user would be. It's a full administrator and those rights cannot be taken
away. Because of this, it's usually considered bad practise to use this account to log in on a
regular basis and we will create an "unpriviledged user" for our application to run as and for you
to connect as to do everything.

Using the IP that Dediserve displayed in their dashboard after you created your VM, let's connect
to our fresh server instance. To do this, we use the `ssh` command in the Terminal on your local
development machine:

    ssh root@XXX.XXX.XXX.XXX

Replace `XXX.XXX.XXX.XXX` with the IP for your machine.

You should see something like the following:

    The authenticity of host '96.8.123.64 (96.8.123.64)' can't be established.
    RSA key fingerprint is 22:fb:91:18:d0:98:ee:e6:c7:77:ae:fa:55:8c:3a:08.
    Are you sure you want to continue connecting (yes/no)?

The server will provide a unique identifier to you which identifies the server, known as its
"SSH Fingerprint." This is used to ensure that you're connecting to the server that you expect
to connect to. We're not going to worry about verifying this fingerprint right now as we're only
concerned about connecting to the machine.

Type `yes` and press enter and you will be prompted for the root password for the machine. Type
that in and you'll be presented with a prompt. It should look something like this:

    root@spike001:~#

Congrats! You've just connected to your own server! Now, to make a user for you and your app.

#### Creating your own user

Creating your own user is done using the `useradd` command. This command has a whole slew of
options that it accepts for customizing your user, but that's beyond the scope of this lecture.
You can get a full list of all of the options by typing the following into your ssh session:

    useradd --help

The settings that we're most interested in are as follows:

 * `-s <shell>` -- the shell that is assigned to this user.
 * `-G <groups>` -- any security groups to which this user belongs.
 * `-m` -- to ensure that the home directory is created

Your shell is the commandline interface that you interact with when logging into your server.
There are a lot of different shells available including `sh`, `zsh` and `tcsh` in addition to
`bash`, which is one of the most common and my personal favourite.

We will add your user to the `sudo` group. Sudo is a command that allows you to temporarily
raise your priviledge level to that of the root user (or, any other user, depending on the
configuration of the server) without actually logging in as root. Most Linux distributions ship
with the root user disabled and have you create an administrator user which has sudo access for
security purposes. On servers with multiple administrators, using sudo allows much finer-grained
controls for access to the machine without needing to share the root password.

By adding your user to the `sudo` group, it allows your new user to run commands with increased
priviledges.

To create your user type the following command:

    useradd -s /bin/bash -G sudo -m USERNAME

Replace `USERNAME` with the name of your user.

Next, you want to create a password for your user, so run the following command:

    passwd USERNAME

Again, replace `USERNAME` with the name of your user.

You will be prompted for the new user's password, so choose one, type it and press enter. Then
type it again to confirm.

You should now be able to log into your server as your new user, so disconnect by typing `exit`,
which brings you back to your local machine's prompt, and re-connect using the following command:

    ssh USERNAME@XXX.XXX.XXX.XXX

Replace `USERNAME` and `XXX.XXX.XXX.XXX` with your new username and server's IP and you should
be prompted for your password. Type that in and you're in as yourself!

### Getting the server ready for your application

Now that you have a server and you can connect to it, it's time to prep it so you can run your
application on it.

### Installing the necessary packages

#### Apt

A vanilla Ubuntu Server install does not have everything you need to get started. We'll need to
install the `build-essential` package, which installs everything you need to compile packages
on your server including `gcc`, `make` and other developer tools.

In addition to that, we need to install Ruby 1.9, Rubygems, their development packages (which
are used when gems need to be compiled from source) and our database server (PostgreSQL).

Ubuntu uses a package management system called `apt-get` which simplifies the whole process
of installing software and their dependencies. Without that, we'd need to manually go around
to various FTP servers, fetch each software package and compile it from source, which, depending
on how many packages you need and how much information you have up front, could take days. With
`apt-get`, we'll be done in about 2 minutes.

Before we start installing packages, we need to make sure that the apt repositories and all
installed packages are up to date. This is equivalent to checking for updates, then installing
all system updates.

Run the following command to update the apt repository:

    sudo apt-get update

Then upgrade all installed packages:

    sudo apt-get upgrade

Press enter when prompted to confirm the packages to update. Any time apt is going to install
more than the program you selected, due to dependencies, it will confirm with you before
continuing. Pressing enter or typing 'Y' and pressing enter will kick the install proces off.

At this point, we're ready to start installing all of our necessary packages.

The first package to install is the `build-essential` package:

    sudo apt-get install build-essential

Then install the various Ruby packages:

    sudo apt-get install ruby1.9.3 sqlite3 libsqlite3-ruby libsqlite3-dev

That installs Ruby and the sqlite3 libraries required to install the sqlite3 gem.

We also need to install git (for deploying our app):

    sudo apt-get install git

You may have noticed that we haven't installed a webserver for our application; you'll see why
in the next section.

#### Gems

Because we use Bundler for our application's gem dependency management, the only gems we'll
install manually are the ones required for running the application in production and Bundler
itself:

    sudo gem install bundler

We'll be running our application in Passenger, which is a Rack appserver that acts as a module
for nginx, a high-performance, evented webserver which is quickly catching up to Apache as
the number one Linux webserver and is the goto webserver for Rails deployments and admins.

We then need to install the `sqlite3` gem:

    sudo gem install sqlite3 sinatra

To install Passenger, first we install the gem:

    sudo gem install passenger

The Passenger gem installs all of the helper applications and scripts for installing it either
as an Apache or nginx module. Since we're going to run this in nginx, that's what we'll focus
on.

## Deployment

### Capistrano

Capistrano is a tool which executes commands on multiple remote servers, concurrently, and is
primarily used for deploying applications. That's what we're going to use it for today.

In order to use Capistrano, you must first install the `capistrano` gem. This is done with
the following command on your local machine:

    gem install capistrano

Capistrano does not require anything to be installed on your server except for SSH. The gem
makes 2 commands available to you:

 * `capify` which is used to prepare your application to be deployed using Capistrano.
 * `cap` for deploying your application.

To get started, in your Terminal, `cd` into your application's directory and type:

    capify .

This creates a `Capfile` and creates a directory called `config` which contains `deploy.rb`.
The `Capfile` is the entrypoint for `cap` to access its configuration.

We are going to edit the `config/deploy.rb` file to enable deployment of our application.
The file should look as follows:

    set :application, "studentbody"
    set :repository,  "GITHUB_REPOSITORY"

    set :user, 'USERNAME'
    set :deploy_to, "/home/#{ user }/#{ application }"
    set :use_sudo, false

    set :scm, :git

    default_run_options[:pty] = true

    role :web, "96.8.123.64"                          # Your HTTP server, Apache/etc
    role :app, "96.8.123.64"                          # This may be the same as your `Web` server

    # if you want to clean up old releases on each deploy uncomment this:
    # after "deploy:restart", "deploy:cleanup"

    # if you're still using the script/reaper helper you will need
    # these http://github.com/rails/irs_process_scripts

    # If you are using Passenger mod_rails uncomment this:
    namespace :deploy do
     task :start do ; end
     task :stop do ; end
     task :restart, :roles => :app, :except => { :no_release => true } do
       run "#{try_sudo} touch #{File.join(current_path,'tmp','restart.txt')}"
     end
    end

Capistrano has a very declaritive syntax, but it's all Ruby. It first defines some variables
using the `set` function. The first parameter is the name of the variable and the second is
the value.

We set the following variables:

 * `application` -- the name of our application; used in the `deploy_to` variable
 * `repository` -- the URL for the repository on github. Replace this with yours
 * `deploy_to` -- the location on the remote server that we will deploy the application
 * `use_sudo` -- we set this to false because we are deploying to a location that our user owns
 * `user` -- the user that we are deploying as. This should be the username you chose for your
 user when setting up the server.
 * `scm` -- this is set to the source-code-management system we're using, `:git`

We then have to set `default_run_options[:pty] = true`. This is done because on some servers,
Capistrano doesn't interact with the shell properly. In our case, this is necessary is is not
there by default.

We then define 2 roles using the `role` function. Capistrano has a concept of roles so you may
have appservers, which serve the app, but web servers which handle the requests and a separate
`db` server with your database. In our case, we're running on one server, so we need to define
both the `web` and `app` roles with our server's IP.

We also uncomment the `:deploy` namespace. Capistrano is a task-oriented application. Various
tasks are defined internally, and you can define your own tasks using the `task` method. These
tasks that we just uncommented are for starting, stopping and restarting our application via
the Passenger interface. The `task` system is very robust and is a large enough topic to cover
an entire class, so we won't go into detail here.

Ensure that `Capfile` and `config/deploy.rb` are both added to your git repository and committed.

### Setting up the app to run on the server

In order for the application to run on the server, it needs a special configuration file called
a "Rackup file." This file describes how to launch your application and where everything lives
that it needs.

Create a new file in the root of your app and call it `config.ru`. It should contain the
following data:

    require './studentbody'
    run StudentBody.new

This will require your `studentbody.rb` file which contains your Sinatra application and start
it up. Passenger requires this file in order to be able to run your application.

Make sure this file is also added to git and committed.

### Preparing the deployment

Capistrano has a full deployment framework built in and with the above configuration, it has
enough information to deploy your app. The first thing we need to do is ensure that the
file structure exists on your server by running the built-in `deploy:setup` task.

From your local machine, in your application's directory, run the following command:

    cap deploy:setup

This will connect to your server, prompt for your password and create the necessary folder
structure exists on the server. It will spit out all the actions that it's executing on the
server and when it's done, you'll be brought back to your prompt.

Capistrano's directory structure is pretty simple:

    /home/USERNAME/studentbody/
      releases/
      shared/

When you deploy, it makes a copy of your git repository into a timestamped directory in the
`releases` directory. It then creates a symbolic link (or a symlink) to that new release,
inside your applicaiton's directory, calling it `current`. What this enables you to do is
release updates to your code, but retain older versions on the server. In the event that you
need to revert to an older version, it becomes easy and you also have a record of what's been
deployed in the past and when it was deployed.

Capistrano deploys your code right from Github, so you have to make sure that you've pushed
all of your changes before doing a deploy.


## Passenger

Because nginx doesn't have a loadable module feature like Apache, when you want to add a new
module to nginx, you have to re-compile it from source with the module's code. Luckily,
Passenger automates most of this process for you and will even download the nginx source code
and compile it for you.

To build nginx from source and install it, we run the `passenger-install-nginx-module` executable
that the Passenger gem provides for us. It must be run as root as it needs to install things
into locations that may not be writable by an under-privilaged user:

    sudo passenger-install-nginx-module

This will start the automated Passenger installer. First it will explain what it's going to do,
then it will do a verification phase to make sure we have all of the correct libraries installed:

    Checking for required software...

     * GNU C++ compiler... found at /usr/bin/g++
     * The 'make' tool... found at /usr/bin/make
     * A download tool like 'wget' or 'curl'... found at /usr/bin/wget
     * Ruby development headers... found
     * OpenSSL support for Ruby... found
     * RubyGems... found
     * Rake... found at /usr/local/bin/rake
     * rack... found
     * Curl development headers with SSL support... not found
     * OpenSSL development headers... not found
     * Zlib development headers... not found

    Some required software is not installed.
    But don't worry, this installer will tell you how to install them.

It turns out that we didn't install all of the correct packages. Pressing the Enter key will
prompt passenger to display instructions for what to do next. We can install all the required
packages with a single command:

    sudo apt-get install libcurl4-openssl-dev libssl-dev zlib1g-dev

And then run the `passenger-install-nginx-module` again. This time, it will confirm
that all necessary packages are installed and it will guide you through the steps
to install nginx with the Passenger module:

    Nginx doesn't support loadable modules such as some other web servers do,
    so in order to install Nginx with Passenger support, it must be recompiled.

    Do you want this installer to download, compile and install Nginx for you?

     1. Yes: download, compile and install Nginx for me. (recommended)
        The easiest way to get started. A stock Nginx 1.2.3 with Passenger
        support, but with no other additional third party modules, will be
        installed for you to a directory of your choice.

     2. No: I want to customize my Nginx installation. (for advanced users)
        Choose this if you want to compile Nginx with more third party modules
        besides Passenger, or if you need to pass additional options to Nginx's
        'configure' script. This installer will  1) ask you for the location of
        the Nginx source code,  2) run the 'configure' script according to your
        instructions, and  3) run 'make install'.

    Whichever you choose, if you already have an existing Nginx configuration file,
    then it will be preserved.

    Enter your choice (1 or 2) or press Ctrl-C to abort: 

We will choose 1 since we don't need to do anything advanced. Type a '1' and press
enter. Passenger will then download and unpack nginx and prompt you for a location
to install it.

We'll use the default prefix of `/opt/nginx` for installing our application.

A prefix is a base path for where to install a series of files. When installing,
no directories will be overwritten, but if you choose a non-existent path, it will
be created for you. For example, if you were to install nginx to `/`, or the "root
filesystem" rather than `/opt/nginx`, nginx files would be mixed into the rest of
your Linux install and would be more difficult to remove. Installing it in `/opt/nginx`
enables you to completely remove it by simply removing that one directory.

Press the Enter key to continue with the default prefix and Passenger will
begin the automated compilation and installation steps. This should take a couple
minutes.

After a bunch of text flies by, you sould eventually be greeted with with a message
that nginx completed successfully:

    Nginx with Passenger support was successfully installed.

    The Nginx configuration file (/opt/nginx/conf/nginx.conf)
    must contain the correct configuration options in order for Phusion Passenger
    to function correctly.

    This installer has already modified the configuration file for you! The
    following configuration snippet was inserted:

      http {
          ...
          passenger_root /var/lib/gems/1.9.1/gems/passenger-3.0.17;
          passenger_ruby /usr/bin/ruby1.9.1;
          ...
      }

    After you start Nginx, you are ready to deploy any number of Ruby on Rails
    applications on Nginx.

    Press ENTER to continue.

Press Enter to get additional setup information and be brought back to your prompt.

Now, we configure nginx to serve your application.

## nginx

nginx is the actual webserver. It's a piece of software that speaks HTTP, is
very high performance and sits between your application and the internet. Although
Passenger is capable of serving all content associated to your application, there's
no reason to re-invent the wheel and implement all of the features that nginx has.

When you host a Rack application (such as Sinatra or Rails) in Passenger, on nginx,
your application will handle requests that it's configured to handle and nginx
will serve up any static content without going through your application. Static
content is considered to be any content that you can see on the filesystem
in your app's public folder. Content that is served directly from the harddrive,
to the internet at large.

The way that Passenger works is it starts up several "handlers" for your application.
That is, there will be several instances of your app running independently and
Passenger will hand off requests to these instances in a cycle. By doing this, it
ensures that each instance of your app is only handling a single request at a time
and it keeps things as simple as possible, which is good for avoiding bugs. If
more requests come in than your handlers can accept, passenger will worry about
placing the requests in a queue so clients don't get rejected. All of this happens
transparently to your application, so, as a developer, you don't have to think
about this stuff.

nginx is capable of serving this content much faster than your app and won't tie
up handlers in the process.

### Configuring nginx

nginx's configuration files are plain text and live at `/opt/nginx/conf`. We'll use
`vim` to edit them. To edit the primary nginx config file (`nginx.conf`) run the
following command:

    sudo vim /opt/nginx/conf/nginx.conf

This will open up the vim editor and you can start editing.

The nginx config format is easy to read and is structured with blocks of
configuration surrounded by braces ({}). Each line of configuration is terminated with
a semi-colon (;) and comments are denoted with a hash (#) like in Ruby.

The first thing we need to do is make it so nginx runs as an unprivilaged user. On
Ubuntu systems, the typical webserver user is `www-data`. At the top of the file
is a commented out line:

    #user  nobody;

We want to un-comment it and change the user to be `www-data` so it matches the
following:

    user www-data;

Passenger has already partially configured nginx for us, but it's not quite configured
for serving our application, yet. We first need to point the "document root" to our
application and enable Passenger for it.

Document root is the base location that nginx will serve your content. Any requests
to your website will be served relative to this path and nothing outside of it will
be publically accessible. In Rack apps (eg: Rails and Sinatra), this should be pointed
to your `public` directory. Although your actual code lives outside of the `public`
directory, Passenger will be able to handle requests properly and nginx will serve
any static content located inside this directory.

To configure nginx to serve your app, ocate the `location` block in the config file,
which looks like:

    location / {
      root   html;
      index  index.html index.htm;
    }

Delete the whole block and replace it with the following 2 lines:

    root /home/USERNAME/APPNAME/current/public;
    passenger_enabled on;

Replace `USERNAME` and `APPNAME` with your username and the name of your application
respectively. Don't forget the semi-colons.

The `passenger_enabled on;` line tells nginx to use Passenger when serving content
at this location.

That's it! You can now exit the editor and we can move on to the next step.

### Starting nginx

nginx can be started and stopped all through the `/opt/nginx/sbin/nginx` command.
In order to reduce typing, we're going to create a symlink to it in a place that
will allow us to call just `nginx`.

Unix-like systems (like OSX and Linux) have a thing called the `PATH` which is a series
of places that it searches when you type a command on the commandline. Such locations
include `/bin`, `/usr/bin` and `/sbin`. You can inspect your current path by typing:

    printenv PATH

into your console. It should return something like the following:

    /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games

which is a colon-delimited list of the places it will check, in order.

We're going to place the symlink to nginx in `/usr/local/sbin` since the `/usr/local`
namespace is, by convention, the location that administrators should put any
programs that isn't installed by a package manager, directly.

We do this with the following command:

    sudo ln -s /opt/nginx/sbin/nginx /usr/local/sbin/

Now, if we type `nginx -h`, it will spit out the usage for the nginx command.

To start nginx, run the following command:

    sudo nginx

It should immediately return you to your prompt. When you start nginx, it spawns a server
process in the background so you can continue doing other things on the server. It will
continue to run in the background until you stop it or reboot the server.

To stop nginx, you send it a "stop" signal using the `-s` switch:

    sudo nginx -s stop

And that's it. Once it's running, you should be able to access your application by going to:

    http://YOUR-IP

in a web browser.


#### Virtual Hosts

nginx also has a feature that it supports called Virtual Hosts, or "vhosts". When a
request comes in via http, a header in the request indicates what hostname the
request is intended for. The term "Virtual Host" is independent of our virtual
machine and applies to any hostname-based request routing.

An example of this is where you have a single server, but you want to host differnet
content for flatironschool.com, students.flatironschool.com and mysite.com. By
looking at the request header, nginx can use different rules for serving the content,
pointing to different directories, logging things differently or denying access
based on login credentials or the requester's IP address.

Let's set up the vhost for our application.




 * set up passenger/nginx
   * point it to the right place
 * set up production database

## Once you're up and running

Following are some topics that need to be mentioned, but are beyond the scope of this lecture.

### Logrotate

After your app is launched, there is a common thing that many people miss, and that is `logrotate`.

Logrotate is a package, which is usually installed on most servers by default and it watches your
logfiles on a schedule and will compress them and delete them as necessary. Although it is typically
already configured for your system logs, if you want to rotate your application's logs, you'll have
to configure this manually.

Since running out of disk space is a very common cause of sites going offline, `logrotate` is
necessary to keep that aspect of your server in check. Without `logrotate`, on a long enough timeline
your disk *will* fill up.

I typically configure it to compress my logs once a day and only keep 8 days worth of logs. This way,
the server always has the last week's worth of logs available for you to examine if necessary, and
unless your app is getting a **serious** amount of traffic, all of your logs probably won't take up
more than 100MB.

### Backups

Another thing that I need to mention is backups. In today's day and age of automated server
configuration, server images and fast internet connections, full-system backups aren't as necessary.
Most of the time, you'll find that you really only want to back up your application's data (databases, etc)
and, if you're into long-term analytics, your log files.

Just remember, replication of your databases and RAID for your disks are not acceptable alternatives
for backing up. Dataloss due to user-error or malicious users will replicate the same as your
normal data. Also, copy your backups off the server, preferably to another physical location such as
S3 or another provider.

And last piece of advice: Test your backups regularly. There's nothing worse than thinking you have
backups, having dataloss and discovering that all this data you've been hoarding is unusable or
incomplete.

