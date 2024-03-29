---
layout: post
title:  "Raspberry Pi"
date:   2019-04-30 01:30:13 +0000
categories: Platform
tags: Hardware RaspberryPi WiFi Bluetooth
---

Some Raspberry Pi settings

# Set up

Insert the SD card in your laptop.

## Step 1: Set up an empty SD Card

**(Skip this section if you have an SD Card with NOOBS pre-installed)**

Download and install the software

Get the latest Raspbian here: [Raspbian](https://www.raspberrypi.org/downloads/raspbian/)

Unzip the file, you obtain an image file (extension .img)

To install this image on the SD card, download and install Etcher: [Etcher](https://www.balena.io/etcher/)

Starting Etcher, you first select your image file, then your SD card, and 'Flash'.

# Step 2: Getting Started headless (without Monitor)

## 5.2.1 SSH

To connect to the Raspberry Pi without monitor, mouse and keyboard, we use directly
your laptop. To do this, we need to enable the 'ssh' protocol (for Secure Shell)
on the  Raspberry Pi. This protocol gives us the possibility to remotely access
a computer through the network. On your laptop, open text editor (TextEdit on Mac,
Editor on Windows) and save an empty file named 'ssh' (without extension) at the
root of the 'boot' disk (SD card). This file will indicate that we want to enable ssh.

## 5.2.2 Network Access

To connect your Raspberry Pi to the network, create a second file
'wpa_supplicant.conf' with the following content depending on the network you 
want to connect to.

<details><summary markdown="span">Personal Network</summary>

<pre><code>
country=NL
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
  ssid="YOUR_NETWORK_SSID"
  psk="YOUR_NETORK_PASSWORD"
}

</code></pre>

</details>


<details><summary markdown="span">Eduroam</summary>

<pre><code>
country=NL
update_config=1
ctrl_interface=/var/run/wpa_supplicant

network={
  scan_ssid=0
  ssid="eduroam"
  key_mgmt=WPA-EAP
  eap=PEAP
  phase2="MSCHAPV2"
  identity="YOUR_EDUROAM_NETID"
  password="YOUR_EDUROAM_PASSWORD"
}

</code></pre>

<p>
Replace YOUR_EDUROAM_NETID and YOUR_EDUROAM_PASSWORD with your netid and password.
</p>

</details>

Save this file on the 'boot' partition. Make sure that its extension is .conf rather
than .conf.txt (most texts editor will automatically add .txt or .rtf and hide it,
double-check that your file is not recognised as a text document).

**Disclaimer**: this process requires to insert the Eduroam password. Thus, it is
important to protect the access to your Raspberry Pi. Make sure you apply ALL the
following steps marked as **SECURITY**

**SECURITY** Disable auto-login: by default, anyone with an HDMI cable can look at
your Raspberry Pi and its files. Disable this feature

### 5.3 Booting and Connecting

**DISCLAIMER:** If you do this tutorial as part of a class, keep in mind that
all Raspberry Pi will have the same name on the network. You will have to power
your Raspberry Pi one after the other to be able to identify them.

Eject the SD card and insert it in on the Raspberry Pi, then power the Pi.

If the settings are correct, it takes about 30 seconds to get the Raspberry Pi on
the network. Make sure your laptop is connected to the same network, then connect
via ssh with the following command.

**On Windows**, you need to search for Windows Powershell and type the below command in there

**On Mac**

```bash
ssh pi@raspberrypi.local
```

In this command, 'pi' is the default username and raspberry.local is your default hostname (the
name of the Pi on the local network). You should type in your own username and hostname if you have changed it.

First you will need to type in 'yes' followed by Enter.

Then, you will be prompt for the default password. Type in 'raspberry'. Note:
when you type in the password, no letter appears in the terminal. This is the
normal behaviour to protect your password.

![SSH Pi](/docs/assets/res/ssh_pi.png)

Once connected, we want to change the hostname, i.e. the name of your Raspberry Pi
on the network. By default, it is 'raspberrypi' which is not practical while you
have several of them (like in a classroom setting). To do this, we need to edit
two files /etc/hostname and /etc/hosts. We use the editor nano for this.

Type in:

```bash
sudo nano /etc/hostname
```

This command opens the file /etc/hostname in nano. Replace 'raspberrypi' with the
name of your choice (without space). In the following example, we use the
hostname 'noisy-wheelchair'.

![Hostname](/docs/assets/res/hostname.png)

To save and exist, press Ctrl+X, press Y ()to answer 'Yes' to the question) followed
by Enter. Similarly, edit the file /etc/hosts and change 'raspberrypi' for the
same name, e.g. 'noisy-wheelchair'.

```bash
sudo nano /etc/hosts
```

![Hosts](/docs/assets/res/hosts.png)

Again, save and exit with Ctrl+X, then Y followed by Enter.

Reboot your Rasberry Pi with:

```bash
sudo reboot
```

After about 30 second, you should be able to connect to you Raspberry Pi with
your new hostname. For example:

```bash
ssh pi@noisy-wheelchair.local
```

**SECURITY** Next, we want to change the user password: changing the
default password 'raspberry' gives you more guaranty you are the only one
accessing your Raspberry Pi.

```
sudo passwd
```

![Change password](/docs/assets/res/pass.png)

### 5.4 Installing Requirements

First, we need to update the Raspberry Pi with the latest version of its software.

```bash
sudo apt-get update
sudo apt-get upgrade
```

Set up Git

```bash
sudo apt-get install git
```

Clone your GitHub repository. Similarly to cloning the Git repository on your laptop,
this time we clone it on the Raspberry Pi. For example:

```bash
git clone https://github.com/example/prototype.git
```

In the terminal, to navigate through folder we use 'cd'. Enter the folder you have just
cloned with:

```bash
cd prototype
```

Then, we need to create an .env file, in the project folder, with  THING_ID, the THING_TOKEN and the
SERIAL port.

```bash
nano .env
```

Copy your thing id and token, and use /dev/ttyUSB0 as a serial port (there are other serial ports on your raspberry pi you can try, such as ttyS0, ttyACM0).

![Change password](/docs/assets/res/env_file.png)

Install the requirements with Pip

```bash
python3 -m pip install -r requirements.txt --user
```

Your Raspberry Pi is ready to run code!
