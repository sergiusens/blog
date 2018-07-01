---
date: 2014-11-27T00:06:12-03:00
title: Travis and ruby gems setup on Ubuntu
categories:
  - ubuntu
---

## Quick install

Most of the instalation instructions that dangle the web just make you
`sudo gem install`, I don't like that, so here's the quick reference
for when I need to do this next time:

    sudo apt install ruby-dev
    export GEM_HOME=$HOME/gems
    gem install travis -v 1.7.4 --no-rdoc --no-ri

And this goes into `~/.bashrc`:

    export GEM_HOME=$HOME/gems
    export PATH=$PATH:$GEM_HOME/bin
