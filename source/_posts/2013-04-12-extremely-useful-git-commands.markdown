---
layout: post
title: "Extremely useful git commands"
date: 2013-04-12 08:11
comments: true
categories: git
---

While I've only been using git for several years, I have not had the pleasure of learning or using all of the commands.
I've since tried to change that by getting a better feel of the VCS. Here are a few of the commands that I either didn't know or love to use:

##Amending a commit
How many times have you inadvertantly forgotten to include a file in your last commit? I know my [Github](http://github.com) can attest to my forgetfullness. We will first start out by creating a file named `help.rb`

```bash
git add help.rb
git commit -m "Add help file"
```

It seems we forgot to include some text in the help file. To fix it we will append the information to the end of the file using the `echo` command.

```bash
echo 'You can also contact me at foo@bar.com' >> hello.rb
```

You can now add it back to the list of tracked files and amend your commit using the following

```bash
git add help.rb
git commit --amend -m "Add contact information to help file"
```


##References
+ [Git-SCM](http://git-scm.com/book/en/Git-Basics-Undoing-Things)
