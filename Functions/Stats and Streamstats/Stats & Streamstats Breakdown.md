
# Concepts Breakdown
Think of your ingested events as being like this for a second:
```powershell
1. Bob logs in  
2. Bob logs in  
3. Alice logs in  
4. Bob logs in  
5. Alice logs in
```



## STATS VS STREAMSTATS

###### 1. Stats
Stats is like saying “Stop. I’m going to count everything and give you a summary.”
```PowerShell
| stats count by user
```

**Output:**
You have got exactly what you want, but you've lost the timeline and the individual events. This is only showing the total. 
```Powershell
Bob 3
Alice 2
```



###### 2. Streamstats
Streamstats is like saying “As events go past, I’ll keep a running count — but I won’t delete anything.”

**Output:**
Now you are getting the count that you wanted, but you are not losing the event. 
```PowerShell
Bob logs in count=1  
Bob logs in count=2  
Alice logs in count=1  
Bob logs in count=3  
Alice logs in count=2
```
