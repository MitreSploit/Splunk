# Subnet Search w/ Lookup
On the Dashboard page we are able to import the lookup created in [[Append Lookup]]].
We can use **`cidrmatch`** to perform a subnet search. The only issue with this method is that you won't be able to create a multiselect using **`*`** because this function isn't available with a wildcard. 
```PowerShell
| inputlookup dns_resolution.csv

| table IP_Addr, Hostname

| where cidrmatch($SUBNET$, IP_Addr)
```

