# Installing Koha - 04/17/2026

## Overview

This document describes the process of installing koha onto a new server set up with firewalls.

## Environment

- VM Provider: Google Cloud
- OS: Ubunuto 24.04 LTS x86/64

## Process

### Step 1 - Create a New Server with Firewalls

From the group chat, I saw that some of my classmates chose to build their new VM off of a snapshot of the VM we've been using this semester, the same way I had to do to fix loading times on my WordPress site. I chose not to do this, however, as IP mismatches had been a major source of frustration for me in the last couple weeks and were not something I wanted to risk without good reason at this point. Instead, I followed the steps to create a new VM instance solely to run Koha. I've been running a free trial of Google Cloud all semester that came with $300 in credit, so the cost isn't something I'm too concerned with. I will also be deleting that instancce when the class is concluded.

I configured the VM to match the specifications given, and before finalizing it, moved on to the next step to create the firewalls rules before adding them to the machine and creating it.

To create both firewall rules, I went to the Google Cloud console, clicked on VPC Network, then Firewall, and then followed the instructions to create 2 firewall rules following the specific standards laid out for each of them.

Once the VM instance had the firewall tags applied and was created, I connected to the machine and began the process of setting up Koha.

### Step 2 - Prepare to Install Koha

To begin, I ran the tmux command that will reestablish my connection if my VM instance crashed while installing. Thankfully, though it did take some time, my VM did not crash.

```
tmux
```

Next, I made sure that my server was fully updated and that unneeded packages were removed in order to save disk space.

```
sudo apt update
sudo apt upgrade
```

```
sudo apt autoremove -y && sudo apt clean
```

Next, we add repositories to our local database to sync with and download Koha through. I don't fully understand what is being done, but the first set of commands is used to set up keys to make sure the software we download is the correct software.

```
sudo apt install apt-transport-https ca-certificates curl
sudo mkdir -p --mode=0755 /etc/apt/keyrings
sudo curl -fsSL https://debian.koha-community.org/koha/gpg.asc -o /etc/apt/keyrings/koha.asc
```

Then we become the root user to apply another command that I believe adds the repository that we want to add to the list of repositories to sync with and download software from (in this case, a repository that contains the Koha software).

```
sudo su
```

```
tee /etc/apt/sources.list.d/koha.sources <<EOF
Types: deb
URIs: https://debian.koha-community.org/koha/
Suites: 25.05
Components: main
Signed-By: /etc/apt/keyrings/koha.asc
EOF
```

Then use the command "exit" to exit the root user account and return back to the regular user account.

### Step 3 - Install MariaDB and Koha

Koha can run off of either MySQL or MariaDB. MariaDB works exactly the same as MySQL in most cases and Koha defaults to it, so we install it for Koha to run off of.

```
sudo apt update
sudo apt install mariadb-server
```

Finally it's time to install Koha. Since we've added a new repository, we'll run "sudo apt update" once more, review the package information to ensure we're downloading the correct package, and then install Koha. This is a process that took much longer than any previous installation, but thankfully it installed without issue.

```
sudo apt update
apt show koha-common
sudo apt install koha-common
```

### Step 4 - Configuration

In order to set up both sides of Koha (public and private), we have to configure Koha and Apache to use two different ports to direct users to two different interfaces - staff or public - using the two firewall rules set up when creating the VM instance. 

First, I duplicate the configuration file in order to have a backup.

```
sudo cp /etc/koha/koha-sites.conf /etc/koha/koha-sites.conf.backup
```

Then I open the configuration file with nano, and edit the INTRAPORT and OPACPORT lines to match the firewall ports established earlier.

```
INTRAPORT="8080"
OPACPORT="8081"
```

Moving over to Apache2, which was installed when Koha was installed, we enable some modules, then restart the software. I also ran a status check.

```
sudo a2enmod rewrite cgi headers proxy_http
sudo systemctl restart apache2
sudo systemctl status apache2
```

Apache2 has to be configured with the ports as well, so we make a copy of the configuration file, then under "Listen 80", add "Listen 8080" and "Listen 8081"

```
sudo cp /etc/apache2/ports.conf /etc/apache2/ports.conf.backup
```

```
Listen 8080
Listen 8081
```

### Step 5 - Create Koha Instance

To install Koha, we'll create a Koha library using a simple command, and then restart Apache2.

```
sudo koha-create --create-db bibliolib
sudo systemctl restart apache2
```

With the Koha library created, we can further configure Apache2 - first, we turn off the default the web document root. Then we turn on network compression to compress data sent from the Koha server to client-side machine, then finally enable the newly created Koha library.

```
sudo a2dissite 000-default
sudo a2enmod deflate
sudo a2ensite bibliolib
```

Then we reload and restart Apache2.

```
sudo systemctl reload apache2
sudo systemctl restart apache2
```

### Step 6 - Web Installation

Before going to the browser, we need the server login information. To get this, we use the command:

```
sudo koha-passwd bibliolib
```

Which will give us the username and password for the Koha library we created. From there, going to http://"IPADDRESSFORVM":8080 will bring us to the welcome screen for Koha's onboarding process, where it will ask for the username and password received from our earlier command. From there, follow the onboarding instructions to complete installation and construction of the OPAC.

## Reflection

Overall, installation went very smoothly. My machine didn't crash, I seem to have correctly enabled the firewalls on the VM instance, I didn't have to redo any commands or relaunch the server, and everything seems to be functioning as intended with Koha. Hopefully everything will remain operational. 
