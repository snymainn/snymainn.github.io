# Install driver for Canon printer

- Install printer driver (I wonder if this is really necessary when using driverless, but at least it installs some necessary dependencies)
- Install scan program
- Install nss-mdns (maybe avahi also if not installed by dependencies above)
- Insert `mdns_minimal [NOTFOUND=return]` in /etc/nsswitch.conf as indicated here [https://wiki.archlinux.org/title/Avahi](https://wiki.archlinux.org/title/Avahi)
- Restart avahi-daemon (not sure if this is necessary)
- Add printer using driverless from [http://localhost:631](http://localhost:631)

```
git clone https://aur.archlinux.org/cnijfilter2.git
cd cnijfilter2
makepkg -si
cd ..

git clone https://aur.archlinux.org/scangearmp2.git
cd scangearmp2
makepkg -si

sudo pacman -Sy nss-mdns

sudo vim /etc/nsswitch.conf
# Add mdns_minimal [NOTFOUND=return] in /etc/nsswitch.conf

sudo systemctl restart avahi-daemon
```

Then add printer using cups web interface at [http://localhost:631](http://localhost:631)

