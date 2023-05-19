<!-- CSS Style -->
<style>
.back-to-top {
  color:white; 
  background-color:#14b8a6; 
  padding: 2px 8px; 
  border-radius: 100px;
  text-decoration: none;
  height: fit-content;
  align-self: center;
  transition: 0.1s;
}

.back-to-top:hover {
  background: #02d1ba;
  text-decoration: none;
}

.back-to-top:active {
  background: #0a665b;
  text-decoration: none;
}

.table {
  display: flex;
  flex-direction: column;
  background: #23252e;
  border-radius: 12px;
  padding: 4px;
  width: 310px;
  border: 2px solid #14b8a6;
}

.table h2 {
  margin: auto;
}

.table a {
  display: block;
  color: white;
  margin-left: 10px;
  margin-right: 10px;
  cursor: pointer;

  transition: 0.3s;
}

.table a:hover{
  background: gray;
  transition: 0.3s;
  text-decoration: none;
  border-radius: 100px;
  margin:0px;
  padding: 0 10px;
}

.horizontal-line {
  width: 95%;
  background-color: gray;
  height: 2px;
  margin: auto;
  border-radius: 100px;
  margin-top: 5px;
  margin-bottom: 5px;
}

</style>

<!-- Content Start -->
# About this repo
This file is made for Fedora 35, 36, 37 
C31

**30 April 2022**

*** 

<div class="table">
  <h2>Table of content</h2>
  <div class="horizontal-line"></div>
  <a href="#list-of-useful-website">List of useful website</a>
  <a href="#optimus-nvidia">Optimus Nvidia Stuff</a>
  <a href="#linux-battery-saver">Linux Battery Saver</a>
  <a href="#miscellaneous">Miscellaneous</a>
</div>

***

# List of useful website
- https://rpmfusion.org/Howto/Optimus
- https://www.markdownguide.org/basic-syntax/
- https://linrunner.de/tlp/installation/fedora.html
- http://wiki.get-good.net/index.php?title=RFactor_Skinning
- https://www.markdownguide.org/cheat-sheet/


# Add things to PATH
1. Open up Terminal
> $ nano .bashrc

2. Add to the bottom the path you want
> export PATH = "$PATH:[program-you-want]"

3. Refresh Bash to keep changes
> $ source .bashrc

4. Validate using 'which [programs]'
> $ which flutter
> /Home/flutter/bin/flutter 


# Useful Tools
1. nethogs => Network Monitor
2. gt => TunnelDPI

<div 
style="display: flex; 
  justify-content: space-between;
  height: fit-content">
  <h1 id="optimus-nvidia" style="margin:0; border-bottom: 0">Optimus Nvidia Stuff</h1>
  <a class="back-to-top" href="#about-this-repo">🔼 Back to top 🔼</a>
</div>

***
## NVIDIA PrimaryGPU Support 
> https://rpmfusion.org/Howto/Optimus 

Using PrimaryGPU allows to use the NVIDIA driver by default instead of the iGPU. This is also required in order to use external display when internally connected from the NVIDIA hardware. Unfortunately, setting this option automatically when an external display is connected is not supported by NVIDIA at this time. To recover this previous behaviour, you can use:

> $ cp -p /usr/share/X11/xorg.conf.d/nvidia.conf /etc/X11/xorg.conf.d/nvidia.conf

And edit the file to use:
> Option "PrimaryGPU" "yes"

/!\ SSDM and lightdm display manager users: Please Have a look at the ArchLinux wiki about configuring display manager. In Fedora, Gnome users don't need this change.

***

## Finer-Grained Control of GLX + OpenGL 
> https://rpmfusion.org/Howto/Optimus  

For GLX + OpenGL, the environment variable `NV_PRIME_RENDER_OFFLOAD_PROVIDER` provides finer-grained control. While `NV_PRIME_RENDER_OFFLOAD=1` tells GLX to use the first NVIDIA GPU screen, `NV_PRIME_RENDER_OFFLOAD_PROVIDER` can use an RandR provider name to pick a specific NVIDIA GPU screen, using the NVIDIA GPU screen names reported by `xrandr --listproviders`.

