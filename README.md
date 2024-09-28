# RainSensor
Anti-Corrosion Rain Sensor


## Overview
Unlike the cheap rain sensors that corrode quickly this design uses a small AC signal to detect rain either resistively or capacitively. The AC signal is small enough to be below the voltage required for electrochemical corrosion thereby extending the sensor life considerably. 
In addition, this design features both analog and digital outputs and can operate from 3.3V or 5V. The on-board heater can also operate automatically when moisture is detected, or manually via a control pin.


## Pin Description
| Nbr | Name    | Description 			| Specifications														|
| --- | -------	| --------------------- | --------------------------------------------------------------------- |
|  1  | HTR+ 	| Heater supply voltage | 5V input = 2W heater, 3.3V = 0.9W. 5V max								|
|  2  | GND  	| Ground 0V  			| Common 0V but intended mainly for heater current						|
|  3  | Heat  	| Heater control input	| 0V/open=off 3.3V-5V=on. In auto-heat mode can be used to read DigOut	|
|  4  | VDD  	| Logic supply voltage	| 3.3V-5V input. Typical current consumption 5-8mA						|
|  5  | GND  	| Ground 0V  			| Common 0V but intended mainly for logic voltage						|
|  6  | DigOut  | Digital output  		| 0V=no rain, VDD=rain. Recommend <5mA draw. Adjustable threshold.		|
|  7  | AnaOut	| Analog output			| 0V=no rain, VDD=rain. Recommend <5mA draw. Adjustable gain.			|


## Setup and Configuration Options
1. First decide if the Auto-Heat option is desired - refer to the last section of "Circuit Description" below. To enable auto heat, bridge jumper JP1, otherwise leave it open (default) to disable auto heat. 
2. Next, power up the board with the desired VDD (3.3V to 5V).
For resistive sensing mode follow the numbered steps below. Capacitive sensing mode is more durable but less sensitive - if this is desired then first cover the entire sensing area in a thin plastic tape (such as clear packaging tape) then follow the same steps below. The thinner the tape the better the sensitivity.
3. For analog output, cover the sensor element with some al-foil or other conductive material. In capacitive mode, make sure it is pressed against the sensor but do not touch it with your hands whilst adjusting the trimpots as that will affect the measurement.
4. Adjust the gain trimpot all the way counter clockwise (min gain) then slowly clockwise until the AnaOut voltage reaches the maximum ADC input voltage for your system. For example most ESP32 boards have a 1V ADC input maximum. The sensor is now ready for use.
5. For digital output use, remove the conductive material and instead apply a few drops of water. Adjust the DigOut Thresh trimpot until the orange LED lights to indicate moisture is detected. Then reduce the threshold very slightly (counter-clockwise) to give a little margin on the threshold point.
6. Install your sensor in a waterproof case and position it in an open area, angled to allow rain to slide off it preferably with the points of the sensor element pointing downwards.


## Circuit Description
Opamp U1A is configured as a relaxation oscillator with a frequency of about 6-8kHz. R5 and D1 on the output clamp the voltage to about 0.5Vpp independant of the supply voltage. This passes through C3 to make it AC-coupled into the rain sensor element. R6 and R7 keep the sensor biased around 0V. Opamp U1B is a precision rectifier configuration with gain set by RV2, and its frequency response is shaped to reject 50Hz and provide peak gain at the ~8kHz oscillator frequency. The opamp charges C5 which holds the peak votlage which is then buffered by U1C for the analog output. The digital output is handled by opamp U1D in comparator configuration with hysteresis set by R11/R10 and threshold set by RV1. 
The ananog and digital outputs have 100R protection resistors near the connector which limits the practical output current to 5-10mA, however changing this resistor for 0R should provide 20-30mA (limited by the opamp capabilities). The LEDs are the main current consumers in the design and could be removed (or current reduced) to save power.
The heater is a string of 100R 1206 resistors controlled by an N MOSFET. With the "Auto Heat" jumper open (disabled) the Heat pin controls the heater state directly. When the "Auto Heat" jumper is bridged (enabled) this connects the MOSFET to the digital output signal so that when moisture is detected the heater turns on automatically. In this configuration the Heat pin actually has several functions:
 - For pure auto-heat operation, leave the Heat pin open or connected to an input pin.
 - The Heat pin will copy the state of the DigOut pin, meaning the Heat pin can be read instead of having to run another wire for DigOut and Heat pins.
 - Driving the Heat pin high or low at any time will enable or disable the heater function, thereby over-riding the automatic behaviour.


## TODO
The latest schematic includes component value changes from testing, as well as a modification that require some PCB track cutting, these changes are highlighed on the schematic and included below as changes for the next revision.
### Modifications required for Rev A
- 

## Future changes for Rev B
The modifications required for Rev A above, plus these less-important changes to improve circuit performance/robustness:
- Polarity protection at input
- Optional position for resistor divider at digital output (to allow 3.3V logic with 5V power source): better than adjusting gain trimpot
- Low-pass filter at U1B input: 
- Look to commonise the two trimpot values to 500k
- Consider a better solution for the gain trimpot since it is either 100k-150k for resistive sense mode or 500k for capacitive sense mode
- Consider if sensor element can have more capacitive coupling (closer spacing?)