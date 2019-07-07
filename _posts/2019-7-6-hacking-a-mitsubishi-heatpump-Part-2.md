In my previous post I showed how to use a cheap Arduino to connect wirelessly to a Mitsubishi heat pump to control its settings.  Yet, it still took some work to connect to the device Access Point/Web Server.  Having multiple heat pumps in your home is even more laborious.  Without a lot more work, you also couldn’t communicate directly with them from outside of your Wi-fi network.  What we need is a centralized app that can communicate with the Arduinos directly.

### MQTT

From the [mqtt.org](https://mqtt.org) site:
MQTT is a machine-to-machine (M2M)/"Internet of Things" connectivity protocol. It was designed as an extremely lightweight publish/subscribe messaging transport.

I won't go into too much detail on [how MQTT works](http://mosquitto.org/man/mqtt-7.html), but it is essentially a Publish/Subscribe messaging model (publish messages/subscribe to topics).  Clients connect to a MQTT broker to subscribe to topics they want and publish messages to topics.  

### Hass.io and Home Assistant

Hass.io turns your Raspberry Pi (or another device) into the ultimate home automation hub powered by Home Assistant. With Hass.io you can focus on integrating your devices and writing automations.

Home Assistant is the centralized app we needed.  This provides the MQTT Broker (Mosquitto Add-on) and great UI.  It also has some advanced home automation capabilities that I have not utilized yet.  You can access it from your browser or from your phone via the free Home Assistant app.  To access outside your Wi-fi network you will have to enable port forwarding of the port and ip address that hass.io is running on.  See instructions for your router.

The best installation instructions I have found are in the below video:
https://www.youtube.com/watch?v=qnCRcGTznXs

As well as the [hass.io documentation](https://www.home-assistant.io/hassio/installation/)

While you could [install Home Assistant on Windows](https://www.home-assistant.io/docs/installation/windows/), it is not fully supported and not all third-party modules work.  Because of this reason (and since I have never played with one before) I opted to pick up a Raspberry Pi since it was the most common install platform. 

After installing, be sure to install and setup the MQTT Broker (Mosquitto Add-on) so the code running on our Arduino can connect and publish MQTT messages.  Note that I had to use the ip address 172.17.0.1 (the docker network default gateway) for the broker to get it to work properly.  I found that fix in [this reddit post](https://www.reddit.com/r/homeassistant/comments/74qqtc/hassio_mqtt_installation_beginner_questions/) to the homeassistant subreddit.

```ruby
mqtt:
  broker: 172.17.0.1
  port: 1883
  username: <my mqtt user name>
  password: <my mqtt password>
  discovery: true
```

We will have to do some extra steps to have Home Assistant work with the Mitsubishi Heat Pump, but that will come later.


### Hardware

I purchased the below Pi with a case.

CanaKit Raspberry Pi 3 B+ (B Plus) with Premium Clear Case and 2.5A Power Supply 
https://www.amazon.com/dp/B07BC7BMHY/ref=cm_sw_em_r_mt_dp_U_UTyiDbRW5SK29 

I also bought a 32GB memory card for the Pi (not included in the above CanaKit)

Samsung 32GB EVO Plus Class 10 Micro SDHC with Adapter 80mb/s (MB-MC32DA/AM) 
https://www.amazon.com/dp/B00WR4IJBE/ref=cm_sw_em_r_mt_dp_U_VTyiDbZT8GYBT 


### Code

See the code available on the SwiCago / HeatPump repo at:
HeatPump/examples/mitsubishi_heatpump_mqtt_esp8266_esp32

Note - the above code will have some dependency libraries required that you will have to download through the Arduino IDE.  These include ArduinoJson and PubSubClient.

*mitsubishi_heatpump_mqtt_esp8266_esp32.h*

This header contains constants used in the mitsubishi_heatpump_mqtt_esp8266_esp32.ino file.  This includes your Wi-fi SSID and password, your mqtt server/broker ip address, port and password as well as the mqtt client name and topic paths. 

```c++
//#define ESP32
//#define OTA
//const char* ota_password = "<YOUR OTA PASSWORD GOES HERE>";

// wifi settings
const char* ssid     = "<YOUR WIFI SSID GOES HERE>";
const char* password = "<YOUR WIFI PASSWORD GOES HERE>";

// mqtt server settings
const char* mqtt_server   = "<YOUR MQTT BROKER IP/HOSTNAME GOES HERE>";
const int mqtt_port       = 1883;
const char* mqtt_username = "<YOUR MQTT USERNAME GOES HERE>";
const char* mqtt_password = "<YOUR MQTT PASSWORD GOES HERE>";

// mqtt client settings
// Note PubSubClient.h has a MQTT_MAX_PACKET_SIZE of 128 defined, so either raise it to 256 or use short topics
const char* client_id                   = "heatpump"; // Must be unique on the MQTT network
const char* heatpump_topic              = "heatpump";
const char* heatpump_set_topic          = "heatpump/set";
const char* heatpump_status_topic       = "heatpump/status";
const char* heatpump_timers_topic       = "heatpump/timers";

const char* heatpump_debug_topic        = "heatpump/debug";
const char* heatpump_debug_set_topic    = "heatpump/debug/set";
```

Be sure to uncomment the #define OTA if you want to flash your Arduino over Wi-fi.  If you are like me you don't want to open up your heat pump again if you don't have to.

Note: If you are setting up multiple heat pumps (like I was) be sure to have not only a unique name for the client_id, but also for the topic paths.  Otherwise all of your heatpumps will be sending messages on the same topic.  For example,

```c++
const char* client_id                   = "Master Bedroom Heatpump"; // Must be unique on the MQTT network
const char* heatpump_topic              = "Master Bedroom/heatpump";
const char* heatpump_set_topic          = "Master Bedroom/heatpump/set";
const char* heatpump_status_topic       = "Master Bedroom/heatpump/status";
const char* heatpump_timers_topic       = "Master Bedroom/heatpump/timers";

const char* heatpump_debug_topic        = "Master Bedroom/heatpump/debug";
const char* heatpump_debug_set_topic    = "Master Bedroom/heatpump/debug/set";
```

Also, follow the instructions of the comment and update the size of MQTT_MAX_PACKET_SIZE to 256 just in case you have issues with the default of 128.

*mitsubishi_heatpump_mqtt_esp8266_esp32.ino*

The implementation code.  I set the below to false since it was causing a significant amount of MQTT traffic.   You can see the MQTT traffic using the [MQTT Fx](http://mqttfx.org/) tool.

```c++
// debug mode, when true, will send all packets received from the heatpump to topic heatpump_debug_topic
// this can also be set by sending "on" to heatpump_debug_set_topic
bool _debugMode = true;
```

### Flashing the Arduino 

After configuring the settings for the one or more Arduino chips, flash the devices.  Verify by plugging them into your PC that they connect and show up on your local Wi-fi router.  If you have Home Assistant installed or [MQTT Fx](http://mqttfx.org/) you can verify that the devices are publishing MQTT messages with the default values.  


### Adding the Mitsubishi MQTT Custom Component to Home Assistant 

Copy mitsubishi_mqtt folder from the below location

https://github.com/SwiCago/HeatPump/tree/master/integrations/home-assistant.io/custom_components

to the home assistant config directory

\config\custom_components\mitsubishi_mqtt

This contains the climate.py and manifest.json files.



*customize.yaml*

Add the line below

```ruby
climate.mistubishi_heatpump: {}
```

*configuration.yaml*

Add the mqtt and climate entries.  These are mine.

```ruby
mqtt:
  broker: 172.17.0.1
  port: 1883
  username: <my mqtt user name>
  password: <my mqtt password>
  discovery: true

climate:
   - platform: mitsubishi_mqtt
     name: "Keira Room Heatpump"
     command_topic: "Keira Room/heatpump/set"
     temperature_state_topic: "Keira Room/heatpump/status"
     state_topic: "Keira Room/heatpump"

   - platform: mitsubishi_mqtt
     name: "Connor Room Heatpump"
     command_topic: "Connor Room/heatpump/set"
     temperature_state_topic: "Connor Room/heatpump/status"
     state_topic: "Connor Room/heatpump"

   - platform: mitsubishi_mqtt
     name: "Family Room Heatpump"
     command_topic: "Family Room/heatpump/set"
     temperature_state_topic: "Family Room/heatpump/status"
     state_topic: "Family Room/heatpump"

   - platform: mitsubishi_mqtt
     name: "Master Room Heatpump"
     command_topic: "Master Room/heatpump/set"
     temperature_state_topic: "Master Room/heatpump/status"
     state_topic: "Master Room/heatpump"
```

FYI - It wasn't available when I did my install but there is a way to use the built-in MQTT climate component instead of this custom component.  See the additions to the configuration.yaml in the below gist.  I have heard confirmation that this works and is easier than the above.

https://gist.github.com/kmdm/29f740e5f36036fb23daba8f2109c359.js

### Adding a Heat Pump in Home Assistant

From the top right menu, select Configure UI.  

![Adding a Heat Pump in Home Assistant - Step 1](/assets/images/homeassistant_addcard0.jpg)

If prompted to "take control" say yes.  Next, click the "+" button in the bottom right.  This will open the Card Configuration window. Click the Thermostat entry.

![Adding a Heat Pump in Home Assistant - Step 2](/assets/images/homeassistant_addcard1.jpg)

Give the new thermostat card a name.  Next, select an entity from the drop down.  This is the mqtt device.  Click Save.  

![Adding a Heat Pump in Home Assistant - Step 3](/assets/images/homeassistant_addcard2.jpg)

The device should now be on your home screen.


