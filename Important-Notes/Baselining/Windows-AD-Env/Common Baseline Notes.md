# Table Of Contents
- [[#1. Hosts on the Network|1. Hosts on the Network]]
- [[#2. Users, Privileged Users and Service Accounts|2. Users, Privileged Users and Service Accounts]]

**All investigation has been accomplished using Splunk BOTS**
##### 1. Hosts on the Network
In almost every exercise or task that I have come across, there has always been a request to confirm that the network map that I have received is valid? That's if I have a network map at all. The first thing we are usually told is to match the IP Addresses, MAC Addresses and Hostnames. 

<span style="color:rgb(0, 176, 80)">Using Zeek's DNS:</span>
Here we are creating a lookup table using Zeek's DNS output (stream:dns). 
I am matching the values of **`host_addr`** and **`hostname`**, then I am exporting this as a lookup table.
```bash
index=botsv1 AND source=stream:dns AND hostname{}=* | dedup host_addr{}

| rename host_addr{} AS IP_Addr

| stats 
values(hostname{}) AS Hostname
by IP_Addr

| sort IP_Addr
| outputlookup dns_IP_lookup.csv
```

Next I am running a separate query and importing my lookup table. 
From here I am matching the field **`IP_Addr`** from the lookup, with the field in the search called **`src_ip`**. I'm then simply adding **`src_mac`** in order to match up all three. 
```bash
index=botsv1 AND source=stream:dns AND hostname{}=* 
| dedup host_addr{}

| lookup dns_IP_lookup.csv IP_Addr AS src_ip OUTPUT Hostname

| stats
values(src_mac) 
values(Hostname)
by src_ip
```



##### 2. Users, Privileged Users and Service Accounts

 1. <span style="color:rgb(0, 176, 240)">Standard Users</span> --> <span style="color:rgb(0, 176, 240)">Different ways to capture the data:</span>
- All users --> Remember to use <b>`spath`</b> when the XML data in Message isn't extracting subfields
- Event Code 4624 will be the primary code that I will use here
```bash
index=botsv2 AND EventCode=4624
```


- I created a Sankey Diagram that depicts the following flow of information: 
- <span style="color:rgb(0, 176, 80)">Computer Name & IP</span> ------> <span style="color:rgb(0, 176, 80)">Username or Computer Name</span> ----> <span style="color:rgb(0, 176, 80)">Logon Type</span>
```bash
index=botsv2 AND EventCode=4624
| fillnull value=unknown src_user

| eval Computer = ComputerName . " - " . src_ip
| eval Expanded = case(Logon_Type = 2, "2 - Interactive", Logon_Type = 3, "3 - Network", Logon_Type = 4, "4 - Batch", Logon_Type = 5, "5 - Service", Logon_Type = 8, "8 - NetworkCleartext", Logon_Type = 9, "9 - NewCredentials", Logon_Type = 10, "10 - RemoteInteractive")

| stats count by Computer, user, Expanded

| eval stage1_source = Computer
| eval stage1_target = user

| eval stage2_source = user
| eval stage2_target = Expanded

| appendpipe [  
stats sum(count) as count by stage1_source stage1_target  
| rename stage1_source as source stage1_target as target  
]  

| appendpipe [  
stats sum(count) as count by stage2_source stage2_target  
| rename stage2_source as source stage2_target as target  
]  
  
| stats sum(count) as count by source target
```


Playing around with some different collection methods:
```bash
index=botsv2 AND EventCode=4624
| stats
values(Security_ID) AS Usernames,
dc(Security_ID) AS Unique_Name_Count
```