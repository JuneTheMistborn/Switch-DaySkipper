# Switch DaySkipper
Switch DaySkipper is code based off of bertrandom's snowball thrower, but modified to skip a given amount of days in the switch system to perform glitches across all sorts of games. **However, note that currently it is tuned to the _mm/dd/yy_ format, I know it's dumb, and I am working on adding the _dd/mm/yy_ format.** The system does not currently work with numbers exceeding 9,223,372,036,854,775,807, but you would die billions of years before it stopped skipping if you set it that high.

### How to use:
#### Setting the # of days you skip
You just have to go into the Joystick.c file, find the variable at the top that is called "wantDaysSkip" and modify that line to:
long wantDaysSkip = (number of days you want to skip);
#### Actually using it
Just navigate to the switch's Date and Time settings under System Settings>System>Date and Time (note that you must first turn off the Synchronize Clock via Internet option in Date and Time). Next, click on the Date and Time option, and ***change the month and day to January 1st***. Finally, navigate to the ok button, and make sure that it is highlighted, plug the controller in (directly to the switch or through the dock) and it should start skipping days.

## Wait, what?
On June 20, 2017, Nintendo released System Update v3.0.0 for the Nintendo Switch. Along with a number of additional features that were advertised or noted in the changelog, additional hidden features were added. One of those features allows for the use of compatible USB controllers on the Nintendo Switch, such as the Pokken Tournament Pro Pad.

Unlike the Wii U, which handles these controllers on a 'per-game' basis, the Switch treats the Pokken controller as if it was a Switch Pro Controller. Along with having the icon for the Pro Controller, it functions just like it in terms of using it in other games, apart from the lack of physical controls such as analog sticks, the buttons for the stick clicks, or other system buttons such as Home or Capture.

The original version of the code that this repo is based off of emulated the Pokken Tournament Pro Pad, but changes have been made to support the HORIPAD wired controller for Nintendo Switch instead. In addition, many additional features/improvements have been added.

### Setup
#### Prerequisites
* A LUFA-compatible microcontroller such as the Teensy 2.0++, Arduino UNO R3, or the Arduino Micro
A USB-to-UART adapter.
* In a pinch, an Arduino UNO R3 with the ATMega328p disabled (connect RESET to GND) will work.
* A machine running Linux or MacOS. Currently there are issues running under Windows.
#### Compiling and Flashing onto the Teensy 2.0++
Go to the Teensy website and download/install the Teensy Loader application. For Linux, follow their instructions for installing the GCC Compiler and Tools. For Windows, you will need the latest AVR toolchain from the Atmel site. See this issue and this thread on GBAtemp for more information. (Note for Mac users - the AVR MacPack is now called AVR CrossPack. If that does not work, you can try installing avr-gcc with brew.)

Next, you need to grab the LUFA library. You can download it in a zipped folder at the bottom of this page. Unzip the folder, rename it LUFA, and place it where you like. Then, download or clone the contents of this repository onto your computer. Next, you'll need to make sure the LUFA_PATH inside of the makefile points to the LUFA subdirectory inside your LUFA directory. My Switch-Fightstick directory is in the same directory as my LUFA directory, so I set LUFA_PATH = ../LUFA/LUFA.

Now you should be ready to rock. Open a terminal window in the Switch-Fightstick directory, type make, and hit enter to compile. If all goes well, the printout in the terminal will let you know it finished the build! Follow the directions on flashing Joystick.hex onto your Teensy, which can be found page where you downloaded the Teensy Loader application.

#### Compiling and Flashing onto the Arduino UNO R3
##### My experience:
Firstly you need to create a folder where you are going to work with this project, then download the above files, and extract them into your premade folder. Then extract the LUFA zip into the folder you created (not the Switch-DaySkipper directory you just downloaded). Next you must download mingw32 from here: http://osdn.net/projects/mingw/downloads/68260/mingw-get-setup.exe/ (it will auto download when you click on the link), then you need to run the file that is downloaded. Go through the installer until you get to the installation manager, then click on the box next to mingw32-base-bin and click the "mark for installation" option, then go to the installation tab in the top left and click "apply all changes" and then apply. After that, go to https://www.arduino.cc/en/Main/Software, then click on the **Windows Installer** and download arduino (you don't have to donate). Then run the file you've downloaded, and go through the installer without changing settings. Next, travel to This PC>Local Disk (C:)>Program Files (x86)>Arduino>hardware>tools>avr and shift right click on bin then press "copy as path". After that, press the windows key (opens search bar on left of taskbar) and type "path", click on the "edit system environment variables" option, then in the menu it brings up, click on "Environment Variables" button on the bottom right, then in the System Variables box click on "Path" and then edit. After you bring up another menu, click on the "new" button and then ctrl+v (paste) into the text box that is brought up. After that make sure to press ok in all the menus, then install Atmel Flip from here: https://www.microchip.com/DevelopmentTools/ProductDetails/PartNO/FLIP.


##### Original instructions:
You will need to set your Arduino in DFU mode, and flash its USB controller. (Note for Mac users - try brew to install the dfu-programmer with brew install dfu-programmer.) Setting an Arduino UNO R3 in DFU mode is quite easy, all you need is a jumper (the boards come with the needed pins in place). Please note that once the board is flashed, you will need to flash it back with the original firmware to make it work again as a standard Arduino. To compile this project you will need the AVR GCC Compiler and Tools. (Again for Mac users - try brew, adding the osx-cross/avr repository, all you need to do is to type brew tap osx-cross/avr and brew install avr-gcc.) Next, you need to grab the LUFA library: download and install it following the steps described for the Teensy 2.0++.

Finally, open a terminal window in the Switch-InputEmulator directory, edit the makefile setting MCU = atmega16u2, and compile by typing make. Follow the DFU mode directions to flash Joystick.hex onto your Arduino UNO R3 and you are done.

#### Compiling and Flashing onto the Arduino Micro
The Arduino Micro is more like the Teensy in that it has a single microcontroller that communicates directly over USB. Most of the steps are the same as those for the Teensy, except do not download Teensy Loader program. You will also need to edit makefile before issuing make. Change MCU = at90usb1286 on line 15 to MCU = atmega32u4.

Once finished building, start up Arduino IDE. Under File -> Preferences, check Show verbose output during: upload and pick OK. With the Arduino plugged in and properly selected under Tools, upload any sketch. Find the line with avrdude and copy the entire avrdude command and all options into a terminal, replacing the .hex file and path to the location of the Joystick.hex created in the previous step. Also make sure the -P/dev/?? port is the same as what Arduino IDE is currently reporting. Now double tap the reset button on the Arduino and quickly press Enter in the terminal. This may take several tries. You may need to press Enter first and then the reset button or try various timings. Eventually, avrdude should report success. Store the avrdude command in a text file or somewhere safe since you will need it every time you want to print a new image.

Sometimes, the Arduino will show up under a different port, so you may need to run Arduino IDE again to see the current port of your Micro.

If you ever need to use your Arduino Micro with Arduino IDE again, the process is somewhat similar. Upload your sketch in the usual way and double tap reset button on the Arduino. It may take several tries and various timings, but should eventually be successful.

The Arduino Leonardo is theoretically compatible, but has not been tested. It also has the ATmega32u4, and is layed out somewhat similar to the Micro.
