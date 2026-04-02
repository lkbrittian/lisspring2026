# Creating a Bare Bones OPAC and Cataloging Module

## Overview

In this document, I will discuss practicing working with MySQL, creating a basic OPAC, and then building a cataloging module. 

## Environment
- VM Provider: Google Cloud
- OS: Ubuntu 24.04 LTS x86/64

## Introduction

An OPAC (Online Public Access Catalog) is an internet-accessable version of a library catalog that can be searched through using an online portal with filters such as keywords, dates, medium, and other variables. OPACs are used by library patrons and librarians alike to find materials, update records of library holdings, and manage the currently existing records of library materials.

## Part One: Practicing MySQL

### Process

#### Step 1: Create a New Database

Since the user account created for MySQL in the last lesson does not have the ability to create a new database, I log in as the root user, who does have the ability to create new databases.

Once I'm logged in as the root user, I create a database called DinnerDB and give the user account privileges for it. I then quit and relog in as the opacuser.

```
sudo mysql -u root
mysql> create database DinnerDB;
mysql> grant all privileges on DinnerDB.* to 'opacuser'@'localhost';
mysql> \q
```

```
mysql -u opacuser -p
```

#### Step 2: Create Tables

After logging in as the opacuser, I pulled up the list of databases and then moved to the newly created DinnerDB database.

```
show databases;
use DinnerDB;
```

Once in DinnerDB, I created two tables - Meals and Ingredients. Meals was created with this code: 

```
create table Meals (
    meal_id int auto_increment primary key,
    meal_name varchar(100) not null,
    cuisine varchar(50),
    cooking_time int not null default 1 check (cooking_time > 0),
    vegetarian boolean
);
```

"create table Meals" starts the command, and then "(" opens for the commands that will build out the coloumns of the table.

"meal_id"
is the name of the column, "int auto_increment primary key" will have an automatically increasing number for each line in the table that serves as the primary key.

"meal_name" is the name of the column, "varchar(100)" gives a 100-character limit to the item in that column, and "not null" means it can't be empty.

"cooking_time" is the name of the column, and "int not null default 1 check (cookingtime > 0)" ensures that the value in that column can't be a zero or a negative number, and a blank will be replaced with 1.

"vegetarian" is the name of the column, and "boolean" means the value in that column must be either TRUE or FALSE. 


Ingredients was made with this code.

```
create table Ingredients (
    ingredient_id int auto_increment primary key,
    meal_id int not null,
    ingredient_name varchar(100) not null,
    quantity varchar(50),
    foreign key (meal_id) references Meals(meal_id) on delete cascade
);
```

Much of this is the same as the code for the Meals table, but the last line of code connects the two tables. "foreign key (meal_id)" identifies this value as one referencing a value outside the Ingredients table, "references Meals(meals-id)" tells it where the key is located to pull from, and then "on delete cascade" lets ingredients associated with a specific meal id to be deleted when that meal id is deleted.

#### Step 3: Insert and Query Data

```
insert into Meals (meal_name, cuisine, cooking_time, vegetarian) values
    ('Spaghetti Bolognese', 'Italian', 45, FALSE),
    ('Vegetable Stir Fry', 'Chinese', 20, TRUE),
    ('Chicken Curry', 'Indian', 50, FALSE),
    ('Mushroom Risotto', 'Italian', 35, TRUE);
```

```
insert into Ingredients (meal_id, ingredient_name, quantity) values
    (1, 'Spaghetti', '200g'),
    (1, 'Ground Beef', '250g'),
    (1, 'Tomato Sauce', '1 cup'),
    (2, 'Broccoli', '100g'),
    (2, 'Carrots', '50g'),
    (2, 'Soy Sauce', '2T'),
    (3, 'Chicken Breast', '300g'),
    (3, 'Curry Powder', '2T'),
    (3, 'Coconut Milk', '1 cup'),
    (4, 'Arborio Rice', '1 cup'),
    (4, 'Mushrooms', '1 cup'),
    (4, 'Parmesan Cheese', '1/2 cup');
```

"insert" is the command to insert data, "into 'table'" tells it which table, inside the parentheses is the columns each value will do into, and "values" begins the list of values to fill into each column.

Notably, in the ingredients table, for the value meal_id, the number each meal has on the Meals table is used to tie each ingredient to that specfic meal.

```
select * from Meals;
```
displays the Meals table

