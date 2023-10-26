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

`sudo docker run -it --rm archlinux bash -c "echo hello world"`

Since we also want to be able to run our `docker` command without `sudo` (and we NEED to run it without sudo for the `MacOs on Linux` part), we need to do [the following steps according to the Arch package wiki](https://wiki.archlinux.org/title/docker#Installation): 

> If you want to be able to run the docker CLI command as a non-root user, add your user to the docker user group, re-login, and restart docker.service. 

(but there's also a warning: `Warning: Anyone added to the docker group is root equivalent because they can use the docker run --privileged command to start containers with root privileges.`)

So here we go:

```
sudo usermod -aG docker "${USER}"
```

Log out and log back in so that your group membership is re-evaluated - or even restart your machine to make the changes take effect. Maybe also `newgrp docker` should be enough.

Restart the Docker service via:

```
systemctl restart docker
```

Now we should be able to run docker without sudo:

```
docker run -it --rm archlinux bash -c "echo hello world"
```





## MacOS on Linux

You may already guessed it: I will need MacOS for some time to follow - just to be able migrate some workflows I created over all those years. And also to use Samsung Smart Switch, my tax software and others. So is there a problem to run MacOS virtualized on Linux?

First I thought about using VirtualBox to do the job - but then I read statements like: "It could work (after many crazy configuration steps) - but then you shouldn't do an upgrade ever, since it may stop working right after the update". What...?! OMG.

See https://najigram.com/2022/01/run-macos-in-virtualbox-on-linux-os/, https://github.com/hkdb/VBoxMacSetup, https://www.macwelt.de/article/1506511/macos-ventura-mit-virtualbox-als-vm-betreiben.html




## MacOS as Docker container on Linux

But then I had an idea: Why not use containers to do the job? And a quick google search got me to https://github.com/sickcodes/Docker-OSX 

Let's try this out! First we need to [get some prerequisites ready](https://github.com/sickcodes/Docker-OSX?utm_source=pocket_saves#initial-setup):

> Before you do anything else, you will need to turn on hardware virtualization in your BIOS.

Now if you have hardware virtualization activated, we need to install some packages:

```
sudo pamac install qemu libvirt qemu-desktop dnsmasq virt-manager bridge-utils flex bison iptables-nft edk2-ovmf
```

Since pamac [will prompt you for optional dependencies](https://www.reddit.com/r/archlinux/comments/1z9y3l/install_optional_dependencies/), I choosed `none` and used `2:  qemu-desktop  8.1.0-2  extra` as the QEMU provider.

Now we also need to enable libvirt and load the KVM kernel module:

```
sudo systemctl enable --now libvirtd
sudo systemctl enable --now virtlogd

echo 1 | sudo tee /sys/module/kvm/parameters/ignore_msrs

sudo modprobe kvm
```

Finally we should be able to run a MacOS Docker container like [Monterey](https://github.com/sickcodes/Docker-OSX#monterey-):

```
docker run -it \
    --device /dev/kvm \
    -p 50922:10022 \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    -e GENERATE_UNIQUE=true \
    -e MASTER_PLIST_URL='https://raw.githubusercontent.com/sickcodes/osx-serial-generator/master/config-custom.plist' \
    sickcodes/docker-osx:monterey
```

or [Ventura](https://github.com/sickcodes/Docker-OSX#ventura-):

```
docker run -it \
    --device /dev/kvm \
    -p 50922:10022 \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    -e GENERATE_UNIQUE=true \
    -e MASTER_PLIST_URL='https://raw.githubusercontent.com/sickcodes/osx-serial-generator/master/config-custom.plist' \
    sickcodes/docker-osx:ventura
```

![](macos-ventura.png)

It's important to run the `docker` command without `sudo` (see https://github.com/sickcodes/Docker-OSX/issues/91#issuecomment-786794711):

> Run without sudo (GTK can't run in roots desktop because root isn't running a display server.)

Now we should see a QEMU firing up running MacOS in recovery mode:

![](macos-in-docker-on-linux.png)



## Troubleshooting MacOS in Docker

see also https://github.com/sickcodes/Docker-OSX#troubleshooting

I had some issues getting the container to run.

#### iptables

```
iptables: No chain/target/match by that name
```

See https://stackoverflow.com/questions/31667160/running-docker-container-iptables-no-chain-target-match-by-that-name - clearing all the chaings fixed it for me:

```
sudo iptables -t filter -F
sudo iptables -t filter -X
systemctl restart docker
```

#### gtk initialization failed

```
alsa: Could not initialize ADC
alsa: Failed to open `default':
alsa: Reason: No such file or directory
ALSA lib confmisc.c:855:(parse_card) cannot find card '0'
ALSA lib conf.c:5181:(_snd_config_evaluate) function snd_func_card_inum returned error: No such file or directory
ALSA lib confmisc.c:422:(snd_func_concat) error evaluating strings
ALSA lib conf.c:5181:(_snd_config_evaluate) function snd_func_concat returned error: No such file or directory
ALSA lib confmisc.c:1334:(snd_func_refer) error evaluating name
ALSA lib conf.c:5181:(_snd_config_evaluate) function snd_func_refer returned error: No such file or directory
ALSA lib conf.c:5704:(snd_config_expand) Evaluate error: No such file or directory
ALSA lib pcm.c:2666:(snd_pcm_open_noupdate) Unknown PCM default
alsa: Could not initialize ADC
alsa: Failed to open `default':
alsa: Reason: No such file or directory
audio: Could not create a backend for voice `adc'
gtk initialization failed
```

As described here https://github.com/sickcodes/Docker-OSX/issues/91#issuecomment-786794711 execute the following:

```
xhost +
```

#### qemu Gdk-WARNING 'BadAccess (attempt to access private resource denied)'. (Details: serial 220 error_code 10 request_code 130 (MIT-SHM) minor_code 1)

I got a:

```
(qemu:972): Gdk-WARNING **: 09:10:53.652: The program 'qemu' received an X Window System error.
This probably reflects a bug in the program.
The error was 'BadAccess (attempt to access private resource denied)'.
  (Details: serial 220 error_code 10 request_code 130 (MIT-SHM) minor_code 1)
  (Note to programmers: normally, X errors are reported asynchronously;
   that is, you will receive the error a while after causing it.
   To debug your program, run it with the GDK_SYNCHRONIZE environment
   variable to change this behavior. You can then get a meaningful
   backtrace from your debugger if you break on the gdk_x_error() function.)
```

Check what status the services `libvirtd` and `virtlogd` have - and restart them, if they have `inactive (dead)`:

```
$ systemctl status libvirtd                                                                                                                                                      ✔ 
○ libvirtd.service - Virtualization daemon
     Loaded: loaded (/usr/lib/systemd/system/libvirtd.service; enabled; preset: disabled)
     Active: inactive (dead) since Fri 2023-09-22 10:36:17 CEST; 47min ago
   Duration: 2min 2ms
TriggeredBy: ● libvirtd-ro.socket
             ● libvirtd.socket
             ● libvirtd-admin.socket
       Docs: man:libvirtd(8)
             https://libvirt.org
    Process: 742 ExecStart=/usr/bin/libvirtd $LIBVIRTD_ARGS (code=exited, status=0/SUCCESS)
   Main PID: 742 (code=exited, status=0/SUCCESS)
        CPU: 222ms

Sep 22 10:34:17 pikelinux systemd[1]: Starting Virtualization daemon...
Sep 22 10:34:17 pikelinux systemd[1]: Started Virtualization daemon.
Sep 22 10:36:17 pikelinux systemd[1]: libvirtd.service: Deactivated successfully.
$ systemctl status virtlogd                                                                                                                                                    3 ✘ 
○ virtlogd.service - Virtual machine log manager
     Loaded: loaded (/usr/lib/systemd/system/virtlogd.service; indirect; preset: disabled)
     Active: inactive (dead)
TriggeredBy: ● virtlogd.socket
             ○ virtlogd-admin.socket
       Docs: man:virtlogd(8)
             https://libvirt.org
$ sudo systemctl enable --now libvirtd
$ sudo systemctl enable --now virtlogd
```





## Install MacOS in the MacOS Docker container

As we saw the container is starting a bare Mac system in the recovery mode. Without manually reinstalling MacOS, we aren't able to use any software.

But reinstalling MacOS doesn't work out-of-the box inside the container using the wizard, because no disk is available for installation.

Therefore head over to the disk utilities and erase the biggest QEMU harddrive with around 200gb and name it e.g. `MyDockyOSX`.

Then the MacOS install wizard should show the `MyDockyOSX` disk as a possible installation harddrive:

![](mac-os-in-docker-install-wizard.png)

No worries: it's a virtual drive and only grows as needed :)

This actually may take a while!

After 2 hours or so the setup process should be ready and the MacOS configuration wizard will ask you about iCloud login etc.

If you managed to get past these screens, you should have a running MacOS:

![](mac-os-running-in-docker-on-linux.png)




## Save the hdd for later MacOS Docker runs (to not be forced to install MacOS again)

Steps taken from https://www.youtube.com/watch?v=wLezYl77Ll8

Run `docker ps -a` to find your MacOS container's id:

```
$ docker ps -a

e4e227957bb9   sickcodes/docker-osx:monterey   "/bin/bash -c 'sudo …"   48 minutes ago      Up 48 minutes                  0.0.0.0:50922->10022/tcp, :::50922->10022/tcp   sad_ganguly
```

Now inspect the container and search for `Upper`:

```
$ docker inspect e4 | grep Upper
"UpperDir": "/var/lib/docker/overlay2/fe1f1d8e462caa79fc89aa9bfe9892382ccda1d954a975f53c3f88875de36291/diff",
```

Visit the folder (which is our containers base file system) and go to `home/arch/OSX-KVM`:

```
$ su root
$ cd /var/lib/docker/overlay2/fe1f1d8e462caa79fc89aa9bfe9892382ccda1d954a975f53c3f88875de36291/diff/home/arch/OSX-KVM
$ ls -lha

[pikelinux OSX-KVM]# ls -lha
insgesamt 31G
drwxr-xr-x  8 jonashackt jonashackt  4,0K 21. Sep 13:58 .
drwxr-xr-x  3 jonashackt jonashackt  4,0K 19. Nov 2022  ..
-rw-r--r--  1 jonashackt jonashackt  711M 21. Sep 16:58 BaseSystem.img
drwxr-xr-x  2 jonashackt jonashackt  4,0K 21. Sep 13:58 bootdisks
-rw-r--r--  1 jonashackt jonashackt   39K 21. Sep 13:58 config-custom.plist
drwxr-xr-x  4 jonashackt jonashackt  4,0K 19. Nov 2022  EFI
drwxr-xr-x  2 jonashackt jonashackt  4,0K 21. Sep 13:58 envs
-rw-r--r--  1 jonashackt jonashackt   31G 21. Sep 16:59 mac_hdd_ng.img
-rwxr-xr-x  1 jonashackt jonashackt 1021K 21. Sep 13:58 macserial
-rw-------  1 jonashackt jonashackt     0 21. Sep 13:58 nohup.out
drwxr-xr-x  2 jonashackt jonashackt  4,0K 19. Nov 2022  OpenCore
drwxr-xr-x 20 jonashackt jonashackt  4,0K 21. Sep 13:58 OpenCorePkg
-rw-r--r--  1 jonashackt jonashackt  128K 21. Sep 16:54 OVMF_VARS-1024x768.fd
drwxr-xr-x  2 jonashackt jonashackt  4,0K 21. Sep 13:58 plists
-rw-r--r--  1 jonashackt jonashackt   116 21. Sep 13:58 serial_sets-2023-09-21-11:58:27.csv
-rw-r--r--  1 jonashackt jonashackt   100 21. Sep 13:58 serial.tsv
-rw-r--r--  1 jonashackt jonashackt    26 21. Sep 13:58 startup.nsh
-rw-r--r--  1 jonashackt jonashackt   56K 21. Sep 13:58 vendor_macs.tsv
```

The file `mac_hdd_ng.img` is our (big, after installation) hdd, where the installed MacOS resides.

Now we can copy that over and use it with other MacOS containers (leveraging the nacked image):

```
$ cp mac_hdd_ng.img /home/jonashackt/mac_hdd_ventura.img
$ su jonashackt
$ cd $HOME
$ sudo chown jonashackt mac_hdd_ventura.img 
```

Now we should be able to use the naked image like that, defining our own hdd image:

```
docker run -it \
    --device /dev/kvm \
    -p 50922:10022 \
    -v "${PWD}/mac_hdd_ventura.img:/image" \
    -v /tmp/.X11-unix:/tmp/.X11-unix \
    -e "DISPLAY=${DISPLAY:-:0.0}" \
    -e GENERATE_UNIQUE=true \
    -e MASTER_PLIST_URL='https://raw.githubusercontent.com/sickcodes/osx-serial-generator/master/config-custom.plist' \
    sickcodes/docker-osx:naked
```

And voilà our container should start with MacOS fully installed!


## Optimizing performance of the dockerized MacOS

See https://github.com/sickcodes/osx-optimizer

#### Disable spotlight indexing on macOS to heavily speed up Virtual Instances

Inside the MacOS instance, open a terminal and execute:

```
sudo mdutil -i off -a
```



## Taming the terminal

https://forum.manjaro.org/t/bash-with-autocomplete-and-fancy-flags/112108

https://github.com/romkatv/powerlevel10k



## Small tweaks

* Switching back to last tab on Firefox: https://superuser.com/questions/290704/switching-back-to-last-tab-on-firefox

* Night mode: https://www.reddit.com/r/ManjaroLinux/comments/ogf1iy/turn_on_night_mode/





# Productivity Software on Linux

## Enable flathub Repository in Manjaro package management

Simply activate in Add/Remove Programs, since it's already installed - as the docs state https://flatpak.org/setup/Manjaro

> Flatpak is installed by default on Manjaro 20 or higher.

> To enable its support, navigate to the Software Manager (Add/Remove Programs)

> Click on the triple line menu [or dots depending on the Desktop Environment] on the right, in the drop down menu select "Preferences"

> Navigate to the "Flatpak" tab and slide the toggle to Enable Flatpak support (it is also possible to enable checking for updates, which is recommended).

Flatpack is super useful to install many Desktop applications like MS Teams, Zoom, Slack etc.


## Spotlight like search

https://github.com/cerebroapp/cerebro

Install it via the AUR package https://aur.archlinux.org/packages/cerebro-bin


## Microsoft Teams

Microsoft announced to discontinue the Linux client in favour of a Progressive Web App (PWA), which is integrated in Microsoft Edge for Linux:

https://techcommunity.microsoft.com/t5/microsoft-teams-blog/microsoft-teams-progressive-web-app-now-available-on-linux/ba-p/3669846

So I installed Microsoft Edge from the Manjaro Package Manager using the flatpack package and then went to `teams.microsoft.com` and clicked on the three dots in Edge, then `Apps` and then `Install this site as an app`. 

Alternativeley you can use the (also flatpack managed) unofficial `Teams for Linux` client (which is hosted on GitHub https://github.com/IsmaelMartinez/teams-for-linux and powered by Electron).


## Zoom

Theres's also simply a flatpack package available: https://flathub.org/apps/us.zoom.Zoom

Install it via Manjaros package manager (gui or command line.


## Slack

Flatpack is here to help again: https://flathub.org/apps/com.slack.Slack


## Miro

There's a snap available here https://snapcraft.io/install/miro/manjaro

To be able to use Snapcraft on Manjaro we need to install it first - again either via gui or command line:

```
sudo pacman -S snapd
```

Also enable the systemd unit that manages the main snap communication socket:

```
sudo systemctl enable --now snapd.socket
```

Restart your system to ensure snap’s paths are updated correctly. 

Now install Miro via snap:

```
sudo snap install miro
```


## SIP Telefone app for Linux

If you're like me you want to be callable even when you're mobile phone is on airplane mode. The easiest solution is a SIP phone client software, where you just configure your SIP credentials and can use your laptop or desktop machine to have phone calls.

There's huge list of SIP clients around, but already on my Android phone there are only a few that really work.

While writing this docs I found out about linphone https://www.linphone.org/technical-corner/linphone, which has clients for nearly every OS. And it's also OpenSource, developed on GitLab.com https://gitlab.linphone.org/BC/public/linphone-desktop

I installed the AUR AppImage package https://aur.archlinux.org/packages/linphone-desktop-appimage (the other https://aur.archlinux.org/packages/linphone-desktop didn't work on my machine).

My first tests worked like a charm!



## Google Drive Desktop file sync

On my Mac I had a Desktop App to sync my Drive files into my file manager. Sadly there's no Google Drive Desktop for Linux https://support.google.com/drive/answer/10838124

There's a great post on baeldung about this topic: https://www.baeldung.com/linux/google-drive-guide (see also https://askubuntu.com/questions/1390151/google-drive-in-ubuntu-with-full-local-copy)

But we have some alternatives. First thing I checked was `gnome-online-accounts` (see [the docs](https://help.gnome.org/users/gnome-help/stable/accounts-which-application.html.en)). This package is already pre-installed on Manjaro. Simply head over to `preference / online accounts` and log in to your Google account. Now the Gnome file manager should have a new entry, where you can access your Google Drive files: 

![](gnome-online-accounts-google-drive-integration.png)

But [they aren't stored locally sadly](https://gitlab.gnome.org/GNOME/nautilus/-/issues/784), so they will be downloaded every time you access them. And as the baeldung post states, the file names are completely obscured on the command line. E.g. if you have a file called `bla` in the file manager, it's named like `11lfzX-8dH_eWtf2JWa3caRtodOnlXDbN` on the command line and you even need to access it in a weird way like `cd '/run/user/1000/gvfs/google-drive:host=gmail.com,user=myemail'`.


If you need locally stored files with Google Drive, IMHO there's no perfect solution. Maybe https://www.insynchq.com is worth a try, but also costs some 30 bucks...




## Dropbox Linux client

There's a official Dropbox Linux client https://help.dropbox.com/de-de/installs/linux-commands and also a AUR package https://aur.archlinux.org/packages/dropbox

Compared to Drive the integration is superb. Start the app via the application menu and after the login you will find a Dropbox icon right at the top of your Gnome Desktop - just as you are used to on a Mac:

![](dropbox-gnome-integration.png)

Also you can drag the folder `Dropbox` in your profile directory into the left menu bar of the Gnome file manager. And voila: you have the same integration as on a Mac!

![](dropbox-gnome-filemanager-integration.png)




# Development software

## VSCode

There are plenty of ways to install VSCode to Linux / Manjaro. First I tried the flathub package, but then I realised that a Flatpack packaged app is really separated from the rest of the system. Since it runs in a container. So no other development tools or frameworks will work inside the VSCode container, we would have to install it all into it... 

Although I love the idea of container packaged software, I don't really wanted to live it that kind of hard fashioned with my development setup. Sure, development containers would also work. But I wanted kind of a more traditional installation. And luckily there's the AUR package https://aur.archlinux.org/packages/visual-studio-code-bin . Beware of the `-bin` in the name of the package, the other one installs the `Code - OSS` app instead. [See the differences here](https://github.com/microsoft/vscode/wiki/Differences-between-the-repository-and-Visual-Studio-Code).




# Misc

## Printer setup

Here I learned to __ALWAYS__ search for an AUR package first!

I have an old Canon MX870 printer, which has ultra low cost and separate printer cartridges. So I went to the Canon Driver page and it was simply empty. No Linux drivers at all. A google search got me to [driverscollection.com](https://de.driverscollection.com/?file_cid=4527114398460c3f541c53ebcfb), but there were only `.deb` (Ubuntu, Debian) and `.rpm` (Fedora, SUSE) packages. The build from source also didn't work, since file were missing... (I already searched for "How to Install .DEB files in Arch Based Distros" - [don't do that!](https://forum.manjaro.org/t/how-to-install-deb/34452/3)).

But than I simply searched for `canon mx870 linux driver arch` - and there really was an AUR package for my printer! Horay!

Now installing `canon-pixma-mx870-complete` gave me some errors - but I managed to solve them. The first error indicated, that I didn't have `autoconf` installed. So I installed it with `pamac`. The second error got me to the missing `automake`, which I also installed. Then I had a strange error with the [lib32-libusb-compat package](https://archlinux.org/packages/core/x86_64/libusb/), which is needed by the `canon-pixma-mx870-complete` package: 

```
syntax error near unexpected token `LIBUSB,'
```

Luckily [this thread](https://bbs.archlinux.org/viewtopic.php?id=251492) and also the [`lib32-libusb-compat` AUR package site](https://aur.archlinux.org/packages/lib32-libusb-compat) got me to the problem: I needed to install [`base-devel` AUR package](https://archlinux.org/packages/core/any/base-devel/) first!

Now running `pamac install lib32-libusb-compat canon-pixma-mx870-complete` ran like a charm!

The scanner now worked using the app `Document Scanner`.


But there was no printer configured out-of-the-box. Although the driver (`PPD` files) seem to be present correctly.

In the normal settings dialog had a `add printer` button, but my Canon network printer wasn't found there and I couldn't add it though:

![](system-settings-printer.png)

So I went over to the console and started the CUPS gui directly just executing `system-config-printer`. Now adding a new printer in the CUPS gui also shows `network printers` - and there my Canon MX870 showed up!

![](cups-gui-network-printer-found.png)

Really nice. I clicked `forward` and the Driver was automatically set to `Canon MX870 series - CUPS+Gutenprint v5.3.4 Simplified`, which may also work - but we installed the original `Canon MX870 series Ver.3.30` right?! Therefore I changed the created printer after the wizard is finished and clicked on `brand and model` and change... and then selected the correct driver from the database:

![](change-printer-driver.png)

I hoped my printer would work now, but trying to print a example page didn't work. A final piece was missing.

But finally I found it: In the printer's settings under `guidelines` the condition was not `activated`. I activated the printer here and everything worked fine!






# Links

https://www.makeuseof.com/how-to-install-and-remove-packages-arch-linux/

Optional dependencies in Manjaro's pamac: 

https://forum.manjaro.org/t/pamac-how-to-install-all-optional-dependencies/59041/3

https://www.reddit.com/r/archlinux/comments/1z9y3l/install_optional_dependencies/


https://wiki.archlinux.org/title/docker

https://dev.to/kenji_goh/got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket-3dne

https://www.digitalocean.com/community/questions/how-to-fix-docker-got-permission-denied-while-trying-to-connect-to-the-docker-daemon-socket

https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user



# Open Topics

* VS Code and Dev 

* Get Linux keyboard (with print, no Apple cmd, F-keys etc.)

* Samsung Smart Switch 

