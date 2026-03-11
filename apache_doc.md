# Apache Documentation - 03/10/2026

## Overview

This document describes the process of installing Apache, confirming its status, and using Apache to create a very basic website.

## Environment
- VM Provider: Google Cloud
- OS: Ubuntu 24.04 LTS x86/64

## Process

### Step 1: Install Apache2

After ensuring that my system is up to date through sudo apt update and sudo apt upgrade, I search for the software for Apache using the package name apache2. Piping the results through the head command limited the results from that search to the first few.

```
apt search apache2 | head
```

**Note:** | is the symbol for piping, aka feeding the results of one command into another command.

```
apt show apache2
sudo apt install apache2
```

apt show is used to confirm that this package is the one we want, and sudeo apt install installs the package.

To check that the server is running correctly, I ran the command:

```
systemctl status apache2
```
The status for the Active field came back active, and the status for Loaded came back enabled, meaning that everything is running as it should be.

### Step 2: Create a Webpage

I chose to use a graphical browser to view the default webpage, using the External IP address link in Google Cloud. I did see the Apache2 Ubuntu Default Page, so things were functioning as intended.

To create the webpage, I navigated to the document root (the place that provides to part of the file system) and located a file titled index.html. I renamed the file to preserve it, and then created a new page with that name to create a new page at the address.

```
cd /var/www/html/
sudo mv index.html index.original.html
sudo nano index.html
```

I do not know any HTML, unfortunately, so I copy-pasted the code provided, saved the nano file, exited out, and then reloaded the public webpage. The new content had appeared!

## Reflection

Everything went smoothly installing Apache, confirming its status, and then creating a basic webpage. 
It may be stupid, but given that most of my experience website building is through "website building" sites like Wix (or rather a similar site that I used for free to make school projects that's name is escaping me), it hadn't really occurred to me that webpages are built and hosted on specific machines and accessed through an IP address. Obviously I know that webpages are hosted on servers, but I don't I'd ever pictured that server as being something like my laptop - I always pictured server farms, a square box of a machine. Having this experience helped to change that picture in my head and better understand a fundemental aspect of the internet.

