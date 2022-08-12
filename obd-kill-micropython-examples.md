# OBD-Kill MicroPython Examples

### 5. Examples

### Pin

&#x20;A pin object is used to control I/O pins (also known as GPIO - general-purpose input/output). Pin objects are commonly associated with a physical pin that can drive an output voltage and read input voltages. The pin class has methods to set the mode of the pin (IN, OUT, etc) and methods to get and set the digital logic level. For analog control of a pin, see the ADC class.

A pin object is constructed by using an identifier which unambiguously specifies a certain I/O pin. The allowed forms of the identifier and the physical pin that the identifier maps to are port-specific. Possibilities for the identifier are an integer, a string or a tuple with port and pin number.

```python
from machine import Pin

# create an output pin on pin #0
p0 = Pin(0, Pin.OUT)

# set the value low then high
p0.value(0)
p0.value(1)

# create an input pin on pin #2, with a pull up resistor
p2 = Pin(2, Pin.IN, Pin.PULL_UP)

# read and print the pin value
print(p2.value())

# reconfigure pin #0 in input mode with a pull down resistor
p0.init(p0.IN, p0.PULL_DOWN)

# configure an irq callback
p0.irq(lambda p:print(p))
```

Pin values can be set; however, Signals are easier to control

### Signal (use for LEDs and Buttons)

[https://docs.micropython.org/en/latest/library/machine.Signal.html](https://docs.micropython.org/en/latest/library/machine.Signal.html)

The Signal class is a simple extension of the Pin class. Unlike Pin, which can be only in “absolute” 0 and 1 states, a Signal can be in “asserted” (on) or “deasserted” (off) states, while being inverted (active-low) or not. In other words, it adds logical inversion support to Pin functionality. While this may seem a simple addition, it is exactly what is needed to support wide array of simple digital devices in a way portable across different boards, which is one of the major MicroPython goals. Regardless of whether different users have an active-high or active-low LED, a normally open or normally closed relay - you can develop a single, nicely looking application which works with each of them, and capture hardware configuration differences in few lines in the config file of your app.

The following example shows how to create signals to control the inner LED on both eyes of the OBD-Kill

```python
from machine import Signal
import utime
 
rightMiddlePin = Pin(10, Pin.OUT)
 
rightMiddleLED = Signal(rightMiddlePin)
 
# .can combine constructor to one 
leftMiddleLED = Signal(Pin(26, Pin.OUT)
 
rightMiddleLED.on()
leftMiddleLED.off()
 
# .value() returns the status of the LED (On=1, Off=0)
print(rightMiddleLED.value()) # will print 1
print(leftMiddleLED.value()) # will print 0
 
 
# alternate flashing each center eye LED every second
while True:
  rightMiddleLED.on()
  leftMiddleLED.off()
  utime.sleep_ms(1000)
   
  rightMiddleLED.on()
  leftMiddleLED.off()
  utime.sleep_ms(1000)
```

### PWM – Pulse Width Modulation

This class provides pulse width modulation output&#x20;

[https://docs.micropython.org/en/latest/library/machine.PWM.html](https://docs.micropython.org/en/latest/library/machine.PWM.html)

Here is a Map that contains the pitches and their corresponding frequencies. See the next example to see how to access the frequency.

```python
tones = {"B0": 31, "C1": 33, "CS1": 35, "D1": 37, "DS1": 39, "E1": 41, "F1": 44, "FS1": 46, "G1": 49, "GS1": 52,
"A1": 55, "AS1": 58, "B1": 62, "C2": 65, "CS2": 69, "D2": 73, "DS2": 78, "E2": 82, "F2": 87, "FS2": 93, "G2": 98,
"GS2": 104, "A2": 110, "AS2": 117, "B2": 123, "C3": 131, "CS3": 139, "D3": 147, "DS3": 156, "E3": 165, "F3": 175,
"FS3": 185, "G3": 196, "GS3": 208, "A3": 220, "AS3": 233, "B3": 247,"C4": 262, "CS4": 277, "D4": 294, "DS4": 311,
"E4": 330, "F4": 349, "FS4": 370, "G4": 392, "GS4": 415, "A4": 440, "AS4": 466, "B4": 494, "C5": 523, "CS5": 554,
"D5": 587, "DS5": 622, "E5": 659, "F5": 698, "FS5": 740, "G5": 784, "GS5": 831, "A5": 880, "AS5": 932, "B5": 988,
"C6": 1047, "CS6": 1109, "D6": 1175, "DS6": 1245,"E6": 1319, "F6": 1397, "FS6": 1480, "G6": 1568, "GS6": 1661,
"A6": 1760, "AS6": 1865, "B6": 1976, "C7": 2093, "CS7": 2217, "D7": 2349, "DS7": 2489, "E7": 2637, "F7": 2794,
"FS7": 2960, "G7": 3136, "GS7": 3322, "A7": 3520, "AS7": 3729, "B7": 3951, "C8": 4186, "CS8": 4435, "D8": 4699,
"DS8": 4978, "rest": -1}py
```

The duty cycle is set by using the .duty\_u16() method. This value is divided by 65535 to calculate the duty cycle. A larger number corresponds with a louder volume.

{% hint style="info" %}
### Warning

Use caution when programming large duty cycles. Loud sounds may cause hearing damage. For these examples, the duty cycle will never exceed 1000.
{% endhint %}

```python
buzzer.freq(tones["C5"]) # set the frequency to match pitch C5
buzzer.duty_u16(2000) # set the duty cycle for the pulses to be 1000/65535=1.5%
```

Increase the volume (duty cycle) for 3 seconds, hold for 3 seconds, decrease the volume for 3 seconds

```python
for i in range(0, 1000):
        buzzer.duty_u16(i)
        utime.sleep_ms(3)
 
utime.sleep_ms(3000)
 
for i in reversed(range(0, 1000)):
        buzzer.duty_u16(i)
        utime.sleep_ms(3)
```

Create a serial channel
