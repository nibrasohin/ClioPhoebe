# Feed Metadata Documentation:
 The tabular metadata schema will be having the following format:
```
{
  "sources":{
      "tag" : {
        "publish_offset": value, 
        "datafeed_runtime": value, 
        "content_offset": value, 
        "time_zone": "EST", 
        "content_interval": value, 
        "publish_interval": value
      }
  },
  
  "columns":{
    "name" : ["available_date", "validity_start", "validity_end", "inclusivity", "object_id", "tag", "value"],
    "type" : [type("available_date"), type("validity_start"), type("validity_end"), type("inclusivity"), type("object_id"), type("tag"), type("value])
  },
  
  "version": "VERSION_NUMBER"
  
}
```


* `sources:` sources will contain the tag that will be the corresponding feed name. Each `tag` needs to have the following metadata associated with it:




| Name                          | Type      | Description |
|:-------------------------|:----------|:---------------|
| publish_interval     | Integer | The expected duration in seconds between each release of a file. e.g. daily |
| publish_offset         | Integer | The expected offset in seconds from the publish interval. e.g. if the file is released daily at 6 AM the offset would be 6 hours |
| content_interval    | Integer | The content interval and offset (below) are used together with a release date in order to estimate the end of the content (typically `max(target_date)`). e.g. the file released hourly has forecast that are 7-days out |
| content_offset        | Integer | See above |
| time_zone     | String   | The POSIX time zone name in which calculations should occur. Needed to correctly round to daily periods |
| datafeed_runtime | Integer | The estimated duration from when a file is released to when it is available for use by the autopredictor. Note `release_date + datafeed_runtime = available_date`. |

These fields will need to be accessible through the API for S3DB and will be updated periodically by the datafeeds.



* `column:` All CSV objects will have the extension `.csv` and all files are provided as comma-separated columns.These columns are the file contents that contain the following information:
`available_date, validity_start, validity_end, inclusivity, object_id, tag, value`

Details about the column attributes and the column format is mentioned [here](https://gitlab.invenia.ca/invenia/TabularDataSchema/edit/master/versions/2017-05-02_001.md).


* `version:` The version is the version of the schema that the metadata is using.



An example metadata file for miso realtime_lmp is shown below: 

```
{
    "sources":{
        "DARTInstantAvg": {
            "publish_offset": 60, 
            "datafeed_runtime": 0, 
            "content_offset": 53700, 
            "time_zone": "EST", 
            "content_interval": 3600, 
            "publish_interval": 3600
        }, 
        "HTTPFinal": {
            "publish_offset": 51840, 
            "datafeed_runtime": 0, 
            "content_offset": 0, 
            "time_zone": "EST", 
            "content_interval": 86400, 
            "publish_interval": 86400
        }, 
        "HTTPPrelim": {
            "publish_offset": 17280, 
            "datafeed_runtime": 0, 
            "content_offset": 0, 
            "time_zone": "EST", 
            "content_interval": 86400, 
            "publish_interval": 86400
        }, 
        "DART": {
            "publish_offset": 0, 
            "datafeed_runtime": 0, 
            "content_offset": 46800, 
            "time_zone": "EST", 
            "content_interval": 3600, 
            "publish_interval": 3600
        }

    },

    "columns":{

            "name" : ["available_date", "validity_start", "validity_end", "inclusivity", "object_id", "tag", "value"],
            "type" : ["timestamp", "timestamp", "timestamp", "int", "str", "str", "float"]
    },

    "version": "2.0"
}
```
