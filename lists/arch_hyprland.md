# Install Wayland and Hyprland

- Install hyprland and launch hyprland
  - hyprland requires wayland and will be installed automatically
- Filebrowser: nemo
- Terminal: kitty
- Launcher: wofi
- Lockscreen: swaylock
- Screenshot tool: hyprshot
- Event manager: mako
- Automatically lock screen: hypridle
```
# As your own user
sudo pacman -Sy hyprland kitty nemo wofi swaylock brightnessctl hyprshot mako hypridle
```

My hyprland config file with comments can be fetched here:
[hyprland.conf](https://github.com/snymainn/linux_configfiles/blob/main/hyprland.conf)

Config file location: ```~/.config/hypr/hyprland.conf```

Changes that are very local for me  
- Norwegian keyboard layout :  ```kb_layout = no```
- Specific sensitivy settings for my Logitec g703 mouse

Changes that might be important for all
- Set scaling to 1 to avoid fuzzy web browser
  - ```monitor=,preferred,auto,1```
- Enable dark mode in launcher drun and filebrowser nemo
  - ```env = GTK_THEME,Adwaita:dark```


## Kitty config

My kitty config with comments can be fetched here: [kitty.config](https://github.com/snymainn/linux_configfiles/blob/main/kitty.conf)

Config file location: ```~/.config/kitty/kitty.conf```

- Run once in terminal to enable kitty as terminal in nemo

```
gsettings set org.cinnamon.desktop.default-applications.terminal exec kitty
```

Important settings (for me) in kitty.conf
- Disable bell: ```enable_audio_bell no```
- Enable cut and paste in kitty terminal without any extra install like xclip or wl-clipboard
  - NOTE: middle click does not work everwhere yet, seems to be different clipboard
  - NOTE FOR VIM: Remember to hold down shift when marking text in vim to copy

```
map ctrl+c copy_and_clear_or_interrupt
map ctrl+v paste_from_clipboard
```

## Configure hypridle

My hypridle config can be downloaded here: [hypridle.conf](https://github.com/snymainn/linux_configfiles/blob/main/hypridle.conf)

Config file location: ```~/.config/hypr/hypridle.conf```

The important setting here is how long the system shall be idle before it locks (300 seconds) and that ```swaylock``` will be called when timer hits. It is possible to stop the automatic lock with the idle-inhibitor in the waybar.

Remember that hypridle must be started in hyprland config like this: ```exec-once = hypridle```

## Configure hyprshot

Add keyboard shortcut in [hyprland.conf](https://github.com/snymainn/linux_configfiles/blob/main/hyprland.conf) and add [hyprshot.desktop](https://github.com/snymainn/linux_configfiles/blob/main/hyprshot.desktop) so wofi finds it.

Location of desktop file: ```~/.local/share/applications/hyprshot.desktop```


# Install and configure waybar

Wiki page: [https://github.com/Alexays/Waybar/wiki](https://github.com/Alexays/Waybar/wiki)

```
sudo pacman -Sy waybar
```
- Remember to add ```exec-once = waybar``` to [hyprland.conf](https://github.com/snymainn/linux_configfiles/blob/main/hyprland.conf)

My config files can be found here [config.jsonc](https://github.com/snymainn/linux_configfiles/blob/main/config.jsonc) and [style.css](https://github.com/snymainn/linux_configfiles/blob/main/style.css)

Config files must be copied (or linked here): ```~/.config/waybar/```

In addition [power_menu.xml](https://github.com/snymainn/linux_configfiles/blob/main/power_menu.xml) is neccessary to get a power button on the far right in the waybar. Same configfile location as above. 

Defaults for config files can be found in ```/etc/xdg/waybar/```.

NOTE: the below font ```otf-font-awesome``` is required to get the icons in the waybar. This is explicitly stated in the default config files, but easy to miss. 

# Fonts

```
sudo pacman -Sy noto-fonts noto-fonts-cjk noto-fonts-extra noto-fonts-emoji otf-font-awesome
```


