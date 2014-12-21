title: Setting up Tweet Nest in Nginx
date: 2012-11-23
type: blog
grouping: blog
---
Today I've set up [Tweet Nest](http://pongsocket.com/tweetnest/) at [tweets.tenzer.dk](http://tweets.tenzer.dk/). Tweet Nest is a simple application that archives all your tweets, and make it easy to search for old tweets you've written that you might want to find again. Tweet Nest comes by default with `.htaccess` files containing an Apache mod_rewrite specific configuration, but since I'm using [Nginx](http://nginx.org/) that configuration won't work for me.

The mod_rewrite rules are limited to rules for handling requests to `/sort`, `/favorites`, `/search` and pages to show tweets for a specific month and day. In the .htaccess file they are specified as:

    RewriteEngine On
    RewriteRule ^sort/?$ ./sort.php [L]
    RewriteRule ^favorites/?$ ./favorites.php [L]
    RewriteRule ^search/?$ ./search.php [L]
    RewriteRule ^([0-9]+)/([0-9]+)/?$ ./month.php?y=$1&m=$2
    RewriteRule ^([0-9]+)/([0-9]+)/([0-9]+)/?$ ./day.php?y=$1&m=$2&d=$3

I have replaced those with the following Nginx rules, which are specified within the `server {}` directive for the site:

    rewrite ^/sort /sort.php;
    rewrite ^/favorites /favorites.php;
    rewrite ^/search /search.php;
    rewrite ^/([0-9]+)/([0-9]+)/([0-9]+)? /day.php?y=$1&m=$2&d=$3;
    rewrite ^/([0-9]+)/([0-9]+)? /month.php?y=$1&m=$2;

A few things to note:

- The `?` at the end of the regular expression rules in the final two rules, means any extra parameters given in the URL will be ignored.
- With the regular expression rule I have defined for the month view, I need it to be below the rule for showing a specific day, otherwise the month rule will catch all requests to specific days as well. I could probably modify the rule to not do this, but I like how clear it looks here.
