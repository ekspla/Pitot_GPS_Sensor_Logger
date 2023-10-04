# An Air/Ground Speed Sensor (Logger) Using Pitot Tube and GPS

(C) 2023 [ekspla](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/)

## Abstract


An air/ground speed measurement system with logging functionality was easily built 
with a Pitot tube [1](https://en.wikipedia.org/wiki/Pitot_tube) and a GPS receiver using micropython [2](https://micropython.org/).  The system allows us 
to measure the air and the ground speeds almost simultaneously at 5 Hz and more in various 
outdoor activities.  As a demonstration of this system, a correlation map of the air 
and the ground speeds of drfating in cycling [3](https://en.wikipedia.org/wiki/Drafting_(aerodynamics)) is shown.


## Materials


- SoC:
BlackPill (STM32F411CEU, 8 MB SPI flash), WeAct Studio [4](https://github.com/WeActStudio)

RTC clock (LSE) adjusted to 32.768 kHz using a pair of additional ~5 pF C0G capacitors, 
pin headers of PC14/15 removed, a coin-cell (CR2032) attatched to VBAT.

An universal counter and [this code](https://jhalfmoon.com/dbc/2023/03/28/micropython%e7%9a%84%e5%8d%88%e7%9d%a198-stm32%e7%89%88%e3%80%81nucleo%e3%81%ae%e3%82%af%e3%83%ad%e3%83%83%e3%82%af%e8%a8%ad%e5%ae%9a%e3%82%92%e8%a6%8b%e7%9b%b4%e3%81%99/) were used to measure/adjust the LSE frequency.

Micropython 1.20.0 [2](https://micropython.org/) installed via [ST-LINK](https://www.st.com/en/development-tools/st-link-v2.html).


- Pitot tube:
A cheap pitot tube for UAVs (drones), Aliexpress.

A home built GoPro (GP) compatible mount for the tube and the differential pressure sensor (see below).


- Differential pressure sensor:
D6F-PH5050 [5](https://github.com/omron-devhub), OMRON, +/- 500 Pa, I2C.

[`d6f_ph.py`](https://github.com/ekspla/D6F-PH)

Similar sensors such as MS5525 (TE Connectivity) and SDP810 (Sensirion) may be used.


- GPS receiver:
u-blox [NEO-M8N](https://www.u-blox.com/en/product/neo-m8-series), UART

  Update rates were set at 0.5, 5, and 5 Hz for GNRMC, GNVTG and GNGGA sentences, respectively.
  
`micropyGPS.py` [6](https://github.com/ekspla/micropyGPS), a forked/modified version of [inmcm/micropyGPS](https://github.com/inmcm/micropyGPS)


- A display to show the air/ground speed in real time (optional):
A cheap HD44780 (16x2 character) type LCD, I2C

`lcd_api.py`, dhyLands [7](https://github.com/dhylands/python_lcd)


- SD Card (optional, SPI):
Used for logging data, FAT format.

`sdcard.py`, micropython-lib [8](https://github.com/micropython/micropython-lib)


- Environmental sensor, BMP280 from BOSH Sensortec [9](https://www.bosch-sensortec.com/products/environmental-sensors/pressure-sensors/bmp280/), (optional, I2C):
Used for air density calibration, as well as for calculating altitudes.

Note that this sensor should be protected against direct sunlight and air stream.  A tiny piece of black open-cell foam (sponge) may suffice.

A modified version of Adafruit [10](https://github.com/adafruit) driver; continuous mode, Px16, Tx2, and w/o FIR filter.


## Assembling

Most of the micropython codes were precompiled before installation using [mpy-cross](https://github.com/micropython/micropython/tree/master/mpy-cross).
The assembled units of SoC/display and air speed sensor are shown in the photos (see below).
The procedure to calculate air speed is shown in elesewhere. [A link to Wikipedia](https://en.wikipedia.org/wiki/Pitot_tube)

![PHOTO_DISPLAY_UNIT](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/assets/23088524/597a1803-d24d-48b3-8af5-0211344b13ab "Display_Unit")![PHOTO_SENSOR_UNIT](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/assets/23088524/bd19487f-eebe-436c-b4d1-b03a5846598e "Sensor_Unit")

In the following experiments, the air speed sensor unit (GP mount, consisting of Pitot 
tube and differential pressure sensor) was mounted in front of a bicycle.


## Calibration

Air speed was calibrated by using Pitot coefficient and an offset of differential pressure.
Its accuracy was tested in a foggy morning around dawn (no wind / no sunlight).
See a correlation map below.  The tiny deviation from the diagonal line in 10-20 km/h range 
may be caused by headwinds of oncoming cars.

![FIG1](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/assets/23088524/f7df4ba7-1bcd-483e-b431-faf6cff3856e "Fig1_Test_Calibration")


## A Demonstrative Result -- Visualize the Effect of Drafting in Cycling --

A pair of bicycles were used for this experiment in a day of almost no wind outdoor.  
The mesurement system was attached in front of the trailer's bicycle following after the 
leader's bicycle in line.  The distances between them were kept 1.0-1.5 m at speeds 
below 40 km/h, while a bit longer distances at above these speeds for safety reasons.  
**The effect of drafting is easily seen in the figure**; the measurement points in the correlation 
map start to deviate from the diagonal line by increasing the ground speed at above 10 km/h.

![FIG2](https://github.com/ekspla/Pitot_GPS_Sensor_Logger/assets/23088524/567399aa-fdd2-4cee-abe9-0b21babf997b "Fig2_Drafting_in_Cycling")

It should be noted that **this data was easily taken by a single experiment** of 16 min, while 
most of the drafting experiments done in the past needed at least two independent experiments,
i.e. experiments with and without the leader's bicycle.


## Concluding Remarks

An affordable priced (for the hobbiest) system to simultaneously measure the air and the ground speeds 
outdoor was easily built.  As a demostrative example, a mesurement of drafting effect in cycling outdoor 
is shown.

This system can be modified to show the effect of wind direction outdoor as well, by using 
multiple (possibly three, each at an angle of 120 deg.) air speed sensors and vectorial math.


## References

[Pitot Tube](https://en.wikipedia.org/wiki/Pitot_tube)

[Drafting (Aerodynamics)](https://en.wikipedia.org/wiki/Drafting_(aerodynamics))

