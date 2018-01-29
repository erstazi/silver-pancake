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

We will break this down into the smaller segments. However, here is the first SQL table!

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

With this statement, we have it here even though it doesn't exist yet. Why? Because when you are first developing a new database, you might need to copy and paste the whole create SQL table creation statement over and over. So, having the `DELETE TABLE` statement there helps speed up time involved.
```SQL
DROP TABLE IF EXISTS `client_messages`.`users`;
```

We create the table, if it doesn't exist.

```SQL
CREATE TABLE IF NOT EXISTS `client_messages`.`users` (
```

Now to the SQL table's columns! First, the type of user. Could be admin, client, or anonymous (less than client as they aren't known). Some would suggest ENUM instead of CHAR however I would disagree with them on it is a limiting factor with the database to do that.

```SQL
  `type`                CHAR(25) NOT NULL           DEFAULT ''                       COMMENT 'Type of User',
```

Username/Email and Password. The username should be an email so they can be contacted back. This might change. For password, we encrypt it with a salt with PHP before we store it into MySQL.

```SQL
  `username`            CHAR(100) NOT NULL          DEFAULT ''                       COMMENT 'Username/Email',
  `password`            CHAR(50) NOT NULL           DEFAULT ''                       COMMENT 'Encrypted Password',
```

First and Last Name. Not much to explain. I have did a maximum length tests on census data in the US and haven't ever found a first name or last name with over 50 characters so I have always set it to 50 chars.

```SQL
  `first_name`          CHAR(50) NOT NULL           DEFAULT ''                       COMMENT 'First Name',
  `last_name`           CHAR(50) NOT NULL           DEFAULT ''                       COMMENT 'Last Name',
```

Some user location information about the person. Again, not much to it. With states, I store Pennsylvania as PA. So, the drop down would be similar to `<option value="PA">Pennsylvania</option>`

```SQL
  `street_address_1`    CHAR(255) NOT NULL          DEFAULT ''                       COMMENT 'Street Address (1)',
  `street_address_2`    CHAR(255) NOT NULL          DEFAULT ''                       COMMENT 'Street Address (2)',
  `city`                CHAR(255) NOT NULL          DEFAULT ''                       COMMENT 'City',
  `state`               CHAR(2) NOT   NULL          DEFAULT ''                       COMMENT 'State',
  `zip_code`            CHAR(10) NOT  NULL          DEFAULT ''                       COMMENT 'Zip Code',
```

Storing their phone is a good method for contacting them. Typically, we would do phone1, phone2, etc. **Or** we would do a separate table called `users_phones` which would have a foregin key (e.g. `id_user` ) that points back to `users`. Depends on usage.

```SQL
  `phone`               CHAR(45) NOT  NULL          DEFAULT ''                       COMMENT 'Phone',
```

With these two columns, we can mark if the user is verified (as in through an email verification) or if the user is deleted. This is purely binary. 0 means No. 1 means Yes. If a user is marked 1 in the deleted column, then that means they are deleted. If it is a 0, then they are not deleted. Default is 0 for deleted.

```SQL
  `is_verified`         INT(1)    NOT NULL          DEFAULT '0'                      COMMENT 'Is Verified',
  `deleted`             INT(1)    NOT NULL          DEFAULT '0'                      COMMENT 'Is Deleted',
```

We store the datetime (e.g. 2018-01-29 16:38:00 [which is Jan 29th, 2018 14:38PM]) when the user is inserted (created) or updated. Normally, we would do a log (e.g. `users_log`) but this will suffice.

```SQL
  `inserted`            DATETIME NOT NULL           DEFAULT '0000-00-00 00:00:00'    COMMENT 'Inserted',
  `updated`             DATETIME NOT NULL           DEFAULT '0000-00-00 00:00:00'    COMMENT 'Updated',
```

Ahhh, SQL Keys. When doing SQL queries, keys speed things up. I do make liberal use of keys for things I need to search for often. [Read more in the MySQL manual about SQL keys](https://dev.mysql.com/doc/refman/5.7/en/mysql-indexes.html)

```SQL
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
```

Going to use MyISAM as the storage type and UTF8 for the character set

```SQL
) ENGINE=MyISAM  DEFAULT CHARSET=UTF8 AUTO_INCREMENT=1;
```

For now, that's the jist. More will come. Final edit: 2018-01-29 16:48:05