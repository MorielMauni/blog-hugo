+++
date = '2025-07-15T23:22:09Z'
draft = false
title = '"Getting" more RAM'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++

Need more RAM? 

We can get memory from the SSD and make it a RAM memory.

--- 
---
##                             !!! Don't install, READ FIRST !!!
---
---

## Overview
### Method 1: Swap file

Post installation is better, don't need to mount anything and the creation is easy.

```
sudo dd if=/dev/zero of=/swapfile bs=1M count=17408 status=progress
```

- **dd**: disk duplicator tool
	- if: input file (zeroed bytes).
	- specify a directory
- of:  output file path (must be under `/`
	- out put directory.
	- must to be  under / (root)
- bs: block size.
- count: total MBs to allocate- binary [GB to MB](https://www.gbmb.org/gb-to-mb).
- status: shows live progress during creation.

Permissions:
```
sudo chmod 600 /swapfile
```

```
sudo mkswap -U clear /swapfile
```

Activate:
```
sudo swapon /swapfile
```

Need to mount the swapfile:
```
sudo nvim /etc/fstab
```

add the path to swap file:
```
/swapfile none swap defaults 0 0
```

### Method 2: Zram

[zram](https://docs.kernel.org/admin-guide/blockdev/zram.html), formerly called compcache, is a Linux kernel module for creating a compressed block device in RAM, i.e. a RAM disk with on-the-fly disk compression. The block device created with zram can then be used for swap or as a general-purpose RAM disk. The two most common uses for zram are for the storage of temporary files (`/tmp`) and as a swap device. Initially, zram had only the latter function, hence the original name "compcache" ("compressed cache").

Install:
```
sudo pacman -S zram-generator
```

Create a config file:
/etc/systemd/zram-generator.conf
```
[zram0]

zram-size = ram / 2

EOF
```

Save + Reboot to activate. 


### What to pick?

#### Best Practice:

 **Use ZRAM (6 GB)** as fast, compressed memory.
- Performance boost.
- No SSD wear.
- Great for apps, browser tabs, compiling, etc.

**Use a Swap File (14–15 GB)** as safety net.
- Handles memory overflow.
- Enables hibernation (optional).  
- Easy to resize or delete later.

#### Why?

ZRAM:

- Stores more "virtual RAM" by compressing in real RAM.
- Much faster than SSD.
- Doesn't wear down your SSD.
- Ideal for **active use** (apps, multitasking, compiling).

Swap File

- Only used **after** zram fills.
- Protects from OOM (Out-of-Memory) crashes.
- Optional for hibernation support.

#### The Plan:

As we seen above, i'm going to make with **Zram** with 6 GB and **Swap-file** method with 5 GB.

#### Zram

Install Zram:
```
sudo pacman -S zram-generator
```

Create the config file:
```
sudo nvim /etc/systemd/zram-generator.conf
```

zram-generator.conf
```
[zram0]
zram-size = 6G
compression-algorithm = zstd
```

##### Troubleshoot

If it's fails, create in manually 
```
sudo modprobe -r zram
sudo modprobe zram
echo 4G | sudo tee /sys/block/zram0/disksize
sudo mkswap /dev/zram0
sudo swapon /dev/zram0
swapon --show
```

**Then, reboot.**


#### Make the Swap-file:

Disable CoW:
```
sudo mkdir -p /var/swap
sudo chattr +C /var/swap
```

Make the file:
```
sudo dd if=/dev/zero of=/swapfile bs=1M count=16384 status=progress
```

Give the swap permissions:
```
sudo chmod 600 /swapfile
```

```
sudo mkswap /swapfile
```

```
sudo swapon /swapfile
```

Mount it:
```
echo '/swapfile none swap defaults 0 0' | sudo tee -a /etc/fstab
```

Check the new memory:

```
swapon --show
free -h
```

### Results

```bash
|Memory Type|Size|Description|
|---|---|---|
|Physical RAM|~15 GB|Actual installed RAM chips|
|---|---|---|
|ZRAM Swap|e.g. 6 GB|Compressed swap in RAM (fast virtual swap)|
|---|---|---|
|Swap File|16 GB|Swap space on SSD (slower virtual swap)|
```
