# AI on Linux

This section covers docs about AI on Linux.

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