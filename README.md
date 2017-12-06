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
* https://help.ubuntu.com/community/RadeonDriver
* https://help.ubuntu.com/community/AMDGPU-Driver
* https://www.x.org/wiki/RadeonFeature/
* https://wiki.archlinux.org/index.php/ATI



  * **Install Extras and Utilities**
      
    ```sudo apt-get install git wget curl libcurl4-openssl-dev clinfo glmark2 linux-headers-generic g++ mesa-common-dev gksu terminator```
  
  * **Install Dependancies**
  
    Update the repositories in the usual fashion
    ```sudo apt-get update```
    ```sudo apt-get install linux-headers-generic```
    

        
  * **Install Legacy FGLRX Driver**
    
    Incase you are like me and you have tried many different times to get these drivers to work.  I recommend working on a fresh install of Ubuntu 14.04 since it really needs to be brought all the way back to 14.04.1 but in case for some reason you need to, any previous installations of FGLRX should be removed with this command.  
    
    ```sudo apt-get purge fglrx*```
    
    Install the FGLRX driver stack and configuration tool.  The AMD control center was rolled into the main FGLRX package in 14.04.
    
    ```sudo apt-get install fglrx```
    
    Install the hardware acceleration packages
    
    ```sudo apt-get install xvba-va-driver libva-glx1 libva-egl1 vainfo```
    
    Once all the driver packages are installed, the GPU(s) need to be initialized with the following command.  This command will create a back-up of your existing xorg.conf which can read using `cat /etc/X11/xorg.conf`.
    
    | HW Configuration | Command |
    |------------------|--------|
    | Single GPU | `sudo amdconfig --initial` |
    | Multiple GPUs | `sudo amdconfig adapter=all --initial`|
    
    ```sudo reboot```
    
  * **Configure Displays and GPUs**
  
    I had some problems with the xorg.conf reverting back to the original for some reason when I followed this procedure and had to repeat the configuration step more than once.  I finally was able to make it work by renaming xorg.conf to something else like xorg.new.conf prior to the reboot then renaming it afterward to xorg.conf.  Not sure why it wouldn't take but the workaround got it done for me.  Without it the AMD control center will not be able to detect the displays.  
    
    Once everything is installed and the initial configuration set through xorg.conf, any other GPUs can be tested using the AMD configuration tool.  It can be launced from the Unity or run from the command line using the gksu package.
    
    ```gksudo amdcccle```
    
    If you are trying to mine a cryptocurrency with this hardware it is unlikely you will want to actually use these GPU for displaying a gui so make sure that they are disabled as outputs.  You can verify the graphics card and drive functionality by running some of the video benchmarking tools like `glxgears` or running `glmark2` (if you want to test at fullscreen resolution use `glmark2 --fulscreen`)
  
### 3. **Setting Up Ethereum Mining Tools**
Before moving on to setting up the mining tools you should verify that the environment is set up properly for OpenCL.  To use ethminer 0.9.0+ you will need to make sure that you are running OpenCL 1.2 or better.  this can be checked by running

    
```clinfo```
    

this output should appear in your output or something went wrong

