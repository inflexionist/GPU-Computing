# This Guide is Currently Under Development
I appologize for the incompleteness of this guide for those of you struggling to get this working.  I am trying hard to get it done.

# GPU-Computing On Ubuntu with Legacy Hardware
This guide presents information on setting up Legacy AMD hardware and drivers on Ubuntu for GPU computing and Cryptocurency Mining and collects what I feel are the best links for relevant and accurate information based on my experience.

Specifically, this guide is written for installing multiple AMD Tahiti HD 7970 (R9 280) GPUs for use in GPU computing and Ethereum Mining on Ubuntu 14.04.  Although I have only tested this hardware and driver stack with the named GPUs I believe it will work for all Southern Islands graphics cards and other models with GCN 1.0 architecture.  I believe newer GPU hardware is currently compatible with the proprietary AMDGPU-PRO drivers from AMD which also supports OpenCL 1.2 (Required for new GPGPU applications like Ethminer).  If you have anything newer than GCN 1.0 architecture or your driver is otherwise compatible with the proprietary AMDGPU-PRO driver I would recommed using that as it will save a lot of trouble.

I spent many hours trying to get the GPU hardware working on Ubuntu 16.04 and was able to have some limited success but was not able to establish an OpenCL environment which was compatible with many required GPGPU computing tools.  That said, it is generally understood that AMD does plan to support the HD 7900 series Souther Islands (SI) hardware and other currently unsupported GCN 1.0 boards with the AMDGPU-PRO driver eventually.  At that point, this guide will require updating and will likely be obsolete.  As of December 2017 the latest proprietary AMD driver is v17.40 and releases have been coming out approximately every 3 months.  

A review of the [open source Radeon Driver Feature Matrix](https://www.x.org/wiki/RadeonFeature/) shows that the remaining feature development for the Souther Islands (SI) family is mostly complete with the notable standouts being the CrossFire feature which may be important to gamers still being listed as TODO and OpenCL listed as work in progress.  This leads me to believe that the incoporation of this family of boards is on the horizon and may be available sometime in the first half of 2018.  Obviously, this is beginning to cut it close as support for 14.04 will end in early 2019.  

Another note I would like to make is that this guide is meant to establish an enviroment which can be used by software which is currently running with OpenCL 1.2 (The Tahiti boards should support OpenCL up to v2.0 but this guide does not get there yet).  If your application only requires OpenCL 1.1, you can use the open source Radeon driver that come pre-installed on Ubuntu 16.04 and enjoy the pleasure of working on the most current LTS.  As of December 2017 the open source drivers on Ubuntu 16.04 do not support OpenCL 1.2 even though the open source AMDGPU driver is supposed to (based on feedback from the community) but this will likely get fixed.

## Requirements
Beyond the obvious requirements of needing Hardware, you will have to make sure that the system you build has an appropriate power supply to drive the number of GPUs that you plan to install.  You will also need a substantial hard drive due to the large size of the Ethereum Blockchain.  I also believe that Ethereum also requires GPUs with more than 2GBs of memory which the Tahiti HD 7900 (R9 280) has.

## Building a GPGPU System on Ubuntu 14.04

In order to get OpenCL 1.2 to run on my system, I needed to go all the way back to Ubuntu 14.04.1.  My understanding is that this is due to changes in the X server and the Kernel which eventually became incompatible with the proprietary FGLRX driver as the kernel got updated. 

### 1. Setting Up the System

  * **Build Your Computer**

    Despite what is written online, I was not able to install Ubuntu 14.04 with all the GPUs installed from the start.  My Motherboard does not have integrated graphics so I had to install one of the GPUs but adding any more resulted in Black Screen when trying to install from USB drive.  If you have integrated graphics or a cheap video card that you plan to use for your display I recommed using that first with no GPUs installed until the OS is running.
  
  * **Install Ubuntu 14.04.1**

    ISO images of older versions of Ubuntu 14.04 can be found at the following URL http://old-releases.ubuntu.com/releases/trusty/.  NOTE: if you are planning on mining Ethereum, GETH is a requisit component and does not have 32-bit support.  As such, from this point on, this guide will assume you are working on a 64-bit system.
    
    Once the ISO image has been downloaded, make an installation USB drive by using the Startup disk utility of your choice.  As this is a topic that is well covered on the internet I will not provide direct instructions but if you have access to another Ubuntu desktop you can follow this video to learn how to use the [Start up Disk Creator Tool](https://www.youtube.com/watch?v=aVmz-hyzL4E).
    
    Once the USB drive is ready you can begin the installation process by making sure the computer is turned off, inserting it into your system and setting the bios to boot from USB.  Once the Start Up Disk is loaded, you can follow the instructions to install the OS.
  
  * **Install GPU Hardware**
  
    Once the OS is running, you are able to finish installing the hardware on the computer.  Shutdown the computer, remove the Start Up USB and install the GPUs.  Do not hook them up to displays to avoid any drivers trying to detect displays and causing X Sever problems.  The system should boot up normally but the GPUs will not be recognized yet because the driver stack needs to be installed. 
    
### 2. **Setting Up the GPGPU Tools**

With the system completely built and the OS installed, the next step is to beginning adding the required software components necessary to run the GPUs and build the computing environment.  There are many website providing instructions on how to install the FGLRX drivers but these were the ones I found most useful.  Hopefully this saves some time.  I have taken the most streamlined approach I could find to get you to the end point but the procedures came from these resources.

* https://help.ubuntu.com/community/BinaryDriverHowto/AMD


  * **Install Extras and Utilities**
  Install git wget curl libcurl4-openssl-dev clinfo glmark2 linux-headers-generic g++ mesa-common-dev
  
  
  * **Install Dependancies**
  * **Install Legacy FGLRX Driver**
  * **Configure Displays and GPUs**
### 3. **Setting Up Ethereum Mining Tools**
  * **Build and Install CMake**
  * **Build and Install Ethminer**
  * **Install Ethereum Wallet**
  * **Benchmark the system and Start Mining**

## Building a GPGPU System on Ubuntu 16.04
