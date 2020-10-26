# SmartMirror_IgnitionMaker
Smart Mirror using Inductive Automation's Ignition - Maker Edition

The smart mirror is running on a Raspberry Pi 4. I can run the calendar, weather, and current date/time on a Raspberry Pi Zero. The carousel requires too much processing power to run on a Pi Zero.
This is a living project and as such the screenshots may show different configurations and items.

## Screenshots
![Smart Mirror](/images/20201010_075746.jpg)

![Smart Mirror](/images/Screenshot_20201004_203613.png)

![Smart Mirror](/images/Screenshot_20201008_215252.png)

## SmartMirror AddOns
### Weather Forecast
The Weather Forecase AddOn uses the One Call API from [OpenWeatherMap](https://openweathermap.org). Data needed for this AddOn can be brought into Ignition either through Node-RED using the node-red-node-openweathermap and mqtt nodes or by installing the tags and scripts located in the weather directory.

![Weather](/images/WeatherAddOn_Screenshot.png)

### NHL
The NHL portion is a little more complicated, and requires imorting tags, scripts, and Perspective Views.

![NHL](/images/NHLAddOn_Screenshot.png)

### Calendar
The Calendar has two main parts, a rotated carousel that iterates through all events 90 days into the future and a calendar that shows days of the week along with the scheduled events for that day. The calendar portions expect tags to named for the day of occurrence, with the value being all the text for that day. The Calendar page has more details.
