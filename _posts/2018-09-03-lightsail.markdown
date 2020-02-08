---
layout: post
title: Lightsail server setup
date: '2018-09-03 01:00:00'
tags:
- hosting
- aws
---

Since my initial [blog-server setup](https://libreengineer.com/the-server-basics-aws-ec2/), [Amazon Web Services (AWS)](https://aws.amazon.com/) have introduced a simple, low cost server option through [Lightsail](https://aws.amazon.com/lightsail/).

> Everything you need to jumpstart your project on AWS — compute, storage,
and networking — for a low, predictable price.

The lowest-cost Lightsail option is $USD3.50/month and cheaper than my current EC2 setup (t2.micro) which is $0.0116/hour or ~$8.352/month. Although the Lightsail option has half the memory (0.5GiB vs. 1.0GiB).

So given that Lightsail is designed to be simpler to setup and use, and is cheaper, I thought I'd use it as the base for the transition to a ghost blog. It's not certain, but the [ghost documentation](https://docs.ghost.org/docs/hosting#section-recommended-stack) looks like a 0.5GiB will be enough for the server (assuming this blog doesn't become a run-away success).

The process is amazingly simple and quick.
1. Open the [Amazon Lightsail console](https://lightsail.aws.amazon.com/ls/webapp/home/instances) (and sign-up for an account if required)
2. Select 'Create instance'
3. Select the instance location (e.g. Sydney)
4. Select the instance image (e.g. Ubuntu 16.04 LTS)
5. Select the instance plan (e.g. $USD3.50/month)
6. Select create

The instance is now being created and should be ready within a minute or so.
Once created the web-based terminal to the server can be selected from the instance information.

If everything has gone as expected, a simple terminal window should now be visible which a simple hello world prompt can be typed into.
```
$ echo hello world
hello world
```


