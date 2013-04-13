---
layout: post
title: "Extremely useful git commands"
date: 2013-04-12 08:11
comments: true
categories: git
---

While I've only been using git for several years, I have not had the pleasure of learning all of the commands.
I've since tried to change that by getting a better feel of the VCS. Here are a few of the commands that I either didn't know or love to use:

####git commit --amend
```bash
git add help.rb
git commit -m "Add help file"

# It seems I forgot to include a crucial part of the help text.

echo 'You can also contact me at foo@bar.com' >> hello.rb
git add help.rb
git commit --amend -m "Add contact information to help file"
```

