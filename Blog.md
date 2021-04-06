
Air pollution can cause serious harm to human and animal health. It is really upsetting to see how the air quality of different countries has been deterioated.Air pollution is measured by the Air Quality Index (AQI). The index reflects a scale that ranges from 0 to 500. The higher the AQI value, the greater the health risk.


## Today,we will learn to how to create a web application using Azure map and free API from Air Quality Open Data Platform.


### Prerequisites:

* Python 3.6 or later
* Any IDE (I used Visual Studio)
* [An Azure Portal Account](https://azure.microsoft.com/en-in/free/search/?&ef_id=EAIaIQobChMIyoqGs-Hp7wIVTCUrCh2bhgOPEAAYASABEgKSYfD_BwE:G:s&OCID=AID2100054_SEM_EAIaIQobChMIyoqGs-Hp7wIVTCUrCh2bhgOPEAAYASABEgKSYfD_BwE:G:s)


### STEP 1: CREATE AN AZURE MAPS ACCOUNT

1. You can get your free azure account for 12 months with free services.If you don’t have one create here : [Azure account](https://azure.microsoft.com/en-in/free/search/?&ef_id=EAIaIQobChMIyoqGs-Hp7wIVTCUrCh2bhgOPEAAYASABEgKSYfD_BwE:G:s&OCID=AID2100054_SEM_EAIaIQobChMIyoqGs-Hp7wIVTCUrCh2bhgOPEAAYASABEgKSYfD_BwE:G:s)

2. After you go to your Azure Portal ,select create a resource.

3. In the search box,type Azure Map account

![alt text](https://github.com/Kritika37/global-air-quality-with-Azure/blob/main/pictures/g1.png)
you will be able to see this

4. Select create and fill the required things

* Subscription: Your subscription (Mine is Visual studio subscription one but you can select free one)

* Resource group: Select Create new (Name it something relatable as per the app)

* Name: A unique value, such as global-air-quality

* Pricing tier: Standard S1

5. Now,finally select Review+create and then go to resource. There on the left pane you will see a tab named Authentication.

![alt text](https://github.com/Kritika37/global-air-quality-with-Azure/blob/main/pictures/g2.png)

6. After selecting it you will see keys,Copy and save the value of primaryKey without the quotation marks. We will need this in future.


### STEP 2:SET UP THE DEVELOPMENT ENVIRONMENT FOR THE APP

1. Create a directory for the code (since we are creating a air quality app, i will name it Air-Quality-checker).

2. After installing python 3 ,create a virtual environment

```
# macOS or Linux 
# Create the environment 
  python3 -m venv venv 
# Activate the environment 
  source ./venv/bin/activate

  ```

3. Now,open the directory in your IDE. After that create a requirements.txt file inside the main folder and add the following:

```
flask
python-dotenv
requests

```

4. Return to your terminal or command and perform the following command,this will install all the required things :

```
pip install -r requirements.txt
```

> Congratulations! You’ve now setup your environment for development!


### STEP 3: LET'S CREATE THE APP

1. Typically, the entry point for Flask applications is a file named app.py. We’re going to follow this convention and create the core of our application.

2. At the root of your application code folder, create a folder named templates. This folder will hold the HTML templates that the Flask app will use.

3.  Create a new file in the main directory named .env,this will have your key which we saved earlier.

```
MAP_KEY=<your map key>
Replace <your map key> with the value of the primary key you retrieved after you created the Azure maps account

```

4. Create a new file named app.py and add this code to your flask application. 

```
import os, json
from flask import Flask, render_template, request
import requests

# Load the Azure Maps key from the .env file.
MAP_KEY = os.environ["MAP_KEY"]

# Initialize the Flask app.
app = Flask(__name__)

# Handle requests to the root of the website, returning the home page.
@app.route("/")
def home():
    # Create data for the home page to pass the Maps key.
    data = { "map_key" : MAP_KEY }
    # Return the rendered HTML page
    return render_template("home.html", data = data)
    ```


This code handles requests to /, which is the root of the website. When this webpage is loaded, the app uses the key from your .env file to create data. The data is used to render the home.html file as a parameter named data.

5. In Visual Studio Code, in the templates folder, create a new HTML file named home.html.

```
<!doctype html>
<html>
<head>
    <title>Air quality tracker</title>
    <!-- Ensures that Internet Explorer and Microsoft Edge use the latest versions and that they don't emulate earlier versions. -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">
    <meta charset='utf-8'>
    <!-- Ensures that the webpage looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <!-- Import the Azure Maps control. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    <style>
        html,
        body {
            margin: 0;
        }
        #myMap {
            height: 100vh;
            width: 100vw;
        }
    </style>
</head>
<body>
    <div id="myMap"></div>
    <script type="text/javascript">
        window.addEventListener("DOMContentLoaded", function () {
            // Pick a predefined location of the Microsoft headquarters.
            map_center = [-122.136866, 47.642472]

            // If the user grants permission when prompted, get the user's location.
            if (navigator.geolocation) {
                navigator.geolocation.getCurrentPosition(function (position) {
                    map_center = [position.coords.longitude, position.coords.latitude]
                })
            }

            // Create an instance of the map control by using the map key from the Flask app.
            var map = new atlas.Map('myMap', {
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '{{ data.map_key }}'
                }
            });

            // When the map is ready, center the map on the user's location.
            map.events.add('ready', function () {
                map.setCamera({
                    center: map_center,
                    zoom: 5
                })
            })
        })
    </script>
</body>
</html>
```


This webpage renders a full-screen div element that has an ID of myMap. After the page is fully loaded, in the browser, the app requests the user's location. The app can get the user's location only if the user grants permission. 


6. Now we are ready to test our home page on localhost:5000 ,which is default port number for http requests in flask.

```

Linux/macOS (server will automatically reload with every change)

export FLASK_ENV=development

Now you can just type this for running your server

flask run
```


### STEP 4: SHOW AIR QUALITY DATA ON MAP

> This the most important part where we retrive the data in json format from the API and display on the map. Keep Patince we will get through this :)

1. To get the API Token

2. Go to the [Air Quality Open Data Platform](https://aqicn.org/data-platform/token/#/?azure-portal=true) token request page on the WAQI website.

3. Enter your email address and name.

4. Review and agree to the API usage terms of service.

5. Select Submit.

6. Check your email inbox for a confirmation message from the WAQI website. In the email message, select the Confirm your email address link.

7. After you confirm your email address, you’re redirected to a new webpage that displays your token. Copy and save your token.

The token is the WAQI API key value 

8. Now add these to our already created .env file

```
WAQI_API_KEY=<your waqi key>

Replace <your waqi key> with the value of your WAQI API key.

```

9. Now we need to load this WAQI API KEY in our app,so we need include this in app.py

```
# Load the World Air Quality Index key from the .env file.
WAQI_API_KEY = os.environ["WAQI_API_KEY"]
WAQI_API_URL = https://api.waqi.info/map/bounds/?latlng={},{},{},{}&token={}

this basically calls the api to get the air quality readings

```

10. Now open the home.html file in the templates folder for our app. And replace this code 

```
// When the map is ready, center the map on the user's location.
map.events.add('ready', function () {
    map.setCamera({
        center: map_center,
        zoom: 5
    })
})
```

with this

```
// When the map is ready, center the map on the user's location.
map.events.add('ready', function () {
    // Declare a data source for the AQI data.
    var datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    // Declare a function to update the AQI data.
    function updateAQIData(e) {
        // Get the current bounds on the screen.
        bounds = map.getCamera().bounds

        // Set the data source data to results of the AQI call.
        // This is a feature collection that contains the AQI measurements.
        fetch('./aqi?bounds=' + bounds)
            .then(res => {
                return res.json()
            }).then(response => {
                datasource.clear()
                datasource.setShapes(response)
            })
    }

    // Add a bubble layer.
    map.layers.add(new atlas.layer.BubbleLayer(datasource, null, {
        radius: 10,
        opacity: 0.5,
        strokeOpacity: 0,
        // Get the color from the color property.
        color: ['get', 'color']
    }));

    // Handle any events that change the bounds of the map.
    map.events.add('zoomend', updateAQIData)
    map.events.add('dragend', updateAQIData)
    map.events.add('pitchend', updateAQIData)

    map.setCamera({
        center: map_center,
        zoom: 5
    })
})
```


> This data source is used to create a bubble/circle type that shows circles on a map. This get the color according to Air Quality index and represnet it on the map['get', 'color'] and tells the map to load the color from a property of the feature called color.


11. Now the final step for calling our API .It calls the API inside the Flask app that loads the AQI data from the API for a specific set of coordinates. The AQI data is then converted to a feature collection and returned as a JSON string. Then, the API can be called from the webpage.

12. open the app.py file.

Add the following code to the bottom of the file:



```
def get_color(aqi):
    # Convert the AQI value to a color.
    if aqi <= 50: return "#009966"
    if aqi <= 100: return "#ffde33"
    if aqi <= 150: return "#ff9933"
    if aqi <= 200: return "#cc0033"
    if aqi <= 300: return "#660099"
    return "#7e0023"

def load_aqi_data(lon1, lat1, lon2, lat2):
    # Load the air quality data.
    url = WAQI_API_URL.format(lat1, lon1, lat2, lon2, WAQI_API_KEY)
    aqi_data = requests.get(url)

    # Create a GeoJSON feature collection from the data.
    feature_collection = {
        "type" : "FeatureCollection",
        "features" : []
    }

    for value in aqi_data.json()["data"]:
        # Filter out empty values.
        if value["aqi"] != "-":
            feature_collection["features"].append({
                "type" : "Feature",
                "properties" : {
                    "color" : get_color(int(value["aqi"]))
                },
                "geometry" : {
                    "type":"Point",
                    "coordinates":[value['lon'], value['lat']]
                }
                })

    return feature_collection

@app.route("/aqi", methods = ["GET"])
def get_aqi():
    # Get the bounds from the request.
    bounds = request.args["bounds"].split(",")

    # Load the AQI data and create the GeoJSON for the specified bounds.
    return json.dumps(load_aqi_data(bounds[0], bounds[1], bounds[2], bounds[3]))
    ```

![alt text](https://github.com/Kritika37/global-air-quality-with-Azure/blob/main/pictures/output.png)


> After so much of hard work,here we go.Congratulations We’ve now successfully created a website that uses Azure map and an API to track the air qulaity globally.

Complete code: <https://github.com/Kritika37/global-air-quality-with-Azure>

Questions? Comments? Feel free to leave your feedback in the comments section or contact me directly at <https://www.linkedin.com/in/kritikasagar/>
