# DOCUMENTATION - MISSING DATA TOOL:

## CLASS VARIABLES: 
* `feed_dic`  : A dictionary to hold feed objects to download data to fill missing data gaps
* `trans_dic` : A dictionary to hold transmuter objects to generate data from raw storage to fill missing data gaps
* `bucket`    : The bucket for s3 invenia-tabular-data table to look for missing data
* `conn`      : connection created with s3

## FUNCTIONS DESCRIPTION:

`setup():`
 * Creates a connection to s3
 * Gets the bucket in which to look for missing datas
 * Initializes feed and transmuter objects
 
`create_connection():`
* Creates a connection to s3 if no connection exists
* If a connection already exist then just returns the connection
  
`extract_filename(date_range, file_obj, file_names):`
 * Given a date range, extracts files names from the file object verifying the file lies withing the date range
 * Appends the file names to the list of file names
 
`find_hourly_missing_data(self, path, duration=None, interval=None, date_range=None):`
* Given a path and date range, finds out missing data hours from files within the date range
* Creates a connection to s3
* Gets bucket contents from the given path
* Extracts file names from the objects in the buckets
* Reads each file row by row from the bucket
* Creates a list unix datetime stamps of hours for each file and checks if all the hours from the list of dates exist in the files.
* If a datetime stamp exists, that datetime stamp is removed from the list of dates, at the end remaining with the list of datetimestamps with only those datetimestamps for which no data was found.
* Also for each file for all the missing hours, a corresponding list of date range is created where the daterange covers for the whole duration of the file.
* This list of date range is passed to anothe function along with the list of missing datetimestamps to fill the data gaps
* The funciton returns the list of missing datetimestamps along with the list of ranges of missing dates

`create_transmuter(self, market):`
* Creates all transmuter objects which are later on used to generate missing data from the raw storage

`create_feed(self, tag)`
* Creates feeds object to download the missing data from internet

`get_feed(self,tag):`
* Returns a feed object from the feed dictionary using the tag

`get_trans(self,tag):`
* Returns a transmuter object from the transmuter dictionary using the tag

`unix_to_human_time(self,unix_timestamp):`
* Converts a unix timestamp to human readable time format

`daterange_generator(self,start,end,delta):`
* Given a start and end date, will generate a list of dates inclusive of the start and end date

`filter_hourly_data(self, row_elements, date_list):`
* Given a row from the file and a list of dates
* Verifies whether the validity start date of that row is in the list of dates or not
* If the validity start exist in the list of dates, then removes that date from the list which indicates that the data for that hour exist
* When the function returns, the list of dates will only contain the dates for which no data exists, meaning a list of missing dates

`fill_hourly_data_gaps(self,market, market_type, date_list, date_range):`
* Given a list of missing datetimestamps for missing hours and the date range for that whole date
* Runs the `feed.fetch()` to see if it can download the missing data from the internet
* If it can download it then runs `feed.update()` to store the downloaded data in the raw storage
* After that runs the `transmuter.fetch()` to fetch the data from the raw storage.
* If there exist missing data in the raw storage then runs the `trans.update()` to update the data from the raw storage to the corresponding s3 bucket in aws.

`fill_missing_file_gaps(self, path, date_range):`
* Given the date range for the missing file will try to generate the missing file data using the corresponding transmuter and upload it to S3

`find_missing_files(self,path, date_range):`
* Given a date range and a path to s3 bucket, will convert the date range to utc times to prevent DST issues
* Then will generate a list of dates from the utc date range
* Then from the given path, will cross referrence each file unix timestamp with the list of dates to verify if all the path contains all the files in the date range.
* If the uinix time stamp of a file mathces the date from the list of dates, then that date is removed from the list of dates
* So after traversing all the files, the list of dates will only contain the dates for which no files were found and will return the list of missing dates

`split_path(self, path):`
* Given a path to s3 bucket, splits the path and returns the market name and market type

## USAGE:

Open a python interpretter.
Then run the following commands from the datafeeds environment (To activate the environemnt run `workon datafeeds` from the root directory of the datafeeds project):

```
import datetime
import missing_data_tool

from datafeeds.utils.datetime_range import DatetimeRange
from datafeeds.utils.dates import localize
from dateutil.tz import tzoffset
from missing_data_tool import MissingDt

est = tzoffset('EST', -18000)

msd = MissingDt()
path = '2017-05-02_001/collection=miso/data_type=realtime_lmp/'
msd.setup()
date_range = DatetimeRange(localize(datetime.datetime(2014,12,31),est),localize(datetime.datetime(2015,3,20),est))
msd.find_hourly_missing_data(path,date_range=date_range,duration=23,interval=1)
missing_files= msd.find_missing_files(path,date_range)
```

## Testing the Tool:

### Running Individual Modules and Tests

If you want to run a new or existing unit test suite, you must do so by running the module itself (rather than a specific file or python command).
You can do so like this:

```
python -m unittest tests.missingdatatool.test_missing_data_tool
```

## Challenges and Future Improvements:
* For creating a feed object, the challenge is mapping the feed to its corresponding transmuter from the tag. As feeds of different markets have the same name, so figuring out which market the feed belongs to or which market to generate the feed for becomes a challenge. The `create_feed()` function right now pre generates only the feeds for the miso realtime market. If an easy way to map the feeds to their corresponding market from the tag is identified then functionality for pregenerating all the feed can be added here.
* All the outputs of the missing data tool are done using logger.info but print statements to print out the outputs are commented out, as sometimes logger.info output tends to be hard to read, one can comment out the print statements to see the print output in the stdout.
* Getting the data interval and duration from the metadta is incorporated in tests, but haven't been actually tested with actual data on S3.
* Initially the tool was for finding missing hour gaps for data in S3 but also added the option to find missing files though there are hardly a whole file missing. Also the option to fill in missing files is there. For filling missing file gaps the missing data is retrieved from the raw storage if exist, not downloaded from the internet (Something to add if needed in the future).


