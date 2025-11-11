+++
date = '2025-11-11T10:57:47Z'
draft = false
title = 'Ansible environment commands'
author = 'Moriel Mauni'
tags = [" "]
categories = [" "]
description = " "
+++


---

## Linux

#### Ansible

Switch to root

```bash
sudo su
```

Update + install Ansible

```bash
apt-get update && apt-get install ansible -y
```

Install an Ansible package

```bash
sudo apt install ansible-lint
```

**Return to the user**

Install pythin-winrm

```bash
sudo apt-get install python3-winrm
```

Install pip

```bash
sudo apt-get install pip
```

If there is an UTF-8 error:

```bash
echo 'export LANG=en_US.UTF-8' >> ~/.bashrc
echo 'export LC_ALL=en_US.UTF-8' >> ~/.bashrc
echo 'export LANGUAGE=en_US.UTF-8' >> ~/.bashrc
```

#### Chocolatey

==Most of the times it's already installed==
On the Control Machine (UNIX/WSL)

```bash
ansible-galaxy collection install chocolatey.chocolatey
```

#### CredSSP

Not best practice

```bash
pip install --break-system-packages requests-credssp pywinrm
```

#### DNS

Edit ```/etc/resolv.conf```

```bash
nameserver <Domain_DNS>
search <Domain_Name>
```

---

## Windows

#### WinRM

List the service status:

```PowerShell
winrm get winrm/config/Service
```

Check for:

```PowerShell
AllowRemoteAccess = true
HTTP = 5985
HTTPS = 5986
```

List the listener status:

```PowerShell
winrm get winrm/config/listener
```

##### Troubleshoot

Restore configuration,
Re-enable default listener,
List the listener:

```PowerShell
winrm invoke Restore winrm/Config
winrm quickconfig
winrm enumerate winrm/config/listener
```

#### CredSSP

Check if if on with PowerShell:

```PowerShell
Get-WSManCredSSP
```

The output is:

```
The machine is configured to allow delegating fresh credentials.
This computer is configured to receive credentials from a remote client computer.
```

Activate CredSSP:

```
Enable-WSManCredSSP -Role Server
```

#### ICMP

Checking ICMP (Ping) Status

- Open the "Windows Defender Firewall with Advanced Security" (search for "firewall" or run `wf.msc`).
- Go to "Inbound Rules" and look for the rules called "File and Printer Sharing (Echo Request - ICMPv4-In)" and "File and Printer Sharing (Echo Request - ICMPv6-In)".
- If these rules are enabled, ICMP is allowed; if not, ping is blocked.

```PowerShell
Get-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)"
```

Activate it:
Enabling ICMP (Ping) on Windows
You can enable ICMP using the graphical interface:

- Open "Windows Defender Firewall with Advanced Security".
- Find and enable the inbound rules for "File and Printer Sharing (Echo Request - ICMPv4-In)" and "File and Printer Sharing (Echo Request - ICMPv6-In)" by right-clicking and choosing "Enable Rule".

```PowerShell
Enable-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)"
Enable-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv6-In)"
```
