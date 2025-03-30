# Arch Linux Configuration Guide

Welcome to the Arch Linux Configuration Guide! This comprehensive guide is divided into multiple sections to help you set up and optimize your Arch Linux environment.

# Personal Apps & Bashrc Configuration

### Switch to Other Shells

```
# Fish (Friendly Interactive Shell)
sudo pacman -S --needed fish ttf-jetbrains-mono-nerd
sudo chsh -s /usr/bin/fish swapnanil
reboot
```

# Fundamentals

### Connect to Internet

If you are utilizing a desktop environment, utilize its built-in Wi-Fi capabilities, or connect via Ethernet. Otherwise, install one of the following applications:

```bash
sudo pacman -S --needed nmcli     # For NetworkManager CLI
or
sudo pacman -S --needed iwctl     # For interacting with iwd (iNet Wireless Daemon)
```

### Faster Internet using DNS
Using Faster DNS and enable caching with increased size:

```sudo vim /etc/systemd/resolved.conf ```
```bash
[Resolve]
DNS=9.9.9.9
FallbackDNS=149.112.112.112
DNSOverTLS=yes
DNSStubListener=yes
DNSSEC=no
ReadEtcHosts=yes
MulticastDNS=no
LLMNR=no
Cache=yes
CacheSize=10000
```
If you prefer security over faster internet change DNSSEC to yes.

Flush DNS cache using this command:

```sudo resolvectl flush-caches```

### Automatic Package Cache Cleanup

Implement a Pacman hook to automatically clear the package cache when necessary.

```
sudo pacman -S --needed pacman-contrib
sudo mkdir /etc/pacman.d/hooks
sudo vim /etc/pacman.d/hooks/clean_package_cache.hook
```

Paste the following inside clean_package_cache.hook

```
[Trigger]
Operation = Upgrade
Operation = Install
Operation = Remove
Type = Package
Target = *
[Action]
Description = Cleaning pacman cache...
When = PostTransaction
Exec = /usr/bin/paccache -rk 2
```

save & exit

### Optimize Arch Linux Mirrors

Improve the performance of your Arch Linux system by optimizing mirror configurations. Follow these steps to achieve faster downloads:

```
sudo pacman -S --needed reflector
sudo reflector --verbose --latest 8 --download-timeout 16 --country 'India,Bangladesh,China' --sort rate --protocol https,http --save /etc/pacman.d/mirrorlist
```

### Essential System Packages

Install essential packages for a well-rounded Arch Linux system:

# Core System Components

```
sudo pacman -S --needed dkms ufw timeshift
```

# General Utilities and Tools

```
sudo pacman -S --needed jshon expac git wget acpid avahi net-tools xdg-user-dirs cronie \
vim openssh htop wget iwd smartmontools xdg-utils
```

# Enable System Services

```
systemctl enable cronie.service
systemctl enable ufw.service
systemctl enable acpid avahi-daemon systemd-timesyncd
systemctl enable fstrim.timer # This is optional if discard=async in enabled in fstab
```

# Set Firewall Defaults

```
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
```

### Networking Setup

Configure networking on your Arch Linux system with the following commands:

```
# Install NetworkManager and related tools
sudo pacman -S --needed bind networkmanager networkmanager-openvpn networkmanager-pptp networkmanager-vpnc

# Enable NetworkManager service
sudo systemctl enable NetworkManager
```

```
Check Secure DNS status
dig +short txt qnamemintest.internet.nl
```

### Archive and File System Utilities

Install essential archive and file system utilities with the following commands:

```
# Compression Utilities
sudo pacman -S --needed p7zip unrar unzip unace xz rsync zip unarj lrzip lha cpio arj

# File System Utilities
sudo pacman -S --needed nfs-utils cifs-utils ntfs-3g exfat-utils gvfs udisks2
```

### Multimedia Codecs

Enhance multimedia codec support by installing the required packages:

```
sudo pacman -S gst-libav gst-plugins-base gst-plugins-good gst-plugins-bad gst-plugins-ugly gstreamer-vaapi x265 x264 lame --needed
```

