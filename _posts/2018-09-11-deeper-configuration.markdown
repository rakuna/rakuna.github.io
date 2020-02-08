---
layout: post
title: Deeper configuration
date: '2018-09-11 13:13:30'
tags:
- aws
- blogging
---

Now that the [blog](https://libreengineer.com/installing-ghost/) is set up and running, lets have a look at some deeper / more advanced configurations.

We'll cover:

1. Automated software updates
2. Website address redirections
3. Advanced e-mail sending
4. Server-side SSL settings

## [Automatic updates](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)

Since we're not needing to connect to the back-end of the server in normal usage of the blog, it's a good idea to configure automatic updates.

### [Security](https://help.ubuntu.com/community/AutomaticSecurityUpdates#Using_the_.22unattended-upgrades.22_package)

It's only a matter of time until a security flaw is found and then patched.  
By installing security updates as soon as they're available we'll be staying as secure as possible from all future flaws.

A simple way to achieve this is with the **unattended upgrade** package.

Install unattended-upgrades:

    $ sudo apt-get install unattended-upgrades

Enable unattended-upgrades through the dynamic configuration environment (_The default option is a daily check and install of distribution and security updates._):

    $ sudo dpkg-reconfigure --priority=low unattended-upgrades

Unattended upgrades should now be correctly configured.

To confirm that everything is configured correctly:

    $ apt-config dump APT::Periodic::Unattended-Upgrade

Which should output something like:  
`APT::Periodic::Unattended-Upgrade "1";`.  
Which is stating that Unattended-Upgrade will run every (1) day(s).  
If the number is "0" then unattended upgrades are disabled.

Unattended-upgrades can also be configured to send an e-mail when there is a problem in updating a package.  
To enable this functionality edit the configuration file:

    $ sudo nano /etc/apt/apt.conf.d/50unattended-upgrades

Then uncomment the `Unattended-Upgrade::Mail` line and insert the e-mail address we want to receive these notifications.

### [Non-security](https://help.ubuntu.com/lts/serverguide/automatic-updates.html.en#update-notifications)

We may also want to know when other, non-security, updates are available, so that we can install them at a suitable time.  
We can do this with the **apticron** package.

Install apticron:

    $ sudo apt install apticron

To enable apticron we need to give it a meaningful e-mail address.  
This is done by changing the configuration file

    $ sudo nano /etc/apticron/apticron.conf

In particular, by changing the `EMAIL=""` line to use our desired e-mail.

To test apticron is working as expected (when you know there are some updates available) simply run apticron:

    $ sudo apticron

## Simple redirections

### from non-secure to secure / http to https

Non-secure/plain-text (http) connections are being seen more and more as an undesireable aspects to internet access:

- [2014: Google begins using https as a factor in search rankings](https://webmasters.googleblog.com/2014/08/https-as-ranking-signal.html)
- [2017: Mozilla builds more verbose dangers of http into Firefox](https://blog.mozilla.org/security/2017/01/20/communicating-the-dangers-of-non-secure-http/)

Given that we've already setup secure (https) connections to our website (in the Ghost installation) let's make this the default (and only) way to access the website.

A simple way to do this is to tell nginx (our webserver) to redirect any http traffic to use https.

This is done by changing the nginx configuration files, which are found in the Ghost installation directory `...ghost/system/files/`

We need to edit the http file `[hostname].com.conf` and make the following change:

Replace the (first) location block (leaving the second _.well-known_ location block, which is used for renewing certificates) with:  
`return 301 https://[hostname].com$request_uri;`

Once these changes are made we want to check they have valid syntax and then tell nginx to use them.

    $ sudo nginx -t
    $ sudo nginx -s reload

We should now be redirected to https whenever we access the http website.

### from complex to simple / www to non-www

For simplicity purposes, we'd like users who access our blog from `www.[hostname].com` and `[hostname].com` to have the same experience.  
As above, with http to https redirection we can redirect from www to non-www (or vice versa).

This tutorial is adapted from the [Ghost documentation](https://docs.ghost.org/v1.0.0/docs/cli-knowledge-base#section-ssl-for-additional-domains)

Create the _www_ domain:

    ghost config url https://www.[hostname].com

Generate the SSL for the _www_ domain:

    ghost setup nginx ssl

We've now got our _www_ and _non-www_ domains configured with SSL.  
Now lets tell Ghost to use our _non-www_ domain again:

    ghost config url https://[hostname].com

Finally, we'll need to edit the nginx config files for our _www_ domain to redirect to our _non-www_ domain.  
Edit both `/system/files/www.[hostname].com.conf` and `/system/files/www.[hostname].com-ssl.conf`, making the following change:

Replace the (first) _location_ block (leaving the second _.well-known_ location block, which is used for renewing certificates) with:  
`return 301 https://[hostname].com$request_uri;`

Once these changes are made we want to check they have valid syntax and then tell nginx to use them.

    $ sudo nginx -t
    $ sudo nginx -s reload

We should now be redirected to `[hostname].com` whenever we access `www.[hostname].com`

## Advanced e-mail sending

By default Ghost uses basic e-mail sending capabilities for user account generation and back-up/password resets. However all the e-mails it sent to my account were marked as spam. So I thought it would be worthwile setting up a more sophisticated e-mailing service, that is less likely to end up in spam.

### Which e-mail service?

Since I've been using Amazon Web Services (AWS) for my other blog-related services, I figured the AWS Simple E-mail Service (SES) was worth a look into.

A quick look at the SES pricing (as at September 2018) shows:

- $0.10 per 1,000 e-mails sent and received.
- $0.12GB of data in the messages sent.

Which should cost a grand total of $0.01 per month based on my expected usage (send and receive \< 100 e-mails per month).

### SES sign-up

Sign-up for SES is a relatively straight forward affair.

1. Access SES from the AWS Console: [https://console.aws.amazon.com/ses/](https://console.aws.amazon.com/ses/)
2. Verify a domain
  - Add the configuration settings to your DNS  
(or let AWS do it for you if using Route53)
3. Create SMTP credentials
  - Save these for later

### Configuring Ghost to use SES

The [Ghost documentation](https://docs.ghost.org/v1.0.0/docs/mail-config#section-amazon-ses-a-id-ses-a-) has a very straightforward guide to using SES as the e-mail service.

Simply edit the contents of the _mail_ block in your ghost configuration file to reference SES.  
The configuration file can be found in the root directory of your Ghost installation:  
`...ghost/`

Change:

    "mail": {
    "transport": "Direct"
    },

To:

    "mail": {
        "transport": "SMTP",
        "options": {
            "host": "YOUR-SES-SERVER-NAME",
            "port": 465,
            "service": "SES",
            "auth": {
                "user": "YOUR-SES-ACCESS-KEY-ID",
                "pass": "YOUR-SES-SECRET-ACCESS-KEY"
            }
        }
    },

Replacing:  
_YOUR-SES-ACCESS-KEY-ID_ and _YOUR-SES-SECRET-ACCESS-KEY_ with your SMTP credentials, and _YOUR-SES-SERVER-NAME_ with the SES server address.

Finally restart Ghost

    .../ghost$ ghost restart

If Ghost fails to restart (like it did for me because I missed some commas), check the configuration file for any typos or missing commas and try again.

### Testing e-mail sending

We should By now we should have Ghost restarted with our updated configuration file.

1. Access the Ghost admin interface `<website>/ghost`.
2. Access the labs area
3. Select test e-mail configuration  
If everything is working as expected, an e-mail should arrive in your e-mail inbox (the one tied to your author account).

In my case, this did not go as expected.  
It turns out that SES places new users into a _sandbox_ which is used to limit and avoid spam on the platform (a good thing), but which makes it harder to get setup and going (a bad thing).

A simple work around for now, is to verify your account e-mail within SES, so that SES will send e-mails to it, and we can confirm the Ghost test e-mail is working.

Which successfully sent the e-mail and showed that the Ghost configurations were correct.

### Getting out of the SES sandbox

Now that we know we're in the sandbox, let's figure out how to get out.

#### Requirements

There is an [AWS form](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html) that needs to be filled out, so that AWS can understand the intended use and avoid spam from their service.  
The main requirement is to setup processes to handle:

- e-mail bouces
- complaints (e-mails flagged as spam by the receiver)

#### [Handling bounce backs and complaints](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/configure-sns-notifications.html)

The simplest way to achieve this, within AWS, is to setup a Simple Notification Service (SNS) topic that SES can post to, and which SNS will then deliver to subscribers.  
In my case the only subscriber will be my personal e-mail which will be forwarded the details of any e-mail bounces and complaints.

Setting up SES and SNS:

1. Login to AWS SES and view your verified domain
  - Verify your domain if it is not already been verified
2. Open the _Notifications_ area
3. Select the _Edit Configuration_ button
4. Create an SNS topic, or
5. Select an existing SNS topic option for **Bounces** and **Complaints**
  - _e.g. NotifyMe_
6. Select Include original headers
7. Save the configuration
8. Open AWS SNS
9. Ensure your e-mail is subscribed to the SNS topic

We should now have a process that will correctly handle bounces and complaints.

To test that everything is working as expected we can use the _Send a Test Email_ option in the AWS SES Domains page and the [AWS Mailbox Simulator](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/mailbox-simulator.html) which provides a list of e-mail addresses that will respond in a known way.  
In our case, we want to use:

- [bounce@simulator.amazonses.com](mailto:bounce@simulator.amazonses.com)
- [complaint@simulator.amazonses.com](mailto:complaint@simulator.amazonses.com)

A quick test e-mail to each of these addresses should lead to a bounce and complaint e-mail to our e-mail.

#### Requesting to leave the sandbox

Now that we've satisfied the requirements (and because we don't intend to send spam) let's submit the [request to leave the sandbox](https://aws.amazon.com/ses/extendedaccessrequest/)

AWS now needs to review our request.

12 hours later and we've been successfully moved out of the sandbox.

## Server-side SSL

Applying security patches is a good way to help protect the applications within a webserver. However we also need to consider security for the traffic to and from our webserver.  
We've already configured https to be the default form of traffic, so the next question is **which sort(s)** of https we wish support.  
This is where server-side SSL configurations come into play.

Mozilla (the people behind Firefox) have a nice [SSL configuration generater service](https://mozilla.github.io/server-side-tls/ssl-config-generator/) which can help determine appropriate security configurations based on your end-users ( **Modern** , **Intermediate** , **Old** ).

At first it sounds weird that there is more than one option.  
There should only be one recommended security setting right‽  
Upon closer inspection, the reason there is more than one option is to do with the landscape of end-users. Some have very old software, which only supports older (and less secure) security protocols. Others may have modern software which support the latest (and most secure) security protocols.

Depending on the purpose of the website or service, the users with old software may be part of our desired audience. In which case, choosing to not support the protocols that they are able to use will mean that we are implicitly excluding them from our service.

In our case, the intended audience is assumed to be using **modern** software, with the oldest compatible clients being:  
2013. Firefox 27, Chrome 30, IE 11 on Windows 7  
2014. Android 5.0, Java 8  
2015. Edge, Safari 9  
2017. Opera 17

Now let's setup nginx to facilitate this support.

Having a read through the Ghost-generated nginx files, we can see that they call an nginx _snippets_ file in `/etc/nginx/snippets/`, which contains the majority of the SSL configuration settings for nginx.  
We'll incorporate our SSL changes by editing the nginx SSL snippets file to incorporate the Mozilla recommendations.

Once these changes are made we want to check they have valid syntax and then tell nginx to use them.

    $ sudo nginx -t
    $ sudo nginx -s reload

To confirm that our SSL settings are working as expected, we can make use [SSL Labs](https://www.ssllabs.com/ssltest/) which provides a grade reflecting the security settings of a website.

After our changes have been applied, our grade is returning as **A+** which suits our purposes.

It's interesting to note however, that we received an A+ grade, but a score of approximately 90%. It is [possible](https://zurgl.com/how-to-get-a-100-score-on-ssl-labs-nginx-lets-encrypt/) to get to 100%, but as we don't have complete control of our end-users' software, we'll stick with the modern configuration.

## Final thoughts

There we go, some deeper configurations to provide a more secure and simpler blog, and to avoid our e-mails being marked as spam.

