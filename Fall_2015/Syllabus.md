# Advanced GIS: A Seminar in Spatial Analysis
* Columbia University | GSAPP | Planning A6232 | Fall 2015
* Tuesdays and Thursdays 7-9pm | 202 Fayerweather (computer lab)
* Office hours: Mondays 10am - 12pm (previous email required)
* Professor: Juan Francisco Saldarriaga (jfs2118@)
* Teaching Assistant: Mehak Sachdeva (ms4978@)

## Course Overview
This research seminar is meant to provide students with advanced analytical and practical skills in Geographic Information Systems (GIS) and Spatial Analysis. In addition, the seminar also aims to teach students how to identify and frame unique research questions, how to develop a methodology for answering those questions and how to visually represent their research and findings. Finally, the class also seeks to introduce students to new techniques in mapping and collecting existing data.

## Advanced Topics
* Developing research questions
* Public participation, web-mapping and APIs
* Surface modeling
* Spatial statistics
* Remote sensing
* Decision support
* Transportation and land-use
* Scripting
* Data visualization

## Evaluation and Grading
* 5% Attendance
* 10% Class participation and discussion
* 30% Individual assignments
* 15% Final project proposal and midterm presentation
* 40% Final project and final presentation and report

## Resources and Materials
Course files, tutorials and presentations will be located on the X-Drive. This drive also contains the GIS data available to all GSAPP students. Students are encouraged to explore the data that already exists in the drive and if necessary, use it in their projects. In addition, students should also contact the Digital Social Science Center (DSSC) located in Lehman Library (SIPA) for extra information and data.

The readings for the class will be duly uploaded to Courseworks. Similarly, students will be required to submit their assignments by uploading them to Courseworks. Finally, the class will also rely heavily on submissions
to the blog. Students will be required to upload some of their own work as well as inspirational material, encouraging and developing a critical stance and visual skills.