|      |       |
|------|-------|
|Name:	| Tahiti |
|Vendor: | Advanced Micro Devices, Inc. |
|Device OpenCL C version:	|	OpenCL C 1.2 |

  * **Build and Install CMake**
  
  One of the problems you will run into by using Trusy 14.04.1 is that many of the packages installed from the repos will not be current enough.  As such many of the tools need to be compiled from source.  In particular, CMake is really (circa 3.1.0 i think)  and many contemporary packages require at least 3.4.x.  As of year end 2017, CMake is at 3.10.  This portion of the guide will help you build CMake from source.
  
  In case you don't know (but I'm sure you do), CMake is a cross-compiling tool which is very common in the open source community.  If you build it from source, it will only be able to handle compilation of code with whatever libraries you have install when it is built.
  
  **NOTE:** This guide assumes you are building a mining rig and you have started with a fresh install of Trusty 14.04.1.  If these assumptions are bad in any way, you will need to make sure you have added any library dependancies you may need in order to get CMake to function properly, otherwise you will need to rebuild it again later.
  
  For the purposes of installing the Ethereum tools the dependencies were installed above.  In particular, CMake uses a tool called Hunter which relies on a secure version of cURL to work in particular you need to make sure that you have also installed `libcurl4-openssl-dev` to enable Hunters functionality.  
  
  To start, [download the source code for the latest version of CMake](https://cmake.org/download/) and extract the tar.gz file into a build directory.
  
  ```mkdir ~/CMake Build``` 
  
  (this directory will contain the contents of the extracted tar.gz)
  
  ```cd ~/CMake Build```
  
  ```./bootstrap --prefix=/usr/local --system-curl``` 
  
  (not sure if the additional options are required but it seems to be good pracice)
  
  ```make```
  
  ```make install```
  
  Before you know it you will have a version CMake which is capable of building the Ethereum Tools
  
  * **Build and Install Ethminer**
  Detailed instructions for building Ethminer from source can be found [here](https://github.com/ethereum-mining/ethminer). Start by cloning the Etheminer repository on Git or download the zip for the latest version.  Just like with CMake, create a directory to build it in and move the contents of the zip file into that directory.
  
  ```mk ~/Ethminer Build```
  
  (this directory will contain the contents of the extracted zip)
  
  ```cd ~/Ethminer Build```
  
  ```cmake .```
  
  (this is actually different than the directions on the Ethminer Git Repository.  The instructions there tell you to enter ".." not "." which will ask CMake to look to configure the wrong directory if you have navigated to the build directory as the instruction tell you to do.
  
  ```make --build .```
  
  (Note the single "." again if you get an error referencing the lack of a directory this is your problem)
  
  ```sudo make install```
  
  Even though sudo is used I still had to manually add the ethminer executable to /usr/bin to be able to run it from the command line.  Otherwise you will need to execute it from the directory where it resides
  
  Once it is built and installed you can verify the installation and that it can find the GPUs by using the following command
  
  ```ethminer --list-devices```
  
  The each instance of GPU hardware should appear in the output.
  
  * **Install Ethereum Wallet**
  
  [Download the lates version of Ethereum Wallet/Mist](https://github.com/ethereum/mist/releases).  I would recommend Ethereum Wallet unless you are a developer.  [Here is good explanation of the differnce between Mist and Ethereum Wallet](https://ethereum.stackexchange.com/questions/2690/what-is-the-relationship-between-mist-and-ethereum-wallet).  It should be noted that Ethereum Wallet include Geth which is a command line back end for which Ethereum Wallet uses.  the Ethereum Wallet frontend app is basically just a wrapper and not necessary if you are comfortable working in the command line.
  
  One issue with using the Ethereum Wallet GUI is that the Blockchain Syncing is a very slow process.  This phenomenon is well documented online with much discussion and frustration associated with it.  In my experience, using the Ethereum Wallet GUI did not provide enough feedback on progress and it looked like it was just hanging when in fact, it was working and it takes a LONG TIME.  Like, days as of December 2017.  At the time of writing this guide the block chain was ~4.6+ million blocks.  To make things worse there was an attack on the network in November 2016 which created over 20 million fake accounts.  This slows down syncing to a crawl between blocks ~2280000 - 2370000.  [There is a good explanation here](https://theethereum.wiki/w/index.php/Ethereum_Wallet_Syncing_Problems).  Another note on speed:  Due to the nature of the hash and the sheer volume of drive writes HDD drives are not recommended and I have seen many posts saying it won't work at all.  SSD drives should be used and a 4GB+ swap drive is also helpful.
  
  After trying several times and not really knowing what was going on I decided to use the `geth` commandline tool.  Geth can be used to start syncing in fast mode.  I recommend running this command in `teminator` terminal as it allows for windowed terminals which makes things easier to monitor but obviously any terminal will work.
  
  ```geth --syncmode=fast --cache=1024```
  
  Higher RAM can allow a larger cache=2048 and presumably increase the speed further but I have not tested this.  Once geth is running fast mode can be verified by entering the following command in a separate terminal:
  
  ```ps ux | grep geth```
  
  If syncing is going in fast mode the output will include the --fast flag and also indicate the cache size.
  
  There is not a user friendly way to tell from the Geth output how far along the progress is but geth provides a Javascript console environment which can be accessed by entering this in the command line.
  
  ```geth --attach```
  
  Once in the javascript console these commands can be helpful in interrogating the progress:
  
  ```eth.syncing```        (will output serveral values to show you the current state)
  
  ```eth.syncing.currentBlock```   (Will output the current block being synced)
  
  ```eth.syncing.highestBlock```   (Will output the final block in the chain when syncing was started)
  
  These commands can be access from the BASH commandline using a geth inline command using `geth --exec "[command]" attach`.  Entering the following command in the **command line** will allow you to get a continuously polling progress that gives a % Complete number that is constantly updating.  IMO this give confidence that nothing has hung up and things are progressing as they should which can reduce the frustration of the process.  In a new terminal enter this command:
  
  ```while (( $(geth --exec "eth.syncing.highestBlock-eth.syncing.currentBlock" attach) > 0)); do echo $(geth --exec "100 * eth.syncing.currentBlock / eth.syncing.highestBlock" attach) % Complete; done```

  
  
  * **Benchmark the system and Start Mining**
  
  Be aware that it seems that as of December 2017 it seems that benchmarking OpenCL hardware is broken on Ethminer.  That said it will still reliably give you a very good sense of the expected hash rate but will throw an error after the final trial.  Not reall a big deal and as far as I can tell it does not affect minig.  NOTE: Ethminer can benchmark in a separate terminal while geth is syncing but cannot mine.  In order to benchmark the full system use this command:
  
  ```ethminer -G -M```
  
  The `-G` flag tells Ethminer to use OpenCL GPU computation and the `-M` will put it into benchmarking mode.  For maximum verbosity in output use this command:
  
  ```ethminer -G -M -v 9```
  
  To inspect individual GPUs use this command:
  
  ```ethminer -G -M --opencl-device X```
  
  Where X is the Device ID from the `ethminer --list-devices` output.  this command accepts a comma separated list of devices (e.g. 1,2...).  If you have a mix of NVIDIA and/or AMD GPUs it will be necessary to use the `--opencl-platform` and `--cuda-plaform` and `--cuda-device`.  If you use NVIDIA hardware the `-U` flag is also required instead of the `-G` flag.




