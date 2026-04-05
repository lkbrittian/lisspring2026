# Installing WordPress - 04/05/2026

## Overview

This document describes the process of installing WordPress for the first time.

## Environment

- VM Provider: Google Cloud
- OS: Ubuntu 24.04 LTS x86/64

## Process

### Step 1: Verify Requirements

Since WordPress has very specific system requirements, it's necessary to make sure the software packages I have can meet thsoe requirements. Using the following commands, I checked to make sure PHP and MySQL are at a high enough version to e compatable with WordPress.

```
php --version
mysql --version
```

Both came back meeting the benchmarks (PHP at version 8.3 or higher and MySQL at version 8.0 or higher), so I did not check the Ubuntu release. Instead, I installed the recommended modules for PHP to help with functionality, then restarted Apache2 and MySQL.

```
sudo apt install php-curl php-xml php-imagick php-mbstring php-zip php-intl
```

```
sudo systemctl restart apache2
sudo systemctl restart mysql
```

### Step 2: Download and Extract WordPress

Unlike other installations we've done in the past, this installation isn't through apt, ut through a different program: wget. It also has us extract the package by unzipping it the same way I would install a program on my desktop.

I navigate to the document root and download the latest version of WordPress, then unzip the package to install, creating a new directory for WordPress at the document root in the process.

```
cd /var/www/html
sudo wget https://wordpress.org/latest.zip
sudo unzip latest.zip
```

I ran into a little trouble with the last command, with the response being that the command unzip could not be found. I would have thought that would have been something preinstalled, but perhaps I messed up the configuration of this VM as I did the first one. At any rate, I ran a search for the program unzip and read the description, double checked that there were no other programs that seemed more appropriate, then installed unzip. After installation, I reran the unzip command and watched a flood of information on the screen. When it stopped, I checked the directory files and saw that there was a new directory for WordPress, indicating that I had suceeded in unzipping the file.

```
apt show unzip
apt seach unzip
sudo apt install unzip
sudo unzip latest.zip
ls
```

### Step 3: Create a New User and Database

Like how we created a user and a database for the OPAC, we have to create a user for the WordPress database and a database for WordPress. To do this, we log in as the root user and use the create user command to create a new username and password. We then create the new database, give all privileges to the new user we created, and then show databases to ensure that the database was successfully created.

```
create user 'wordpress'@'localhost' identified by 'XXXXXXXXX';
create database wordpress;
grant all privileges on wordpress.* to 'wordpress'@'localhost';
show databases;
\q
```

I actually struggled when creating a password. I didn't want anything too complicated or that I use for other projects, so I initially went with something very simple... only to get rejected... multiple times. Every password I came up with was rejected for not meeting standards. I eventually googled the error number, and while reading the potential reasons, I saw a mention of a togglable password strength variable. I do remember setting the password strength to low when setting up the OPAC database. Since we didn't do so this time, I assume that does not carry over. I tried a more complex password, and it was finally accepted. The rest of the process went smoothly.

### Step 4: Set Up wp-config.php

Like when setting up the OPAC, we created a php file that allowed the program to authenticate itself. WordPress has a similar file, so rather than creating a new file, we can pull up the file (wp-config.php) in the WordPress directory and edit it with the login information that we just created for the wordpress user.

I was nervous to be editing the file, worrying that I would mess something up as the file didn't look like the login.php file we'd created before when I opened it with nano, but I edited in the database name, database username, and database password as instructed, saved it, and then pulled up the WordPress page in the external browser. Everything looks operational so far, so I believe that I did it right!

```
cd /var/www/html/wordpress
sudo cp wp-config-sample.php wp-config.php
sudo nano wp-config.php
```

### Step 5: Finish Installation

From there, I followed the browser instructions, creating a fictional library name for the site title, creating a username and password, and allowing search visibility. Everything seems to have installed correctly - I was able to login, and I'm looking at a dashboard that says "Welcome to WordPress!". I believe all that's left is to build the webpage in the browser.

## Reflection

I will admit that the installation process did not go as smooth as I'd hoped, but that was primarily me tripping over little things that I was able to smooth out and get over. Otherwise, it went well, and I was able to solve the problems that I did have. Now, time to take a break from the command line and build a webpage.
