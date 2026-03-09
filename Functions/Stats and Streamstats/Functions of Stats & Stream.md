###### Counting Functions
Use either of these with the following:
- Note **`estdc`** is estimated distinct count - Supposed to be slightly faster than dc. 
```PowerShell
count
count(field)
sum(field)
dc(field)
estdc(field)
```

###### Math/Numeric Functions
Mathematical functions available with these:
```PowerShell
avg(field)
min(field)
max(field)
range(field)
median(field)
mode(field)
stdev(field)
var(field)
```

###### Values/List Functions:
Values/List Functions available:
```PowerShell
values(field)
list(field)
first(field)
last(field)
```

###### Time Based Functions:
```PowerShell
earliest(field)
latest(field)
```


###### Conditional Stats (Embed a Function)
Here is an example of how you can embed a function:
```PowerShell
| stats count(eval(EventCode=4625)) as failed_logons
| stats count(eval(EventCode=4624)) as successful_logons
BY host
```

- This would return a table of these counts by the host name



###### Reset Conditions
In this example, we might be looking at a specific host machine, and want to count the events of each user.
We can do a running count. The count will reset when the username is identified as different to the last one.
```PowerShell
| streamstats count by user reset_on_change=true
```


