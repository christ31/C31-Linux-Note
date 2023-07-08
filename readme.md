<!-- Content Start -->
# About this repo
Tips, problem, and a lot of things when using Linux

This file is made for Fedora 35, 36, 37, 38   
C31

Last Edit: **4 July 2023**

***

## Table of Content
> 1 [Gnome Extensions Used](#gnome-extensions-used)  
> 2 [List of useful website](#list-of-useful-website)  
> 3 [Optimus Nvidia Stuff](#nvidia-optimus-stuff)  
> 4 [Linux Battery Saver](#linux-battery-saver)   
> 5 [Android on Linux](#android-on-linux)    
> 6 [Wayland Stuff](#wayland-stuff)   
> 7 [Random Small Fix](#random-small-fix)    
> 8 [Miscellaneous](#miscellaneous)    

***
# Gnome Extensions Used
- Blur My Shell | [GitHub](https://github.com/aunetx/blur-my-shell)
- Burn My Windows | [GitHub](https://github.com/Schneegans/Burn-My-Windows)
- Current screen only in windows switcher | [GitHub](https://github.com/mmai/Current_screen_only_on_window_switcher)
- Dash to Panel | [GitHub](https://github.com/home-sweet-gnome/dash-to-panel)
- Desktop Clock | [GitLab](https://gitlab.com/AndrewZaech/azclock)
- Gnome 4x UI Improvements [GitHub](https://github.com/axxapy/gnome-ui-tune)
- gTile | [GitHub](https://github.com/gTile/gTile)
- Internet Speed Monitor | [GitHub](https://github.com/rishuinfinity/InternetSpeedMonitor)

<br>

# List of useful website
- https://linrunner.de/tlp/installation/fedora.html
- https://rpmfusion.org/Howto/Optimus
- https://www.markdownguide.org/basic-syntax/
- https://www.markdownguide.org/cheat-sheet/
- http://wiki.get-good.net/index.php?title=RFactor_Skinning

<br>

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

<br>

# Useful Tools
nethogs => Network Monitor   
gt => TunnelDPI   
nm-connection-editor => Connection Settings   
dconf-editor => A graphical viewer and editor of applications’ internal settings   

<p align="center">
<a align="center" href="#about-this-repo">🔼 Back to top 🔼</a>
</p>

# Nvidia Optimus Stuff

## NVIDIA PrimaryGPU Support 
> https://rpmfusion.org/Howto/Optimus 

Using PrimaryGPU allows to use the NVIDIA driver by default instead of the iGPU. This is also required in order to use external display when internally connected from the NVIDIA hardware. Unfortunately, setting this option automatically when an external display is connected is not supported by NVIDIA at this time. To recover this previous behaviour, you can use:

> $ cp -p /usr/share/X11/xorg.conf.d/nvidia.conf /etc/X11/xorg.conf.d/nvidia.conf

And edit the file to use:
> Option "PrimaryGPU" "yes"

/!\ SSDM and lightdm display manager users: Please Have a look at the ArchLinux wiki about configuring display manager. In Fedora, Gnome users don't need this change.

<br>

## Finer-Grained Control of GLX + OpenGL 
> https://rpmfusion.org/Howto/Optimus  

For GLX + OpenGL, the environment variable `NV_PRIME_RENDER_OFFLOAD_PROVIDER` provides finer-grained control. While `NV_PRIME_RENDER_OFFLOAD=1` tells GLX to use the first NVIDIA GPU screen, `NV_PRIME_RENDER_OFFLOAD_PROVIDER` can use an RandR provider name to pick a specific NVIDIA GPU screen, using the NVIDIA GPU screen names reported by `xrandr --listproviders`.

Examples:  
> __NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia glxgears  
__NV_PRIME_RENDER_OFFLOAD_PROVIDER=NVIDIA-G0   __GLX_VENDOR_LIBRARY_NAME=nvidia glxgears  

<br>

## Finer-Grained Control of Vulkan
> https://rpmfusion.org/Howto/Optimus

The `NV_PRIME_RENDER_OFFLOAD` environment variable causes the special Vulkan layer `VK_LAYER_NV_optimus` to be loaded. Vulkan applications use the Vulkan API to enumerate the GPUs in the system and select which GPU to use; most Vulkan applications will use the first GPU reported by Vulkan. The `VK_LAYER_NV_optimus` layer causes the GPUs to be sorted such that the NVIDIA GPUs are enumerated first. For finer-grained control, the `VK_LAYER_NV_optimus` layer looks at the `VK_LAYER_NV_optimus` environment variable. The value NVIDIA_only causes `VK_LAYER_NV_optimus` to only report NVIDIA GPUs to the Vulkan application. The value non_NVIDIA_only causes `VK_LAYER_NV_optimus` to only report non-NVIDIA GPUs to the Vulkan application.

Examples:

> __NV_PRIME_RENDER_OFFLOAD=1 __VK_LAYER_NV_optimus=NVIDIA_only vkcube

<br>

## Configure Graphics Applications to Render Using the GPU Screen
To configure a graphics application to be offloaded to the NVIDIA GPU screen, set the environment variable `NV_PRIME_RENDER_OFFLOAD` to 1. If the graphics application uses Vulkan, that should be all that is needed. If the graphics application uses GLX, then also set the environment variable `GLX_VENDOR_LIBRARY_NAME` to nvidia, so that GLVND loads the NVIDIA GLX driver. NVIDIA's EGL implementation does not yet support PRIME render offload.

Examples:

> __NV_PRIME_RENDER_OFFLOAD=1 vkcube  
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia glxinfo | grep vendor
__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia ./osu.AppImage 

<br>

## Configuration on Hybrid Nvidia + Intel IGP
https://download.nvidia.com/XFree86/Linux-x86_64/510.68.02/README/randr14.html

<p align="center">
<a align="center" href="#about-this-repo">🔼 Back to top 🔼</a>
</p>

# Linux Laptop Battery Saver

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

<p align="center">
<a align="center" href="#about-this-repo">🔼 Back to top 🔼</a>
</p>

# Android on Linux

https://docs.waydro.id/debugging/troubleshooting   
https://copr.fedorainfracloud.org/coprs/aleasto/waydroid/

## To restart Waydroid, go to Terminal
> $ sudo waydroid container restart

## How to install
> $ sudo waydroid init -c https://ota.waydro.id/system -v https://ota.waydro.id/vendor -s GAPPS

## How to reset Waydroid
1. Stop the waydroid-container.service  
> $ systemctl stop waydroid-container.service

2. Cleanup 
> $ sudo rm -rf /var/lib/waydroid /home/.waydroid ~/waydroid ~/.share/waydroid ~/.local/share/applications/*aydroid* ~/.local/share/waydroid

3. Initialise 
> $ waydroid init -f or # waydroid init -f -i /usr/share/waydroid-extra/images  
Note: Didn't work, see above ## How to install

4. Start the service again 
> $ systemctl start waydroid-container.service

<p align="center">
<a align="center" href="#about-this-repo">🔼 Back to top 🔼</a>
</p>

# Wayland Stuff
<p align="center">
  Table of Content
</p>

> 1 [Diagonal Split Rendering](#wayland-bug)  
> 2 [Force Disable VSYNC](#force-disable-vsync)  
> 3 [Restarting Gnome-Shell in Wayland](#restarting-gnome-shell-in-wayland)  
> 4 [Using X11 Backend in Wayland](#using-x11-backend-in-wayland)   
> 5 [Scrcpy window doesn't show up in Fedora 36 Wayland](#scrcpy-window-doesnt-show-up-in-fedora-36-wayland)  
> 6 [(Unfixed) Wayland screen sharing on Chrome in Google Meet crash the browser](#unfixed-wayland-screen-sharing-on-chrome-in-google-meet-crash-the-browser)    
> 7 [Deskreen Black Screen in Wayland](#deskreen-black-screen-in-wayland)

<br>

## Diagonal Split Rendering
Diagonal split rendering bug / 1 frame lag rendering when FPS below 60

https://www.reddit.com/r/linux_gaming/comments/swljmt/diagonal_tear_line_in_wayland/
https://gitlab.gnome.org/GNOME/mutter/-/issues/2151
https://gitlab.freedesktop.org/xorg/xserver/-/issues/1317
https://github.com/NVIDIA/open-gpu-kernel-modules/issues/187

<br>

## Force Disable VSYNC
https://dri.freedesktop.org/wiki/ConfigurationOptions/

> $ vblank_mode=0 ./osu.AppImage    

<br>

## Restarting Gnome-Shell in Wayland
https://gitlab.gnome.org/GNOME/gnome-shell/-/issues/5634

All apps will crash

1. Go to TTY
Use Ctrl + Alt + F2 or Ctrl + Alt + F3

2. Stop GDM 
> sudo systemctl stop gdm

3. Start GDM
> sudo systemctl start gdm

<br>

## Using X11 Backend in Wayland
in Terminal, do
> $ GDK_BACKEND=x11 ./[programs]

<br>

## Scrcpy window doesn't show up in Fedora 36 Wayland
https://github.com/Genymobile/scrcpy/issues/3431

> $ SDL_VIDEODRIVER=x11 scrcpy   
>
> $ SDL_VIDEODRIVER=x11 scrcpy -b 2M --max-fps=15

<br>

## (Unfixed) Wayland screen sharing on Chrome in Google Meet crash the browser
**Affects:** Entire Screen, A Window  
**Version:** Version 114.0.5735.133 (Official Build) (64-bit)  

**My Fix:**  
Just launch in Terminal
> $ google-chrome-stable

**Suggested Solutions:**  
type in address bar  
> chrome://flags/#enable-webrtc-pipewire-capturer  

WebRTC PipeWire support > Enable

Note: Weirdly, this doesn't happen with Chromium and Firefox

<br>

## Deskreen Black Screen in Wayland
Add this parameter to enable PipeWireCapture in wayland session
> $ ./Deskreen-1.0.12.AppImage --enable-features=WebRTCPipeWireCapturer


<p align="center">
<a align="center" href="#about-this-repo">🔼 Back to top 🔼</a>
</p>


# Random Small Fix
<p align="center">
  Table of Content
</p>

> 1 [Wifi keep disconnecting](#wifi-keep-disconnecting)  
> 2 [Zoom 5.10.4 (2845) Crashing on F36](#zoom-5104-2845-crashing-on-f36)  
> 3 [Pressing CTRL+. (Period) will show `e` in underscore](#pressing-ctrl-period-will-show-e-in-underscore)  
> 4 [Cleaning up logs/journal](#cleaning-up-logsjournal)   
> 5 [MangoHud OpenGL Fix](#mangohud-opengl-fix)    
> 6 [Video Downloader Change Download Location](#video-downloader-change-download-location)   
> 7 [Convert WEBM to MP4 using FFMPEG](#convert-webm-to-mp4-using-ffmpeg)   
> 8 [Steam (Controller Missmatch Button)](#steam-controller-missmatch-button)    
> 7 [Publish New Repo on VSCode default to master](#when-using-vscode-to-publish-new-repo-to-github-it-will-default-to-master)    
> 9 [Shared Linux Folder in Windows Guest (QEMU/KVM)](#shared-linux-folder-in-windows-guest-qemukvm)    
> 10 [N-Key Rollover Problem in Linux](#n-key-rollover-problem-in-linux)    
> 11 [Disable Gnome Screenshot Sound when taking screenshot](#disable-gnome-screenshot-sound-when-taking-screenshot)  
> 12 [Realme 6 cannot connect to Gnome Android Hotspot](#realme-6-cannot-connect-to-gnome-android-hotspot)    
> 13 [Exclude a certain package when running dnf update](#exclude-a-certain-package-when-running-dnf-update)    
> 14 [MikuMikuDance Fixes](#mikumikudance-mmd-fixes)
> 15 [Drag and Drop doesn't works most of the times in Wayland](#dnd-doesnt-works-most-of-the-times-in-wayland-open-issue)

<br>

## Wifi keep disconnecting
1. Solution: Disable Wifi Power saving   
https://linrunner.de/tlp/settings/network.html

> $ sudo nano /etc/tlp.conf  
WIFI_PWR_ON_AC=off      
WIFI_PWR_ON_BAT=off    
>
> $ sudo tlp start   

2. Solution 2: Disable Bluetooth

## Zoom 5.10.4 (2845) Crashing on F36
This is related to the GPU sanbox I think   
https://community.zoom.com/t5/Meetings/Version-5-10-0-crashing-at-startup-on-Fedora-35/td-p/51487

This command to disable GPU sanboxing on Zoom works
> $ zoom --disable-gpu-sandbox

<br>

##  Pressing CTRL+. (Period) will show `e` in underscore
This is shortcut to Emoji Annotation. you can disabled it by going in terminal
>$ ibus-setup

IBus Preferences will show up, go to Emoji Tab, delete Emoji annotation

<br>

## Cleaning up logs/journal
https://www.linuxuprising.com/2019/10/how-to-clean-up-systemd-journal-logs.html

One time usage
> $ sudo journalctl --rotate   
>
> $ sudo journalctl --vacuum-time=1s

Configuration location
>$ nano /etc/systemd/journald.conf

<br>

## MangoHud OpenGL Fix
OpenGL games may also need dlsym hooking. Add `--dlsym` or `MANGOHUD_DLSYM=1` env var to your command like `mangohud --dlsym %command%` for Steam.

<br>

## Video Downloader Change Download Location
Change the download location

> $ flatpak run --command=gsettings com.github.unrud.VideoDownloader set com.github.unrud.VideoDownloader download-folder '/mnt/LinuxData/Download'

<br>

## Convert WEBM to MP4 using FFMPEG
> $ ffmpeg -i input_filename.WebM output_filename.MP4

<br>

## Steam (Controller Missmatch Button)
https://steamcommunity.com/discussions/forum/1/1621726179577658947/#c1697175413682520103

I found a fix today after looking for weeks. Hopefully it works for you as well.

- Disable all Controller Support Options (IE PS4 Support etc)
- Exit Steam and unplug the controller
- Open the config.vdf file stored on your computer
- By default it is located in `C:\Program Files (x86)\Steam\config\config.vdf`
- Search for "SDL_GamepadBind" and delete everything underneath it
- Save the file
- Start Steam and plug in the controller

<br>

## Publish New Repo on VSCode default to master
Change the Git Config in the system
> $ git config --global init.defaultBranch main

<br>

## Shared Linux Folder in Windows Guest (QEMU/KVM)
https://virtio-fs.gitlab.io/howto-windows.html

1. Setup the KVM 
- Enable shared memory
- Add FileSystem shared

2. Setup on the Windows Guest
- Install WinFsp
- Download virtiofs ISO (https://github.com/virtio-win/virtio-win-pkg-scripts/blob/master/README.md)
- Open ISO, and copy virtiofs folder
- Run virtio-win-guest-tools.exe
- Starting the virtiofs service (Run as Admin) so the disk will be mounted starting with Z:\   
> sc create VirtioFsSvc binpath="C:\virtiofs\virtiofs.exe" start=auto depend="WinFsp.Launcher/			VirtioFsDrv" DisplayName="Virtio FS Service" 

<br>

## N-Key Rollover Problem in Linux

https://unix.stackexchange.com/questions/675933/keyboard-input-n-key-rollover

> $ cat /proc/bus/input/devices 

That file format is explained well here => https://unix.stackexchange.com/a/74907/499581   
and here => https://unix.stackexchange.com/a/94329/508931


In the HyperX case, there are five different handlers available (can be more/less in others):

handler for keyboard events, standard mode
mouse handler (e.g. remote USB keyboard witch touchpad). I'm assuming it is always there by design
just by referring to its name "System Control", it provides events for power, sleep, etc.
referring to "Consumer Control", it provides events for multimedia and related special keys
handler for NKRO events
I can find the "standard mode" keyboard handler easily by looking for EV=120013 (I'm not sure if it is a proper way either). In that particular case I could look for other entries with an identical bitmap of supported keys, but unfortunately in Havit case that bitmap is pretty different. Another pattern that I see and might be useful is to look for sysrq in handlers, However, I don't know what it means.

And that's it.

How can I properly find both standard mode and NKRO handlers for given keyboard?

https://www.devever.net/~hl/usbnkro

<br>

## Disable Gnome Screenshot Sound when taking screenshot 
https://forum.manjaro.org/t/disable-gnome-screenshot-sound/13513/2

PATH: 
/usr/share/sounds/freedesktop/stereo

Just rename camera-shutter.oga to camera-shutter.bak (can be anything other than '.oga')

<br>

## Realme 6 cannot connect to Gnome Android Hotspot
https://askubuntu.com/questions/1424633/unable-to-connect-with-the-hotspot-created-on-ubuntu

What got things working for me was the disablement of Protected Management Frames

type in CMD  
> $ nmcli c modify Hotspot 802-11-wireless-security.pmf 1 

<br>

## Exclude a certain package when running dnf update
Use --exclude parameter
> $ dnf update --exclude=package_name*

<br>

## MikuMikuDance (MMD) Fixes
1. Install All MS fonts using Winetricks + JP fonts
2. Set ENV args before launching
> $ LANG=ja_JP.UTF-8
3. Garbled mess graphics is expected, so click 'view(V)', then 'seperate window(W)'
4. Install DXVK (2.2) to fix Garbled Mess Graphics

Issues to Fix:
- Font shifted lower than it should
<br>

## Drag and Drop doesn't works most of the times in Wayland (Open Issue)
Problem: Mutter
Link: https://gitlab.gnome.org/GNOME/mutter/-/issues/2216

Temporary Solution: 
- Keeps redraging into the area, will work about 10-20% of the time
- Switch to Xorg 

<br>

<p align="center">
<a align="center" href="#about-this-repo">🔼 Back to top 🔼</a>
</p>