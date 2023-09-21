# mac-to-linux
Experimenting with leaving my Mac in favour of a Linux machine


## Why going from Mac to Linux?

After many years (8+) of using a MacBook and fancy Apple stuff I found myself in thinking about leaving the golden cage.

I already dropped my iPhone in favour of an Android phone (currently Samsung Galaxy S23, mainly because of the great camera, the small form factor and the long update period) - and I don't really use iPhoto and others anymore. Also tethering doesn't work between an Android phone and a Mac (really! in 2023). 

Since I had a hard accident this year with a long recovery period I started to build a gaming PC just for fun - after maybe 10 years! At first I thought it was because I really wanted to play the new Diablo version (I loved the old v2 back when I was a teenager), but I experienced the fun building your own rig the way YOU want it. From the parts YOU choose from the MARKET or used market prices (and NOT from Apple prices).

And I remembered my old values of free standards, free software, free choice... wow - why did I opt for Apple devices in the first place?

So as time went by the thought of switching my main machine from Mac to a Linux box got bigger and bigger. Finally I cought myself in reading through laptop tests at https://www.notebookcheck.com :)


## Choosing a distro

That one is hard. Because if you ask, everybody yells at you :D 

So beeing a software architect I know the only way to get around the discussions is to define your requirements.

I tried to come up with some:

* Rolling releases: I hate big releases! So I don't want to reinstall my system every year like most of the people are used to from MacOS, Ubuntu, Fedora etc. I want a rolling release distro.

`tbd: more info on distro choosing`

Finally I opted for Manjaro Linux https://manjaro.org/, which is based on Arch Linux.


## Docker

Is there a way to install and use Docker on Linux? Yes I know: Linux containers were invented ON Linux. So why do we need Docker at all?

But having the ease of use of my beloved command line tooling would be great to make the switch as easy as possible.

There seems to be kind of an semi official Docker release from the creators (see https://docs.docker.com/desktop/install/archlinux/). But the problem is, the installation is [based on a binary you need to update manually](https://docs.docker.com/engine/install/binaries/#install-daemon-and-client-binaries-on-linux) - wow, in 2023?! 

But luckily we can simply use the power of the [Arch User Repository (AUR)](https://wiki.archlinux.org/title/docker#Installation) - and install Docker via the following commands:

```
pamac install docker
systemctl start docker.service
```

That should be everything to fire up our first Docker container on Linux:

`docker run -it --rm archlinux bash -c "echo hello world"`
