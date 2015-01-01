title: New website
date: 2014-12-26
type: blog
grouping: blog
---
It has been in the making for some time but now it's finally out. I've made a new design for my website and in the process also switched it from being based on Wordpress to now being a static site.

Back in July my colleague [Esben](http://cknv.dk/) started work on his own static site generator, [Beetle](https://github.com/cknv/beetle), and I got interested in the project shortly after. Within a month I made the first pull request to Beetle while also acting as a sparring partner to Esben's development work.

For a while I had an urge to do something about my website which was more or less desserted for the last couple of years. The only thing I did with it was to update the Wordpress installation every few months due to new security issues being found in the project. So instead I thought that I would redo it all as a static site, with a new fresh, mobile and retina friendly, easy readable design, and I hope that also would help spark my urge to blog some more again (I have yet to prove that).

For inspiration of how the site could look and what content I should include, I looked at some of my friends' websites ([cknv.dk](http://cknv.dk/), [mntr.dk](https://mntr.dk/), [kenneth.io](https://kenneth.io/)) and found a [color scheme](https://color.adobe.com/Flat-UI-color-theme-2469224/) and a simple [Wordpress theme](http://fastr-demo.themes.kanishkkunal.in/) that I liked which I could base my own site upon.

I worked on the site on and off over the following months, finding bugs and limitations in Beetle that was solved along with Esben, and I also made a few plugins for Beetle that I needed (a [SASS compiler](https://github.com/Tenzer/beetle-sass), [HTML minifier](https://github.com/Tenzer/beetle-htmlmin) and a [sitemap.xml generator](https://github.com/Tenzer/beetle-sitemap)).

Then on christmas eve, moments before the family dinner was about to start, I switched my website to point to this new wonder and announced it on [Twitter](https://twitter.com/Tenzer/status/547800770742337536). It didn't cause as much of a uproar as I expected, probably due to my timing, but I'm at least satisfied with what I have produced.

I have a few topics in mind going forward that I want to blog about, mostly operations/devops related stuff, such as:

* My new HP Microserver at home, running [SmartOS](https://smartos.org/)
* Amazon Web Services related posts, ie. about the new [Boto3 Python library](https://github.com/boto/boto3) for working with the API
* Tips and configuration examples for the [Nginx](http://nginx.org/) webserver
* Recommendations and guides for how to monitor your applications and set up monitoring systems.

But I don't want to limit myself to only those subjects, so don't be surprised if other topics pop up here.

If there's something you think I should talk about first, some topics that I missed or just some general feedback on the site, feel free to contact me on Twitter at [@Tenzer](https://twitter.com/Tenzer) or make a pull request on the [repository](https://github.com/Tenzer/tenzer.dk) for the site.
