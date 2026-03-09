# What is it?
Computes the difference between nearby results using the value of a specific numeric field (I've used it for **`_time`**).
For every event where the field value is a number, **`delta`** will computer the difference, in search order, between the `<field>` value for the current event and the `<field>` value for the previous event. The difference will be written into a newly created field. 

By default, the events returned are in reverse time order from new events to old events. 

###### Splunk Documentation Use Cases:
**1. What is the difference in the number of purchases between the top 10 buyers?**
Find the top ten people who bought something yesterday, count how many purchases they made and the difference in the number of purchases between each buyer.
```PowerShell
sourcetype=access_* status=200 action=purchase | top clientip | delta count p=1
```

**2. Calculate the difference in time between recent events**
Calculate the difference in time between each of the recent earthquakes in Alaska. Run the search using the time range All time.
```PowerShell
source=all_month.csv place=*alaska* | delta _time p=1 | rename delta(_time) AS timeDeltaS | eval timeDeltaS=abs(timeDeltaS) | eval "Time Between Quakes"=tostring(timeDeltaS,"duration") | table place, _time, "Time Between Quakes"
```
