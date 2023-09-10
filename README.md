# Iot_Project_group29

The project already contains a predefined set of configuration data for testing purposes, specifically it can be run with 2 users with 1 greenhouse each:

    - userID: 0 - greenhouseID: 0
    
    - userID: 1 - greenhouseID: 0

The user and greenhouse 0 are already registered as they were inserted from the web page and do not have any type of predefined strategy; the second user instead is not registered and should be inserted from the web page. Both greenhouses present all the types of sensors (temperature, humidity) and actuators (temperature, humidity, windows, irrigation) controlled by our system. 
Since each greenhouse has its own set of physical devices (now simulated) it needs its own device connector along with Thingspeak key. 

The following steps allow the creation and management of a new user/greenhouse and they must be performed by a system administrator after having performed a contract (network and system utilisation + device installation) with the specific user. 

### STEP 1
In the resource catalog configuration file ResourceCatalog/db/catalog.json add to the "registered_users" section the userID and greenhouseID of the user and greenhouse that should be inserted to allow their registration on the web page.

      ...
      
      {
         "userID": x,
         "greenHouses": [
            {
               "greenHouseID": x
            }
         ]
      }

### STEP 2
Create a new directory named DeviceConnector_x which has the same contents of DeviceConnector_0. 
Modify all the strings in the newly created DeviceConnector_x/Dockerfile and DeviceConnector_x/db/device_connector_db.json from "device_connector_0" to "device_connector_x".
Modify also the devices section of DeviceConnector_x/db/device_connector_db.json in order to make it compliant with the user and greenhouse capacities.

      ...
      
      "devices": 
      {
          "sensors": 
          [
              "temperature",
              "humidity"
          ],
          "actuators": 
          [
              "temperature",
              "humidity",
              "irrigation",
              "windows"
          ]
      }

### STEP 3
From the Thingspeak web page create a new channel dedicated to the new user/greenhouse and collect the key. 
Modify the users section of ThingSpeakAdaptor/db/thingspeak_adaptor_db.json in order to allow the management of the newly created Thingspeak channel. 

      ...
      
      "userID": x,
            "greenHouses": [

                {
                    "greenHouseID": x,
                    "channelID": XXXXXX,
                    "KEY": "XXXXXXXXXXXXXXXX",
                    "temperature": "&field1={}",
                    "humidity": "&field2={}",
                    "weather": "&field3={}",
                    "irrigation": "&field4={}"
                }

            ]


# HOW TO RUN THE TEST NETWORK


-‌ Make sure Docker engine is running (i.e. open Docker Desktop).
  
-‌ Open a terminal and navigate to the extracted folder (the folder containing this file), then run the following set of commands to create the containers' network and build and run them:

    - <docker network create smart_greenhouse>
    - <docker-compose up --build>

‌- Wait until all the containers are up and running. Once all the Node Red nodes are deployed and functioning the web page can be accessed and the system is working.

-‌ In the search bar of a browser search for <127.0.0.1:1880/ui> to see web page from the user perspective or <127.0.0.1:1880> to see it from the developer perspective.

For detailed information about the different web pages, strategy management and statistic section see https://youtu.be/lfoMCLfvQuU


## LIMITATIONS

The project shows the following limitations which are correlated to the utilisation of free softwares or free version's softwares:

-‌ The free version of Thingspeak allows the creation of just 4 channels, with 4 fields each, limitating the number of couples user-greenhouse to 4 and the number of graphs per each of them to also 4. 

-‌ The Accuweather API that is used to receive real time data about the greenhouses' cities offers a very limited number of available calls for the same API key, therefore it should be changed frequently.

-‌ The MQTT broker that is used, Eclipse Mosquitto, is a widely used open source broker, in fact few times, during testing phase, it was not able to manage and deliver correctly published messages.
