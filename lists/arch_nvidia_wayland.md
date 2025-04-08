## Nvidia on old Alienware 

### Basic wayland support

There are two kernel parameters for the nvidia_drm module to be considered: modeset and fbdev. Both are enabled by default when using the nvidia-utils package. NVIDIA also plans to enable them by default in a future release.

### Build and install driver from AUR for old cards

You must find your own driver, the 470xx applies to my GT 750M.

This will build and install
- opencl-nvidia-470xx
- nvidia-470xx-dkms : The driver
- nvidia-470xx-utils
- lib32-nvidia-470xx-utils

### For new cards

Just install nvidia-open.

**NOTE: Select correct driver that shall provide the lib32-vulkan-driver**

```
sudo pacman -Sy nvidia-open
#SELECT lib32-nvidia-utils when asked!!!
```

**It is essential** to install **linux-headers** FOR OLDER CARDS or there will be no modules to load. Installing linux-headers after install of nvidia-470xx-utils will actually trigger install of modules.  

The lib32-nvidia-470xx-utils is the package that provide lib32_vulkan_driver for nvidia and is **essential for steam** to be able to connect to vulkan.

```
sudo pacman -Sy linux-headers
git clone https://aur.archlinux.org/nvidia-470xx-utils.git
cd nvidia-470xx-utils
makepkg -si
git clone https://aur.archlinux.org/lib32-nvidia-470xx-utils.git
cd lib32-nvidia-470xx-utils
makepkg -si
```

**It is NOT** necessary to:
- modify mkinitcpio to load modules early or remove kms(nouvaeu driver)
- set drm kernel parameters in grub or systemd
- add pacman hook

**And this applies to both new and old cards!!!**

### Monitoring and performance checkingtool

 - nvtop : to monitor use of GPU
 - vulkaninfo : to check if vulkan can see the nvidia card
 - glmark2 : Will report back the GPU (which should be internal) and demonstrate that it is not running on nvidia GPU
```
sudo pacman -Sy nvtop vulkan-tools glmark2
```

### Check that vulkan is using nvidia GPU

```
[name@alienware ~]$ vulkaninfo | grep driver
	VK_LUNARG_direct_driver_loading        : extension revision 1
	driverVersion     = 471.0.2.0 (1975517312)
	driverUUID                        = b67e126b-ca9f-5837-843e-603a6ad11b15
	driverID                                             = DRIVER_ID_NVIDIA_PROPRIETARY
	driverName                                           = NVIDIA
	driverInfo                                           = 470.256.02
	VK_KHR_driver_properties                  : extension revision 1
```


### Check that hyprland and its application is using internal GPU

```
[name@alienware ~]$ glmark2
=======================================================
    glmark2 2023.01
=======================================================
    OpenGL Information
    GL_VENDOR:      Intel
    GL_RENDERER:    Mesa Intel(R) HD Graphics 4600 (HSW GT2)
    GL_VERSION:     4.6 (Compatibility Profile) Mesa 25.0.2-arch1.2
    Surface Config: buf=32 r=8 g=8 b=8 a=8 depth=24 stencil=0 samples=0
    Surface Size:   800x600 windowed
```

### Install steam

#### Enable multilib
```
sudo vim /etc/pacman.conf
#UNCOMMENT THESE
[multilib]
Include = /etc/pacman.d/mirrorlist
sudo pacman -Sy steam
```

### Play games

- For native linux games add `-vulkan` in Launch options for game.
- For windows games
  - Add `-vulkan` in Launch options for game
  - Select Proton under Compatibilty

Even though you have no integrated graphics card, the fps will be around 30 without the `vulkan` option, and much better with the `vulkan` option. 

Thats it. In summary clone two packages from AUR, build and install them togheter with linux-headers. A range of other packages will also be installed because `-s` to makepkg will resolve dependencies. 

In my experience the packages themselves mutes the nouvaeu driver and no other config is necessary.