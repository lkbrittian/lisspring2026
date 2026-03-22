# MySQL Documentation - 03/12/2026

## Overview
This record will document the process of installing and configuring MySQL to complete the LAMP stack (Linux, Apache, MySQL, PHP).

A LAMP stack is a collection of software that is used to host database-backed websites, with each piece of software providing a specfic function to build and host the website. Because each piece of software used in a LAMP stack is open source, it's free to use and can be adjusted to the user's needs, making it easily accessible at a low cost and commonly used as a result.

## Environment
- VM Provider: Google Cloud
- OS:Ubuntu 24.04 LTS x86/64 

## Process

### Step 1 - Install and Configure

The first thing I did when booting up my VM was to run the sudo apt update commands to ensure everything is as up to date as possible.

```
sudo apt update
sudo apt upgrade
sudo apt autoremove
sudo apt clean
```

The upgrade took longer to process than usual, but otherwise this went smoothly.

I then installed the MySQL Community Server package. This also took a few minutes to fully install, but when I checked the status, it was enabled and active, indicating it was running smoothly.

```
sudo apt install mysql-server
systemctl status mysql
```

I then ran the following command when prompted by the text:

```
sudo mysql_secure_installation
```

This is a package that sets up some security checks and a baseline configuration of MySQL. I answered yes to all the questions posed, and chose a low security password (though I am surprised that it did not ask me to create a password then).

Once I got the "all done!", I ran the command to log into the database.

```
sudo mysql -u root
```

This brought up the MySQL prompts, indicating I'd sucessfully logged in. I typed the command "show databases;", and it returned with this result just as intended.

+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.01 sec)

So far, installation and access has gone smoothly with everything responding as expected.

### Step 2 - Create a User Account and Database

For a multitude of reasons (the primary one being I'm stupid and the secondary being I can't spell to save my life), creating the user account was the most difficult part of this lesson.
When copy-pasting the command, I was in too much of a rush to erase the mysql> prompt and so the command failed... twice. Then I wised up and typed it out, but forgot the semicolon, resulting in an indented arrow below. I had no idea if this meant Query OK, but given that I couldn't move on from that, I figured it wasn't. I logged out of MySQL, logged back in, and pulled up the video version of the lesson to follow along (I prefer reading at my own pace, so I don't usually listen to the videos). Watching the video, I wondered if I needed to replace the Xs in the command, tried a couple versions of that, tried retyping the command again and misspelling it,  before finally highlighting the command (minus mysql>), copy, and pasting... and having everything work great, leaving me to wonder what the heck I did wrong the first five times and work out the error for each one.

I know better now.

```
create user 'opacuser'@'localhost' identified by 'XXXXXXXXX';
```

After that, I made sure to highlight all of the MySQL prompts. I created the database opacdb, confirmed its existence through "show databases", and granted privileges to the opacuser that I painstakingly created in the last step.

```
create database opacdb default character set utf8mb4 collate utf8mb4_0900_ai_ci;
show databases;
grant all privileges on opacdb.* to 'opacuser'@'localhost';
```

### Step 3 - Creating Tables

I logged back into MySQL as the created user (after restarting a couple times thinking my VM had frozen when there was no password feedback(it had already crashed once today))

```
mysql -u opacuser -p
```

And opened up the list of databases to confirm that opacdb was still there, then switched to it.

```
show databases;
use opacdb;
```

I copy-pasted the following code to create a table in the opacdb database, then confirmed that the table was created as specified by running commands to show the tables in the opacdb, and then describe the table to make sure it matched what the code had set up.

```
create table books (
        id int unsigned not null auto_increment,
        author varchar(150) not null,
        title varchar(150) not null,
        copyright year not null,
        primary key (id)
);
```

```
show tables;
describe tables;
```

### Step 4 - Adding Records to Tables

```
insert into books (author, title, copyright) values
('Jennifer Egan', 'The Candy House', '2022'),
('Imbolo Mbue', 'How Beautiful We Were', '2021'),
('Lydia Millet', 'A Children\'s Bible', '2020'),
('Julia Phillips', 'Disappearing Earth', '2019');
```

Using the above code, I inserted 4 rows of records into the table. The first line of the command lays out the template of items to be inserted, and each subsequent line follows that template to inset each item into the proper spot in the record. 

To check that the table had been correctly update with records, I used the command

```
select * from books;
```

Everything had been properly filled out! With that done, I moved on to the test commands. I won't copy them all here, but I found them all practical and fairly self-explanatory in construction - it feels a lot more intuitive to use than many other commands do, which makes sense depending on who this is designed to be used by.

```
select author from books;
select copyright from books;
select author, title from books;
select author from books where author like '%millet%';
select title from books where author like '%mbue%';
select author, title from books where title not like '%e';
select * from books;
alter table books add publisher varchar(75) after title;
update books set publisher='Simon & Schuster' where id='1';
delete from books where author='Julia Phillips';
select author, publisher from books where copyright < '2011';
select author from books order by copyright;
```

### Step 5 - Install PHP and Support

```
sudo apt install php-mysql
```

This command installed the package to provide PHP support for MySQL. Installation for this was much quicker than installing MySQL, probably due to size.

After installing the package, I restarted Apache and MySQL - I would assume to reboot them with the new package installed?

```
sudo systemctl restart apache2
sudo systemctl restart mysql
```

Then to connect PHP to MySQL, we need to allow PHP to authenticate itself at the document root. To do that, we navigate to the document root, create the file login.php, cahange the file permissions, change the file owner, and put the login information for the opac database in that file where it will not be accessible later by anyone that accesses the database.

```
cd /var/www
sudo touch login.php
sudo chmod 640 login.php
sudo chown :www-data login.php
ls -l login.php
sudo nano login.php
```

Now that we have a connection between PHP and MySQL, we can create a new PHP file to be displayed on the website that will display information from the database we created. Navigating to the html directory in the document root, we create a new file. Copying the given code in the created file and then saving, I then test it for errors in the syntax.

```
cd /var/www/html
sudo nano opac.php
```

```
sudo php -f /var/www/login.php
sudo php -f /var/www/html/opac.php
```

The first command came back blank, showing no lines causing errors, and the second command came back with HTML, showing that the conversion was corrected. Finally, I opened the website for my server to see if it had worked. From the base address, all I could see was the browser checker that had been set up before. Adding /opac.php to the address though brought up the new file created, with everything looking as it should.

## Reflection

I feel like I grasp how to use MySQL very well. Creating, filling in, editing, and deleting parts of the table feels intuitive and self explanatory, and I can see how this or something like it can be used to build and edit an OPAC or other database systems. Where I get more lost is the section where we connect PHP to MySQL. I kind of understand that we connected MySQL to PHP, which can be read by the Apache server and displayed via browser, but I don't really understand the technicals of it. Reading the posts in the discussion board helps explain it somewhat, but I don't feel like I really understand the how. I definitely need more practice with this concept.
