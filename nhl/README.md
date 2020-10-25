
# Installation
1. Import the following:
* SmartMirror_NHL_ViewsAndScripts.zip
* TimeTags.json
* nhl_UDT.json
2. Unzip the nhl_icons.zip folder. Using "Image Management" under Tools, upload all the icons to a folder named "nhl" in the root folder. I.e., the path to all the icons is /nhl/...
3. Go to Config -> MQTT Engine -> Namespaces -> Custom and add subscription with value "nhl/#", root tag folder = "nhl", and payload JSON set to True.
4. Check that you can run "nhlapi.getDataMethods.setUpNHLGames()" in the Script Console by checking Config -> Gateway Settings -> Gateway Scripting Project. If an error occurs when running the aforementioned script, either adjust the name in Gateway Scripting Project accordingly or change the Tag Change Event Scripts
5. Add the two Global Tag Change Event Scripts:
 * ![Smart Mirror](/nhl/images/NHLAddOn_EventSetup_TagChange.png)
  ![Smart Mirror](/nhl/images/NHLAddOn_EventSetup_TagChange_Script.png)
 * ![Smart Mirror](/nhl/images/NHLAddOn_EventSetup_TagChangeMaintenance.png)
  ![Smart Mirror](/nhl/images/NHLAddOn_EventSetup_TagChangeMaintenance_Script.png)
 
# Screenshot
![NHL](/images/NHLAddOn_Screenshot.png)
  
# Additional Info  
The SmartMirror_NHL_ViewsAndScripts contains Perspective Views, namely /SmartMirror/NHLGames/NHLCard and SmartMirror_NHL. The SmartMirror_NHL View contains a carousel item that will iterate through all the games found in the [MQTT Engine]nhl/nhl/games folder.

The TimeTags.json contains two Expression Tags that evaulate to True at 3:00 AM and 3:05 AM. These tags are used in the two global tag change events in step 5 of the Installation section. By default, it gets games from the current and previous days. To change how far back and forward the script should get games, find these two lines in nhlapi.getDataMethods.setUpNHLGames():
```python
days_before = 1
days_after = 0
```
The days_before variable is how many days before the current day to retrieve games while days_after is how far in the future to go. For example, if you wanted to see scores from two days ago plus get the schedule for up to, and including, five days aways, change days_before to 2 and days_after to 5.

The nhl UDT is set to update every 30 seconds during game time. The tag fireEvent is an expression tag whose expression is floor(getSecond({[System]Gateway/CurrentDateTime})/30) = 0. If 30 seconds is too much, it would be possible to change the expression to something like floor(getMinute({[System]Gateway/CurrentDateTime})/2) = 0, where it updates every two minutes. 
