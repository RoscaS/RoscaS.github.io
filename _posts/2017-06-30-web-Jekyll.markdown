---
layout: post
title: "web: Git, Jekyll & MD "
subtitle: ""
date: 2017-06-30
author: Sol
category: Web
tags: web jekyll static generator
finished: false
---

# Git

## SSH

* `ls -al ~/.ssh` $$ \Rightarrow $$ check if a key already exist
* `ssh-keygen -t rsa -b 4096 -C "sol.rosca@he-arc.ch"` $$ \Rightarrow $$ make new key
* `eval "$(ssh-agent -s)"` $$ \Rightarrow $$ check if ssh agent is working
* `ssh-add ~/.ssh/id_rsa` $$ \Rightarrow $$ put key in ... folder
* `sudo apt-get install xclip` $$ \Rightarrow $$ nice tool
* `xclip -sel clip < ~/.ssh/id_rsa.pub ` $$ \Rightarrow $$  using the nice tool

## git

* `apt-get install git` 

Git comes with a tool called git config that lets you get and set configuration variables that control all aspects of how Git looks and operates. These variables can be stored in three different places:

.1 `/etc/gitconfig file` $$ \Rightarrow $$  Contains values for every user on the system and all their repositories. If you pass the option `--system` to `git config`, it reads and writes from this file specifically.

.2 `~/.gitconfig` or `~/.config/git/config file` $$ \Rightarrow $$  Specific to your user. You can make Git read and write to this file specifically by passing the --global option.

.3 `config` $$ \Rightarrow $$  file in the Git directory (that is, `.git/config`) of whatever repository you’re currently using: Specific to that single repository.


* ` $ git config --global user.name "John Doe"`
* ` $ git config --global user.email johndoe@example.com`

* `$ git config --global color.ui auto`


# Jekyll

## links
[jekyll website](https://jekyllrb.com/)
[jekyll doc](https://jekyllrb.com/docs/home/)
[Hands on tutorial](http://jekyllrb.com/tutorials/)

## Installation

### Ruby

* `$apt-get install ruby-full`  
* `gem install jekyll`


### usefull cmd

* `jekyll --version`
* `gem update jekyll`
* `jekyll serve` $$ \Rightarrow $$ http://localhost:4000/
* `gem install jekyll bundler` $$ \Rightarrow $$ usefull the first time
* `jekyll new myblog` $$ \Rightarrow $$ Create a new blog in _myblog_

