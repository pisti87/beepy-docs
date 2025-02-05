---
sidebar_position: 2
---

# Power Management & Battery

The onboard RP2040 controls the power to the Pi, as well as measuring the battery level with its ADC. The Beepy can be safely shutdown by holding the "End Call" button on the keyboard.

To battery voltage on the Beepy can read from the register ```0x17``` over I2C. This is a read-only register, it is 2 bytes in size. It returns a 16 bit value from the ADC (VREF = 3.3V). There is a voltage divider so the battery voltage can be calculated as VBAT = 3.3V * (value/4095) * 2.

## Examples

### Battery Level Reporting

The following sysfs entries are available under `/sys/firmware/beepy` to read the system battery level:

- `battery_raw`: raw numerical battery level as reported by firmware. Read-only
- `battery_volts`: battery voltage estimation. Read-only
- `battery_percent`: battery percentage estimation. Read-only

## Script

The following script is an example to calculate a voltage estimation from the raw battery level:

```
#!/bin/sh

V=$(cat /sys/firmware/beepy/battery_raw)

V=$(echo "obase=10; ibase=16; $V" | bc)
echo "$V * 3.3 * 2 / 4095" | bc -l | cut -c1-5
```

### Sleep/Wake

To Do
When powered off and in standby the Raspberry pi can consume 1.29 Watts of power. 
It will sit there with the power Led lit and the SoC powered up ready. 
The reason for this that some  HATs have an issue with the 3.3v rail begin powered off, 
  but the 5V, passed through from the USB powered supply begin active.
What if we could lower our power consumption at standby by 185% with just one line of code?

1. Open a terminal and run this command.
 ```
   sudo rpi-eeprom-config --edit
   ```
2. Scroll down and change the following line.
  ```
  POWER_OFF_ON_HALT=0
  Change to
  POWER_OFF_ON_HALT=1
```
3. Press CTRL + X,Y and then ENTER to save and exit
4. Reboot the Raspberry Pi device to write the changes to the EEPROM.
   When powered off, your device will now consume much less power in standby.
