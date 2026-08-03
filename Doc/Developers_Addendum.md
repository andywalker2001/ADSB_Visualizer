<img width="1675" height="463" alt="Header" src="https://github.com/user-attachments/assets/75ca770e-477a-4ed5-99f9-95702f5528a6" />

### Developers Adendum:

This project includes 3 different versions of the application, one command line, one using flask and focused on Streamlit. The Streamlit app is the main.py application. However the other versions are included to ease inclusion in other, larger projects.

The command line application can be found in the ./CLI folder and can be invoked with:

``python .\command\_line\_app.py``

<img width="1478" height="507" alt="Figure_3" src="https://github.com/user-attachments/assets/764c14c1-07bc-4db7-bf42-7297591ec27e" />

Figure 1: Gives example output, tabulated for easy reading.

The output uses Lat/Lon/Altitude to each aircraft to produce an Azimuth and Elevation pointing angle from the sensor to the aircraft. A Slant Range to the aircraft is also calculated to give an approximate location in 3-space of the aircraft to guide the sensor. The æmaskedÆ indicator gives a sense of whether the aircraft should be visible to the sensor or not due to terrain masked, and the time stamp returns the time of the measurement.

The flask applications builds on the command line script, plotting the data of the aircraft with hotspot popups on a moving map. Invoking flask should yield output as shown in Figure 4.

<img width="1204" height="697" alt="Figure_4" src="https://github.com/user-attachments/assets/41cb9440-b5fe-4241-807f-a6dc452f53c8" />

Figure 2: Example Output from CLI Version

Figure 5 shows the hosted web page from the Flask server. As can be seen in the figure, no controls are available on the Flask served page, instead simply showing the location of the aircraft with their respective popups.



Figure : Expected Flask Server Invocation and Output

<img width="1131" height="694" alt="Figure_5" src="https://github.com/user-attachments/assets/228f342c-54b8-4e48-801c-492996d295a8" />

Figure 3: Flask Application User Interface

### Command Line Functionality

Developers interested in using this work can directly utilize the functions available in the library by including it in their project:

``import MyModules``

**Functions Implemented:**

def calculate\_slant\_range(radar, plane): Calculates the azimuth and elevation angles from the sensor location to the aircraft as well as a slant range. The slant range is the range that the sensor would need to detect and track over.

def read\_gps\_coordinates(serial\_port='COM5', baud\_rate=4800, timeout=1, max\_attempts=50): Reads the location of the sensor from a GPS receiver on COM5 (default). This output will be refined with information from the Iowa Real Time Kinematic Network in future work.

def calculate\_radar\_range(pt\_watts=250, gain\_db=26, num\_pulses=1000, freq\_hz=2.45e9, rcs\_sqm=1.0, s\_min\_watts=1e-13, loss\_db=0): Calculate the effective range of the sensor given 1000 coherenet pulses. This is the equation used to generate the range æstop-lightÆ rings shown on the map.

def call\_api(latitude, longitude, altitude, limit\_range="75", units="M"): Collects the data from the ADS-B API.

def plot\_map (latitude, longitude, range\_10, range\_20, range\_30, my\_map): Initializes æmy\_mapÆ and plots the range rings that show what aircraft are within detection range of the sensor.

def plot\_plane (coord1, coord2, my\_map, description, col = "blue", heading = 0): Plots the locations of the planes. One of the next steps is to add unobtrusive persistence to this plotting.

def get\_masking(coord1, coord2, num\_segments): Calculates the elevation at each point along a line projected on the ground between the sensor and the aircraft. If any of the elevations are higher than the elevation of the line of sight between the sensor and the aircraft, the line of sight is marked æmaskedÆ and the aircraft is plotted in red to show that it is probably not visible to the sensor.

def filter\_list(r): Consumes the JSON file ærÆ and filters out the information from the ADS-B return that is used in the subsequent processing. Altitude, latitude, and longitude are copied from input to output. Heading is available but not used; instead the tracking algorithm should use a æplausibility circleÆ to determine which aircraft past state corresponds to which aircraft current state.

def set\_location(Port="COM5"): Has become the single function for defining the location of the center node. Accommodates GPS coordinates or variable defaults. Applies to AC\_Range.py, app.py, and streamlit\_app.py

def rotate\_icon(angle): Rotates a PNG icon to an angle specified by the caller and saves (overwrites) the newly rotated image for use as an icon on a map.

def plot\_vector(coord1, coord2, my\_map, col = "blue"): Takes as input the location of an aircraft along with itÆs last location to generate a ætrail of antsÆ vector that points in the direction of travel. WIP.

def extract\_matching\_flights(file1\_path, file2\_path): Finds matching flights between two files and outputs a new file containing pairs of (lat, lon) coordinates for those flights.

Data/print\_data.py: Formats the data.txt file for easy inspection

Data/print\_output.py: Formats the output.txt file for easy inspection

**Data Files Generated:**

Ac\_range.log: The formatted table of Azimuth and Elevation angles and slant ranges from the sensor location to each aircraft, including terrain masking bit and time of measurement in UTC.

Data.txt: A JSON file (a dictionary of lists of dictionaries) of data returned from the ADSB API call containing all the dat of the aircraft within range of the sensor

Interactive\_map.html: An HTML file that, once rendered, shows the locations of each aircraft, the range rings, and the popup content of each aircraft.

Output.txt: A filtered list of aircraft data including flight number, altitude according to GPS, the track the aircraft is flying, the latitude and longitude of each aircraft.

Output.old: The list of aircraft data from the prior API call. This data is preserved from call to call so the track can be verified. This method matches the track solution the sensor would generate, since no self-reported data is available to the sensor in realistic scenarios.

**Some Anticipated Errors:**

A range of errors will be returned if the data files are not available in the /Data file due to incomplete error checking for file presence and content. Therefore, once all packages are installed, any remaining errors should first be mitigated by copying old data files to the /Data directory.

**Ongoing Work:**

As of this writing, there are 2 outstanding tasks that should be reengaged:

1. The proper rotation of the aircraft to match the true and reported headings
2. The ætrail of antsÆ moving vector that illustrates a simple linear extrapolation of the current heading

Both of these have been working in previous development versions and only need debug to incorporate back into the main code line.

Next steps are described in other documents in the /Doc folder and focus on integration of the calculations and visualizations into a larger control UI for a real multi-static sensor.
