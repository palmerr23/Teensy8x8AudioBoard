# Teensy Audio TLV320AIC3104 8x8 CODEC Board audioControl library

An Arduiono-compatible library for the Texas Instruments TLV320AIC3104 stereo channel CODEC.

The library was created specifically for the associated four-CODEC Teensy Audio board. As the AIC320 only suppoers a single I2C address, there is some code to ensure the correct CODEC is being programmed via a multiplexed I2C bus.

This CODEC is TDM-compatible as can be programmed to offset its read/write slots to anywhere in a 256 slot TDM cycle and put the DO line in a Hi-Z state when not transmitting.


## Single CODEC operation
Call audioMode() with the first parameter set to 1 prior to enable( ).

I2S mode is the default for single codecs, however TDM may be selected.

The first CODEC on the mux with the lowest I2C address will be used.

Avoid the more complex forms of the various function calls, accepting the default arguments where they are available.

## TDM operation with mutliple CODECs
With the current Teensy Audio TDM driver and the TDMA driver supplied with this library, two stacked boards are practical, providing 16x16 operation.

A PCA9544 I2C multiplexer selects one of the four CODECs on each board.
There are on-board jumpers to theoretically allow up to eight muxes (boards) to be stacked with a single Teensy.

As of Teensyduino 1.59, EVEN channel samples are not transferred correctly by the Teensy Audio TDM driver. The TDMA driver supplied with this library corrects this issue.


## Hardware

There are several boards in the set. 

1. The main PCB which has the CODECs and control logic. 

	It features:

	8 input channels capable of microphone  to line level  balanced (-54 dBm to +10 dBm) or unbalanced (-60 dBm to -1.5 dBm) inputs.

	8 balanced line output channels with a maximum output level of +10dBm (+4 dbm single-ended) and capable of driving headphones or 600 ohm lines.

2. Wing-boards for input and out connectors and DC coupling capacitors.
Four different wing boards asre offfered:
	1. 4 x 6.5mm balanced-line TRS phone jacks. These are suitable for inputs or outputs.  2 x 3.5mm stereo headphone sockets are also provided, for the output configuration only.
	2. Combo XLR/TRS sockets for balanced or unbalanced inputs.
	3. Male or Female XLR sockets for balanced input or outputs.
	4. An amplified 4-channel TRS output board.
 
The main board has jumpers that allow up to eight boards to be managed.

There is a practical limit on the standard Teensy Audio Library, which can handle 16 inputs and 16 outputs x 16-bit TDM channels simultantously - that is 2 8x8 boards.
Alternate Audio libraries, such as that managed by Jonathan Oakley https://github.com/h4yn0nnym0u5e/Audio/tree/feature/multi-TDM, offer mutliple channels of input and output for each TDM instance.

There are jumpers on the PCB to allow alternate DI/DO pins to be used. This feature is untested, and the Teensy may not be able to drive more than two boards simultaneously, due to excessive reflections and distortion of the high frequency signals by multiple long PCB tracks.

1. Download the [Gerber Files](GERBER_FILES.zip) and have boards produced by your favorite PCB manufacturer.
2. Purchase ready-made boards from my Tindie store XXXXX






## Uploading Firmware

### Hardware Setup 

Using a USBasp programmer connect it to the ICSP Header on the board. Refer the following 

### Software Installation



#### Uploading the .HEX file and .EEP file to MCU

```
avrdude -c usbasp -B 20  -p m328p -P usb -U flash:w:./TransistorTester.hex:a -U eeprom:w:./TransistorTester.eep:a
```

#### Setting the FUSES

```
avrdude -c usbasp -B 200  -p m328p -P usb  -U lfuse:w:0xe2:m -U hfuse:w:0xd9:m -U efuse:w:0xfc:m
```

---

### References
- [Schematic](./Schematic.pdf)
- [Original Transistor Tester](https://www.mikrocontroller.net/articles/AVR_Transistortester)
