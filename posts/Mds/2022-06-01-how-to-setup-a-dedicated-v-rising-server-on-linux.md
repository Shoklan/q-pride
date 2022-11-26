---
aliases:
- /dedicated/gaming/instructions/linux/v rising/2022/06/01/how-to-setup-a-dedicated-v-rising-server-on-linux
categories:
- dedicated
- gaming
- v rising
- linux
- instructions
date: '2022-06-01'
description: Your Milage May Vary.
layout: post
title: How to Setup V Rising on Linux
toc: true

---

# The Quick Version:
If you're here becuase you just want the answer then here are the exact steps that always work for me.
1. Download the V Rising Dedicted Server from steam to your account.
2. Download Proton Experimental and change to the Bleeding Edge Beta branch.
3. Run this command:
```
STEAM_COMPAT_CLIENT_INSTALL_PATH=/tmp STEAM_COMPAT_DATA_PATH=/tmp <Path/to/proton/experimental/install/location/proton> run VRisingServer.exe <args-from-offical-repo>
```
- If this doesn't work then proceed to the below:
4. Download Lutris and install the dependecies it asks for.
5. Find and install the Epic Games laucher from Lutris.
6. Do the steps it asks you to run through: including those dependencies.
7. Reboot.
8. Run the command:
```
STEAM_COMPAT_CLIENT_INSTALL_PATH=/tmp STEAM_COMPAT_DATA_PATH=/tmp <Path/to/proton/experimental/install/location/proton> run VRisingServer.exe <args-from-offical-repo>
```

# Story Time
I have been looking forward to playing this game since I accidently found it a while back. As someone who's favorite genre is definitely Survival Games and Vampires are one of my favorite supernatural creatures, this game was going to be at the top of the list. Adding to that the skills along with skillshots for combat and this is about as perfect as it gets. Sadly, the game does not support linux and - per the [Protondb page](https://www.protondb.com/app/1604030) - most people were simply not able to get the game running.
End of Story.

But in actuality, after a few days of work and some investigation by the wonderful Proton Community they found that it was [missing some configuration files](https://github.com/ValveSoftware/Proton/issues/5845#issuecomment-1133871512) and they were taken from another game the developer had released. Once you copied those into the proper place then the game could launch. Luckily, Proton is very active and soon this change was pushed into the the **Bleeding Edge Beta** of Proton and once swapped over the game would run without issues. I also confirmed this myself and happily booted the game into my first test world!
End of Story.

# So, What About the Dedicated?
So, the problem is that this cannot work for the Dedicated server. Since launching the dedicated server only shows a link to the [Instructions to configure the server](https://github.com/StunlockStudios/vrising-dedicated-server-instructions) and it assumes that it is running on a Windows Computer. All we have locally is the simple example .bat file for windows which is of little use to us. What we need to do is run the Dedicated server somehow with Proton and we'll have to find a way to put this into a script. 

## How Do We Run Something Manaully With Proton?
Luckily, just like the wonder people over at the Github Page, we can find that answer online! According to this old thread from [Reddit](https://old.reddit.com/r/linux_gaming/comments/9au2qp/how_do_i_use_proton_via_command_line/):
> *STEAM_COMPAT_DATA_PATH=~/.proton/ ~/.steam/steam/steamapps/common/Proton\ 3.7/proton run whatever.exe*  
> You need to create ~/.proton (it can be any directory and can be empty)  

Excellent! Now we have something to work with. Looking at the command I built, I simply put those in `/tmp` since I didn't want to think about what compatdata is; I still don't really know what this is after a quick Google but it works like this.
So, simply create a little script to be ran in bash:
```bash
#!/usr/bin/bash
STEAM_COMPAT_CLIENT_INSTALL_PATH=/tmp STEAM_COMPAT_DATA_PATH=/tmp <Path/to/proton/experimental/install/location/proton> run VRisingServer.exe <args-from-offical-repo>
```
... and then `chmod +x scriptName.sh` and run it to start the server.

## This Didn't Work!
So, in my own troubleshooting I found that I ended up with an error `Failed to create batch mode window: Success.` when I ran this a second time on my server. However, after doing some testing on multiple different distributions - Ubuntu 20.10, EndevourOS, Manajaro - I found that it didn't work on any of my systems even though it had ran once. Adding to my confusion, the dedicated server ran find on my own Desktop and without issues at all. Considering one of my servers was Manjaro just like my desktop then there must be something installed on locally which is not being installed along with the Steam Proton Dependencies. The only noticable difference in dependencies was that I have Lutris Installed on my own Destktop and it's not on any of my other systems. So, I ran through exacty what is installed via Lutris and once I rebooted my other server it worked without issues.

## So, What Is Missing?
The honest answer is that I don't know. The list of dependences that gets installed along with the process is [quite](https://github.com/lutris/docs/blob/master/InstallingDrivers.md) a  [list](https://github.com/lutris/docs/blob/master/WineDependencies.md) and I simply don't have time to isolate which ones are the correct packages. If you have the time to figure it out then please let me know so I can update this post.
Otherwise, Happy Hunting!
