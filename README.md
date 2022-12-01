# Linux From Scratch (LFS) for Raspberry Pi 
This repo aims to provide a step-by-step guide in order to build your own custom linux system on Raspberry Pi, completely from scratch. 
#### Why LFS ? 
  1.  Learn how a Linux System works internally
  2.  Building very compact Linux Systems. There are LFS enthusiasts out there who were able to install a system that was just enough to run the Apache web server, total disk space usage was approximately 8 MB. With further stripping, that can be brought down to 5 MB or less. Try that with a regular distribution 👀.
  3.  LFS is extremely flexible. You can literally skip so many plug-ins which you really do not need but they come with regular distribution by default. 
## Prerequisites
In order to build LFS, following are the prerequisites you need:
  1.  Desktop Linux machine (Ubuntu 22.04 LTS in my case)
  2.  Micro SD Card and Card Reader (I'm currently using 32 GB SD card)
  3.  Raspberry Pi 4 (This tutorial will guide you to build a 64-bit Debian dist on RPI 4 but the same applies for RPI 3B)
  Rest of the stuff is required after we prepare our SD card
  4.  LAN cable
  5.  USB-to-C/USB-to-B cable for RPI 4/RPI 3B respectively
  6.  Micro HDMI-to-HDMI/HDMI-to-HDMI cable for RPI 4/RPI 3B respectively
  7.  Monitor with HDMI support and Keyboard 
## Installing VMware
Virtual Machines are better than dual boot system when you have to switch between windows and linux machines
  1.  
