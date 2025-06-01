# mac-to-linux
Docs on me leaving my Apple/Mac(book) in favour of a Linux machine


# Why going from Mac to Linux?

After many years (nearly 9) of using a MacBook and fancy Apple stuff I found myself in thinking about leaving the golden cage.

I already dropped my iPhone in favour of an Android phone (currently Samsung Galaxy S23, mainly because of the great camera, the small form factor and the long update period) - and I don't really use iPhoto and others anymore. Also tethering doesn't work between an Android phone and a Mac (really! in 2023). 

Since I had a hard accident this year with a long recovery period I started to build a gaming PC just for fun - after maybe 10 years! At first I thought it was because I really wanted to play the new Diablo version (I loved the old v2 back when I was a teenager), but I experienced the fun building your own rig the way YOU want it. From the parts YOU choose from the MARKET or used market prices (and NOT from Apple prices).

And I remembered my old values of free standards, free software, free choice... wow - why did I opt for Apple devices in the first place?

So as time went by the thought of switching my main machine from Mac to a Linux box got bigger and bigger. Finally I cought myself in reading through laptop tests at https://www.notebookcheck.com :)


## Choosing a distro: Why not going rolling release & AUR?

That one is hard. Because if you ask, everybody yells at you :D 

So beeing a software architect I know the only way to get around the discussions is to define your requirements.

I tried to come up with some:

* Rolling releases: I hate big releases! So I don't want to reinstall my system every year like most of the people are used to from MacOS, Ubuntu, Fedora etc. I want a rolling release distro.

There are some popular options with rolling releases like https://distrowatch.com/table.php?distribution=endeavour

Here's a great example, why Manjaro is a fantastic choice for software developers: You get up to date versions of the libraries you want - in the default system package manager: https://rclone.org/install/#package-manager and this image:

![](https://repology.org/badge/vertical-allrepos/rclone.svg?columns=3)

`tbd: more info on distro choosing`

Finally I opted for Manjaro Linux https://manjaro.org/, which is based on Arch Linux.


# Sections

These docs are separated into sections, which contain specific information:

## [01 Prepare, install & configure Manjaro Linux](01-prepare-configure-manjaro-encrypted-ssd-fingerprint.md)

This section contains steps to 

* prepare your machine for installation (UEFI, hardware-based SSD Encryption, Dual Boot Windows for Thinkpad Lenovo Vantage software (BIOS updates!) and Fingerprint configuration in UEFI OPAL SSD)
* Install Manjaro Linux
* Alternative to self-encrypting SSDs: full software-based file-system encryption with LUKS
* Enable AUR & Flatpack
* Fingerprint for Gnome & sudo
* Backup with restic/resticprofile
* Firewall configuration
* HiDPI Scaling

## [02 Productivity Software on Linux](02-software.md)

This section contains how-to-install & configure software I use (or need to use for work):

* Microsoft Teams, Zoom, Slack, Miro
* SIP Telefone app for Linux
* Google Drive Desktop Sync & Dropbox
* Remarkable
* Remote Desktop
* VSCode & Tiling Terminal
* Printer Setup
* Balsamic Mockups on Linux with Bottles


# [03 Virtualized Windows & MacOS on Linux](03-virtualization-windows-macos.md)

This section covers using Windows and MacOS on Linux.


# [04 Hardware recommendations](04-laptop-hardware-recommendations.md)

This section contains details on how to find a good Laptop to run Linux on. And how to configure a Thinkpad in detail:

* Finding a good laptop for Linux
* Configure NVidia graphics card (driver, external Display, power consumption)
* Getting your laptop silent & prolong battery life
  * envycontrol
  * auto-cpufreq
  * Fancontrol
  * Thinkpads: thinkfan


# [05 AI on Linux](05-ai-on-linux.md)

This section covers docs about AI on Linux.













