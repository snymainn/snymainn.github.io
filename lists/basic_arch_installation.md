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
  - Root partition /
  - EFI system partition if you booted in UEFI mode (check if dir /sys/firmware/efi/efivars is present)
    - EFI partition should probably be first and must a least be 260MB
  - Swap partion (more than 512MB)
  - Optional home partiton /home/
```
fdisk -l (take note of the disk you will be using here)
cfdisk /dev/sd<a|b|c> 
# Do not use the number after sd<letter> 
# Select dos for BIOS boot with MBR or gpt for UEFI boot
# Make root partition bootable
# Set type swap on swap partition
# Select Write og answer yes
```
- Format partitions
  - E.g using brtfs to support timeshift
```
# First list disks to get devicenames and then issue format commands
fdisk -l
mkfs.btrfs /dev/<root device>
mkswap /dev/<swap device>
mkfs.btrfs /dev/<home device>
```

## Mount filesystem and install packages

=If the installation somehow get stuck in a bad state, e.g. you are missing a package, but can connect to the internet to download it, this is where you continue after booting from the ISO image again

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
pacstrap /mnt base based-devel linux linux-firmware dhcpcd vim man-db man-pages texinfo iwd
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
    - For this was `en_US-UTF-8 UTF-8` and `nb_NO ISO-8859-1` 
```
vim /etc/locale.gen
locale-gen
```
- Create locale.conf and set LANG, LC_MESSAGES and LANGUAGE
```
# E.g. for Norwegian formats, but English userinterface
vim /etc/locale.conf
LANG=nb_NO.ISO-8859-1
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
<my hostname>
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
- Install boot loader
```
# It is important the the last config step reports that a linux image is found
pacman -S grub os-prober
grub-install /dev/sda
grub-mkconfig –o /boot/grub/grub.cfg
```

- Exit chroot and reboot
```
exit
reboot
```

