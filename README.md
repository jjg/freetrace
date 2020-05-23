# freetrace

A device which provides the wearer a privacy-respecting way to monitor and share their risk of contracting or transmitting COVID-19 (or other communicable diseases) based on contact with others.

## Distinguishing Features

* **Privacy:** Unlike app-based contact tracing using smartphones, freetrace requires no Internet connection and knows absolutely no personal information about the user other than their diagnosis status
* **Accessibility:** Freetrace requires no special skills or experience to operate and is language-agnostic
* **Reliability:** Freetrace has no screen, no glass and only one connector (to charge the battery) which makes it possible to be constructed in a much more durable way than a smartphone 
* **Cost:** Freetrace's hardware far less inexpensive than a smartphone, making it practical for a much wider range of people (including children)

## Usage

The device is worn (or carried, depending on the exact implementation) when the user leaves their residence.  When the wearer's risk crosses a threshold this is indicated by illuminating the corresponding LED (for example: red = isolate, yellow = only essential interactions, green = no risk, etc. Exact thresholds have not yet been defined, see below).  When the wearer is in an area of heightened risk, status is displayed in "real-time", allowing the wearer to alter their location temporarilly to reduce risk of transmission.

If the wearer has been diagnosed, a switch on the device is used to indicate this and the device will broadcast the wearers status to reflect maximum risk until the wearer has recovered from the infection.

>Internally, status based on the number of minutes since contact (direct or indirect) with a diagnosed person.  Ranges can be established to provide recommendations for behavior changes to reduce the chances of spreading or contracting the infection.



## Theory of Operation

Each device keeps a table of BLE MAC addresses that have been seen over the last 2 weeks.

| mac | status|
|-----|-------|
| 6e:3d:f0:a0:00:36 | 1-20160 |

Once per minute the device recomputes its status using the following steps:

1. Subtract 1 from the status value of each entry in the contacts list
2. Remove any entries with a status of 0 or less
3. If the "diagnosed" switch is set, set this device's status to 20106 (20106 minutes = 2 weeks); otherwise set the device status to the highest contact status

When one device ("A") detects another device ("B") the following occurs:

1. Device A adds device B's MAC address and status to its list
2. Device B adds device A's MAC address and status to its list


## Proposed Implementation (PROTOTYPE)

The current design is based around the [ESP32](https://en.wikipedia.org/wiki/ESP32) microcontroller.  The [Adafruit HUZZAH32 Feather](https://learn.adafruit.com/adafruit-huzzah32-esp32-feather) has been selected for its built-in power management.  Additional hardware includes a li-poly battery, LED's to indicate status, a switch to signal "diagnosed" status and a button to activate the display when not triggered by other events.

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

* [Arduino ESP32 BLE Scanner](https://github.com/moononournation/Arduino_BLE_Scanner)
* [Adafruit HUZZAH32](https://learn.adafruit.com/adafruit-huzzah32-esp32-feather)

