title: Zenphoto Sitemap
slug: projects/zenphoto-sitemap
type: page
active_menu: projects
---
!!! Attention
    This plugin is no longer maintained, as the functionality has been incorporated into the Zenphoto core repository with the [sitemap-extended](http://www.zenphoto.org/news/sitemap-extended) plugin.

This is a plugin for [Zenphoto](http://www.zenphoto.org/) which generates a [sitemaps.org](http://www.sitemaps.org/) compatible sitemap for the gallery, when the gallery is accessed with `?sitemap` in the URL. The sitemap contains links to all public and non-password protected albums and images within Zenphoto.

The sitemap creates URLs depending on whether [mod_rewrite](https://en.wikipedia.org/wiki/Rewrite_engine) is enabled or not, so you still will get those nice clean links from the search engines that we all like, even if this plugin is enabled.

Installation
------------

The installation procedure for this plugin is really simple:

1. Place `zenphoto-sitemap.php` inside the `plugins/` folder of your Zenphoto installation.
2. Activate the plugin from the Zenphoto administration panel, under the Plugins tab.
3. Verify that you are shown an XML file when browsing `[your gallery URL]/?sitemap`.

That's it, then you are free to submit the address to search engines such as
[Google](https://support.google.com/webmasters/answer/183669?hl=en&rd=1), [Bing](http://www.bing.com/webmaster/help/how-to-submit-sitemaps-82a15bd4) and so on.

Robots.txt
----------

Consider adding a reference to the sitemap to the galleries `robots.txt` file located in the root of your Zenphoto installation. That will make search engines able to find the sitemap on their own, if they are able to follow such references.
Simply add the line Sitemap: `[your gallery link]/?sitemap` to the beginning of
`robots.txt`, and you are done.

Bug reports and feature requests
--------------------------------

You are of course welcome to post bug reports and feature requests for this plugin on the [GitHub issue tracker](https://github.com/Tenzer/zenphoto-sitemap/issues).

You can get the plugin here: [Zenphoto Sitemap v2.1.0](https://github.com/Tenzer/zenphoto-sitemap/archive/v2.1.0.zip).

Changelog
---------

**1.0.0 - 2008-11-26**

* Initial version.

**1.0.1 - 2009-01-24**

* Removed the `<priority>` tag from the root page, since it didn't do any good and generated an error message from Google.
* Added header information to be sent out with the sitemap (Thank you Adrien Gibrat).

**1.0.2 - 2009-02-26**

* Made handling of non-alphanumeric characters correct, so they will be URL encoded (Thank you 'suxab' for reporting this).

**2.0.0 - 2010-02-19**

* Revamp of the entire code - Now one big sitemap is generated instead of one index which then points to a sitemap for each gallery. This will decrease the amount of SQL queries and HTTP requests needed, all in all making the plugin more eco friendly ;).

**2.1.0 - 2010-03-18**

* Added Zenpage support thanks to contribution from Malte Müller (acrylian).

There's a [thread](http://www.zenphoto.org/support/topic.php?id=4394) on the Zenphoto forum regarding the plugin.
