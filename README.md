# SmartMirror_IgnitionMaker
Smart Mirror using Inductive Automation's Ignition - Maker Edition

The smart mirror is running on a Raspberry Pi 4. I tried running it on a Raspberry Pi Zero, but there just isn't enough processing power to run it smoothly.
This is a living project and as such the screenshots may show different configurations and items.

## Screenshots
![Smart Mirror](/images/20201010_075746.jpg)

![Smart Mirror](/images/Screenshot_20201004_203613.png)

![Smart Mirror](/images/Screenshot_20201008_215252.png)

## SmartMirror AddOns
### Weather Forecast
The Weather Forecase AddOn uses the One Call API from [OpenWeatherMap](https://openweathermap.org). Data needed for this AddOn can be brought into Ignition either through Node-RED using the node-red-node-openweathermap and mqtt nodes or by installing the tags and scripts located in the weather directory.
