# Instructions/information for station data project

## Introduction

The purpose of this project is to examine changes in drift potential over the last several decades along the Lake Michigan shoreline.

I chose various airport weather stations near the shoreline. My goal was to find stations that represented different parts of the coast and had long data records (70+ years). I also chose some stations with shorter records.

## Data

I used the "cli-Mate" web application from the Midwestern Regional Climate Center (MRCC) to download the data. The app can be accessed here: [https://mrcc.illinois.edu/CLIMATE/](https://mrcc.illinois.edu/CLIMATE/). To use the app, you need to create a free account.

Within the app, under 'Current Hourly Station Information' (at the top), click 'Select Hourly Station'. Search for the station. For example, to find Tulip City Airport, type 'BIV'. Select a date range. I found that the server would have trouble if I made the date range too large. It had no problem handling 30 years at a time. Under 'Raw Data', 'Select All' to get all of the meteorological variables available. Click 'Get tabular data'. Then in the left pane there is a link to 'Data CSV Version'. Click that to download a CSV version. 

I used a consistent naming format for the files. For example 'BEH_19730101_20201207.csv' is the file for the BEH station data from January 1, 1973 through December 7, 2020. Because the data files are somewhat large, I did not want to synch them with my gitHub project, but I wanted to be able to share them easily, so I saved them in Projects/station_data/raw_data in my Google Drive, and created symlink in the repository project directory Projects/station_data pointing to that Google Drive folder. This acts like having these files in a 'raw_data' directory in the repository project directory. The symlink is listed in .gitignore, so the link itself is not synched. Someone cloning the repository should create their own symlink in the project directory pointing to the location of the raw data on their own local system.

## Cleaning the data and doing some preprocessing

I corrected some problems with the data, calculated some additional variables, and created a single cleaned/preprocessed data file for each station. The results were saved as '.rds' files in the Projects/station_data/clean_data folder in my Google Drive folder. I created a symlink to this folder from the project directory for the repository. The files have informative names. For example, 'BEH_19730101_20201207_dp.rds' is the file for the BEH station and has measurements and calculated variables listed between January 1, 1973 and December 7, 2020.

The 'Combine_Station.Rmd' file does the cleaning/preprocessing. The script is saved in the 'code' directory within the repository project directory.

Something I noticed with some of the stations (GRB, MKE, TVC, BEH) is that the data from the MRCC app are listed with a block of consecutive observations at the beginning, then the data jump backward in time and are listed in order for the rest of the data set. This means that the first 2400 or so observations are duplicates of observations that occur part of the way through the data. Oddly enough, the jumps tend to happen on April 8 or April 9 at each station. I thought that it might be a daylight savings problem, but it's too early for that. For these stations, if the first 2400 or so observations are skipped, the rest of the observations are in the correct order and there is no duplication, and none of the remaining data are skipped. For these stations, the problem always occurs in the late 1940s or in 1950. I dropped the repetitive starting lines from these data sets.

Cleaning steps included selecting the correct rows, labeling columns, and creating a column with measurement times (as time objects).

The only preprocessing was calculating drift potential (per time) following Fryberger and Dean.

# Analyzing and visualizing the data



