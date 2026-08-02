<img width="1675" height="463" alt="Header" src="https://github.com/user-attachments/assets/411a0a07-8429-4ccc-8340-4b4c482c1a4e" />
 
### Project Introduction

In the past, radar research has been limited to large companies and those with exceptional budgets. However, Amateur Radio enthusiasts are increasingly able to buy and build capable microwave systems for communications, sharing of data, and sensing of obstacles. These capabilities are primed to push amateur electronics into the realm of real microwave sensors like radars. Radars use radio signals to detect and track ætargetsÆ in their field of view, and larger targets make for easier, more certain detections. Commercial aircraft provide excellent targets of opportunity for such a system. They are physically and electrically large, they move at high speed and broadcast their position regularly via radio links that can be used to provide a source of truth for the hobbyist measurement. This enables educational radar training by providing a test bed for unclassified, amateur radio compatible radar. This project captures the broadcast position data and maps it relative to the sensor location to provide ætargets of opportunityÆ to the sensor.

### How to install

Install the unique requirements by opening a terminal in the folder where the files were downloaded and executing:

``pip install -r requirements.txt``

This will install the non-standard libraries that this project requires. Once finished successfully, the final version should be started with:

``streamlit run .\main.py``

This should automatically open a web browser window that looks like Figure 1 and Figure 2. In this user interface, the primary map interface is front and center. To the side, buttons are present to configure range details of the aircraft plotter (1), the automatic map updating (2), start and stop the background application (3), and the log of the background process (4). In (1), the latitude, longitude, and altitude mark the location of the sensor (the nominal radar sensor), and the range number sets how far out the presumed sensor can sense. In practical terms, this is used to limit the number of aircraft plotted to improve update rate performance and memory utilization.

<img width="1528" height="675" alt="Figure_1" src="https://github.com/user-attachments/assets/4dc1c0a5-f174-429d-b57f-42f34c90663c" />

Figure 1: HTML Interface Hosted by Streamlit

<img width="1528" height="675" alt="Figure_2" src="https://github.com/user-attachments/assets/3a4a0245-5d94-4ba9-acf3-7bed3e7ea958" />

Figure 2: HTML Interface with Side Panel Extended

<img width="1478" height="507" alt="Figure_3" src="https://github.com/user-attachments/assets/cf8e180a-79dc-4888-a13f-c28a8bed06c2" />

### APIs Used

To plot this date, 2 APIs are used. The first is provides the ADSB data of the aircraft in the area, and the second provides topographic maps from which terrain masking is extracted.

https://globe.adsbexchange.com/

ADS-B Exchange is the world's largest independent and unfiltered global flight tracking data network, providing unmitigated civil and military flight tracking data. It operates via a crowdsourced community of feeders sharing raw receiver data without filtering out specific aircraft. The platform focuses on raw data access without FAA-compliance filtering or requested blockings for military and private aircraft provided by global volunteers hosting hardware receivers feeding real-time positional data (latitude, longitude, altitude, and velocity) into the collective map.

This API can be used with or without a key. However, responsiveness is greatly improved via a low-cost API key subscription.

https://open-meteo.com/en/features

OpenMeteo provides a wide range of weather-related data via an easy to use API. Among the interfaces provided is an Elevation API providing access to a terrain database that returns the terrain elevation at the provided coordinates. Terrain elevation is provided in meters for any set of WGS84 coordinates using a 90 m resolution digital elevation model and accepts batches of up to 100 coordinate pairs per request.

This API requires no key to extract elevation information. The returned data is limited and does impact update rate.

### Developers Adendum:

This project includes 3 different versions of the application, one command line, one using flask and focused on Streamlit. The Streamlit app is the main.py application. However the other versions are included to ease inclusion in other, larger projects.

The command line application can be found in the ./CLI folder and can be invoked with:

``python .\command\_line\_app.py``

Figure 3 gives example output, tabulated for easy reading.

![](data:image/png;base64...)

Figure : Example Output from CLI Version

The output uses Lat/Lon/Altitude to each aircraft to produce an Azimuth and Elevation pointing angle from the sensor to the aircraft. A Slant Range to the aircraft is also calculated to give an approximate location in 3-space of the aircraft to guide the sensor. The æmaskedÆ indicator gives a sense of whether the aircraft should be visible to the sensor or not due to terrain masked, and the time stamp returns the time of the measurement.

The flask applications builds on the command line script and plots the data of the aircraft with hotspot popups on a moving map. Invoking the flask application and the expected output is shown in Figure 4.

![](data:image/png;base64...)

Figure : Expected Flask Server Invocation and Output

Figure 5 shows the hosted web page from the Flask server. As can be seen in the figure, no controls are available on the Flask served page, instead simply showing the location of the aircraft with their respective popups.

![](data:image/png;base64...)

Figure : Flask Application User Interface

### Command Line Functionality

**Functions Implemented:**

def calculate\_slant\_range(radar, plane): Calculates the azimuth and elevation angles from the sensor location to the aircraft as well as a slant range. The slant range is the range that the sensor would need to detect and track over.

def read\_gps\_coordinates(serial\_port='COM5', baud\_rate=4800, timeout=1, max\_attempts=50): Reads the location of the sensor from a GPS receiver on COM5 (default). This output will be refined with information from the Iowa Real Time Kinematic Network in future work.

def calculate\_radar\_range(pt\_watts=250, gain\_db=26, num\_pulses=1000, freq\_hz=2.45e9, rcs\_sqm=1.0, s\_min\_watts=1e-13, loss\_db=0): Calculate the effective range of the sensor given 1000 coherenet pulses. This is the equation used to generate the range æstop-lightÆ rings shown on the map.

def call\_api(latitude, longitude, altitude, limit\_range="27", units="M"): Collects the data from the ADS-B API.

**d**ef plot\_map (latitude, longitude, range\_10, range\_20, range\_30, my\_map): Initializes æmy\_mapÆ and plots the range rings that show what aircraft are within detection range of the sensor.

def plot\_plane (coord1, coord2, my\_map, description, col = "blue"): Plots the locations of the planes. One of the next steps is to add unobtrusive persistence to this plotting.

def get\_masking(coord1, coord2, num\_segments): Calculates the elevation at each point along a line projected on the ground between the sensor and the aircraft. If any of the elevations are higher than the elevation of the line of sight between the sensor and the aircraft, the line of sight is marked æmaskedÆ and the aircraft is plotted in red to show that it is probably not visible to the sensor.

def filter\_list(r): Consumes the JSON file ærÆ and filters out the information from the ADS-B return that is used in the subsequent processing. Altitude, latitude, and longitude are copied from input to output. Heading is available but not used; instead the tracking algorithm should use a æplausibility circleÆ to determine which aircraft past state corresponds to which aircraft current state.

def set\_location(Port="COM5"): Has become the single function for defining the location of the center node. Accommodates GPS coordinates or variable defaults. Applies to AC\_Range.py, app.py, and streamlit\_app.py

def rotate\_icon(angle): Rotates a PNG icon to a an angle specified by the caller and saves (overwrites) the newly rotated image for use as an icon on a map.

def plot\_vector(coord1, coord2, my\_map, col = "blue"): Takes as input the location of an aircraft along with itÆs last location to generate a ætrail of antsÆ vector that points in the direction of travel. WIP.
