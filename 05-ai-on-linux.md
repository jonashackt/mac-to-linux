# 05 AI on Linux

This section covers docs about AI on Linux.


## Voice-to-Model with voxtype.io

Input context using your voice!

https://voxtype.io/

https://voxtype.io/docs/#post-installation-setup

Gnome Wayland:

Install voxtype and dootool (wtype doesn't work under Gnome/Wayland):
> "On KDE/GNOME Wayland, wtype will fail and voxtype will use dotool or ydotool."

```shell
pamac install voxtype-bin dotool
# choose a model
voxtype setup model

# check setup
voxtype setup check

# manually run voxtype
voxtype
# press FN+K and hold to record

# systemctl daemon
systemctl --user enable --now voxtype

# enable gpu accelleration
sudo voxtype setup gpu --enable
systemctl --user restart voxtype
systemctl status voxtype --user

# auf Deutsch umstellen
nano ~/.config/voxtype/config.toml
language = ["en","de"]
systemctl --user restart voxtype

# use it
Fn + K
```


y and z swapped: use dotool instead of ydotool https://github.com/peteonrails/voxtype/blob/main/docs/CONFIGURATION.md

```
[output]
# Primary output mode: "type" or "clipboard"
# - type: Simulates keyboard input at cursor position (requires ydotool)
# - clipboard: Copies text to clipboard (requires wl-copy)
mode = "type"
dotool_xkb_layout = "de"
```

There's also a super handy CLI-based configuration frontend, open it via:

```shell
voxtype configure
```



## AI support for running local LLMs

This is one of the best articles I know if you want to dive into hardware considerations: https://www.reddit.com/r/XMG_gg/comments/18uf17w/psa_local_ai_acceleration_in_xmg_schenker_laptops/

Up until I found it I really thought, my whole Mac-to-Linux project might fail since LLMs just only run great on a Mac with it's unified architecture.

But... :)

Now what I really really love about Schenker / XMG / Tuxedo: they started a list for brand-agnostic performance comparison of CPU NPUs:

> We would like to maintain a brand-agnostic performance comparison between the AI-acceleration capabilities of current CPU platforms. As a starter, we use the metric “TOPS” (TeraOPS, understood as “trillion operations per second”) as a performance indicator. Later, we may also add industry-standard benchmark scores. 

The list can be found here https://docs.google.com/spreadsheets/u/1/d/e/2PACX-1vTYc3HUjlRoUr96ilnb1YxWC2r4OSeyUltiNLzgWTGI3Bnrq-NzQwA7ZHAPKfiCVF1RdPywM5F_h4bl/pubhtml?utm_source=pocket_saves


### AMD Ryzen AI Support on Linux

AMD released a driver just in January 2024, which sadly is not part of the official Kernel (yet):

https://www.phoronix.com/news/AMD-XDNA-Linux-Driver-Ryzen-AI

