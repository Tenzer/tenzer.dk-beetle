title: Configuring the Solaris DHCP server
date: 2009-05-23
type: blog
grouping: blog
---
As I recently had to configure the Solaris bundled DHCP server on a box, I though that I would write down the steps I took to get the task done, for everybody else to use and also for myself later on.

I will configure the server with some static entries for specific hosts, and also with a small pool of addresses for random users.

In order to get the DHCP server installed on OpenSolaris, you have to install the package named `SUNWdhcs`. This is done by running:

    :::shell
    pfexec pkg install SUNWdhcs

After the installation, you should start of by using the `dhcpconfig` command to set up the initial configuration directory to hold the settings used by the DHCP server:

    :::shell
    pfexec dhcpconfig -D -r SUNWfiles -p /var/dhcp -a 192.168.0.1 -d tenzer.lan -h files -l 86400

Parameters:

- `-D` - Specify that we setting up a new instance of the DHCP service.
- `-r SUNWfiles` - We want a plain text file store. Other options are SUNWbinfiles and SUNWnisplus, but I wont be covering those here.
- `-p /var/dhcp` - An absolute path to where the configuration files should be stored.
- `-a 192.168.0.1` - The DNS server to use on the LAN.
- `-d tenzer.lan` - The domain name to use for the network.
- `-h files` - Specify where the host informations should be stored. Other values are `nisplus` and `dns`.
- `-l 86400` - The lease time in seconds. 86400 seconds = 24 hours.

After that you have to create the network we are going to set up. This is done with the following command:

    :::shell
    pfexec dhcpconfig -N 192.168.0.0 -m 255.255.255.0 -t 192.168.0.1

Parameters:

- `-N 192.168.0.0` - Create a new network with the specified network address.
- `-m 255.255.255.0` - The network mask to use for the network.
- `-t 192.168.0.1` - Specifies the default gateway.

All the above entered network settings are put into macros, which each is a small assembly of configuration options, and which makes it easier to put a group of settings on specific hosts. You can see which macros there is currently created and their settings by running:

    :::shell
    dhtadm -P

You will see that there are three macros created. One macro is named after your servers hostname, and contains the settings specified as system wide settings with the first `dhcpconfig` command.
One has the name of the network you just created, and contains those settings entered in the last command.
The last macro named `Locale` simply holds timezone information, with a setting claiming the amount of seconds your local timezone differs from UTC.

As you can see from the output, the current timeserver specified is set to `127.0.0.1` which aren't going to do any good for the DHCP clients, so we are going to correct that to `192.168.0.1`, the same address used by our default gateway and DNS server. The change is done like this:

    :::shell
    pfexec dhtadm -M -m $(hostname) -e Timeserv=192.168.0.1

Parameters:

- `-M` - We are going to modify a macro.
- `-m $(hostname)` - This is the macro we are going to modify. $(hostname) will be replaced with your servers hostname by the shell.
- `-e Timeserv=192.168.0.1` - The setting we want to change, and what it should be changed to.

Again, you can check that the setting is indeed changed with:

    :::shell
    dhtadm -P

We are also going to alter the 192.168.0.0 macro, and set it to include the settings from the macro named after the hostname:

    :::shell
    pfexec -M -m 192.168.0.0 -e Include=$(hostname)

The third command used to configure the DHCP server is `pntadm`, which sets up settings for each host on the network. The following command adds a static DHCP entry for a host:

    :::shell
    pfexec pntadm -A 192.168.0.2 -c "Comment" -f MANUAL -i 01001BFC92BC10 -m 192.168.0.0 -y 192.168.0.0

Parameters:

- `-A 192.168.0.2` - Add the IP address 192.168.0.2.
- `-c "Comment"` - Specify a comment for the entry, for your own convenience.
- `-f MANUAL` - We set the flag MANUAL in order to only assign this IP address to the MAC address specified.
- `-i 01001BFC92BC10` - This sets the MAC address (Client_ID) for the host this entry goes for. Should be prepended with "01" to specify this is ethernet.
- `-m 192.168.0.0` - This host is going to use the "192.168.0.0" macro.
- `-y` - Verify that the macro entered actually exist.
- `192.168.0.0` - This is the network the address is assigned to.

The command to check the settings is:

    :::shell
    pntadm -P 192.168.0.0

And now we add an IP address for any host on the network:

    :::shell
    pfexec pntadm -A 192.168.0.10 -m 192.168.0.0 -y 192.168.0.0

The same parameters account as before, but we have just left out a lot more options, since we don't know which host is going to get this address.

The last thing to do, is to restart the DHCP server, in order for all the changes to take effect:

    :::shell
    pfexec svcadm restart dhcp-server

And then you are done.

References: [docs.sun.com](http://docs.sun.com/app/docs/doc/816-4554/dhcptm-1?a=view) and man pages.
