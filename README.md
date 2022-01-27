# Installation of PowerShell to Turris Omnia LXC Container
> This repo is a step-by-step tutorial how to install PowerShell Core to Turris Omnia LXC container. It's aiming to Windows/PowerShell oriented users which not familiar with SSH/Bash environment and the official tutorials from CZ.NIC are driving them crazy.

## Overview

The installation consists of these steps:

1. Create Debian LXC container
2. Install prerequisites
3. Deployment of PowerShell Core libraries to LXC container
4. Hello world!

## 1. Create Debian LXC container

Connect to your Turris Omnia router by SSH and create the LXC container for PowerShell Core. **[Official Manual](https://www.turris.cz/doc/en/howto/lxc)**

**Note**: If you don't see any templates in LuCI or you receive [this](https://forum.turris.cz/t/lxc-container-no-templates/5296/17) error, just change your DNS settings in Foris from whatever you have there to Cloudface or anything else. The problem is caused by DNS which is not resolving template URL correctly.
```
lxc-create -t download -n PSCore
```

- Distribution: **Debian**
- Release: **Bullseye**
- Architecture: **armv7l** (that is seven and the lower "L" letter, not 1 (digit)

Alternatively, you can use LuCI to create the LXC container. A distribution doesn't not really matter. This tutorial was tested on Debian but it would work more likely on other distributions.

## 2. Simple Debian configuration

Start the container from LuCI or by executing this command:

```
lxc-start -n PSCore
```

Connect to the container:

```
lxc-attach -n PSCore
```

Install prerequisites:

```
apt-get update
apt-get install git-core openssh-server rsync sudo fakeroot cifs-utils nano wget libunwind8 icu-devtools -y
```

SSH is not really necessary but it may become handy sooner or later.

Change the container hostname (for eg. `PSCore`):

```
nano /etc/hostname
```

Set the new hostname to localhost:

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

Now, let's create a directory for PowerShell Core and set path to it.

```
mkdir -p $HOME/powershell/7 && cd $HOME/powershell
```

Now, you can download the PowerShell Core binaries:

```
wget https://github.com/PowerShell/PowerShell/releases/download/v7.2.0/powershell-7.2.0-linux-arm32.tar.gz
```

You can customize this link to whatever version of PowerShell you want. Don't forget to change a specific path (*name of last directory, 7 in our case*) in previous and following command for each specific version of PowerShell Core you wish to run:

```
tar zxf ./powershell-7.2.0-linux-arm32.tar.gz -C $HOME/powershell/7
```

For an easy use of PowerShell, you can execute:

```
nano ~/.bashrc
```

and enter these lines at the end of the file:
```
export PATH=$PATH:$HOME/powershell/7
```

This will create powershell environmental variables every time when you log in to the container by SSH. **If you have plans to run multiple PowerShell Core version side-by-side, I would avoid the manipulation with the environment variables and just use a full path classification.**

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
PowerShell 7.1.4
Copyright (c) Microsoft Corporation. All rights reserved.

https://aka.ms/powershell
Type 'help' to get help.
```

go ahead and try the **Hello world** cmdlet to write to the console.

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
cd $HOME/powershell/7/
```

and execute the `pwsh` command here. Alternatively, you can use the full path classification:

```
$HOME/powershell/7/pwsh
```

## Links
[PowerShell Core Releases](https://github.com/PowerShell/PowerShell/releases)<br>
[How to deploy .NET Core SDK to LXC Turris Container](https://github.com/KUTlime/Installation-of-dotNET-Core-to-Turris-Omnia-LXC-Container)

## Credits

All goes to PowerShell Core team. YOU ROCK!
  
