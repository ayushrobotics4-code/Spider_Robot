# Spider_Robot
It's a DIY spider Robot kit Quadruped 12 servo motor with 3 DOF in each leg. The Spider robots chassis is Usually 3D printed but in my case I choose to implement my DIY skills and made the chassis through ice cream sticks.



# Wiring and Pin connection

Power Distribution Logic


​Before connecting your ESP32 or PCA9685, use a multimeter to adjust the LM2596 screw potentiometer until the output reads 5V or 6V (depending on the amount needed for your spider robot and the Amount u are providing) 

Component Pin Connected To Type
Battery (2S) Pos/Neg Switch & LM2596 Input Source
LM2596 Out+ 5V-6V ESP32 Vin & PCA9685 V+ Main Power
LM2596 Out- GND ESP32 GND & PCA9685 GND Common Ground
HC-SR04 Trig ESP32 GPIO 5 Signal
HC-SR04 Echo ESP32 GPIO 18 Signal
PCA9685 SDA ESP32 GPIO 21 I2C Data
PCA9685 SCL ESP32 GPIO 22 I2C Clock



⚠️ Critical Note :





Voltage Regulation: The LM2596 must be set to 5V-6V. Higher voltage may burn out the servos; lower voltage will cause "jittering" or resets.


​Common Ground: Ensure the ESP32 GND and the LM2596 OUT- (GND) are connected. Without a common ground, the I2C signals to the PCA9685 will fail.


​Current Draw: 12 servos can pull over 2A during movement. Ensure your batteries are high-drain (like 18650s) and your buck converter is rated for at least 3A.


Additional Note:
If you notice the ESP32 resetting when the robot starts to walk, it's because the servos are pulling too much "surge" current. Adding a large capacitor (e.g., 1000\mu F) across the PCA9685 V+ and GND pins can help stabilize the power.
