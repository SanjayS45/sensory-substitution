# ESP32 PCA9685 Motor Controller

Firmware for an ESP32-WROOM driving vibration motors through a PCA9685 PWM controller and ULN2803A low-side driver.

The PCA9685 should drive the ULN2803A inputs, not motors directly. Tie ESP32, PCA9685, ULN2803A, and motor-supply grounds together.

## Wiring

- ESP32 `3.3V` to PCA9685 `VCC`
- ESP32 `GND` to PCA9685 `GND`
- ESP32 `GPIO21` to PCA9685 `SDA`
- ESP32 `GPIO22` to PCA9685 `SCL`
- PCA9685 `OE` to `GND` or otherwise held low
- PCA9685 `OUT0` through `OUT15` to two ULN2803A input banks, or to as many driver inputs as you have populated
- ULN2803A outputs to each motor negative terminal
- Motor positive terminals to motor supply positive
- ULN2803A `GND` to common ground
- ULN2803A `COM` to motor supply positive so the internal clamp diodes can catch motor kickback

For each DIP ULN2803A, the pin mapping is:

- `IN1` pin 1 -> `OUT1` pin 18
- `IN2` pin 2 -> `OUT2` pin 17
- `IN3` pin 3 -> `OUT3` pin 16
- `IN4` pin 4 -> `OUT4` pin 15
- `IN5` pin 5 -> `OUT5` pin 14
- `IN6` pin 6 -> `OUT6` pin 13
- `IN7` pin 7 -> `OUT7` pin 12
- `IN8` pin 8 -> `OUT8` pin 11
- `GND` pin 9 -> common ground
- `COM` pin 10 -> motor supply positive

The ULN2803A is a Darlington sink array, so each motor will see less than the full motor supply voltage. For high-current motors or maximum vibration strength, logic-level MOSFETs are usually better.

If the serial monitor says the PCA9685 is detected but motors do not move, check the ULN2803A side with a multimeter:

- PCA9685 output pin should rise when a channel is on.
- Matching ULN2803A output pin should pull low when that input is high.
- Motor positive should stay at the motor supply voltage.
- Motor negative should be connected to the matching ULN2803A output, not ground directly.

## Commands

```powershell
python -m platformio run
python -m platformio run --target upload
python tools\serial_console.py
```

The firmware retries PCA9685 detection every 2 seconds and only runs motor patterns after the board responds at I2C address `0x40`.

Serial monitor commands:

```text
help
status
scan
test
pattern
off
all 4095
m 0 4095 2000
m 15 4095 2000
```
Sensory Substitution Vest Signal / Power Flow
VISUAL PROCESSING PIPELINE
OAK-D Lite Camera ↓ Captures RGB + Depth Data ↓ ESP32 Microcontroller ↓ Processes Visual Input / Downscales to 8×8 Representation ↓ Computes Desired Intensity for Each Motor ↓ Sends PWM Duty Cycle Commands via I2C ↓ 4× PCA9685 PWM Controllers ↓ Generate 64 Independent PWM Control Signals ↓ Motor Driver Stage ↓ Amplifies PWM Control Signals into High-Current Outputs ↓ 64× Coin Vibration Motors ↓ Spatial Tactile Feedback on User’s Back

POWER DELIVERY PIPELINE
1S LiPo Battery ↓ Power Regulation / Distribution Circuit ↓ Supplies: • ESP32 • PCA9685 Controllers • Motor Driver Stage

Battery Motor Rail ↓ Motor Driver Supplies Current to Coin Motors

PER-MOTOR SIGNAL / POWER PATH
PCA9685 PWM Output ↓ Motor Driver Input ↓ Motor Driver Modulates Battery Power ↓ Battery → Motor Driver → Coin Motor → Ground ↓ Motor Vibrates with Intensity Proportional to PWM Duty Cycle

COMPONENT RESPONSIBILITIES
OAK-D Lite Camera: Captures environmental visual/depth information.

ESP32: Processes camera output and determines motor activation levels.

PCA9685: Generates precise PWM control signals for each motor channel.

Motor Driver: Converts low-power PWM signals into high-current outputs suitable for driving vibration motors.

Battery: Supplies electrical power for the system.

Coin Motors: Convert electrical power into tactile vibration feedback.
