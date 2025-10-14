# First Arch linux installation steps

Sources used:
- [Arch wiki installation guide: https://wiki.archlinux.org/index.php/Installation_guide](https://wiki.archlinux.org/index.php/Installation_guide)
- [Arch on virtuabox: https://www.howtoforge.com/tutorial/install-arch-linux-on-virtualbox/](https://www.howtoforge.com/tutorial/install-arch-linux-on-virtualbox/)

## Create installation media

- Download iso from https://wiki.archlinux.org/index.php/Installation_guide
- Verify signature by download sig file and running :
```
# Is this correct?
gpg --keyserver-options auto-key-retrieve --verify <latest-iso>-x86_64.iso.sig <Name of iso??>
```
- Write iso to a usb key with dd or cp on linux, dd on macos, or Rufus on Windows: https://wiki.archlinux.org/title/USB_flash_installation_medium
```
# Example for cp in linux
# Do not mount usb drive, instead do an ls in /dev/disk/by-id/ and find the usb stick
# Copy to top level usb drive
# e.g.
cp <iso image> /dev/disk/by-id/usb-something-0\:0
```

## Start installation
- Boot with downloaded ISO
- Load keymap (available list here: /usr/share/kbd/keymaps/**/*.map.gz)
```
# E.g. for Norway
loadkeys no-latin1
```
- If wifi: connect to internet
```
  # Password is stored in /var/lib/iwd/<ssid name>.psk
  iwctl
  device list
  station <device,e.g wlan0> scan
  station <device> get-networks
  station <device> connect <your wifi ssid name>
```
- Enable time update over internet by enabling ntp client
```
timedatectl set-ntp true
timedatactl status
```
- Partition disks with the following partitions
   - EFI system partition if you booted in UEFI mode (check if dir /sys/firmware/efi/efivars is present)
    - EFI partition should probably be first and must a least be 1GB
  - Root partition /
  - Swap partion (more than 512MB)
  - Optional home partiton /home/
```
fdisk -l (take note of the disk you will be using here)
cfdisk /dev/sd<a|b|c> 
# Do not use the number after sd<letter> 
# Select dos for BIOS boot with MBR or efi/gpt for UEFI boot
# Make root or efi partition bootable
# Set type swap on swap partition
# Select Write og answer yes
```
- Format partitions
  - E.g using brtfs to support timeshift
```
# First list disks to get devicenames and then issue format commands
fdisk -l
mkfs.fat -F 32 /dev/sdxY # If EFI partition
mkfs.btrfs /dev/<root device>
mkswap /dev/<swap device>
mkfs.btrfs /dev/<home device>
```

## Mount filesystem and install packages

**If the installation somehow get stuck in a bad state, e.g. you are missing a package, but can connect to the internet to download it, this is where you continue after booting from the ISO image again**

- Mount the root, home and swap partitions
```
mount /dev/<root partition> /mnt
swapon /dev/<swap partition>
# If you created and home partition
mkdir /mnt/home
mount /dev/<home partition> /mnt/home
# If booted with efi
mkdir /mnt/efi
mount /dev/<efi partition> /mnt/efi
```

- Install the basic packages to root at /mnt/
```
pacstrap /mnt base base-devel linux linux-firmware dhcpcd vim man-db man-pages texinfo iwd
```
- Generate fstab with old style device names (use -U for new UUID names)
```
genfstab /mnt >> /mnt/etc/fstab
```
- Chroot into the new installation
```
arch-chroot /mnt
```
## Configure and reboot
- Set timezone
```
ln -sf /usr/share/zoneinfo/Europe/Oslo /etc/localtime
hwclock --systohc
```
- Set localization and generate locales
  - Edit /etc/locale.gen and uncomment the types you will be using in next step locale.conf
    - For this was `en_US-UTF-8 UTF-8` and `nb_NO.UTF-8 UTF-8` 
```
vim /etc/locale.gen
locale-gen
```
- Create locale.conf and set LANG, LC_MESSAGES and LANGUAGE
```
# E.g. for Norwegian formats, but English userinterface
vim /etc/locale.conf
LANG=nb_NO.UTF-8
LANGUAGE=en_US.UTF-8
LC_MESSAGES=en_US.UTF-8
```
- Make keyboard layout persistent in /etc/vconsole.conf
```
vim /etc/vconsole.conf
KEYMAP=no-latin1
```

- Create hostname file
```
vim /etc/hostname 
myhostname
```

- Create hosts file
```
vim /etc/hosts
127.0.0.1 localhost
::1 localhost
127.0.1.1 <my hostname.localdomain <my hostname>
```
- Enable dhcp and iwd
```
systemctl enable dhcpcd
systemctl enable iwd
```
- Improve network speed (Don´t know if this is necessary in 2025)
```
sudo echo "options iwlwifi 11n_disable=8" | sudo tee /etc/modprobe.d/iwlwifi11n.conf
```
- Set root passwd with 
`passwd`
- Install boot loader, old LEGACY version, see below for EFI
```
# It is important the the last config step reports that a linux image is found
pacman -S grub os-prober
grub-install /dev/<main device without number>
grub-mkconfig -o /boot/grub/grub.cfg
```

- Install boot loader, EFI version
```
# It is important the the last config step reports that a linux image is found
pacman -S grub os-prober efibootmgr
grub-install --target=x86_64-efi --efi-directory=/efi --bootloader-id=GRUB
mkdir /mnt/windows
mount /dev/<windows boot mgr partition> /mnt/windows/
#Add GRUB_DISABLE_OS_PROBER=false to /etc/default/grub
grub-mkconfig -o /boot/grub/grub.cfg
```

- Exit chroot and reboot
```
exit
reboot
```

## Add network again and user

- Add wifi network again if using wifi
```
iwctl
station <device> connect <ssid name>
```
- Add user with password
```
useradd -m <name>
passwd <name>
```

- Add user to sudoers (maybe only on protected networks)
  - Make sure vim is installed
  - Add EDITOR=vim to environment, e.g. update .bashrc with `export EDITOR=vim`
  - Edit /etc/sudoers file with `visudo` command
  - Add the following to the file:  `<user> ALL=(ALL) ALL`

## Add some tools and applications

- Web browser : vivaldi
- ~~Photo management : shotwell~~ Seems to be to slow and buggy
- Office : libreoffice-still
- Photo editing: gimp
- Office for science nerds: texlive texlive-langeuropean
- Password management: keepassxc
- Coding: vscode and git
- ssh login: openssh
- Task manager: htop
- Cool sysinfo: screenfetch
- Calculator: gnome-calculator

```
pacman -S vivaldi git htop openssh libreoffice-still keepassxc vscode screenfetch gimp gnome-calculator texlive texlive-langeuropean
```

## Configure ssh server

- Add ```AllowUsers``` to /etc/ssh/sshd_config to enable user login
- Enable sshd daemon
- Maybe disable if travelling and using open networks

```
vim /etc/ssh/sshd_config
# Add AllowUsers <username>
systemctl enable sshd
```

## Configure sound and install Spotify
- Alsa utils will install and enable service alsa-restore
- Pipewire is a server that connects to alsa to control it
  - It is a replacement for pulseaudio and works with both cosmic and hyprland desktop
- pavucontrol will popup sound control when icon in waybar pressed

```
pacman -Sy pipewire pipewire-pulse alsa-utils pavucontrol spotify-launcher
```

## Disable annoying bell in login shell

Add `set bell-style none` to `/etc/inputrc`

## Install solaar to check battery on logitech devices

```
pacman -S solaar
```

Download udev permissions from git repo:
[solaar git repo](https://github.com/pwr-Solaar/Solaar/tree/master/rules.d-uinput) and copy to `/etc/udev/rules.d/` to enable access to devices without being root (I wonder if this is completely safe)

```
sudo cp ~/Downloads/42-logitech-unify-permissions.rules /etc/udev/rules.d/
```
Then reboot computer to make udev rules take effect.

Then it is possible to issue `solaar` command. 

Update Oct, 2025: Need to add MODE="666" to the bottom of the rules file to enable config access to devices.

## Enable access for Lemokey/Keychron web config tools

My Lemokey mouse G2 worked out of the box on linux, but the web configuration tool [launcher.lemokey.com](https://launcher.lemokey.com/) was not able to access the mouse.

To enable user access to the device I copied the above 42-logitech-unify-permissions.rules files and made a new file [99-lemokey.rules](https://github.com/snymainn/linux_configfiles/blob/main/99-lemokey.rules)  that enabled access. 

To find the device idVendor and idProduct id's the command `lsusb` can be used:
```
Bus 001 Device 007: ID 362d:d028  Ultra-Link 8K
```
The first hex number is idVendor and second id idProduct. This is the wireless connection. If the mouse was connected with cable a second row would show up with another idProduct for usb. It seems this is idProduct is not necessary in the configuration file.

NOTE: Remember to unplug the cable if connecting wireless or else the web config tool will not find the correct idProduct and complain that firmware needs to be upgraded.  

NOTE2: It might be necessary to connect it with cable first after the rules are applied.

Reboot when config is copied to `/etc/udev/rules.d/`. 

Update Oct, 2025: Need to add MODE="666" to the bottom of the rules file to enable config access to devices.

## Install kuro to get Microsoft ToDo

There are several versions. This is an appimage version that bundles everything in one image and uses fuse2 to run.
I am co maintainer on this one. 

```
git clone https://aur.archlinux.org/kuro-appimage.git
cd kuro-appimage
makepkg -si
```

## Install Microsoft teams

There are several versions of teams in the AUR.
I have used this one which seems to work ok:
https://aur.archlinux.org/packages/teams-for-linux-bin

I also added a line in the hyprland config to start teams minimized when starting hyprland. `exec-once = /opt/teams-for-linux/teams-for-linux %U --minimized`

Installation:
```
git clone https://aur.archlinux.org/teams-for-linux-bin.git
cd teams-for-linux-bin
makepkg -si
```

## Security

Using simple nmap commands show that only the ssh daemon port is open, but is not accepting root logins. So it should be fairly safe. 

I also tried the vulscan script from arch repo with nmap, but that seemed to have a bug.

Thinking about it is probably smart to 
- disable the ssh daemon when outside the home network
- not have sudo rights on user
- and be careful with the passwords

```
# Scan max range of ports
nmap <ip> -p 1-65535

# Detect version of ssh protocol
nmap -sV <ip>

# Prope the ssh port for max information
sudo nmap -p 22 -sU -A <ip>
```
