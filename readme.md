# Raspberry Pi Cross Compile Environment Setup
* You will be able to:
	* Compile C/C++ source code in your Host PC (Ubuntu)
	* Remote debug the program you created
* Environment:
	* Target: Raspberry Pi 2 Model B (Raspbian)
	* Host: Ubuntu 14.04 (64-bit), Eclipse
	* Language: Userland C/C++

## Note
* You may not need "ARCH=arm" for build command
* You may use gcc-linaro-arm-linux-gnueabihf-raspbian/ directory instead of gcc-linaro-arm-linux-gnueabihf-raspbian-x64 if you are in 32-bit environment
* I assume the IP address of RaspberryPi is 192.168.0.144. You may need to change this
* I assume you will put toolchain in /home/myname/dev/raspberry/tools

## Try simple compile
### Get basic tools on ubuntu
```
ubuntu$> sudo apt-get update
ubuntu$> sudo apt-get install build-essential libncurses-dev git git-core
```

### Get Toolchain for Raspberry pi
```
ubuntu$> mkdir raspberry
ubuntu$> cd raspberry
ubuntu$> git clone https://github.com/raspberrypi/tools
```

### Compile
```
ubuntu$> mkdir mySrc
ubuntu$> cd mySrc
ubuntu$> vi hello.cpp
ubuntu$> ARCH=arm ../tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-g++ hello.cpp
```
[hello.cpp is here](sample/hello.cpp)

### Run on Raspberry Pi
```
ubuntu$> sftp pi@192.168.0.144
sftp> put ./a.out
sftp> quit
```
```
ubuntu$> ssh pi@192.168.0.144
pi$> ./a.out
pi$> exit
```


## Debug on Eclipse
### Prepare workspace on "Raspberry Pi"
```
pi$> mkdir tmp
```

### Download Eclipse CDT
<https://eclipse.org/downloads/>  
Eclipse IDE for C/C++ Developers 64-bit  
* if needed, install Oracle JDK


### Setup Eclipse Project
#### Setup remote configuration (maybe once only)
* Window -> Show View -> Other -> Remote Systems -> Remote Systems
* Right click on Remote System screen -> New -> Connection -> SSH Only
	* Host name = 192.168.0.144
* Check Connection
	* Right click on "Ssh Shells" on the host you created -> connect, then Launch Shell

#### Create New Project
* File -> New -> C++ Project
* C++ Project
	* Project type = Executable -> EmptyProject
	* Toolchains = Cross GCC
	* Project name = Hello # whateber you want
* Select Configurations
	* select Debug and Release
* Cross GCC Command
	* Cross compiler prefix = arm-linux-gnueabihf-
	* Cross compiler path = /home/myname/dev/raspberry/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin

### Add Source file and build
You should add file and build at first anyway to make the following settings easier

* Right click on the project on ProjectExplorer -> New -> File -> hello.cpp
	* [hello.cpp is here](sample/hello.cpp)
* Build for Debug

### Setup debug configuration
* Run -> Debug Configurations
* Double click on C/C++ Remote Application, then "Hello Debug" created
* Main tab
	* C/C++ Application = Debug/hello 	# you will be able to "Search Project" after you add and build the project
	* Connection = 192.168.0.144	# the connection you created ealier
	* Remote Absolute File Path for C/C++ Application = /home/pi/tmp/hello
	* Commands to execute before application = 
		* sudo -i
		* chmod a+x /home/pi/tmp/hello
* Debugger tab
	* GDB debugger = /home/myname/dev/raspberry/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin/arm-linux-gnueabihf-gdb
* Click "Debug"
### Run or Debug
* Debug -> Hello Debug  # Do not choose "Local C/C++ Application"
* Debug perspective will open and break at the first line
* You can also "Run" the application
