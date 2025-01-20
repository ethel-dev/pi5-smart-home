# Raspberry Pi

<!-- TOC -->

- [Raspberry Pi](#raspberry-pi)
  - [1. Get The Pi 5...](#1-get-the-pi-5)
  - [2. Raspberry Pi Imager](#2-raspberry-pi-imager)
  - [3. Accessories/Hardware](#3-accessorieshardware)
  - [4. Setting up Raspberry Pi OS (Raspbian)](#4-setting-up-raspberry-pi-os-raspbian)
    - [4.1 Using SSH](#41-using-ssh)
      - [4.1.1 SSH Client](#411-ssh-client)
    - [4.2 Raspberry Pi Command Line Basics](#42-raspberry-pi-command-line-basics)
    - [4.3 Turn off the lights!](#43-turn-off-the-lights)

<!-- /TOC -->

This specific guide is not about setting up your Raspberry Pi to be a complete smart hub. Information on that can be found throughout this repo in relevant articles. *This guide is about how to set up your Raspberry Pi to be a working computer in the first place, outlining the early stages and choices that I made in setting up my Pi to be a future smart hub.*

For the purposes of this guide and flexibility, I just recommend installing Raspberry Pi OS (Raspbian). This will give you a full Debian-like Linux environment to do basically whatever you want with. There are other options, such as [Home Assistant OS](https://www.home-assistant.io/installation/raspberrypi) and [Homebridge](https://github.com/homebridge/homebridge-raspbian-image/wiki/Getting-Started), that you can flash easily to a Pi, but I avoided these as they limit the flexibility of using the device. In order to install these services anyways, one can use Docker on top of the OS and see similar results (see Docker article/other articles as they are written for information on using Docker to install and manage these services).

However, it is also worth noting that Home Assistant will have the best experience if you flash it directly to the Pi via Home Assistant OS. But it is still a great experience through the Docker container. I want to do a lot of stuff for my home with this single Pi 5, so I am sticking with the pure Linux tap. This guide reflects the choice of Raspberry Pi OS.

## 1. Get The Pi 5...

For the purposes of this smart home project, I chose a Raspberry Pi 5. These are shockingly capable computers for smart hub applications. With my Pi 5 with 4GB of RAM, I am able to record two 1080p streams 24/7, do object detection on those streams, run multiple smart home integration softwares, and more, without even really maxing out the resources it has to offer. I could probably do at least a few more cameras before it starts to really struggle.

It being an ARM computer, it is very efficient and low on power consumption. It also runs Linux very easily and is well supported by open-source software in the home hacking space. Perhaps, it would be an interesting project to do something similar like this with an M4 Mac Mini, but those are a lot more expensive (for astronomically more power, though). If you set reasonable expectations for your Pi 5 smart hub it can actually do quite a bit though. But for instance, ain't no way you're doing the NVR plus the smart home software plus any kind of media server without it getting at least a little shaky.

Especially nowadays, since the supply chain has gotten much stronger, I don't recommend getting anything less powerful than the Pi 5 with 4 GB if you are thinking of using it for similar jobs to me. I have heard some people have a decent experience with the Pi 4 with the same amount of RAM running 2-3 1080p cameras and the works, but there's no doubt the Pi 5 is a much more capable computer. 

The Pi 5 also has support for more M.2/PCIe cards such as the Coral TPU M.2 AI Accelerator, which is cheaper than the USB variant. So if you plan on using a TPU with the computer (say, for better object detection on your cameras that doesn't need to hit the CPU as hard), it is sort of a no-brainer. It will save you money on the TPU long term to get a Pi 5, just so you aren't stuck buying the USB one which is double the cost of the M.2 card.

## 2. Raspberry Pi Imager

Regardless of which OS you choose to flash, it is easiest to flash and set up a Raspberry Pi with the [Raspberry Pi Imager](https://www.raspberrypi.org/software/) software. There is a pretty good [official step-by-step guide](https://www.raspberrypi.com/documentation/computers/getting-started.html#raspberry-pi-imager) to using the Imager software located in the [Raspberry Pi Documentation](https://www.raspberrypi.com/documentation/). As stated above, I installed [**Raspberry Pi OS**](https://www.raspberrypi.com/software/) onto my Raspberry Pi, which is essentially a Debian Linux port. 

For the purposes of this guide, I will not go into extreme depth, but the important things are as follows:

1. Make sure you have a large enough MicroSD to be comfortable. I chose a 128 GB MicroSD. For most purposes, this should be fine. You should not be recording video onto the system drive anyways. Save the money and buy a fat external hard drive that can be separately powered with an additional adapter if you want to record video.
2. Be sure to set up a user account and enable SSH under services. You should use an RSA key or some kind of public-key authorization for SSH. **It is strongly advised by me and all other security conscious people not to use password authentication for SSH.** If you do not already have a private-public key pair, you can use the "Run SSH-Keygen" button. The important thing is that you have the corresponding private key in the clients' SSH agent, which should happen automatically using the Imager tool. The public key will be stored on the Raspberry Pi allowing any client which has the private key to securely log into the Pi's shell and operate it via commands. ![SSH settings in Raspberry Pi imager](https://www.raspberrypi.com/documentation/computers/images/imager/os-customisation-services.png?hash=bbc8c0ff2f1eb7207d43180d7694c399)
3. Set a memorable hostname (you will use this to connect, as well as whatever local IP your router assigns to it), and refrain from giving it access to your wireless network. You should probably use Ethernet when making a Raspberry Pi into a smart hub. If it is impossible to use Ethernet (it shouldn't be), then you can use wireless (there is nothing inherently unusable about it per se), but keep in mind smart home operation may/will be a lot less stable (and have more latency).

Once you have configured the OS and the related settings, all you need to do is go ahead and click through, flash the operating system to the MicroSD card, and eject it when done then pop it into the Raspberry Pi.

## 3. Accessories/Hardware

While you can use just the bare Raspberry Pi computer, it can help to buy a few accessories to help its functionality. Of course, you need the aforementioned MicroSD card. But in my case, I also bought a little kit from Vilros that included acrylic top and bottom plates, mounting gear, and the official Raspberry Pi Active Cooler, which mounts directly onto the Raspberry Pi 5 toollessly and is software controlled via a little fan header on the board. I strongly recommend an active cooling solution/case of some kind, especially if you are using cameras/doing anything that will require high spikes of usage. The Active Cooler I bought rarely has to even engage because the system usually can subsist at low temperatures with simple passive cooling, due to the decently sized heatsink on the cooler. So, it operates with very sparse and quiet sound.

I also purchased an external power brick from Argon that uses GaN for ~$12, which should be an efficient and proper spec power supply for the Pi 5. As far as I understand, any USB-C PD charger that can give the proper wattage/amperage (>27W, 5A) should be fine, but your mileage may vary and in the end I just opted to buy a specific power supply for the Pi to be safe. There is also a stock Raspberry Pi one that you can buy separately, but I opted for the GaN one for the increased efficiency (and relatively low upcharge).

There are many so-called "hats" for Raspberry Pi 5 that can extend the functionality of the board and provide you with more I/O and features. These hats can give you M.2 drive capability, allow you to add a Coral TPU (for AI acceleration), an audio DAC, etc. With the basic Vilros test-bench style open "plate-case" you can pretty much mount all the hats and still put the plates back on, but it might be best for you to buy a case that includes some of the I/O / hat functionality you want built-in for a slicker integration. Argon sells some great cases with M.2 slots and beefy active cooling / better I/O built in, but they come at a somewhat significant premium over other solutions and you trade your flexibility for ease of integration.

## 4. Setting up Raspberry Pi OS (Raspbian)

### 4.1 Using SSH

SSH is an essential tool in your toolbox for managing your Raspberry Pi smart hub. It allows you to access the terminal of the Raspberry Pi from other computers on your local network (and remotely, see the guide on remote access later...) and run commands on your Raspberry Pi without having to physically plug in a keyboard, mouse, and monitor. You will have to – gasp! – use the command line to do anything substantial I will outline in this guide. But have no fear, it is really a lot simpler than one may think at first, and I will do my best to give a pertinent crash course here.

#### 4.1.1 SSH Client

There are endless options for SSH clients you can use to "talk with" your Raspberry Pi and open a remote terminal session. The important thing is to ensure your client has the private and public keys you created/assigned to the Raspberry Pi when using the Imager to flash the OS to the MicroSD card. This is so that your SSH client can authenticate with the Raspberry Pi.

I have heard great things about MobaXTerm and PuTTY for Windows. I use Visual Studio Code and the built in SSH support on Mac via WezTerm (but you can also just use default Terminal). On my iPhone, I use the Termius app to connect and use SSH when I'm not even at a full-size computer. But these are all just a few of the options for these platforms that you could use, any SSH client (go Google!) should work in theory.

Once you have an SSH client installed and the correct keys configured with your client, you can access the Raspberry Pi's terminal through your local network via `ssh [hostname].local` or `ssh [local ip of pi]`. When you have remote access configured, you would just use that the gateway IP (whatsmyip dot com public IP) or some sort of dynamic DNS location instead.

### 4.2 Raspberry Pi Command Line Basics

Once you are logged into your Pi via SSH or using the built-in terminal, you can begin to execute commands to manage your Raspberry Pi.

Below is a command you can run that will show you the config settings of your Pi. Many of these can be changed in the graphical desktop, but it is honestly easiest to access them through the command line. Use the arrow keys and enter/escape keys to navigate.

```sh
sudo raspi-config
```

I recommend looking through some of these settings and seeing if it would be conveinent to adjust them. One setting I change a lot is the menu under `System Options -> Boot / Auto Login`, which can make it so the Pi boots with/without going into a GUI environment, and allows one to choose whether auto-login is enabled. Note that if you try to change the Power LED settings, it probably won't work on the Pi 5. You'll have to do that a different way (see section below).

Another useful command allows you to reboot the Pi from the command line, making it easy to reboot the Pi remotely. This will close your connection over SSH, so you will have to log back in later.

```sh
sudo reboot now
```

One thing to know about Debian Linux based systems in general is that they are reliant on the `apt` package manager. You can install packages and delete them using these commands:

```sh
sudo apt install [package-name]
sudo apt uninstall [package-name]
```

You can modify files on the system and edit text using the `nano` text editor. If you want to use Vim or Vi or Emacs... This section is not for you, go ahead and use those. But for the layperson, it is easiest to use `nano` for basic editing from the Linux terminal.

```sh
# Edit the message of the day as root user (message that shows when you launch the terminal)
sudo nano /etc/motd

# Nano can also be run as a normal user by omitting "sudo", but for editing some files, you will need sudo/super user access.
nano text.txt
```

As a general rule, in the context of shell, when looking at keyboard shortcuts (which show up across the bottom when you open `nano`) the `^` character means to hit the control/ctrl key along with whatever letter after it. So, to exit `nano`, you would use `Ctrl-X`. 

### 4.3 Turn off the lights!

If you'd like to disable the (potentially extremely disruptive and annoying) blinking lights on your Raspberry Pi 5, it is possible to do so by editing a firmware config file found at `/boot/firmware/config.txt` (if you are running Raspberry Pi OS). You will need to run a text editor with sudo/root access to edit this file. For a somewhat simple approach, you could run the command `sudo nano /boot/firmware/config.txt` to edit the file from your Raspberry Pi's terminal with the GNU `nano` text editor.

A good guide on this is written by Don at [dky.io](https://dky.io/posts/how-to-disable-the-status-leds-on-a-raspberry-pi-5/). It basically boils down to adding the following lines to the `config.txt` file:

```sh
dtparam=pwr_led_trigger=default-on
dtparam=pwr_led_activelow=off
dtparam=act_led_trigger=none
dtparam=act_led_activelow=off
dtparam=eth_led0=4
dtparam=eth_led1=4
```

If you just want to turn off the ethernet lights, or just the power light, etc, just only include the lines for those specific cases. You don't have to copy all of these options over. In my case, I just turned off the ethernet lights and kept the power LED active (so, I only copied over the last two lines pertaining to each `eth_led`).