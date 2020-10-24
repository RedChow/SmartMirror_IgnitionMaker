# Calendar
I currently have a cron job scheduled to check three Google Calendars for events once an hour (events on my calendar just don't change that frequently to call on a shorter even cycle). It then builds HTML and color codes the events based on which calendar the event originated. I could have structed the MQTT tags differently and let Ignition handle the color coding, but this method has worked well enough for my purposes. 

## Screenshot
![Smart Mirror](/calendar/images/CalendarAddOn_Screenshot.png)

# Example Calendar Script

