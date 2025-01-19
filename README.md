# Raspberry Pi 5 Smart Home

In the past few months, I have been tinkering around with creating a smart home. I had a few goals with this project, which started simply to have the ability to remotely control the lighting in my apartment, but sprawled into all kinds of concerns including locally hosted remote video. 

This has been a very interesting homelab project that added a lot of tech joy to my life. I'm not trying to sell people on my solution, but I'd like to provide my own experiences and compile some of the solid resources I used throughout this process, in hopes that it helps others in approaching their own solutions. Also, I just really wanted to yap about this topic to my heart's content. "Into the void!"

The solution that I ended up on uses a **Raspberry Pi 5** as a smart home hub, running **Raspberry Pi OS** (Raspbian), with **Home Assistant**, **Scrypted**, **Homebridge** and other utilities through **Docker** and directly through system packages. I use **OpenVPN** and **SSH** to provide ***secure remote access***, as well as **HomeKit/iCloud** *(via a HomePod Mini)* and the **cloud proxy offerings** from Scrypted and Home Assistant.

I decided to set up as much of my smart home through the **Matter** and **Thread** protocols where possible, for easy integration with Home Assistant and HomeKit. For general smart home gadgets like smart plugs and smart bulbs where it was still economically attainable for me I bought Matter compatible variants. But in many cases, Matter-enabled products in the categories I wanted were not economically attainable, which is where services like Homebridge, Home Assistant, Scrypted... etc... that I listed above come in clutch.

## Repository Structure

This repository is organized into Markdown files with descriptive titles based on their specific concern. This README will be the base document providing a general overview, which will include links to the detailed instructions on each individual topic as I write them. It will be an evolving set of documents on the practice I have taken with setting up and maintaining a smart home.

## Specification

My smart home consists of the following components:

### Hardware
  - **Raspberry Pi 5, 4GB of RAM** (~$70)
    - The Raspberry Pi is used as a smart hub which is primarily intended to connect all the pieces of the smart home together. It runs locally managed services which allow one to use their smart home without relying too much on cloud services, Google, Alexa, etc. In my case, it also records my cameras from the network to the hard drive 24/7. With a smaller number of cameras, or just when running smart home software such as Home Assistant or Homebridge, it can be a perfect, high-efficiency and cheap computer to build a locally managed smart home around.
    - I use all of these accessories/components to assist the Pi
      - 128 GB MicroSD card
      - A 4TB external USB hard drive with separate power source from the USB port
      - Vilros Raspberry Pi 5 Active Cooler with acrylic top and bottom plates
      - Argon 27W USB-C GaN Raspberry Pi 5 power supply
      - Raspberry Pi DAC Pro
    - See the [associated guide](RASPBERRY-PI.md) for general usage information in this application.
  - **Apple HomePod Mini** (~$100)
    - For better or for worse, I am an Apple ecosystem user™ and I wanted my smart home to be as functional as possible using HomeKit and Apple services. I tolerate and even enjoy HomeKit and there is really no other way to get a good HomeKit experience than buying an Apple Smart Home Hub. This is totally optional but I will include some information on it throughout these guides.
  - **TP-Link Tapo RGBW Matter-Compatible Smart Light Bulbs *(6pc)*** (~$15/2pc)
  - **TP-Link Tapo Matter-Compatible Smart Plugs *(3pc)*** (~$25/3pc)
  - **Levoit Ve-Sync Wi-Fi Humidifier and Air Purifier** (prices vary)
    - These are relatively affordable and pretty decent quality air quality products. Many of them are compatible with Homebridge and Home Assistant via plugin integration with their cloud management offering, VeSync. They connect to Wi-Fi.
  - **Wyze Cam 3 *(2pc)*** (~$30/ea)
    - These are relatively cheap cameras that are easy to flash with the open-source firmware Thingino to ensure local control of your cameras and provide in-home RTSP streams. They include a night mode (infrared lights/cam) which is compatible with Thingino firmware. 
    - Any camera that provides an RTSP stream would work with the methods I used and will outline in this guide.
  - **Windows Media/Gaming PC**
    - I am lucky to have access to a gaming PC at home that runs Windows and has a decent AIO CPU water cooler, a Ryzen 5600X and a Nvidia GeForce RTX 3070. This is a quite beefy system which I use to game, and to provide access to Jellyfin and my local media to my network and remotely. It has the power to transcode multiple video streams with hardware acceleration, but as a result is very power hungry and thus, not a very good candidate for an "always-on" smart home hub.
    - I am including it here as I have integrated it with my smart home ecosystem using Wake on LAN and network shutdown functionality. I can turn the PC off remotely to save power when my remote streaming service (via Jellyfin) *is not* needed, and power it on remotely when *it is* needed.
  - **Tuya IR Blaster** (~$5)
    - I use this to provide my smart home access to controlling my old TV receiver (a Sony STR-DN1030) that I don't want to replace. It allows me to turn up and down the volume on receiver, power it on, change input, etc. either remotely or just when I don't want to get my ass up to find the remote.
    - The main reason I bought this specific model of IR blaster is that it is extraordinarily cheap (~$5). There are certainly better models that may be easier to integrate into your solution (for instance, I have heard great things about the Broadcom RM4). But so far this one has been fine, for my uses.
  - **Tuya MT29 Air Quality Sensor** (~$30)
    - This is an air quality sensor that provides data to Tuya, which can be then integrated with other services. It also has a small screen and display to show the data directly. I used this to add automatic functionality to my air purifier which doesn't have a sensor built-in.
    - They are also pretty cheap which is again why I bought one.
    - ***Sidenote**: I experimented with a cheaper "Tuya Air Housekeeper" model. It would be my advice not to even waste your money or time on these specific models, they provide very slow updates and bad data. They work, but only on technicality...*

### Software
- Via Docker containers on the Raspberry Pi...
  - [**Scrypted**](CAMERAS-AND-NVR.md) with NVR and HomeKit plugins
    - Scrypted NVR capability is either $15/3mo, or $40/yr.
  - **Home Assistant**
  - **Homebridge**
  - **Portainer.io**
    - This allows me to manage my running Docker containers via a web interface.
  - **OpenVPN Server**
    - This allows secure, remote access to the local network, minimizing the number of ports and services you need to expose.
- **Thingino** open-source RTSP camera firmware
- **Apple Home** and **HomeKit** for easy management via Siri and my Apple devices.

---

> The guides in this repository are under MIT License and thus are permissible to copy, share and modify in any way and use for educational purposes. Any external links or resources referenced have their own licenses and copyright.

*Please stay tuned to this repo for more information as I continue to work on the writeups!*