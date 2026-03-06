---
categories:
date: '2026-03-05'
description: "Box and Gears, Gears and Boxes"
layout: post
title: 'Setting up a Working  Satisfactory Server in Incus'
toc: false
---

In my continued willingness to learn about Incus for personal projects, I worked out how to configure Dedicated Servers inside an Incus Container.
This process should work for your own Dedicated Steam Servers so long as they are capable of running on a Linux Server. If it does depend on Windows, then I am afraid that there are no windows containers as of the time of this writing. You can refer to my previous post about working around this in V Rising using Proton if it is non-negotiable for your server.

## Setting Up the Container
It will be asssumed that you have installed Incus before doing anything else. My configuration is default at this point since it is a fresh installation.
If you run into trouble doing that, then you're on your own.

Firstly, we will want to [select a container image](https://images.linuxcontainers.org) to start. I am going to use Ubbuntu 24.04 since it is common and well supported by most software. You can both download and launch the container in a single command:
```bash
incus launch images:ubuntu/24.04 steam-satisfactory
```
Incus allows us to mount an external drive for permenant storage. It might not be necessary if you are only hosting one server but being able to store all your different game servers and have the host manage back ups is solid design.
A word of caution for using these external mounts: you will want to pass `shift=true` which allows the UID/GID from the host system to be passed to the container. This prevents permission issues about ownership inside the container. Therefore, if you make an account - like I am about to for steam - then you will want to set the uid/gid to match the container uid/gid.
```bash
# put this wherever you want; name it whatever you want
sudo mkdir -p /srv/steam/servers/satisfactory
```
... and then we add the mount points. To do that we will need:
 - the container name
 - the disk name
 - **disk**
 - host mount point
 - container mount point
 - the shift for permissions
```bash
incus config device add steam-satisfactory \
  games disk \
  source=/srv/steam/servers/satisfactory \
  path=/home/steam/servers/satisfactory \
  shift=true
```

Now, there are lots of different ways to do networking configuration but **macvlan**  worked the best for me. This option is new to me even though I am experienced with virtualization technologies. Simply put, it gives the container its own MAC and  IP Address directly on the physical network but at the cost of communicating with the Host itself. If you are using this for technology stacks like Apache Spark and its slaves then avoid this option. For my single game server this is perfect:
```bash
incus config device add steam-satisfactory eth0 nic \
  nictype=macvlan \
  parent=eno1 \
  name=eth0
```

## Inside the Container
Now that the external part is mostly completed we can proceed to login and download the server. If you have a script setup, then this is where you could copy the script in with `incus file push myInstallScript.sh steam-satisfactory/<path-in-container>` and run it with `incus exec steam-satisfactory -- <path-to-script>`. If you are doing this from scratch then you will just need to login with `incus exec steam-satisfactory -- bash`.

Now that we're inside, we need to add a bunch of remote repo's to allow us to find steamcmd:
```bash
apt-get install -y software-properties-common
add-apt-repository -y multiverse
add-apt-repository -y universe
dpkg --add-architecture i386 # must have for steamcmd
apt-get update

apt-get install -y steamcmd
```

Many servers - including Satisfactory - will refuse to run if are root. It is also good practice not to run these servers as root because if they get compromised then the attacker already has root. Make sure that you add a distinct user for the server(s):
```bash
adduser --disabled-login --gecos "" steam
```
This is the point where the permission fix is required. Inside the container, make sure to give control of the home folder to the user you created. My steam user did not end up controllering their own home directory and I had to update that:
```bash
chown -R steam:steam /home/steam
```
Collect the user and group ids using `id` as the steam user for this next part. You will need to drop out of the container, navigate to where you pointed the host mount point and change the owner to match the container info:
```bash
sudo chown -R 1001:1001 satisfactory # on the host
```
Hop back into the container and install the server at last:
```bash
sudo -u steam -H /usr/games/steamcmd \
  +force_install_dir /home/steam/servers/satisfactory \
  +login anonymous \
  +app_update 1690800 validate \
  +quit
```

Now you can navigate to the Dedicated Server folder and run `./FactoryServer.sh`.
If you want, run this as a systemd unit or in a screen/tmux or something else entirely. Manage your container services however you feel comfortable.