### Sound System Setup

#### New! improve pipewire performance. This mitigates a lot of the common issues such as audio glitching or cutting out in certain scenarios.

```
sudo pacman -S realtime-privileges
sudo gpasswd -a $USER realtime
```
#### Configure the sound system on your Arch Linux system with the following commands:

```
# Sound System Components
sudo pacman -S --needed alsa-utils pipewire pipewire-pulse pipewire-jack wireplumber

# Sound Control for Qt-based Desktops (e.g., KDE Plasma or LXQt)
sudo pacman -S --needed pavucontrol-qt

# Sound Control for GTK-based Desktops (other desktop environments)
sudo pacman -S --needed pavucontrol
```

### Manage Audio Output Streams with Helvum (optional)

If you have multiple headphones/speakers and need to manage audio output streams, install Helvum. Helvum is a graphical patchbay for PipeWire, allowing you to create and remove connections between applications and/or devices to reroute audio, video, and MIDI data where needed.

```
sudo pacman -S --needed helvum
```

### Fonts Installation for Compatibility

Install essential fonts, especially required for Electron apps or Steam Proton gaming, and additional Nerd Fonts for a richer experience:

# Common System Fonts

```
sudo pacman -S --needed ttf-dejavu ttf-liberation noto-fonts ttf-caladea \
ttf-carlito ttf-opensans otf-overpass ttf-roboto tex-gyre-fonts ttf-ubuntu-font-family \
ttf-linux-libertine freetype2 terminus-font ttf-bitstream-vera ttf-droid ttf-fira-mono \
ttf-fira-sans ttf-freefont ttf-inconsolata ttf-liberation libertinus-font \
adobe-source-sans-fonts adobe-source-serif-fonts adobe-source-sans-pro-fonts \
cantarell-fonts opendesktop-fonts
```

### Input Device Drivers

Install the necessary packages to ensure proper functionality of input devices. Even if not immediately required, these packages do not cause harm by being installed.

# Common Input Drivers (Xorg)

```
sudo pacman -S --needed xf86-input-synaptics xf86-input-libinput xf86-input-evdev
```

# Additional Driver for Virtual Machines

```
sudo pacman -S --needed xf86-input-vmmouse   # Required when installing inside a virtual machine
```

### Wi-Fi Connection Setup (Especially for Laptops)

Enable Wi-Fi connectivity, particularly for laptops, by installing the necessary tools:

```
sudo pacman -S --needed wireless_tools wpa_supplicant ifplugd dialog
```

### Bluetooth Support

Adds Bluetooth Support (Especially for Laptops)

# Install Bluetooth Packages and Enable Bluetooth Service

```
sudo pacman -S --needed bluez bluez-utils
systemctl enable bluetooth
```

## Install paru (AUR Helper)

```
mkdir repos
cd repos
git clone https://aur.archlinux.org/paru-bin.git
cd paru-bin
makepkg -sic
```

```
Tips :) 1.Add 'SkipReview' to /etc/paru.conf to skip review of pkgbuild of every app you install, just like yay
     :) 2.Uncomment/Add 'CleanAfter' to Remove untracked files after installation.

```

### Code and IDE

#### Code-OSS with Microsoft's proprietary marketplace

```
paru -S code code-features code-marketplace
```

### Setup Terminal, Prompt, Neovim, and LazyVim

```
sudo pacman -S --needed alacritty eza neovim fd ripgrep python-pynvim nodejs npm
```

#### Make a backup of your current Neovim files:

```
mv ~/.config/nvim{,.bak}
mv ~/.local/share/nvim{,.bak}
mv ~/.local/state/nvim{,.bak}
mv ~/.cache/nvim{,.bak}
```

#### Clone the starter, remove .git, and run Neovim:

```
git clone https://github.com/LazyVim/starter ~/.config/nvim
rm -rf ~/.config/nvim/.git
nvim
```

#### Set Alacritty theme (remember to grab the dotfiles)

```
mkdir -p ~/.config/alacritty/themes
git clone https://github.com/alacritty/alacritty-theme ~/.config/alacritty/themes
```

