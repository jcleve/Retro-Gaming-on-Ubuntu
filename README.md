# Retro-Gaming-on-Ubuntu
A comprehensive step-by-step guide to building a dedicated retro gaming system on Ubuntu  
This guide includes instructions on installing Retroarch and Emulationstation, configuring the software, connecting controllers, and more
## Required/Suggested Materials
* A computer running Linux-Ubuntu
  * Single board computers work great if you don't want to use your desktop or laptop
  * I used the Nvidia Jetson TK1 because it has a powerful graphics processor and I got it at a discounted price
* HDMI Monitor
* USB Hub w/ External Power
  * You will need several USB ports, and many single board computers only have one or two ports that often do not carry power
* USB Mouse and Keyboard
  * If you use a wired mouse and keyboard, this is where you will need the powered USB ports
  * Alternatively, you can use a wireless USB mouse/keyboard since they run on batteries
* USB controller
  * Options include an NES Gamepad, N64 Controller, or PlayStation Controller
  * DO NOT use a GameCube controller with a USB adapter. It is very difficult, if not impossible to configure this controller to work with the operating system and the emulation software
* Internet Connection
  * Many single board computers require a Wi-Fi dongle for wireless internet, or you can use Ethernet
* Two or Three-Way Power Splitter/Extension Cord
  * With several components that require AC power, it might be best to have a power splitter so you only have one thing to plug into the wall
* Additional Storage
  * If you plan on downloading a lot of ROMs, you might want some storage in addition to what your computer has. This can take the form of a USB drive, SD card, or SATA hard drive

## Configuring External Storage (Optional)
#### Mounting The Device
If you permanently leave a storage device plugged in, Ubuntu may detect but not mount the device at startup. For example, the device will mount the first time you open it in the file browser, but when the system is up and running, you will not manually mount the device.  

1. Open a blank Text Editor document  
2. In Terminal, enter `sudo blkid` and enter your password
3. Find your device in the list and copy `UUID="xxxxx"`  
4. Paste this line in the Text Editor document, and be sure to remove the quotes so the document reads `UUID=xxxxx`  
5. In Terminal, enter `cat /proc/mounts`  
6. Find your device and copy everything starting with `/media/...`  
7. With a space after the UUID, paste this in Text Editor  
8. In Terminal, enter `sudo gedit /etc/fstab`  
9. On a new line, append `# Storage` just as a "bookmark" of sorts  
10. On a new line, paste the line from your gedit document, save, and restart your system for good measure.  
11. Save your changes

Note that it is critical that this line is written correctly, as your device may not boot properly if there is an error  

Also note that if you are not seeing your device with the `sudo blkid` and `cat /proc/mounts` commands, your device may not be mounted. To mount, open it in the file browser.

For a more comprehensive tutorial, see [this](https://www.youtube.com/watch?v=mPDhoYNao1o) video.

### Creating An Alias
Emulationstation requires the ROMs to be located in your home directory or a subfolder, so you will need to create an alias for your device.

In Terminal, you will enter a command with this format:  
`sudo ln -s /path/to/original /path/to/alias`
For example:
`sudo ln -s /media/ubuntu/storage /usr/ubuntu/` where `ubuntu` is your user name

Now there will be an alias to your storage device in the home folder, so Emulationstation can access the device with the path `/usr/ubuntu/storage`

## Installing Retroarch with PPA
RetroArch is a multi-system emulator that acts as a front end for the libretro API. It will be the base of our gaming system, and it is easiest to install with ppa in Terminal

1. In Terminal, enter `sudo add-apt-repository ppa:libretro/stable` and enter your password if necessary
2. In Terminal, enter `sudo apt-get update`
3. In Terminal, enter `sudo apt-get install retroarch retroarch-assets retroarch-joypad-autoconfig libretro*` to install the necessary support and cores
  * Alternatively, you can enter individual cores instead of `libretro*`

For more information, see [this](http://libretro.com/forums/archive/index.php?t-1556.html)

## Building and Installing Emulationstation
Emulationstation is a graphical front-end for RetroArch that allows you to access all of your games using just a controller. Since Emulationstation has not been officially released for Ubuntu, we will need to compile the source code.

1. Install dependencies in Terminal with `sudo apt-get install libsdl2-dev libboost-system-dev libboost-filesystem-dev libboost-date-time-dev libboost-locale-dev libfreeimage-dev libfreetype6-dev libeigen3-dev libcurl4-openssl-dev libasound2-dev libgl1-mesa-dev build-essential cmake fonts-droid`
2. Download the source code
  * Go to [http://emulationstation.org/#download](http://emulationstation.org/#download) and click "Browse on GitHub"
  * You can either click "Download ZIP" at the top and save the file somewhere on your computer, or you can clone the repository using git
  * To clone the repository, first install git in Terminal using `sudo apt-get install git`
  * Next, copy the HTTPS link next to the "Download ZIP" button, and in Terminal, enter `git clone HTTPS`, but paste the link in place of `HTTPS`
3. In Terminal, enter `cd your/emulationstation/directory`
4. In Terminal, enter `cmake .`
5. In Terminal, enter `make`. This step will take a while
6. Lastly, in Terminal, enter `sudo make install`

Note that if you are having issues with the commands `cmake .` and `make`, you may need to operate with root permissions using `sudo`

## Configuring Emulationstation
Not that Emulationstation is installed, it needs to be configured.

1. First, you need to open Emulationstation. You can do this in Terminal by entering `emulationstation` or by searching your applications.
  * If the Terminal command is not working, it is possible that you didn't install the program correctly. Try running `sudo make install` again from inside the Emulationstation folder, and restart your system for good measure.
  * If you're still having problems, make sure Emulationstation is executable. Open the Emulationstation folder in your file browser, and find the item called emulationstation. Right click and view properties, and make sure `Allow executing file as program` is checked under the Permissions tab
2. Close Emulationstation. The first time you open, a configuration file is created which we now need to edit
3. In Terminal, enter `sudo gedit ~/.emulationstation/es_system.cfg`
4. You will need to set up the configuration file with the folowing format
  ```
  <systemList>
  <system>
  <name></name>
  <fullname></fullname>
  <path>~/ConsoleGames/</path>
  <extension></extension>
  <command>%ROM%</command>
  <platform></platform>
  <theme></theme>
  </system>
  </systemList>
  ```
  * You can find an example configuration file for download as part of this project.  
  * Note that you will need a `<system></system>` block for EACH system you plan to emulate. Follow the example file if you are having trouble. Most importantly, make sure you have the right file extension for the ROMs each system will read, that you have provided the correct path for the ROMs, and that you have linked to the correct path for the emulator itself.
  * Note that Emulationstation will crash if the wrong filepath is provided in the configuration file, especially for ROMs. This is where you will use the alias you created to your external storage, as the path you provide should be a subdirectory of home.  

5. Save your changes

Note that you need at least one ROM to run Emulationstation. I suggest you keep the ROMs for each system in separate folders just to stay organized. In your configuration file, you will need to link to the correct file path for each system.  
For more, see [this](http://quidsup.net/tutorials/?p=retrogame)

## Connecting Controllers
At this point, you can actually start playing games with your keyboard, but if you want to use a controller, there is some setup involved.

