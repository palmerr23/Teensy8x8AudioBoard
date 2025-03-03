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

The main PCB which has the CODECs and control logic. It features:

* 8 input channels capable of microphone  to line level  balanced (-54 dBm to +10 dBm) or unbalanced (-60 dBm to -1.5 dBm) inputs.

* 8 balanced line output channels with a maximum output level of +10dBm (+4 dbm single-ended) and capable of driving headphones or 600 ohm lines.
	
* A multiplexer to select the I2C control channel for individual CODECs. 
	
* An optional -5V supply (rear of PCB) for the amplified TRS wing board.

Wing-boards with input and out connectors and DC coupling capacitors. Four different wing boards asre offfered:
	1. 4 x 6.5mm balanced-line TRS phone jacks. These are suitable for inputs or outputs.  2 x 3.5mm stereo headphone sockets are also provided, for the output configuration only.
	2. Combo XLR/TRS sockets for balanced or unbalanced inputs.
	3. Male or Female XLR sockets for balanced input or outputs.
	4. An amplified 4-channel TRS output board.
 
While up to eight boards may theoretically be stacked, there is a practical limit with the standard Teensy Audio Library TDM driver, which handles 16 x 16-bit duplex TDM channels - that is two 8x8 boards.
Alternate Audio libraries, such as that managed by Jonathan Oakley https://github.com/h4yn0nnym0u5e/Audio/tree/feature/multi-TDM, offer mutliple input and output pins for each TDM instance.

There are jumpers on the PCB to allow alternate DI/DO pins to be used. This feature is untested, and the Teensy may not be able to drive more than two boards simultaneously, due to excessive reflections and distortion of the high frequency signals by multiple long PCB tracks. Please read the hardware notes in this repo before 

# Obtaining the hardware
1. Download the [Gerber Files] and have boards produced by your favorite PCB manufacturer and then mount the SMD components.
2. Order assembled (PCBA) boards from JLCPCB. These boards are supplied with just the SMD components. The headers required for your application must be added. Input and output coupling capacitors are not implemented on the PCB, so your implementation must include them if the Wing boards are not used.
2. Purchase ready-made boards from my Tindie store https://www.tindie.com/products/palmerr223/teensy-8x8-audio-board/.


## Compatibility
Arduino 2.x with Teensyduino 1.59 or later and the supplied TDMA driver.

# Function Reference

The TDMA driver operates in the same way as the standard Teensy Audio TDM driver. A 'fixed' TDM2 driver is not provided.

### Constructor

AudioControlTLV320AIC3104 aic(uint8_t codecs = 1, bool useMCLK = true, uint8_t i2sMode = AICMODE_I2S,  long sampleRate = 44100, int sampleLength = 16);

The simplest form for a single codec defaults to I2S mode with standard teensy audio parameters:

AudioControlTLV320AIC3104 aic();

For TDM operation the simplest form is: 

AudioControlTLV320AIC3104 aic(n); //(n > 1)

### AudioMemory( )

One AudioMemory block is required for each input or output.

### Wire

Defining and initialising the Wire library is the responsibility of the user applciation. 

This must be completed before begin( ) is called.

### begin( )

Resets the CODECs and muxes and then probes for muxes.

begin( ) may be called with no arguments if the standard reset pin (22) is used.

### enable( )

Called without arguments, enable( ) configures all attached CODEC control registers.

enable(codec) may be useful when debugging hardware.

### inputMode(mode, codec)

Set single-ended (AIC_SINGLE) or differential (AIC_DIFF) input mode.

When called before enable( ), inputMode sets the default input mode and the second argument is ignored.

When called after enable( ), all CODECS (codec = -1) or a single CODEC may be affected.

In differential mode '-' inputs should be grounded for unbalanced signals to reduce noise.

### inputSelect(value, channel, codec)

The balanced inputs (L1) are used for both mic and line inputs. PGA gain is adjustable between 0 and 59.5 dB.

Compliant with the Teensy audio standard and the SGTL5000 implementation, this sets an input to either MIC (-62.5 dBm) or Line (0dBm).

For finer gain control, use inputLevel( ).

### inputLevel(value, channel, codec) and gain(value, channel, codec)

The balanced inputs (L1) are used for both mic and line inputs. PGA gain is adjustable between 0 and 59.5 dB.

The two functions are equivalent, setting the maximum input level or gain of an input channel. 

For inputLevel( ) the range is -59.9 to 0 dB.

For gain( ) the range is 0 to 59 dB.
 
Values outside these ranges are appropriately constrained. 

When called without channel and codec arguments, all codecs and channels are affected.

### volume(value, channel, codec)

Sets the volume of an output channel. 
 
When called without channel and codec arguments, all codecs and channels are affected.

For most applications, using other means to control the output level is preferable to changing the default volume level using this function.

### setVerbose(int verbosity)
Sets the level of messages on stderr. 

0 turns messages off.

1 will provide some error messages on startup if the hardware doesn't match the supplied number of CODECs, etc.

Should be left at 0 for production as the writes may block if USB isn't connected.

### listMuxes( )
Useful for checking, initally that the board jumpers are set as expected.

Should be called after begin( ), where the muxes are probed and recorded.

