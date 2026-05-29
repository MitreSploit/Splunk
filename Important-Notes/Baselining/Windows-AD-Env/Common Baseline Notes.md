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
A general approach to how to 




