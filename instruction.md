# MultiWii Configuration Instructions

Based on your parts list, here are separate instructions for the transmitter and drone controller board setup.

## Parts List Summary

**Drone Controller (Receiver)**:
- Arduino NANO
- NRF24L01 radio module
- MPU6050 (gyro/accelerometer)
- 4x brushed coreless DC motors
- 4x SI2302 MOSFETs
- 4x 1N5819 Schottky diodes
- 4x 1K resistors
- 5x 100uF capacitors
- 3.7V LiPo battery

**Transmitter**:
- Arduino NANO
- NRF24L01+PA radio module
- Game controller/joysticks
- 7.4V battery
- 3.3V regulator

---

# PART 1: TRANSMITTER SETUP

## 1. Transmitter Hardware Wiring

**Arduino NANO to NRF24L01+PA**:
```
NRF24    Arduino NANO
VCC   -> 3.3V (via regulator)
GND   -> GND
CE    -> Pin 9
CSN   -> Pin 10
SCK   -> Pin 13
MOSI  -> Pin 11
MISO  -> Pin 12
```

**Controller/Joysticks to Arduino**:
```
Right Stick X (Roll)     -> A0
Right Stick Y (Pitch)    -> A1
Left Stick X (Yaw)       -> A2
Left Stick Y (Throttle)  -> A3
AUX switches             -> A4, A5, etc.
```

## 2. Transmitter Code Configuration

The transmitter doesn't use MultiWii - it needs custom NRF24 transmitter code to read joysticks and send RC data.

---

# PART 2: DRONE CONTROLLER SETUP

## 1. Basic MultiWii Configuration

Edit `config.h` for your nano quadcopter:

**Choose frame type**:
```cpp
#define QUADX      // X configuration
```

**Configure for Arduino NANO**:
```cpp
// No specific board define needed for NANO
// Configure individual components instead
```

**Configure sensors**:
```cpp
#define MPU6050    // Your gyro/accelerometer
```

**Configure receiver**:
```cpp
// Since you're using NRF24, you'll need custom serial RX
#define SERIAL_RX
#define RX_SERIAL_PORT 0
```

## 2. Hardware Wiring (Drone Controller)

**Arduino NANO to MPU6050**:
```
MPU6050   Arduino NANO
VCC    -> 3.3V
GND    -> GND
SDA    -> A4
SCL    -> A5
```

**Arduino NANO to NRF24L01**:
```
NRF24    Arduino NANO
VCC   -> 3.3V
GND   -> GND
CE    -> Pin 7
CSN   -> Pin 8
SCK   -> Pin 13
MOSI  -> Pin 11
MISO  -> Pin 12
```

**Motor Control (via SI2302 MOSFETs)**:
```
Motor 1 (Front Right) -> Pin 3
Motor 2 (Back Right)  -> Pin 5
Motor 3 (Back Left)   -> Pin 6
Motor 4 (Front Left)  -> Pin 9
```

**MOSFET Driver Circuit (per motor)**:
```
Arduino Pin -> 1K Resistor -> SI2302 Gate
SI2302 Source -> GND
SI2302 Drain -> Motor Negative
Motor Positive -> Battery Positive
1N5819 Diode across motor (flyback protection)
100uF capacitor near each motor
```

## 3. Power System

**Voltage Regulation**:
```
3.7V LiPo -> HT7333 -> 3.3V for Arduino/sensors
3.7V LiPo -> Direct to motors (brushed motors can handle 3.7V)
```

## 4. Software Modifications Needed

**Important**: Standard MultiWii expects PWM RC input, but you're using NRF24 serial communication. You'll need to:

1. **Modify RX.cpp** to read NRF24 serial data instead of PWM
2. **Add NRF24 library** and receiver code
3. **Configure motor outputs** for brushed motors (different from brushless ESCs)

## 5. Upload and Test Procedure

1. **Flash transmitter code** to transmitter Arduino
2. **Flash modified MultiWii** to drone Arduino
3. **Test radio communication** first
4. **Calibrate MPU6050** on level surface
5. **Test motor outputs** (props off!)
6. **Verify motor directions** match QUADX configuration
7. **Test flight controls** and adjust PID values

## Important Notes for Nano Quad:

- **Brushed motors** require different ESC handling than brushless
- **NRF24 range** is limited - stay close during testing
- **3.7V direct drive** to motors - no ESCs needed
- **Very lightweight** - PID values will need significant tuning
- **Power consumption** - monitor battery voltage closely

## Wiring Diagram Needed:

w

## TO HAVE IN MIND (problems)

- If the Arduino resets itself, add a bigger capacitor at the input.
- If radio connection is bad, I've soldered a thin wire to the NRF24 PCB.
- Try to remove any extra weight.
- Use small 3.7V battery, 200mAh
- Before the flight, test with the Java platform the IMU data, and the received values for each channel.

