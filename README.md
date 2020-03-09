# MAX7219 PIC lib
A library for MAX7219, 7-segments display and Microchip PIC microcontrollers.  

### Functions

```c
void MAX7219_init(uint8_t digitN, uint8_t decodeM)
```
Initialize the MAX7219
- digitN : number of digits you've on the display, from 1 to 8
- decodeM: decode mode, possible values: DECODE (max7219 will use BCD code B), NODECODE  

```c
void MAX7219_clear(void)
```
Clears all digits (both decode and nodecode modes). Display remains turned on, only internal shift register is cancelled.  

```c
void MAX7219_clearc(void)
```
clears all digits using the cursor effect (from left to right). Only for the nodecode mode. Display remains turned on, only internal shift register is cancelled.


```c
void MAX7219_send(uint8_t reg, uint8_t dat)
```
Send a byte to the register
- reg: register to write in
- dat: data to write in the register  

Note: This function is used also for writing chars in the nodecode mode (ex.: MAX7219_send(8,'A') => writes A letter on the 8th digit)

```c
void MAX7219_char(uint8_t digit, uint8_t ch, bool point)
```
Puts a single char
- digit: digit number to write on (from 1 to 8)
- ch: char to write (ex.: 'g', '1', '.' ecc)
- point: will turn on (true) or off (false) the point/comma on the selected digit  

Note: you can use both uppercase and lowercase letters, in every case letters will be converted to use the font I've defined. You can write all letters but W,X and Z that can't be effectively rendered on 7-segments displays.

```c
void MAX7219_numch(uint8_t digit, uint8_t n, bool point)
```
Puts a number as char
- digit: digit number to write on (from 1 to 8)
- n: number to write, inserted as integer (from 0 to 9)
- point: will turn on (true) or off (false) the point/comma on the selected digit

```c
void MAX7219_setDecode(void)
```
Turns on the Decode Mode (BCD Code B)  

```c
void MAX7219_setNoDecode(void)
```
Turns on the No-Decode mode (you must use your own defined chars and numbers)  

```c
void MAX7219_setIntensity(uint8_t val)
```
Set the display luminosity
- val: value from 0 (lowest luminosity) to 9 (highest luminosity)  

```c
void MAX7219_test(void)
```
Turns on the test mode (all leds on).   
For exiting the test mode you must recall the display init function since the test mode overwrites all settings.  

```c
void MAX7219_shutdown(bool yesno)
```
Turns on or off the display visualization.  
- yesno: true=no display visualization. false=display is on.  

Note: shift register content is not deleted.  

```c
void MAX7219_puts(const char *s, bool cursor)
```
Writes a string on the display used my font, using or no the cursor visual effect, from left to right  
- s: string to write (ex.: "hello")
- cursor: true=use the cursor effect, false=writing appears fast, without visual effects  

```c
uint8_t MAX7219_putun(uint16_t num, uint8_t pointPos, uint8_t rightspace)
```
Writes a unsigned 16bit integer by eventually turning on the comma/point on a certain digits and eventually leaving some space on the right if you want to put a simbol or simply left-align the number  
- num: 16 bit unsigned integer from 0 to 65535
- pointPos: digit where turning on the comma/point, from 1 to 8. Put 0 if you don't want to turn on the point.
- rightspace: places to leave free on the right. Put 0 if you want to right-align the number  
Returns the number of digits printed (used by putsn for writing the minus sign near the most-left digit).  
Note: pointPos is used for fixed-point decimal notation.  

```c
void MAX7219_putsn(int16_t num, uint8_t pointPos, uint8_t rightspace)
```
As above but writes a 16bit signed integer by putting a minus sign near the most-left digit  
- num: 16 bit signed integer from from -32768 to 32767
- pointPos: digit where turning on the comma/point, from 1 to 8. Put 0 if you don't want to turn on the point.
- rightspace: places to leave free on the right. Put 0 if you want to right-align the number  

Note: pointPos is used for fixed-point decimal notation.  
