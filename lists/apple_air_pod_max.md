# Use Apples Air Pod Max headphones

Check the reference at https://wiki.archlinux.org/title/Bluetooth


- Install bluez and bluez-utils
- (bluetooth btusb driver is probably loaded if you have bluetooth on your computer)
- Enable bluetooth service 
- Pair and connect to headphones after holding down the square button on the Max headphones so the white light under them are pulsing
- pavucontrol in waybar even show the sound level when you turn the volume knob on the headphones, fantastic :)

```
sudo pacman -S bluez bluez-utils
sudo systemctl enable bluetooth
sudo systemctl start bluetooth

#As your own user
bluetoothctl
# scan on
# pair <mac address>
# exit

bluetoothctl -- connect <mac address>
```

To make connection easy an alias can be added to e.g. bashrc
'''
alias connect_max_headephones='bluetoothctl -- connect <mac address>
'''

