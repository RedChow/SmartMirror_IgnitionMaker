
# Installation
1. Import the following:
  * SmartMirror_NHL_ViewsAndScripts.zip
  * TimeTags.json
  * nhl_UDT.json
2. Unzip the nhl_icons.zip folder. Using "Image Management" under Tools, upload all the icons to a folder named "nhl" in the root folder. I.e., the path to all the icons is /nhl/...
3. Go to Config -> MQTT Engine -> Namespaces -> Custom and add subscription with value "nhl/#", root tag folder = "nhl", and payload JSON set to True.
4. Check that you can run "nhlapi.getDataMethods.setUpNHLGames()" in the Script Console by checking Config -> Gateway Settings -> Gateway Scripting Project. If an error occurs when running the aforementioned script, either adjust the name in Gateway Scripting Project accordingly or change the Tag Change Event Scripts
5. Add the two Global Tag Change Event Scripts:
  
# Import Info  
The SmartMirror_NHL_ViewsAndScripts contains Perspective Views, namely /SmartMirror/NHLGames/NHLCard and SmartMirror_NHL. The SmartMirror_NHL View contains a carousel item that will iterate through all the games found in the [MQTT Engine]nhl/nhl/games folder.

The TimeTags.json contains two Expression Tags that evaulate to True at 3:00 AM and 3:05 AM. These tags can global
