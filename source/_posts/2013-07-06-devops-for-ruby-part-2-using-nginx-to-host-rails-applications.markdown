---
layout: post
title: "DevOps for Ruby Part 2: Using Nginx to Host Rails Applications"
date: 2013-07-06 19:26
comments: true
categories: [DevOps, Nginx, VPS, Ruby, Rails, PostgreSQL]
---
In [DevOps for Ruby Part 1](http://blog.fdp.io/2013/06/23/devops-for-ruby-part-1-vps-and-ubuntu-installation/), we set up a new VPS with all of the software needed to serve Ruby on Rails applications. In this post we will be setting up two Rails application with seperate PostgreSQL databases.


## Objectives

The goal of this tutorial is to:

- Create the necessary folders to host our web applications
- Use Git to sync our applications from Github onto the VPS.
- Create PostgreSQL databases for our applications and modify the `database.yml` file.
- Set up Nginx to serve our application.

## Initial setup

First, we must create a `/var/www` folder. We can do that by `ssh`-ing into our VPS. We can create the folder using:
```bash
sudo mkdir -p /var/www
```
`mkdir` makes the directory and the `-p` flag adds intermediate folders if they don't exist.

## GitHub setup

In order to avoid typing passwords all of the time, we must generate SSH keys and tie them to our Github account. You can follow this [guide](https://help.github.com/articles/generating-ssh-keys) to get started.

Next, lets fetch our Rails application using the `git clone` command:
```bash
cd /var/www
git clone git@github.com:NanoXD/fdpio.git
git clone git@github.com:NanoXD/testapp.git
```

Verify that you have two folders within `/var/www/` for your two Rails apps.

## Database Setup

We are going to create a seperate user for each application as it is good security practice to limit how much access a user has. Let's start off by logging into the PostgreSQL shell

```bash
sudo -u postgres psql
```

We can create users and databases for each of our applications using this syntax:

```psql
postgres=# create user REPLACE_WITH_USER with password 'ENTER_PASSWORD';
postgres=# create database DATABASE_NAME owner USERNAME_FOR_DATABASE;
```

### Configuring your database.yml

Each Rails application comes with a `database.yml` file like I described in [Setup PostgreSQL for Rails on a Mac](/2013/05/23/setup-postgresql-for-rails-on-a-mac/). Let's modify this file to connect to our new PostgreSQL database.

```yml
# /var/www/fdpio/config/database.yml

production:
  adapter: postgresql
  encoding: unicode
  host: localhost
  database: fdpio_db
  pool: 5
  username: fdpio
  password: password-man
```

Do the same for your secondary Rails app.

## Configure Nginx

We now have to setup Nginx to allow us to serve our two applications using one server. If you followed [Part 1](2013/06/23/devops-for-ruby-part-1-vps-and-ubuntu-installation/) of this tutorial, you can find your nginx.conf in the `/opt/`. Let's open it up using vim:

```bash
sudo vim /opt/nginx/conf/nginx.conf
```

Keep in mind that this configuration is suited to my two applications. Ensure that you chage the paths and names to fit your application. I will describe the changes below.

```
worker_processes  1;

events {
    worker_connections  1024;
    multi_accept on;
}

http {
    passenger_root /home/nano/.rvm/gems/ruby-2.0.0-p195/gems/passenger-4.0.5;
    passenger_ruby /home/nano/.rvm/wrappers/ruby-2.0.0-p195/ruby;

    include       mime.types;
    default_type  application/octet-stream;
    sendfile        on;
    keepalive_timeout  65;

    gzip  on;
    gzip_vary on;

    server {
        listen       80;
        server_name  fdp.io;
        charset utf-8;
        root /var/www/fdpio/public;
        passenger_enabled on;
        rails_env production;
    }

    server {
        listen       80;
        server_name  testapp.com;
        charset utf-8;
        root /var/www/testapp/public;
        passenger_enabled on;
        rails_env production;
    }

}
```

The two Passenger settings that have to suit your machine are:
`passenger_root` - Specifies where Passenger is
`passenger_ruby` - Tells Passenger what version of Ruby to use.

We created two server settings for each application. Here is what each setting means:

```
# What port does this server listen to?
listen       80;

# Default charset to use.
charset utf-8;

# What domain will I route to this application?
server_name  fdp.io;

# The root of the application, for Rails apps it's always the public folder.
root /var/www/fdpio/public;

# Let Passenger handle the Rails application.
passenger_enabled on;

# Set the RAILS_ENV variable to 'production'.
rails_env production;
```

As long as these domains are in your possession and you have set up the DNS to point to your VPS, you can now visit the domains and see your application.

The only caveat left is whenever you update your application, you will need to let Passenger know to reload the application. You can alleviate this in one of two ways:

1. Create/Modify the `tmp/restart.txt` in your Rails application folder.
2. Restart Nginx manually

We are officially done with the manual work! We will be going over ways to automate this setup using tools like Capistrano and Chef in DevOps Part 3.
