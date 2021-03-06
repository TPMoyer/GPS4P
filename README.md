# GPS4P

 Processing Java framework sketch recording data from Adafruit UltimateGPS  

App to log the several data available from the Adafruit Ultimate GPS.  The goal is to collect data for analysis of how to get the best resolution position (latituce, longitude and altitude) obtainable from this relatively inexpensive unit.

App to log the several data available from the Adafruit Ultimate GPS. The goal is to collect data for analysis of how to get the best resolution position (latituce, longitude and altitude) obtainable from this inexpensive unit.

Initial analysis of 24 hour samples suggest that the module reports precise co-ordinates at the 1/100 arc second level (0.2 to 0.3 meter) from Satellite data which combine to give fixes which are inaccurate to the level published for GPS.   That accuracy level is described as 95% of fixes closer than 10 meters from true position.   The precision and accuracy of the recorded fixes seem not strongly different when fixes are obtained at 0.1 Hz or 10 Hz.  

### GOAL

The goal of the application suite is to produce a 3D representation of a vacant land parcel to enable building dimensions, locations, and sightlines to be determined prior to submission of a building permit.  In the initial application, no features of interest are more than 250 meters appart.  The tactics being undertaken include obtaining fixes over extended durations.  Analytic techniques will exclude some fixes, and aggregate best estimates of true positions will be asserted. Initial analysis shows that fix accuracy is strongly dependent on the number of satellites which are actively contributing to the position fixes.  When there are 11 satellites active, the position variance is substantially less than when only 3 or 4 satellites are active.

A second GPS unit is being obtained to investigate differential fixes.  The geometry of intersecting non-infinitly-thin spheres originating from rapidly moving, quantifiably specified locations implies that the fix variance over small distances (like 250 meters for instance) will be substantially smaller than the 10 meter published metric.  The variance from atmospheric signal propigation retardation would also be expected to be small.  Commercial differential survey GPS units are said to be capable of centimeter scale accuracy after fix collection for a few minutes.  These commercial units are described as using data broadcast from GPS units at known fixed locations such as USGS survey benchmarks.  The present endevor will not engage in any data broadcast, but instead will log data (the GPS4P app) and post-process (with a not-yet-written app).

### My Setup

Hardware:

1. Adafruit Ultimate GPS USB Board with firmware query response TK705,AXN_2.31_3339_13101700,5632,PA6H,1.0*6B
2. active 28 dB GPS antenna
3. headless Raspberry Pi Zero W
4. USB battery pack
5. monitor, keyboard, and mouse for initial PI setup. 

Core Software:

1. Processing framework: 
   1. RPi download at https://pi.processing.org/download/ 
   2. G4P (Gui for Processing) library
      3.log4j 
2. GPS4P (Global Position System For Processing) sketch

Ecosystem Software

1. VNC is used to obtain a remote GUI for the headless pi.  
2. MobaXTerm on my windows development box as an SSH file GUI and terminal.  One can drag-drop data to-and-from the mobaXTerm left file-display-panel into-from the windows file explorer app.  This capability is used to great advantage by big data developers who are often called upon to work on remote and/or virtual machines.

### ACCURACY

What size (in meters) are the steps output from the MTK3339?   Elevation is output in meters with one position past the decimal point, so 0.1 meter precision.  For Latitude and Longitude a two step conversion is necessary to obtain meters.   
    First step in lat lon conversion to meters: decrypting the output format. Output for both latitude and longitude with digits representing degrees, minutes and seconds are encoded into a single floating point number.   I did not initially recognize that this encoding was present and came up with positions which were 30 miles off from my true lat,lon.   Guru's on the web had shown others the error of our ways, and how to interpret the MTK3339 lat lon format.  For questions of precision, the net is that the output format has precision of 1/100 of an arc second.
    Second step in translating 1/100 arc second to distance can be performed by converting the latitude, longitude, altitude triplets to XYZ cartesian coordinates, using the formula provided at https://en.wikipedia.org/wiki/Reference_ellipsoid#:~:text=The%20ellipsoid%20WGS%2D84%2C%20widely,more%20precisely%2C%2021.3846858
    The XYZ of the GPS reciever location is calculated, and recalculated with 1/100 arc second added to the latitude.  The radial difference from the reciever location XYZ gives the meters corresponding to the incremental reporting steps in latitude.   A similar calculation is performed to give the size of the incremental reporting steps in longitude.  Note that because latitude lines are equally spaced from the equator to the pole, the 1/100 arc second reporting
    precision is very close to 0.309 meters everywhere near the earths surface.   Latitude lines are maximaly distant at the equator and converge at the poles.   The 1/100 arc second of latitude thus shrinks as one goes further from the equator.

The tactic I hope to deploy is to have one GPS unit log fixes at an unmoving location for a substantial period of time.   Hopefully a goodly portion of a day will be enough, or until the USB battery pack runs out of charge.   Best location would be repeatable, and with good visibility to the sky (few trees, no buildings or mountains below 15 degrees to the horizon).   The second unit will be carried to a series of locations, and will sit at each location for a period of time long enough to get good readings.  Again, Hopefully that time period at each location will not need to be longer than a few minutes.  Probably will want to develope a "goodness" metric to allow shorter times being needed when more satellites are actively paticipating in the GPS fix.
   The current firmware version of the MTK3339 unit gives Latitude,Longitude formatted to have only enough digits to show 1/100 of an arc second.  I intend to ask the chipset makers for a firmware version which would ouput two more decimal places on latitude, longitude, and altitude.   This is expected to be into the noise, but is preferred to the present situation of having best possible precision hidden by an output format.
