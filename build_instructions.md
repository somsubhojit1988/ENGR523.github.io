# Instructions to build applications locally

Particle provides the flexibility of building user applications using
a number of ways. For example the web IDE was already shown in the
class. The particle CLI tool can also be used to build applications
online and then download the binaries.

This documentation lists out a process to build user applications
locally against the complete device firmware source, which is open
sourced and available on [Github.](https://github.com/particle-iot/device-os) This provides the additional
flexibility of modifying the firmware code. It uses the ARM GCC
tool-chain for building the entire source code tree.


<a id="orgc331188"></a>

# Creating a project

A project is a container for all the source code that you would be
running on the Argon device, and Particle has made life a lot easier
by providing utilities to create one!


<a id="orgf4d400b"></a>

## Cloning particle's device firmware

Device firmware is the piece of software that is putting everything
together. It contains all the necessary drivers, a RTOS
implementation and exposes a neat set of APIs which makes
application development a much simpler task.
Particle device firmware can be cloned using the following set of
git commands.

    git clone https://github.com/particle-iot/device-os.git
    cd device-os
    git checkout v0.8.0-rc.27
    git submodule update --init

The shared Virtual image(VM) contains a fresh checkout of the
device firmware and is present in ~/workspace/device-os/

Application projects can be created using the particle CLI
tool. The command for which is as below.

    particle project create

The virtual image contains such a project and is present in
~/workspace/blinky
Please refer to Particle's [documentation](https://docs.particle.io/tutorials/developer-tools/cli/#working-with-projects-and-libraries) for further details. 


<a id="org9832710"></a>

## Project structure

There's a certain structure that is adhered to by Particle, and
`particle project create`, sets up this basic scaffolding.The
directory structure for a project, created with `particle project
   create` is such - (shown for the "blinky" example present in the VM)

    ├── inc
    │ └── utils.h
    ├── project.properties
    ├── README.md
    └── src
     ├── blinky.cpp
     └── build.mk

Please note: the inc directory is not created by the `particle
   project create` command, and has been created separately to keep a
cleaner code. (It's a healthy practice to keep the header files
separate from the source code, and any user defined header is meant
to go inside this directory, as has happened with "Utils.h").

"blinky.cpp" is the main source file for the application created
for this demonstration. It just sets up the led ports in `setup()`,
turns the leds on/off at regular intervals in the `loop()`
function, which were shown in the class.

Here is the code for "blinky.cpp", please feel free to use this as
a starting point and experiment by modifying it.

    #include "Particle.h" // A necessary header inclusion
    // Defining 2 int variables which references the D0 and D7 pins.
    int led1 = D0;
    int led2 = D7;
    // This will run the device in manual mode, and not connect to the
    // wifi, Particle cloud by default.
    SYSTEM_MODE(MANUAL);
    
    // setup() runs once, when the device is first turned on.
    void setup() {
    	// Put initialization like pinMode and begin functions here.
    	pinMode(led1, OUTPUT);
    	pinMode(led2, OUTPUT);
    }
    
    // loop() runs over and over again, as quickly as it can execute.
    void loop() {
    
    	// To blink the LED, first we'll turn it on...
    	digitalWrite(led1, HIGH);
    	digitalWrite(led2, HIGH);
    
    	// We'll leave it on for 1 second...
    	delay(1000);
    
    	// Then we'll turn it off...
    	digitalWrite(led1, LOW);
    	digitalWrite(led2, LOW);
    
    	// Wait 1 second...
    	delay(500);
    	// And repeat!
    }

The above example is very conveniently borrowed from the blinky
example that is also available on the web IDE.

The make file is named build.mk (by default and can be changed if
needed).It is to contain the following definitions prior to any
other custom additions.

    INCLUDE_DIRS += $(SOURCE_PATH)/$(USRSRC)  
    CPPSRC += $(call target_files,$(USRSRC_SLASH),*.cpp)
    CSRC += $(call target_files,$(USRSRC_SLASH),*.c)
    APPSOURCES=$(call target_files,$(USRSRC_SLASH),*.cpp)
    APPSOURCES+=$(call target_files,$(USRSRC_SLASH),*.c)
    INCLUDE_DIRS += $(SOURCE_PATH)/inc
    
    ## Any additions are more than welcome beyond this point!

Please refer to the [documentation](https://docs.particle.io/support/particle-tools-faq/local-build/#including-additional-header-directories) for further details.


<a id="orge948ec4"></a>

# Building the project


<a id="org5d24b8a"></a>

## To build the project

The VM instance shared has a certain directory structure, the home
directory of engr523 contains a sub-directory called workspace which
has been set up for the convenience of first time use. Please start
by moving into the workspace directory using `cd ~/workspace/`.

    cd ~/workspace/device-os/main/
    # to build the blinky application
    make clean all PLATFORM=argon APPDIR=../../blinky

`APPDIR` and `PLATFORM` in the above command are environment
variables that is used by make. As you might have already figured
out `PLATFORM` states the platform that we are building for (which
is "argon" for our purpose), where as `APPDIR` points to the user
application that we are trying to build("blinky" for this
demonstration).

Please feel free to go through Particle's build documentation on
[github](https://github.com/particle-iot/device-os/blob/v0.8.0-rc.27/docs/build.md#quick-start) as there are a lot of customization that is possible with
the Particle build system. *Something that will come in handy at a
later point in this semester.*

It is wise to do a clean build for the first time and subsequently
the `clean` option can be dropped from the above make command to do
incremental builds, which reduces the build time significantly.
After a successful build there are 2 things created

-   a system image which is the device firmware and contains the RTOS,
    drivers.. etc
-   a binary of the application ("blinky" for this demo).


<a id="orgbb191f0"></a>

## Getting your code to run on the Argon device

The make files provide a handy utility option `program-dfu` to
build and subsequently flash the system-image, application binary
into the argon device at the same time. In order to do this add the
`program-dfu` option to the previously used build command.

    # to build and then flash the blinky app
    	make all program-dfu PLATFORM=argon APPDIR=../../blinky

This will flash the built image and the application binary through
DFU. Prior to executing this please ensure the **device is set to
DFU mode(blinking yellow led)**.


<a id="orgbd22de5"></a>

# Note

Please email/contact Subhojit(susom@iu.edu) in case:

-   You are facing any issues with the setup/building
-   You feel this document is all gibberish and does not make any sense at all!
-   This documentation is missing something or all this could be better arranged

If you don't want to limit yourself to the VM that is shared, please
feel free to browse through Particle's [documentation](https://docs.particle.io/support/particle-tools-faq/local-build/) for setting up
a local build environment and you will get to know where I did get
ALL this from!


<a id="org434c676"></a>

# References

-   Particle [documentation](https://docs.particle.io/support/particle-tools-faq/local-build/) for setting up a local build environment
-   [github](https://github.com/particle-iot/device-os/blob/v0.8.0-rc.27/docs/build.md#quick-start) build doc page for particle firmware
-   Particle community [post](https://community.particle.io/t/locally-building-firmware-for-the-argon-and-xenon-boards/46765).

