# Driver for RTL88x2bu wifi adaptors (TP-LINK Archer T3U/T4U V3)

Updated driver for RTL88x2bu wifi adaptors based on Realtek's source distributed with myriad adapters.

Realtek's 5.6.1.6 source was found bundled with the [Cudy WU1200 AC1200 High Gain USB Wi-Fi Adapter](https://amzn.to/351ADVq) and can be downloaded from [Cudy's website](http://www.cudytech.com/wu1200_software_download).

Build confirmed on:

```
Linux version 5.4.0-4-amd64 (debian-kernel@lists.debian.org) (gcc version 9.2.1 20200203 (Debian 9.2.1-28)) #1 SMP Debian 5.4.19-1 (2020-02-13)
```

## Using and Installing the Driver

### Simple Usage

In order to make direct use of the driver it should suffice to build the driver with `make` and to load it with `insmod 88x2bu.ko`. This will allow you to use the driver directly without changing your system persistently.

It might happen that your system freezes instantaneously. Ensure to not loose important work by saving and such beforehand.

### DKMS installation

If you want to have the driver available at startup, it will be convenient to register it in DKMS. An executable explanation of how to do so can be found in the script `deploy.sh`. Since registering a kernel module in DKMS is a major intervention, only execute it if you understand what the script does.

### Unknown Symbol Errors

Some users reported problems due to `Unknown symbol in module`. This can be caused by old deployments of the driver still being present in the systems directories.
One solution reported was to forcefully remove all old driver modules:

```
sudo dkms remove rtl88x2bu/5.8.7.4 --all
find /lib/modules -name cfg80211.ko -ls
sudo rm -f /lib/modules/*/updates/net/wireless/cfg80211.ko
```

This can also be caused by cfg80211 module not being present in the kernel.
You can remedy this by running:

```
sudo modprobe cfg80211
```

### Linux 5.18+ and RTW88 Driver

Starting from Linux 5.18, some distributions have added experimental RTW88 USB
support (include RTW88x2BU support). It is not yet stable but if it works well
on your system, then you no longer need this driver. But if it doesn't work or
is unstable, you need to manually blacklist it because it has a higher loading
priority than this external drivers.

Check the currently loaded module using `lsmod`. If you see `rtw88_core`,
`rtw88_usb`, or any name beginning with `rtw88_` then you are using the RTW88
driver. If you see `88x2bu` then you are using this RTW88x2BU driver.

To blacklist RTW88 8822bu USB driver, run the following command:

```
echo "blacklist rtw88_8822bu" > /etc/modprobe.d/rtw8822bu.conf
```

And reboot your system.

### Secure Boot

Secure Boot will prevent the module from loading as it isn't signed.

Check your secure boot setting with `mokutil --sb-state`. You should see something like:

    SecureBoot disabled

If not, disable secure boot in BIOS.