[Link to the blog](http://gsappadvgis2015fall.tumblr.com/).

[Link to number of posts](https://docs.google.com/spreadsheets/d/11O2WTCk-XuzbKksEJHnpIR-pAACmjbIqgfVusWHabV0/edit?usp=sharing).

## Schedule
### Week 1: Introduction
Course administration and syllabus:
* *Meirelles, Isabel. Design For Information, Chapters 4 and 5*
* *Batty, Michael. Cities and Complexity: Understanding Cities with Cellular Automata, Agent-Based Models, and Fractals, Introduction*
* *[What is Code? - Bloomberg](http://www.bloomberg.com/graphics/2015-paul-ford-what-is-code/)*
* *[Digital Matatus - Wired](http://www.wired.com/2015/08/nairobi-got-ad-hoc-bus-system-google-maps/)*

Introduction to Python and APIs:
* Python references:
  * [Python 2.7.10 Documentation](https://docs.python.org/2/)
  * [stackoverflow](https://stackoverflow.com/)
  * [codecademy Python course](https://www.codecademy.com/en/tracks/python)
  * [Lynda Python tutorial](http://www.lynda.com/Python-tutorials/Up-Running-Python/122467-2.html)
  * [Plethora Project - Python for Rhino and other tutorials](http://www.plethora-project.com/education/)
* Examples of what people have done with APIs:
 * [Netflix - New York Times](http://nyti.ms/1gldFbG)
 * [Flickr - Tourists vs. Locals](http://bit.ly/JKpE84)
 * [Foursquare - Personal timeline](https://foursquare.com/timemachine)
 * [Foursquare - Overall](https://foursquare.com/infographics/pulse)
 * [Drone Strikes](http://drones.pitchinteractive.com/)
 * [New York Times](http://bit.ly/1ilGWEF)
* Some Python IDE (integrated development environment) or text editors:
 * [Canopy](https://www.enthought.com/products/canopy/)
 * IDLE: Comes pre-installed with ArcGIS
 * [Sublime Text](https://www.sublimetext.com/)
* Some basic Python concepts:
 * Variables
 * Loops
 * Nested loops
 * Conditionals
 * Functions
 * Comments (one line and multiple lines)
 * Shell vs. Files
 * Pseudocode
 * Modules or Libraries
 * Global vs. Local variables
 * Reading and writing files

### Week 2: Scripting - Basic Python and APIs
Introduction to Python and APIs:
* Code so far:
```python
# Importing libraries
print 'Importing libraries...'
import urllib2 # Now I am importing the whole urllib2 library, not just the urlopen function, in order to catch any HTTPErrors
from json import load
import csv, time, codecs

# Setting global variables
clientID = 'your client id here'
clientSecret = 'your client secret here'
baseURL = 'https://api.foursquare.com/v2/venues/search'
limit = 5 # This limit can be adjusted to a maximum of 50

# Opening the points file
pointFileLocation = 'path to your points file'
with open(pointFileLocation, 'rb') as basePoints:
    reader = csv.reader(basePoints, delimiter = ',')
    pointsList = list(reader)

# Opening the output file
output = codecs.open('path to your output file', 'wb', encoding='utf-8') # For tab delimited files, it's best to save your file as .txt

# Writing the first line of the output file (header)
output.write('Name' + '\t' + 'lat' + '\t' + 'lon' + '\t' + 'checkins' + '\n')

# Starting the loop (for every single line in the points file)
errors = 0 # I'm setting up this variable to keep track of the number of errors
for line in pointsList:
    lat2 = line[2]
    lon2 = line[3]
    # Querying the API
    print 'Querying the API...'
    try:
        # I've changed the request to use client ID and client secret because it gives you a higher API quota (5,000 requests per hour)
        request = baseURL+'?'+'ll='+str(lat2)+','+str(lon2)+'&client_id='+clientID+'&client_secret='+clientSecret+'&v=20150918'+'&limit='+str(limit)
        response = urllib2.urlopen(request) # Now we have to call the urlopen function, from the urllib2 module like this: urllib2.urlopen
        baseData = load(response)
        venues = baseData['response']['venues']
        # Looping through the venues and getting some of the data out
        for x in range(len(venues)):
            venueName = venues[x]['name']
            location = venues[x]['location']
            venueLat = location['lat']
            venueLon = location['lng']
            stats = venues[x]['stats']
            venueCheckinsCount = stats['checkinsCount']
            output.write(venueName + '\t')
            output.write(str(venueLat) + '\t')
            output.write(str(venueLon) + '\t')
            output.write(str(venueCheckinsCount) + '\n')
            venueName2 = venueName.encode('utf8', 'replace') # This line makes any weird characters in the venue name printable for the next line
            print venueName2 + ', ' + str(venueLat) + ', ' + str(venueLon) + ', ' + str(venueCheckinsCount)

        print 'Done with loop ' + str(line[0])

    # Catch any HTTP errors and print them to the output screen
    except urllib2.HTTPError as e:
        errors += 1
        print e
        # If you start getting 403 errors, it's probably because you've exceeded your quota. So you have to wait for a while before you can query the API again.

    # Wait for one second before starting the next query
    time.sleep(1)

# Closing the output file
output.close()

print 'Done with everything...'
print 'There were ' + str(errors) + ' errors...'
```
* Common APIs:
 * [Google Geocode API](https://developers.google.com/maps/documentation/geocoding/intro)
 * [Google Directions API](https://developers.google.com/maps/documentation/directions/intro)
 * [Mapquest Geocode API](https://developer.mapquest.com/products/geocoding)
 * [Mapquest Directions API](https://developer.mapquest.com/products/directions)
 * [Foursquare API](https://developer.foursquare.com/)
 * [Twitter API](https://dev.twitter.com/overview/documentation)
 * [NPR API](http://www.npr.org/api/index.php)
 * [Facebook API](https://developers.facebook.com/)
 * [Instagram API](https://instagram.com/developer/)
 * [Zillow API](http://www.zillow.com/howto/api/APIOverview.htm)
 * [Trulia API](http://developer.trulia.com/)
* Possible exercises:
 * Analyze bike lanes in New York based on Citibike origin and destination (using Google Maps - Directions - API)
 * Compare PLUTO land-use information with Foursquare venues or check-ins.
 * Collect data from Twitter using specific hashtags and analyze it geographically.
 * Map taxi routes using data from the TLC and Google Maps - Directions - API.
 * Measure "deadhead" trips for the Green cabs using Google Maps - Directions - API or Mapquest directions API.

### Week 3: Scripting - Arcpy and scripting in ArcGIS
ArcGIS scripting functions:
* Different ways of scripting in ArcGIS:
 * Field calculation in the Attribute Table
 * Labeling features
 * Python console in ArcMap
 * Export tool as Python script
 * Export Geoprocessing Results as Python script
 * Standalone scripts with ArcPy module
* Other GIS scripting tools:
 * [Python console in QGIS](http://docs.qgis.org/testing/en/docs/pyqgis_developer_cookbook/intro.html)
 * [GDAL](https://pypi.python.org/pypi/GDAL/)

Exercises:

* Label census tracts based on their median household income:
 * Create a new field in the attribute table and write a script to fill in the values for the label.
 * Use the expression option in the layer properties and write a script to calculate the appropriate label.
* Study "deadhead" green cab trips and figure out the distribution of origins in the edge TAZs
 * Create a random sample (5,000 or 10,000) trips.
 * Create two shapefiles with the random sample, one with origins and one with destinations.
 * Manually select the "deadhead" trips from the destination shapefile and extract stats.
 * Manually select all the "edge" TAZs polygons.
 * Write a script to select by location the origins of green cabs for each "edge" TAZ and exports a .csv file with their stats.
 * Calculate the distance from each destination to the nearest "edge" TAZ (centroid).
 * Calculate the distance from each destination to all "edge" TAZ (centroid) and multiply by proportion.
 * Add all distances and figure out the range for the "wasted" trips.
 * Files to use: *Traffic Analysis Zones (X:/GIS/New_York_City/Transportation/TAZ)* & *[Green cab trips](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml)*
* Code - Creating random samples (for multiple files):
```python
print 'Importing modules...'
import csv, random

# Setting up global variables
inputLocation = 'path to your folder'
outputLocation = inputLocation
#baseFile = 'green_tripdata_2015-05.csv' These are commented out because we are using the loop to generate the file names
#outputFile =  'Random_Green_201505.csv'

randomPopulation = 5000

# Looping through each of the files and creating the random sample
for x in range(1,7): # Since we only have 6 files we loop from 1 to 7
    baseFile = 'green_tripdata_2015-' + "%02d" % (x,) + '.csv' # We use "%02d" % (x,) to format x as 01, 02, 03, etc...
    with open(inputLocation + '/' + baseFile, 'rb') as baseData: # Reading the base file
        reader = csv.reader(baseData, delimiter = ',')
        tripList = list(reader)
    outputFile = 'Random_Green_20150' + str(x) + '.csv'
    output = open(outputLocation + '/' + outputFile, 'wb') # Creating the output file

    output.write(','.join(tripList[0]) + '\n') # Writing the first line (headers)

    randomSample = random.sample(tripList[1:], randomPopulation) # Creating the sample list

    for line in randomSample:
        output.write(','.join(line) + '\n') # Writing every line from the sample list to the output file

    output.close()
    tripList = [] # Emptying the tripList array so that we don't run out of memory
    print 'Done with file ' + str(x)

print 'Finished....'
```
* Code - Creating shapefiles based on X and Y data (for multiple files):
```python
print 'Importing modules...'
import csv
import arcpy

# Setting up global variables
inputLocation = 'path to your folder'
outputLocation = inputLocation

arcpy.env.workspace = inputLocation
arcpy.env.overwrite = True

# Setting up variables for X and Y data
originX = 'Pickup_longitude'
originY = 'Pickup_latitude'
destinationX = 'Dropoff_longitude'
destinationY = 'Dropoff_latitude'

# Setting up variable with the appropriate projection
spRef = r'Coordinate Systems\Geographic Coordinate Systems\World\WGS 1984.prj'

# Loop through each one of the files
for x in range(1,7):
    print 'Starting the loop for file ' + str(x)
    baseFile = 'Random_Green_20150' + str(x) + '.csv'
    outputOrigin = 'Random_Green_20150' + str(x) + '_Origins_B.shp'
    outputDestination = 'Random_Green_20150' + str(x) + '_Destinations_B.shp'
    tempLayerOrigin = 'tempLayerOrigin' + str(x)
    tempLayerDestination = 'tempLayerDestination' + str(x)
    
    # Starting the process with the ORIGIN data
    print 'Creating the XY event layer...'
    arcpy.MakeXYEventLayer_management(baseFile, originX, originY, tempLayerOrigin, spRef)
    # Couting the number of trips in the file, to make sure it's correct
    trips = arcpy.GetCount_management(tempLayerOrigin)
    print 'There are ' + str(trips) + ' trips in the file...'
    print 'Copying the origins layer....'
    arcpy.CopyFeatures_management(tempLayerOrigin, outputOrigin)

    # Starting the process with the DESTINATION data
    print 'Creating the XY Destination event layer...'
    arcpy.MakeXYEventLayer_management(baseFile, destinationX, destinationY, tempLayerDestination, spRef)
    # Couting the number of trips in the file, to make sure it's correct
    trips = arcpy.GetCount_management(tempLayerDestination)
    print 'There are ' + str(trips) + ' trips in the file...'
    print 'Copying the destinations layer....'
    arcpy.CopyFeatures_management(tempLayerDestination, outputDestination)

print 'Finished..............'
```

### Week 4: Scripting - Arcpy and scripting in ArcGIS (Part 2)
* Code - Selecting by location (for multiple files):
```python
print 'Importing modules...'
import csv
import arcpy

# Setting up global variables
inputLocation = 'path to your location'
outputLocation = inputLocation

arcpy.env.workspace = inputLocation
arcpy.env.overwrite = True

# Creating the layer for the file we will select with
deadheadFile = 'Deadhead_Zone_03.shp'
arcpy.MakeFeatureLayer_management(deadheadFile, 'deadheadFile') 


for x in range(1,7):
    print 'Starting the loop for file ' + str(x)
    # Creating the layer for the trip file
    tripFile = 'Random_Green_20150' + str(x) + '_Destinations_B.shp'
    tripOutput = 'DeadheadTrips_' + str(x) + '.shp'
    arcpy.MakeFeatureLayer_management(tripFile, 'tripFile' + str(x))
    
    # Counting the number of trips in the file
    trips = arcpy.GetCount_management('tripFile' + str(x))
    print 'There are ' + str(trips) + ' trips in the file...'
    
    # Selecting by location
    arcpy.SelectLayerByLocation_management('tripFile' + str(x), 'intersect', 'deadheadFile')
    
    # Counting the number of selected trips
    trips = arcpy.GetCount_management('tripFile' + str(x))
    print 'There are ' + str(trips) + ' selected trips in the file...'

    # Creating a new file based on the selected trips
    arcpy.CopyFeatures_management('tripFile' + str(x), tripOutput)

print 'Finished..............'
```
* Code - Projecting files (for multiple files):
```python
print 'Importing modules...'
import csv
import arcpy

# Setting up global variables
inputLocation = 'path to your folder'
outputLocation = inputLocation

arcpy.env.workspace = inputLocation
arcpy.env.overwrite = True

# Setting up the file from which we are getting the right projection (name)
sampleFile = 'Deadhead_Zone_03.shp'
spatialRef = arcpy.Describe(sampleFile).spatialReference
print 'Projecting to: ' + spatialRef.name

for x in range(1,7):
    print 'Starting the loop for file ' + str(x)
    deadheadTrips = 'DeadheadTrips_' + str(x) + '.shp'
    outputTrips = 'DeadheadTrips_Projected_' + str(x) + '.shp'
    
    # Projecting the files using the projection from the base file
    arcpy.Project_management(deadheadTrips, outputTrips, spatialRef)

print 'Finished..............'
```
* Code - Calculating distance to nearest point (for multiple files):
```python
print 'Importing modules...'
import csv
import arcpy

# Setting up global variables
inputLocation = 'path to your location'
outputLocation = inputLocation

arcpy.env.workspace = inputLocation
arcpy.env.overwrite = True

# File that contains the points to which we will calculate the distance
centroidFile = 'Edge_Taz_Centroid.shp'

for x in range(1,7):
    print 'Starting the loop for file ' + str(x)
    deadheadTrips = 'DeadheadTrips_Projected_' + str(x) + '.shp'
    
    # Calculating the distance from 'deadhead destinations' to nearest 'TAZ centroid'
    arcpy.Near_analysis(deadheadTrips, centroidFile)

print 'Finished..............'
```
* **Assignment:**
  * For every borough in the city select the lots that fall into the following categories:
    * Residential or Mixed-Use
    * Excess FAR (built FAR < maximum allowable FAR)
    * For buildings built between 1930 and 1958, FAR =< 4.9
    * For buildings built in 1959, FAR =< 7.5
    * For buildings built in 1960, FAR =< 11.25
    * For buildigns built after 1960, FAR =< 12.0
  * Create a map for each time period.
  * Get the following data from each time period:
    * Extra square footage that could have been built (based on the maximum allowable FAR).
    * Extra number of units that could have been built (based on an average size of 1,700 sf per unit).
    * Extra number of people that could have been housed (based on an average of 2.4 people per unit).
  * Get the same data just for buildings within 1/2 a mile of transit stops (subway stops).
  * Submit maps, charts, data, code and pseudocode.
  * Files to be used:
    * PLUTO lot files: X:/GIS/New_York_City/Buildings_Lots/Lots/PLUTO/2015_15v1/
    * Subway stations: X:/GIS/New_York_City/Transportation/Subway/Subway_Stations_2009/
    * Other files that you might need for your maps.
   * Example of answer code (there are many ways of doing this, this is just one of them):
```python
print 'Importing modules...'
import arcpy

inputLocation = 'path to your folder'
outputLocation = inputLocation

arcpy.env.workspace = inputLocation
arcpy.env.overwrite = True

# Creating the arrays to be used in the loops
print 'Setting up global variables...'
boroughs = ['BK', 'BX', 'MN', 'QN', 'SI']
years = [1930, 1959, 1960, 1961, 2016]
FAR = [4.9, 7.5, 11.25, 12]

# Starting the loops
print 'Making feature layer...'
for x in range(len(years)-1):
    print 'Starting the loop for ' + str(years[x])
    startYear = years[x]
    endYear = years[x + 1]
    maxFAR = FAR[x]
    for borough in boroughs:
        print 'Starting the loop for ' + borough
        arcpy.MakeFeatureLayer_management(borough + 'MapPLUTO.shp', 'featureFile')
        print 'Count of features in file = ' + str(arcpy.GetCount_management('featureFile'))

        # Selecting by attribute
        arcpy.SelectLayerByAttribute_management('featureFile', "NEW_SELECTION", """ "LandUse" = '01' OR "LandUse" = '02' OR "LandUse" = '03' OR "LandUse" = '04' """)
        arcpy.SelectLayerByAttribute_management('featureFile', "SUBSET_SELECTION", """ "YearBuilt" >= """ + str(startYear) + """ AND "YearBuilt" < """ + str(endYear))
        arcpy.SelectLayerByAttribute_management('featureFile', "SUBSET_SELECTION", """ ("BuiltFAR" - "ResidFAR") < 0 AND "BuiltFAR" < """ + str(maxFAR))
        print 'Count of selected features = ' + str(arcpy.GetCount_management('featureFile'))

        print 'Copying layer...'
        arcpy.CopyFeatures_management('featureFile', borough + '_selectedLots_' + str(years[x]))
        arcpy.Delete_management('featureFile') # Deletes 'featureFile' so it can be used again once the loop starts again

print 'All done...'

'''
# Listing fields - this is useful to know what fileds each shapefile has and their type
fields = arcpy.ListFields(boroughs[0] + '_featureFile')
for field in fields:
    print field.name
    print field.type
'''
```
* Optional exercise: Select top Citibike stations and taxi trips within a radius of those stations.
 * Files to use: *[Citibike trips](https://www.citibikenyc.com/system-data)* & *[Yellow cab trips](http://www.nyc.gov/html/tlc/html/about/trip_record_data.shtml)*
* **Sep. 29: APIs Review**:
 * 5 minute presentation (slides)
 * Reserach question
 * Maps, graphs and analysis (describe the data and your findings)
 * Pseudocode and code
 * Problems with coding or with APIs (what do we need to be careful about?)

### Week 5: Webmapping
CartoDB and Mapbox with Tilemill
* Webmapping presentation
* CartoDB exercise:
  * Create taxi activity and land-use maps (and combination):
    * Create file for one day of green cabs dropoffs
    * Load into CartoDB (explore ways of visualizing and filtering)
    * SQL example:
    * `SELECT * FROM table_01_selected_trips_20150601 WHERE fare_amount > 50 AND passenger_count <= 3`
    * [CartoDB Tips & Tricks](http://docs.cartodb.com/tips-and-tricks.html)
    * Load building footprint files
    * Show CartoCSS editor (yearbuilt category)
    * Show land-use
    * Combine both layers
    * Add basemap, title, info window and publish
* [CartoDB Tutorials](http://docs.cartodb.com/tutorials.html)
* Mapbox exercise:
  * Create project and choose basemap
  * Upload data and customize
  * Save map and get info
  * Add basemap to CartoDB and publish
* TileMill exercise:
  * Create project
  * Load data
  * Style data
  * Variables
  * Attributes
  * Zoom levels
  * Exporting
  * Loading onto Mapbox
  * Code example for TileMill:
```
@waterColor: #333333;
@landColor: #4C4C4C;
@color1900: #D90000;
@color1925: #FF8C00;
@color1950: #A7C520;
@colorMore1950: #3E606F;
@tripsColor: #FFFFFF;

Map {
  background-color: @waterColor;
}

#tristate {
  line-width:0;
  polygon-opacity:1;
  polygon-fill:@landColor;
}

#waterTristate {
  line-width:0;
  polygon-opacity:1;
  polygon-fill:@waterColor;
}

#MNBuildings {
  line-width:0;
  polygon-opacity:1;
  polygon-fill: @colorMore1950;
  [CONSTRUCTI < 1950]{
    polygon-fill: @color1950;
  }
  [CONSTRUCTI < 1925]{
    polygon-fill: @color1925;
  }
  [CONSTRUCTI < 1900]{
    polygon-fill: @color1900;
  }
}

#trips {
  [zoom > 14]{
  	marker-width:3;
  	marker-fill:@tripsColor;
  	marker-line-width:0;
  	marker-allow-overlap:true;
  	marker-ignore-placement:true;
  }
  [zoom > 15]{
  	marker-width:5;
  	marker-fill:@tripsColor;
  	marker-line-width:0;
  	marker-allow-overlap:true;
  	marker-ignore-placement:true;
  }
  [zoom > 16]{
  	marker-width:7;
  	marker-fill:@tripsColor;
  	marker-line-width:0;
  	marker-allow-overlap:true;
  	marker-ignore-placement:true;
  }
}
```
* [OpenStreetsMap](https://www.openstreetmap.org)
* [Mapzen](https://mapzen.com/)
* **Oct. 5: Scripting in ArcGIS assignment due**
* **Oct. 6: Final project proposal due**

### Week 6: Spatial analysis and statistics
Spatial autocorrelation, spatial sampling, point pattern analysis and quadrat analysis
* *McGrew, J. Chapman and Monroe, Charles B. An Introduction to Statistical Problem Solving in Geography, Chapter 12*
* *Mitchell, Andy, The ESRI Guide to GIS Analysis, Volume 2: Spatial Measurements & Statistics, Testing Statistical Significance and Chapter 3*
* Downloading data from 311 (NYC Open data)
* **Oct. 12: Webmapping assignment due**
* Assignment for this week [here](https://github.com/juanfrans-courses/advanced-gis/blob/master/Fall_2015/05_Spatial_Statistics_Assignment_01.md).

### Week 7: Spatial analysis and statistics and midterm presentations
Clusters, quadrats, hot-spots and cold-spots
* *Mitchell, Andy, The ESRI Guide to GIS Analysis, Volume 2: Spatial Measurements & Statistics, Chapter 3, 4 and 5*
* *Ogneva-Himmelberger, Yelena and Cooperman, Brian, Spatio-temporal Analysis of Noise Pollution near Boston Logan Airport: Who Carries the Cost?, Urban Studies 2010 47: 169.*
* **Oct. 19: Spatial statistics assignment 1 due**
* **Oct. 22: Midterm presentations**:
 * 600 Ware Lounge (7pm - 9pm)
* Answers to the Spatial Statistics assignments can be found [here](https://github.com/juanfrans-courses/advanced-gis/blob/master/Fall_2015/A_0506_Answers_Spatial_Statistics.md).

### Week 8: Remote sensing
Applications, history, process, key concepts and limitations
* *Campbell, James B. Introduction to Remote Sensing, Chapter 1*
* *Miller, Roberta Balstad and Small, Christopher, Cities from space: potential applications of remote sensing in urban environmental research and policy, Environmental Science & Policy 6 (2003)*
* *Ryznar, Rhonda M. and Wagner, Thomas W. Using Remotely Sensed Imagery to Detect Urban Change, Viewing Detroit from Space, Journal of the American Planning Association, Summer 2001*
* *Sawaya, Kali E., Olmanson, Leif G., Heinert, Nathan J., Brezonik, Patrick L. and Bauer, Marvin E. Extending satellite remote sensing to local scales: land and water resource monitoring using high-resolution imagery, Remote Sensing and Environment 88 (2003)*
* *Small, Christopher, High spatial resolution spectral mixture analysis of urban reflectance, Remote Sensing of Environment 88 (2003)*
* *Trauth, Kathleen M. A role for remote sensing information in storm water planning and management, Clean Tech Environ Policy 6 (2004)*
* *Yang, X and Lo, C. P. Using a time series of satellite imagery to detect land use and land cover changes in the Atlanta, Georgia Metropolitan area, International Journal of Remote Sensing 9 (2002)*
* **Oct. 26: Spatial statistics assignment 2 due**
* **Oct. 29th: Presentation by Madeeha Merchant on the Aleppo Project**
* Wed. Oct. 28th: Lecture by Phil Martin - Data Scientist at NYC City Council Policy and Innovation Division.
* **Downloading Landsat images and creating composite rasters:**
 * Go to [Earth Explorer](http://earthexplorer.usgs.gov/) and login. If you don't have an account, create one.
 * Enter the place you wish to search for. You can also enter a date to filter your results. Alternatively, you can also create a polygon, although this is not 100% necessary.
 * Click `Show` and select the right option, and zoom in to verify the location.
 * Click on `Data Sets`.
 * Expand `Landsat Archive` and check `L8 OLI/TIRS` and `L7 ETM+ SLC-off (2003-present)`.
 * Click `Results`.
 * Explore the different viewing options: `Footprint`, `Browse Overlay`, etc.
 * Scroll through the results and select the desired image. Click `Download Options`.
 * Download the Level 1 GeoTIFF Data Product (the largest one).
 * Once you've downloaded the file, unzip it and you can proceed to add the necessary bands to ArcMap.
 * To create the composite rasters (combination of different bands) in ArcMap use the `Composite Bands` tool.
 * Try to add the different bands in the right order. The first band you add will be the red, then green and then blue. However, if you don't do it in order, you can always change the symbology in the properties panel.
 * Some common composite images are:
    * True color composite: Bands 4, 3, 2
    * False color composite, SPOT (good for vegetation): Bands 5, 4, 3
    * False color composite (wet earth vs. dry earth): Bands 7, 5, 1
    * False color composite (vegetation, urban areas, etc): Bands 10, 7, 3
    * False color composite (urban): Bands 7, 5, 2
    * False color composite (NDVI): Bands 5, 4
* Links:
 * [Earth Explorer](http://earthexplorer.usgs.gov/)
 * [Landsat Live Mapbox](https://www.mapbox.com/blog/landsat-live-live/)
 * [Landsat on AWS](https://aws.amazon.com/blogs/aws/start-using-landsat-on-aws/)
 * [Development Seed Libra](https://libra.developmentseed.org/)
 * [Landsat-Util](https://developmentseed.org/projects/landsat-util/) - command-line Landsat tools
 * [Tutorial](https://www.mapbox.com/tilemill/docs/guides/landsat-8-imagery/) for processing Landsat imagery on Tilemill
 * Mapbox [tutorial](https://www.mapbox.com/guides/processing-satellite-imagery/) for working with satellite imagery
 * Mapbox Landsat metadata [viewer](https://www.mapbox.com/blog/satellite-metadata/)
 * Mapbox Landsat bands [explanation](https://www.mapbox.com/blog/putting-landsat-8-bands-to-work/)
* **Assignment:**
  * Download one or multiple Landsat images and using some of the techniques we saw in class and in the tutorial, perform some kind of analysis on the image(s). Basically, you need to come up with your own question and try to answer it through Landsat images and feature analyst tools.
  * The deliverable is one PDF board (uploaded to Courseworks) in which you show your images, your process and your results. Please quantify your results and describe your process.

### Week 9: Surface modeling
Sampling, interpolation and prediction
* *Bolstad, Paul. GIS Fundamentals: A First Text on Geographic Information Systems, chapter 11 and 12*
* *DeMers, Michael N. Fundaamentals of Geographic Information Systems, chapter 10*
* *Marshall, Julian D., Brauer, Michael and Frank, Lawrence D. Healthy Neighborhoods: Walkability and Air Pollution, Environmental Health Perspectives 117 (2009)*
* **Nov. 2: Remote sensing assignment due**
* Surface modeling assignment located [here](https://github.com/juanfrans-courses/advanced-gis/blob/master/Fall_2015/08_Surface_Modeling_Assingment.md)

### Week 10: Network analysis and transportation planning
Introduction, network characteristics and history of transportation planning
* *Mitchell, Andy. The ESRI Guide to GIS Analysis, Volume 1: Geographic Patterns & Relationships, Measuring distance or cost over a network*
* *Nyerges, Timothy L. GIS in Urban-Regional Transportation Planning (chapter 7), in The Geography of Urban Transportation, Edited by Hanson, Susan and Giuliano, Genevieve*
* *Oliver, Lisa N. Schuurman, Nadine and Hall, Alexander W. Comparing circular and network buffers to examine the influence of land use on walking for leisure and errands, Internation Journal of Health Geographics (2007)*
* *Schuurman, Nadine, Fiedler, Robert S., Grzybowski, Stefan CW. and Grund, Darrin. Defining rational hospital catchments for non-urban areas based on travel-time, International Journal of Health Geographics (2006)*
* *Sultana, Selima and Weber, Joe. Journey-to-Work Patterns in the Age of Sprawl: Evidence from Two Midsize Southern Metropolitan Areas, The Professional Geographer (2007)*
* **Nov. 9: Surface modeling assignment due**
* Mon. Nov. 9th: Lecture by Lela Prashad, Chief Data Scientist and Co-Founder at NiJeL (also works on GeoDa and does remote sensing at ASU).

### Week 11: Model builder
Decision support systems, levels of measurement, rules for spatial data combination, ranking strategies, the build-out model and artificial intelligence models
* *Allen, Jeffery and Lu, Kang. Modeling and Prediction of Future Urban Growth in the Charleston Region of South Carolina: a GIS-based Integrated Approach, Conservation Ecology 8(2)*
* *Town of Amherst, Massachusetts, Build-Out Analysis and Future Growth Study (2002)*
* *Carr, Margaret H. Smart Land-Use Analysis: The LUCIS Model, chapters 4 - 12*
* *Johnson, Michael P. Spatial decision support for assisted housing mobility counseling, Decision Support Systems 41 (2005)*
* *Longley, Paul A., Goodchild, Michael F., Maguire, David J. and Rhind, David W. Geographic Information Systems and Science, chapter 16*
* **Nov. 16: Network analysis assignment due**

### Week 12: Spatial relationships and regressions
* **Nov. 23: Model builder assignment due**

### Week 13: Work in class
Final project development

### Week 14: Final presentations and final papers due
* **Dec. 8: Final presentations - Part 1 (Fayerweather 200 South)**
  * Zhiguan & Shasha
  * Alexandra, Valentina & Kaylee
  * Chris & Abdulla
  * Jeongwon, Seung Whang & Jack
* **Dec. 10: Final presentations - Part 2 (Avery 114)**
  * Emily, Sinae & Mark
  * Jialei & Yuhan
  * Logan & Tim
* **Dec. 10: Final papers due**

## References
### Books
* Data Flow: Visualizing Information in Graphic Design
* Data Flow 2: Visualizing Information in Graphic Design
* Meirelles, Isabel, Design for Information
* Yau, Nathan, Data Points
* Bertin, Jaques, Semiology of Graphics
* Elke, Beyer, Atlas of Shrinking Cities
* Tufte, Edward, The Visual Display of Quantitative Information
* Tufte, Edward, Envisioning Information
* Tactical Technology Creative, Visualizing Information for Advocacy
* Orff, Kate, Petrochemical America
* Dodge, Martin, Kitchin, Rob and Perkins, Chris, [The Map Reader](http://onlinelibrary.wiley.com/book/10.1002/9780470979587)

### Blogs & Websites
* [Visualizing Data](http://www.visualisingdata.com/)
* [Flowingdata](http://flowingdata.com)
* [Periscopic](http://periscopic.com)
* [Visualizing.org](http://visualizing.org)
* [Accurat](http://accurat.it)
* [Moritz Stefaner](http://truth-and-beauty.net/)
* [Nocholas Felton](http://feltron.com)
* [Infosthetics](http://infosthetics.com)
* [Visualcomplexity](http://visualcomplexity.com)
* [The Economist - Graphic Detail](http://www.economist.com/blogs/graphicdetail)
* [New York Times - The Upshot](http://www.nytimes.com/upshot/)
* [Visualoop](http://visualoop.com/)
* [FiveThirtyEight](https://fivethirtyeight.com/datalab/our-33-weirdest-charts-from-2014/)
* [Huffington Post](http://www.huffingtonpost.com/2014/12/22/huffpost-infographics-201_n_6351828.html)
* [LA Times](http://graphics.latimes.com/2014-in-graphics/)
* [Wall Street Journal](http://graphics.wsj.com/wsj-interactives-2014/)
* [Washington Post](https://www.washingtonpost.com/graphics/national/2014-in-graphics/)
* [Quartz](http://qz.com/318339/all-of-the-charts-we-made-in-2014/)
* [New York Times - Interactive Storytelling](http://www.nytimes.com/interactive/2014/12/29/us/year-in-interactive-storytelling.html?_r=0#data-visualization)
* [Fathom](http://fathom.info/)
* [Data Canvas](http://map.datacanvas.org/#)
* [Waze Global Driver Satisfaction Index](http://blog.waze.com/2015/09/global-driver-satisfaction-index.html)
