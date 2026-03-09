# Using Delta
One use case I've learnt is with **`delta`**.
Delta can be used to calculate the difference between numeric values. If beaconing is scheduled at consistent times, then this can be very helpful.
###### Breakdown of the Query
- **`sort 0`** is used to return all results. Otherwise there is a default limit of `10,000` rows. If a delta table has more than 10,000 rows, then it would usually only display the first 10k. 
- **`sort 0`** is essential because delta will only work if the events are in the correct time order.
- **`delta _time as delta_time`** this is when delta calculates the difference between event time in seconds.
- **`avg`** is counting the average time between connections (like every 60 seconds etc. ).
- **`stdev`** Standard Deviation is how consistent the timing is. Does it consistently do the same thing. Very little deviation is consistent with beaconing. Whereas a normal browser activity might look like this: 
```PowerShell
10  
40  
200  
5  
600
```

- **`values`** Will actually show us the specific values. 
- **`where count > 15`** Only pick up the events where over 15 connections have been made. Beaconing is often quite consistent. 
- **`deviation_time < 10`** A very low deviation time is going to mean that the events are very consistent. 


```PowerShell
index=network_logs  
| sort 0 src_ip dest_ip _time  
| delta _time as delta_time  
| stats 
count avg(delta_time) 
stdev(delta_time) as deviation_time
values(delta_time) as intervals 
values(id.resp_p) as dest_port
by src_ip dest_ip  
| where count > 15 AND deviation_time < 10
```