I recently installed 4 heat pumps in my house to provide heat in the winter and AC in the summer time.  While the weather year round in Seattle is fairly pleasant, there is about a month in the summer where life is miserable if you don't have air conditioning - a rare luxury in residential homes in the Pacific Northwest.

![Mitsubishi MSZ-FH09NA Heat Pump](/assets/images/mitsubishi_heat_pump_msz-fh09na.jpg)

I had 4 [Mitsubishi MSZ-FH09NA Heat Pumps](https://www.mitsubishicomfort.com/node/1103) installed in my home.  They each came with a remote control but, being IR, I had to be in the room to use it.  This was an inconvenience when I needed change the temperature in my kids' rooms while they were sleeping and not wake them.  Mitsubishi does offer an additional hardware adapter that allows connectivity over the internet for the heat pumps via an iOS or Android app.  I was shocked and disappointed to find out that the device would cost approximately $180 each.  For 4 units that would be around $1k total with tax!  Ouch!  Second, according to reviews, their app and service were not the best quality.  Third, the adapter attaches to the outside of the heat pump and looks ugly.

![Mitsubishi Ductless Wifi Interface Adapter for Kumo Cloud Price](/assets/images/mitsubishi_ductless_wifi_interface_adapter_for_kumo_cloud.jpg)

Eventually I came across a [SwiCago's Github project](https://github.com/SwiCago/HeatPump) that provides source code that implements the protocol used by the adapter to communicate with the heat pump using an Arduino.  This is based of the original work posted in [this blog](https://nicegear.nz/blog/hacking-a-mitsubishi-heat-pump-air-conditioner/). I don't have much of a hardware background and I have never worked with Arduinos before, but it seemed rather simple.  I just had to order the same cable the Mitsubishi adapter uses and 4 Arduino chips.

You can check if your Mitsubishi Heat Pump model will work with this code by checking the [supported models list](https://github.com/SwiCago/HeatPump/wiki/Supported-models) on SwiCago's Github page.

### Materials

The Github project has part suggestions with links to online suppliers.  

#### CN105 Cable

The site I originally ordered my CN105 cables from is no longer in business.  The below should work.

https://m.aliexpress.com/item/1005002904897793.html (select 5P option)

#### Arduino

I was originally planning on using the [Adafruit Huzzah Feather ESP8266](https://www.adafruit.com/product/2821) since I saw from other implementers that they had luck with the device.  Also, the Huzzah could handle the 5V from the heat pump directly without requiring a 5V to 3.3V regulator.  I then saw posts where people said they were successfully using the Wemos D1 Mini.  The D1 Mini could also handle the 5V without a regulator but was also significantly cheaper.  The Huzzah costs close to $20 each.  You can get a [pack of 5 Wemos D1 Mini (clones) for that much](https://www.amazon.com/Organizer-ESP8266-Internet-Development-Compatible/dp/B081PX9YFV/).  The D1 was also much smaller and would fit better in tight spaces.  While the Huzzah is superior in other respects, the minimum requirements of what I needed for this project made the D1 Mini more desirable.

| Huzzah Feather ESP8266 | Wemos D1 Mini |
|-------|--------|
| ![Huzzah Feather ESP8266](/assets/images/Huzzah_Feather_ESP8266.jpg) | ![Wemos D1 Mini](/assets/images/Wemos_D1_Mini.jpg) |

#### Micro-USB to USB Adapter

You will also need a micro-usb to USB adapter to connect the Arduino to your computer to upload your code.  You likely already have one of these lying around.  Note that you can also update the code on your Arduino over Wi-fi, which will come in handy later after we install the chip inside the heat pump.

#### Other Things You Will Need

* Soldering iron
* Solder

### Code

#### Setting Up Your Environment

Note: Instructions here are geared towards Windows but shouldn't differ that much on other platforms.

Download and Install Arduino IDE

* Navigate to https://www.arduino.cc/en/Main/Software
* Download the Arduino IDE for your platform and install it

Configure Arduino IDE

* Plug your Arduino into your machine using the micro-USB to USB cable
* Open Arduino IDE
* From the Tools menu you will need to select the board that matches your device.  In this case I am using the "LOLIN(WEMOS) D1 R2 & mini" board.  After selecting this it will fill in some defaults.  Note: If you can't find your board you can search for and download it from the Boards Manager.  In my case this was included in the esp8266 board package which supports a large number of boards.  To get this included in your list of boards to install, go to File > Preferences, add a Boards Manager URL http://arduino.esp8266.com/stable/package_esp8266com_index.json.  Go back to the boards manager and search for esp8266 and install.

![Arduino IDE](/assets/images/Arduino_IDE1.jpg)

* Select the Port your device is on.  In my case, it is on COM4.  After this we should be able to upload code to the device.
* Next, just to test that your setup and Arduino are working, lets upload some sample code that simply blinks the onboard LED.

   * From the file menu, select Examples > ESP8266 > Blink
   
   * From the Sketch menu, select Upload
    
If the upload completes successfully and the device is functioning correctly, you should see a blinking light on your Arduino

#### Working With the Heatpump Code

Get the code

* Navigate to https://github.com/SwiCago/HeatPump
* Click the "Clone or download" button
* Click "Download ZIP"
* Open the zip folder and copy the folder to your Arduino libraries folder (ex: c:\users\<username>\documents\arduino\libraries)
* Move the examples folder to the arduino folder (ex: c:\users\<username>\documents\arduino\examples)
* Restart Arduino IDE so it picks up the content you added
* From the File menu, select Sketchbook > HP_cntrl_esp8266
  
This is a simple webserver that operates as a Wi-fi access point you can join to operate your heat pump. 

![Arduino IDE](/assets/images/Arduino_IDE2.jpg)

* From the Sketch menu, select Verify/Compile
  Note: you may get some dependency errors like the ones below for libraries that are not installed.  
      ArduinoJson.h: No such file or directory
      PubSubClient.h: No such file or directory
      
  If that is the case you can install them from the Tools menu by clicking "Manage Libraries…".  From there, search for ArduinoJson and PubSubClient and install the packages.  Then try Verify/Compile again.  If successful, then upload the code to the device via the Sketch > Upload menu item.

The below is from the browser on my iPhone.  If the chip was connected to the heat pump it would be displaying the current settings.  This sample code provides basic functionality to allow you to access each of your heat pumps while you are at home and modify their settings.  Next, lets solder up our CN105 cable to the Arduino so we can plug it in to our heat pump.

![Heat Pump Demo Screen](/assets/images/heat_pump_demo_screen.jpg)

### Soldering

I only needed 4 of the 5 wires on the premade cable.  Stripping and soldering the wires to the D1 was quick and easy.  I did all 4 chips in 20 minutes.

The 5V (brown) and ground (orange) pinouts are on the left, the RX (blue) and TX (red) on the right.  You can follow the cables below since your CN105 wire colors might be different

| ![Wemos D1 Soldered](/assets/images/Wemos_D1_Solder1.jpg) | ![Wemos D1 Soldered](/assets/images/Wemos_D1_Solder2.jpg) |

[High resolution so you an see the pinouts](/assets/images/wemosd1-hires.jpg)

### Installing

I was a little hesitant to open up my heat pump at first.  They are certainly not cheap.  I was surprised how easy it was to take apart. 

* Turn off the heat pump.  You should really disconnect it from power if you can.
* First, lift the main cover like the image below.  Then pull it away from the unit.  It should slide out of the hinges with a little wiggling.

![Install Step 2](/assets/images/heatpump_install1.jpg)

* Remove screws at the below locations.  The area circled on the bottom is a little plastic piece that you can remove by hand which hides a screw.  With these removed you should be able to take off the right plastic shell.

![Install Step 3](/assets/images/heatpump_install2.jpg)

* Remove the below screw and slide the right metal shielding away from the wall.

![Install Step 4](/assets/images/heatpump_install3.jpg)

* With the shielding removed, you should see the control board.  In the below image I point out the CN105 connection where you will connect the Arduino

![Install Step 5](/assets/images/heatpump_install4.jpg)

* Plug in the device.  I tucked mine in the best I could where it wouldn't be in the way when I slid the metal shielding back on over the control board.

| ![Install Step 6](/assets/images/heatpump_install5.jpg) | ![Install Step 6](/assets/images/heatpump_install6.jpg) |

* Turn the heat pump on.
* If you have the Wi-fi access point example installed on the chip you can try connecting to it to now to verify if it is reading the data on the heat pump.  Try modifying the settings as well.
* Put the heat pump case back together.

### Limitations

This is a simple setup to enable changing the settings on your heat pump from a browser.  A few things I didn't like:
* UI is not user friendly
* The Wi-fi Access Point seems flaky.  I kept getting dropped from my iPhone.  Not sure if it is just that device or not.  I didn't use this solution for long so I didn’t troubleshoot it much.
* The example code didn't enable OTA updates for the chip.  So I would have to physically take it out of the heat pump every time I wanted to update it.  Other examples do and it is pretty simple to add it.
* I need to connect to different access points to read/change settings on each of my heat pumps.
* I can't connect to the heat pump over the internet - only when I am physically home.  I would like to get the house warm/cold before I get home.

The good thing is that some of the other project examples leverage [MQTT (Message Queuing Telemetry Transport)](https://en.wikipedia.org/wiki/MQTT) to allow communication across the internet.  Some home automation tools such as Hass.io leverage this.  I'll cover those in my [next post](https://chrdavis.github.io/hacking-a-mitsubishi-heat-pump-Part-2/).