Examples:  
> __NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia glxgears  
__NV_PRIME_RENDER_OFFLOAD_PROVIDER=NVIDIA-G0   __GLX_VENDOR_LIBRARY_NAME=nvidia glxgears  

***

## Finer-Grained Control of Vulkan
> https://rpmfusion.org/Howto/Optimus

The `NV_PRIME_RENDER_OFFLOAD` environment variable causes the special Vulkan layer `VK_LAYER_NV_optimus` to be loaded. Vulkan applications use the Vulkan API to enumerate the GPUs in the system and select which GPU to use; most Vulkan applications will use the first GPU reported by Vulkan. The `VK_LAYER_NV_optimus` layer causes the GPUs to be sorted such that the NVIDIA GPUs are enumerated first. For finer-grained control, the `VK_LAYER_NV_optimus` layer looks at the `VK_LAYER_NV_optimus` environment variable. The value NVIDIA_only causes `VK_LAYER_NV_optimus` to only report NVIDIA GPUs to the Vulkan application. The value non_NVIDIA_only causes `VK_LAYER_NV_optimus` to only report non-NVIDIA GPUs to the Vulkan application.

Examples:

> __NV_PRIME_RENDER_OFFLOAD=1 __VK_LAYER_NV_optimus=NVIDIA_only vkcube

***

## Configure Graphics Applications to Render Using the GPU Screen
To configure a graphics application to be offloaded to the NVIDIA GPU screen, set the environment variable `NV_PRIME_RENDER_OFFLOAD` to 1. If the graphics application uses Vulkan, that should be all that is needed. If the graphics application uses GLX, then also set the environment variable `GLX_VENDOR_LIBRARY_NAME` to nvidia, so that GLVND loads the NVIDIA GLX driver. NVIDIA's EGL implementation does not yet support PRIME render offload.

Examples:

> __NV_PRIME_RENDER_OFFLOAD=1 vkcube  
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia glxinfo | grep vendor
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia ./osu.AppImage 

***

## Configuration on Hybrid Nvidia + Intel IGP
https://download.nvidia.com/XFree86/Linux-x86_64/510.68.02/README/randr14.html

<br>
<div 
style="display: flex; 
  justify-content: space-between;
  height: fit-content">
  <h1 id="linux-battery-saver"
  style="margin:0; border-bottom: 0">Linux Laptop Battery Saver</h1>
  <a class="back-to-top" href="#about-this-repo">🔼 Back to top 🔼</a>
</div>

***

## Using TLC to saving battery on laptop
> https://linrunner.de/tlp/installation/fedora.html

Step by Step

> ### Download TLP using DNF  
> \$ sudo dnf install tlp tlp-rdw  
>
> ### Uninstall the conflicting power-profile-daemon package  
> \$ sudo dnf remove power-profiles-daemon
>
> ### Enable TLP's Service  
> \$ systemctl enable tlp.service
>
> ### Mask some service to avoid conflicts and assure proper operation of TLP's Radio Device Switching  
> \$ systemctl mask systemd-rfkill.service systemd-rfkill.socket

### Changing and Applying Configuration on TLC
> $ sudo tlp start

### Disable TLC and using vanilla GNOME Power Management
> \$ sudo dnf install power-profiles-daemon  
> \$ systemctl disable tlp.service  
> \$ systemctl unmask systemd-rfkill.service systemd-rfkill.socket  

<br>
<div 
style="display: flex; 
  justify-content: space-between;
  height: fit-content">
  <h1 id="miscellaneous"
  style="margin:0; border-bottom: 0">Miscellaneous</h1>
  <a class="back-to-top" href="#about-this-repo">🔼 Back to top 🔼</a>
</div>

***

##  Pressing CTRL+. (Period) will show `e` in underscore
This is shortcut to Emoji Annotation. you can disabled it by going in terminal
>$ ibus-setup

IBus Preferences will show up, go to Emoji Tab, delete Emoji annotation

