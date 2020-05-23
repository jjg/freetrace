# freetrace

A device which provides the wearer a privacy-respecting way to monitor and share their risk of contracting or transmitting COVID-19 (or other communicable diseases) based on contact with others.

Status is determined internally to the device as a number based on the number of minutes since contact (direct or indirect) with a diagnosed person.  Ranges can be established to provide recommendations for behavior changes to reduce the chances of spreading or contracting the infection.

"real-time" feedbac can also be provided by indicating when other devices are present based on their current status value.

If the wearer has been diagnosed, a switch on the device is used to indicate this and the device will set the wearers status to reflect maximum risk until the wearer has recovered from the infection.


## Theory of Operation

Each device keeps a table of BLE MAC addresses that have been seen over the last 2 weeks.

| mac | status|
|-----|-------|
| (ex. MAC) | 1-20160 |

Once per minute the device recomputes its status using the following steps:

1. Subtract 1 from the status value of each entry in the contacts list
2. Remove any entries with a status of 0 or less
3. If the "diagnosed" switch is set, set this device's status to 20106 (20106 minutes = 2 weeks); otherwise set the device status to the highest contact status

When one device ("A") detects another device ("B") the following occurs:

1. Device A adds device B's MAC address and status to its list
2. Device B adds device A's MAC address and status to its list


## Proposed Implementation (PROTOTYPE)

The current design is based around the [ESP32]() microcontroller.  The [Adafruit Huzza Feather]() has been selected for its built-in power management.  Additional hardware includes a li-poly battery, LED's to indicate status, a switch to signal "diagnosed" status and a button to activate the display when not triggered by other events.

The device firmware is written using the Arduino IDE and ESP32 module.  The main loop is outlined below in pseudocode below:

```
wake-up
scan for freetracer devices
(if devices are found, enable real-time feedback)
update contact table
compute status
(if status crosses a risk threshold, indicate on display)
broadcast curren status
```

## Notes

* The 2 week "decay period" is just a guess and could be changed based on input from experts (it could even be nonlinear)
* A "transmission factor" could be applied to attenuate a contacts status value based on the likelyhood of transmission (again, expert feedback is needed here)


## References

* [Nano ESP32 BLE Scanner]()


