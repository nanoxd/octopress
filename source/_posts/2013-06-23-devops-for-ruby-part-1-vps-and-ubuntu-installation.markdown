---
layout: post
title: "DevOps for Ruby Part 1: VPS &amp; Ubuntu Installation"
date: 2013-06-23 21:53
comments: true
categories: [Rails, PostgreSQL, Ruby, Setup, RVM, Ubuntu, DevOps, Passenger, Nginx] 
---
When you first start learning _Ruby_ or _Rails_ you are expected to learn many different languages at a time. When it comes to deploying that application you hit a crossroad. Will you use a site like [Heroku](https://www.heroku.com/) or try your luck at a VPS? For small applications, a cost-comparison will yield Heroku the prime candidate but once you need more dynos it quickly becomes cost-prohibitive. VPSs are an unyielding dragon that quickly breathe fire at a newcomer with it's extensibility. This guide aims to help in lowering the burden and making VPS more accessible to everyone. 

_Notice: This guide is only meant as a starting point._ 

## Picking a Virtual Private Server (VPS)

There are a plethora of VPS offerings out there. Some are inexpensive, use a different OS, include more ram, offer  more bandwidth. Look around and find one that fits you and your application needs best. 


The three VPS providers I hold dear to my heart are [DigitalOcean](https://www.digitalocean.com/), [Linode](https://www.linode.com/), and [MediaTemple DV](http://mediatemple.net/webhosting/vps/developer/index-3.php). DigitalOcean provides SSD cloud servers that can be built in minutes, Linode has quick and painless customization, and MediaTemple while being the most expensive, offers RAID-10. All three providers have excellent customer support and extensive documentation for any error you may encounter.

Try to pick a server with at least:  
- 1GB RAM  
- 500GB Bandwidth  
- 20GB Storage  

## Ubuntu Setup and Configuration

I will cover Ubuntu 13.04 x64 as it is the latest version available on DigitalOcean and Linode.

After selecting the operating system, you will be given a root password and IP to connect to your server. We will be using a terminal (e.g. Terminal or [iTerm2](http://www.iterm2.com/)) to connect using the following command:

```sh
$ ssh 10.0.0.1
  root@10.0.0.1's password: TYPE_PASSWORD_HERE
  Welcome to Ubuntu 13.04 (GNU/Linux 3.8.0-19-generic x86_64)
```

### Updates
The first thing we want to do is make sure our package manager has the latest updates and perform any upgrades.

```
# The first command pulls package lists while the second command runs the install script
$ sudo apt-get update
$ sudo apt-get upgrade
```
### Security

Next we want to disable root access to the server. We do this by creating a new user using the `adduser` command. It will ask for a password (ideally different than the root password) and optional personal information.

```
$ adduser sample
Adding user `sample' ...
Adding new group `sample' (1001) ...
Adding new user `sample' (1001) with group `sample' ...
Creating home directory `/home/sample' ...
Copying files from `/etc/skel' ...
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
Changing the user information for sample
Is the information correct? [Y/n] Y
```

Then we want to allow our new user to have root capabilities by adding it to the sudo list:

```
$ sudo visudo
```

`visudo` will bring up a document with lots of information. The only part you need to focus on is User privilege specification. We will add our new user to this section like so:

```
# User privilege specification
root    ALL=(ALL:ALL) ALL
sample  ALL=(ALL:ALL) ALL
```

Press `Ctrl + X` and enter `Y` to save the document. Next we will disable SSH login to the `root` login. _Note: We will be logging into our newly created account soon!_

Let's open up the SSH configuration file and find `PermitRootLogin` under the `Authentication` tab and change it to look like below:
```
$ vim /etc/ssh/sshd_config
# You can also use nano if you don't like vim
$ nano /etc/ssh/sshd_config


# Authentication:
LoginGraceTime 120
PermitRootLogin no
StrictModes yes
```

For the changes to take effect you need to run
```
$ /etc/init.d/ssh restart
```

Finally, let's logout from the `root` account using the `logout` command.

Now that we are logged out, lets use our new user `sample` using the following:
``` 
$ ssh sample@ip-address
```

## Software Installation

Let's install some basic software before we install _rvm_. We need `curl` to download the script for rvm and `git` for version control. 
```
$ sudo apt-get install curl git-core
```

### RVM Installation
Next, let's install [RVM](http://rvm.io/). RVM stands for Ruby Version Manager and as it's name implies, it manages different versions of Ruby. There are a few alternatives such as [rbenv](http://rbenv.org/) and [chruby](https://github.com/postmodern/chruby). You are welcome to pick whichever one you like but the following instructions are for RVM.

First, let's grab RVM:
```
$ curl -L get.rvm.io | bash -s stable
```

After it is done installing, we will need to load it into our environment.
```
$ source ~/.rvm/scripts/rvm
# We can verify the install using:
$ rvm -v
#=> rvm 1.21.2 (stable) by Wayne E. Seguin <wayneeseguin@gmail.com>, Michal Papis <mpapis@gmail.com> [https://rvm.io/]
```
In order to work, RVM has some of its own dependancies that need to be installed. You can install and see what they are using:
```
$ rvm requirements
#=> Installing required packages: gawk, g++, gcc, make, libc6-dev, libreadline6-dev, zlib1g-dev, libssl-dev, libyaml-dev, libsqlite3-dev, sqlite3, libxml2-dev, libxslt1-dev, autoconf, libc6-dev, libgdbm-dev, libncurses5-dev, automake, libtool, bison, pkg-config, libffi-dev
```

### Ruby Installation
Now that RVM is installed we can install the latest patch level of Ruby 2 using:
```
$ rvm install 2.0.0
# To install 1.9.3
$ rvm install 1.9.3
```
Once the version of Ruby you selected is finished installing, we can make it the _default_ Ruby version.
```
$ rvm --default use 2.0.0
```

### Rails Installation

_Feel free to skip this section if you are using [Sinatra](http://www.sinatrarb.com/)._

Installing Rails is very simple, the only hard part is deciding what version you want to use. 

#### Rails 3.2
Great! You picked the battle-tested Rails 3.2, which means less typing!

```
$ gem install rails --version 3.2.13
```

To verify the version installed run ```rails -v```

#### Rails 4
__Update: Rails 4 was released today on June 25th, 2013__
So you want to see what the hype is about? What are Turbolinks and Strong Parameters? Maybe you like to play with the Russian Doll Caching. Whatever it is, we can install it using:

```
$ gem install rails
```

Let's run `rails -v` to verify our version number matches 4.0.0 RC2. 

### PostgreSQL
PostgreSQL is a highly regarded database in the Rails community as an object-relational database management system. To install it on Ubuntu run the following:
```
$ sudo apt-get install postgresql
```

For security purposes, we will change the `postgres` user password using:
```
$ sudo -u postgres psql postgres
```

The command will drop us into a psql session. Enter the following to change the default password:
```sql
\password postgres
# Enter new password:
# Enter it again:
```

Once you are finished, press `Ctrl + D` to terminate the psql session. Next we need to allow communication to the Postgres server from our Rails app. Let's open up the default configuration:
```
$ sudo nano /etc/postgresql/9.1/main/postgresql.conf
```
We are looking for a block that is commented out that allows localhost connections. Uncomment the line like so:
```diff
- #listen_addresses = 'localhost'
+ listen_addresses = 'localhost'
```

All we need now is a Postgres server reboot:
```
$ sudo service postgresql restart
```

### Passenger + Nginx Installation
My favorite app-server + web-server are Passenger and Nginx due to the ease of installation. First we will install the Passenger gem and setup the Nginx module.
```
$ gem install passenger
$ rvmsudo passenger-install-nginx-module
```

You will be presented with three choices. Choose Option 1 to automate the installation of Nginx. You might have some missing package dependencies, follow the installation instructions for them and rerun `rvmsudo passenger-install-nginx-module` 

Nginx is now installed and configured on your machine but it is recommended to install the following script to start, stop, or restart nginx.
```
wget -O init-deb.sh http://library.linode.com/assets/660-init-deb.sh
mv init-deb.sh /etc/init.d/nginx
chmod +x /etc/init.d/nginx
/usr/sbin/update-rc.d -f nginx defaults
```

The following commands will allow you to easily start or stop Nginx:
```
sudo /etc/init.d/nginx stop
sudo /etc/init.d/nginx start
```

Congratulations, you are done with the first part of your VPS deployment. If you visit the ip address of your VPS, you will be greeted with the standard "Welcome to nginx!" page. 

Check back for Part two next week!



## References
- [Ubuntu PostgreSQL](https://help.ubuntu.com/community/PostgreSQL)
- [Nginx Init Script](https://library.linode.com/web-servers/nginx/installation/ubuntu-10.04-lucid)

