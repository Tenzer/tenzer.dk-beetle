title: Some thoughts about package dependencies
slug: some-thoughts-about-dependencies
date: 2009-07-28
type: blog
grouping: blog
---
Last night I installed [SqueezeCenter](http://wiki.slimdevices.com/index.php/Logitech_Media_Server) (the server software used with Squeezebox's) on my home server, since I want to use it together with [SqueezeSlave](http://wiki.slimdevices.com/index.php/SqueezeSlave) running on my server. That way I can play music from my server while I have a webinterface to control it from all computers on my network.

For the installation of SqueezeCenter I downloaded the .deb package, since it was going to be installed on my home [Ubuntu](http://www.ubuntu.com/) server. I ran `sudo dpkg -i squeezecenter_7.3.3_all.deb`, which to my big disappointment showed that the dependency list for SqueezeCenter required MySQL server to be installed in either version 4.0 or 5.0. Since I already had MySQL server 5.1 installed, configured and running on the server, I didn't want that dependency to be met, since that would cause an uninstallation of my existing MySQL server instance, due to the packages claiming to conflict.

My workaround to this was to extract the `control.tar.gz` file from the .deb package by running `ar x squeezecenter_7.3.3_all.deb control.tar.gz` (I could probably write an article on .deb packages later), and then extract the `control` file from the `control.tar.gz` archive: `tar xzf control.tar.gz ./control`.

The `control` file basically contains the same information you can see by running `apt-cache show <package-name>`, but it lets you edit the information. So I changed the existing `Depends` line to include "mysql-server-5.1", "mysql-client-5.1" and "libmysqlclient16-dev", and then packed it all together again and it would then let me install the package without messing about with my MySQL server. The final product came out just fine, and the SqueezeCenter server works without a hitch.

Note that [a better way](http://ailoo.net/2009/06/repack-a-deb-archive-with-dpkg-deb/) for extracting the files does exists, which makes it easier than doing it manually.

This morning in the shower I then came to think back on this, and thought that a better way could be made. Because when there will be a need to update the package, I will then have to do the same manual changes again to the package, unless the package maintainer has updated the dependency list to include MySQL 5.1. Also, this could just as well be the case with a package found in the APT repository, which could make this even more cumbersome, in case you update the packages on the system, and without noticing it, installs an updated version of a package, which doesn't contain the modified dependency list. Then you could have to spend some time correcting the packages which should get uninstalled and old packages to install again.

Another case where I have been a bit irritated about some of the dependency listings is with the "sun-java6-plugin" package, which provides Java support to the installed browsers on the system. However, since Firefox 3.5 isn't included in the list of dependencies for the package, you will have to install the Firefox 3 package in order to install the Java plugin. That means that you will need to have Firefox 3 installed, alongside Firefox 3.5, simply in order to satisfy the dependencies for the sun-java6-plugin package. [Multiple](https://bugs.launchpad.net/ubuntu/+source/sun-java6/+bug/267260) [bugreports](https://bugs.launchpad.net/ubuntu/+source/sun-java6/+bug/367063) exists about this, but no action seems to taken. Wouldn't it be good to be able to take matters into your own hands, instead of either having to wait for the package maintainers to fix this, or set up your own APT repository where you create packages yourself with a corrected dependency list?

That's were I thought of a tool which simply should allow you to overwrite the dependency listings for a given package, regardless of package version. That way you could simply specify your own dependency list for a specific package, and don't care about obscure dependencies again. The same tool could of course also be used for the other settings in the control file, such as the `Conflicts` and `Replaces` lines.

The tool should obviously hook into `dpkg` in the process where it evaluates the above mentioned lines while handling packages, check if the package in question is set to be overwritten in any way, and if yes, then ie. update the dependency listings before dpkg evaluates it.

I don't how hard this would be to create, but I can see on the Debian dpkg mailing list that there has been some talk about making some hook functions and triggers, which may be able to do this, otherwise a fork of `apt-get`/`aptitude` could possibly do it.

Right now I don't think that a change like this could make it into the official `dpkg` source, since it would be a bit counter productive, regarding the updating and correctness of the packages in the public repositories, but when requests for changes to the packages are not being performed, I don't see any other proper way.

One of the reasons behind this post is to get any feedback on the idea, and if you have any further insight into this, then information on how hard it would be to implement and how to do it is greatly appreciated.

Update
------
Perhaps the [equivs](https://packages.debian.org/equivs) package can be used for these kinds of things.