### Organize your personal applications and set up a simplified bash environment with these commands:

## Browser

```
paru -S --needed firefox brave-bin torbrowser-launcher
```

# Other Apps

```
paru -S --needed vesktop telegram-desktop keepassxc mpv qbittorrent bleachbit onlyoffice-bin
```

### Optional: Remap Keys on keyboard

#### Methord 1 (CLI): Keyd

```
sudo pacman -S keyd
sudo systemctl enable keyd
```

Edit Keyd config file:

```
sudo vim /etc/keyd/default.conf
```

Add these (ids section -> Specify keyboard | main section > remap keys):

```
[ids]

*

[main]
f9=noop
leftmeta=leftalt
leftalt=leftmeta

```

#### Important tips for keyd:-

```
1. To test what keyboard and what you press on (useful for setting default.conf)
 sudo keyd monitor
2. Reload your default.conf without reboot
 sudo keyd reload
3. View logs to fix error using this command
 sudo journalctl -eu keyd -f
4. noop disables a key
```

#### Methord 2 (GUI): input-remapper

```
paru -S --needed input-remapper-git
sudo systemctl restart input-remapper
sudo systemctl enable input-remapper
```

### Config .zshrc/.bashrc/config.fish

```
alias ls='eza -al --color=always --group-directories-first'
alias pi='sudo pacman -S'
alias pr='sudo pacman -R'
alias prr='sudo pacman -Rns'
alias vim='nvim'
```

### Format And Granting Read/Write Permissions to Linux Hard Drives

To grant read and write permissions to a Linux hard drive, use the chmod command in the terminal.

```
sudo pacman -S e2fsprogs
lsblk
sudo mkfs -t ext4 /dev/sda1
sudo e2label /dev/sda1 SetaDriveLabel
```

```
sudo chown -v your_username:your_username /media/your_external_drive
```

### Improve Read/Write Speeds on Linux Hard Drives

Boost Linux Hard Drive Speeds: Add "notime" & "discard=async" in fstab
Enhance read/write speeds by editing /etc/fstab:

```
/dev/nvme0n1    /mnt/abcdefgh    btrfs   rw,noatime,ssd,discard=async,compress=zstd:3,space_cache=v2 0 0
/dev/nvme0n1    /mnt/zxcvedfd    btrfs   rw,noatime,nodatasum,nodatacow,ssd,discard=async,space_cache=v2,subvolid=256,subvol=/@
UUID=xxxxxxxx	/mnt/SeagateRead	ext4	defaults,noatime,user,auto,exec,data=writeback,barrier=0,nobh,errors=remount-ro		0	0
```

Add `x-gvfs-show` to let gnome's nautilus detect as mounted

# Multimedia & Utilities - KDE, Gnome, Cinnamon, Terminal Setup, and More

Optimize your multimedia and utility experience by installing various desktop environments, applications, and configuring essential tools:

### KDE Apps and Themes

To install KDE Plasma 6

```
sudo pacman -S --needed plasma-meta konsole kwrite dolphin ark egl-wayland sddm
sudo systemctl enable sddm.service
```

Post install apps and themes

```
sudo pacman -S --needed packagekit-qt6 okular kate gwenview spectacle kalk kwalletmanager kdeconnect sshfs plasma-systemmonitor kfind kolourpaint kdevelop kamoso krita kdenlive mediainfo

sudo pacman -S --needed materia-kde materia-gtk-theme capitaine-cursors fcitx5-breeze kvantum-theme-materia
```

### Gnome Apps, Extensions, and Themes

```
sudo pacman -S --needed gnome-text-editor gnome-tweaks gnome-calculator gnome-calendar gnome-photos gnome-sound-recorder gnome-weather gnome-system-monitor gparted gnome-disk-utility
sudo pacman -S --needed gnome-shell-extension-appindicator gnome-shell-extension-arc-menu gnome-shell-extension-caffeine gnome-shell-extension-dash-to-panel  gnome-shell-extensions
sudo pacman -S --needed arc-gtk-theme arc-solid-gtk-theme arc-icon-theme papirus-icon-theme
```

