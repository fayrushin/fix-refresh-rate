1. Generate edid.bin by nvidia settings gui
2. Copy it to /usr/lib/firmware/edid/edid.bin
3. Add it to boot
```
sudo kernelstub -a "drm.edid_firmware=eDP:edid/edid.bin"
sudo kernelstub -a "amdgpu.freesync_video=1"
```
4. Create file:
```
sudo touch /etc/initramfs-tools/hooks/eDP-edid
```
5. Fill it with command `sudo -E nvim /etc/initramfs-tools/hooks/eDP-edid` 
```
#!/bin/sh
mkdir -p "${DESTDIR}/usr/lib/firmware/edid"
cp /usr/lib/firmware/edid/edid.bin "${DESTDIR}/usr/lib/firmware/edid/"
exit 0
```
6.  Next make the hook executable: 
```
sudo chmod +x /etc/initramfs-tools/hooks/eDP-edid
```
7.  Update the initial ramdisk: 
```
sudo update-initramfs -u -v
```
8. Create file: /etc/X11/xorg.conf.d/20-amdgpu.conf
```
Section "Device"
Identifier "AMD"
Driver "amdgpu"
Option "VariableRefresh" "true"
Option "TearFree" "1"
Option "DRI" "3"
EndSection
```
9. Reboot and check output `sudo dmesg | grep edid`
