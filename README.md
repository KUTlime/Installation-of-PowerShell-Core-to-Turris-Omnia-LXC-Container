# Installation of PowerShell Core to Turris Omnia LXC Container
> This repo is a step-by-step tutorial how to install PowerShell Core to Turris Omnia LXC container. It's aiming to Windows/PowerShell orientied users which not familiar with SSH/Bash enviroment and the official tutorials are driving them crazy.

## Overview

The installation consists of these steps:

1. Create Debian LXC container
2. Install prerequisites 
3. Deployment of PowerShell Core libraries to LXC container
4. Hello world!

## 1. Create Debian LXC container

Connect to your Turrins Omnia router by SSH and create the LXC container for PowerShell Core. **[Official Manual](https://www.turris.cz/doc/en/howto/lxc)**
```
lxc-create -t download -n PSCore
```

- Distribution: **Debian**
- Release: **Stretch**
- Architecture: **armv7l** (that is seven and the lower "L" letter, not 1 (digit)

Alternatively, you can use LuCI to create the LXC container. A distribution doesn't not really matter. This tutorial was tested on Debian but it would work more likely on other distribution.

## 2. Simple Debian configuration

Start the container from LuCI or by executing this command:

```
lxc-start -n PSCore
```

Connect to container:

```
lxc-attach -n PSCore
```

Install prerequisites:

```
apt-get update
apt-get install sudo
apt-get install nano
apt-get install wget
apt-get install libunwind8
apt-get install git-core openssh-server rsync sudo fakeroot cifs-utils -y
```

SSH is not really necessary but it may become handy sooner or later.

Change hostname (for eg. `PSCore`):

```
nano /etc/hostname
```

Set new hostname to localhost:

```
nano /etc/hosts
```

Add this line (for `PSCore` as hostname):

```
127.0.1.1   PSCore
```

Reboot the LXC container from LuCI interface or execute this command:

```
exit
```
to exit the LXC container SSH session (*which takes you to your Turris Omnia session*) and

```
lxc-stop -n PSCore -r
```

to restart the container.

Go to **Foris**, the **DNS** setting tab and turn on the "Domain of DHCP clients in DNS" if you don't have it enabled already. Now, you can access the LXC container by its domain name - **PSCore**. 

After restart, you can connect to the container by executing:

```
lxc-attach -n PSCore
```

and if your setup was successful, you should see: **root@PSCore:~#** in your terminal.

## 3. Deployment of PowerShell Core libraries to LXC container

Now, let's create a directory for PowerShell Core and step into it.

```
mkdir -p $HOME/powershell/7-preview && cd $HOME/powershell
```

Now, you can download the PowerShell Core binaries:

```
wget https://github.com/PowerShell/PowerShell/releases/download/v7.0.0-preview.1/powershell-7.0.0-preview.1-linux-arm32.tar.gz
```

You can customize this link to whatever version of PowerShell you want, just don't forget to change a specific path (*name of last directory, 7-preview in our case*) for each specific version of PowerShell Core in previous and following command:

```
tar zxf ./powershell-7.0.0-preview.1-linux-arm32.tar.gz -C $HOME/powershell/7-preview
```

For an easy use of PowerShell, you can execute:

```
nano ~/.bashrc
```

and enter these lines at the end of the file:
```
export PATH=$PATH:$HOME/powershell/7-preview
```

This will create powershell environmental variables everytime when you log in to the container by SSH. **If you have plans to run multiple PowerShell Core version side-by-side, I would avoid the manipulation with the enviroment variables and just use a full path classification.**

Now, you can execute:

```
exit
```
to exit the LXC container SSH session (*which takes you to your Turris Omnia session*) and

```
lxc-stop -n PSCore -r
```

to restart the container. After restart, execute:

```
lxc-attach -n PSCore
```

to attach to the container again. Now, you should have all set and good to go.

## 4. Hello world!

Try to run following command:

```
pwsh
```

if you see following outcome (*it may change overtime*)

```
PowerShell 7.0.0-preview.1
Copyright (c) Microsoft Corporation. All rights reserved.

https://aka.ms/pscore6-docs
Type 'help' to get help.
```

go ahead and try the **Hello world** write to console.

```
Write-Host "Hello World!"
```

If you see:

```
Hello World!
```

congratulation! You have fully operational PowerShell Core up and running in the LXC container hosted in your Turris Omnia.

If you receive the error "bash: pwsh: command not found" navigate manually to it:

```
cd $HOME/powershell/7-preview/
```

and execute the `pwsh` command here. Alternatively, you can use the full path classification:

```
$HOME/powershell/7-preview/pwsh
```

## Links
[PowerShell Core Releases](https://github.com/PowerShell/PowerShell/releases)<br>
[.NET Core SDK installation tutorial for Turris Omnia](https://github.com/KUTlime/Installation-of-dotNET-Core-to-Turris-Omnia-LXC-Container/blob/master/README.md)<br>

## Credits

All goes to PowerShell Core team. YOU ROCK!
  
