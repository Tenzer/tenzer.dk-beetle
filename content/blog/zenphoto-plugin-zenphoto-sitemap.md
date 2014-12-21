title: 'Zenphoto plugin: Zenphoto Sitemap'
date: 2008-11-26
type: blog
grouping: blog
---
I have been coding a bit the last couple of days on a plugin for the gallery system [Zenphoto](http://www.zenphoto.org/). I wasn't very happy with how the images from my gallery where indexed, and I couldn't find any other decent coded sitemap generators for Zenphoto, so I decided to create one myself.

The result is a one-file plugin for Zenphoto, which is being activated by putting `?sitemap` in the URL for your Zenphoto gallery. When that is in the URL, a sitemap index will be generated, pointing to a new sitemap for each album in the gallery, which then contains links for each image in the album. The result is a [sitemaps.org](http://www.sitemaps.org/) compatible sitemap structure which can be used with several search engines, ie. Google, Bing among many others.

You can find more information on how to get the plugin and install it one the sub-page I have created for it. It is to be found at [Projects](/projects/) -> [Zenphoto Sitemap](/projects/zenphoto-sitemap/). Enjoy!
