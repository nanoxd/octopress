---
layout: post
title: "Setup PostgreSQL for Rails on a Mac"
date: 2013-05-23 14:57
comments: true
categories: [postgres, SQL, Rails, Setup]
---
## Pick your elephant poison

There are a myriad of ways to install PostgreSQL on a Mac. I will be going over `brew` and __Postgres.app__. I like to go with the app as it eases into my workflow but does require it's path to be set in your profile (e.g `.zshrc`, `.bashrc`, or `fish.config`)

#### Homebrew Installation

The homebrew installation is very straightforward and can be done by following the guide below.

* Run a brew command to install PG and take note of the documentation that follows it.
```bash
brew install postgresql
```
* If this is your first install run the following command to create your first database
```bash
initdb /usr/local/var/postgres -E utf8
```
* Create aliases to simplify the start and stop of the Postgres service
```bash
alias pg-start='pg_ctl -D /usr/local/var/postgres -l /usr/local/var/postgres/server.log start'
alias pg-stop='pg_ctl -D /usr/local/var/postgres stop -s -m fast'
```

#### Postgres.app Installation

1. Visit http://postgresapp.com
2. Download the latest version which as of this writing is `9.2.4.1`
3. Drag, drop, and open the elephant icon into the Application folder.
4. Set the follwing in your `.bashrc` or`.zshrc`
```
    PATH="/Applications/Postgres.app/Contents/MacOS/bin:$PATH"
    # If you are using Fish, use the following:
    set PATH /Applications/Postgres.app/Contents/MacOS/bin $PATH
```
5. Open the elephant and verify it is referencing the app using `which psql`

The path should look like what was entered above but if all else fails restart your terminal session.

## Use Postgres in Rails

Now that you've successfully installed PG, you can utilize it in rails by including it in your gemfile.

#### New Rails App
If you have yet to generate your rails app, you can set Postgresql as your database by running `rails new blog -d postgresql`
You will then have to run `rake db:create:all` to create the databases in the `database.yml` file.

#### Existing Rails App
For an existing rails app you will need to add the `pg` gem to your gemfile like so.

```ruby Gemfile
source 'https://rubygems.org'

gem 'rails', '4.0.0.rc1'
gem "pg", "~> 0.15.1"

gem 'sass-rails', '~> 4.0.0.rc1'
gem 'uglifier', '>= 1.3.0'
gem 'coffee-rails', '~> 4.0.0'
gem 'jquery-rails'
gem 'turbolinks'
```

You will also need to change your database.yml file to look something like this:

```yml database.yml
development:
  adapter: postgresql
  encoding: utf8
  database: blog_dev
  host: localhost
test:
  adapter: postgresql
  encoding: utf8
  database: blog_test
  host: localhost

production:
  adapter: postgresql
  encoding: utf8
  database: blog_production
  host: supersecretserver
```

Finally, you can run `rake db:create:all` followed by `rake db:migrate` and continue editing your amazing rails app!

## Resources

* [Railscasts Ep. 342](http://railscasts.com/episodes/342-migrating-to-postgresql)
* [Postgres.app](http://postgresapp.com/)
* [PG Gem](http://rubygems.org/gems/pg)
