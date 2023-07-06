# SSD1303 Breakout Board

A breakout board for a specific SSD1303 OLED display.  
Optimized for low-cost PCB assembly services from a specific chinese manufacturer with blue logo.

You can [preview the PCB](https://kicanvas.org/?github=https%3A%2F%2Fgithub.com%2FLeoDJ%2FSSD1303_Breakout%2Fblob%2Fmain%2FSSD1303_Breakout.kicad_pcb) using the amazing KiCanvas. (Or the [schematic](https://kicanvas.org/?github=https%3A%2F%2Fgithub.com%2FLeoDJ%2FSSD1303_Breakout%2Ftree%2Fmain) if I forget to update the [PDF](./SSD1303_Breakout.pdf) ^^' )

## Pinout
| Pin | Function                                                                     |
|-----|------------------------------------------------------------------------------|
| GND | GND                                                                          |
| VCC | 3.3 - 5 V (up to ~250mA[^1])                                                 |
| CLK | SPI: `SCK` <br>I²C: `SCL`                                                    |
| DI  | SPI: `MOSI` <br>I²C: `SDA`                                                   |
| RES | Reset (active low)                                                           |
| DC  | SPI: data/command line <br>I²C: address. 0: `0x3C` / 1: `0x3D` (default low) |
| CS  | SPI: `CS`                                                                    |

* I/O pins are 5V tolerant
* To switch between SPI and I²C mode, you need to bridge / unbridge the solder jumper at the back (default is SPI)
* I've tried to make the pinout as compatible as possible to existing OLED modules, but there are many different variants

[^1]: Might draw up to ~250 mA @ 3.3V with all pixels on, guesstimated.  
I've measured roughly 50mA @ 12V with the highest brightness and all pixels on, so with pessimistic conversion losses around 750mW.

## Rev 1.0
### Issues
- In SPI mode, the "serial data out" pin of the SSD1303 (pin D2) doesn't always stay high[^2], as was initially measured. Thus the simple diode trick to allow both SPI and I²C mode, without needing to "keep D2 disconnected in SPI mode" as stated in the datasheet, won't work.
  - **Workaround**: Remove D1 (below the FPC) for working SPI mode
  - Fix in next revision: An additional FET in series to the D2 signal that gets activated by the solder jumper
- An additional solder jumper for I²C address would probably be nice
  - In case one wants to use the breakout board on a 4-pin-only socket
  - **Workaround**: solder a jumper wire from the DC pin/pad to the VCC pin/pad to select address `0x3D`

[^2]: It appears to shift out some kind of data, maybe from an internal register. The SSD1303 datasheet doesn't give any hint to the behaviour of pin D2 in SPI mode, except to keep it disconnected.  
Over time the value that gets shifted out accumulates 1 bits. Starting at some random value with few bits set on power-up. (It probably turns into a constant high signal as soon as it reaches 0xFF, but I could only actually observe it up to 0x7F/0xF7 in my last measurement.)  
When the pin is actively pulled low by the SSD1303, it interferes through the diode D1 with the actual data meant for the data input pin D1.  

### Screenshots of 3D view
Somehow I got a bit carried away for a relatively simple design, but otoh I've learned a lot again.

<p float="left" style="display: flex; flex-wrap: wrap; align-items: center;">
  <img src="img/front.jpg" width="49%" />&nbsp;
  <img src="img/side.jpg" width="49%" />
  <img src="img/back.jpg" width="48%" />&nbsp;
  <img src="img/back_noOLED.jpg" width="50%" />
</p>