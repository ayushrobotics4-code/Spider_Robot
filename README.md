# Spider_Robot
It's a DIY spider Robot kit Quadruped 12 servo motor with 3 DOF in each leg. The Spider robots chassis is Usually 3D printed but in my case I choose to implement my DIY skills and made the chassis through ice cream sticks.



# Wiring and Pin connection

Power Distribution Logic


​Before connecting your ESP32 or PCA9685, use a multimeter to adjust the LM2596 screw potentiometer until the output reads 5V or 6V (depending on your servo rating).





Battery Holder




(+) Positive




Switch (Pin 1)




2x 18650 Li-ion batteries






Switch




Pin 2




LM2596 IN+




Main Power Toggle






Battery Holder




(-) Negative




LM2596 IN-




Common Ground Start






LM2596 OUT+




Output (5V)




PCA9685 V+




Servo Power (High Current)






LM2596 OUT+




Output (5V)




ESP32 Vin




Logic Power






LM2596 OUT-




Output (GND)




PCA9685 GND




System Ground






LM2596 OUT-




Output (GND)




ESP32 GND




System


Control Logic and Component Wiring




HC-SR04




VCC




Power Rail (5V)




-






 




Trig




Signal




GPIO 5






 




Echo




Signal




GPIO 18






 




GND




Ground Rail




-






PCA9685




VCC




Logic Power




3.3V (or 5V)






 




SDA




I2C Data




GPIO 21






 




SCL




I2C Clock




GPIO 22






 




GND




Logic Ground




GND






 




V+




LM2596 OUT+




High Current Rail



Servo Mapping ( PCA9685)




Channel 0, 1, 3, 4




Attack Legs




attackStance() (Moves to 160°)






Channel 6, 7, 9, 10




Walk Legs




moveBackward() (Walking gait)






Channels 0 - 11




All Joints




robotStand



⚠️ Critical Note :





Voltage Regulation: The LM2596 must be set to 5V-6V. Higher voltage may burn out the servos; lower voltage will cause "jittering" or resets.


​Common Ground: Ensure the ESP32 GND and the LM2596 OUT- (GND) are connected. Without a common ground, the I2C signals to the PCA9685 will fail.


​Current Draw: 12 servos can pull over 2A during movement. Ensure your batteries are high-drain (like 18650s) and your buck converter is rated for at least 3A.


Additional Note:
If you notice the ESP32 resetting when the robot starts to walk, it's because the servos are pulling too much "surge" current. Adding a large capacitor (e.g., 1000\mu F) across the PCA9685 V+ and GND pins can help stabilize the power.
