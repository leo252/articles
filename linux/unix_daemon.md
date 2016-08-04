# UNIXDAEMON

[Original URL](http://www.unixdaemon.net/tools/puppet/simple-puppet-module-testing-with-dockunit.html)

> Tue, 20 Oct 2015 Simple Puppet Module Testing with Dockunit I've recently begun to look at replacing as much of my custom puppet tooling as possible with third-party, open source, code. As part of...

Tue, 20 Oct 2015

## [Simple Puppet Module Testing with Dockunit]()

I've recently begun to look at replacing as much of my custom puppet tooling as possible with third-party, open source, code. As part of this I'm planning to update my old libvirt testing infrastructure with more modern tools, and this seems to be leading me heavily down the docker path.

One of the simpler, but less known, solutions in this space seems to be [Dockunit](https://www.npmjs.com/package/dockunit), which bills itself as "Containerized unit testing across any platform and programming language" and is remarkably simple to get started with. I decided to try it on a Fedora host and use it to execute rspec tests included in a tiny puppet module on both Centos and Ubuntu.

Assuming you already have docker installed and running the addition of dockunit to your system is quick and easy, although dockunit is written in node so you'll now have that run time installed too.

```
# check docker is up and running
$ systemctl status docker
$ docker info

# install npm so dockunit can run
sudo dnf install npm

# do a local install of the dockunit package
npm install dockunit
```

Now dockunit and its dependencies are installed you need to add a Dockunit.json file inside the directory containing the module. For our first test we'll run the specs inside a Centos container and turn the verbosity up so we can follow along.

```
$ cd $module

$ cat dockunit.json
{
 "containers": [
 {
 "prettyName": "Centos 7",
 "image": "centos:7",
 "beforeScripts": [
 "yum install -y rubygems rubygem-bundler ruby-devel make rubygem-json gcc",
 "bundle install"
 ],
 "testCommand": "bundle exec rake spec"
 }
 ]
}

# the local node install didn't put the binary in my path
$ ~/node_modules/dockunit/bin/dockunit --du-verbose

... snip ...
Testing on container Centos 7

Pulling docker image: centos:7

... snip ...

Running "bundle exec rake spec" on container Centos 7
/usr/bin/ruby -I... --pattern spec/\{classes,unit,integration\}/\*\*/\*_spec.rb --color

nettools
 should compile into a catalogue without dependency cycles
 should contain Package[net-tools]

Finished in 1.12 seconds (files took 0.74765 seconds to load)
2 examples, 0 failures

1 container(s) passed
```

Although I've heavily snipped dockunits output you can still get an idea of the basic flow, including the results from our specs. As helper tools go this one requires a very small ramp up and if you're looking to test basic modules, gems or pip packages then dockunit might be all you need for quite a while. Now we've got our first use case running let's be a little more ambitious and try to do a run on Centos as before but also add Fedora 22 and Ubuntu 15.04.

```
$ cat Dockunit.json 
{
 "containers": [
 { 
 "prettyName": "Centos 7",
 "image": "centos:7",
 "beforeScripts": [
 "yum install -y rubygems rubygem-bundler ruby-devel make rubygem-json gcc",
 "bundle install"
 ],
 "testCommand": "bundle exec rake spec"
 },
 {
 "prettyName": "Fedora 22",
 "image": "fedora:22",
 "beforeScripts": [
 "dnf install -y rubygems rubygem-bundler ruby-devel make rubygem-json gcc",
 "bundle install"
 ],
 "testCommand": "bundle exec rake spec"
 },
 {
 "prettyName": "Ubuntu 15:04",
 "image": "ubuntu:15.04",
 "beforeScripts": [
 "apt-get update",
 "apt-get install -y bundler",
 "bundle install"
 ],
 "testCommand": "bundle exec rake spec"
 }
 ]
}

# run the tests under all containers
$ ~/node_modules/dockunit/bin/dockunit --du-verbose

# run the tests against a single container
# --du-container is the container index, starting from Centos at 0
$ ~/node_modules/dockunit/bin/dockunit --du-verbose --du-container=1
```

The examples here are a little slow due to pulling in all the dependencies on container creation but that's easily fixed by creating a reusable image with the testing dependencies and storing it your own docker repository. Running the dockunit command again will now run the testes under each of the three containers, unfortunately in serial, so it can take a little while. Still, dockunit is a nice, self-contained tool that can be an excellent jumping in point for someone who wants to use containers for simple testing but has no supporting infrastructure and wants to as simply as possible.

Posted: 2015/10/20 18:09 | _[/tools/puppet](http://www.unixdaemon.net/tools/puppet/)_ | [Permanent link to this entry](http://www.unixdaemon.net/tools/puppet/simple-puppet-module-testing-with-dockunit.html)

Copyright © 2000-2014 Dean Wilson :: [RSS Feed](http://www.unixdaemon.net/cgi-bin/blosxom.pl/index.rss "Unixdaemon RSS Feed")