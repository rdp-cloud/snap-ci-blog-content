---
layout: post
title:  Continuous Deployment Strategies
description: Learn implementation techniques for various Continuous Deployment Strategies.
date:   2015-06-22
author: Ketan Padegaonkar
keywords: snap ci, continuous delivery, continuous integration, developer tools, github, continuous deployment, snowflake servers, phoenix servers, blue green servers, canary releases
categories: article deployments
index-image-url: screenshots/cd-strategies/continous-delivery.png
index-image-alt: 'Continuous Delivery loops'
---

Not very long ago, during the days of Perl/CGI and PHP (and even today); deployments involved *ftp*-ing your files to the webserver directory and running a bunch of migrations by shelling into the webserver.

Modern webapps and application servers have evolved quite a bit since then, but many developers continue to use  similar strategies to perform deployments. FTP has been replaced by **git pull** followed by **bundle install** or **npm install** and then restarting the appserver that you use.

Along with similar deployment strategies, many developers also continue to use the same servers for months and years on end. But even with the right patches, updates, etc, after a while it's easy to end up with a "snowflake server": a server with a delicate, unique configuration that does not lend itself to change. A solution to this is the "phoenix server." Here's a little more about both these concepts.

## [Snowflake servers](http://martinfowler.com/bliki/SnowflakeServer.html)

While this approach may work great for small webapps, it quickly falls apart when you're managing anything but a couple of servers.

It is generally difficult to set up a new server if your existing server has problems. If you're looking to scale by adding more servers, it's difficult to keep each server's software, configuration and services in sync. You may not be in a position to replicate a production (or production-like) environment for testing.

Over time, one loses track of the magic configuration files, packages and services that are installed on the production machines.


## [Phoenix servers](http://martinfowler.com/bliki/PhoenixServer.html)

Some these issues can be addressed by using a bit of configuration management tools like [chef](https://www.chef.io/), [puppet](https://puppetlabs.com/), or [ansible](http://www.ansible.com/home) among a few others.

These tools help avoid configuration drift, the sort of one-off changes that go unnoticed and undocumented.

An important point to note is that these tools only apply the configuration that they are asked to. If you applied any additional configuration outside of what these tools are aware of, or if you forgot to un-apply or remove some configuration, you'd be out of luck. This what [Ranjib Dey](https://github.com/ranjib) calls the [Accumulator Anti-Pattern](http://server.dzone.com/articles/infrastructure-tooling-anti).

A much better alternative is to tear down servers periodically and apply configuration changes every once in a while. This helps avoid and catch any configuration drift outside of configuration management tools. Eventually one can move to deploying new servers on every configuration change. This strategy is known as an [Immutable Server](http://martinfowler.com/bliki/ImmutableServer.html).

## How does this apply to Continuous Deployment?

Continuous Deployment requires that at a very minimum, you have:
* a solid foundation of tests that gives you confidence in your software
* a set of automation tools and scripts that give you confidence that your deployment will succeed, or rollback in case of issues

A few of the more popular continuous deployment strategies include blue/green deployments and canary releases, which I've briefly outlined below.

## Blue/green deployments

This is one of the simplest CD strategies to implement and get started with.

![blue green]({% asset_path screenshots/cd-strategies/blue-green.png %}){: .screenshot .big}

Blue/green is a technique for deployments where the existing running deployment is left in place. A new version of the application is installed in parallel with the existing version. When the new version is ready, cut over to the new version by changing the load balancer configuration.

This makes rollback really simple and gives time to make sure that the new version works as expected before putting it live.

## Canary releases

This is named after the "canary in a coal mine" metaphor. The metaphor originates from the times when miners used to carry caged canaries into the mines with them: if there were any dangerous gases (methane or carbon monoxide) in the mine, the canary would die before the gas levels reached levels hazardous to humans.

![canary releases]({% asset_path screenshots/cd-strategies/canary.png %}){: .screenshot .big}

Canary releasing is similar to blue/green, although only a small amount of the servers are upgraded. Then, using a cookie or similar, a fraction of users are directed to the new version.

This allows for the load and functionality of the site to be tested with a small group of users. If the application behaves as expected, migrate more and more servers to the new version until all the users are on the new version.

This technique can also be used to do some interesting multi-variant testing and performance testing.

## Next steps

In my next post, I'll get into the implementation techniques of blue/green deployments. A subsequent post will talk about implementing canary releases. Hope you will find this series very useful.

 
Snap CI © 2017, ThoughtWorks
