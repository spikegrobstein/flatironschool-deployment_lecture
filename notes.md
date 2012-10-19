# Flatiron School Lecture Notes

Ops for Developers. Where to go from here.

## Differences between development and production

### Operating System

 * OSX vs Linux
 * starting and stopping services

### appservers

 * weBRICK vs Thin
 * passenger
   * apache
   * nginx
 * also mentioned: unicorn, puma, einhorn

### security model

 * users
 * permissions
 * how they apply to a deployed app
   * contrast php vs rails on passenger
 * sudo

### web server basics

 * internals of how requests are handled from client-server-nginx-rails
 * external databases (postgres/mysql)

## Deployment

### capistrano

 * getting capified
 * preparing the server
 * doing your first deploy
 * deployment maintenance
   * `number_to_keep`

## Once you're up and running

 * logrotate
 * keeping an eye on things
   * top
   * iotop
   * htop
   * netstat
   * iostat
   * uptime
 * load averages

### Backups!

 * what to back up
 * why to back up
 * where to back it up
 * testing your backups
