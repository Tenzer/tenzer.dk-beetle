title: 'Guide: Private Docker Registry'
date: 2015-01-14
type: blog
grouping: blog
---
I got to play around with [Docker](https://www.docker.com/) lately at work, and for that we needed a private place to store Docker images. Since the other guides I found either were unnecessarily advanced, or hosted the registry inside a Docker instance, instead of making use of our existing server setup, I've gone ahead and made a guide for how I set it up.

This guide shows the steps to take on an Ubuntu machine, but it should be easy to apply to other operating systems.


Installation
---------------

We are going to use the official [Docker Registry](https://github.com/docker/docker-registry) Python project for this, but first, lets create a user to run the registry as:

    sudo useradd --system --create-home docker

We are going to install Docker Registry in a Python virtual environment, so lets install some packages for that:

    sudo apt-get install python-virtualenv libpython-dev liblzma-dev swig libssl-dev

Then lets switch to the `docker` user to get it installed:

    sudo su - docker
    virtualenv /home/docker/venv
    /home/docker/venv/bin/pip install docker-registry


Configuration
-------------

Next up we need to prepare a configuration file for Docker Registry. There's a sample configuration file included in the installed Python package, so lets use that as a base:

    cp /home/docker/venv/lib/python2.7/site-packages/config/config_sample.yml /home/docker/sample.yml

If you open the configuration file, you will see there are different sections of the configuration, where most of them inherit settings from one above. Each of those sections specify a configuration "set" or "flavor", and contains various settings regarding log levels, storage options and so on.

I won't go into detail about how to set up your configuration file, but I suggest that you create a new flavor at the end of the file, where you inherit the settings from the existing flavor, which is the closest match to what you would like.

When that's done and you have created any folders, AWS S3 buckets or whatever you may need according to your configuration, we need to get the Docker Registry started as a service.


Upstart
-------

Upstart is probably the easiest way to set up a new service on Ubuntu so lets use that. Copy the following Upstart configuration to `/etc/init/docker-registry.conf`:

    description "Docker Registry"

    start on runlevel [2345]
    stop on runlevel [!2345]
    respawn

    setuid docker
    chdir /home/docker

    env DOCKER_REGISTRY_CONFIG=/home/docker/config.yml
    env SETTINGS_FLAVOR=custom
    env PATH=/home/docker/venv/docker-registry/bin:$PATH

    exec docker-registry

You should change the `SETTINGS_FLAVOR` variable to the name of the flavor you created in the configuration file, and add any other environment variables you may need. Then you just need to start the service with:

    sudo start docker-registry

Check the log file at `/var/log/upstart/docker-registry.log` and make sure it doesn't spit out a bunch of Python stack traces. If you have `curl` installed on the machine, you can also do a quick test by running `curl http://127.0.0.1:5000/` in order to make sure it responds with `"docker-registry server"`.


Nginx
-----

Docker Registry does not provide any means of authentication or encryption for communication with clients, so you should put it behind an Nginx instance since it can provide that easily.

Install the Nginx version you prefer, here I go with `nginx-light` since it contains all we need. We also install `apache2-utils` since it contains the `htpasswd` utility we are going to use shortly:

    sudo apt-get install nginx-light apache2-utils

Then replace the contents of `/etc/nginx/sites-available/default` with something like the following:

    upstream docker { server 127.0.0.1:5000; }

    server {
        listen 80;
        listen 443 ssl;

        ssl_certificate cert.pem;
        ssl_certificate_key cert.key;

        client_max_body_size 500M;
        proxy_set_header Host $host;

        location / {
            auth_basic "Docker Registry";
            auth_basic_user_file /home/docker/auth;

            proxy_pass http://docker;
        }

        # Endpoints which are not requested with authentication
        location /v1/_ping { proxy_pass http://docker; }
        location /v1/search { proxy_pass http://docker; }
    }

!!! Attention
    Docker expects the registry to use HTTPS, but I won't go into how that is setup in Nginx as there are plenty of guides available on how to configure HTTPS already.

Reload the Nginx configuration, and then create a user in the auth file with `htpasswd`:

    sudo service nginx reload
    sudo htpasswd /home/docker/auth <username>

You will be asked what password you would like the user to have, and then you should be all set. On your client machine you will first have to log into the registry with your newly created user with `docker login <server URL>` (it asks you for an e-mail address but it is not used for anything), and then you can search, push and pull from the Docker registry.

I hope you found this guide useful, and in case you have any comments to this guide or just want to get in touch with me, then find my on Twitter as [@Tenzer](https://twitter.com/Tenzer).
