---
layout: post
title: Build and deploy huge static websites with Caddy
date: 2018-12-07 19:03
categories: en
tags: devops
---

In this post, I will show you how to host a big static website (5000+ pages) on a server with Caddy.

# Introduction

## First of all, don't do it!

There are very few cases where it makes sense to use such a setup.
Netlify is a great hosting provider for static websites, it is way simpler to setup, it will run your build scripts in no time, give you HTTPS out of the box, and CDN delivery.
All that for free! So really, try with them first.

In some cases though, you may reach Netlify's limits.
I for example experienced that building 20k+ pages prevents deploys from running through.
Even though building the pages itself takes 2 minutes or so, the deploys hang on the upload step.

## Why not host it on S3 ?

The next reasonable option would be to host the static website on S3.
It's a pretty common setup too and is well documented by AWS.

Because builds should be automated, I wanted to setup an AWS Lambda function that runs the build and uploads the files to S3.
After 2 days of headaches fighting with the AWS docs to understand how to make it work, I managed to finally run it.
Only to find out that the upload process from the AWS Lambda local storage to S3 is slow, or at least not fast enough.

I tried parallelizing the uploads using threads, but it was still too slow.
I managed to get to about 500 pages/minute but the Lambdas have a maximum timeout of 15 minutes so it's not enough.
In any cases, a build that takes more than 15 minutes would not have been satisfying.

I'm not an AWS expert, but the whole process was so frustrating that I decided to go old-school and host it on a server I control.

# Server setup

I chose to use Digital Ocean for hosting my server, but most of these instructions would be applicable to any other provider.
Digital Ocean has very nice interfaces and great docs, I am personnaly never disappointed.

This whole section is a 100% condensed copy-paste from this post on Digital Ocean's blog : [Deploying a Fully-automated Git-based Static Website in Under 5 Minutes](https://blog.digitalocean.com/deploying-a-fully-automated-git-based-static-website-in-under-5-minutes/).

If you're not familiar with these type of setups, I suggest you follow the blog post rather than my super-fast summary.

> &nbsp;
> 1. follow [this DO guide](https://www.digitalocean.com/community/tutorials/initial-server-setup-with-ubuntu-16-04) to setup a secure Ubuntu 18.04 droplet, **but skip the firewall step**
> 2. ssh into the server and install Caddy with these commands :
> ```sh
> # Download and extract Caddy :
> wget -O caddy.tar.gz "https://caddyserver.com/download/linux/amd64?plugins=http.git&license=personal"
> mkdir caddy
> tar vxf caddy.tar.gz -C caddy
> cd caddy
> # install the binary :
> sudo cp caddy /usr/local/bin
> sudo chown root:root /usr/local/bin/caddy
> sudo chmod 755 /usr/local/bin/caddy
> # allow it to listen on protected ports :
> sudo setcap 'cap_net_bind_service=+ep' /usr/local/bin/caddy
> # create the configuration directories and set proper permissions :
> sudo mkdir /etc/caddy
> sudo chown -R root:www-data /etc/caddy
> sudo mkdir /etc/ssl/caddy
> sudo chown -R www-data:root /etc/ssl/caddy
> sudo chmod 0770 /etc/ssl/caddy
> # install the Systemd service file :
> sudo cp init/linux-systemd/caddy.service /etc/systemd/system/
> sudo chown root:root /etc/systemd/system/caddy.service
> sudo chmod 644 /etc/systemd/system/caddy.service
> sudo systemctl daemon-reload
> ```
> 3. Write a basic config file for Caddy :
> ```apache
> # /etc/caddy/Caddyfile
> example.com {
>     tls you@example.com
>     internal /.git
>     gzip
>     redir 301 {
>         if {scheme} is http
>         /  https://{host}{uri}
>     }
>     git https://github.com/kamaln7/basic-static-website.git {
>         interval 300
>     }
> }
> ```
> 4. start Caddy
> ```
> sudo systemctl start caddy
> sudo systemctl enable caddy
> ```
> 4. If you own a domain name, you can add an DNS record of the A type that points to your droplet's IP, or follow [this short guide](https://www.digitalocean.com/docs/networking/dns/how-to/add-domains/) to use DigitalOcean's own name servers.
> 5. Create a Firewall [here](https://cloud.digitalocean.com/networking/firewalls) and allow SSH, HTTP, and HTTPS inbound traffic

So if everything worked out so far, you should be able to access your website on your own domain, and see this :

![basic static website](/images/basic-static-website.png)

In case it does not work properly so far, you can run this command.
It should allow you to see logs and exceptions from Caddy.

```
tail -f /var/log/syslog
```

# Use a dynamic build system

We will now go through a series of changes in order to use a dynamic build system.
This means that a script will be re-executed upon each git push to the repository.
This script will build the HTML pages that will then be served as a static website.

For the purpose of this article, I have created a very simple [Static Website Builder](https://github.com/adipasquale/huge-static-website-builder).
It's a 50-lines Python3 script, that queries a [dummy API](https://jsonplaceholder.typicode.com/) for 5000 photos, and builds an HTML page for each, plus an index listing them.

![huge static website screencast](/images/huge-static-website-screencast.gif)

Let's go and deploy it on our server !

First, fork [huge-static-website-builder](https://github.com/adipasquale/huge-static-website-builder), go in Settings > Webhooks and create a new webhook with the `https://example.com/github_webhook` url. You can use the `uuidgen` CLI tool to generate a random secret :

![Create a GitHub Webhook](/images/github-webhook.png).

Don't worry, the webhook will fail upon creation, because we have not implemented yet.

Let's now make a few changes in the Caddyfile configuration. SSH into the server and update the `/etc/caddy/Caddyfile` :

```apache
# /etc/caddy/Caddyfile
example.com {
    tls you@example.com
    internal /.git
    gzip
    redir 301 {
        if {scheme} is http
        /  https://{host}{uri}
    }
    git https://github.com/YOUR_GITHUB_HANDLE/huge-static-website-builder /var/code/huge-static-website-builder {
        interval 300
        hook /github_hook [YOUR_WEBHOOK_SECRET]
        then python3 /var/code/huge-static-website-builder/build.py
    }
    root /var/www
}
```

Here is what we have changed :
- We have switched the GitHub repository from the old static one to the new dynamically built one you just forked.
- We have also specified where the GitHub repo should be fetched : `/var/code/huge-static-website-builder`
- We have added a `hook` option in order to receive the push events from GitHub.
- We have added a call to the `build.py` script from the fetched repo in the `then` option.
It will be ran upon each new pull.
- Finally, we have declared that the server's root is `/var/www`.
This means that the server will serve static files from there only.

We still have to perform a few steps in order for this setup to work.
Again, ssh into the server and run these commands

```sh
sudo apt-get install python3 ?
# Create the new folders and set correct permissions
mkdir /var/code/
mkdir /var/www/
sudo chown www-data:www-data /var/code/
sudo chown www-data:www-data /var/www/
# Restart Caddy
sudo systemctl start caddy
```

You should now be able to access

# Extra : API to trigger rebuilds
