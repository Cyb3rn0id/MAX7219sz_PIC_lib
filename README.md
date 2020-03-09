# MAX7219 Library for Microchip PIC microcontrollers

This is a library for MAX721 and 7-segments displays (no led matrix) to be used with Microchip PIC microcontrollers.  
Library is licensed under [AGPLv3 license](https://github.com/Cyb3rn0id/MAX7219_PIC_lib/blob/master/LICENSE).  
You can read how MAX7219 works on my blog [in this page](https://www.settorezero.com/wordpress/max7219-pilotare-display-a-7-segmenti-o-matrici-di-led-e-un-gioco-da-ragazzi/) => It's in italian language but I've included a translation service on the right bar where you can select your language.

### Hardware implementation
#### Microcontroller Instruction Cycle
Library is tested with Microchip PIC Microcontrollers up to 32MHz. The critical part is that MAX7219 needs a minimum pulse duration of 50nS. With a 32MHz 8bit PIC there are about 62,5nS pulse durations (instruction cycle of a PIC12/16/18 is 4 clock cycles). Using a PIC microcontroller having a Fosc>32MHz you need to insert delays between clock and latch pulses.  

#### Pins used
You need 3 GPIOS defined as outputs: Data, Clock and Latch (!CS/LOAD).  

### Library Functions

```c
void MAX7219_init(uint8_t digitN, uint8_t decodeM)
```
Initialize the MAX7219.
- _digitN_ : number of digits you've on the display, from 1 to 8
- _decodeM_: decode mode, possible values: DECODE (for BCD code B) | NODECODE  

---

```c
void MAX7219_clear(void)
```
Clears all digits for both decode and nodecode modes.  
Display remains ON: only internal shift register is cancelled.  

---

```c
void MAX7219_clearc(void)
```
Clears all digits using the cursor effect from right to left.  
This function is used only for the NODECODE mode.  
Display remains ON: only internal shift register is cancelled.  

---

```c
void MAX7219_send(uint8_t reg, uint8_t dat)
```
Sends a byte to the selected register.
- _reg_: MAX7219 register to write in
- _dat_: data to be written in the register  

Note: This function is used also for writing chars in the nodecode mode example:  
_MAX7219_send(8,'A')_ => writes the A letter on the 8th digit (the most-left digit).

---

```c
void MAX7219_putch(uint8_t digit, char ch, bool point)
```
Writes a single char on the selected digit.  
- _digit_: digit number to write on (from 1 to 8)
- _ch_: char to write (ex.: 'g', '1', '.' ecc)
- _point_: will turn on (true) or off (false) the point/comma on the selected digit  

Note: you can use both uppercase and lowercase letters, in every case letters will be converted to use the defined in the library header.  
You can write all letters but W,X,Z that can't be effectively rendered on 7-segments displays.

---

```c
void MAX7219_numch(uint8_t digit, uint8_t n, bool point)
```
Writes a number as char on the selected digit.
- _digit_: digit number to write on (from 1 to 8)
- _n_: number to write, inserted as integer from 0 to 9
- _point_: will turn on (true) or off (false) the point/comma on the selected digit

---

```c
void MAX7219_setDecode(void)
```
Turns on the Decode Mode (BCD Code B).  
From this moment you can't use the user-defined font and you must use only the MAX7219_send function for writing numbers from 0 to 9 and H,E,L,P letters and minus sign.  

---

```c
void MAX7219_setNoDecode(void)
```
Turns on the No-Decode mode (you must use chars and numbers as defined in the library header).  

---

```c
void MAX7219_setIntensity(uint8_t val)
```
Set the display brightness.  
- _val_: value from 0 (lowest) to 9 (highest)  

Note: any value higher than 9 will be converted in 9.

---

```c
void MAX7219_test(void)
```
Turns on the test mode (all leds on).   
For exiting the test mode you must recall the _MAX7219_init_ function since the test mode overwrites all settings.  

---

```c
void MAX7219_shutdown(bool yesno)
```
Turns on or off the display visualization.  
- _yesno_: true=no display visualization. false=display is on.  

Note: shift register content is not deleted, so digits data are retained.  

---

```c
void MAX7219_puts(const char *s, bool cursor)
```
Writes a string on the display using the font defined in the library header, using or no the cursor visual effect, from left to right.  
- _s_: string to write (ex.: "hello")
- _cursor_: true=use the cursor effect, false=string appears on the digits without visual effects  

---

```c
uint8_t MAX7219_putun(uint16_t num, uint8_t pointPos, uint8_t rightspace)
```
Writes a unsigned 16bit integer by eventually turning on the comma/point on a certain digit and eventually leaving some space on the right if you want to put a simbol or simply left-align the number.    
- _num_: 16 bit unsigned integer (0 to 65535).
- _pointPos_: digit where turning on the comma/point, from 1 to 8. Put 0 if you don't want to turn on the point.
- _rightspace_: places to leave free on the most-right position. Put 0 if you want to right-align the number.  

Returns: the number of digits printed (used by _MAX7219_putsn_ for writing the minus sign near the most-left digit).  
Note: _pointPos_ is intenedd to be used for fixed-point decimal notation. So if you must print a decimal number, you can multiply it by a power of 10 to transform it in an integer and then turn on the point/comma on the desidered digit.    

---

```c
void MAX7219_putsn(int16_t num, uint8_t pointPos, uint8_t rightspace)
```
As above but writes a 16bit signed integer by putting a minus sign near the most-left digit.  
- _num_: 16 bit signed integer (-32768 to 32767).
- _pointPos_: digit where turning on the comma/point, from 1 to 8. Put 0 if you don't want to turn on the point.
- _rightspace_: places to leave free on the most-right position. Put 0 if you want to right-align the number.  

Note: _pointPos_ is intenedd to be used for fixed-point decimal notation. So if you must print a decimal number, you can multiply it by a power of 10 to transform it in an integer and then turn on the point/comma on the desidered digit.    

