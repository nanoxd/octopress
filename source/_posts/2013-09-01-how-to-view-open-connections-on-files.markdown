---
layout: post
title: "How to view open connections on files"
date: 2013-09-01 20:01
comments: true
categories: [Linux, CLI, Debugging]
---
During a pairing session on Ruby, we had an issue with what file the API was stuck on. Luckily, there is a command `lsof` which stands for list open files.

## Usage

When `lsof` is ran without parameters, it will show all the files opened by _any_ process. It can get a bit messy, so I recommend `grep`ing through to find the file/process you want.

To find out what files Ruby has opened you can use `lsof -c`. Here is what it looks like when running `rails server`:

```sh
$ lsof -c ruby
ruby    55222 nano   10w   REG    1,1   1214625 34875337 ~/Developer/Projects/contact-manager/log/development.log
ruby    55222 nano   11u   REG    1,1     49152 34875460 ~/Developer/Projects/contact-manager/db/development.sqlite3
```

To find out what is accessing any file in a directory, use the `lsof +D /usr/directory`

```sh
$ lsof +D /Users/nano/Developer/Projects/contact-manager
vim     44957 nano  cwd    DIR    1,1      748 34873022 ~/Developer/Projects/contact-manager
```

You can list all opened internet sockets using `lsof -i`.

```sh
$ lsof -i :62658
Rdio      63484 nano   23u  IPv4 0x2674679fdf954c9b      0t0  TCP 10.0.1.14:62658->8.18.203.81:sunproxyadmin (ESTABLISHED)
# We can find out the process id for Unicorn running on port 8080
$ lsof -i :8080
COMMAND   PID USER   FD   TYPE             DEVICE SIZE/OFF NODE NAME
ruby    54171 nano    9u  IPv4 0x2674679fe1b36243      0t0  TCP *:http-alt (LISTEN)
ruby    54172 nano    9u  IPv4 0x2674679fe1b36243      0t0  TCP *:http-alt (LISTEN)
```

After finding the process id, you can find out what that process is accessing using `lsof +p PID`

```sh
$ lsof +p 79766
COMMAND   PID USER   FD   TYPE             DEVICE  SIZE/OFF     NODE NAME
ruby    54172 nano  cwd    DIR                1,1       748 34873022 ~/Developer/Projects/contact-manager
ruby    54172 nano  txt    REG                1,1      9184 17640590 ~/.rvm/rubies/ruby-2.0.0-p247/bin/ruby
ruby    54172 nano  txt    REG                1,1   2793108 17640592 ~/.rvm/rubies/ruby-2.0.0-p247/lib/libruby.2.0.0.dylib
ruby    54172 nano  txt    REG                1,1    118244   615195 /usr/local/Cellar/libyaml/0.1.4/lib/libyaml-0.2.dylib
ruby    54172 nano  txt    REG                1,1    378152   797957 /usr/local/Cellar/openssl/1.0.1e/lib/libssl.1.0.0.dylib
ruby    54172 nano  txt    REG                1,1   1663200   797956 /usr/local/Cellar/openssl/1.0.1e/lib/libcrypto.1.0.0.dylib
```

Using several commands leads you to any files that may be _stuck_ in processing. I have found that it is an essential tool in debugging a multitude of issues.

## References

- [A Unix Utility You Should Know About: lsof](http://www.catonmat.net/blog/unix-utilities-lsof/)
- [man lsof](http://linux.die.net/man/8/lsof)
- [lsof Survival Guide](http://stackoverflow.com/questions/106234/lsof-survival-guide)
