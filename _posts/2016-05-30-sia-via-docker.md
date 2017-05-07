---
title: Running Sia on a Synology NAS via Docker
date: 2016-05-30 00:00:00 -04:00
tags:
- docker
- sia
- synology
- nas
excerpt: A walkthrough for setting up Sia in Docker in Synology DSM
---

{% include base_path %}

# Overview

[Sia](https://sia.tech/) is a decentralized, peer-to-peer network for buying and
selling storage space. To participate in the Sia network, the user needs to keep
a Sia server process running at all times.

If you're a regular home user, this may present a challenge. Home computers need
to be rebooted frequently during normal operation and may not be turned on at
all times. In contrast, a
[NAS](https://en.wikipedia.org/wiki/Network-attached_storage) is designed to
have very high uptime and needs reboots very infrequently.

In this guide, we'll be setting up Sia to run on the Synology Disk Station
Manager (DSM) platform, the OS for Synology NAS devices. Because Sia has some
software dependencies that would be difficult to satisfy on a NAS, we'll be
using [Docker](https://www.docker.com/) to create a self-contained environment
in which we can run Sia with all of its dependencies.

The components we are using in this guide are:

* DSM 6.1-15047 Update 2
* Sia v.1.2.1
* Docker v.1.11.2

Though this guide is written specifically for Docker on the Synology DSM system,
the steps relating to Docker should be applicable on any platform that supports
Docker. I successfully tested this on a [Synology DS412+](http://amzn.to/2pf3unf),
but these steps should work on any Synology NAS with the latest DSM and
sufficient CPU/RAM.

# Configuring the NAS

Before we start running Sia, we'll need to get things set up on our Synology
NAS.

## Install Docker

First, we need to install Docker.

Docker is one of the few Synology-published, official packages available for
DSM. You can find it in Package Center by searching for `docker` and clicking
"Install."

![Install Docker package]({{ base_path }}/images/2016-05-30-sia-via-docker/package-docker.png)

## Create Sia Directory

Next, we'll create a dedicated Shared Folder for Sia. This is the folder where
Sia will store all of its state information (including encrypted wallet files
and the blockchain database).

From File Station, create a New Shared folder and name it "sia":

![Create new shared folder]({{ base_path }}/images/2016-05-30-sia-via-docker/new-shared-folder.png)

## Enable SSH access to Diskstation

Because the DSM Docker app does not support creation of images from a
`Dockerfile`, we'll need to do this through the command line. To support this,
open Control Panel > Terminal & SNMP and make sure "Enable SSH service" is
checked.

![Install Docker package]({{ base_path }}/images/2016-05-30-sia-via-docker/enable-ssh.png)

# Creating the Docker image

We're now ready to create the Sia Docker image!

To begin, we'll SSH into our NAS from another machine on the network. Linux and OS X users can run the following command from the terminal. Windows users will need an SSH client, such as [Cygwin](https://www.cygwin.com).

```bash
ssh admin@diskstation
```

*Note: The rest of the commands in this section assume that you are running as
an admin user on the Synology NAS.*

Now that we have a shell on the NAS, switch to the `/tmp/` directory:

```bash
admin@DiskStation:/$ cd /tmp

admin@DiskStation:/tmp/$

```

Now, using a text editor (`vim` is built-in to DSM, so I use that), create a
text file named `Dockerfile` with the following contents:

{% gist mtlynch/54d71bff4c33270c1cd6c0ddf0218558 %}

Or just download my `Dockerfile` with this command:

```bash
wget \
  https://gist.githubusercontent.com/mtlynch/54d71bff4c33270c1cd6c0ddf0218558/raw/f54c5108d39a897d7c4d33a6cd8b365db71e7dea/Dockerfile
```

This `Dockerfile` does a few things:

* Creates a Docker image from the golang base image so that the latest stable
  version of Go is available within the container.
* Downloads Sia v.1.2.1 (the latest stable release as of this writing) and
  installs it to the `/opt/sia` directory.
* Configures the image to run `siad`, the Sia daemon process, when the container
  starts up.
* Uses `socat` to forward the container's external port 8000 to the container's localhost:9980 port (the `siad` API port). Otherwise, no `siac` client outside the container would be able to execute commands against the container's `siad` server.
* Instructs `siad` to use `/mnt/sia` as its folder for Sia state information.
  In the next step, we'll link `/mnt/sia` to the Synology Shared Folder "sia"
  that we created earlier so that the files `siad` generates are visible on the
  NAS.
* Exposes all Sia ports (`9980`-`9982`) so that they are accessible outside the
  Docker container.

With our `Dockerfile` complete, we are ready to build and run the container:

```bash
# Create a Docker image tagged with the label "sia"
admin@DiskStation:/tmp/$ sudo docker build --tag sia .

# NOTE: Replace 10.0.0.101 with the IP address of your Synology NAS on your
# local network.
admin@DiskStation:/tmp/$ LOCAL_IP=10.0.0.101

# Create a Docker container based on our sia image and start running it in the
# background.
admin@DiskStation:/tmp/$ sudo docker run \
  --detach \
  --publish "${LOCAL_IP}:9980:8000" \
  --publish 9981:9981 \
  --publish 9982:9982 \
  --volume /volume1/sia:/mnt/sia \
  --name sia-container sia
```

The previous commands do the following:

* Creates a Docker image from our `Dockerfile`.
* Creates a Docker container for our image and starts running the container in
  the background.
* Forwards traffic to ports `9980`-`9982` on the NAS (the Docker host) to those
  same port numbers within the Sia container.
  * **Important**: Notice that for port `9980` we bind *only to the local
    network interface*, whereas for other ports we implicitly bind to all
    interfaces. This is a security measure. Anyone who can communicate with
    `siad` on port `9980` has full control of our host and can, for example,
    empty our wallet. This measure is not strictly necessary if our network
    does not expose this port externally, but it is a useful precaution
    regardless.
  * In my case, my NAS has IP address `10.0.0.101`. You can find your NAS's IP
    address with the command `dig diskstation +short` from another machine on
    your local network.

## Checking for success

From DSM, open the Docker app and open the "Container" panel.

If everything worked, we should see something similar to the following:

![Sia container running]({{ base_path }}/images/2016-05-30-sia-via-docker/sia-running.png)

If we open the "sia" Shared Folder we created earlier, we see that `siad` has
created several folders:

![Sia generated folders]({{ base_path }}/images/2016-05-30-sia-via-docker/sia-folder-populated.png)

# Managing Sia with `siac`

## Checking status

Now let's connect to our Sia daemon using the command-line client, `siac`.

With `siad` running on our NAS, we can communicate with it from any other
machine on our local network. The machine will need the [latest Sia
release](https://github.com/NebulousLabs/Sia/releases).

Once we've copied the `siac` binary to our machine, we can run `siac` commands
by specifying our NAS's hostname in the `addr` parameter:

```bash
# DISKSTATION is the hostname of the NAS on my local network, the default for
# Synology NAS devices.
$ ./siac --addr DISKSTATION:9980
Synced: No
Block:  0000000001ac2429ee234370ddf139ce87161277eded4bd58bcd31c5e5e2554f
Height: 727
Target: [0 0 0 0 12 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204 204]
```

## Configuring a host storage folder

If we would like to use Sia to host files for other users, we can create a
subdirectory for that in our "sia" shared folder:

![Sia storage folder]({{ base_path }}/images/2016-05-30-sia-via-docker/create-storage-folder.png)

Then we can use `siac` to add that folder as a new Sia host storage folder:

```bash
./siac --addr DISKSTATION:9980 host folder add /mnt/sia/host-storage 500GB
# When prompted for a password, enter: a
```

Note that `/mnt/sia/host-storage` is the path from the *daemon's* perspective
(from within the Docker container), not the perspective of `siac`.

# Allow Sia through firewall

Sia needs to communicate with remote peers over ports `9981` and `9982`, so if
we're using a home router, we'll need to configure it to forward those ports
to our Synology NAS. The exact process will vary by router, but it should look
something like the following

*Note: Replace `10.0.0.101` with the IP address of your Synology NAS.*

![Firewall settings]({{ base_path }}/images/2016-05-30-sia-via-docker/firewall.png)

We deliberately do **not** expose port `9980` because that is Sia's port for
API communications. Exposing it to the public Internet would leave our Sia peer
vulnerable to compromise.

# Conclusion

We now have a working Sia node that stays online as long as our NAS is up and
running. By using Docker's `PAUSE` functionality, we can eliminate the need to
ever restart the Sia daemon at all.

Because we configured Sia to keep all persistent state outside of the container,
it will be very easy for us to modify our `Dockerfile` to upgrade Sia as new
releases are published.

# Further Reading

The official Sia blog just published a post about setting up Sia to be a storage
host:

* [How to Run a Host on Sia](https://blog.sia.tech/how-to-run-a-host-on-sia-2159ebc4725)

Check that guide out for an in-depth walkthrough of configuring the Sia host we
just set up.

# Updates

* 2016-05-30: Original publication.
* 2016-07-08: Updated instructions for the Sia 1.0.0 release.
* 2017-01-15: Updated instructions for the Sia 1.0.4 release.
* 2017-05-07: Updated instructions for the Sia 1.2.1 release.

<br>
*Disclosure: Some of the links to particular products in this post use affiliate tags. This allows the blog to receive a commission when readers make purchases through these links.*
