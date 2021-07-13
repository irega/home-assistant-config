# ATTENTION: This project is officially closed!
Since this project was based on the work done by [Alberto Geniola](https://github.com/albertogeniola/MerossIot), since he has released a [homeassistant component](https://github.com/albertogeniola/meross-homeassistant), this project make no sense to be maintained. I suggest to move on that component.

# hassio_meross_sensor_switch
- A Home Assistant (HA) custom-component for Meross devices, based 
on the work done by [Alberto Geniola](https://github.com/albertogeniola/MerossIot) and [Chris Hurst](https://github.com/hurstc/hassio-meross)
- This custom-component adds the switch and sensor capabilities of your Meross device in HA as shown below:

- Example of switches (labels are in Italian):<br/>
<img src="res/switches.png" alt="Switches" />

- Example of electricity sensors (labels are in Italian):<br/>
<img src="res/sensors.png" alt="Sensors" />

Devices
============

The custom-component is intended to manage the [Meross smart outlets](https://www.meross.com/product?category_id=4). 
Other types of devices (e.g. 
[bulbs](https://www.meross.com/product?category_id=5), 
[garage door opener](https://www.meross.com/product?category_id=8), etc.) 
**are not currently supported**. 

The custom-component has been tested with [mss310](https://www.meross.com/product/6/article/) and 
[mss210](https://www.meross.com/product/3/article/) version 2.0.0 smart outlets.
However, refer to [Alberto Geniola](https://github.com/albertogeniola/MerossIot) for the full compatibility list.

The custom-component will discover the Meross devices associated to your Meross account and will add switches and 
sensors (measuring the electricity: power, voltage and current) in HA.
  

Install
============

1. Copy all the `.py` and `manifest.json` files into your `/config/custom_components/meross` folder.
- Your configuration should look like:
```
config
└── custom_components
    └── meross
        └── __init__.py
        └── manifest.json
        └── switch.py
        └── sensor.py
```

2. Remember to **reboot** Hassio (or Home Assistant)

Dependencies
============
This custom-component relies on the python meross_iot library developed by [Alberto Geniola](https://github.com/albertogeniola/MerossIot).
- On [Hassio](https://www.home-assistant.io/hassio/) (i.e., Home Assistant for Raspberry pi) the 
[meross_iot](https://github.com/albertogeniola/MerossIot) framework will be downloaded automatically;
- Note that if you forgot to copy the `manifest.json`, Hassio will not be able to download automatically its dependencies
- On other [Home Assistant](https://www.home-assistant.io/getting-started/) installations, if the dependencies fails to 
be loaded, install it [manually](https://github.com/albertogeniola/MerossIot#installation). 



Configuration
============

Add your credentials to `configuration.yaml`:
- `username` and `password` are **mandatory**
- `scan_interval` is **optional**. It must be a positive integer number. It represents the seconds between two consecutive scans to gather new values of Meross devices' sensors and switches. The default value is 10 seconds. 
- `meross_devices_scan_interval` is **optional**. It must be a positive integer number. It represents the seconds between two consecutive scans to update the list of available Meross devices. The default value is 900 seconds (15 minutes). 

For example:
```
meross:
  username: !secret meross_userame
  password: !secret meross_password
  scan_interval: 10
  meross_devices_scan_interval: 900
```

Performances
============
Consider that the custom-component works using a **polling strategy**: it is a time-driven not an event-driven system. 
It means that there will be always a **small delay** between an action and the result of that action.

In particular:
- acting a on/off switch on HA should result in an (almost) instantaneous effect on the device and the Meross mobile App;
- acting a on/off switch on the Meross mobile App, should result in an (almost) instantaneous effect on the device, but you have to wait up to `scan_interval` seconds before it updates on HA;
- electricity values (power, voltage, currant) are updated approx each `scan_interval` seconds before it updates on HA;
- unplugging a device will be detected after several `scan_interval` cycles (normally less than a minute);
- plugging in a device will be detected within `scan_interval` seconds;
- registering a new device (to the associated Meross account) will be detected within `meross_devices_scan_interval` seconds;
- unregistering a device (from the associated Meross account) will be detected after a HA reboot.

Debug
============

- To enable debug diagnostics, add this to your `configuration.yaml`:
```
logger:
  default: WARNING
  logs:
    meross_sensor: DEBUG
    meross_switch: DEBUG
    meross_init: DEBUG
```

