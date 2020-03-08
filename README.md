# MAX7219 PIC lib
A library for MAX7219, 7-segments display and Microchip PIC microcontrollers.  

### Functions

```c
void MAX7219_init(uint8_t digitN, uint8_t decodeM)
```

```c
void MAX7219_clear(void)
```

```c
void MAX7219_clearc(void)
```

```c
void MAX7219_send(uint8_t reg, uint8_t dat)
```

```c
void MAX7219_char(uint8_t digit, uint8_t ch, bool comma)
```

```c
void MAX7219_numch(uint8_t digit, uint8_t n, bool comma)
```

```c
void MAX7219_setDecode(void)
```

```c
void MAX7219_setNoDecode(void)
```

```c
void MAX7219_setIntensity(uint8_t val)
```

```c
void MAX7219_test(void)
```

```c
void MAX7219_shutdown(bool yesno)
```

```c
void MAX7219_puts(const char *s, bool cursor)
```

```c
uint8_t MAX7219_putun(uint16_t num, uint8_t pointPos, uint8_t rightspace)
```

```c
void MAX7219_putsn(int16_t num, uint8_t pointPos, uint8_t rightspace)
```
