## Transcom Event Speed Data Format

This draft format is ndjson format, each line is valid JSON delimited by endline

The overarching format for each line is show below.

```
{
    "event_id": "ORI201136507", // Transcom Event Id
    "event_tmc": "120-05847", // Traffic Messaging Channel Id (speed network road segment) that AVAIL determined the accident occured on
    "event_osmid": "33298429", // Open Street Map Edge Id that AVAIL determined the accident occurred on
    "tmcArray": [ 
    	/* 
    	   An Array of TMC id arrays, 1 for each possible branch of down stream links.
    	   These links are ordered going upstream (against traffic).
    	   The 3rd link will always be the incident tmc
    	*/         
        [
            "120-05848", // downstream segments
            "120N05848", // 
            "120-05847", // incident segment
            "120P25371", // upstream segments
            "120N05847", //
            "120-05846", //
            "120N05846"  //
        ]
    ],
    "tmcMeta": {
    	/* One object for each unique tmc in TMCArray */
    	"120N05846": {
            "meta": {
                "2019": {
                    "aadt": 99545,  // Average Annual Daily Traffic
                    "length": 0.734377, // Length in miles
                    "roadname": "I-90" // road name
                }
            }
        }
        ...
    },
    incidentSpeeds: {...},// speed object see below
    avgWeekdaySpeeds: {...} // speed object see below 

}
```

The speed objects hold the speed data for each unique tmc in the tmc array.

The incidentSpeeds data is the speed for every 5 minute epoch for the day of the incident.
the avgWeekdaySpeeds data is the average speed for every 5 minute epoch for all weekdays in 2019 (there where 261 weekdays in to 2019).


Each object has a possible of 288 entries for each tmc representing each 5 minute epoch in the day, however the data is sparse and this format reflects that, if there isn't data for that reporting period it won't be in this data so in practice there are usually less than 288 entries per tmc.
```
[
  {
    tmc: '120-05846', //tmc id
    resolution: 0, // epoch index [0 = 12:00am, 1 = 12:05am, 2 = 12:10am ... 287 = 11:55pm]
    value: 40.2380001068115, // travel time in seconds (use length from tmcMeta to calculate speed)
    numEpochs: '10' /* 
    	number of epochs reporting, for incident data will always be 1 because 
    	there is only one possible instance of each 5 minute segment of the day. For avgWeekdaysSpeeds 
    	this can range from 1 to 261 and can give you one indication of data completeness and quality 
    */
  },
  {
    tmc: '120-05846',
    resolution: 1,
    value: 38.1718184731223,
    numEpochs: '11'
  },
  {
    tmc: '120-05846',
    resolution: 2,
    value: 37.5440002441406,
    numEpochs: '10'
  },
  ...
]
```
