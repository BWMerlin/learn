---
title: Basic Stack Requirements
metadata:
    description: UserFrosting requires a web server, PHP 7.3 or higher, and some sort of database.
taxonomy:
    category: docs
process:
    twig: true
---

The basic requirements for running UserFrosting are pretty typical of any web framework or CMS. You'll need:

- Web server software (Apache, Nginx, IIS, etc)
- **PHP 7.3** or higher (**8.0** or higher recommended)
- Database (MariaDB, MySQL, Postgres, SQLite, or SQL Server)

[notice=tip]MariaDB is just an open-source fork of MySQL. The reason it exists is because of [numerous concerns](http://www.computerworld.com.au/article/457551/dead_database_walking_mysql_creator_why_future_belongs_mariadb/) that Oracle would not do a good job honoring the open-source nature of the MySQL community. For all technical purposes, MariaDB and MySQL are more or less perfectly interoperable.[/notice]

### Web Server Requirements

#### Special requirements for Apache users

If you are using Apache (the default web server that comes installed with XAMPP, WampServer, and most shared web hosting services), check that you have the Rewrite Engine module (`mod_rewrite.c`) installed and enabled.

Some distributions, like WampServer, may not have this module automatically enabled, and you will need to do so manually. In a shared hosting environment, you may need to have your hosting service do this for you.

**In addition**, make sure that the `Directory` block in your `VirtualHost` configuration is set up to allow `.htaccess` files. For example:

```
# Allow .htaccess override
<Directory /var/www/userfrosting/public/>
    Options Indexes FollowSymLinks MultiViews
    AllowOverride All
    Order allow,deny
</Directory>
```

For more information, see [this troubleshooting page](/troubleshooting/common-problems#installation-went-fine-except-i-don-t-see-any-styling-on-my-home-page-i-am-using-apache-).

### PHP Requirements

UserFrosting requires the following PHP modules to be installed and enabled:

#### GD

Occasionally, people use web hosting services that do not provide the GD library, or provide it but do not have it enabled. The GD library is an image processing module for PHP. UserFrosting uses it to generate the captcha code for new account registration.

##### Windows

If you are having trouble with `gd` in Windows, you should first check your `php.ini` file to make sure it is enabled. You'll include the GD2 DLL `php_gd2.dll` as an extension in `php.ini`. See http://php.net/manual/en/image.installation.php.

##### Ubuntu/Debian

In Ubuntu/Debian, you can install GD as a separate module. Replace the php version you actually need :

```bash
sudo apt-get install php7.3-gd
sudo service apache2 restart
```

##### MacOS

For MacOS users (Yosemite and Capitan), you might have GD installed but `imagepng` isn't available. In this case, you need to upgrade the default version of GD that ships with these versions of MacOS. See [this answer on Stack Overflow](http://stackoverflow.com/a/26505558/2970321) for a complete guide.

### File System Permissions

UserFrosting needs to be able to write to the file system for a few directories:

- `/app/cache` - This is where UF will cache rendered Twig templates for faster processing, as well as other objects;
- `/app/logs` - UF writes error, debugging, and mail logs to this directory;
- `/app/sessions` - If you're using file-based sessions, UF writes to this directory instead of PHP's default session directory.

You should make sure that the group under which your webserver runs (for example, `www-data`, `apache`, `_www`, `nobody`) has read and write permissions for these directories. You may need to use `chgrp` to ensure that these directories are owned by the webserver's group.

To determine the user under which, for example, Apache runs, try this command:

`ps aux | egrep '(apache|httpd)'`

Once you know the user, you can determine the group(s) to which the web server user belongs by using the `groups` command:

`groups <username>`

For all other directories, you should make sure that they are *not* writable by the webserver. We also recommend keeping the `/app` directory out of your web server's document root entirely, to prevent it from inadvertently serving any files in that directory. Only the contents of `/public` need to be in the document root.

[notice=tip]For detailed help with file permissions in Unix/Linux environments, please see our [Unix Primer for Ubuntu](https://learn.userfrosting.com/going-live/unix-primer-ubuntu#Filepermissions).[/notice]

### Other software (local development environment only)

During development, and before you're ready to deploy, you'll also want to have the following tools installed:

- [Composer](https://getcomposer.org) - PHP package manager
- [Node.js](https://nodejs.org/en/) - Javascript runtime environment and package manager

See the [next section](/installation/requirements/essential-tools-for-php) for more information on these tools.

## But my host only supports PHP 5.x! Why do I need PHP 5.6? And don't get me started about PHP 8!

Look, programming languages evolve, and PHP is no exception. Actually, PHP (and other web languages) have it particularly tough because they have so many responsibilities. PHP is the bouncer at the door and it has to be prepared to defend against the constantly evolving security threats to your server. At the same time it has to keep up with the demand for faster performance, and satisfy the demand for new features from the [enormous](https://w3techs.com/technologies/overview/programming_language/all) PHP community.

Honestly, PHP 5.6 isn't exactly cutting edge - in fact, **PHP 5.6 is no longer in active support as of [January 19, 2017](http://php.net/supported-versions.php) and declared "End of Life" as of [January 1st, 2019](http://php.net/supported-versions.php)**.

And the truth is, we didn't make this decision directly. UserFrosting depends on a lot of third-party components, and *those* components require a minimum PHP version of _7.3_. Thus, UF does too, and the whole community moves forward. Even PHP 7.3 won't be supported starting [December 6th 2021](http://php.net/supported-versions.php) !

If your hosting service doesn't have PHP 7.3 or higher installed, call them and ask them to upgrade. If they refuse, point out that PHP 5.6 has been out of support for {{ date("now").diff(date("2017-01-19")).m }} months! To be honest, there is little reason to use a shared hosting (e.g. cPanel) service these days, especially when VPS providers like DigitalOcean and Amazon EC2 are so inexpensive. Unless you're stuck with shared hosting for some reason another (fussy boss), [there's no real reason not to switch to a VPS](https://www.hostt.com/still-use-shared-hosting-theres-vps/).

As for your local development environment ([You _do_ have a local development environment, right ?](/background/develop-locally-serve-globally)), if it's that much of a burden then...I don't know what to tell you. So what are you waiting for? Upgrade!

[notice=warning]As of UserFrosting 4.6.0, PHP 8.0 is officially recommended. While you can still use UserFrosting 4.6.x with PHP 7.3 and 7.4, upgrading to PHP 8.0 (or above) is highly recommended as both PHP 7.3 and 7.4 support will be removed the future.[/notice]

## Third-party components? Why don't you write all your own code?

Before you run away because you are against dependencies as a matter of principle, read [this section](/background/dont-reinvent-the-wheel).
