---
title: "Getting Started"
layout: documentation
---

## Getting started with fpm-cookery

This page helps you to get started with the fpm-cookery tool and guides you
through the installation and the creation of a simple recipe to build your
first package.

You will create a package for the [tmux](http://tmux.sourceforge.net/) program.

## Prerequisites

The following instructions have been tested with an Ubuntu 12.04 Linux system.
It might work on other versions or other Linux systems but that cannot be
guaranteed. Please use something like [Vagrant](http://www.vagrantup.com/) to
create an Ubuntu 12.04 VM if you do not have one at hand.

## Installation

### Rubygems

fpm-cookery is written in Ruby. Before we can actually install the rubygem, you
have to install a Ruby interpreter and some build tools.
Execute the following to install the required packages.

<pre>
$ sudo apt-get install ruby1.9.1 ruby1.9.1-dev build-essential curl
</pre>

Ruby 1.9 includes the `gem` program to install rubygems.

<pre>
$ sudo gem install fpm-cookery
</pre>

This installs the fpm-cookery rubygem and its dependencies. At the end you
should see something like this.

<pre>
Successfully installed fpm-cookery-{{ site.data.project.version }}
</pre>

Your fpm-cookery installation is ready to build some packages now!

### OS Package

We are planning to provide a packaged version for different operating systems.
Please use the Rubygems installation method above in the meantime.

## The Recipe

The recipe is a Ruby file that contains a simple class which acts as a DSL
to set the attributes of a package (like name and version) and to describe
the build and installation process of a package.

You might want to create some folders to organize your recipes.

<pre>
$ mkdir recipes
$ mkdir recipes/tmux
$ cd recipes/tmux
$ touch recipe.rb
</pre>

The last command creates an empty recipe file. See the following snippet for
the complete recipe to build a tmux package. We will go through each step
afterwards. Use your text editor to add the code to the `recipe.rb` file.

<pre class="code">
class Tmux < FPM::Cookery::Recipe
  description 'termnial multiplexer'

  name     'tmux'
  version  '1.9a'
  homepage 'http://tmux.sourceforce.net/'
  source   'http://freefr.dl.sourceforge.net/project/tmux/tmux/tmux-1.9/tmux-1.9a.tar.gz'

  build_depends 'libevent-dev', 'libncurses5-dev'
  depends       'libevent-2.0-5'

  def build
    configure :prefix => prefix
    make
  end

  def install
    make :install, 'DESTDIR' => destdir
  end
end
</pre>

<pre>
$ fpm-cook
===> Starting package creation for tmux-1.9a (ubuntu, deb)
===>
===> Verifying build_depends and depends with Puppet
===> Verifying package: libevent-dev
===> Verifying package: libevent-2.0-5
===> Missing/wrong version packages: libevent-dev
ERROR: Not running as root; please run 'sudo fpm-cook install-deps' to install dependencies.
</pre>

<pre>
$ sudo fpm-cook install-deps
===> Verifying build_depends and depends with Puppet
===> Verifying package: libevent-dev
===> Verifying package: libevent-2.0-5
===> Missing/wrong version packages: libevent-dev
===> Running as root; installing missing/wrong version build_depends and depends with Puppet
===> Installing package: libevent-dev
===> ensure changed 'purged' to 'present'
===> All dependencies installed!
</pre>

<pre>
$ fpm-cook
===> Starting package creation for tmux-1.9a (ubuntu, deb)
===>
===> Verifying build_depends and depends with Puppet
===> Verifying package: libevent-dev
===> Verifying package: libncurses5-dev
===> Verifying package: libevent-2.0-5
===> All build_depends and depends packages installed
===> Fetching source:
######################################################################## 100.0%
===> Building in /home/vagrant/recipes/tmux/tmp-build/tmux-1.9a
checking for a BSD-compatible install... /usr/bin/install -c
checking whether build environment is sane... yes

[lots of output removed]

make[1]: Nothing to be done for `install-data-am'.
make[1]: Leaving directory `/home/vagrant/recipes/tmux/tmp-build/tmux-1.9a'
===> [FPM] Converting dir to deb {}
===> [FPM] No deb_installed_size set, calculating now. {}
===> [FPM] Reading template {"path":"/var/lib/gems/1.9.1/gems/fpm-1.0.2/templates/deb.erb"}
===> [FPM] Creating {"path":"/tmp/package-deb-build20140308-7998-1v6uqm5/control.tar.gz","from":"/tmp/package-deb-build20140308-7998-1v6uqm5/control"}
===> [FPM] Created deb package {"path":"tmux_1.9a-1_amd64.deb"}
===> Created package: /home/vagrant/recipes/tmux/pkg/tmux_1.9a-1_amd64.deb
</pre>

<pre>
.
|-- cache
|   `-- tmux-1.9a.tar.gz
|-- pkg
|   `-- tmux_1.9a-1_amd64.deb
|-- recipe.rb
|-- tmp-build
|   `-- tmux-1.9a
`-- tmp-dest
    `-- usr
</pre>
