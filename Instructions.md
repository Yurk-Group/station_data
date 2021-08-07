# Instructions/information for station data project

## Introduction

There are two components to this project:

1. To examine changes in drift potential over the last several decades along the Lake Michigan shoreline.

I chose various airport weather stations near the shoreline. My goal was to find stations that represented different parts of the coast and had long data records (70+ years). I also chose some stations with shorter records.

2. To examine the relationship between drift potential within 45 degrees of a coast direction (for example, west coast includes winds from the SW trhough NW) and the locations of system centers (e.g., centers of low and high pressure systems). The coast-oriented drift potentials were computed by Ed Hansen using weather stations near the shoreline, who also manually identified the locations of the system centers using weather maps. This was done for all the storms occurring in 2015 that were identified as significant potential aeolian transport events. These were events in which wind speed exceeded 6.2 m/s for at least 3 hours at several stations (among 11 stations total). Events with a wind direction shift of more than 90 degrees during the event were split into two parts. There were a total of 83 events.

## Data

1. For the airport station data, I used the "cli-Mate" web application from the Midwestern Regional Climate Center (MRCC) to download the data. The app can be accessed here: [https://mrcc.illinois.edu/CLIMATE/](https://mrcc.illinois.edu/CLIMATE/). To use the app, you need to create a free account.

Within the app, under 'Current Hourly Station Information' (at the top), click 'Select Hourly Station'. Search for the station. For example, to find Tulip City Airport, type 'BIV'. Select a date range. I found that the server would have trouble if I made the date range too large. It had no problem handling 30 years at a time. Under 'Raw Data', 'Select All' to get all of the meteorological variables available. Click 'Get tabular data'. Then in the left pane there is a link to 'Data CSV Version'. Click that to download a CSV version. 

I used a consistent naming format for the files. For example 'BEH_19730101_20201207.csv' is the file for the BEH station data from January 1, 1973 through December 7, 2020. Because the data files are somewhat large, I did not want to synch them with my gitHub project, but I wanted to be able to share them easily, so I saved them in Projects/station_data/raw_data in my Google Drive, and created symlink in the repository project directory Projects/station_data pointing to that Google Drive folder. This acts like having these files in a 'raw_data' directory in the repository project directory. The symlink is listed in .gitignore, so the link itself is not synched. Someone cloning the repository should create their own symlink in the project directory pointing to the location of the raw data on their own local system.

2. For the coastal drift potential / storm center work the 'raw data' (from my perspective) are in a table that he provided in the file "Table R-1.xlsx".

## Cleaning the data and doing some preprocessing

1. I corrected some problems with the data, calculated some additional variables, and created a single cleaned/preprocessed data file for each station. The results were saved as '.rds' files in the Projects/station_data/clean_data folder in my Google Drive folder. I created a symlink to this folder from the project directory for the repository. The files have informative names. For example, 'BEH_19730101_20201207_dp.rds' is the file for the BEH station and has measurements and calculated variables listed between January 1, 1973 and December 7, 2020.

The 'Combine_Station.Rmd' file does the cleaning/preprocessing. The script is saved in the 'code' directory within the repository project directory.

Something I noticed with some of the stations (GRB, MKE, TVC, BEH) is that the data from the MRCC app are listed with a block of consecutive observations at the beginning, then the data jump backward in time and are listed in order for the rest of the data set. This means that the first 2400 or so observations are duplicates of observations that occur part of the way through the data. Oddly enough, the jumps tend to happen on April 8 or April 9 at each station. I thought that it might be a daylight savings problem, but it's too early for that. For these stations, if the first 2400 or so observations are skipped, the rest of the observations are in the correct order and there is no duplication, and none of the remaining data are skipped. For these stations, the problem always occurs in the late 1940s or in 1950. I dropped the repetitive starting lines from these data sets.

Cleaning steps included selecting the correct rows, labeling columns, and creating a column with measurement times (as time objects).

The only preprocessing was calculating drift potential per time (Q) following Fryberger and Dean. This is called drift potential in the code.

2. There was only minimal cleaning required for the second part of the project and this is done as part of the analysis script.

## Analyzing and visualizing the data

1. I did some additional processing of the data and constructed some plots in the 'Long_term_analysis.Rmd' file. There is variation in the length of time between measurements. In some years it even appears as if measurements are not made at night at some stations. Rather than assuming that the last wind measurement preceding a long period is representative for the entire period, I decided to limit the period to just the first three hours following the last wind measurement. The remaining time period is considered lost time, during which we have no applicable wind measurements. I kept track of the amount of time that is lost each year. In yearly calculations, years with more than 175 lost hours were removed from the calculations. Drift potential is computed by multiplying drift potential per time (computed in the 'Combine_Station.Rmd' script) by the amount of time until the next measurements (or three hours if that is smaller).

For each station, drift potentials were totaled over each year (without too much lost time). Drift potentials were also totaled by direction for 16 directions (N, NNE, ENE, E, ..., NNW). These are useful for creating Fryberger drift roses. 'Coast' drift potentials were also computed for 8 directions. These represent wind directions within 45 degrees of the central 'coast' direction.

Decadal totals were also computed for each of the above mentioned directions and over all. Resultant drift potentials were also computed.

The total yearly drift potential was plotted over time for the seven stations. Decadal Fryberger diagrams were plotted for 5 stations with longer data sets. These were scaled so that the resultant drift potential vector had the same length for each station.

Linear regression analysis was performed to determine if there was a linear relationship between total drift potential and year for each of the stations.

The proportion of drift potential that is accounted for by winds corresponding to each of the 4 cardinal coast directions are plotted for each direction. These calculations were done on a decadal basis. 

2. Ed Hansen's table is analyzed in the 'two_props.Rmd' script. There are sometimes multiple stations for a single coast direction. For each coast direction-station pair events were identified as affecting the coast-station if drift potential exceeded 1 at that station from that coast direction. The 83 storms were classified as either affecting the coast-station or not, and the proportion of events with low pressure centers in each of 8 directions was compared between the two groups using a Fisher exact test for each direction. This was also done for high pressure centers. The code that is included here allows this analysis to be done for a single coast-station pair at a time. Ed Hansen ran this for each coast-station pair to produce figures in Excel.