### Cinnamon Desktop Apps & Themes

```
paru -S --needed xed xreader galculator gnome-screenshot gparted webkit2gtk gnome-terminal file-roller celluloid drawing gufw warpinator mint-themes mint-y-icons
paru -S --needed nemo-audio-tab  nemo-emblems  nemo-fileroller nemo-image-converter nemo-pastebin nemo-preview  nemo-python  nemo-seahorse nemo-share gvfs-mtp
paru -S --needed pix xviewer webapp-manager mint-backgrounds
```

### Android Debug Bridge (ADB) & Firmware Management

To enable the Android Debug Bridge for your user, use the following commands:

```
# Install ADB and Udev rules for Android
sudo pacman -S --needed android-tools android-udev

# Add your user to the adbusers group
sudo usermod -aG adbusers swapnanil
```

#### Common ADB commands:

```
# Check connected devices
adb devices

# Reboot into fastboot mode
adb reboot fastboot

# Reboot into recovery mode
adb reboot recovery

# Sideload a package
adb sideload <filename>

```

If you intend to flash firmware or custom images to your Samsung device, follow these steps:

```
# Install Heimdall for Samsung devices
sudo pacman -S --needed heimdall
Common Heimdall commands:

# Print partition information
heimdall print-pit

# Flash recovery image without rebooting
heimdall flash --RECOVERY recovery.img --no-reboot

```

# Linux Gaming Setup (Wine & Kernel)

### Install Steam

```
sudo pacman -S --needed steam
```

### AMD Graphics Driver

Install the necessary graphics driver components for AMD GPUs:

```
sudo pacman -S --needed mesa lib32-mesa libva-mesa-driver lib32-libva-mesa-driver mesa-vdpau lib32-mesa-vdpau \
vulkan-icd-loader lib32-vulkan-icd-loader vulkan-radeon lib32-vulkan-radeon \
libva-vdpau-driver lib32-libva-vdpau-driver opencl-clover-mesa lib32-opencl-clover-mesa \
opencl-rusticl-mesa lib32-opencl-rusticl-mesa rocm-opencl-runtime
```

Optional (For X11)

```
sudo pacman -S --needed xorg-server xorg-xinit xf86-video-amdgpu xf86-video-ati
```

### Install Wine & Wine Dependencies | Escape Wine's infamous dependency hell

Recommended Install

```
sudo pacman -S --needed alsa-lib alsa-plugins cabextract cups dosbox gamemode giflib glfw gnutls gst-plugins-base-libs gtk3 innoextract lib32-alsa-lib lib32-alsa-plugins lib32-fontconfig lib32-gamemode lib32-giflib lib32-gnutls lib32-gst-plugins-good lib32-gst-plugins-base lib32-gst-plugins-base-libs lib32-gtk3 lib32-libgcrypt lib32-libgpg-error lib32-libjpeg-turbo lib32-libldap lib32-libpng lib32-libpulse lib32-libva lib32-libxcomposite lib32-libxinerama lib32-libxslt lib32-mpg123 lib32-ncurses lib32-ocl-icd lib32-openal lib32-opencl-icd-loader lib32-sqlite lib32-v4l-utils lib32-vkd3d lib32-vulkan-icd-loader libgcrypt libgpg-error libjpeg-turbo libldap libpng libpulse libva libxcomposite libxinerama libxslt mpg123 ncurses ocl-icd openal opencl-icd-loader samba sqlite ttf-liberation unzip v4l-utils vkd3d vulkan-icd-loader wine wine-gecko wine-mono winetricks wqy-zenhei sane
```

Wine Extras (More Bloat & Compatibility)

```
sudo pacman -S --needed lib32-libnm cups-filters libexif libmikmod libpaper liburing qpdf sdl12-compat sdl_net sdl_sound lib32-libxss lib32-nspr lib32-nss lsb-release lsof usbutils xorg-xrandr zenity python-evdev lib32-libxxf86vm lib32-libxml2 lib32-openssl nss-mdns lib32-gstreamer gstreamer gsm lib32-glu lib32-libsm lib32-libice gst-plugins-good schedtool ccache lib32-sdl2
```

