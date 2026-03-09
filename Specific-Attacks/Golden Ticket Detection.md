###### 1. High Service count in a short timeframe
**Uses Event Code 4769: The Kerberos Key Distribution Centre (KDC) received a (TGS) request.** 
The assumption here is that an account has compromised the DC and achieved a golden ticket and are now able to access all services and are therefore accessing an abnormally high amount of services. 
```PowerShell
index=botsv2 AND EventCode=4769
| stats dc(Service_Name) AS Services BY Account_Name
```20 Services access might be too low depending on the environment and Admin activity```
| where Services > 20
```

###### 2. Combination High Service Count + 
Play around with the numbers. This is a combination search looking for either a high service count or a high count of the hostname. The assumption is that an attacker might be trying to access multiple services on multiple machines across the domain. 
```PowerShell
index=botsv2 EventCode=4769
| stats 
dc(Service_Name) as services 
dc(Service_ID) as hosts 
by Account_Name
| where services > 5 OR hosts > 10
```