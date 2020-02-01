# HiToStrava
## Introduction
HiToStrava converts health activities registered in the Huawei Health app into a file format that can be directly
imported in Strava.

The activities are converted to Garmin TCX XML files which can be directly imported in Strava. 

## How it works
All users can use conversion from a JSON file using the --json option. For users with rooted phones, legacy file and tar
options are still available.

You can get the required JSON file as follows.

Activities can be mass converted using the data from the JSON file with the motion path detail data available in the 
Privacy Data zip file that you can request in the Huawei Health app.

Use the new --json command line option and
specify your extracted "motion path detail data.json" file.
To be able to request your data in the Huawei Health app, a prerequisite is to have an enabled Huawei account in the app.

To request your data:
- Tap the 'Me' button in the lower right-hand corner, then tap on your account name on top of 
the screen.
- Tap on 'Privacy Center' (one but last option just above 'Settings'). 
- Tap 'Request Your Data' You can now request ALL your Huawei Health app data in a zip file. 

You will receive a mail with a link to download the zip file. 
- Once downloaded, open the zip file and go to the "data/Motion path detail data & description" folder.
- Extract the file "motion path detail data.json" from the zip file. Use this file in the --json command line option.  
This will generate both the original HiTrack files and the converted TCX files.

## Requirements
- [`Python 3.7.3`](https://www.python.org/downloads/) or higher.
- A Huawei account to request your health data.

## Usage
### Command Line Arguments Overview
> usage: HiToStrava.py [-h] [-f FILE]
>                     [-s {Walk,Run,Cycle,Swim_Pool,Swim_Open_Water}] [-j JSON]
>                     [-t TAR] [--from_date FROM_DATE]
>                     [--pool_length POOL_LENGTH] [--output_dir OUTPUT_DIR]
>                     [--output_file_prefix OUTPUT_FILE_PREFIX]
>                     [--validate_xml] [--log_level {INFO,DEBUG}]
>
>optional arguments:
>>  -h, --help            show this help message and exit

>>  --log_level {INFO,DEBUG}
>>                        Set the logging level.

>FILE options:
>>  -f FILE, --file FILE  The filename of a single HiTrack file to convert.
>
>> -s {Walk,Run,Cycle,Swim_Pool,Swim_Open_Water}, --sport {Walk,Run,Cycle,Swim_Pool,Swim_Open_Water}
>>                        Force sport for the conversion. Sport will be auto-
>>                        detected when this option is not used.

>JSON options:
>>  -j JSON, --json JSON  The filename of a Huawei Cloud JSON file containing
>>                        the motion path detail data.

>>  --json_export         Exports a file with the JSON data of each single
>>                        activity that is converted from the JSON file in the
>>                        --json argument. The file will be exported to the
>>                        directory in the --output_dir argument with a .json
>>                        file extension. The exported file can be reused in the
>>                        --json argument to e.g. run the conversion again for
>>                        the JSON activity or for debugging purposes.

>TAR options:
>>  -t TAR, --tar TAR     The filename of an (unencrypted) tarball with HiTrack
>>                        files to convert.

>DATE options:
>>  --from_date FROM_DATE
>>                        Applicable to --json and --tar options only. Only
>>                        convert HiTrack information from the JSON file or from
>>                        HiTrack files in the tarball if the activity started
>>                        on FROM_DATE or later. Format YYYY-MM-DD

>SWIM options:
>>  --pool_length POOL_LENGTH
>>                        The pool length in meters to use for swimming
>>                        activities. If the option is not set, the estimated
>>                        pool length derived from the available speed data in
>>                        the HiTrack file will be used. Note that the available
>>                        speed data has a minimum resolution of 1 dm/s.

>OUTPUT options:
>>  --output_dir OUTPUT_DIR
>>                        The path to the directory to store the output files.
>>                        The default directory is ./output.

>>  --output_file_prefix OUTPUT_FILE_PREFIX
>>                        Adds the strftime representation of this argument as a
>>                        prefix to the generated TCX XML file(s). E.g. use
>>                        %Y-%m-%d- to add human readable year-month-day
>>                        information in the name of the generated TCX file.

>>  --validate_xml        Validate generated TCX XML file(s). NOTE: requires
>>                        xmlschema library and an internet connection to
>>                        retrieve the TCX XSD.
                        
### Usage Examples
#### JSON file conversion example
Use the command below to convert all activities available in the motion path JSON file from the requested Huawei 
Privacy data that were started on October, 3rd, 2019 or later. Source HiTrack files and converted TCX files will be 
generated in folder ./my_output_dir/json 
>python HiToStrava --json "motion path detail data.json" --from_date 2019-10-03 --output_dir my_output_dir/json

#### Single file conversion examples
The example below converts extracted file HiTrack_12345678901212345678912 to HiTrack_12345678901212345678912.tcx in 
the ./output directory

>python HiToStrava --file HiTrack_12345678901212345678912

The next example converts extracted file HiTrack_12345678901212345678912 to HiTrack_12345678901212345678912.tcx in 
the ./my_output_dir directory. The program logging level is set to display debug messages. The converted file is 
validated against the TCX XSD schema (requires installed xmlschema library and an internet connection). 

>python HiToStrava --file HiTrack_12345678901212345678912 --output_dir my_output_dir --validate_xml --log_level DEBUG

The following example converts an extracted file HiTrack_12345678901212345678912 to HiTrack_12345678901212345678912.tcx 
in the ./output directory and forces the sport to walking. 

>python HiToStrava --file HiTrack_12345678901212345678912 --sport Walk

The next example converts an indoor swimming activity in an extracted file HiTrack_12345678901212345678912 to 
HiTrack_12345678901212345678912.tcx. The length of the pool in meters is specified to have a more accurate swimming data
calculation.  

>python HiToStrava --file HiTrack_12345678901212345678912 --pool_length 25

#### Direct tar file conversion examples
The first example extracts and converts any HiTrack file found in tar file com.huawei.health.tar into the ./output 
directory. The output directory will contain both the extracted HiTrack file and the converted TCX XML file. 

>python HiToStrava --tar com.huawei.health.tar

In the example below, only activities in the com.huawei.health.tar tarball that were started on August 20th, 2019 or 
later will be extracted and converted to the ./output directory.

>python HiToStrava --tar com.huawei.health.tar --from_date 20190820
 
## Release Notes
### Version 3.1 Build 2002.0101
#### New features and changes
- JSON conversion: new command line argument --json_export that exports a file with the JSON data of each single
  activity that is converted from the JSON file in the --json argument. The file will be exported to the directory in 
  the --output_dir argument with a .json file extension. The exported file can be reused in the --json argument to e.g. 
  run the conversion again for the JSON activity or for debugging purposes.

#### Solved issues
- Corrected a potential bug for activities with an unknown sport type.


### Version 3.0 Build 2001.2301
#### Solved issues
- INFO logging level now works again.

### Version 3.0 Build 2001.2001
#### New features and changes
- JSON conversion: Use calories burned information from Huawei Health data.

### Version 3.0 Build 2001.1301
#### New features and changes
- Display help message when no arguments are passed.

### Version 3.0 Build 1912.1902
#### New features and changes
- Refactored logging to use a program specific logger instead of the root logger for more reliable logging.
- Reformatted some logging messages to have clearer logging of dates / times.

### Version 3.0 Build 1912.1901
#### New features and changes
- JSON conversion now uses the following detailed information available in the JSON data:
    - Sport types for walking and cycling activities are automatically set (instead of being detected based on the HiTrack
    data).
    - All date and time information in the converted TCX files includes the time zone information of the
    local time when the activity took place.

#### Solved issues
- Conversion failed if HiTrack data started with a GPS loss / pause record.

#### Known Limitations
- We (currently) don't have the JSON sport type values for activity types other than walking or cycling. 

### Version 3.0 Build 1912.0301
#### New features and changes
- Generated TCX files now contain the Strava custom sport type when XML validation is NOT being used.
  Strava recognizes the correct sport type on import without the need to manually adjust it after import.
  
  NOTE: when using the --validate_xml option, TCX XML compliant sport types will be used as in previous versions.

#### Solved issues
- Script terminated with error when using --tar and --json options without --from_date option.
- Conversion failed with a division by zero error when there is only 1 valid step frequency record. 
- When there are multiple activities on the same date, only the first activity would be converted. 
    
### Version 3.0 Build 1910.0301
#### New features and changes
- It is now possible to (mass) convert activity data from the JSON file with the motion path detail data available in
  the Privacy Data zip file that you can request in the Huawei Health app. Use the new --json command line option and
  specify your extracted "motion path detail data.json" file.
  To be able to request your data in the Huawei Health app, a prerequisite is to have an enabled Huawei account in the app.
  To request your data, tap the "Me" button in the lower right-hand corner, then tap on your account name on top of 
  the screen. Next, tap on 'Privacy Center' (one but last option just above 'Settings'). You can now request ALL your
  Huawei Health app data in a zip file by tapping 'Request Your Data'. You will receive a mail with a link to download
  the zip file. Once downloaded, open the zip file and go to the "data/Motion path detail data & description" folder.
  Extract the file "motion path detail data.json" from the zip file. Use this file in the --json command line option.  
  This will generate both the original HiTrack files and the converted TCX files.

#### Known Limitations
- The JSON file from the Huawei Privacy export contains other interesting data and information which is currently not
  used (yet). 

### Version 2.3 Build 1909.2401
#### Solved issues
- Program would error out when trying to convert without the --validate_xml option when the xmlschema library wasn't 
  installed.

#### Known Limitations
- Since the latest version of Huawei Backup (10.0.0.360_OVE) and more importantly Huawei Health (10.0.0.533), the
  program is ( / might be) defunct in its current state. Last version of Huawei Health app seems to disallow
  backups through its properties. Huawei Backup release notes state that encryption of the backup is required.

### Version 2.3 Build 1909.1501
#### New features and changes
- Added **--output_file_prefix** command line argument option. You can now add the strftime representation of this argument 
   as a prefix to the generated TCX XML file(s). E.g. use %Y-%m-%d- to add human readable year-month-day information in 
   the name of the generated TCX file.
- Reworked auto-detection of activity types. This was needed for the detection and distinction between pool swimming 
   and open water swimming. Added internal auto-distinction between walking and running based on
   research in https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5435734. For validation purposes, both types will still be
   converted as 'Run' in the TCX file (for now) to comply with the Garmin TCX XSD.
- The --sport command line argument now disables auto-detection of the type of sport of the activity and also enforces 
   the selected type of sport for the conversion.
- The values of --sport command line argument have been changed. 'Swim' has been replaced by 'Swim_Pool' or 
   'Swim_Open_water'. Please adapt your script(s) or remove the command line argument and try the (improved) auto-detection.   
- Alpha support for open water swimming activities. Tested on a single activity file only. SWOLF and speed
   data were found to be unreliable / unusable. The conversion relies on the GPS data for this type of activity. We welcome
   your feedback if you have any problems or remarks using this feature. Closes #28. 

#### Solved issues
- Bug solved in swimming lap generation that could cause each lap to be generated twice (probably since V2.0 B1908.3101).
- Bug solved that could cause the --sport command line argument to get overruled by auto-detection. Closes #30.

#### Known Limitations
- Distance calculation during long(er) pause periods can be off due to continuing speed data records
  during the pause in the HiTrack file. If you suspect a converted activity to have a wrong distance, you can 
  try to recalculate the distance from the activity details screen in Strava for now.

### Version 2.2 Build 1909.0801
#### New features and changes
- Conversion of Walking, Running ad Cycling activities without any GPS data is supported. You can now convert 
  activities that were recorded using a fitness band only (that has no GPS) without using your phone during the 
  activity. Distance calculation is an estimated distance and may differ from the real distance because calculation is
  based on (average) speed data during a period of seconds (typically 5 seconds) with a resolution of 1 dm/s.

#### Solved issues
- Solved conversion error due to improper segment handling in some cases of GPS loss and/or pause.
- Solved potential conversion error due to improper distance calculation in case the HiTrack file would not contain 
  an explicit stop record.

#### Known Limitations
- Open water swimming activities were not and are not supported (yet).

### Version 2.1 Build 1909.0701
#### New features and changes
- More accurate distance calculation in case of GPS loss during an activity is now supported for walking, running and 
  cycling activities. The real-time speed data in the HiTrack file is used to determine the distance during GPS loss.

#### Known Limitations
- Walking, Running and Cycling activities without any GPS data at all can't be converted (yet) and the conversion will
  fail with an error. This might be related to issue #18 (0 m distance) reported in pre-version 2. Possible use-case: 
  You use your fitness band (that has no GPS) without your phone during an activity. To be solved in a future update.

### Version 2.0 Build 1908.3101
#### Solved issues
- Solved issue causing only a part of the activity to be converted. It was detected in a case where the
  activity track contained multiple loops over the same track (and/or on some devices, the records in the HiTrack
  file are not in chronological order).

### Version 2.0 Build 1908.2901
#### New features and changes
- Changed the auto-detected activity type from 'Walk' to 'Run' for walking or running activities. Please note that the
  known limitation from the previous version still exists: no auto-distinction between walking and running activities,
  they both are detected as running activities.  

#### Solved issues
- Error parsing altitude data from tp=alti records in HiTrack file

### Version 2.0 Build 1908.2201
#### New features and changes
- Support for swimming activities

  In this version duration and distances are supported. SWOLF data is available 
  from the HiTrack files but is not exported (yet) since we don't have the information how to pass it in the TCX 
  format or if Strava supports it natively.
- Direct conversion from tarball

  It is now possible to convert activities directly from the tarball without the 
  need to extract them.
- Auto activity type detection

  Auto detection of running, cycling or swimming activities. There is no auto 
  distinction (yet) between walking and running activities. For now, walking activities will be detected as running. The 
  activity type can be changed in Strava directly after importing the file. 
- Extended program logging

  Ability to log only information messages or to have a more extensive debug logging.
- Restructured and new command line options
  
  This includes new options for direct tarball processing (--tar and 
    --from_date), file processing (--file), forcing the sport type (--sport), setting the pool length for swim
    activities (--pool_length) and general options to set the directory for extracted and converted files 
    (--output_dir), the logging detail (--log_level) and the optional validation of converted TCX XML files 
    (--validate_xml)   
- The source code underwent a restructuring to facilitate the new features. 

#### Solved issues
- Step frequency corrected. Strava displays steps/minute in app but reads the value in the imported file as 
strides/minute. As a consequence, imported step frequency information from previous versions was 2 times the real value.

#### Known Limitations
- Auto distinction between walking and running activities is not included in this version.
- For walking and swimming activities, in this version the correct sport type must be manually changed in Strava directly 
  after importing the TCX file.
- Distance calculation may be incorrect in this version when GPS signal is lost during a longer period or there is no
GPS signal at all.
- Due to the very nature of the available speed data (minimum resolution is 1 dm/s) for swimming activities, swimming
  distances are an estimation (unless the --pool_length option is used) and there might be a second difference in the
  actual lap times versus the ones shown in the Huawei Health app.
- There is no direct upload functionality to upload the converted activities to Strava in this version.
- This program is dependent on the availability of the temporary/intermediate 'HiTrack' files in the Huawei Health app.
- This program is dependent on the Huawei Backup to take an unencrypted backup of the Huawei Health app data.
- This program was not tested on Python versions prior to version 3.7.3

## Copyright and License
Copyright (c) 2019 Ari Cooper Davis, Christoph Vanthuyne
