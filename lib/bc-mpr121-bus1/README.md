# bc-mpr121-bus1

This is the bare conductive mpr121 library but configured for bus1 on wire 

https://github.com/BareConductive/mpr121

# For multiple boards:

In the library files, there's a **pre-instantiated global object** created. If you want to use multiple MPR121 boards then you will need to make your own objects instead.

**In MPR121.h (line 468):**
```cpp
extern MPR121_type MPR121;
```

**In MPR121.cpp (line 964):**
```cpp
MPR121_type MPR121 = MPR121_type();
```

The library authors created a single global instance called `MPR121` to make it easier for beginners. However, **you absolutely CAN create your own instances with different names** for multiple MPR121 boards!

## Solution for Multiple MPR121 Boards

Create your own instances like this:

```cpp
#include <bc-mpr121-bus1.h>
#include <Wire.h>

// Create named instances for each board
MPR121_type sensor1 = MPR121_type();
MPR121_type sensor2 = MPR121_type();
MPR121_type sensor3 = MPR121_type();

const uint8_t MPR121_ADDR_1 = 0x5A;
const uint8_t MPR121_ADDR_2 = 0x5B;
const uint8_t MPR121_ADDR_3 = 0x5D;

const uint8_t MPR121_INT_1 = 4;
const uint8_t MPR121_INT_2 = 5;
const uint8_t MPR121_INT_3 = 6;

void setup() {
  Serial.begin(115200);
  Wire.begin();

  // Initialize first sensor
  if (!sensor1.begin(MPR121_ADDR_1)) {
    Serial.println("Sensor 1 initialization failed");
  }
  sensor1.setInterruptPin(MPR121_INT_1);
  sensor1.setTouchThreshold(40);
  sensor1.setReleaseThreshold(20);

  // Initialize second sensor
  if (!sensor2.begin(MPR121_ADDR_2)) {
    Serial.println("Sensor 2 initialization failed");
  }
  sensor2.setInterruptPin(MPR121_INT_2);
  sensor2.setTouchThreshold(40);
  sensor2.setReleaseThreshold(20);

  // Initialize third sensor
  if (!sensor3.begin(MPR121_ADDR_3)) {
    Serial.println("Sensor 3 initialization failed");
  }
  sensor3.setInterruptPin(MPR121_INT_3);
  sensor3.setTouchThreshold(40);
  sensor3.setReleaseThreshold(20);
}

void loop() {
  sensor1.updateTouchData();
  sensor2.updateTouchData();
  sensor3.updateTouchData();

  // Use each sensor independently
  if (sensor1.getTouchData(0)) {
    Serial.println("Sensor 1, electrode 0 touched");
  }
  
  if (sensor2.getTouchData(0)) {
    Serial.println("Sensor 2, electrode 0 touched");
  }
  
  if (sensor3.getTouchData(0)) {
    Serial.println("Sensor 3, electrode 0 touched");
  }
}
```

**Key points:**
- The class is `MPR121_type`
- You can create as many instances as you need
- Each instance needs a unique I2C address (the MPR121 can be configured to addresses 0x5A, 0x5B, 0x5C, or 0x5D by changing the ADD pin)
- You can still use the default `MPR121` object if you want, or ignore it completely
