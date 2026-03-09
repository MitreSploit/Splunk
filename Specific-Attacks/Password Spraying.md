###### 1. Password Spraying more than 5 accounts:
In this example, we are using the failed logon event code 4625.
We are counting the number of account names that the source IP attempted to login to. 
I have set the number to 5. I want to see any IP address that failed to login to any more than 5 user accounts. 

```PowerShell
index=botsv2 AND EventCode=4625 NOT Account_Name="-"
|  stats count 
dc(Account_Name) as Unique_Users 
values(Account_Name) as Targeted_Accounts
by src_ip
| where Unique_Users > 5
| sort -Unique_Users
```

###### 2. High Failure rate, but with a successful attempt:
```PowerShell
index=botsv2 AND EventCode IN (4624, 4625) NOT Account_Name="-" NOT Account_Name="*$"
| stats 
count(eval(EventCode=4625)) AS Failed_Attempts 
count(eval(EventCode=4624)) AS Successful_Attempts
by Account_Name src_ip
| where Failed_Attempts > 10 AND Successful_Attempts > 0
```