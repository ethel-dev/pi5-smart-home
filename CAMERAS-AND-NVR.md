# Cameras and Network Video Recorder (NVR)

<!-- TOC -->

- [Cameras and Network Video Recorder (NVR)](#cameras-and-network-video-recorder-nvr)
  - [1. Thingino Open Source RTSP Firmware](#1-thingino-open-source-rtsp-firmware)
  - [2. Scrypted NVR (Network Video Recording) and Smart Home Integration](#2-scrypted-nvr-network-video-recording-and-smart-home-integration)
    - [2.1. External Storage for Recordings](#21-external-storage-for-recordings)
      - [2.1.1. Setting up external storage in your Scrypted Docker container after installation](#211-setting-up-external-storage-in-your-scrypted-docker-container-after-installation)
    - [2.2. Installing Scrypted with Docker to your Raspberry Pi](#22-installing-scrypted-with-docker-to-your-raspberry-pi)
    - [2.3. Installing Scrypted with Proxmox VE](#23-installing-scrypted-with-proxmox-ve)
    - [2.4. Setting up your cameras and smart home integrations with Scrypted](#24-setting-up-your-cameras-and-smart-home-integrations-with-scrypted)

<!-- /TOC -->

##  1. Thingino Open Source RTSP Firmware

As a privacy-conscious person, I did not want to use any cloud solution that didn't work from a personally hosted server which I control for my cameras. Because of this, I decided to flash my Wyze Cam 3 cameras with [Thingino](https://thingino.com/) open-source firmware. It does not "phone home" anywhere, and works entirely locally on the devices and within in my home network. 

The cameras expose standard [RTSP](https://en.wikipedia.org/wiki/Real-Time_Streaming_Protocol) streams into the local network which can be accessed by the smart hub (Raspberry Pi) and associated software for recording/detections (Scrypted).

I picked the Wyze Cam 3 cameras with Thingino in mind as they are easy to flash with the third-party firmware using only a Micro SD card. Many other cameras are compatible with Thingino and other open source/hacked firmwares which better ensure local operation, privacy and secure.

There is a [guide](https://thingino.com/wyze-c3) (as well as downloads) for how to flash the Wyze Cam 3 cameras (and many other models) with the Ingenic SoC on the [Thingino website](https://thingino.com/) and [project wiki](https://github.com/themactep/thingino-firmware/wiki). For the Wyze Cam 3 cameras, it is such a simple process that I won't detail it too much here. One important detail is that the specific download for your specific SKU of the camera is located as the links under the camera's section on the Thingino homepage.

Once you have Thingino flashed it is a simple process of connecting to the dummy Wi-Fi network that the camera creates, providing it your Wi-Fi details and doing some basic setup via a captive portal. Triple-check the Wi-Fi details before confirming or else you may need to re-flash the firmware (speaking from experience). Note down the hostnames that are provided by the cameras and the RTSP link portion of the `mpv` command provided onscreen when you go to `[camera-hostname].local` in your browser and log into the cameras. This will be used later in Scrypted.

![RTSP stream link from Thingino](https://i.imgur.com/a5T6KsZ.png)

Thingino has a great featureset allowing you to change the streamer settings for the cameras locally and take some transcoding load off your NVR system under `Settings -> Streamer`. It is a good idea to set the cameras to a reasonable bitrate and resolution from the jump, especially if you are not using a more powerful NVR solution. Here are my "sweet spot" settings for the streamer with the Raspberry Pi and Scrypted streaming/recording.

![Thingino Wyze Cam 3 streamer settings](https://i.imgur.com/OQgo2yN.png)

Another neat screen in `Settings -> Streamer` is the OSD settings, which allow you to change the display of the date format and related settings for on-screen printout on the camera files. Note that many cameras will have both a main stream and a "substream." The substream is usually lower quality and used for lower bandwidth needs. In my case, I disabled the substream and put all the power into the main stream.

Any camera that has firmware that works locally providing your LAN with RTSP streams will work with the following NVR setup process. Many cameras that do not necessarily natively provide RTSP streams can be hooked up over various means to Scrypted, so look into plugins/integrations for prospective camera manufacturers and do your own research on that if needed. It is a good chance that if you already have some kind of cameras in your home, you may be able to liberate their functionality from the cloud via some means.

##  2. Scrypted NVR (Network Video Recording) and Smart Home Integration

I used [Scrypted](https://www.scrypted.app/), which is a local solution for NVR and smart home camera integration. The NVR portion is a paid subscription, but I found it to be affordable at $15 every 3 months. For this guide, I assume you are already running Raspbian (Raspberry Pi OS) on your Raspberry Pi, which can be flashed onto a MicroSD via the easy to use Raspberry Pi Imager app that you can install on another computer.  See my other guide on [general Raspberry Pi setup](RASPBERRY-PI.md) for more details. 

It's worth mentioning, this guide uses **Docker Compose**, so truthfully it could probably be done on just about any supported target system running Linux (perhaps with some modifications, YMMV).

###  2.1. External Storage for Recordings
 
If you can, it is best to have external storage already set up for the video recording and plugged into/formatted to the [ext4](https://en.wikipedia.org/wiki/Ext4) filesystem ***before installing*** Scrypted for ease of setup. For Scrypted to work, there are different tiers of storage you will need to attach based on the number of cameras you have. By default, it needs enough for at least 3 days of continuous recording.

| Resolution | 4 Cameras | 6 Cameras | 10 Cameras | 16 Cameras |
| :--------- | :-------- | :-------- | :--------- | :--------- |
| 1080P      | 1TB       | 1TB       | 1.5TB      | 2.4TB      |
| 2K         | 1.2TB     | 1.8TB     | 3TB        | 4.8TB      |
| 4K         | 2.4TB     | 3.6TB     | 6TB        | 9.6TB      |

> source: https://docs.scrypted.app/scrypted-nvr/recording-storage.html

####  2.1.1. Setting up external storage in your Scrypted Docker container after installation

If you don't have external storage at the time of installing Scrypted in a Docker container, and are in the process of setting it up later, *you will have to specially reconfigure the Scrypted Docker container to have access to whatever external volume you are using.* Luckily, this is something the Scrypted devs have made rather easy. There is a [great guide](https://docs.scrypted.app/scrypted-nvr/storage/docker.html) on the process of setting up a new volume for Scrypted via Docker provided in the [Scrypted Docs](https://docs.scrypted.app/). See the [`New Disk`](https://docs.scrypted.app/scrypted-nvr/storage/docker.html#new-disk) section in that guide, which will even also format the disk properly for use with Scrypted for you.

###  2.2. Installing Scrypted with Docker to your Raspberry Pi
Once you have RTSP streams rolling on all of your cameras in network, it is possible to set up Scrypted. I used the [`Linux - Docker` setup guide](https://docs.scrypted.app/install/linux-docker.html) for Scrypted. The commands provided at the top of the page are an easy, one-stop place to install Scrypted and Docker (if needed) to your Pi.

```sh
# download the installation script into your user home folder as "install-scrypted-docker-compose.sh"
curl -s https://raw.githubusercontent.com/koush/scrypted/main/install/docker/install-scrypted-docker-compose.sh > ~/install-scrypted-docker-compose.sh 

# run the installation script you just downloaded as super user, will ask for password
sudo SERVICE_USER=$USER bash ~/install-scrypted-docker-compose.sh

# optionally remove the script
rm ~/install-scrypted-docker-compose.sh
```
> source: https://docs.scrypted.app/install/linux-docker.html, comments added by me for clarity

Use SSH or just the GUI interface terminal through HDMI or remote desktop and run the commands to have the Pi's shell run the provided script and walk you through installing Scrypted via Docker. If you don't know anything about Docker, maybe check back later for a guide on Docker as it pertains to basic use for this project in this repo.

###  2.3. Installing Scrypted with Proxmox VE

It is recommended by the Scrypted devs to use Proxmox VE instead of Docker to run Scrypted, for reasons outlined in [this section of the installation guide](https://docs.scrypted.app/installation.html#proxmox-ve-vs-docker). There is a [guide](https://docs.scrypted.app/install/proxmox-ve.html) here which details the process of setting up Scrypted with Proxmox VE and installing drivers to allow use of the Coral TPU, etc. 

One should be able to use Proxmox VE with the Raspberry Pi instead of Docker and will probably have a better experience, at least, like I said, according to the Scrypted devs. It seems like there may also be issues with Scrypted interacting with Homebridge or other applications that need host networking services when run inside Docker containers (in my case, I run Homebridge as a system package since it is the lightest and easiest thing to run in that fashion and have had no issues at all).

Nonetheless, I went with Docker as there is [still support for the Coral TPU](https://docs.scrypted.app/detection/object-detection.html#tensorflow-lite) if I get one in the future. Plus, I am just more familiar with Docker as a platform / not particularly bothered by the drawbacks of using it with Scrypted.

###  2.4. Setting up your cameras and smart home integrations with Scrypted

> To be continued...