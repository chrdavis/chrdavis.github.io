## Hacking a Mitsubishi Heat Pump

### Introduction

I recently installed 4 heat pumps in my house to provide heat in the winter and AC in the summer time.  While the weather year round in Seattle is fairly pleasant, there is about a month in the summer where life is miserable if you don't have air conditioning - a rare luxury in residential homes in the Pacific North West.

![Mitsubishi MSZ-FH09NA Heat Pump](/_posts/images/mitsubishi_heat_pump_msz-fh09na.jpg)

I had 4 [Mitsubishi MSZ-FH09NA Heat Pumps](https://www.mitsubishicomfort.com/node/1103) installed in my house.  They came with an IR remote for controlling the them - but being IR I had to be in the room to use it.  This was a pain when I had needed change the temperature in my kids' rooms and not wake them.  Mitsubishi does offer an additional hardware adapter that allows connectivity over the internet for the heat pumps via an iOS or Android app.  I was shocked and disappointed to find out that the device would cost approximately $180 each.  For 4 units that would be around $1k total with tax!  Ouch!  Second, according to reviews, their app and service was not the best quality.  Third, the adapter attaches to the outside of the heat pump and looks ugly.

![Mitsubishi Ductless Wifi Interface Adapter for Kumo Cloud Price](/_posts/images/mitsubishi_ductless_wifi_interface_adapter_for_kumo_cloud.jpg)

Eventually I came across a [Github project](https://github.com/SwiCago/HeatPump) that reverse engineered the protocol used by the adapter and provided source code to communicate with the heat pump using an Arduino.  I don't have much of a hardware background, but it seemed rather simple.  I just had to order the same cable the Mitsubishi adapter uses and a 4 Arduino chips.    

### Materials

#### CN105 Cable

The Github project has part suggestions with links to online suppliers.  I ended up ordering 5 premade CN105 cables from the below link.  Since they were only $1.99 I figured getting an spare would be a good idea.

http://www.usastore.revolectrix.com/Products_2/Cellpro-4s-Charge-Adapters_2/Cellpro-JST-PA-Battery-Pigtail-10-5-Position

#### Arduino

I was originally planning on using the [Adafruit Huzzah Feather ESP8266](https://www.adafruit.com/product/2821) since I saw from other implementers that they had luck with the device.  Also, the Huzzah could handle the 5V from the heat pump directly without requiring a 5V to 3.3V regulator.  I then saw posts where people said they were successfully using the Wemos D1 Mini.  The D1 Mini could also handle the 5V without a regulator but was also significantly cheaper.  The Huzzah costs close to $20 each.  You can get a [pack of 5 Wemos D1 Mini (clones) for that much](https://www.amazon.com/dp/B076F52NQD/ref=cm_sw_em_r_mt_dp_U_hzfdDbC64GWC5).  The D1 was also much smaller and would fit better in tight spaces.  While the Huzzah is superior in other respects, the minimum requirements of what I needed for this project made the D1 Mini more desirable.
