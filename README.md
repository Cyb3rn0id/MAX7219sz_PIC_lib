[![GitHub license](https://img.shields.io/badge/license-AGPL--3.0-blue)](LICENSE)  

# MAX7219 Library for Microchip(R) PIC(R) microcontrollers

**Version: 1.0**  

This is a library for MAX7219 and 7-segments displays (no led matrix) to be used with Microchip(R) PIC(R) microcontrollers.  
Library is licensed under [AGPLv3 license](https://github.com/Cyb3rn0id/MAX7219_PIC_lib/blob/master/LICENSE).  

You can read how MAX7219 works on my blog [in this page](https://www.settorezero.com/wordpress/max7219-pilotare-display-a-7-segmenti-o-matrici-di-led-e-un-gioco-da-ragazzi/) => It's in italian language but I've included a translation service on the right bar where you can select your language.  

[Folowing this link](https://www.settorezero.com/wordpress/max7219-libreria-per-usare-display-led-a-7-segmenti-con-i-microcontrollori-pic/) you can read an article, in italian language, about this library. If you don't speak italian this link is not necessary since usage instructions in english are just here.

This is a video showing the demo:  
[![MAX7219sz PIC Lib Demo](https://img.youtube.com/vi/tz2HAa73ssA/maxresdefault.jpg)](https://www.youtube.com/watch?v=tz2HAa73ssA)  

The demo is made using the PIC16F15376 Curiosity Nano. You can download the demo [here](https://github.com/Cyb3rn0id/Microchip_Curiosity_Nano_Examples/tree/master/16F15376_Curiosity_Nano_MAX7219.X)  
  
## Preliminary Info

### Microcontroller Instruction Cycle
Library is tested with Microchip PIC Microcontrollers up to 32MHz. The critical part is that MAX7219 needs a minimum pulse duration of 50nS. With a 32MHz 8bit PIC there are about 125nS pulse durations.  

> Istruction cycle of a PIC12/16/18 is 4 clock cycles (4/Fosc). Assuming Fosc=32MHz we have T=31.25nS so instruction cycle is 4 * T=125nS.  

In this case, assuming an instruction cycle of 4/Fosc, I think library may work up to 80MHz. If you notice library does not work with your PIC microcontroller (16 or 32 bit, or 8bit with a Fosc greater than 32MHz), make some calculations as I've done above and if your instruction cycle is less than 50nS, insert delays in the _MAX7219_send_ function between and after any pulse.

### Pins used
MAX7219 needs 3 GPIOs (General Purpose Input/Output).

### Limitations
Library is currently tested for a single MAX7219 (8 Digits) and only for 7-segment displays. Led Matrix are not supported.

### Misc Info
Digits are numbered from right (1) to left (8), so digits are 1-based index (MAX7219 starts from digit 1).    
Strings are normally left-aligned. You can pad strings using the function _MAX7219_lputs_ instead of _MAX7219_puts_.  
Numbers are right-aligned. You can pad numbers using a parameter in the function that prints number.   
  
## Libray Setup

It's reccomended to use the [MPLAB(R) Code Configurator](https://www.microchip.com/mplab/mplab-code-configurator). Choose 3 pins, make them digital outputs and rename them as: _MAX_DAT_, _MAX_CLK_ and _MAX_LAT_:  

- _MAX_DAT_ : connected to MAX7219 _DIN_ pin (pin n°1)
- _MAX_CLK_ : connected to MAX7219 _CLK_ pin (pin n°13)
- _MAX_LAT_ : connected to MAX7219 _LOAD/!CS_ pin (pin n°12)

> Library uses the _PIN_SetLow()_ and _PIN_SetHigh()_ functions created by the Code Configurator, so the main library file (_MAX7219sz.c_) includes the "mcc_generated_files/pin_manager.h" file. The _device_config.h_ file is also included because in this header is defined the _XTAL_FREQ_ value, used by XC8 builtin delay functions.  

If you've never used the MPLAB Code Configurator it's the good time for start. You can [read a tutorial I've made about it](https://www.settorezero.com/wordpress/curiosity-nano-code-configurator-per-entrare-nel-mondo-dei-microcontrollori-pic-senza-sforzo-e-in-economia/)  

After you've defined pins, you can include _MAX7219sz.h_ and _MAX7219.c_ in your project and make changes in the header library file (_MAX7219sz.h_):  

- change _DIGITS_ value to fit your display. Actually the library is tested only for 8 digits. If your display uses less than 8 digits, you must be aware about the limit resistor and intensity register, in other cases your display and MAX7219 can be damaged.
- change _MAXINTENSITY_ value. The maximum value is 9. If you use less than 8 digits is better lower this value.
- if you want to use different delay libraries, change _DELAYCURSOR_, _DELAYSCROLL_ and _DELAYBLINK_ macros that are used respectively for the speed of cursor effect in the _MAX7219_puts_ function, the scrolling speed in the _MAX7219_scroll_ function and the blinking speed in the _MAX7219_blink_ function. You can also change the delay values in those macros if you desire different writing/scrolling/blinking speeds. 
- change eventually _SCROLLBUFFER_ value if you want to use scrolling effect with less chars and then saving some memory. See _MAX7219_scroll_ function description for further informations.
  
  
## Library Functions

```c
void MAX7219_init(void)
```
Initialize the MAX7219 using the number of digits defined by constant _DIGITS_ in library header file, _NODECODE_ mode, max brightness.  
This function also turns off the Test mode, so if you want to start your application performing a display test (as the demo does) you can use the _MAX7219_test_ function first, give some delay and then call this initialization function.

---

```c
void MAX7219_clear(void)
```
Clears all digits for both _DECODE_ and _NODECODE_ modes.  
Display remains turned on: only internal shift register of MAX7219 is erased.

---

```c
void MAX7219_clearc(void)
```
Clears all digits using the cursor effect from right to left starting from the most right used digit.  
This function is used only in the _NODECODE_ mode.  
Display remains ON: only internal shift register of MAX7219 is erased.

---
```c
void MAX7219_lclearc(uint8_t startpos)
```
Clears all digits using the cursor effect from right to left starting from the most right used digit until _starpos_ digit.  
This function is used only in the _NODECODE_ mode.  
Display remains ON: only internal shift register of MAX7219 is erased.  
This function is used for deleting text inserted by the _MAX7219_lputs_ function.

---
```c
void MAX7219_send(uint8_t reg, uint8_t dat)
```
Sends a byte to the selected register.  

- _reg_: MAX7219 register to write in
- _dat_: data to be written in the register  

This function is also used for writing symbols in the _NODECODE_ mode: in that case the _dat_ value must define wich segments must be turned on. Example:  
_MAX7219_send(8,SEGA|SEGB|SEGF|SEGG|SEGE|SEGC)_ => writes the A letter on the 8th digit (the most-left digit) by turning all segments but segment D and comma/point.

---

```c
void MAX7219_putch(uint8_t digit, char ch, bool point)
```
Writes a single ASCII char on the selected digit.  

- _digit_: digit number to write on (from 1 to 8)
- _ch_: char to write (ex.: 'g', '1', '.' ecc) - you must use single quote mark (ASCII 39)
- _point_: will turn on (_true_) or off (_false_) the comma/decimal point on the selected digit  

You can use both uppercase and lowercase letters, in every case letters will be converted to use the defined font in the library header. You can write all letters but W,X that can't be effectively rendered on 7-segments displays. Some other chars will look a bit weird (ex.: the 'Z' is rendered as 2) but..hey... it's a 7-segment display!  

---

```c
void MAX7219_numch(uint8_t digit, uint8_t n, bool point)
```
Writes a number as char on the selected digit.  

- _digit_: digit number to write on (from 1 to 8)
- _n_: number to write, inserted as integer from 0 to 9
- _point_: will turn on (_true_) or off (_false_) the comma/decimal of the selected digit

---

```c
void MAX7219_setDecode(void)
```
Turns on the _DECODE_ Mode (BCD Code B). After you've called this function you can't use the user-defined font and you can only use the _MAX7219_send_ function for writing numbers from 0 to 9 and H,E,L,P letters and minus sign.  See the attached datasheet for further informations.

---

```c
void MAX7219_setNoDecode(void)
```
Turns on the _NODECODE_ mode (you must use chars and numbers as defined in the library header). This is the default mode.  

---

```c
void MAX7219_setIntensity(uint8_t val)
```
Set the display brightness.  Any value higher than _MAXINTENSITY_ will be converted in _MAXINTENSITY_.  A value of 0 does not turns off the display visualization: digits are still visibile even them appears faint.  If you want to slowly lower the luminosity making display disappearing, you must use the _MAX7219_shutdown_ function at the final point. See the _MAX7219_glow_ function for further informations.

- _val_: integer value from 0 (lowest) to _MAXINTENSITY_ (highest)  

---

```c
void MAX7219_test(void)
```
Activate the test mode (all leds on). For exiting the test mode you must recall the _MAX7219_init_ function since the test mode overwrites all settings.  

---

```c
void MAX7219_shutdown(bool yesno)
```
Turns on or off the display visualization. After turning off, MAX7219 shift register content is not deleted, so digits data are retained and will appear again after you turn on the display, the _MAX7219_blink_ function uses this feature.  

- _yesno_: turns off (_true_) or on (_false_) the display visualization

---

```c
void MAX7219_puts(const char *s, bool cursor)
```
Writes a constant string on the display using the font defined in the library header, using or no the cursor visual effect, starting from the most-left digit. The string will be printed from left to right and the last used digit will be saved in RAM memory for the _MAX7219_clearc_ and _MAX7219_lclearc_ functions.

- _s_: string to write (ex.: _"hello"_) - you must use the double quotation mark (ASCII 34)
- _cursor_: display the string using the cursor effect (_true_) or make string appearing immediately without visual effects (_false_)  

If string is bigger than _DIGITS_ chars, will be truncated.  

---

```c
void MAX7219_lputs(const char *s, bool cursor, uint8_t startpos)
```
Writes a constant string on the display using the font defined in the library header, using or no the cursor visual effect, starting from a specified digit. The string will be printed from left to right and the last used digit will be saved in RAM memory for the _MAX7219_clearc_ and _MAX7219_lclearc_ functions.

- _s_: string to write (ex.: _"hello"_) - you must use the double quotation mark (ASCII 34)
- _cursor_: display the string using the cursor effect (_true_) or make string appearing immediately without visual effects (_false_)  
- _startpos_: digit from which starting to print

String is truncated after the digit 1.  

---

```c
uint8_t MAX7219_putn(int32_t num, uint8_t decimals, uint8_t rightspace)
```
Writes an integer by eventually turning on the comma/decimal point on a certain digit for showing a decimal number and eventually leaving some space on the right if you want to put a simbol or simply left-align the number. The minus sign will added near the most-left digit if needed. Function uses a 32bit signed integer but actually the number of visualized digits is limited in the library to 8 digits.  

- _num_: integer from -9'999'999 to 99'999'999, bigger or lower numbers will not be properly visualized since library is limited to 8 digits
- _decimals_: if this parameter is greater than 0 function will turn on the comma/decimal point leaving this number of digits after the comma/decimal point. Set this parameter to 0 if you don't want to visualize a decimal number.
- _rightspace_: places to leave free on the most-right position. Put 0 if you want to right-align the number.  
- Returns: position of most-left printed digit (minus sign included).

Numbers are right-aligned, you can use _rightspace_ parameter for move them on the left or for add a symbol (ex.: °C) on the right of displayed number.  

_decimals_ is used for fixed-point decimal notation. So if you must print a decimal number, you can multiply it by a power of 10 to transform it in an integer and then set the number of decimals to show. Example: if you must print 24.1, you must print 241 and set _decimals_ to 1. If you must print 0.002, you must print 2 and set _decimals_ to 3.    

Function will also delete previous digits if the current number to be printed is smaller than previous one: if you print "99999" and after this you print "1" in other cases you'll visualize 99991 since normally the previous digits are retained in the MAX7219 shift register; the library keeps in mind the most-left used digit when printing numbers and will remove all previous digits until the new ones but without performing a display clear since this will cause flickering: only previous unused digits are cancelled.  

---

```c
uint8_t MAX7219_putun(uint32_t num, uint8_t decimals, uint8_t rightspace)
```
This function is the same as _MAX7219_putn_ but is used only for unsigned integers. Since the number of digits is limited to display width the advice is to use exclusively the _MAX7218_putn_ function for every kind of number: better have only one function for avoiding confusion. This function will remain for future implementations (I hope) using more than one MAX7219 and is however recalled by the _MAX7219_putn_ function for printing the integer part.

---

```c
MAX7219_scroll(const char *s, bool appear, bool disappear)
```
Writes a constant string using scrolling effect, from right to left  

- _s_: String
- _appear_: choose to make appear immediately the first _DIGITS_ chars of the string (_false_) or start the scroll with a blank display (or previous chars visualized that will be overwrited) making the string appear from the right one char at time (_true_)
- _disappear_: choose to make string scrolling out of the display leaving it blank (_true_) or  make last _DIGITS_ chars of the string remain visualized (_false_)

The maximum amount of string chars is given by:  

> _SCROLLBUFFER_-1-(_DIGITS_ * _appear_)-(_DIGITS_ * _disappear_).  

If _appear_==_true_ then _DIGITS_ spaces will be added on the left of the string and other _DIGITS_ spaces will be added on the right if you use the _disappear_ flag. So if _SCROLLBUFFER_ is 80, your display has 8 digits and you use both _appear_ and  _disappear_ flags, you can write max 63 chars. One char is used for the '\0' string terminator.

---

```c
void MAX7219_glow(uint8_t times)
```
Glows the display using both Intensity and Shutdown registers.  

- _times_: number of glowing cycles.  

One glowing cycle is made of those events:  

- start from maximum brightness (_MAXINTENSITY_)
- slowly decrement the brightness arriving to the minimum value (0)
- turn off the display visualization
- turn on the display visualization
- start from minimum brightness (0)
- slowly increment the brightness arriving to the maximum value (_MAXINTENSITY_)

---

```c
void MAX7219_blink(uint8_t times)
```
Blinks the display using the Shutdown register.  

- _times_: number of blinking cycles.  

A single blink is made turning off and then on the display.  


## Support me ##
  
If you want to support my free work, you can make me a gift from my [amazon wishlist](https://www.amazon.it/gp/registry/wishlist/DX4SUGLWNLYB/) or give a reading [here](https://www.settorezero.com/wordpress/info/donazioni/)

## Trademarks ##

Microchip, MPLAB, PIC are registered trademarks property of Microchip Technology Inc. [Here is a list](https://www.microchip.com/about-us/legal-information/trademarks/microchip-trademarks) of Microchip Technology Inc. trademarks.  

Maxim is a registered trademark property of Maxim Integrated Products Inc. [Here is a list](https://www.maximintegrated.com/en/legal/trademarks.cfm) of Maxim Integrated Products trademarks.  

All other brands or product names are property of their respective holders.  
