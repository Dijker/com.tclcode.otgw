### Homey OpenTherm Gateway application
This is the OpenTherm Gateway application for Homey.
More information on the OpenTherm Gateway (OTG) can be found on http://otgw.tclcode.com.

### Settings
After installing the application, first visit the Homey Settings and navigate to the 'OpenTherm Gateway' application.
![](http://homey.ramonbaas.nl/otgw/to_settings.png)
#### Application configuration
##### Network
There is no automatic way of finding an OpenTherm Gateway in your network, hence you will need to show the app where it can be found.

![](http://homey.ramonbaas.nl/otgw/settings_network.png)

After entering the ip address and port number, press 'Save network settings'. The application will start searching for the OTG and once found the following message appears on the top of the settings screen.

![](http://homey.ramonbaas.nl/otgw/network_found.png)
##### Features
The following application features are available.

![](http://homey.ramonbaas.nl/otgw/settings_features.png)

When a temperature change on the thermostat device is made, either a 'TC' or 'TT' can be send to the OTG. A 'TC' will make it a permanent ('constant') change, until you change a setting on the thermostat. A 'TT' will make the temperature setting 'temporary'. In this case a program change (e.g. at 17.00 or so) will undo the settings, and the program will resume.
**Please note that not all thermostats support these commands. Check the [Equipment matrix](http://otgw.tclcode.com/matrix.cgi#thermostats "Equipment matrix") on the OTG website for message 9 and 100.**

The second option allows you to have the app send a 'SR=70:0,0' command. This results the thermostat believing that a ventilation device is supported. Some thermostats (like e.g. the Honeywell Chronotherm Modulation) can show the air humidity. But it will show it only when this option is set to 'Yes'. You can send a humidity value to the OTG via a Homey Flow card.
##### <a name="Logging"></a>Logging
All values changes that occur between master and slave can be logged in Homey Insights. However, only messages that are supported by the boiler and/or thermostat will show up in the list. When fist starting up, it will take some time before the app knows which messages give valid values. So make sure the app is running for some time before configuring the values to be logged.

![](http://homey.ramonbaas.nl/otgw/settings_logging.png)

Also note that for some values you can choose to create a device. When creating a device the value changes will by default be logged to Insights. So you could log them twice. Hence the advice is to only log values for which no device exists.

#### Gateway configuration
##### Device settings
This section will show the settings as stored in the OTG. Please refer to the [OTG website](http://otgw.tclcode.com/firmware.html#dataids "Data ids") for details. An example configuration could look like this:

![](http://homey.ramonbaas.nl/otgw/settings_device.png)

### Devices
The following Homey devices are currently supported.
* Thermostat
* Temperature sensor
* Pressure sensor

You can add a device by clicking the '+' and selecting the 'OpenTherm Gateway'

![](http://homey.ramonbaas.nl/otgw/add_device.png)

Normally you can only add one thermostat device, except if you have a second circuit and it is supported by the boiler.

![](http://homey.ramonbaas.nl/otgw/device_thermostat.png)

On the Thermostat device you change the temperature, which will either send a 'TT' or 'TC' command (depending on the setting) with the new temperature.
If the new temperature is higher or equal to the current temperature, the thermostat mode will display 'Heat'. If the new set temperature is lower, the state will show 'Cool'.
Adjusting the state to 'Cool' or 'Heat' manually has no effect. However, setting the state to 'Automatic' will send a 'TT=0' command, which results in the thermostat returning to the regular program.

Only one pressure sensor is available (Central heating water pressure). It can also be that your boiler does not support this measurement via OpenTherm, in which case no pressure sensor can be added.

Likely various temperature sensors can be added. Again, which ones are available depends on what you Boiler supports.

![](http://homey.ramonbaas.nl/otgw/device_temperature.png)

*Note*: OpenTherm messages can come by in a slow pace, which means that updates can also be slow. E.g. when setting a new temperature on the thermostat, it will not take effect immediately. So when re-opening the device it can still show an old value. There's no other remedy than patience.


### Flows
The OpenTherm app offers cards for triggers, conditions and actions. Note that also your thermostat and temperature/pressure devices can be used in flows.

![](http://homey.ramonbaas.nl/otgw/flow_status.png)
##### Actions
The following actions are available:

* Status flag changes (becomes active or inactive)
  * token 'Status': the status flag that gets active/inactive, which can be:
    * 'CHEnabled' (Central heating enable)
    * 'DHWEnabled' (Domestic hot water enable)
    * 'CoolEnabled' (Cooling enable)
    * 'OTCActive' (Outside temp. comp. active)
    * 'CH2Enabled' (Central heating 2 enable)
    * 'SummerWinter' (Summer/winter mode)
    * 'DHWBlocked' (Domestic hot water blocking)
    * 'Fault' (Fault indication)
    * 'CHMode' (Central heating mode)
    * 'DHWMode' (Domestic hot water mode)
    * 'Flame' (Flame status)
    * 'Cooling' (Cooling status)
    * 'CH2Mode' (Central heating 2 mode)
    * 'Diagnostic' (Diagnostic indication)

    ![](http://homey.ramonbaas.nl/otgw/trigger_status.png)
    ![](http://homey.ramonbaas.nl/otgw/condition_status.png)
    ![](http://homey.ramonbaas.nl/otgw/action_status.png)
* Fault flag changes (becomes active or inactive)
  * token 'Fault': the fault flag that gets active/inactive, which can be:
    * 'ServiceRequest' (Service request)
    * 'LockoutReset' (Lockout-reset)
    * 'LowWaterPressure' (Low water pressure)
    * 'GasFlame' (Gas/flame fault)
    * 'AirPressure' (Air pressure fault)
    * 'OverTemperature' (Water over-temperature)
  * token 'OEM': the OEM fault code (if supported)

    ![](http://homey.ramonbaas.nl/otgw/trigger_faultcode.png)
    ![](http://homey.ramonbaas.nl/otgw/action_faultcode.png)
* Temperature override (becomes active or inactive)
  * token 'Temperature': the override temperature, e.g. '21.00'

    ![](http://homey.ramonbaas.nl/otgw/trigger_override.png)
    ![](http://homey.ramonbaas.nl/otgw/action_override.png)
* A response is received to a command that has been sent
  * token 'Response': the full response text, e.g. 'PR=A: OpenTherm Gateway 4.2.3'
  * token 'Result': the result part only, e.g. 'OpenTherm Gateway 4.2.3'

    ![](http://homey.ramonbaas.nl/otgw/trigger_response.png)

##### Conditions
Only one condition currently exists, which is to check whether the Remote Override is (in-)active (i.e. whether a temperature has been set via the application or not).
##### Actions
The following actions are supported.

* *Send Gateway command*. This should be a command as supported by the OTG. For a list of commands see the [OTG website](http://otgw.tclcode.com/firmware.html#dataids "Data ids"). Once the command is received the trigger 'Response received' will be fired.

    ![](http://homey.ramonbaas.nl/otgw/action_command.png)
* *Set hot water mode*. The DHW (Domestic Hot Water) mode can be set to be either 'Thermostat controlled', 'Keep water hot' or 'Don't pre-heat'. You can use this to save energy when e.g. you are not home or at night, as you likely don't need hot water right away.

    ![](http://homey.ramonbaas.nl/otgw/action_dhw.png)
* *Set current thermostat time*. For thermostats that support message ids 21 and 22, you have the option to synchronize the time from Homey to the thermostat. 

    ![](http://homey.ramonbaas.nl/otgw/action_time.png)
* *Set outside temperature*. If there is a temperature sensor device in Homey that measures the outside temperature, you can use this to send that information to the thermostat. This can enable an option like 'Optimal Comfort', which can heat the room independent of the temperature measured by the thermostat itself. 

    ![](http://homey.ramonbaas.nl/otgw/action_outside.png)
* *Set humidity level*. Send a humidity percentage measurement to the thermostat, as it would be measured by a ventilation system. 

    ![](http://homey.ramonbaas.nl/otgw/action_humidity.png)

### Speech
You can ask Homey a number of questions related to the thermostat or boiler, e.g.

* "What is the room temperature?"
* "What is the target temperature?"
* "Is there a boiler problem?"
* "What is the boiler pressure?"
* "What is the boiler water temperature?"

### Insights
See the settings section on how to configure [logging](#Logging) to Homey Insights.

### API
To enable usage of the values read from the OpenTherm Gateway outside of the app, there is are 2 API function available. They can be used unauthenticated.

* `getOtgwConfig`: returns the configuration settings of the OpenTherm Gateway
* `getOtgwVars`: returns all values read from the OpenTherm Gateway

##### Examples
An example how to invoke `getOtgwVars`

* from the console (returns object):
`api('GET', '/app/com.tclcode.otgw/getOtgwVars', function(err, x) { console.log(x) })`
* direct URL (returns JSON):
`<homey_ip>/api/app/com.tclcode.otgw/getOtgwVars`


### Versions
* 1.0.0 Updates for thermostat_mode and Homey FW 1.0.1
* 0.6.2 Fixes for API changes of Homey FW 0.9.1 (custom capabilities)
* 0.6.1 Custom state capability added since Athom removed the 'thermostat_state' device capability
* 0.6.0 Added speech, using booleans for Insights, fix override temperature display
* 0.5.2 Fixes for API changes of Homey FW 0.8.24
* 0.5.1 Bug fix, improved settings page
* 0.5.0 Second app-store release - adds better flow support, insights logging & re-connect
* 0.1.0 First app-store release - added gateway config (RO), language support, flow actions, fixes/refactoring
* 0.0.2 Second release - added pressure sensor, 2nd thermostat, flags, fixes & code refactoring
* 0.0.1 Initial release - functionality limited to one thermostat and multiple temperature sensors