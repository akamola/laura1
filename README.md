# laura1

*laura1* is a LoRaWAN prototype with an LoRaWAN gateway based on Raspberry Pi and an LoRaWAN node based on Arduino.

## Hardware

### LoRaWAN Gateway

- Raspberry Pi 3 Model B
- [Dargino LoRa/GPS HAT](http://www.dragino.com/products/lora/item/106-lora-gps-hat.html)
- 16 GB MicroSDHC Card

### LoRaWAN Node

- Arduino Uno
- [Dargino LoRa/GPS Shield](http://www.dragino.com/products/lora/item/108-lora-gps-shield.html)

## Setup LoRaWAN Gateway

- [Download Raspbian Lite](https://www.raspberrypi.org/downloads/raspbian/)
- [Install it on the SD card](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)

For the next steps of the Raspberry Pi configuration you need a keyboard and monitor with HDMI cabel or an HDMI adapter.

- Plug in the power cabel to start the Raspberry Pi
- Login with the [default user and password](https://www.raspberrypi.org/documentation/linux/usage/users.md)
- Configure the Raspberry Pi with `sudo raspi-config`:
  - For all details [see the documentation](https://www.raspberrypi.org/documentation/linux/usage/users.md)
  - Recommended settings:
    - Change default password
    - Expand filesystem
    - Change language, timezone and/or keyboard layout (localisation)
    - Enable SSH (for remote access)
- Connect the Raspberry Pi via LAN or [setup WiFi](https://www.raspberrypi.org/documentation/configuration/wireless/wireless-cli.md)

If the Raspberry Pi is now connected via LAN or WiFi an SSH is activated you don't need the keyboard and monitor anymore.

Pro tip: Assign the Raspberry Pi a static IP address on your router within your network based on it's LAN or WiFi MAC address.

The Raspberry Pi is now pre-configured and ready to use for _all_ purposes. But to use it as a LoRaWAN gateway some more steps are required:

- Shutdown the Raspberry Pi: `sudo shutdown -h 0` and remove all cables
- Put the Dragino LoRa/GPS HAT on the Raspberry Pi
- Restart the Raspberry Pi (if you have LAN/WiFi and SSH enabled you only need to plug the power cable in)
- Install git: `sudo apt-get install git`

Now you have to decide if you want to setup a experimental *Local LoRaWAN Gateway* or a *The Things Network Gateway*.

### Local LoRaWAN Gateway

For testing purposes you can use the small program `single_chan_pkt_fwd` that receives LoRa packages and forward it to a server (that could be your computer).

- Install WiringPi first: `sudo apt-get install wiringpi`
- Download `single_chan_pkt_fwd` via: `git clone https://github.com/tftelkamp/single_chan_pkt_fwd`
- Go into it's directory: `cd single_chan_pkt_fwd`
- Edit the `main.cpp` to fit your configuration: `nano main.cpp`
  - Set your information for platform, email and description on line 82–84
  - The default frequency is set to 868.1 Mhz (for Europe), if you use a different frequency change it on line 74
  - Set the IP of your server at line 88 or 89, e.g. your local computer: `#define SERVER1 "192.168.0.42"`
  - Exit the editor with `^x` and save your changes
- Compile the program: `make`
- Now start the program with: `./single_chan_pkt_fwd`

You can receive the forwarded packages on your computer on port 1700 via UDP, e.g. with [Packet Sender](https://packetsender.com/):

- Enable the UDP server at port 1700 in the preferences
- Watch the status updates and/or packages coming in from the gateway:

	// @todo: embed screenshot

### The Things Network Gateway

	// @todo

## Setup LoRaWAN Node

- Put the Dragino LoRa/GPS Shield on the Arduino
- Connect the Arduino via USB to your computer
- Open the Arduino IDE
- Download the [Arduino-LMIC library](https://github.com/matthijskooijman/arduino-lmic) as ZIP
- Import the library into the Arduino IDE: "Sketch > Include Library > Add .ZIP Library" (also see the README of Arduino-LMIC library)

### Hello World

For a basic "Hello World" example you can use the `raw` example from the Arduino-LMIC library, which you can find in the `examples` folder, but a few adjustments must be made:

First the pin mapping on the lines 32–38 have to be adjustet to:

	// Pin mapping
	const lmic_pinmap lmic_pins = {
		.nss = 10,
		.rxtx = LMIC_UNUSED_PIN,
		.rst = 9,
		.dio = {2, 6, 7},
	};

On line 138 the frequency has to be adjusted to match the one of the gateway:

	LMIC.freq = 868100000;

For the *Local LoRaWAN Gateway* experiment also the spread rate (SR) on line 148 has to be adjusted to match the one of the gateway:

	LMIC.datarate = DR_SF7;

Upload it to the Arduino and watch the packets being send at every "TX" on the serial monitor.
