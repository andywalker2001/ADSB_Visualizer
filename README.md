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

Additional details are available in the “Developers_Addendum.md” file in the /Doc folder 

def rotate\_icon(angle): Rotates a PNG icon to a an angle specified by the caller and saves (overwrites) the newly rotated image for use as an icon on a map.

def plot\_vector(coord1, coord2, my\_map, col = "blue"): Takes as input the location of an aircraft along with itÆs last location to generate a ætrail of antsÆ vector that points in the direction of travel. WIP.
