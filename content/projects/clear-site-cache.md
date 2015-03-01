title: Clear Site Cache
slug: projects/clear-site-cache
type: page
active_menu: projects
---
This is a Firefox add-on which removes the items in the browser cache from the current site, with a click of a button.

I wanted a more selective way of removing items from the cache than just clearing the entire browser cache. The only other way of doing that was through the "Forget About This Site", which also removes the history, cookies and what not for that site - Not really what you need if you just want to make sure you don't get cached items for a specific site.

So I made this plugin which goes through all the items in the cache and removes any which share the hostname with the site you are currently on.

This plugin might be a bit slow the first time you run it, that's presumably because Firefox has to pull out all the items in the cache in order for the plugin to check their origin. Subsequent runs are much faster though and SSDs also seem to help out.

The add-on is available on [addons.mozilla.org](https://addons.mozilla.org/en-US/firefox/addon/clear-site-cache/) and the source is on [GitHub](https://github.com/Tenzer/clear-site-cache).
