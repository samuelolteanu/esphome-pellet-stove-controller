I proudly present you a fully featured pellet controller based on ESPHome. The stove is an Alfa Plam 14KW that had a dead (expensive) and not well documented motherboard. 

Highlighted features: 
- Constant (adjustable) internal water temperature without an inlet-outlet mixer
- Advanced auger modulation
- Web server with everything that is going on.
- Completly autonomous so Home Assistant or WIFI for that matter is not a must
- Pellet consumption and power output estimation (in watts) based on pellet usage.
- Fan throttle via sigma delta modulation when needed.
- Pump freeze protection
- Can be tested with cold hardware <s> or simply no hardware, just a flashed ESP</s>. Future simulation versions will need a TM1638 as it became deeply integrated into the project. 

Disclaimer: 
IT HAS THE POTENTIAL TO BURN DOWN YOUR HOUSE OR EVEN WORSE AND I WILL NOT BE RESPONSIBLE FOR IT IN ANY WAY.

Warning: disconnecting the display will result in a bootloop. Auger should stay off all the time when this happens, else use other pin for auger or change to high/low input relay.

Hardware used:  
 - Sensors:
    - Stoves own 10k themistors, exhaust k type thermocuple, and negative air pressure binary sensor (presostat)
    - 2x safety thermostats NO in paralell with heating pump and fan relays to bypass them.
    - 0-5 Bar 1/4 water pressure sensor,  DS1820 for ambient temperature 
 - ESP32S2, ADS1115 (NTC sensing, Water Pressure), MAX31855 (can be replaced by an ads1115 channel if calibration is 
   possible and that would make it more realible as well)
   I would recommend to not get am s2 clone for this project.
 - TM1638 Display+LEDs+Buttons Module, passive rtttl
 - 5V Power Supply: normal phone charger.
 - SSR modules, 1x4 active high, 1x4 active low. The 30A relays in the picture were replaced because they were overkill.

   
While a pid controller could have make it more precise (ESPHome provides this if I really wanted to), it would introduced some unknowns on my part so I implemented what I know better: liniar regressions.
As a quick explanation, the stove tries to keep its temp around a set point with relatively generous delta (can be modified but works well as is) where:
If:
 - temp < set_point - 10:  heating pump is fully off
 - temp < set_point > set_point - 10: granular 0-100% heating pump "ON" based on a regression controlling slow_pwm output.
 - temp > set_point < set_point + 10: auger modulation "ON" 4.5s to 1.5s (out of 10 seconds) by a regression
 - temp > set_point + 10: auger set to 1.5s (min) up until temp > max_water_temp after wich it will forcefully shut down with error: fan throttle via sigma delta modulation to "suffocate" flame, then normal extinguish process takes over at a safer temp.  

![internal_water_temp_control](https://github.com/samuelolteanu/esphome-pellet-stove-controller/assets/85267083/fb90b2ad-fa58-4964-86ae-ab55a4b77e26)


Bit of work ahead...
![bit of work ahead](https://github.com/samuelolteanu/esphome-pellet-stove-controller/assets/85267083/10a08385-9fa7-42a6-aa9e-75acc9892a5d)

Calibrating the thermistor
![calibrating the original thermistor](https://github.com/samuelolteanu/esphome-pellet-stove-controller/assets/85267083/bf9c8848-4a19-4f81-8012-9a4b9a6530be)

The expander here was deleted. ESP32 S2/<s>S3</s> has plenty of ports for this project and the PCF8575 introduced some delay warnings.
It's all switches and esphome is very good at it.
![20231104_203415](https://github.com/samuelolteanu/esphome-pellet-stove-controller/assets/85267083/3708bd34-eedb-4afb-96ba-43884e9346f4)



https://github.com/samuelolteanu/esphome-pellet-stove-controller/assets/85267083/059ff512-fc86-4e0c-a212-13c8246e5940


