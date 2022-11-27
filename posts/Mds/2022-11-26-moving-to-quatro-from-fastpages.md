---
categories:
- environment
- process
- migration
date: '2022-11-26'
description: This Was Not Simple.
layout: post
title: 'Migrating From Fastpages to Quarto'
toc: false
---

# What is Fastpages?
For those unaware - or not following what Jeremy and his team produce -, Fastai also produced something called **Fastpages** which is intended to help creators in the Machine Learning Space Blog about their work. What was so useful about how this worked is that you could very easily explore a problem in a Jupyter Notebook only to clean it up and have the tooling produce a blog post: code and all. But, since it also supported other formats - such as Markdown documents, Word documents - you could really just use it as a general blogging tool as well. They made setting this all up easy so one could focus on writing and exploring instead of fighting with template engines or other tools. I have dabbled a bit with other technologies for blogging - a moment of word press, a few flask apps, and a bit of Django - but they were always about prose and not code. There was also a large amount of copying and pasting from editors to code blocks; not to mention trying to get the output from the runs showing in some meaningful way. While looking over quite a few of these I had started to consider actually writing my own just becuase there was such an annoying gap here.

Thankfully, **[Fastpages was born](https://github.com/fastai/fastpages)** and it was exactly what I was looking for in a blogging platform. The original version of this blog was using fastpages and I was having good success slowly getting used to working through problems or ideas and then getting them online. Only to find out one day that it was being depreciated for a different tool: [Quarto](https://quarto.org). Quarto's purpose is about creating documents in general as opposed to simply blog posts. But, that's not a loss so long as the blogging allows me to still write Jupyter Notebooks and have them converted to blog posts. Unsurprisingly, it does do this and so and to quote the disclaimer from Fastpages:

> Even though I created fastpages, I believe that people trying to blog with notebooks should be using Quarto. I am a believer in recommending the best tools for the job and helping people use those tools where possible. To that end, I have created this [migration guide](https://nbdev.fast.ai/tutorials/blogging.html) 153 that can help you migrate your fastpages projects to Quarto, along with other Quarto resources that might be helpful. I did a quick trial run to see how the migration would go: **it did not go**.

## Problem 1: Quatro Does Not support Arch.
Right from the start, this is not being built to be used across distributions. I am running Manajro linux - which is based on Arch Linux - and the install tries to install a debian package using `apt`. Not going to work here. Obviously. With this being as new as it is, I didn't expect there to be support for it on the AUR but someone did in fact go through the trouble of getting it up [there](https://aur.archlinux.org/packages/quarto-cli). But, sadly, as they note on that page:

> **titaniumbones:** Getting a dependency error when trying to install; dependency deno<1.23.0 leads yay to install deno-bin from AUR (version 1.20), but it appears that deno-bin doesn't actually fulfill the dependency:

> **trap000d**: There is no solution for this yet, until quarto devs manage to move to latest Deno. Either downgrade deno to 22.3... or patch quarto sources.

The [issue](https://github.com/quarto-dev/quarto-cli/issues/1174) has not moved in months at this point and I need this working now. These posts are - while fun - proof that I can use these technologies and I cannot wait an indeterminate number of maybe months for this blog to continue. And, Fastpages was showing some annoying behaviors - really ugly slugs for the urls - which I also needed fixed. <br />
Well, I can always just setup a Debian Based Virtual Machine and post from that; so, that's what I did.


## Problem 2: I Want to Post About Deep Learning - Which Requires my Video Card.
If I want to experiment and write post about more cutting edge ideas then I will need access to my Video Card in this computer. There are a few ways to work around this:
1. Just don't do them.
2. Use a Service like Collab; Download the notebooks.
3. GPU Passthrough to the VM.
4. Build another computer just for Blogging.
5. Network sharing the same Directory.

**Option One** I included for completeness but I refuse to not post about these. <br />
**Option Two** is passable since a Jupyter Notebook is basically just a JSON document so we could use a Cloud Service, explore and clean it up, download it and then save it to post. This is annoying and would slow down the interation process too much. Plus, I want to use my own Video Card that I have already spent money on for this. This is worst case but at least it could work. <br />
**Option Three** is viable but risky. I have never actually done GPU passthrough and both systems would need to share the Video Card which could cause all sorts of weird issues. If I kept the Virtual Machine online and was playing a Video Game with my friends then I expect to have problems. And, that means constantly spinning the VM up and down as I need it. This is quite a lot of maintenance for writing posts. Again, this might work but with this many problems to accidentily trip over it's risky at best and not reliable at worst. <br />
**Option Four** is just silly; And, with Video Card prices what they are there no way I'm building a second computer just for this blog. Nope. <br />

**Option Five** is what I went with. I can do all the work on my computer but save the actually outputs directly into the repository to commit and upload. This allows me to also keep data on my own computer and thus ignore the size constraints of data. It would also let me work on any computer I wanted - for example - my laptop is also Arch and also has a Video card so I could write posts from that as well. The VM could stay up as long as I wanted, I could even migrate it to other computers if need be and it would affect literally nothing. The only real maintenace will be the mounting remotely of the directory which is a very well solved problem.


## Problem 3: Setting up the Connection.
So, I have a Debian Virtual Machine now. There are some packages that I both wanted and needed for this to work:
    * `sshfs` to share the folders.
    * `sshd` to allow access remotely.
    * `vscodium` to write posts with: locally and remotely.
    * `xpra` to host remote windows.
    * I just like `vim`.

Luckily, `sshfs` and `sshd` are installed by default so we wont need to worry about them. I ended up needing to install curl since it's apparently not here by default; installed with `apt install curl` since this is a Debian system. So, I don't tend to use raw `jupyter` notebooks but instead use a derivative of Visual Studio code called `vscodium`. I just happen to be comformtable with using this and it supports notebooks with a few extensions. It also has a *Quatro* extension which is useful for some of the publication parts I could use later.

Always setup passwordless ssh access between systems when you setup a new system. There is a useful new way to do all of this via console and there are [good guides](https://www.linuxtechi.com/passwordless-ssh-login-keys-linux/) already for this. One little caveat which bit me is I have quite a few of these now and so I name them when I make them. If you organize your .pub files - like I do - make sure you **not move the .pub** until *after* you've ran `ssh-copy-id <username>@<ip-address>`. If you move it before then the command doens't find it; I tried to pass it manually but it just wouldn't take.

[`xpra`](https://www.xpra.org) is a useful tool which allows for Remote Window control. It allows you to start a program as a daemon on any system, connect remotely to that system to get the remote window while it is running. If you're familiar with X11 Forwarding then this will not be a new idea. This is simply better though since you can disconnect from the remote system at any time, go do something else and then re-attach later exactly where you were before. I use this at work often to conceptually break computers apart: this one is for Deep Learning and Gaming, this one is for Work related only, this is for Server Hosting, etc. Once it's installed, I started the editor on the host with `xpra start :100 --start=codium` to start the daemon and connect to the editor remotely from my desktop using `xpra attach ssh:<ip-address>:100` to open the editor on my own workstation. Great! Now I can setup the new repository to host the files, open it via the editor on the remote system.

Next I have to connect a folder locally to the VM which is easy using `sshfs`; this creates a fuse mount via an ssh tunnel to mimic the remote folder locally. You could also do this with samba but I find `sshfs` much easier and faster to setup and use. I made a directory as a mount point - `Blog` - and then mounted it via `sshfs blog:<path-to-code> Blog` so that I can work locally and read/write to repository.


## Problem 4: The Actual Migration.
The Document provided made this really easy; none of the steps failed me. One of my posts had an extension .MD instead of .md which caused the post to fail to convert but that's about it. I updated the default them, built out a drafts folder for posts in the future and everything just kind of worked. I've been working through fixing the image paths on a few of my posts but otherwise this is really an improvement. <br />
Expect more posts in the future and thanks for the new tool.
