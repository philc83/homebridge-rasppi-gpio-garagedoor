# homebridge-rasppi-gpio-garagedoor
Raspberry Pi Piface IO GarageDoor plugin for [HomeBridge](https://github.com/nfarina/homebridge)

# Circuit
  This plugin assumes that you are using a Raspberry Pi and Piface IO Board to directly control your garage door. Garage Door openers usually have
  a switch on the wall that you can push to open the garage door. On my model, this is just a very simple switch that completes
  a 24vdc circuit. The button must be pressed for about a second before the door will open. In order for this to be an effective
  garage door opener, you need two parts, a relay that will perform the duty of the button, and a reed switch that will
  detect when your garage door is closed.

  ![](https://raw.githubusercontent.com/benlamonica/homebridge-rasppi-gpio-garagedoor/master/images/Close_Sensor.jpg)

  ![](https://raw.githubusercontent.com/benlamonica/homebridge-rasppi-gpio-garagedoor/master/images/Relay_Wiring.jpg)

# Installation

Note: recent Kernel versions need Device-Tree to be disabled in order to use SPI.

- Install NodeJS

Follow the instructions available here:
https://nodejs.org/en/download/package-manager/

(Optional) Update `npm` and `node-gyp`
```
npm install -g npm@latest
npm install -g node-gyp@latest
```

- Download, build and install the C libraries

```
sudo apt-get install automake libtool git
git clone https://github.com/thomasmacpherson/piface.git
cd piface/c
./autogen.sh && ./configure && make && sudo make install
sudo ldconfig
cd ../scripts
sudo ./spidev-setup
```

- Install the PiFace NodeJS module

```
npm install piface

```


- Install Homebridge and homebridge-rasppi-gpio-garagedoor

  0. Choose the Piface IO's that you are going to use 
  2. Install homebridge using: ```npm install -g homebridge```
  3. Install this plugin using: ```npm install -g homebridge-rasppi-gpio-garagedoor```
  4. Update your configuration file. See sample-config.json snippet below. 
  5. Set up Homebridge to start automatically after reboot (with systemd) https://gist.github.com/johannrichard/0ad0de1feb6adb9eb61a


# Configuration

Configuration sample:

 ```
    "accessories": [
        {
            "accessory": "RaspPiGPIOGarageDoor",
            "name": "Garage Door",
            "doorSwitchPin": 23,
            "doorSwitchPressTimeInMs": 1000,
            "doorSwitchValue": 1,
            "closedDoorSensorPin": 24,
            "closedDoorSensorValue": 1,
            "openDoorSensorPin": 25,
            "openDoorSensorValue": 1,
            "doorPollInMs": 4000,
            "doorOpensInSeconds": 14
        }
    ],
```

Fields: 

* name - Can be anything (required)
* doorSwitchPin - The physical GPIO pin number that controls the relay to trigger the garage door
* doorSwitchPressTimeInMs - number of milliseconds to trigger the garage door button. defaults to 1000 millseconds (1 second) if not specified
* doorSwitchValue - 1 = ACTIVE_HIGH, 0 = ACTIVE_LOW, defaults to 1 if not specified. Set to 0 if you have a relay that requires the signal to be 0v to trigger.
* closedDoorSensorPin - The physical GPIO pin that senses if the door is closed
* closedDoorSensorValue - 1 = ACTIVE_HIGH, 0 = ACTIVE_LOW, defaults to 1 if not specified
* openDoorSensorPin - The physical GPIO pin that senses if the door is open
* openDoorSensorValue - 1 = ACTIVE_HIGH, 0 = ACTIVE_LOW, defaults to 1 if not specified
* doorPollInMs - Number of milliseconds to wait before polling the doorSensorPin to report if the door is open or closed
* doorOpensInSeconds - Number of seconds it takes your garage door to open or close (err on the side of being longer than it actually takes)


