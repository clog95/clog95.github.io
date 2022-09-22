---
title: "How to install your own Ghost Blog on Google Cloud Platform"
description:
date: "2022-09-20"
slug: "how-to-install-your-own-ghost-blog-on-google-cloud-platform"
image: "install-ghost-blog-on-google-cloud-platform.jpg"
categories:
    - Web Development
---

## Overview

In this tutorial I will show you how to self-hosting a Ghost Blog on Google Cloud Platform (GCP) by deploying a VM Instance install Ubuntu 18.04 LTS or 20.04 LTS server.

## Prerequisites

To follow this tutorial, you will need:

- Your own valid GPC account.
- A registered domain name.

> Note: Before getting started, you should set up a DNS A record from your domain, pointing to the server's IP address on VM Instance. This must be done so that SSL can be automatically configured.

## 1. Create a VM Instance and set up a static IP.

### Create a VM Instance

If you have owned a valid GPC account, now you can create a VM Instance with boot disk is Ubuntu 20.04 LTS or Ubuntu 18.04.

When your VM Instance is created, you can see the name and an External IP address. Because the External IP address is changed every time you startup, this will result in some troubles later so you need to set a fixed External IP address.

### Set a static IP

You can see in this video how to create a VM Instance and assign a static IP address for it:

{{< youtube as_MiZen8RY >}}

Finally, probably take a little time until your VM Instance is updated, backward your VM Instances page you can see your fixed External IP address corresponding, it not changed when you startup the server.

## 2. Pointing a DNS A record from your own domain to the server's static IP address.

Because the source code and database of your Ghost blog will be installed on Ubuntu server that you have installed on your VM Instance. To users can see your site on the Internet via your domain name, you need a DNS A record of the domain that points to the static IP address on the server.

Here I used the domain service of Google, the processing is a little bit different from other domain services. You can search on Google the way that manages DNS and how to create and point a DNS A record to.

- I go to [the Google Domains page](https://domains.google.com/), here you can see I have purchased some domains.
- I will use the domain [clog95.com](https://clog95.com/), now click on it.
- On the left side, I click on DNS to go into the DNS settings page.
- On this DNS page, you can see a DNS A record exists, now I will remove it then create a new A record.
- Here I create **a record** with Type is **A** and **Data** is **the static IP address** on the server, the **Host name** and **TTL** options leave as default. You can cover [the DNS A record here](https://www.cloudflare.com/learning/dns/dns-records/dns-a-record/).

## 3. Set up a Firewall with UFW on your server

I will perform this processing via command line, so I need to connect to the server via SSH. Now click on SSH that corresponds with the server you need to connect to, if successful a Terminal will appear and you can start to take some commands.

First , update and upgrade packages on your server.

```shell
sudo apt update
sudo apt upgrade
```

Checking the UFW status, default is inactive

```shell
sudo ufw status
```

Let's set the UFW to the default, deny all incoming and allow all outgoing connections.

```shell
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

Lists the UFW apps

```shell
sudo ufw app list
```

Allowing SSH connections, you need take this action to connect to the server via SSH later

```shell
sudo ufw allow OpenSSH
```

Checking the UFW status again, it is still inactive

```shell
sudo ufw status
```

Enabling UFW, with this command will appear a question that says: *"Command may disrupt existing ssh connections. Proceed with operation (y|n)?"* - you press **y** then **Enter** to agree

```shell
sudo ufw enable
```

Checking the UFW status again, now it is active and you can see OpenSSH is allowed

```shell
sudo ufw status
```

Now, exits your SSH connect

```shell
exit
```

## 4. Install Nginx

Ghost uses Nginx for web server, now install it

```shell
sudo apt install nginx
```

Now, let's list the UFW apps and you can see **Nginx Full** in this list

```shell
 sudo ufw app list
```

You need adjust the firewall to allow access to the web server, to do that simply allows HTTP and HTTPS connections by use UFW

```shell
sudo ufw allow "Nginx Full"
```

Checking the UFW status, now you can see **Nginx Full** is allowed

```shell
sudo ufw status
```
## 5. Install Mysql

Ghost uses Mysql server to manage your database, now install it:

```shell
sudo apt install mysql-server
```

Now you need to login to MySQL to update the root user with its password due to Ghost-CLI will use it to access MySQL and create a database for your Ghost Blog. Take a few extra steps:

```shell
# Login to MySQL
sudo mysql

# Now update the password for your root user
# Replace 12345 with your chosen password
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '12345';

# Exit MySQL
quit
```

## 6. Install Node.js

Naturally Ghost Blog is written by Node.js, and so you need to install it:

```shell
# Add the NodeSource APT repository for Node 16
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -

# Install Node.js
sudo apt install nodejs

# Verify the version of Node.js
# You should have version 16 of Node.js installed
node -v
```

## 7. Install Ghost-CLI

Ghost-CLI is a command line tool to help you get Ghost installed and configured for use, quickly and easily:

```shell
sudo npm install ghost-cli@latest -g
```

## 8. Install Ghost Blog

Once your server is correctly set up and Ghost-CLI is installed, it's time to go to the end of the install process by taking some extra steps to install and run your Ghost Blog on the Internet environment.

### Create a directory

Following some instructions and commands below to create a directory for your Ghost Blog with its own user and permissions is proper setup:

```shell
# Create directory: here I named clog95.
# You can change clog95 to whatever you like.
sudo mkdir /var/www/clog95

# Set directory owner of clog95 is current user.
sudo chown -R $USER:$USER /var/www/clog95

# Set the correct permissions for clog95 directory.
sudo chmod -R 775 /var/www/clog95

# Go into the clog95 directory to install Ghost Blog.
cd /var/www/clog95
```

### Run the install process:

Now inside the directory you just created, run the following command to start the install process:

```shell
ghost install
```

**Note:** After running the install command above, the CLI will ask you a number of questions to configure your site and you must answer it to run your site.

#### Blog URL
Enter your domain name with HTTP or HTTPS protocol. Here I want Ghost-CLI is automatically set up SSL for me so I will use HTTPS protocol for my domain: https://clog95.com

#### MySQL hostname
Because we have installed MySQL on the same server with Ghost blog, so you just simply press Enter to use the default value is localhost.

#### MySQL username and password
In the install MySQL section Above, I have set up **the root user** with its password is **12345**. So I just need supply **root** for MySQL username and **12345** for password (pay attention: password is hidden when you press on keyboard)

#### Ghost database name
Enter the name of your database, Ghost-CLI will automatically set up for you. Here I name it is clog95 (change clog95 to one that that you want)

#### Set up a ghost MySQL user?
You just need press **y** then **Enter**, with this you have agreed to Ghost-CLI to create a custom MySQL user that can only access/edit your new Ghost database and nothing else.

#### Set up Nginx
Just need press **y** then **Enter**, Nginx will automatically set up your site to be viewed on the Internet environment.

#### Set up SSL?
If you want a SSL certificate for your domain and you have already pointed your domain to the static IP address of your server. Now just press **y** then Enter, Ghost-CLI can automatically set up a SSL certificate for you using Let’s Encrypt.

#### Enter your email
When you set up an SSL certificate, it will require an email address so that you can be kept informed about your SSL certificate such as expired and renewal.

#### Set up systemd?
Just press **y** then **Enter** to agree to set up systemd that is manager tool to keep Ghost blog running smoothly.

#### Start ghost?
Now, press **y** then **Enter** to make your site work.