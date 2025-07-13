+++
date = '2025-07-11T09:02:57Z'
draft = false
title = 'Virtual Machine in Arch Linux'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

### Why?

I moved fully to Linux (Arch BTW), and sometimes I need to use Windows for work (like opening RDP files).

### Check virtualization is enabled 

Check if you can virtualization- work on any CPU:

```bash 
egrep -c '(vmx|svm)' /proc/cpuinfo
```

- If the output > 0 we OK.
- If the output = 0 we need to enable virtualization.

### Tech stack 

- QEMU: The emulator.
- libvirt-daemon: runs virtualization in the background.
- bridge-utils: impotent networking dependencies.
- Virt-Manager: GUI to manage all the above.

### Installation

First. sync the package manager:

```bash 
sudo packman -Syy
```

Install packages:

```bash 
sudo pacman -S qemu virt-manager virt-viewer dnsmasq vde2 bridge-utils openbsd-netcat ebtables iptables libguestfs swtpm --noconfirm
```

Enable services:

```bash 
sudo systemctl enable --now libvirtd 
```

Give permissions to your user to run libvirt:

```bash 
sudo usermod -aG libvirt $USER
```

```bash 
sudo systemctl restart libvirtd
```

### Images:


 At the start I tried the official ISO with "drivers ISO" [Windows 11](https://www.microsoft.com/en-us/software-download/windows11) & [Virt ISO](https://github.com/virtio-win/virtio-win-pkg-scripts?tab=readme-ov-file) but I got problems with the network (can't get internet), and taking a lot of resources (15G of RAM use). Even couldn't use the image again after a shutdown:

Got this error when trying to activate the VM for the 2nd time- first time after installation is done.
```
 Error starting domain: internal error: QEMU unexpectedly closed the monitor (vm='win11'): 2025-07-10T19:22:48.974831Z qemu-system-x86_64: -tpmdev passthrough,id=tpm-tpm0,path=/dev/fdset/0,cancel-path=/dev/fdset/1: '/dev/fdset/0' is not a TPM device. Traceback (most recent call last): File "/usr/share/virt-manager/virtManager/asyncjob.py", line 71, in cb_wrapper callback(asyncjob, *args, **kwargs) ~~~~~~~~^^^^^^^^^^^^^^^^^^^^^^^^^^^ File "/usr/share/virt-manager/virtManager/asyncjob.py", line 107, in tmpcb callback(*args, **kwargs) ~~~~~~~~^^^^^^^^^^^^^^^^^ File "/usr/share/virt-manager/virtManager/object/libvirtobject.py", line 57, in newfn ret = fn(self, *args, **kwargs) File "/usr/share/virt-manager/virtManager/object/domain.py", line 1384, in startup self._backend.create() ~~~~~~~~~~~~~~~~~~~~^^ File "/usr/lib/python3.13/site-packages/libvirt.py", line 1390, in create raise libvirtError('virDomainCreate() failed') libvirt.libvirtError: internal error: QEMU unexpectedly closed the monitor (vm='win11'): 2025-07-10T19:22:48.974831Z qemu-system-x86_64: -tpmdev passthrough,id=tpm-tpm0,path=/dev/fdset/0,cancel-path=/dev/fdset/1: '/dev/fdset/0' is not a TPM device.
```

### Tiny 11
When looking on the web I saw [Tiny 11](https://library.neelkalpa.com/tiny1125h2v2) . Looks clean, and have nothing on it. I need only the browser most of the time just to get the RDP file.
The image is clean, simple, don't need a lot of resources, and the installation is fast. 

![tiny11](/images/Tiny11.jpg)


