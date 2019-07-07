### Introduction

I recently installed 4 heat pumps in my house to provide heat in the winter and AC in the summer time.  While the weather year round in Seattle is fairly pleasant, there is about a month in the summer where life is miserable if you don't have air conditioning - a rare luxury in residential homes in the Pacific Northwest.

![Mitsubishi MSZ-FH09NA Heat Pump](/assets/images/mitsubishi_heat_pump_msz-fh09na.jpg)

I had 4 [Mitsubishi MSZ-FH09NA Heat Pumps](https://www.mitsubishicomfort.com/node/1103) installed in my house.  They came with an IR remote for controlling the them - but being IR I had to be in the room to use it.  This was an inconvenience when I had needed change the temperature in my kids' rooms and not wake them.  Mitsubishi does offer an additional hardware adapter that allows connectivity over the internet for the heat pumps via an iOS or Android app.  I was shocked and disappointed to find out that the device would cost approximately $180 each.  For 4 units that would be around $1k total with tax!  Ouch!  Second, according to reviews, their app and service was not the best quality.  Third, the adapter attaches to the outside of the heat pump and looks ugly.

![Mitsubishi Ductless Wifi Interface Adapter for Kumo Cloud Price](/assets/images/mitsubishi_ductless_wifi_interface_adapter_for_kumo_cloud.jpg)

Eventually I came across a [Github project](https://github.com/SwiCago/HeatPump) that reverse engineered the protocol used by the adapter and provided source code to communicate with the heat pump using an Arduino.  I don't have much of a hardware background, but it seemed rather simple.  I just had to order the same cable the Mitsubishi adapter uses and a 4 Arduino chips.    

### Materials

#### CN105 Cable

The Github project has part suggestions with links to online suppliers.  I ended up ordering 5 premade CN105 cables from the below link.  Since they were only $1.99 I figured getting an spare would be a good idea.

http://www.usastore.revolectrix.com/Products_2/Cellpro-4s-Charge-Adapters_2/Cellpro-JST-PA-Battery-Pigtail-10-5-Position

#### Arduino

I was originally planning on using the [Adafruit Huzzah Feather ESP8266](https://www.adafruit.com/product/2821) since I saw from other implementers that they had luck with the device.  Also, the Huzzah could handle the 5V from the heat pump directly without requiring a 5V to 3.3V regulator.  I then saw posts where people said they were successfully using the Wemos D1 Mini.  The D1 Mini could also handle the 5V without a regulator but was also significantly cheaper.  The Huzzah costs close to $20 each.  You can get a [pack of 5 Wemos D1 Mini (clones) for that much](https://www.amazon.com/dp/B076F52NQD/ref=cm_sw_em_r_mt_dp_U_hzfdDbC64GWC5).  The D1 was also much smaller and would fit better in tight spaces.  While the Huzzah is superior in other respects, the minimum requirements of what I needed for this project made the D1 Mini more desirable.

| Huzzah Feather ESP8266 | Wemos D1 Mini |
|-------|--------|
| ![Huzzah Feather ESP8266](/assets/images/Huzzah_Feather_ESP8266.jpg) | ![Wemos D1 Mini](/assets/images/Wemos_D1_Mini.jpg) |

#### Micro-USB to USB Adapter

You will also need a micro-usb to USB adapter to connect the Arduino to your computer to upload your code.  Note that you can also update the code on your Arduino over wifi, which will come in handy later after we install the chip inside the heat pump.

#### Other Things You Will Need

* Soldering iron
* Solder

### Code

#### Setting Up Your Environment

Note: Instructions here are geared towards Windows but shouldn't differ that much on other platforms.

Download and Install Arduino IDE

1. Navigate to https://www.arduino.cc/en/Main/Software
2. Download the Arduino IDE for your platform and install it

Configure Arduino IDE

1. Plug your Arduino into your machine using the micro-USB to USB cable
2. Open Arduino IDE
3. From the Tools menu you will need to select the board that matches your device.  In this case I am using the "LOLIN(WEMOS) D1 R2 & mini" board.  After selecting this it will fill in some defaults.  Note: If you can't find your board you will can search for and download it from the Boards Manager.  A quick search online for using your device in Arduino IDE should yield the instructions you need.  In my case this was included in the esp8266 board package which supports a large number of boards.

![Arduino IDE](/assets/images/Arduino_IDE1.jpg)

4. Select the Port your device is on.  In my case, it is on COM4.  After this we should be able to upload code to the device.
5. Next, just to test that your setup and Arduino are working, lets upload some sample code that simply flashes the onboard LED.

   a. From the file menu, select Examples > ESP8266 > Blink
   
   b. From the Sketch menu, select Upload
    
If the upload completes successfully, you should see a blinking light on your Arduino

#### Working With the Heatpump Code

Get the code

1. Navigate to https://github.com/SwiCago/HeatPump
2. Click the "Clone or download" button
3. Click "Download ZIP"
4. Open the zip folder and copy the folder to your Arduino libraries folder (ex: c:\users\<username>\documents\arduino\libraries)
5. Move the examples folder to the parent folder (ex: c:\users\<username>\documents\arduino\examples)
6. Restart Arduino IDE so it picks up the content you added
7. From the File menu, select Sketchbook > HP_cntrl_esp8266
  
This is a simple webserver that operates as a wifi access point you can join to operate your heat pump. 

![Arduino IDE](/assets/images/Arduino_IDE2.jpg)

8. From the Sketch menu, select Verify/Compile
   a. Note: you may get some dependency errors like the ones below for libraries that are not installed.  
      ArduinoJson.h: No such file or directory
      PubSubClient.h: No such file or directory
   b. If that is the case you can install them from the Tools menu by clicking "Manage Libraries…".  From there, search for ArduinoJson and PubSubClient and install the packages.  Then try Verify/Compile again.  If successful, then upload the code to the device via the Sketch > Upload menu item.

The below is from my iPhone.  If the chip was connected to the heat pump it would be displaying the current settings.  This sample code provides basic functionality to allow you to access each of your heat pumps while you are at home and modify their settings.  Next, lets solder up our CN105 cable to the Arduino so we can plug it in to our heat pump.

![Heat Pump Demo Screen](/assets/images/heat_pump_demo_screen.jpg)

### Soldering

I only needed 4 of the 5 wires on the premade cable.  Stripping and soldering the wires to the D1 was quick and easy.  I did all 4 chips in 20 minutes.

The 5V (brown) and ground (orange) pinouts are on the left, the RX (blue) and TX (red) on the right.  You can follow the cables below since your CN105 wire colors might be different

| ![Wemos D1 Soldered](/assets/images/Wemos_D1_Solder1.jpg) | ![Wemos D1 Soldered](/assets/images/Wemos_D1_Solder2.jpg) |


### Installing

I was a little hesitant of opening up my heat pump at first.  They are certainly not cheap.  I was surprised how easy it was to take apart.  

1. Turn off the heat pump
2. First, lift the main cover like the image below.  Then pull it away from the wall.  It should slide out of the hinges with a little wiggling.

![Install Step 2](/assets/images/heatpump_install1.jpg)

3. Remove screws at the below locations.  The area circled on the bottom is a little plastic piece that you can remove which hides a screw.  With these removed you should be able to take off the right plastic shell.

![Install Step 3](/assets/images/heatpump_install2.jpg)

4. Remove the below screw and slide the right metal shielding away from the wall.

![Install Step 4](/assets/images/heatpump_install3.jpg)

5. With the shielding removed, you should see the control board.  Below I point out the CN105 connection where you will connect the Arduino

![Install Step 5](/assets/images/heatpump_install4.jpg)

6. Plug in the device.  I tucked mine in the best I could where it wouldn't be in the way.

| ![Install Step 6](/assets/images/heatpump_install5.jpg) | ![Install Step 6](/assets/images/heatpump_install6.jpg) |

7. Turn the heat pump on.
8. If you have the wifi access point example installed on the chip you can try connecting to it to now to verify if it is reading the data on the heat pump.  Try modifying the settings as well.
9. Put the heat pump case back together.

### Limitations

This is a simple setup to enable changing the settings on your heat pump from a browser.  A few things I didn't like:
* UI is not user friendly
* The Wi-Fi Access Point seems flaky.  I kept getting dropped from my iPhone.  Not sure if it is just that device or not.  I didn't use this solution for long so I didn’t troubleshoot it much.
* The example code didn't enable OTA updates for the chip.  So I would have to physically take it out of the heat pump every time I wanted to update it.  Other examples do and it is pretty simple to add it.
* I need to connect to different access points to read/change settings on each of my heat pumps.
* I can't connect to the heat pump over the internet - only when I am physically home.  I would like to get the house warm/cold before I get home.

The good thing is that some of the other project examples leverage [MQTT (Message Queuing Telemetry Transport)](https://en.wikipedia.org/wiki/MQTT) to allow communication across the internet.  Some home automation tools such as Hass.io leverage this.  I'll cover those in my next post.
