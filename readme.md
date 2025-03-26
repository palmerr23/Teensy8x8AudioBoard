# Teensy Audio TLV320AIC3104 8x8 CODEC Board 

The Board comprises four TLV320AIC3104 stereo CODECs and a PCA9546 I2C multiplexer to allow individual CODECs to be programmed. Audio data transfer is via TDM using the standard Teensy 4.x pin allocations. Pin 22 is used for reset.

These CODECs can be programmed to offset their read/write slots to anywhere in a 256 slot TDM cycle and put the DO line in a Hi-Z state when not transmitting.

The boards are stackable to provide additional channels, by using the three address pins on the I2C muxes for board selection. While up to eight boards can be stacked, the supplied TDMA driver can accommodate two boards. 

The associated Teensy audio control software library is at https://github.com/palmerr23/control_TLV320AIC3104

If more are desired, alternate Audio libraries, such as that managed by Jonathan Oakley https://github.com/h4yn0nnym0u5e/Audio/tree/feature/multi-TDM, are suggested.


# Hardware

There are several boards in the set. 

There is further hardware information in the "8x8 and 16x16 Teensy Audio Board.docx" file.

## Main PCB

The main PCB which has the four CODECs and control logic. It features:

* 8 input channels capable of microphone  to line level  balanced (-54 dBm to +10 dBm) or unbalanced (-60 dBm to -1.5 dBm) inputs.

* 8 balanced line output channels with a maximum output level of +10dBm (+4 dbm single-ended) and capable of driving headphones or 600 ohm lines.
	
* A multiplexer to allow I2C programming of individual CODECs. 
	
* An optional -5V supply (rear of PCB) for the amplified TRS wing board option.

* The main PCB does not have decoupling capacitors on the inputs or outputs. They are on the Wing boards.

## Input and output Wing boards
Wing-boards with input or output connectors and DC coupling capacitors. Several different wing boards are offered:
* 4 x 6.5mm balanced-line TRS phone jacks. These are suitable for inputs or outputs.  2 x 3.5mm stereo headphone sockets are also provided for the output configuration.
* Combo XLR/TRS sockets for balanced or unbalanced inputs.
* Male or Female XLR sockets for balanced input or outputs.
* Female XLR sockets with phantom powering - the user needs to provide a suitable phantom power voltage source.
* An amplified 4-channel TRS output board.

# Obtaining the hardware
Several options are available for obtaining hardware. 
* Download the [Gerber Files] and have boards produced by your favorite PCB manufacturer and then mount the SMD components. The CODEC chips gave QFN SMD (no pins) packages. Only experienced constructors should consider this option.
* Order assembled (PCBA) boards from JLCPCB. https://oshwlab.com/palmerr23/works These boards are supplied with just the SMD components. The headers required must be supplied and mounted by the constructor. Input and output coupling capacitors are not implemented on the PCB, so your implementation must include them if the Wing boards are not used.
* Purchase ready-made boards from my Tindie store https://www.tindie.com/products/palmerr223/teensy-8x8-audio-board/. 


## Compatibility
Arduino 4.x with Teensyduino 1.59 or later and the supplied TDMA driver.

## CPU Load

CPU load for the basic TDM transfers is low. 

* 16 output channels driven by a single sine generator, and no inputs processed consumes 0.12% CPU on a T4.0

* Adding 2 channels of peak readings increases it to 0.35%.

* 8 channels = 0.66%

* 16 channels = 1.1%



## Power consumption
With a Teensy 4.0 and one board and all input and output channels on all four CODECs enabled the consumption is around 130 mA, mostly consumed on the 1.8V rail.

On a T4.0 with two boards and everything enabled, consumption rises to around 220 mA.

These figures are without any significant load on the outputs. Current draw will increase with low impedance loads.

Chip data (p12) indicates 4.3 + 6.7 = 11 mA per stereo (input + output) channel pair (i.e. 88 mA for 8x8).