```
select * from Meals where vegetarian = TRUE;
```

adds a condition to filter out results, ie, where the value for vegetarian is true.

```
select * from Meals order by cooking_time desc; 
select * from Meals order by cooking_time asc; 
```
displays the table, but orders the rows by value of cooking_time, either descending or ascending.

```
select Meals.meal_name as Meals,
    Ingredients.ingredient_name as Ingredients,
    Ingredients.quantity as Quantity
    from Meals
    join Ingredients on Meals.meal_id = Ingredients.meal_id;
```

This command will display the results in a table format and will change the title of the columns for display purposes. 

"select Meals.meal_name as Meals" will remane the column meal-name from the Meals table to Meals,
Ingredients.ingrediant_name as Ingredients renames the column ingredient-name to Ingredients,
and Ingredients.quantity as Quantity changes the column quantity from the Ingredients table to Quantity.

"join Ingredients on Meals.meal_id = Ingredients.meal-id;" will crossreference the tables, pulling up results that share the same meal id and display the name of the meal, the ingredients for each meal, and the quantity of each ingrediant.

```
select ingredient_name as Ingredients,
    quantity as Quantity
    from Ingredients 
    where meal_id = (select meal_id from Meals where meal_name = 'Chicken Curry');
```

Similarly, this command's first two lines rename the column, the third line specifies what table to pull from, and then the final line specifies to display lines where the meal id number matches the meal id number in Meals where the meal_name is Chicken Curry.

```
select cuisine, count(*) as meal_count 
    from Meals
    group by cuisine;
```

"select cuisine" = display cuisine values
"count(*) as meal_count" = display the count of each value under the column title meal-count
"from Meals" = from the Meals table
"group by cuisine" = sort and count by the value in cuisine

```
select meal_name, cooking_time 
    from Meals 
    where cooking_time <= 45
    order by cooking_time asc;
```

Select these two columns from the Meals table, selecting only lines where the cooking time value is less than or equal to 45, and then order that table by ascending cooking times.

### Reflection

For the most part, the commands make sense to me, and I was able to use each one without issue in MySQL. Breaking down each line, and sometimes each section of a line, helped me to understand what each part of the code is doing, which I hope will help me in the next section. The hardest command block to wrap my head around was the one that cross-referenced the Meals and Ingredients. I'm still not sure that I could replicate it based on that last line of code, but I hope that i will gain further understanding and be able to replicate it as I go along.

## Part 2 - Creating a Bare Bones OPAC

### Process

#### Step 1 - Insert Copyright

In order to search by date in the OPAC that I will create, I have to add a publication date to the books table. First, I log into MySQL and access the opacdb database.

```
mysql -u opacuser -p
use opacdb;
```

Then I use the following commands to add a publication date column, set the parameters, and drop the old copyright column in favor of the new publication date column.

```
alter table books add publication_date date;
update books set publication_date = str_to_date(concat(copyright, '-01-01'), '%Y-%m-%d');
alter table books drop column copyright;
alter table books change publication_date copyright date not null;
```

#### Step 2 - Create OPAC Page and Script for Search

Once that is done, we navigate to the document root to create the page that will act as our basic OPAC. 

```
cd /var/www/html/
sudo nano mylibrary.html
```

Once the page is created, I copy-pasted the provided HTML script to create the page at my VM's public IP address. I saved the result, then went to my machine's IP address and added /mylibrary.html. It took me to a page with a search bar and a date range search module, so the code seems to have worked as intended. I tried running a search, but as there was no code yet set up connecting the webpage to the database, nothing happened.

Returning to the codeline and still in the document root, I then created a new file.

```
sudo nano search.php
```

Once again, I copy-pasted the provided code, saved, then reloaded the browser page for the OPAC. I ran a test search, and it pulled up results as intended!

#### Step 3 - Insert More Titles

To add more titles to the OPAC, we have to log back in to MySQL.

```
mysql -u opacuser -p
use opacdb;
```

To insert titles, run the command "insert into books" followed by "(the columns you wish to fill in) values", and then the values you wish to add following that templete with a comma after each line. 

For example:

```
insert into books
(author, title, publisher, copyright) values
('Emma Donoghue', 'Room', 'Little, Brown & Company', '2010-01-01'),
('Zadie Smith', 'White Teeth', 'Hamish Hamilton', '2000-01-01');
```

