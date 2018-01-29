# We Will Name This Later program

## Firsts of First Things

Open your Terminal and run:

`sudo apt-get update`

> Got to keep your software repos updated

## Required and suggested software

> For a one-liner, look at the end

### Apt

Then run this for getting apt:

`sudo apt-get install apt`

Now we can use apt instead of apt-get which gives us less typing unless you tab-complete your commands. This allows you to run something like `sudo apt update` instead of `sudo apt-get update`.

To remove any package, you replace `foobar` with the package name and type:

`sudo apt remove foobar`

To show and read information about any package, you replace `foobar` with the package naem and type:

`sudo apt show foobar`


### Apache

Now we will install Apache:

`sudo apt install apache2`

We will configure this later!

### MySQL

`sudo apt install mysql-server`

You will be asked to enter in the MySQL root user's password (different than your linux root user!). Don't forget this password.

Note, we will run `mysql_secure_installation` another day. However you can read up on that as you wish and run it yourself when you have time. This is just an example however when setting up production servers, you would want to run the `mysql_secure_installation` command **after** you install mysql-server as it locks down the database from known attack vectors.

### PHP

> May the programming gods tremble at the forbidden word of PHP being mentioned...

`sudo apt install php libapache2-mod-php php-mcrypt php-mysql`

Don't need it now but will need it later so why not install PHP now.

### PHPMyAdmin

`sudo apt install phpmyadmin`

Pay attenton to the promps!


### Some Extra Packages

These are some extra packages that I suggest to install now. Some you can remove, like geany, if you don't like it later on. They are only suggestions to install. Subversion, we will use and same with git.

`sudo apt install build-essential geany subversion git`



### The One Liner!

`sudo apt-get install apt apache2 curl mysql-server php libapache2-mod-php php-mcrypt php-mysql phpmyadmin build-essential geany subversion git`

## Onward and up to MySQL!

### Create the Database

Time to create the database! Let's call it `client_messages` for the database name and set the characters to be UTF8 because for odd reasons, it is set to the Swedish latin character set. Which does work fine but UTF8 is more realistic to real life examples.

```SQL
CREATE DATABASE IF NOT EXISTS `client_messages` CHARACTER SET utf8 COLLATE utf8_general_ci;
USE `client_messages`;
```
### Create a MySQL user

Time to create a MySQL user to manipulate the database. Change `password` to something else! 

```SQL
CREATE USER 'client_messages_user'@'localhost' IDENTIFIED BY 'password';
```

Time to set the permissions to `client_messages_user` to only work with the `client_messages` database.

```SQL
GRANT ALL PRIVILEGES ON client_messages.* TO 'client_messages_user'@'localhost';
```
Flush the privileges so the user gets finalized!

```SQL
FLUSH PRIVILEGES;
```

### Create our first SQL table

```SQL

DROP TABLE IF EXISTS `client_messages`.`users`;
CREATE TABLE IF NOT EXISTS `client_messages`.`users` (
  `type`                CHAR(25) NOT NULL           DEFAULT ''                       COMMENT 'Type of User',
  `username`            CHAR(100) NOT NULL          DEFAULT ''                       COMMENT 'Username/Email',
  `password`            CHAR(50) NOT NULL           DEFAULT ''                       COMMENT 'Encrypted Password',
  `first_name`          CHAR(50) NOT NULL           DEFAULT ''                       COMMENT 'First Name',
  `last_name`           CHAR(50) NOT NULL           DEFAULT ''                       COMMENT 'Last Name',
  `birth_date`          DATE NOT NULL               DEFAULT '0000-00-00'             COMMENT 'Birth Date',
  `street_address_1`    CHAR(255) NOT NULL          DEFAULT ''                       COMMENT 'Street Address (1)',
  `street_address_2`    CHAR(255) NOT NULL          DEFAULT ''                       COMMENT 'Street Address (2)',
  `city`                CHAR(255) NOT NULL          DEFAULT ''                       COMMENT 'City',
  `state`               CHAR(2) NOT   NULL          DEFAULT ''                       COMMENT 'State',
  `zip_code`            CHAR(10) NOT  NULL          DEFAULT ''                       COMMENT 'Zip Code',
  `phone`               CHAR(45) NOT  NULL          DEFAULT ''                       COMMENT 'Phone',
  `is_verified`         INT(1)    NOT NULL          DEFAULT '0'                      COMMENT 'Is Verified',
  `deleted`             INT(1)    NOT NULL          DEFAULT '0'                      COMMENT 'Is Deleted',
  `id`                  INT(20) UNSIGNED NOT NULL   AUTO_INCREMENT                   COMMENT 'ID',
  `inserted`            DATETIME NOT NULL           DEFAULT '0000-00-00 00:00:00'    COMMENT 'Inserted',
  `updated`             DATETIME NOT NULL           DEFAULT '0000-00-00 00:00:00'    COMMENT 'Updated',
  PRIMARY KEY  (`id`),
  KEY `type` (`type`),
  KEY `username` (`username`),
  KEY `password` (`password`),
  KEY `first_name` (`first_name`),
  KEY `last_name` (`last_name`),
  KEY `birth_date` (`birth_date`),
  KEY `street_address_1` (`street_address_1`),
  KEY `street_address_2` (`street_address_2`),
  KEY `city` (`city`),
  KEY `state` (`state`),
  KEY `zip_code` (`zip_code`),
  KEY `phone` (`phone`),
  KEY `is_verified` (`is_verified`),
  KEY `deleted` (`deleted`),
  KEY `inserted` (`inserted`),
  KEY `updated` (`updated`)
) ENGINE=MyISAM  DEFAULT CHARSET=UTF8 AUTO_INCREMENT=1;
```
