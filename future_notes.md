# server admin II

## Connecting to your server

 * ssh keys
 * ssh one-off commands
 * transfering files
   * `scp`
   * `sftp`
   * `rsync`
 * sudo without password
 * server banner
 * motd
 * `curl`

## nginx next steps

 * init scripts
   * init.d
   * upstart
   * nginx on-startup
 * virtualhosts

## Server metrics

 * what kinds of things to worry about on your server
 * `uptime`
   * load averages
 * `/proc`
   * cpuinfo
   * meminfo
   * process information
 * disk space
   * partitions
   * `/etc/fstab`
   * what's taking up all my space?
   * `df -h`
   * disk i/o
     * `iostat`
     * `iotop`
 * memory
   * `free -m`
 * processes
   * `top`
   * `htop`
   * `ps`
   * process signals (`kill`)
 * network information
   * `ifconfig`
   * ifconfig.me
   * `ip`
   * `netstat`
   * `nmap`
   * `iptraf`

## Logging

 * viewing logs
   * `less`
   * `grep`
 * app logging
 * troubleshooting by looking at logs
   * auth.log
   * syslog
 * logrotate
   * setting up logrotate for your app

## postgres

 * install it
 * create database
 * backups
   * `pg_dump`
   * `pg_restore`

## advanced capistrano

 * multistage
 * roles
 * custom tasks
   * description
   * getting help on the commandline
   * internal tasks
   * log tail tasks
 * variables
   * lazy variables
   * commandline variables
 * shell