Remembering that little comma at the end was something I consistantly forgot to do, and once you've entered to go to the next line, I couldn't find a way to go back, so that one mistake resulted in a lot of failures when entering in new titles. I didn't expect that to be the hardest part of this section!

I did add a handful of titles to the OPAC, though, and then went to the external browser page to confirm that all the added titles had been updated and added to the online OPAC. They were!

### Reflection
Everything for this section went perfectly smoothly up until adding more lines to the books table - I figured out why I had trouble with it, but it was time consuming to realize I had made a mistake and had to scrap everything and redo it. I will defintiely take more care in the future.

## Part 3 - Creating a Bare Bones Cataloging Module

### Process

#### Step 1 - Create the Cataloging Module Form

First thing we do is navigate to the document root and create a new directory for the module.

```
cd /var/www/html
sudo mkdir cataloging
cd cataloging
```

In the cataloging directory, we now create the html file for the webpage and the php file to connect the page with the MySQL database and books table.

```
sudo nano index.html
```

Once the file is created, I copy-paste the provided code, then save and exit.

```
sudo nano insert.php
```

Again, I copy-paste the provided code, save, and exit. 

To test this, I go back to the external browser and change the url to /index.html. That returns the original first webpage we created for the class. Since the file we're after is under the cataloging directory, I then try /cataloging/index.html. That pulls up a page for adding items to the OPAC! So far so good!

#### Step 2 - Security

Since this webpage allows access to the database from the web where anyone could access it, it's necessarily to add security to limit access.

```
cd /etc/apache2
```

```
sudo htpasswd -c /etc/apache2/.htpasswd libcat
```

I left the username as libcat and created a password, then moved on to the next step, which is letting the Apache2 server know that we are using htpasswd to secure the cataloging module. To do this, we access the Apache2 configuration file.

```
sudo nano /etc/apache2/apache2.conf
```

At the indicated section, I copy-pasted the following code:

```
<Directory /var/www/html/cataloging/>
  Options Indexes FollowSymLinks
  AllowOverride AuthConfig
  Require all granted
</Directory>
```

Jumping ahead a bit, when I ran the configuration test, I got a syntax error, though I wasn't sure what it was saying. I returned to the config file, and saw two "directory" lines of code in a row. I erased one of them, saved, exited, and reran the test. I got back another syntax error, this one saying I didn't close the directory. I went back in to the config file, and I believe I must have erased the wrong "directory" line. I closed the directory section with </directory>, saved, exited, and ran the config test one more time, and finally got the all clear.

Back in the cataloging directory, I created a file called .htaccess.

```
cd /var/www/html/cataloging
sudo nano .htaccess
```

I added the following code, saved, and exited.

```
AuthType Basic
AuthName "Authorization Required"
AuthUserFile /etc/apache2/.htpasswd
Require valid-user
```

I then ran the config test as explained above, then restarted the Apache2 server and checked the status. Everything came back good.

The next step was to limit permissions and ownership. I wil be completely honest in that I don't quite understand what it is that this section does. I believe that Apache2 has a user account for the server, and then set up permissions for that user account regarding the document root? Regardless, I ran the following lines of code to set up those permissions. There was no output from those commands, so I have no way of knowing if they were effective, but I copy-pasted them so I hope they would be.

```
 sudo chown :www-data /var/www/html
 sudo find /var/www/html -type d -exec chmod g+s {} +
```

#### Step 3 - Test the System

I reloaded the page and it asked me for a username and password. I entered the username and password that I had created and it let me in to the cataloging module. I entered a couple books into the system and then returned to the OPAC page. Running a search did pull both added items up a results. I then went back to the server and opened the MySQL database to check the books table, and the two items added from the browser had been added to the table! It looks like everything is working as it should be.

### Reflection

The only part of this section that gave me difficulty was adding the security measures to the webpage. I had the hang up with altering the configuration file, which I feel was most likely caused by copypasting in the wrong spot, and then while updating the permissions for Apache2 went fine, I'm not sure exactly what it is that I did on a practical level or how it affects my ability to access the cataloging module or anyone elses.

Overall, though, the entire project came together nicely. I have an OPAC and a cataloging module that I can use to update the OPAC from the web browser rather than by updating the table in MySQL. I feel good about how it went, and I was able to resolve the difficulties I did have quickly and without too much struggle.
