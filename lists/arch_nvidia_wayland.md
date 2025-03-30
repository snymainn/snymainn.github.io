## Nvidia on old Alienware 

### Basic wayland support

There are two kernel parameters for the nvidia_drm module to be considered: modeset and fbdev. Both are enabled by default when using the nvidia-utils package. NVIDIA also plans to enable them by default in a future release.

### Build and install driver from AUR

This will build and install
- opencl-nvidia-470xx
- nvidia-470xx-dkms : The driver
- nvidia-470xx-utils


sudo pacman -S base-devel linux-headers git nano --needed

```
git clone https://aur.archlinux.org/nvidia-470xx-utils.git
cd nvidia-470xx-utils
makepkg -si
```

**It is not** necessary to:
- modify mkinitcpio to load modules early
- set drm kernel parameters in grub or systemd
- add pacman hook

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
sudo pacman -Sy
```

