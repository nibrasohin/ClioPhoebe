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
  
  "version": "2.0"
  
}
```

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
