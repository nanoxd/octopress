---
layout: post
title: "Ammending git commits"
date: 2013-04-12 08:11
comments: true
categories: git
---

During the past few years of using git, I've come across several commands which have become pivotal in my workflow. One of them is ``` git commit --amend```.

##Amending a commit
How many times have you inadvertantly forgotten to include a file in your last commit? I know my [Github](http://github.com) can attest to my forgetfullness. We will first start out by creating a file named `help.md`

{% codeblock %}
#Help
Nothing can stop the man with the right mental attitude from achieving his goal; nothing on
earth can help the man with the wrong mental attitude.
{% endcodeblock %}

```bash
git add help.md
git commit -m "Add help file"
```

It seems we forgot to include some text in the help file. To fix it we will append the information to the end of the file using the `echo` command.

```bash
echo 'You can also contact me at foo@bar.com' >> hello.md
```

You can now add it back to the list of tracked files and amend your commit using the following

```bash
git add help.md
git commit --amend -m "Add contact information to help file"
```

Congratulations, you have successfully amended a commit!

##References
+ [Git-SCM](http://git-scm.com/book/en/Git-Basics-Undoing-Things)
