# Table Of Contents
- [[#1. Hosts on the Network|1. Hosts on the Network]]
- [[#2. Standard Users|2. Standard Users]]
- [[#3. Privileged Users|3. Privileged Users]]
- [[#4. Service Accounts|4. Service Accounts]]

**All investigation has been conducted using Splunk BOTS**


# General Hosts and Users
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



##### 2. Standard Users

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


- Playing around with some different collection methods.
- <span style="color:rgb(0, 176, 80)">The following uses <b>`eval`</b> to individually separate users with successful login events from users with failed login events. </span>
```bash
index=botsv2 AND (EventCode=4624 OR EventCode=4625)

| eval Successful_Login_Users = if(EventCode=4624, Account_Name, null())
| eval Failed_Login_Users = if(EventCode=4625, Account_Name, null())

| stats
values(Account_Name) AS All_Login_Usernames,
values(Successful_Login_Users) AS Successful_Logins,
values(Failed_Login_Users) AS Failed_Logins,
dc(Account_Name) AS All_User_Name_Count
```

##### 3. Privileged Users
- I believe the main focus I'm going to go with here will be on Event Code **`4672`** --> Special Privileges Assigned.
- I think a big one here might also be Event Code **`4738`** --> This tracks when a users account properties or privileges are modified. 

- <span style="color:rgb(0, 176, 80)">The following is just a simple display of privileged Users: </span>
```bash
index=botsv2 AND EventCode=4672
| stats 
values(Account_Name) AS Usernames,
dc(Account_Name) AS Unique_Name_Count
```

- In regards to Event Code **`4738`** I think for research will be required to best utilize this event code. 
##### 4. Service Accounts
- The approach I took to this was to look for Logins where the Logon Type is equal to either `4`(A batch job) or `5` (A service starts) --> I don't think this will capture all the types I'm looking for, however I will start with this. 

- <span style="color:rgb(0, 176, 80)">General Search I put together looking for either successful or failed login attempts with the logon type being either 4 or 5:</span>
```bash
index=botsv2 AND (EventCode=4624 OR EventCode=4625) AND (Logon_Type=4 OR Logon_Type=5)

| stats 
values(src_user) AS Src_User
values(user) AS Target_User
values(dest_ip) AS Dest_IP
values(dest_owner) AS Dest_Owner
values(Logon_Process) AS Logon_Process
values(Process_Name) AS Process_Name
BY ComputerName
```


# Processes and Services
##### 6. Processes Running
- Commonly during baselining it is important to understand the active processes. I will attempt to create a readable table or Sankey diagram for this. 

<span style="color:rgb(0, 176, 80)">Example Table:</span>
Using Process Creation I should be able to get a table like this together: 

| Process Name | PID | Parent Process Name | PPID | Threads | Handles | Session | Owner | Path |
| ------------ | --- | ------------------- | ---- | ------- | ------- | ------- | ----- | ---- |

