title: Zenphoto Sitemap 1.0.1
slug: zenphoto-sitemap-101
date: 2009-01-24
type: blog
grouping: blog
---
I have updated my Zenphoto Sitemap plugin with two minor fixes.

The first change is that there won't be specified any priorities in the sitemap at all, since the priorities only matter against other URLs in the same exact sitemap (ie. not site-wide). The plugin will have to be rewritten to have all URLs in the same sitemap in order to make use of priorities. As I haven't experienced any need to specify the priority, it is simply left out and then the URLs are assigned the default priority (0.5) by the search engines.

The other change is that an appropriate header is now sent out together with the sitemap, which specifies that we are serving an XML file. This causes the browser to interpret the sitemap correctly, and will probably also help out with search engines which may require this. Thanks goes to Adrien Gibrat who suggested this.

You can see the updated sitemap in action on <s>my gallery</s>, and if you just want to download the new version, then go to the [Zenphoto Sitemap](/projects/zenphoto-sitemap/) page.
