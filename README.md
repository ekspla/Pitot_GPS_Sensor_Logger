# An Air/Ground Speed Sensor (Logger) Using Pitot Tube and GPS

(C) 2023 [ekspla](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/)

## Abstract


An air/ground speed measurement system with logging functionality was easily built 
with a Pitot tube [<sup>1</sup>](https://en.wikipedia.org/wiki/Pitot_tube) and a GPS receiver using micropython [<sup>2</sup>](https://micropython.org/).  The system allows us 
to measure the air and the ground speeds almost simultaneously at 5 Hz and more in various 
outdoor activities.  As a demonstration of this system, a correlation map of the air 
and the ground speeds of drafting in cycling [<sup>3</sup>](https://en.wikipedia.org/wiki/Drafting_(aerodynamics)) is shown.


## Materials


- SoC:
BlackPill (ARM Cortex-M4, STM32F411CEU, 8 MB SPI flash), WeAct Studio [<sup>4</sup>](https://github.com/WeActStudio).

RTC clock (LSE) adjusted to 32.768 kHz using a pair of additional ~5 pF C0G capacitors, 
pin headers of PC14/15 removed, a coin-cell (CR2032) attatched to VBAT.

A universal counter and [this code <sup>5</sup>](https://jhalfmoon.com/dbc/2023/03/28/micropython%e7%9a%84%e5%8d%88%e7%9d%a198-stm32%e7%89%88%e3%80%81nucleo%e3%81%ae%e3%82%af%e3%83%ad%e3%83%83%e3%82%af%e8%a8%ad%e5%ae%9a%e3%82%92%e8%a6%8b%e7%9b%b4%e3%81%99/) were used to measure/adjust the LSE frequency.

Micropython 1.20.0 [<sup>2</sup>](https://micropython.org/) installed via ST-LINK [<sup>6</sup>](https://www.st.com/en/development-tools/st-link-v2.html).


- Pitot tube:
A cheap pitot tube for UAVs (drones), Aliexpress [<sup>7</sup>](https://www.aliexpress.com/w/wholesale-pitot-tube.html).

A home built GoPro (GP) [<sup>8</sup>](https://gopro.com/) compatible mount for the tube and the differential pressure sensor (see below).


- Differential pressure sensor:
D6F-PH5050, OMRON [<sup>9</sup>](https://github.com/omron-devhub), +/- 500 Pa, I2C.

`d6f_ph.py` [<sup>10</sup>](https://github.com/ekspla/D6F-PH)

Similar sensors such as MS5525 (TE Connectivity) and SDP810 (Sensirion) may be used with their appropriate drivers.


- GPS receiver:
u-blox NEO-M8N [<sup>11</sup>](https://www.u-blox.com/en/product/neo-m8-series), UART, 115200 bps.

  Update rates were set at 0.5, 5, and 5 Hz for GNRMC, GNVTG and GNGGA sentences, respectively.
  
`micropyGPS.py` [<sup>12</sup>](https://github.com/ekspla/micropyGPS), a forked/modified version of inmcm/micropyGPS [<sup>13</sup>](https://github.com/inmcm/micropyGPS)


- A display to show the air/ground speed in real time (optional):
A cheap HD44780 (16x2 character) type LCD, I2C.

`lcd_api.py`, dhylands [<sup>14</sup>](https://github.com/dhylands/python_lcd)


- SD Card (optional, SPI):
Used for logging data, FAT format.

`sdcard.py`, micropython-lib [<sup>15</sup>](https://github.com/micropython/micropython-lib)


- Environmental sensor, BMP280 from BOSH Sensortec [<sup>16</sup>](https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/bmp280/), (optional, I2C):
Used for air density calibration, as well as for calculating altitudes.

A modified version of Adafruit [<sup>17</sup>](https://github.com/adafruit) driver; normal mode, oversampling (Px16, Tx2), and w/o IIR filter.

Note that this sensor should be protected against direct sunlight and air stream.  A tiny piece of black open-cell foam (sponge) may suffice.


## Assembling

Most part of the micropython codes were precompiled using mpy-cross [<sup>18</sup>](https://github.com/micropython/micropython/tree/master/mpy-cross) before installation.
The assembled units of SoC/GPS/display and air speed sensor are shown in the photos (see below).
The procedure to calculate air speed from differential pressure of Pitot tube is shown in 
elesewhere. [A link to Wikipedia <sup>1</sup>](https://en.wikipedia.org/wiki/Pitot_tube)

![PHOTO_DISPLAY_UNIT](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/assets/23088524/597a1803-d24d-48b3-8af5-0211344b13ab "Display_Unit")![PHOTO_SENSOR_AND_PITOT](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/assets/23088524/bd19487f-eebe-436c-b4d1-b03a5846598e "Sensor_Unit")![PHOTO_SENSOR_UNIT](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/assets/23088524/8a9c0e06-09cd-4d3d-8f98-5ab75494a01e "Sensor_Unit")

In the following experiments, the air speed sensor unit shown in the 3rd photo (GP mount, 
consisting of the Pitot tube and the differential pressure sensor) was mounted in front of 
a bicycle.  Update and logging rates were set at 5 Hz (200 ms interval), though the system 
can handle > 10 Hz.


## Calibration

Air speed was calibrated by using Pitot coefficient and an offset of differential pressure.
Its accuracy was tested in a foggy morning around dawn (no wind / no sunlight).
See almost perfect fit in the correlation map below.  The tiny deviation from the diagonal 
line in 10-20 km/h range may be caused by headwinds of oncoming cars.

![FIG1](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/assets/23088524/f7df4ba7-1bcd-483e-b431-faf6cff3856e "Fig1_Test_Calibration")


## A Demonstrative Result -- Visualizing the Effect of Drafting in Cycling --

A pair of bicycles were used for this experiment in a day of almost no wind outdoor.  
The air speed sensor was attached in front of the trailer's bicycle after the leader's 
bicycle following in line.  The distances between them were kept 1.0-1.5 m at ground speeds 
below 40 km/h, while a bit longer distances at above these speeds for safety reasons.  
**The effect of drafting is easily seen in the figure**; the measurement points in the correlation 
map start to deviate from the diagonal line by increasing the ground speed at above 10 km/h.

![FIG2](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/assets/23088524/567399aa-fdd2-4cee-abe9-0b21babf997b "Fig2_Drafting_in_Cycling")

It should be noted that **this data was easily taken by a single experiment** of 16 min, while 
most of the data in literatures consists of at least a couple of experiment,
i.e. one with and the other without the leader's bicycle. [Ref.](https:// "REF_TO_BE_INCLUDED")


## Concluding Remarks

An affordable priced (for the hobbiest) system to simultaneously measure the air and the ground speeds 
outdoor was easily built.  As a demostrative example, a mesurement of drafting effect [<sup>3</sup>](https://en.wikipedia.org/wiki/Drafting_(aerodynamics)) in cycling outdoor 
is shown.

This system can be modified to show the effect of wind direction outdoor as well, by using 
multiple air speed sensors (possibly three, each at an angle of 120 deg.) and vectorial math.


## References

[Pitot Tube](https://en.wikipedia.org/wiki/Pitot_tube)

[Drafting (Aerodynamics)](https://en.wikipedia.org/wiki/Drafting_(aerodynamics))

