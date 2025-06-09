# FreeBSDnotes

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

```
sysrc wlans_iwn0="wlan0"
sysrc ifconfig_wlan0="WPA DHCP"
service netif restart
```

## graphics

```
pciconf -lv | grep -B3 display
```

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
    BusID  "PCI:0:2:0"
    VendorName "Intel Corporation"
EndSection

Section "Device"
	Identifier "Card0"
	Driver     "nvidia"
	BusID     "pci0:1:0:0"
EndSection
```

### wayland

```
pw groupmod video -m user
pkg install wayland seatd xwayland
sysrc seatd_enable="YES"
service seatd start
```

