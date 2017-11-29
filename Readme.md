DOCUMENTATION - MISSING DATA TOOL:

CLASS VARIABLES: 
`feed_dic  : A dictionary to hold feed objects to download data to fill missing data gaps`
`trans_dic : A dictionary to hold transmuter objects to generate data from raw storage to fill missing data gaps`
`bucket    : The bucket for s3 invenia-tabular-data table to look for missing data`
`conn      : connection created with s3`

FUNCTIONS:

`setup():`
 *Creates a connection to s3
 *Gets the bucket to look for missing datas
 *Initializes feed and transmuter objects
 
`create_connection():`
*Creates a connection to s3 if no connection exists
*If a connection already exist then just returns the connection
  
`extract_filename(date_range, file_obj, file_names):`
 *Given a date range, extracts files names from the file object verifying the file lies withing the date range
 *Appends the file names to the list of file names
 
`find_hourly_missing_data(self, path, duration=None, interval=None, date_range=None):`

