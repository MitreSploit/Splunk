### Static Table with only Custom Values:
```PowerShell
| table Subnet_Name, Subnet_Range

| append [
    | makeresults count=3
    | streamstats count as row
    | eval Subnet_Name=case(
        row=1, "Dev Subnet",
        row=2, "Marketing Subnet",
        row=3, "IT Subnet"
    )
    | eval Subnet_Range=case(
        row=1, "1.160.0.0/16",
        row=2, "40.97.0.0/16",
        row=3, "13.0.0.0/8"
    )
    | table Subnet_Name, Subnet_Range
]
```

### Counter Table for Value Comparison:
```PowerShell
| table Subnet_Name, Expected_IP_Count, Actual_IP_Count

| append [
    | inputlookup dns_resolution.csv
    | eval Subnet_Name=case(
        cidrmatch("1.160.0.0/16", IP_Addr), "Dev Subnet",
        cidrmatch("40.97.0.0/16", IP_Addr), "Marketing Subnet",
        cidrmatch("13.0.0.0/8", IP_Addr), "IT Subnet"
    )
    | where isnotnull(Subnet_Name)
    | stats dc(IP_Addr) as Actual_IP_Count by Subnet_Name

    | append [
        | makeresults count=3
        | streamstats count as row
        | eval Subnet_Name=case(
            row=1, "Dev Subnet",
            row=2, "Marketing Subnet",
            row=3, "IT Subnet"
        )
        | eval Expected_IP_Count=case(
            row=1, 4,
            row=2, 8,
            row=3, 3
        )
        | table Subnet_Name, Expected_IP_Count
    ]

    | stats max(Expected_IP_Count) as Expected_IP_Coun
```

### Specific Subnet Searching: 
```PowerShell
| inputlookup dns_resolution.csv
|  where cidrmatch("1.160.0.0/16",IP_Addr)

| table IP_Addr, Hostname
```

### Non-Specific Subnet - All IP's
```PowerShell
| inputlookup dns_resolution.csv
|  search IP_Addr=$IP$ AND Hostname=$HOSTNAME$

| table IP_Addr, Hostname
```