sane
Wine Extreme (Most Bloat & Compatibility)

```
sudo pacman -S --needed dbus-glib glew glm gtk2 jsoncpp lib32-dbus-glib lib32-freeglut lib32-glew lib32-gtk2 lib32-libappindicator-gtk2 lib32-libcurl-compat lib32-libcurl-gnutls lib32-libdbusmenu-glib lib32-libdbusmenu-gtk2 lib32-libgcrypt15 lib32-libidn11 lib32-libindicator-gtk2 lib32-libjpeg6-turbo lib32-libmikmod lib32-libmodplug lib32-libpipewire lib32-libpng12 lib32-librtmp0 lib32-libtiff4 lib32-libudev0-shim lib32-libusb lib32-libvpx1.3 lib32-libwebp lib32-pipewire lib32-sdl12-compat lib32-sdl2_image lib32-sdl2_mixer lib32-sdl2_ttf lib32-sdl_image lib32-sdl_mixer lib32-sdl_ttf libcurl-compat libcurl-gnutls libdbusmenu-gtk2 libgcrypt15 libidn11 libindicator-gtk2 libjpeg6-turbo libliftoff libpng12 librtmp0 libtiff4 libudev0-shim libvpx1.3 openssl openvr opusfile sdl2_image sdl2_mixer sdl2_ttf sdl_image sdl_mixer sdl_ttf seatd wlroots xcb-util-errors unixodbc libgphoto2 glusterfs
```

### Install Lutris & Lutris Dependencies

Recommended

```
sudo pacman -S --needed lutris vulkan-tools
```

Optional Extra Lutris Dependency

```
sudo pacman -S --needed python-gobject python-requests python-pillow python-yaml python-setproctitle python-distro python-evdev psmisc p7zip curl soundfont-fluid libgirepository gobject-introspection gobject-introspection-runtime
```

### Install FPS Overlay & gamescope

```
sudo pacman -S --needed mangohud lib32-mangohud goverlay gamescope lib32-vulkan-mesa-layers lib32-vulkan-mesa-layers vulkan-tools
```

### Epic Games

```
paru -S heroic-games-launcher-bin
```

### Common Game Dependencies (Compilation takes time)

```
paru -S --needed protonup-qt ttf-ms-win11-auto lib32-gsm
```

### Use Custom Kernel & Wine

