title: Installing djbdns in an OpenSolaris zone
date: 2009-10-05
type: blog
grouping: blog
---
This guide shows you how to install [djbdns](http://cr.yp.to/djbdns.html) in an OpenSolaris zone. The zone is currently running OpenSolaris build 124, but the guide should work on other versions of OpenSolaris as well, though not tested. The zone is simply set up as a default ipkg OpenSolaris zone, without many default installed packages.

First we start of with the installation of some packages, including `sunstudio` since we are going to use that to compile djbdns with:

    pkg install sunstudio SUNWbtool

That is probably going to take a while, since ~400 MB has to be downloaded.

daemontools
-----------

You will first have to install [daemontools](http://cr.yp.to/daemontools.html) before we can move on. daemontools is used to start and stop the dnscache and tinydns services, plus making sure they are started again in case an issue should happen.

Start by downloading the source code for daemontools, and extract it afterwards:

    curl -O http://cr.yp.to/daemontools/daemontools-0.76.tar.gz
    gunzip daemontools-0.76.tar.gz
    tar xf daemontools-0.76.tar

Enter the newly extracted directory, and make a few modifications in order to use the Sun Studio compiler instead of GCC before trying to compile:

    cd admin/daemontools-0.76
    echo "cc -O2" > src/conf-cc
    echo "cc -s" > src/conf-ld
    package/install

Now there has been added a line to `/etc/inittab` which is supposed to keep the `svscan` service running at all times. However, that line does not work properly on Solaris machines, which causes `svscan` to restart every ten minutes. So you will have to edit the last line in `/etc/inittab` concerning `svscanboot`, and replace the line with the following:

    SV:123456:respawn:/command/svscanboot > /dev/msglog 2>&1 < /dev/null

After that is done you have to reload `init`, in order for it to pick up the new line. This is simply done by executing `init q`. And daemontools is now installed and running.

Note that the executables installed can either be found in:

1. The `command/` folder under which daemontools was extracted
2. Under `/command/` or
3. Inside `/usr/local/bin/`. Feel free to add any of these folder to your `PATH` variable, or specify the full path when you want to use the programs.

ucspi-tcp
---------

Next up is [ucspi-tcp](http://cr.yp.to/ucspi-tcp.html) which enables services to listen on TCP ports through the `tcpclient` and `tcpserver` commands.

Again, download and extract:

    curl -O http://cr.yp.to/ucspi-tcp/ucspi-tcp-0.88.tar.gz
    gunzip ucspi-tcp-0.88.tar.gz
    tar xf ucspi-tcp-0.88.tar

We are basically doing the same modifications to ucspi-tcp as we did to daemontools:

    cd ucspi-tcp-0.88
    echo "cc -O2" > conf-cc
    echo "cc -s" > conf-ld
    make
    make setup check

The installed binaries has been put inside `/usr/local/bin/`.

djbdns
------

Finally we are going to install the [djbdns](http://cr.yp.to/djbdns.html) package, which among others, contains the `dnscache` and `tinydns` servers.

Start of with the downloading and extracting:

    curl -O http://cr.yp.to/djbdns/djbdns-1.05.tar.gz
    gunzip djbdns-1.05.tar.gz
    tar xf djbdns-1.05.tar

And once again, we will have to change the compiler settings before compiling

    cd djbdns-1.05
    echo "cc -O2" > conf-cc
    echo "cc -s" > conf-ld
    make
    make setup check

And then you are done. The binaries has been installed in `/usr/local/bin/`.

Since there are already many guides on setting up dnscache and tinydns, I won't cover that here, but rather just focus on the OpenSolaris specific changes there's needed in order to compile the above three pieces of software.

I have made some spec files for the three packages above, so they may in the future be available pre-compiled in the package system, but for now you have to do with this guide.
