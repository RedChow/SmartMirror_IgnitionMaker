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
The example script below is not included above because it does not work as is. You will need to search for "replace" and replace those strings with something appropriate. 

```python
import datetime
import pickle
import os.path
from googleapiclient.discovery import build
from google_auth_oauthlib.flow import InstalledAppFlow
from google.auth.transport.requests import Request
import json
from dateutil.parser import parse
import paho.mqtt.client as mqtt


class CalendarEventDay:
    def __init__(self, date_time):
        self.date_time = date_time
        self.event_summary_list = []

    def get_data(self):
        return_data = ''
        for event in self.event_summary_list:
            return_data += '{0}'.format(event)
        return return_data

    def add_event(self, event, all_day, holiday, t):
        self.event_summary_list.append(CalendarEvent(event, all_day, holiday, t))

    def __eq__(self, other):
        if isinstance(other, CalendarEventDay):
            return self.date_time == other.date_time
        elif isinstance(other, str):
            return self.date_time == other
        return False

    def __lt__(self, other):
        if isinstance(other, CalendarEventDay):
            return self.date_time == other.date_time
        elif isinstance(other, str):
            return self.date_time == other


class CalendarEvent:
    def __init__(self, summary, all_day, holiday, t):
        self.summary = summary
        self.all_day = all_day
        self.holiday = holiday
        self.start_time = t

    def __repr__(self):
        return_string = '<div style="margin:0;'
        if self.all_day:
            if self.holiday:
                return_string += ' background-color:#ad1a00;'
            else:
                return_string += ' background-color:#758a00;'
        else:
            return_string += ' background-color: #d98d00;'
            return return_string + '">' + self.start_time + ' - ' + self.summary + '</div>'
        return_string += '">' + self.summary + '</div>'
        return return_string

    def __eq__(self, other):
        if isinstance(other, str):
            return self.summary == other
        elif isinstance(other, CalendarEvent):
            return self.summary == other.summary
        return False

# print statements will show up in the logs, some of these used to have elaborate print statements, but have pass now
# if you wish to log more, add print statements


def on_connect(client, userdata, flags, rc):
    pass


def on_message(client, userdata, msg):
    pass


def on_log(client, userdata, level, buf):
    pass


def on_publish(client, userdata, result):
    print("data published \n")
    print(result, userdata)


mqttc = mqtt.Client('pahoCalendarClient', clean_session=True, userdata=None, transport='tcp')
mqttc.on_connect = on_connect
mqttc.on_message = on_message
mqttc.on_log = on_log
mqttc.on_publish = on_publish
mqttc.username_pw_set(username="CHANGE_ME",password="CHANGE_ME")
mqttc.connect('localhost', port=1883)
mqttc.loop_start()
# If modifying these scopes, delete the file token.pickle.
SCOPES = ['https://www.googleapis.com/auth/calendar.readonly']


def main():
    """Shows basic usage of the Google Calendar API.
    Prints the start and name of the next 10 events on the user's calendar.
    """
    creds = None
    # The file token.pickle stores the user's access and refresh tokens, and is
    # created automatically when the authorization flow completes for the first
    # time.
    if os.path.exists('replace_with_your_path/token.pickle'):
        with open('replace_with_your_path/token.pickle', 'rb') as token:
            creds = pickle.load(token)
    # If there are no (valid) credentials available, let the user log in.
    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            flow = InstalledAppFlow.from_client_secrets_file(
                'replace_with_your_path/credentials.json', SCOPES)
            creds = flow.run_local_server(port=0)
        # Save the credentials for the next run
        with open('replace_with_your_path/token.pickle', 'wb') as token:
            pickle.dump(creds, token)

    service = build('calendar', 'v3', credentials=creds)

    # Call the Calendar API
    now = datetime.datetime.utcnow().isoformat() + 'Z' # 'Z' indicates UTC time
    later = datetime.datetime.now() + datetime.timedelta(days=90)
    later = later.isoformat() + 'Z'
    """
    You will need to find your calendar id
    Calendar types will determine which color they get above in the Calendar classes 
    """
    calendar_events = [('replace_calendar_id', 'replace_calendar_type'),
                       ('replace_calendar_id', 'replace_family_calendar')]
    json_doc = {}
    event_list = []

    for calendar_id, calendar_type in calendar_events:
        events_result = service.events().list(calendarId=calendar_id, timeMin=now, timeMax=later, singleEvents=True,
                                            orderBy='startTime').execute()
        events = events_result.get('items', [])
        all_day = False
        holiday = False
        if calendar_type == 'replace_calendar_type':
            holiday = True
        if not events:
            pass
        for event in events:
            event_doc = {}
            start = event['start'].get('dateTime', event['start'].get('date'))
            dt = None
            try:
                dt = datetime.datetime.strptime(start, '%Y-%m-%d')
                dt_strftime = ''
            except ValueError:
                pass
            else:
                all_day = True

            try:
                if dt is None:
                    dt = parse(start)
                    dt_strftime = dt.strftime('%H:%M')
                calendar_date = dt.strftime('%Y-%m-%d')
                if calendar_date in event_list:
                    index = event_list.index(calendar_date)
                    ce = event_list[index]
                else:
                    ce = CalendarEventDay(calendar_date)
                    event_list.append(ce)
                ce.add_event(event['summary'], all_day, holiday, dt_strftime)
            except KeyError:
                pass
            else:
                pass
    all_events = []
    event_list.sort()
    for event in event_list:
        json_doc[event.date_time] = event.get_data()
        all_events.append({event.date_time: event.get_data()})
    jd = json.dumps(json_doc)
    # LOOK BELOW! REMEMBER YOUR SUBSCRIPTION INFO IN IGNITION!!!
    publish_info = mqttc.publish('calendar/{0}'.format('replace_calendar_type'), payload=jd)
    publish_info.wait_for_publish()
    print(publish_info.is_published())
    mqttc.disconnect()


if __name__ == '__main__':
    main()
```
