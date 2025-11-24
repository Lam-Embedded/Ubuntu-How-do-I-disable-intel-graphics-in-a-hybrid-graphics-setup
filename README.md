## Check which GPU your PC is using
```bash
lspci | grep -E "VGA|3D"
```

## SOLUTION 1

Updade bunblebee
```bash
sudo bumblebee-uninstall
sudo apt-get install ppa-purge
sudo ppa-purge ppa:ubuntu-x-swat/x-updates
sudo ppa-purge ppa:xorg-edgers/ppa
sudo apt-add-repository ppa:mj-casalogic/bumblebee
sudo apt-get update
sudo apt-get install bumblebee
```
```bash
glxgears
```

## SOLUTION 2: Turn on power saving mode
```bash
echo "options i915 enable_guc=3" | sudo tee /etc/modprobe.d/i915-power.conf
```
Then
```bash
sudo update-initramfs -u
sudo reboot
```

## SOLUTION 3: Block drivers in Linux
Check if i915 driver is running:
```bash
lsmod | grep -E "i915|xe"
```
Check if module exists in system:
```bash
modinfo i915 | head
```
Create blacklist file:
```bash
sudo nano /etc/modprobe.d/blacklist-intel-gpu.conf
```
More content:
```bash
blacklist i915
blacklist xe
```
Update initramfs (Ubuntu/Debian):
```bash
sudo update-initramfs -u
```
Update GRUB to make sure the module doesn't load
```bash
sudo nano /etc/default/grub
```
Find the line:
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
```
Edit to (add modprobe.blacklist=i915,xe):
```bash
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash modprobe.blacklist=i915,xe"
```
save and exit file

```bash
sudo reboot
```

check
```bash
lsmod | grep -E "i915|xe"
```


## SOLUTION 4: Reduce GPU power but still use graphics
```bash
# Ví dụ bật chế độ tiết kiệm
echo "battery" | sudo tee /sys/class/drm/card0/device/power_dpm_state
```

## SOLUTION 5: Reduce maximum GPU clock speed (underclock)
```bash
sudo sh -c "echo 300 > /sys/class/drm/card0/gt_max_freq_mhz"
```
To reset to default:
```bash
sudo sh -c "echo 0 > /sys/class/drm/card0/gt_max_freq_mhz"
```
## SOLUTION 6: Disable GPU acceleration for desktop
```bash
sudo apt install gnome-shell-extension-disable-unredirect
```
```bash
sudo nano /etc/X11/xorg.conf.d/20-intel.conf
```
ADD CONTENT: 
```bash
Section "Device"
    Identifier "Intel Graphics"
    Driver "intel"
    Option "DRI" "False"
EndSection
```
```bash

```
