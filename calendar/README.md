# Calendar
I currently have a cron job scheduled to check three Google Calendars for events once an hour (events on my calendar just don't change that frequently to call on a shorter even cycle). It then builds HTML and color codes the events based on which calendar the event originated. I could have constructed the MQTT tags differently and let Ignition handle the color coding, but this method has worked well enough for my purposes. 

There are essentially two pieces, of which you can use either or both. The view named "SmartMirror_Calendar" has a Flex Repeater with a binding on the instances attribute. The
```python
next_month = today + datetime.timedelta(days=35)
```
sets the number of days to view. If you wish to view less into the future, set days=28, 21, 14, or 7. Also, the line
```python
tp = '[MQTT Engine]calendar/calendar/family_calendar/' + today_strftime
```
uses the path where the MQTT tags are coming into Ignition. If yours is different, this will need to be changed.

The view entitled "SmartMirror_Calendar_Events" will scroll through all events. There is a custom property on the Carousel that needs to be set to where your MQTT tags are coming into Ignition.

## Screenshots
![Smart Mirror](/calendar/images/CalendarAddOn_Screenshot.png)

![Smart Mirror](/calendar/images/CalendarAddOn_EventsCarousel_Before.png)

![Smart Mirror](/calendar/images/CalendarAddOn_EventsCarousel.png)

# Installation
1. Import the zip file
2. Import the TimeTags.json
3. Set up a global tag change script:
* ![Smart Mirror](/calendar/images/CalendarAddOn_Maintenance.png)
![Smart Mirror](/calendar/images/CalendarAddOn_Maintenance_Script.png)
4. Go to Config -> MQTT Engine -> Settings -> Namespaces -> Custom and add an appropriate subsription, root tag folder, and JSON payload settings. I used calendar/#, calendar, and True (respectively) for mine.

# Example Calendar Script

