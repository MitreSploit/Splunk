The Splunk commands **`bin`** and **`bucket`** are interchangeable. 
**`bin`** is the main command, while **`bucket`** is basically an alias.

###### What does it do?
`bin` can be used to group numeric or time values into ranges (bins).
It is most commonly used for things like:
- Time based grouping (using `_time`)
- Numeric based grouping (like `bytes`, `response` etc.)

# 1. Bin for Numeric Values
Here is an example of using **`bin`** to set a numeric number of **`60`**. 
**`bin`** is used to show events that run over 60 seconds in time. 
Suspiciously long events.

```powershell
index=edr  
| stats avg(duration) as avg_runtime by process_name  
| bin avg_runtime span=60  
| stats count by avg_runtime  
| sort - avg_runtime
```

# 2. Bin for Time based events
Counting the based on time blocks. 
We could use this with **`stats`** to count the number of an event that happens in blocks of minutes.
```powershell
index=main  
| bin _time span=5m  
| stats count by _time
```

