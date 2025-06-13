# FreeBSDnotes

## loader.conf

```
# CONSOLE COMMON
  autoboot_delay=2       # OPT. '-1' => NO WAIT | OPT. 'NO' => INFINITE WAIT
  hw.usb.no_boot_wait=1  # DO NOT WAIT FOR USB DEVICES FOR ROOT (/) FILESYSTEM
  boot_mute=YES          # LIKE '-m' IN LOADER - MUTE CONSOLE WITH FreeBSD LOGO
  loader_logo=none       # DESIRED LOGO OPTIONS: fbsdbw beastiebw beastie none
  loader_menu_frame="none"
  screen.font="6x12"   # adjust

# CONSOLE RESOLUTION - adjust
  kern.vt.fb.default.mode="1920x1080"
  efi_max_resolution="1920x1080"

# TERMINAL vt(4) COLORS
  kern.vt.color.0.rgb="#000000"
  kern.vt.color.1.rgb="#dc322f"
  kern.vt.color.2.rgb="#859900"
  kern.vt.color.3.rgb="#b58900"
  kern.vt.color.4.rgb="#268bd2"
  kern.vt.color.5.rgb="#ec0048"
  kern.vt.color.6.rgb="#2aa198"
  kern.vt.color.7.rgb="#94a3a5"
  kern.vt.color.8.rgb="#586e75"
  kern.vt.color.9.rgb="#cb4b16"
  kern.vt.color.10.rgb="#859900"
  kern.vt.color.11.rgb="#b58900"
  kern.vt.color.12.rgb="#268bd2"
  kern.vt.color.13.rgb="#d33682"
  kern.vt.color.14.rgb="#2aa198"
  kern.vt.color.15.rgb="#6c71c4

# TOUCHPAD
  hw.psm.synaptics_support=1
```

## rc.conf
```
linux_enable="YES"
kld_list="linux linux64"
dbus_enable'"YES"
webcamd_enable="YES"
cupsd_enable="YES"
acpi_ibm_load="YES"
avahi_daemon_enable="YES"
avahi_dnsconfd_enable="YES"
firewall_enable="YES"
firewall_type="workstation"
wlans_iwm0="wlan0"
ifconfig_wlan0="WPA DHCP"
kldload_nvidia="nvidia-modeset"
```

## network


/etc/wpa_supplicant.conf
```
network={
 ssid="myssid" 
 psk="mypsk" 
}

network={
  ssid="YourSSID"
  key_mgmt=WPA-EAP
  identity="YourUsername"
  password="YourPassword"
  eap=PEAP
}
```
Set up wifi for iwm0
```
sysrc wlans_iwm0="wlan0"
sysrc ifconfig_wlan0="WPA DHCP"
service netif restart
```
## bluetooth

```
pkg install iwmbt-firmware
service bluetooth start ubt0         # may have to do twice
```
## graphics
List devices
```
pciconf -lv | grep -B3 display
```

## trackpad and trackpoint

/usr/local/etc/X11/xorg.conf.d/10-input.conf

```
Section "InputClass"
Identifier "libinput keyboard catchall"
MatchIsKeyboard "on"
MatchDevicePath "/dev/input/event*"
Driver "libinput"
Option "XkbRules" "evdev"
EndSection

Section "InputClass"
Identifier "libinput touchpad catchall"
MatchIsTouchpad "on"
MatchDevicePath "/dev/input/event*"
Driver "libinput"
Option "NaturalScrolling" "on"
Option "Tapping" "on"
Option "DisableWhileTyping" "on"
Option "AccelSpeed" "0.42"
EndSection
```

or

```
Section "InputClass"
  Identifier "touchpad"
  MatchIsTouchpad "on"
  Driver "libinput"
  Option "Tapping" "on"
  Option "NaturalScrolling" "on"
  Option "DisableWhileTyping" "on"
EndSection
```
## mediakeys brightness sound etc

If acpi_ibm does not work:

/etc/devd/thinkpad.conf
```
notify 100 {
         match "system"                  "ACPI";
         match "subsystem"               "IBM";
         match "notify"                  "0x10";
         action "/usr/bin/backlight +";
};

notify 100 {
         match "system"                  "ACPI";
         match "subsystem"               "IBM";
         match "notify"                  "0x11";
         action "/usr/bin/backlight -";
};
```

Resources: 

[https://www.davidschlachter.com/misc/freebsd-acpi_video-thinkpad-display-brightness]
[https://vermaden.wordpress.com/2023/05/14/freebsd-13-2-on-thinkpad-t14-gen1/]

### installation
```
pkg install drm-kmod
sysrc kld_list+=i915kms
pkg install nvidia-driver
sysrc kld_list+=nvidia-modeset
```

/usr/local/etc/X11/xorg.conf.d/20-intel.conf
```
Section "Device"
    Identifier "Card0"
    Driver "intel"
    Driver "modesetting" # Without it, I am unable to get any GUI login
    BusID  "pci:0:2:0"
    VendorName "Intel Corporation"
EndSection

Section "Device"
	Identifier "Card0"
	Driver     "nvidia"
	BusID     "pci:1:0:0"
EndSection
```

### wayland

```
pw groupmod video -m user
pkg install wayland seatd xwayland
sysrc seatd_enable="YES"
service seatd start
```

