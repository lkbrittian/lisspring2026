# Installing Omeka - 4/12/2026

## Overview

This document describes the process needed to install and configure Omeka.

## Environment

- VM Provider: Google Cloud
- OS: Ubuntu 24.04 LTS x86/64

## Process

I am begining this after a very long and painful day of removing and reinstalling WordPress (twice!) to fix whatever it is that I broke in the first go around and expanding the storage of my machine and having to reset the IP address (again). I'm still not sure what the original problem was, but my fingers are crossed that now that it seems stable it will not happen again. I completed the first couple steps of this progess before realizing something was wrong with WordPress and I will endevour to provide accurate notes of what I accomplished rather than writing them in real time.

### Step 1 - Complete Prerequisites

First, I made sure my system was completely updated with the typical commands.

```
sudo apt update
sudo apt upgrade
sudo apt autoremove
sudo apt clean
```

I then double-checked that the versions of PHP and MySQL that I had checked when installing WordPress met the system requirements for Omeka (they did).

Next, I installed the packages and modules needed for Omeka to create thumbnail images and rewrite urls.

```
sudo apt install imagemagick
sudo a2enmod rewrite
```

Upon being prompted I restarted apache2, then ran a system check to make sure everything was functioning.

```
sudo systemctl restart apache2
sudo systemctl status apache2
```

### Step 2 - Create a New User and Database

Following the documentation from my installation of MySQL and WordPress, I was able to log into MySQL as the root user, create a new user under the name of "omeka", create a database called "omeka", and grant privileges to the omeka user for the omeka database.

```
sudo mysql -u root
create user 'omeka'@'localhost' identified by 'XXXXXXXXXX';
create database omeka;
grant all privileges on omeka.* to 'omeka'@'localhost';
```

### Step 3 - Download and Unzip Omeka

I will admit, even though I knew it didn't look right as it wasn't a .zip file, I did try using the wget command on the provided download page link. Predictably, that did not work, and I then went to the page and right-clicked the Get Omeka button to get the .zip file link.

```
cd /var/www/html/
sudo wget https://github.com/omeka/Omeka/releases/download/v3.2/omeka-3.2.zip
```

I moved to the document root directory, downloaded the correct file, and then unzipped it into the correct directory.

```
sudo unzip omeka-3.2.zip
```

I then renamed the directory using the mv command to rename it.

```
sudo mv omeka-3.2.zip omeka
```

### Step 4 - Update Database Credentials

Now that the omeka directory is unzipped, I navigated into the directory, located the db.ini file, and updated it with creditionals for the created omeka database and database user (which I will not be giving here for obvious security reasons).

```
cd /var/www/html/omeka
sudo nano db.ini
```

### Step 5 - Update Permissions

Since I was still in the Omeka directory, all I had to do was run the correct chmod command. I verfired with Candace, who has been a lifesaver, that that was the only command needed. I then restarted apache2, then verified it was operating correctly.

```
sudo chmod -R g+w *
sudo systemctl restart apache2
sudo systemctl status apache2
```

### Step 6 - Fix the Configuration of Apache2

The next step was to go to the myipaddress/omeka to complete the setup for Omeka in the browser, but like many of my fellow classmates, I found an error saying mod-rewrite was not enabled despite nowing for a fact that it was. Thankfully, my brilliant classmates had already confronted this problem and had many solutions for me to try.

I first tried editing the .htaccess file in the Omeka directory with gibberish at the top to test if it was an Omeka problem or an Apache2 problem. When I refreshed the page in the browser, I got the same error, which indicated that it was an Apache error. I erased the gibberish I wrote, backed out of it, and then based on the suggestions of Candace and a forum post I found while googling the problem, I switched to the apache2.conf file, then pulled up the code I used when adding a section for the cataloging module, changing the name to /var/www/html/omeka, and changing the AllowOverride to All. I saved it, then refreshed the page in my browser, and that got it!

## Reflection

Save for the snag at the end with the apache2 configuration, nothing about installing Omeka was particularly difficult. I was able to follow my documentation and the textbook instructions for installing MySQL and Wordpress (and the experience of reinstalling WordPress twice) to set everything up for Omeka. My biggest problems were actually with WordPress itself refusing to load, complications with permissions, changed URLS, lack of storage space - any number of problems that made it impossible to access what I needed. I *hopefully* have found the solution to the issue by creating a new instance with more storage space, but I know that I have given it everything I can, and I will say that every time I had to redownload and install WordPress, it got a bit easier and more intuitive, so I do have that going for me.
