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

## Choosing your Linux Distribution

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

 * connecting to the server for the first time.
 * Creating your app's user

## Getting the server ready for your application

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

First, we'll install the `build-essential` package:

    sudo apt-get install build-essential

Then install the various Ruby packages:

    sudo apt-get install ruby1.9.3 rubygems ri rdoc

Then install our database server, PostgreSQL:

    sudo apt-get install postgresql-server-9.1

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

To install Passenger, first we install the gem:

    sudo gem install passenger

The Passenger gem installs all of the helper applications and scripts for installing it either
as an Apache or nginx module. Since we're going to run this in nginx, that's what we'll focus
on.

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


 * set up passenger/nginx
   * point it to the right place
 * set up production database

## Deployment

### Capistrano

 * getting capified
 * how Capistrano deploys
 * doing your first deploy
   * cap deploy:setup
   * cap deploy
 * verify that the app is working

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

