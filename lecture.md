# Flatiron School Lecture Notes

*Deploying your first app.*

## Who am I?

 * Sr DevOps Engineer at Ticket Evolution
 * Systems and Software Engineer in one, specializing in automation and deployment
 * over 10 years of Linux experience

## Going from development to production

### Virtualization

#### What is it, exactly?

Virtualization (not to be confused with emulation) is a technology which allows you to create
isolated execution environments for multiple operating systems running on the same hardware
and sharing resources such as RAM, disk and network. Most modern CPUs contain enhancements that
enable near-native performance in the virtualized environments for many tasks. A single
virtualized environment is called a virtual machine or VM.

Of course, there can be contention with regard to IO resources such as network and disk when
multiple VMs ont he same hardware are fighting for access, but overall, the benefits outweigh
the problems if properly managed.

When it comes to VMs, many of the resources are also virtualized, so that the network interfaces,
for instance, actually show up as separate devices to the rest of the network even though there
is only a single, physical device. Disk devices are also virtualized and there are vendors who
provide products that are designed to be virtualized and can provision disks with guaranteed
throughput to your VMs, mitigating issues with fighting for those resources during high load.

#### Why is it so great?

Virtualization has been around for a long time, but it wasn't until recently that the
applications for it became viable for the masses. With the advent of VMWare's commercial products
in the enterprise and Xen, which is an official Linux kernel addition and allowed Amazon to
create their EC2 cloud offering, getting your app up and running on the public internet is
faster, cheaper and easier than ever.

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

    sudo apt-get install ruby19 ruby19-dev rubygems ri rdoc

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

This will output something like the following:

    INSERT OUTPUT HERE

We will fill it out... INSERT MORE INSTRUCTIONS

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

