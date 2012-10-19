# Flatiron School Lecture Notes

Deploying your first app.

## Who am I?

 * Sr DevOps Engineer at Ticket Evolution
 * Systems and Developer in one, specializing in automation and deployment
 * 10 years Linux experience

## Going from development to production

### Virtualization

 * what is it, exactly?
 * why is it so great?
 * why wouldn't you want it?

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
