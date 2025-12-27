# Installation of PowerShell on Turris Omnia LXC Container

> This repo is a step-by-step tutorial on how to install PowerShell to Turris Omnia LXC container. It's aiming to Windows/PowerShell-oriented users which not familiar with SSH/Bash environment and the official tutorials from CZ.NIC are driving them crazy.

## Overview

The installation consists of these steps:

1. Create a Debian LXC container
2. Install prerequisites
3. Deployment of PowerShell libraries to an LXC container
4. Hello world!

## 1. Create a Debian LXC container

Connect to your Turris Omnia router by SSH and create the LXC container for PowerShell. **[Official Manual](https://www.turris.cz/doc/en/howto/lxc)**

**Note**: If you don't see any templates in LuCI or you receive [this](https://forum.turris.cz/t/lxc-container-no-templates/5296/17) error, just change your DNS settings in Foris from whatever you have there to Cloudface or anything else. The problem is caused by DNS, which is not resolving the template URL correctly.

```bash
lxc-create -t download -n PowerShell
```

- Distribution: **Debian**
- Release: **Bookworm**
- Architecture: **armv7l** (that is seven and the lower "L" letter, not 1 (digit)

Alternatively, you can use LuCI to create the LXC container. A distribution doesn't really matter. This tutorial was tested on Debian, but it will likely work on other distributions.

## 2. Simple Debian configuration

Start the container from LuCI or by executing this command:

```bash
lxc-start -n PowerShell
```

Connect to the container:

```bash
lxc-attach -n PowerShell
```

Install prerequisites:

```bash
apt-get update
apt-get install git-core openssh-server rsync sudo fakeroot cifs-utils nano wget curl libunwind8 icu-devtools jq -y
```

SSH is not really necessary, but it may become handy sooner or later.

Change the container hostname (for e.g., `PowerShell`):

```bash
nano /etc/hostname
```

Set the new hostname to localhost:

```bash
nano /etc/hosts
```

Add this line (for `PowerShell` as hostname):

```bash
127.0.1.1   PowerShell
```

Reboot the LXC container from LuCI interface or execute this command:

```bash
exit
```

to exit the LXC container SSH session (*which takes you to your Turris Omnia session*) and

```bash
lxc-stop -n PowerShell -r
```

to restart the container.

Go to **Foris**, the **DNS** setting tab, and turn on the "Domain of DHCP clients in DNS" if you don't have it enabled already. Now, you can access the LXC container by its domain name - **PowerShell**.

After a restart, you can connect to the container by executing:

```bash
lxc-attach -n PowerShell
```

And if your setup was successful, you should see: **root@PowerShell:~#** in your terminal.

## 3. Deployment of PowerShell libraries to an LXC container

Now, let's create a directory for PowerShell and set the path to it.

```bash
mkdir -p $HOME/powershell/7 && cd $HOME/powershell
```

Now, you can download the PowerShell binaries:

```bash
wget -qO- $(curl -s https://api.github.com/repos/PowerShell/PowerShell/releases/latest | jq -r '.assets[].browser_download_url' | grep "linux-arm32.tar.gz") | tar zxf - -C $HOME/powershell/7
```

You can customize this link to whatever version of PowerShell you want. Don't forget to change a specific path (*name of last directory, 7 in our case*) in the previous and following commands for each specific version of PowerShell you wish to run:

```bash
tar zxf ./powershell-7.2.0-linux-arm32.tar.gz -C $HOME/powershell/7
```

For easy use of PowerShell, you can execute:

```bash
nano ~/.bashrc
```

and enter these lines at the end of the file:

```bash
export PATH=$PATH:$HOME/powershell/7
```

This will create powershell environmental variables every time when you log in to the container by SSH. **If you have plans to run multiple PowerShell version side-by-side, I would avoid the manipulation with the environment variables and just use a full path classification.**

Now, you can execute:

```bash
exit
```

to exit the LXC container SSH session (*which takes you to your Turris Omnia session*) and

```bash
lxc-stop -n PowerShell -r
```

to restart the container. After restart, execute:

```bash
lxc-attach -n PowerShell
```

to attach to the container again. Now, you should be all set and good to go.

## 4. Hello world

Try to run the following command:

```bash
pwsh
```

if you see following outcome (*it may change overtime*)

```bash
PowerShell 7.1.4
Copyright (c) Microsoft Corporation. All rights reserved.

https://aka.ms/powershell
Type 'help' to get help.
```

Go ahead and try the **Hello world** cmdlet to write to the console.

```bash
Write-Host "Hello World!"
```

If you see:

```bash
Hello World!
```

congratulation! You have a fully operational PowerShell up and running in the LXC container hosted in your Turris Omnia.

If you receive the error "bash: pwsh: command not found," navigate manually to it:

```bash
cd $HOME/powershell/7/
```

and execute the `pwsh` command here. Alternatively, you can use the full path classification:

```bash
$HOME/powershell/7/pwsh
```

## Auto update of PowerShell via cron

Create a shell script with the following commands:

```bash
export PATH="$PATH:/usr/local/sbin"
apt-get update
apt-get upgrade -y
apt-get autoclean
echo "Updating PowerShell"
wget -qO- $(curl -s https://api.github.com/repos/PowerShell/PowerShell/releases/latest | jq -r '.assets[].browser_download_url' | grep "linux-arm32.tar.gz") | tar zxf - -C $HOME/powershell/7
chmod +x /root/powershell/7/pwsh
```

Place it to some location, e.g., `/root/installUpdate.sh`.

In the terminal, execute these commands:

```bash
apt-get install cron jq curl -y
chmod +x /root/installUpdate.sh
crontab -e
```

Insert the following line of code:

```bash
44 4 * * * /root/installUpdate.sh >> /var/log/updatejob.log 2>&1
```

This will execute the script every single day at 04:44.

## Accelerated version for power user

Create a container

```bash
lxc-create -t download -n PowerShell
lxc-start -n PowerShell
lxc-attach -n PowerShell
```

Install PowerShell and configure the container environment

```bash
apt-get update
apt-get install git-core openssh-server rsync sudo fakeroot cifs-utils nano wget curl cron libunwind8 icu-devtools jq -y
nano /etc/hostname
nano /etc/hosts
mkdir -p $HOME/powershell/7 && cd $HOME/powershell
wget -qO- $(curl -s https://api.github.com/repos/PowerShell/PowerShell/releases/latest | jq -r '.assets[].browser_download_url' | grep "linux-arm32.tar.gz") | tar zxf - -C $HOME/powershell/7
chmod +x /root/powershell/7/pwsh
nano ~/.bashrc
exit
```

Restart and reconnect

```bash
lxc-stop -n PowerShell -r
lxc-attach -n PowerShell
```

Prepare automatic update

```bash
chmod +x /root/installUpdate.sh
nano /root/installUpdate.sh
```

Copy/paste the update script logic

```bash
export PATH="$PATH:/usr/local/sbin"
apt-get update
apt-get upgrade -y
apt-get autoclean
echo "Updating PowerShell"
wget -qO- $(curl -s https://api.github.com/repos/PowerShell/PowerShell/releases/latest | jq -r '.assets[].browser_download_url' | grep "linux-arm32.tar.gz") | tar zxf - -C $HOME/powershell/7
chmod +x /root/powershell/7/pwsh
```

Prepare update

```bash
crontab -e
```

Insert the following line of code:

```bash
44 4 * * * /root/installUpdate.sh >> /var/log/updatejob.log 2>&1
```

Auto start container

Edit file `/etc/config/lxc-auto`
```bash

config container
        option name PowerShell
        option timeout 60
```

## Links

[PowerShell Releases](https://github.com/PowerShell/PowerShell/releases)<br>
[How to deploy .NET Core SDK to LXC Turris Container](https://github.com/KUTlime/Installation-of-dotNET-Core-to-Turris-Omnia-LXC-Container)

## Credits

All goes to the PowerShell team. YOU ROCK!
