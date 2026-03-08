# PHP Documentation - 03/08/2026

## Overview

This document records the process of installing and configuring PHP.

## Environment
- VM Provider: Google Cloud
- OS: Ubuntu 24.04 LTS x86/64

## Process

### Step 1 - Install PHP
First I updated my package lists to ensure everything was up to date before installing new software.

```
sudo apt update
sudo apt upgrade
```

Then I installed the php and libapache2-mod-php packages as directed by the text's instructions.

```
sudo apt install php libapache2-mod-php
sudo systemctl restart apache2
systemctl status apache2
```

When asked to continue, I typed y. Everything seemed to have installed without errors. I restarted apache2, and when I checked the status, apache was active. Everything seems to be functioning well.

```php -v```

Checking the version of php installed, I have version 8.3.6, updated in Jan 2026, so I believe it is the most current version.

I had no difficulties in installing the packages or checking their status.

### Step 2 - Check Installation

```
cd /var/www/html/
sudo nano info.php
```

I moved to the document root with no problems, then created the info.php file. I added the required text:

```
<?php
phpinfo();
?>
```

And saved and closed. I then tried to open up the file through my browser, but when typing the address out, something went wrong and it timed out. I then copy-pasted the public IP address and added the extension, and that resulted in a page that looks like what is expected.

I then deleted the page.

```
sudo rm /var/www/html/info.php
```

Returning to the page in Chrome resulted in 404 Not Found, so I believe I was sucessful.

### Step 3 - Basic Configurations

```
cd /etc/apache2/mods-available/
sudo cp dir.conf dir.conf.bak
sudo nano dir.conf
```

Following the instructions, I moved to the dir.conf file in the apache2 mods-available directory, creating a backup of the file I wanted to edit in case something went wrong, and then entering the file to change it to prioritize index.php over index.html.
After making this change, I backed out and ran a configuration test.

```
apachectl configtest
```

I received a Syntax OK message back, then reloaded apache and checked the status.

```
sudo systemctl reload apache2
systemctl status apache2
```

Status came back active and looked the same as when I checked the status after installing php, so I believe everything is running smoothly.

### Step 4 - Create an index.php file

```
cd /var/www/html/
sudo nano index.php
```

I returned to the Apache document root and open an index.php file.
I copy-pasted the code provided into the nano document, saved, and closed out.
After issues that I will document below, I was able to confirm that the public IP address of my VM displayed a browser and OS checker that correctly identified that I was using Chrome and Windows.

## Issues Encountered
1. After navigating to /var, I was unsure how to return to the directory that has my respository. I ended up closing and rebooting the VM. I took note of the ~ at the end of the machine name, and then I remembered that cd ~ can be used to return to that directory. I checked that this was the case by navigating back to /var and then back to my directory.

2. To access my public IP address, I have been using the the external IP link that's on the VM instance in Google Cloud. When I click it, it immediately takes me to the index.html extension, showing me the "welcome to my website" page we created last week. I was confused about this, as I thought I'd done everything correctly.
I manually added /index.php to the URL, and that brought up the browser/OS detection page.
I went back into the apache mod directory and reopened dir.conf to make sure that the changes I'd made to put index.php in front had been saved. They were, so I closed that file.
I considered deleting the backup file, wondering if that was interferring with the prioritization, but before doing that, I decided to try reloading the page through my browser. When I did, it showed the browser/OS Detection page.
When I copy-paste the IP address into a new tab, it immediately brings up the browser/OS page without an extension in the URl.
This leads me to believe that using the hyperlink provided by Google Cloud will prioritize index.html even if index.php is prioritized when accessing the page otherwise.
Thankfully, this means I did not mess up the configuration, I just need to access my public IP address manually instead of relying on the hyperlink.

## Reflection

While I wasn't entirely certain what I was doing while initially entering the code, documenting and explaining what I was doing as I was doing it and reading the instructions  did help me come to a better understanding of how I was using the system (I think).
Set-up was fairly smooth aside from the last hiccup, so I would consider this a successful assignment.
