title: Macro module for Apache
date: 2007-10-07
type: blog
grouping: blog
---
This guide is about the wonderful [mod_macro](http://www.coelho.net/mod_macro/), which makes the job of managing an [Apache webserver](http://httpd.apache.org/) much easier, or just for the home developer with several test pages or projects.

I will explain how to get it running and give some examples of how I use the module, to help you get started with using it.

Installation
------------
First we need to get the module installed, since it is not installed in a default [Debian](https://www.debian.org/)/[Ubuntu](http://www.ubuntu.com/) installation. I presume you already have Apache2 installed, otherwise you should install that first. We can use <acronym title="Advanced Package Tool">APT</acronym> to install the module, just run the following command as root:

    apt-get install libapache2-mod-macro

Next we need to activate the module and restart Apache:

    a2enmod macro
    /etc/init.d/apache2 restart

And you are ready to use macros!

Basic usage
-----------
Before we are ready to make our config files, you should first learn how to use mod_macro. It basically adds one new function and a new tag, which now can be used in Apache's config files. The function `Use` and the tag `<Macro>`. We start of with the tag which is used to create a new macro. A simple static macro could look like the following:

    <Macro Logfile>
        CustomLog /var/log/apache2/access.log combined
        ErrorLog /var/log/apache2/error.log
        LogLevel warn
    </Macro>

The macro simply declares that the macro called `Logfile` contains the text within the `Macro` tags. In order to use the macro you just put the following line in your config file, where ever you normally would have put the lines from your macro:

    Use Logfile

When you then reload Apache and it looks through the configuration files, then it automatically converts `Use Logfile` to the lines you have defined in your macro.

That was simple, but what do you do if you don't want all sites to log to the same file? You could create a logfile for each site, by declaring a variable which is then used in the macro:

    <Macro Logfiles $sitename>
        CustomLog /var/log/apache2/$sitename-access.log combined
        ErrorLog /var/log/apache2/$sitename-error.log
        LogLevel warn
    </Macro>

And the `Use` function to go with it:

    Use Logfiles tenzer.dk

Neat, don't you think? If you then want to change the path to your logfiles, you only have to change it in your macro, and not in every config file you have got. The syntax for `Use` looks like this:

    Use <Macroname> [Variable 1] [Variable 2] ... [Variable x]

Which basically means that you have to supply a name for the macro you are using, but the variables are optional.

A thing you need to remember when making macros, is that the file defining your macros needs to be included in Apache's configuration files somewhere, or else you will not be able to use them. I would suggest you to make a file called `macro.conf` inside the folder `/etc/apache2/conf.d/`, since all files in that folder automatically get included by Apache.

Advanced usage
--------------
Macros are not limited to only be used for printing a few lines in Apache's configuration, it can also be used for entire `VirtualHost` declarations, like this:

    <Macro VHost $type $hostname>
        <VirtualHost *>
            ServerAdmin webmaster@$hostname

            Use $type $hostname

            ServerSignature On

            ServerName $hostname
            ServerAlias www.$hostname
        </VirtualHost>
    </Macro>

    <Macro dir $hostname>
        DocumentRoot /var/www/$hostname

        CustomLog /var/log/apache2/$hostname-access.log combined
        ErrorLog /var/log/apache2/$hostname-error.log
        LogLevel warn
    </Macro>

    <Macro cfg $hostname>
        Include /var/www/conf.d/$hostname.conf
    </Macro>

I should probably explain this since it can seem quite complicated at first sight. The macros are supplied with two variables, the first one called `$type`, which can either be set to `dir` or `cfg`, and the second variable called `$hostname` would be the hostname of the site, without "www." in front of it.
The `$hostname` variable sets the name of the logfiles and also defines the ServerName with the domainname, and even adds a `ServerAlias` with "www." prefixed.
The purpose of the `$type` variable is to give you the choice between making a custom config file for the `VirtualHost`, which would then be placed in `/var/www/conf.d/$hostname.conf`, or to use a predefined path for the `DocumentRoot` (`/var/www/$hostname/`).

This makes it very easy to get an overview of which sites you have added in Apache, since the list of virtual hosts would look like this:

    Use VHost dir tenzer.dk
    Use VHost cfg example.com
    etc...

This also makes it easier to script adding and deleting sites to the server, since you only have to look for one single line in the Apache configuration.

Do note that the macro module can be used in all of Apache's config files and not only for setting up virtual host.

I hope that you found this guide helpful, and please let me know of the ways you use mod_macro in your setup!
