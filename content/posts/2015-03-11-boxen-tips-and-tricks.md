+++
title = 'Boxen Tips and Tricks'
categories = ['devops']
summary = """
Running multiple computers can be frustrating. How do you make sure you have
the same software on your work computer as your personal computer. Or how do
you make sure you have the same settings on your desktop as you do on your
laptop? We'll look at Boxen and how to set it up to get the most out of it.
"""
proof = false
+++
{{< image "automate-all-the-things.jpg" "Automate all the things!" `class="float-right"` >}}

A while back I wrote about [The Perfect Dev Setup]({{< ref "2012-02-02-perfect-dev-setup-lion" >}}) that walked through what I considered to be (at the time), the perfect development setup. Part of the reason I wrote a post about it so that I could repeat it on any machine that I used, but it would still take time and copy/pasting commands. Surely there must be a better way?

Spoiler alert, I started automating this with [Boxen](https://github.com/boxen/our-boxen). Boxen it a tool that Github released a couple years back that automated new employee's computers. It was designed for Mac, which is the platform that I use. It uses [Puppet](https://puppetlabs.com/puppet/what-is-puppet), which is a provisioning tool. That means if you know Puppet, you can really do some cool stuff with it, and if not, I think it's easy enough to understand to use.

Getting Boxen setup is really easy. Just follow the [Readme](https://github.com/boxen/our-boxen#our-boxen) and you're good to go. There are tons of [modules](https://github.com/boxen) that give you almost anything that you would need.

Here are some tips and tricks I've learned using Boxen.

## Disable Full Disk Encryption

By default Boxen requires you to have Full Disk Encryption enabled. You can get by that requirement by using the `--no-fde` option when running `boxen`.

    $ boxen --no-fde

## Multiple Computers

If you're anything like me, you probably have multiple computers. You're main computer, a laptop, and probably a work computer. Being able to setup multiple machines and keep changes across those is what Boxen great. But what about when you want something on your work machine, but not on your laptop? Or what about something you only want on your personal machines? Boxen ships with the _people_ module, but that assumes different usernames.

Boxen is built on Puppet, so we can use [Node Definitions](https://docs.puppetlabs.com/puppet/latest/reference/lang_node_definitions.html). By default _manifests/site.pp_ only has a _default_ node, but we can add as many as we want. Let's create a node for each of our computers.

{{< highlight puppet >}}
node 'Matthews-Mac.local' {
    # Only runs on iMac
}

node 'Matthews-Laptop.local' {
    # Only runs on laptop
}

node 'Matthew-OSX.local' {
    # Only runs on work computer
}

node default {
}
{{< /highlight >}}

Now we can copy everything from _default_ and add it to each node and customize it.

**Note**

I recommend keeping the default node around. If you don't have it and no other nodes match, Puppet will fail.

## Using Modules to Abstract Common Things

Having multiple nodes is awesome. Now we can customize what we need for each computer, but you might find that you're using some of the same things over and over. Having to manage those things in multiple places is work. Let's fix this by moving those to modules.

If you don't know Puppet, don't worry, I'll walk you through a basic module structure. If you're feeling ambitious, check out Puppet's [Module Guide](https://docs.puppetlabs.com/guides/module_guides/bgtm.html).

I'm going to call my module _common_ following the best practice. The structure for a module will look something like this:

    /modules
      /common
        /files
          php.ini
        /manifests
          /init.pp
          /development.pp
        /templates
          nginx.conf.erb

At minimum you just need the `manifests/init.pp` file.

Let's start off by moving everything to our common module.

{{< highlight puppet >}}
# modules/common/manifests/init.pp
class common {
  # core modules, needed for most things
  include dnsmasq
  include git
  include hub
  include nginx

  # fail if FDE is not enabled
  if $::root_encrypted == 'no' {
    fail('Please enable full disk encryption and try again')
  }

  # node versions
  nodejs::version { 'v0.6': }
  nodejs::version { 'v0.8': }
  nodejs::version { 'v0.10': }

  # default ruby versions
  ruby::version { '1.9.3': }
  ruby::version { '2.0.0': }
  ruby::version { '2.1.0': }
  ruby::version { '2.1.1': }
  ruby::version { '2.1.2': }

  # common, useful packages
  package {
    [
      'ack',
      'findutils',
      'gnu-tar'
    ]:
  }

  file { "${boxen::config::srcdir}/our-boxen":
    ensure => link,
    target => $boxen::config::repodir
  }
}
{{< /highlight >}}

Then update our nodes to include this.

{{< highlight puppet >}}
node 'Matthews-Mac.local' {
    # Only runs on iMac
    include common
}

node 'Matthews-Laptop.local' {
    # Only runs on laptop
   include common
}

node 'Matthew-OSX.local' {
    # Only runs on work computer
    include common
}

node default {
    include common
}
{{< /highlight >}}

Let's move our development stuff to `common::development`.

{{< highlight puppet >}}
# modules/common/manifests/development.pp
class common::development {
  include mysql
  include redis
  include virtualbox
  include vagrant

  # node versions
  nodejs::version { 'v0.8': }
  nodejs::version { 'v0.10': }
  nodejs::version { 'v0.12.0': }

  class { 'nodejs::global': version => 'v0.12.0' }

  # default ruby versions
  ruby::version { '1.9.3': }
  ruby::version { '2.0.0': }
  ruby::version { '2.1.0': }
  ruby::version { '2.1.1': }
  ruby::version { '2.1.2': }
}
{{< /highlight >}}

Then update our nodes.

{{< highlight puppet >}}
node 'Matthews-Mac.local' {
    # Only runs on iMac
    include common
    include common::development
}

node 'Matthews-Laptop.local' {
    # Only runs on laptop
   include common
}

node 'Matthew-OSX.local' {
    # Only runs on work computer
    include common
    include common::development
}

node default {
    include common
}
{{< /highlight >}}

You can do this with any number of things. A module can have any number of classes. That means if you wanted to separate even more things, you could easily do that.

In additional to modules, you can also continue to make changes individually to nodes. That means if you need Node.js on your laptop, but only want v0.10.26, you can just include that in the node without having all your development items.

### Bonus: Custom PHP Project Module

As a bonus, I've moved out my PHP specific items from my common module and included some usages. You can see them at this [Gist](https://gist.github.com/ivorisoutdoors/5e721a401506d96faea5).

## Hiera

Sometimes you need a module to install a different version of a package, or install in a different location. Instead of forking and making that change manually in a module, some modules support Hiera for changing these. You can read more about it in the Boxen [Readme](https://github.com/boxen/our-boxen#hiera).

For example, I can't use the `.dev` tld that dnsmasq uses by default at work, so instead I use `.localhost`. To change that, it's one line I have to add.

{{< highlight yaml >}}
# hiera/common.yaml
---
dnsmasq::tld: localhost
{{< /highlight >}}

No need to fork and make that change in the code.

## Hacking Further

Once you get the hang of Puppet, you can start to do some really cool things in Boxen. I wrote a [module](https://github.com/ivorisoutdoors/puppet-python) that installs [pyenv](https://github.com/yyuu/pyenv) to let you manage different versions of Python.

Let me know in the comments if you have any tips or tricks with Boxen, or any cool Boxen modules you've written.