"Installing a faster kernel could get you more if not same fps as a debloated windows installation"
[Linux-Tkg](https://github.com/Frogging-Family/linux-tkg) is always recommended
Watch [A1RM4X](https://youtu.be/QIEyv-Pnh0w?si=xBIZg6HLMzp9aP2X) video to install & setup

[wine-tkg-git](https://github.com/Frogging-Family/wine-tkg-git/actions/workflows/wine-arch.yml) is a custom wine-staging build recommended to install if you want absolute wine gaming performance. you must be logged in to github to access prebuilt.

### Davinci Resolve (AMD) PreVega GPU Prerequisites

```
sudo pacman -S --needed libxcrypt-compat rocm-core libarchive xdg-user-dirs patchelf rocm-opencl-runtime
vim /etc/environment
add the line
ROC_ENABLE_PRE_VEGA=1

save and exit
```

# Setup Timeshift and Take a Clean Backup

1. After completing all above steps, Open Timeshift from the application menu.
2. If this is your first time using Timeshift, click "Set up" to start the configuration process.
3. Choose the backup plugin you want to use. You have two options: Rsync or Btrfs. Both are excellent choices, but you might want to choose Rsync if you're backing up an external hard drive.
4. Specify a location for your backups. You can either enter the path manually or click "Browse" to navigate to it using a file explorer.
5. Add any directories that you want Timeshift to exclude from your backup by clicking "Add directory."
6. Click "OK" to save your changes and close the configuration window.
7. Create a new backup by clicking "Create Backup" or exit the app if you're not ready to back up just yet. You can also create a new backup later using the command line interface (CLI).

   - `timeshift --create`: This command creates a new backup of your system.
   - `timeshift --list`: This command lists all available backups.
   - `timeshift-cli backup delete <timestamp>`: This command deletes a specific backup by its timestamp.
   - `timeshift --delete --snapshot '[snapshot]'`: This command restores your system from a specific backup directory.
     Try `sudo -E timeshift-gtk` if timeshift refuse to open in window managers

#### Example

````
timeshift --list
timeshift --list --snapshot-device /dev/sda1
timeshift --create --comments "after update" --tags D
timeshift --restore
timeshift --restore --snapshot '2014-10-12_16-29-08' --target /dev/sda1
timeshift --delete --snapshot '2014-10-12_16-29-08'
timeshift --delete-all```
````

## Overclocking & Optimize Game Performance

amdgpu-clock is the minimal way to overclock amd gpu in linux.

### Installation

```
git clone https://github.com/sibradzic/amdgpu-clocks
cd amdgpu-clocks
cp amdgpu-clocks /usr/local/bin/amdgpu-clocks
cp amdgpu-clocks.service /etc/systemd/system/
sudo systemctl enable amdgpu-clocks
```

### Enable autostart and advanced overclocking profiles
Set the amdgpu-custom-state.pci:0000:xx:xx.x as your pci number. My card is 26:00.0

```sudo vim /etc/default/amdgpu-custom-state.pci:0000:26:00.0```

Find your GPU's PCI ``` <domain>:<bus>:<dev>.<function>``` numbers using lspci.

In my case my RX 570 is ```26:00.0 VGA compatible controller: Advanced Micro Devices, Inc. [AMD/ATI] Ellesmere [Radeon RX 470/480/570/570X/580/580X/590] (rev ef)```

```
# Set custom GPU states 6 & 7:
OD_SCLK:
6:       1300MHz        1050mV
7:       1400MHz        1090mV
# Set custom memory states 1 & 2:
OD_MCLK:
1:       1750MHz        750mV
2:       1900MHz        900mV
# Only allow SCLK states 5, 6 & 7:
FORCE_SCLK: 5 6 7
# Force fixed memory state:
FORCE_MCLK: 2
# Force power limit (in micro watts):
FORCE_POWER_CAP: 187000000
# In order to allow FORCE_SCLK & FORCE_MCLK:
FORCE_PERF_LEVEL: manual
```

### Finally Add these update your kernel parameters by adding theses to grub config or systemd boot/loader config

`zswap.enabled=0`

`nowatchdog`

`mitigations=off`

`amdgpu.ppfeaturemask=0xffffffff`

`amdgpu.dcdebugmask=0x10` Optional

#### Regenerate initramfs
`sudo dracut --regenerate-all --force` if using dracut

`sudo mkinitcpio -P` if using mkinitcpio

After reboot check `systemctl status amdgpu-clocks.service` it should be enabled with manual voltage and frequency tunning.

## Optimize Game Performance

sudo vim /etc/sysctl.d/80-gamecompatibility.conf

```
vm.max_map_count = 16777216
```

sudo vim /etc/sysctl.d/99-splitlock.conf

```
kernel.split_lock_mitigate=0
```

## Enable active noice suppression in headset mic

```
sudo pacman -S noise-suppression-for-voice
```

```mkdir -p ~/.config/pipewire/pipewire.conf.d/```

```vim ~/.config/pipewire/pipewire.conf.d/99-input-denoising.conf```

```
context.modules = [
{   name = libpipewire-module-filter-chain
    args = {
        node.description =  "Noise Canceling source"
        media.name =  "Noise Canceling source"
        filter.graph = {
            nodes = [
                {
                    type = ladspa
                    name = rnnoise
                    plugin = /usr/lib/ladspa/librnnoise_ladspa.so
                    label = noise_suppressor_mono
                    control = {
                        "VAD Threshold (%)" = 50.0
                        "VAD Grace Period (ms)" = 200
                        "Retroactive VAD Grace (ms)" = 0
                    }
                }
            ]
        }
        capture.props = {
            node.name =  "capture.rnnoise_source"
            node.passive = true
            audio.rate = 48000
        }
        playback.props = {
            node.name =  "rnnoise_source"
            media.class = Audio/Source
            audio.rate = 48000
        }
    }
}
]
```

### UniClip: Syncing Clipboards Across Multiple Linux PCs

[UniClip](https://github.com/quackduck/uniclip) is a powerful tool that allows seamless synchronization of clipboards between Linux PCs, laptops, or Android devices.

#### Installation Instructions:

1. **Download the Binary:**

   - Navigate to the releases page on GitHub and download the appropriate binary for your architecture.

2. **Extract and Install:**
   - Extract the downloaded archive and navigate into the extracted directory (e.g., `uniclip_xxx_linux_x86_64`).
   - Install UniClip by copying the `uniclip` binary to `/usr/bin`:
     ```
     sudo cp uniclip /usr/bin
     ```

#### Configuring Firewall (e.g., ufw):

If you're using a firewall like `ufw`, follow these steps to enable connections between devices to share clipboard:

1. **Identify Device IPs:**
   - Obtain the IP addresses of all devices you want to sync clipboards between.
2. **Running UniClip and things to keep in mind:**

   - Start UniClip on the host device (say your desktop). It will display the host's IP address and a random port number between 30000 and 60000.
   - Using a static port is not yet fully supported in uniclip . If support is added the firewall command can be made more specific to allow a unique port .
   - This is important as even though there is very less chance that of syncing between a compromized device can be used to gain access connected devices (I am not sure about this need more research)

3. **Allow Incoming Connections:**

   - On your recieving device(s) (say your laptop#1) Before connecting to host, allow connections to the host device using `ufw`. Replace `192.168.xxx.xxx` with the IP address of the host:
     ```
     sudo ufw allow from 192.168.xxx.xxx proto tcp to any port 30000:60000
     ```
   - This cmd needs to be also run on the host device to allow data transfer form recieving device (i.e same command with your laptop's ip on host aka desktop )

4. **Connecting Devices:**
   - To sync between two or more devices, obtain the IP addresses of all and run the above `ufw` command on each device, substituting the IP of the opposite devices.

Using these steps, you can easily synchronize clipboards across multiple Linux PCs using UniClip and configure the firewall with ufw to enable secure connections.

## Optional Personal Firmware for my pc specs

```
paru -S linux-firmware-qlogic aic94xx-firmware wd719x-firmware upd72020x-fw
```

## Using ananicy-cpp instead of gamemode
### Install CachyOS'S Repos Before Running

```paru -S ananicy-cpp cachyos-ananicy-rules power-profiles-daemon cpupower upower cachyos-settings```

#### Notes : 
Ananicy (ANother Auto NICe daemon) — is a shell daemon created to manage processes' IO and CPU priorities, with community-driven set of rules for popular applications. 
cachyos-ananicy-rules provides rules of popular games and apps that ananicy-cpp can interface with. 
#### trigger the gamemode
```game-performance LD_PRELOAD="" %command%``` cachyos-settings contains "game-performance" script that is used as a launch option just like gamemoderun to start ananicy

Ananicy-cpp + Gamemode is NOT recommended as they act as similar programs can will cause system instabiliity

#### set tweaks for cpu and pci latency
```sudo powerprofilesctl set performance``` sets cpu to performance but requires "amd core boost" set as auto/enabled.

```sudo cpupower frequency-set -g performance``` sets cpu to performace 

```sudo systemctl enable --now pci-latency.service``` improves pci performance

#### check amd-pstate status and cpu governer | Inside the Mobo BIOS Remember to enable cppc and core boost and disable c-states. 
```cat /sys/devices/system/cpu/amd_pstate/status``` Should say active

```cat /sys/devices/system/cpu/amd_pstate/prefcore``` Should say enabled

```cat /sys/devices/system/cpu/cpu*/cpufreq/energy_performance_preference``` should say `performance` while gaming and `balance_performance` in general desktop use
