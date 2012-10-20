# Flatiron School Lecture Notes

Deploying your first app.

## Who am I?

 * Sr DevOps Engineer at Ticket Evolution
 * Systems and Developer in one, specializing in automation and deployment
 * 10 years Linux experience

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
such as 32 CPU cores and 128GB of RAM, the overhead of the virtualization layer may be too much.

Typically large applications will put their databases on non-virtualized, dedicated hardware
in an attempt to get as much IO and CPU power as they can.

Today, we'll be setting up and deploying our application to a virtualized server, so I won't talk
any more about this.

### appservers

 * local development vs production
 * passenger
   * nginx

## Setting up the server

 * Linode
 * Creating your app's user
 * installing the necessary packages
   * apt-get
     * ruby
     * ruby-dev
     * rubygems
     * build-essential
     * database (postgres)
   * gem
     * bundler
     * passenger
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

 * backups
 * logrotate
