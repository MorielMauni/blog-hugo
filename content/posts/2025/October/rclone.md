+++
date = '2025-10-03T00:03:50Z'
draft = false
title = 'rclone: use cloud providers on UNIX'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++


[YouTube guide](https://www.youtube.com/watch?v=lNH4hHyqkVw).

### Install
```
sudo apt install rclone
```

### Activate configuration:
```
rclone config
```

#### What to answer:
- ```n```: new connection.
- ```<name>```: name it.
- ```Google Drive / "drive"```: search for entry.
- ```clinet id```: enter for defualt. 
- ```client secret```: enter for defualt. 
- ```scope```: 1 then enter.
- ```service_acount_file```:  enter for defualt. 
- ```advance config```: N.
- ```use auto config```: Y enter --> opens the browser.
	-  If working with a server, type N.
	- ```rclone authorize "drive"```: in the main machine.
	- task the token that created and put in the server machine.
- ```team drive```: N.
- In the ```Yes this is OK``` pick Y.
- Pick Q.

#### Mount the drive

```
mkdir <mount_point> && sudo chmod 755 <mount_point>
```

```
rclone mount <name-of-drive>: /path/to/mount_point
```



